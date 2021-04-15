# ReactJS 


## 生命周期
在 React 中只有类组件有生命周期。

```JSX

class Element extends React.Component {
    constructor(props){
        super(props); // 调用父类的构造函数

        this.state = {}; // 构造函数是唯一可以给 this.state 赋值的地方：
    }

    // static contextType

    static getDerivedStateFromError(error) {}

    static getDerivedStateFromProps(props, state){}

    shouldComponentUpdate?(nextProps: Readonly<P>, nextState: Readonly<S>, nextContext: any): boolean {};

    // 组件加载后执行，且只执行一次。
    componentDidMount(){}
    // 即将删除
    componentWillMount(){}

    componentWillReceiveProps(){}

    // 即将删除
    componentWillUpdate(){}

    // 组件卸载前执行，且只执行一次。
    componentWillUnmount(){}    

    componentDidCatch(error, errorInfo) {}


}


```


## State

问题：State 的更新可能是异步的。
原因：出于性能考虑，React 可能会把多个 setState() 调用合并成一个调用。
用法: 

```JSX
// 触发试图更新， 第二个参数可选。
this.setState((state, props) => ({
  counter: state.counter + props.increment
}))

this.state({}, (state) => { return state})
```

问题：State 的更新会被合并
原因：

### 事件

在 React 中，Event 是合成事件。并且将事件代理到页面根元素。

```JSX
class Element extends React.Component {
    constructor(props){
        super(props); // 调用父类的构造函数

        this.state = {}; // 构造函数是唯一可以给 this.state 赋值的地方：
        this.deleteRow = this.deleteRow.bind(this);
    }

    deleteRow() {
        this.setState(state => ({
            isToggleOn: !state.isToggleOn
        }));
    }
    // 此语法问题在于每次渲染 Element 时都会创建不同的回调函数。在大多数情况下，这没什么问题，但如果该回调函数作为 prop 传入子组件时，这些组件可能会进行额外的重新渲染。我们通常建议在构造器中绑定或使用 class fields 语法来避免这类性能问题。
    render(){
        <div>
            <button onClick={(e) => this.deleteRow(id, e)}>Delete Row</button>
            <button onClick={this.deleteRow.bind(this, id)}>Delete Row</button>
            <button onClick={this.deleteRow.bind(this, id)}>Delete Row</button>
        </div>
    }
}
class Element extends React.Component {
    constructor(props){
        super(props); // 调用父类的构造函数

        this.state = {}; // 构造函数是唯一可以给 this.state 赋值的地方：
        this.handleClick = this.handleClick.bind(this);
    }

    handleClick() {
        this.setState(state => ({
            isToggleOn: !state.isToggleOn
        }));
    }
    // 此语法问题在于每次渲染 Element 时都会创建不同的回调函数。在大多数情况下，这没什么问题，但如果该回调函数作为 prop 传入子组件时，这些组件可能会进行额外的重新渲染。我们通常建议在构造器中绑定或使用 class fields 语法来避免这类性能问题。
    render(){
        <button onClick={this.handleClick}>Delete Row</button>
    }
}
```


## 受控组件

渲染表单的 React 组件还控制着用户输入过程中表单发生的操作。被 React 以这种方式控制取值的表单输入元素就叫做“受控组件”。

## 非受控组件

要编写一个非受控组件，而不是为每个状态更新都编写数据处理函数，你可以 使用 ref 来从 DOM 节点中获取表单数据。

## import()

在你的应用中引入代码分割的最佳方式是通过动态 import() 语法。

```JSX
import("./math").then(math => {
  console.log(math.add(16, 26));
});
```

## React.lazy

React.lazy 函数能让你像渲染常规组件一样处理动态引入（的组件）。不支持服务端渲染，配合 `Suspense` 使用

```JSX
import React, { Suspense } from 'react';

const OtherComponent = React.lazy(() => import('./OtherComponent'));

function MyComponent() {
  return (
    <div>
      <Suspense fallback={<div>Loading...</div>}>
        <OtherComponent />
      </Suspense>
    </div>
  );
}
```

## Context

Context 设计目的是为了共享那些对于一个组件树而言是“全局”的数据

- React.createContext
- Provider
- contextType
- Consumer

## 错误边界

```JSX
class ErrorBoundary extends React.Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false };
  }

  static getDerivedStateFromError(error) {
    // 更新 state 使下一次渲染能够显示降级后的 UI
    return { hasError: true };
  }

  componentDidCatch(error, errorInfo) {
    // 你同样可以将错误日志上报给服务器
    logErrorToMyService(error, errorInfo);
  }

  render() {
    if (this.state.hasError) {
      // 你可以自定义降级后的 UI 并渲染
      return <h1>Something went wrong.</h1>;
    }

    return this.props.children; 
  }
}
```

## Refs 转发

- React.forwardRef

```JSX
const FancyButton = React.forwardRef((props, ref) => (
  <button ref={ref} className="FancyButton">
    {props.children}
  </button>
));
```

在高阶组件中转发 refs

```JSX
function logProps(WrappedComponent) {
  class LogProps extends React.Component {
    componentDidUpdate(prevProps) {
      console.log('old props:', prevProps);
      console.log('new props:', this.props);
    }

    render() {
      return <WrappedComponent {...this.props} />;
    }
  }

  return LogProps;
}
```

## 高阶组件（HOC）

## Portals
