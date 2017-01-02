---
layout: post
title: Typescript, ImmutableJS, and Redux
---


This post is about using typescript and immutable.js with records.  Please reference my other post on [records and Typescript]({% post_url 2010-07-21-name-of-post %}) for more background.

I am teaching myself React, and in the process, I am working out how I would like best to represent the components.  I prefer things to be simple for the developer, they have enough to worry about.  The library should do its part and do it well.

In my React application, I am using Redux for state management.  But I am also using Immutable.js.  these two are problematic enough that others have written their own combinereducers functions to compensate for it.  But I have found that I can do wihtout that by using Immutable.Js and Typescript Directly.

Here we go:

Define an interface or series of interfaces for your data store:


{% highlight ts %}
export interface IStateModel {
  loggedIn: boolean
  users: Map<string, User>
  groups: Map<string, Group>
}
{% endhighlight %}

This state model is simple, it tracks whether or not you are logged in, and a Map of users that you interact with in the application.  This structure is Record->Map->Record for any user accessed.

As described on my other post, continue the pattern with StateModelClass, and StateModel to arrive at your class that extends Record.

Now lets create the root reducer:

{% highlight ts %}
// reducer for User Records
function users(state = Map<string,User>, action: Action){
  ...
}

// reducer for Group Records
function groups(state = Map<string,Group>, action: Action){
  ...
}

export default function rootReducer(state = new StateModel(), action: Action): StateModel {
  return state
    .set('users', users(state.users, action))
    .set('groups', groups(state.groups, action))
}
{% endhighlight %}

Thats it.  The primary function just chains the action through the reducer for each category, and Immutable.js takes care of the rest.  Actions are normal Redux actions, and we can leverage immutable.js set() to update our state tree.

