---
layout: post
title: Typescript and ImmutableJS Records
---

Typescript and ImmutableJS should go together like peas in a pod.  One is a development language that allows for powerful object-oriented development in what is still a dynamic and interpreted language, and the other is a library for creating immutable objects in that same language.  I use them together, and maybe so should you.  I will not be going over ImmutableJS and it's types here.

There is a shadow over the relationship between Typescript and ImmutableJS: Even with Typescript definitions included, ImmutableJS loses the benefits of type safety.  I primarily use Map and Record in my applications, but the big offender is Record:

1. Records are type-safe, but if you call .set() to get an updated copy, Typescript thinks you are getting a Map<>.  Not only can you not assign it back to the same variable without casting it, but you cannot chain calls to keep your code readable.
2. Records allow you to access their fields as properties, but Typescript can't tell what these are.
3. Records throw a runtime error if you assign to a property instead of calling set(), the Typescript compiler will not stop you.

These are not because of negligence by Facebook on the part of their ImmutableJS definitions, its just how things are.

### Case Study 1: Property Access

So I have a project that uses React, Node.js, and socket.io.  The Node.js application is sending objects across the web-socket, and I would like to wrap these into immutable objects.  Like a good Typescript developer, I have a series of interfaces declared for the Node.js and React applications to share:

{% highlight ts %}
export interface IUser {
  firstName: string
  lastName: string
  email: string
}

export interface IGroup {
  name: string
  members: IUser[]
}
{% endhighlight %}

I have two interfaces to represent two of the objects that my server will send me.  Since I really want these to be immutable (these are from a database which the client has no business changing), I create a set of RecordClass objects so I can wrap incoming objects:

{% highlight ts %}
export const UserClass = Record({
  firstName: '',
  lastName: '',
  email: ''
})

export const GroupClass = Record({
  name: '',
  members: new IGroup[]
})
{% endhighlight %}

With those classes in place, every time I receive an IUser or IGroup object, I can call UserClass() or GroupClass() and convert them into a record.  But lets highlight some of the problems I am about to face:

{% highlight ts %}
let user = UserClass({ ... });
// typescript error
let fName = user.firstName;
// typescript error
let lName = user.lastName;
// works, if you can remember the fields
let email = user.get('email');
{% endhighlight %}

The Typescript compiler will not allow for property access to the fields, even though it is valid in Javascript.  All values must be accessed by using .get(), which is not type-checked or validated.  Bonus points for having to look back at your interface declaration if you haven't this code in a while.

### Case Study 2: Updating Record, and Chaining

Lets say in my application that I like immutable so much, that I am storing the auth data returned from a successful login into an immutable Record.  I will create the RecordClass like so:

{% highlight ts %}
const AuthClass = Record({
  loggedIn: false,
  userName: '',
  accessLevel: ''
})
{% endhighlight %}

Well this is working fine!  I can create a record on page load and update it as my user authenticates.

{% highlight ts %}
let auth = AuthClass(); // default for now

// Whoops! set() returns a Map, Typescript error
auth = auth.set('loggedIn', true);  

// works, but only if this isn't a ReactJS .tsx file
auth = <AuthClass>auth.set('loggedIn', true);

// this one works
auth = (((auth.set('loggedIn', true) as AuthClass
    ).set('username', '...') as AuthClass
  ).set('accessLevel', 0))
{% endhighlight %}

Now you are probably thinking, why on earth would you want to chain them like that?  Because it is supposed to work that way; plus if you replace "auth =" with "return" it is a fragment from a Redux reducer.  Why did I switch from casting using <AuthClass> to casting using ( as AuthClass)?  This is a React application, and hard brackets is recognized as a React.Element instruction.  Casting with <AuthClass> will cause compilation issues in a jsx or tsx file as AuthClass is not a subclass of React.Element.

This is usable, but I think it can get better.

### Solution: Wrapping a Record

Luckily for us, we can create our own Typescript data structure that will fix all of this for us.  I'll put the entire code for one here, and then discuss it:

{% highlight ts %}
export interface IUser {
  firstName: string
  lastName: string
  email: string
}

const UserClass = Record({
  firstName: '',
  lastName: '',
  email: ''
})

export class User extends UserClass implements IUser {
  readonly firstName: string
  readonly lastName: string
  readonly email: string

  set(key: string, value: string): User {
    return <User>super.set(key, value);
  }
}
{% endhighlight %}

There we are.  Now what good is this?  Lets run through a few examples:

{% highlight ts %}
let user = new User();

// works, typescript knows about it.
// Have some auto-complete while we're at it.
let fName = user.firstName;

// A little kludgey for my taste, but no casting required
user = user.set('firstName', '...');  

// This works too
return user                 
  .set('firstName', '...')
  .set('lastName', '...')
  .set('email', '...')

// This is now a compile error instead of just a runtime error
user.firstName = '...';  

{% endhighlight %}

This is a bit more code than using the RecordClass directly, but now it plays nicely with Typescript.  There is also the added benefit of enforcing some type-safety within the set function.  Record.set() takes an 'any' for its value, but my user class will only accept strings, and Typescript will tell you right in your IDE.
