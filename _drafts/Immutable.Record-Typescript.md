---
layout: post
title: Typescript and ImmutableJS
---

I have been working with Typescript for several months now.  With the number of times I have to jump between contexts and programming languages, I am loving it.  I am using it wherever possible.

On one of my current projects where I am using typescript for both the node.js server as well as the client, I am using immutable.js for the state object.  There are many, many posts about immutable.js and why you may want to use it, so I will cover none of that.  What I'll cover here is an idea I put together from several different blog posts I read concerning Typescript and immutable Records.

### Records

In immutable.js, there are two data types that I am using everywhere: Record and Map.  Using type safety on a Map is Straightforward.  Records are different.

As far as typescript is concerned, Records are just an inheriting class of Map; which they are.  But there are a few issues I corrected with a few extra lines of code:

1 - While Records return the type of Record after a call to set(), the typings do not reflect this, so you cannot chain them
2 - Records can be accessed by get(), or by the dot-property notation.  The Typings definitions do not cover these.

So lets fix them:

First: create an interface for your object.

{% highlight ts %}
interface IUser {
  firstname: string
  lastname: string
  password:string
}
{% endhighlight %}

Second: create your record class, setup to match it.
{% highlight ts %}
const UserClass = Record({
  firstname: '',
  lastname: '',
  email: ''
})
{% endhighlight %}

that is enough to use a Record in immutable.js and Typescript, but lets go a step further:
{% highlight ts %}
export class User extends UserClass implements IUser {
  readonly firstname: string
  readonly lastname: string
  readonly email: string

  set(key: string, value: string | number): User {
    return <User>super.set(key, value);
  }
}
{% endhighlight %}

There, now you can instantiate a User object, that is a Record, and it behaves as you would expect.  Why bother?  Since we are working in Typescript, we want the type-checking to be happy.  Compare the following:

Using the record directly:
{% highlight ts %}
let u = new User();
u = u.set('firstname','Tommy');
{% endhighlight %}

That is an error right there.  The normal typings declare the return type of Record.set() as Map<>!  This would run just fine, if you could get tsc to be happy with it.

Lets use Our wrapper class instead
{% highlight ts %}
let u = new User().set('firstname','FName').set('email','email@domain');
u = u.set('lastname', 'LName');
{% endhighlight %}

The compiler is now happy, and we can use our records as though we were coding directly in javascript.

But wait, there is more:
In addition to allowing Record chaining, Autocomplete now allows for property access of the fields.  The readonly flag on each field also makes property assignment a compile-time error instead of a run-time error.

There is a bit more code here, but as the wrapping class is the only part that is exported, it can be squirrelled away into a module and treated like a normal Class.


