React Nested Loader
==========================

Easily inject a loader into a button. Almost no boilerplate.


![image](https://user-images.githubusercontent.com/749374/35107228-b2abbf4a-fc70-11e7-87a5-93528c8797b8.png)
![image](https://user-images.githubusercontent.com/749374/35110949-5457c7fe-fc7a-11e7-8fc9-c0e0687b01f6.png)

![image](https://user-images.githubusercontent.com/749374/35104923-9c57f12e-fc6a-11e7-86ef-aa3a11724dd4.png)
![image](https://user-images.githubusercontent.com/749374/35111647-007356b0-fc7c-11e7-89f9-1211519a1ac0.png)

Maintaining a `loading` prop in state, and passing it down to the deeply nested button/view is annoying.

This library removes the boilerplate.

## Usage

```javascript
import ReactNestedLoader from "react-nested-loader";
```

The ReactNestedLoader HOC will inject a `loading=false` prop to the wrapped component.
Whenever a function props returns a promise (ie, `onClick` callback returns a promise) the button will receive `loading=true` during promise resolution.

```javascript
const Button = ({onClick,loading}) => (
  <button onClick={onClick} disabled={loading}>
    {loading ? "..." : "Click me "}
  </button>
);
const LoadingButton = ReactNestedLoader(Button);
```



Using the `LoadingButton` into a top-level component: no need to use any local state, you just need to add a `return` and it works out of the box.



```javascript
const SomeIntermediateComp = ({onButtonClick}) => (
  <WhateverYouWant>
    <LoadingButton onClick={onButtonClick}/>
  </WhateverYouWant>
);

class Container extends React.Component {
  handleClick = () => {
    const promise = MyAPI.doSomethingAsync();
    // VERY IMPORTANT: the promise MUST be returned to the button
    // the only boilerplate you need is return
    return promise;
  };
  render() {
    return (
      <WhateverYouWant>
        <SomeIntermediateComp onButtonClick={this.handleClick}/>
      </WhateverYouWant>
    )
  }
}
```


## Features

- Works with React and React-Native
- The callback proxies are cached appropriately so that the button does not render unnecessarily
- Will only handle the loading state of the last returned promise, to avoid concurrency issues (think `takeLatest` of Redux-saga`)
- Imperative API (`componentRef.api.handlePromise(promise)`)
- API injected as prop into button (`props.reactNestedLoader.handlePromise(promise))`

## Limits

The HOC does hold the button loading state as React component state. This means it won't be in your state management system (Redux/Apollo/Mobx...) and as any local state you will loose ability to use devtools to replay that state (or other fancy features). In my opinion it is not critical state that is worth putting in your Redux store anyway. I assume perfectly using this lib as well as Redux/Redux-saga/Apollo mutations.

Currently the lib only support injecting a single `loading` prop. As a component may receive multiple callbacks, we could inject multiple loading props. Please open issues with your specific usecase if needed.


## Advices

- Wrap generic app button with `ReactNestedLoader` and manage the `loading` prop inside it to show some alternative content like a spinner
- When button component change from `loading=false` to `loading=true`, make sure the component dimension is not affected for better UX
- A nice UX is to make the text disappear and make the spinner appear, as it does not mess-up with button dimensions (make sure to use a small-enough spinner)
- If needed, pass spinner size in button props

