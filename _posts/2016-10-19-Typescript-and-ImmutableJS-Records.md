---
layout: post
title: Typescript and ImmutableJS Records
published: true
---

I have been working on web applications lately.  Working independently or on small teams, I find ReactJS to be quick and easy to write.  But in learning how to write better, and better performing React code, I should start using other libraries, such as ImmutableJS.  All good things and I like what I see with only one glaring exception:  I work in Typescript.

Typescript is great!  I rarely - if ever - use dynamic typing to mix data types, and the added interfaces and code completiong for a dynamic language is fantastic.  I wish there was something similar for my old flame Python.

Why is Typescript a glaring exception?  Yes, I can load the typings definition and access the exposed API.  Unfortunately Immutable objects are not represented correctly in Typescript definitions.  They can't be, they rely on the dynamic sauce present in javascript that Typescript cannot capture.  Records are created at runtime.  Property access to fields isn't supported, and you will be casting data types often when using get().  CAlling set() is not typesafe, as all it wants is an <any>.  Accessing a deeply nested object?  Welcome to (((object as type).object as type).object as type).  I want my code to be simple and readable.  Clean debuggable code is elegant code.

## The Fix

I can rant with the best of them, but if you don't propose a solution you are wasting everyones time, including your own.  It takes some legwork, but I have a solution that works.  We will create child classes in typescript that wrap the Immutable properties we love.

Lets create an immutable User class module.  All of the following segments will live in the same module.  Create an interface for the class:

{% highlight ts %}
interface IUser {
  firstName: string
  lastName: string
  email: string
}
{% endhighlight %}

This is a normal interface declaration for typescript.  Note that it is not exported.  Now we generate a Record whose definition is suspiciously close to our interface.  Here it is:

{% highlight ts %}
const UserClass = Record({
  firstName: '',
  lastName: '',
  email: ''
})
{% endhighlight %}

This is normal Record class creation.  But note that we didn't export this one either.  These exist only within our module so that we can do the next step:

{% highlight ts %}
export class User extends UserClass implements IUser {
  readonly firstName: string
  readonly lastName: string
  readonly email: string

  set(key: string, value: string): User {
    return <User>super.set(key, value);
  }
}
{% endhighlight %}

That right there is our typescript class definition.  That is what we will instantiate, and pass around.  It is an immutable Record, and behaves exactly like a record, but it is understandable by both my Typescript IDE and my compiler.

Inside of the User class, you can replace the generic set() function with normal object-oriented setters to enable better type safety.  But I will admit here that I have a class or two that use set(key: string, object: type1 | type2 | type3 | type4 | type5).  We also cast the types back to a User isntance from set(), which is correct.  Immutable typings definitions return a map by default, which while technically correct is not very useful to me.

## What good is this?

So, what good is this?  Now you have three object declarations instead of one, what does this buy you?  First off, this isn't much more work.  Its about on par for doing a large class definition in an object oriented language, but the getters are free.

What else is there?

Property access works!

{% highlight ts %}
let user = new User();
// no typescript error here
let firstName = user.firstName;
// vs
let oldWay = user.get('firstName') as string;
{% endhighlight %}

I'm a little excited for that one, as now my IDE can autocomplete my properties instead of looking at the definition to lookup key values.

You can chain operations! (I'll admit this is a contrived example, but it works nonetheless)

{% highlight ts %}
let user = new User();
// no typescript error here
let newUser = user.set('firstName', fname)
                  .set('lastName', lastName)
                  .set('email', email)
{% endhighlight %}

This doesn't benefit from IDE autocompletion, but I do a number of long chains like this when I am updating redux records.  Simplified Redux reducers should be on everyone's christmas list.

## TL;DR

You can wrap/inherit an Immutable record into a Typescript class, that lets you avoid casting and other problems.  Property getters work, and you can add custom setters.

This is a bit more code than using the RecordClass directly, but now it plays nicely with Typescript.