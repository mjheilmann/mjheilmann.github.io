---
layout: post
title: React Navigation with Redux State
---

I may have mentioned before, but I like to keep things simple.  Simple for the developer, that is.  If there are extra steps the library developer can take to make their library simpler for the consuming developer, they should be taken.

I had a run-in with React Navigation.  The navigation libraries I found were not what I wanted at all.  I could not pass props to my components.  I could create a provider for my stage object to pass it around the navigation component, but then I had to wrap all of my navigable components in order to receive the state object.  For me, that is unsatisfactory.

So, I wrote my own.  I let Redux handle the state changes for me.

This isn't a super-slick library, but considering how easy it was, having custom navigation for your application is a simple choice.

Lets start at the root of your application, where coad wont be loading or unloading.  I put this into mt index.tsx:
{% highlight ts %}
// local storage component for the route
let url = window.location.pathname;
// subscribe to your redux store to pick up navigate actions from your application
// Redux manages our application state, this just keeps the window histor in the loop
store.subscripe(() => {
  if (store.getState().url !== url) {
    url = store.getState().url;
    window.history.pushState(null, null, url)
  }
});
// on our first load, initialize our redux object with the route we are on
store.dispatch(
  createNavigateToAction(url)
);
{% endhighlight %}

OK.  We have a url on our redux state store that is initialized on page load.  If the url is updated in redux anywhere, it will also update the window url and browser history.  Good so far.

I have a root component for my React application that will contain all of the routes.  It also subscribes to the entire Redux state and passes data as ImmutableJS objects.  Did you read my post on ImmutableJS and Redux?

{% highlight ts %}
interface rootProps {
  // the immutable redux store
  store: Store<StateModel>
}

export class rootComponent extends React.Component<rootProps, {}> {
  private sub: Redux.Unsubscribe;
  state: {
      st: StateModel
  };
  constructor(props: appProps) {
    super(props);
    this.sub = this.props.store.subscribe(() => {
      if (this.state.st !== this.props.store.getState()) {
        this.setState({
          st: this.props.store.getState()
        });
      }
    });
    this.state = {
      st: this.props.store.getState()
    }
  }
  
  render() {
    switch (this.state.st.url){
      case '/account':
        return (
          <div>
            <Navbar />
            <Account userAcount={} />
          </div>
        )
      default:
        return (
          <div>
            <Navbar />
            <p>Page Not Found...</p>
          </div>
        )
    }
  }
}
{% endhighlight %}

This is a little simplified, but you can see that we have access to the latest redux state in this component, one of which includes the current url.  A simple switch statement allows us to change which component we are displaying, and passing whatever props to it that we want.  We are in full control.

So, what about that Navbar component?  As a quick example using React-bootstrap:

This is the secret function
{% highlight ts %}
handleNav(href: string) {
  this.props.dispatch(createNavigateToAction(href));
}
{% endhighlight %}
What is that?  That prop is the Redux.Store.dispatch method.  Since I am passing around the immutable state instead of forcing every component ro subscribe and unsubscribe from the store, I pass along the dispatch function.  You can see that I create a NavigateTo Action that contains the target href.

The url in the Redux state is updated, the React application updates to match the new route, and the state subscription in my index.tsx file updates the window address bar.

So  the navbar looks a little something like this:
{% highlight ts %}
<Navbar>
  <Navbar.Header>
    <Navbar.Toggle />
    <Navbar.Brand>Application Branding</Navbar.Brand>
  </Navbar.Header>
  <Navbar.Collapse>
    <Nav>
      <NavItem
        active={this.state.url === "/account" || this.state.url === "/"}
        onClick={this.handleNav.bind(this, "/account") }>
        Account
      </NavItem>
      <NavItem
        active={this.state.url === "/users"}
        onClick={this.handleNav.bind(this, "/users") }>
        Users
      </NavItem >
      <NavItem
        active={this.state.url === "/pending"}
        onClick={this.handleNav.bind(this, "/pending") }>
        Pending Users
      </NavItem >
    </Nav >
  </Navbar.Collapse >
</Navbar >
{% endhighlight %}

Is this for everyone?  Perhaps not, but I much prefer to understand and be in control of a central component of an application I am building, rather than bending over backwards for some opinionated library.
