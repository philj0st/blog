---
title: ES6 Promises Basics
date: 2016-04-22 21:14:29
tags: [callbacks, promises, promise chains]
categories:
- tech
- ES6
- Promises
---
Escape callback hell now with Promises in ES6! They're supported in all [major browsers](http://kangax.github.io/compat-table/es6/#test-Promise) and basically look like this:

```
//basic syntax
let myPromise = new Promise((resolve, reject) => {
  //do something asynchronous
  //for example fs.readfile or $.ajax('/user/3')
  //if that was successful
  resolve('done')
  //or reject()
});

//whenever Promise has resolved ...
myPromise.then(value => {
  //... do something
  console.log(value); //'done'
})
```
for further demonstrations let's create a mockup request function we can pass a desired amount of seconds parameter to. This will simulate an asynchronous operation and resolve after the specified time.

```
let Api = {
  requestSomething: seconds => new Promise((resolve, reject) => {
    setTimeout(() => {
      resolve(`promise resolved after ${seconds} seconds`)
    }, seconds * 1000)
  })
}


```

##### Sequential  
now since our `requestSomething` function returns a promise, we can chain promises in a slick syntax like this:

```
//chained promises
Api.requestSomething(1).then((value) => {
  console.log(value);
  return Api.requestSomething(3)
}).then((value) => {
  console.log(value);
  return Api.requestSomething(2)
}).then((value) => {
  console.log(value);
  return Api.requestSomething(1)
}).then((value) => {
  console.log(value);
})

//promise resolved after 1 seconds
//promise resolved after 3 seconds
//promise resolved after 2 seconds
//promise resolved after 1 seconds
```
Sequential chained Promises can be useful if a request 1s response gives us information about what we want to request 2 to look like.

#### Parallel

This doesn't bring any significant advantage over callbacks yet but let's check out `Promise.all`

```
let promises = [
  Api.requestSomething(1),
  Api.requestSomething(2),
  Api.requestSomething(2),
  Api.requestSomething(2),
  Api.requestSomething(2),
  Api.requestSomething(3)
]

//after all the promises have resolved ...
Promise.all(promises).then(value => {
  console.log(value);
})

//["promise resolved after 1 seconds", "promise resolved after 2 seconds", "promise resolved after 2 seconds", "promise resolved after 2 seconds", "promise resolved after 2 seconds", "promise resolved after 3 seconds"]
```
`Promise.all` **returns a new promise** which will resolve after all the promises we passed it have resolved. The `value` that gets passed into the `.then` function is an array of all the resolve values from the initial requests.

what an awesome syntax to keep things clean and readable :)

#### Endnote
Notice that I always assumed that my requests will never fail. in real life specify a function in `.then` that handles a failed request like this:
```
promise.then(successFunction, failFunction)
```
