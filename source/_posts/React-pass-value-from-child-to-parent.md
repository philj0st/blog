---
title: React - Le State and data flow
date: 2016-04-04 22:34:06
tags: [tech, react, state, props]
categories:
- tech
- react
---
We will discover this technique by creating a searchable list component.

![SearchableList Layout](/images/react-state1.png)

For the `List` to be able to display only the `ListItem`s which match the `filterText` entered in the `SearchBar`, we need to pass `filterText` back to the parent component `SearchableList`. Having the `filterText` added to the state of `SearchableList` will automatically inform the `List` once `filterText` changes.

Remember: React is all about one-way data flow down the component hierarchy. It may not be immediately clear which component should own what state. **This is often the most challenging part for newcomers to understand,** so follow these steps to figure it out:

For each piece of state in your application:

  * Identify every component that renders something based on that state.
  * Find a common owner component (a single component above all the components that need the state in the hierarchy).
  * Either the common owner or another component higher up in the hierarchy should own the state.
  * If you can't find a component where it makes sense to own the state, create a new component simply for holding the state and add it somewhere in the hierarchy above the common owner component.

Let's run through this strategy for our application:

  * `List` needs to filter the `ListItem`s based on state and `SearchBar` needs to display the search text and checked state.
  * The common owner component is `SearchableList`.
  * It conceptually makes sense for the filter text and checked value to live in `SearchableList`

Cool, so we've decided that our state lives in `SearchableList`. First, add a `getInitialState()` method to `SearchableList` that returns `{filterText: ''}` to reflect the initial state of your application. Then, pass `filterText` to `List` and `SearchBar` as a prop. Finally, use these props to filter the `ListItem`s in `List` and set the values of the form field in `SearchBar`.
```
//[SearchableList.js]
var React = require('react');
var SearchBar = require('./SearchBar');
var List = require('./List');

var SearchableList = React.createClass({
  getInitialState: function () {
    return({filterText: 'Foo Bar'})
  },
  render: function () {
    return (
      <div id='SearchableList'>
        <SearchBar filterText={this.state.filterText} />
        <List filterText={this.state.filterText} />
      </div>
    )
  }
});
module.exports = SearchableList;
```
```
//[SearchBar.js]
var React = require('react');

var SearchBar = React.createClass({
  render: function () {
    return (
      <div id='SearchBar'>
        <input
          type="text"
          value={this.props.filterText}
          autofocus
        />
      </div>
    )
  }
});

module.exports = SearchBar;
```
```
//[List.js]
var React = require('react');

var List = React.createClass({
  render: function () {
    return (
      <div id='List'>
        List of items that match {this.props.filterText}
      </div>
    )
  }
});

module.exports = List;
```

You can start seeing how your application will behave: set `filterText` to `"Foo Bar"` and refresh your app. You'll see that the data table is updated correctly.

![top-down data flow - what we have](/images/react-state2.png)

So far, we've built an app that renders correctly as a function of props and state flowing down the hierarchy. Now it's time to support data flowing the other way: the form components deep in the hierarchy need to update the state in `SearchableList`.

React makes this data flow explicit to make it easy to understand how your program works, but it does require a little more typing than traditional two-way data binding. React provides an add-on called `ReactLink` to make this pattern as convenient as two-way binding, but for the purpose of this post, we'll keep everything explicit.

If you try to type or check the box in the current version of the example, you'll see that React ignores your input. This is intentional, as we've set the `value` prop of the `input` to always be equal to the `state` passed in from `SearchableList`.

Let's think about what we want to happen. We want to make sure that whenever the user changes the form, we update the state to reflect the user input. Since components should only update their own state, `SearchableList` will pass a callback to `SearchBar` that will fire whenever the state should be updated. We can use the `onChange` event on the inputs to be notified of it. And the callback passed by `SearchableList` will call `setState()`, and the app will be updated.

Though this sounds complex, it's really just a few lines of code. And it's really explicit how your data is flowing throughout the app.

in *SearchableList.js* add the function `changeFilterText` and pass it to the `SearchBar` component.

```
//[SearchableList.js]
...
var SearchableList = React.createClass({
  getInitialState: function () {
    return({filterText: ''})
  },
  //change the state in this wrapper function
  changeFilterText: function (newText) {
    this.setState({filterText: newText});
  },
  render: function () {
    return (
      <div id='SearchableList'>
      {/*pass the wrapper function to the SearchBar component via props*/}
        <SearchBar changeFilterText={this.changeFilterText} filterText={this.state.filterText} />
        <List filterText={this.state.filterText} />
      </div>
...
```
inside *SearchBar.js* call the before passed `changeFilterText` inside a wrapper function and assign the wrapper function to the `onChange` attribute.

```
//[SearchBar.js]
...
var SearchBar = React.createClass({
  handleChange: function (e) {
    //call the function we got passed from SearchableList
    this.props.changeFilterText(e.target.value);
  },
  render: function () {
    return (
      <div id='SearchBar'>
        {/*handle changes with the freshly created handleChange function*/}
        <input
          type="text"
          value={this.props.filterText}
          onChange={this.handleChange}
          autofocus
        />
      </div>
    )
    ...
```

et voilà, this is our two-way data binding.

![two-way data binding](/images/react-state4.png)

{% blockquote Wikipedia https://en.wikipedia.org/wiki/Reactive_programming Reactive Programming (Paradigm) %}
In computing, reactive programming is a programming paradigm oriented around data flows and the propagation of change. This means that it should be possible to express static or dynamic data flows with ease in the programming languages used, and that the underlying execution model will automatically propagate changes through the data flow.
{% endblockquote %}

[GitHub](https://github.com/phipix01/react-SearchableList)
[Thinking in React](https://facebook.github.io/react/docs/thinking-in-react.html)
