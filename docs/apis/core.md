---
sidebar_position: 1
id: Inula
---

# Inula

## 组件
### Component

**功能介绍**

`<Component>`组件：是类组件的基础形式，可以通过继承`<Component>`组件来定义一个类组件，并且可以使用类的特性，如构造函数、生命周期以及内部状态。

**组件定义**

```jsx
class Component<P, S, C> {
  props: P;
  context: C;
  state: S | null;
  refs: any;
  forceUpdate: any;
  isReactComponent: boolean;
  constructor(props: P, context: C);
  setState(state: S, callback?: Callback): void;
}
```



- `props`：组件接收的输入属性；
- `context`：组件的上下文对象；
- `state`：组件内部的状态；
- `refs`：用于引用组件中的 DOM 元素或其他组件。
- `forceUpdate`：用于强制重新渲染组件。
- `isReactComponent`：用于标识当前类是否是 React 组件类。
- `constructor(props: p, context: c)`：构造函数用于初始化组件的状态和属性。
- `setState(state: S, callback?: Callback): void`：用于更新组件的状态，并触发重新渲染

**示例**

```jsx
import * as Inula from 'openinula';

class Component extends Inula.Component {
  constructor(props) {
    super(props);
    this.state = {
      counter: 0
    };
    console.log('Constructor');
  }

  componentDidMount() {
    console.log('Component Did Mount');
  }

  componentDidUpdate() {
    console.log('Component Did Update');
  }

  componentWillUnmount() {
    console.log('Component Will Unmount');
  }

  handleIncrement = () => {
    this.setState(prevState => ({
      counter: prevState.counter + 1
    }));
  };

  render() {
    console.log('Render');
    return (
      <div>
        <h1>Counter: {this.state.counter}</h1>
        <button onClick={this.handleIncrement}>Increment</button>
      </div>
    );
  }
}
```



在上述示例中，基于`<Component>`类组件创建了一个`<Component>`组件以实现计数器功能，并在组件内部测试了一些声明周期的钩子函数。

### PureComponent

**功能介绍**

`<PureComponent>`组件：继承`<Component>`，但是它内置了`shouldComponentUpdate`方法，会自动比较`props`和`state`的变化，如果没有变化则不会重新渲染组件，从而提高性能。

**组件定义**

```jsx
class PureComponent<P, S, C> extends Component<P, S, C> {
    constructor(props: P, context: C);
}
```



**示例**

```jsx
import Inula, { PureComponent } from 'openinula';

// 创建一个继承自PureComponent的组件
class App extends PureComponent {
  render() {
    console.log('Render PureDemo');
    return <div>{this.props.value}</div>;
  }
}
```



上述示例中，继承了`<PureComponent>`组件来创建一个`App`类组件，其使用上与`<Component>`类组件类似，但是其内置了`shouldComponentUpdate`方法，可以直接使用。

> 注意：
> `<Component>`和`<PureComponent>`是`Inula`提供的两种常用组件类。可以根据其优缺点，在实际开发中选择使用
>
> | 组件          | 优点                               | 缺点                                                     |
> | ------------- |----------------------------------|--------------------------------------------------------|
> | PureComponent | 1.自带浅比较，提高性能<br/> 2.适用于大多数情况     | 1.涉及复杂的数据结构，会产生错误或者性能问题 <br/>   **对简单组件来说内置方法可能会存在额外开销 |
> | Component     | 1.较为灵活，没有内置方法，可以细粒度控制 2.适用于多控制场景 | 手动编写shouldComponentUpdate方法                            |
>
> 

### memo

**功能介绍**

`memo`是一个高阶组件（HOC），用于封装函数组件，以使其仅在其 `props` 发生更改时重新渲染。

**组件定义**

```jsx
const MemoizedComponent = memo(type, compare?: Function);
```



- `type`：表示需要被优化的函数式组件。
- `compare`：可选参数，用于比较前后两次渲染的属性和状态是否相同的比较函数。如果未提供该函数，默认使用浅层比较。

**示例**

```jsx
import { memo, useState } from 'openinula';

const ExpensiveComponent = ({ value }) => {
  // 模拟一个昂贵的计算
  const expensiveCalculation = () => {
    console.log("Calculating...");
    let result = 0;
    for (let i = 0; i < value * 1000000; i++) {
      result += i;
    }
    return result;
  };

  const result = expensiveCalculation();

  return <div>Result: {result}</div>;
};

const MemoizedExpensiveComponent = memo(ExpensiveComponent);

function App() {
  const [count, setCount] = useState(1);

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>Increment</button>
      <MemoizedExpensiveComponent value={count} />
    </div>
  );
}
```



在上述示例中，`<ExpensiveComponent>` 是一个昂贵的计算组件，它在每次渲染时都会执行昂贵的计算操作。通过使用 `memo`，可以对 `<ExpensiveComponent>` 进行优化，避免在 `count` 没有变化时重新渲染。`memo` 会对前后两次渲染的属性和状态进行浅层比较，如果相同则不会重新渲染。

### Fragment

**功能介绍**

`<Fragment>` 是一个虚拟组件，用于包裹一组元素而不添加额外的 DOM 元素。它可以用来解决在渲染多个元素时产生的包裹元素问题。

**示例**

```jsx
import Inula, { Fragment } from 'openinula';

function App() {
  return (
    <div>    
      {/* 使用 Fragment */}
      <Fragment>
        <div>Element 4</div>
        <div>Element 5</div>
        <div>Element 6</div>
      </Fragment>

      {/* 或者使用简化形式，使用空尖括号 */}
      <>
        <div>Element 7</div>
        <div>Element 8</div>
        <div>Element 9</div>
      </>
    </div>
  );
}
```



在上面的示例中，你可以看到 `<Fragment>` 的用法。当需要在组件中渲染多个元素时，使用 `<Fragment>` 可以避免添加额外的 DOM 元素。除了使用完整的 `<Fragment>` 标签外，你还可以使用 <> 和 </> 作为空尖括号的简化形式。

> 注意：`<Fragment>` 可以提高渲染性能并减少不必要的包装元素。在一些情况下，如果你不使用 `<Fragment>`，可能会出现额外的 DOM 层次结构，从而可能影响性能和样式。

### Children

**功能介绍**

`Children` 用于处理组件的子元素。它允许开发者在组件中访问和操作传递给组件的子元素，无论子元素是什么类型（单个元素、多个元素、文本等）。`Children`提供了一些方法来遍历、映射和操作子元素，使得操作子元素变得更加方便。

**示例**

```jsx
import Inula, { Children, cloneElement } from 'openinula';

// 示例 1: 遍历子元素并输出它们的类型
function ElementList({ children }) {
  const childArray = Children.toArray(children);

  const childTypes = childArray.map((child, index) => (
    <p key={index}>Child {index + 1} is of type: {child.type.name}</p>
  ));

  return <div>{childTypes}</div>;
}

// 示例 2: 映射子元素并添加额外的 props
function AddPropsToChildren({ children }) {
  const enhancedChildren = Children.map(children, child => {
    return cloneElement(child, { isHighlighted: true });
  });

  return <div>{enhancedChildren}</div>;
}

// 示例 3: 只渲染特定类型的子元素
function OnlyRenderCertainChildren({ children }) {
  const filteredChildren = Children.toArray(children).filter(child => {
    return child.type === 'p';
  });

  return <div>{filteredChildren}</div>;
}

function App() {
  return (
    <div>
      <ElementList>
        <h1>Heading</h1>
        <p>Paragraph 1</p>
        <p>Paragraph 2</p>
        <div>Div element</div>
      </ElementList>

      <AddPropsToChildren>
        <span>Child 1</span>
        <span>Child 2</span>
      </AddPropsToChildren>

      <OnlyRenderCertainChildren>
        <p>Paragraph 1</p>
        <div>Div element</div>
        <p>Paragraph 2</p>
      </OnlyRenderCertainChildren>
    </div>
  );
}
```



上述展示了的示例包含以下方法:

- `Children.map(children, fn, thisArg?)`
- `Children.forEach(children, fn, thisArg?)`
- `Children.only(children)`
- `Children.toArray(children)`。

在上面的示例中，展示了如何使用 `Children` 对子元素进行遍历、映射和筛选。`Children.toArray` 方法用于将子元素转换为数组，从而可以使用数组的方法。`Children.map` 方法可以映射子元素并为其添加额外的 `props`。同时还演示了如何通过 `child.type` 来识别子元素的类型，从而选择性地渲染特定类型的子元素。

### Suspense

**功能介绍**

`<Suspense>`是一个组件，用于在异步加载数据时展示一个fallback视图

**接口定义**

```tsx
<Suspense fallback={<FallbackComponent />}>
  <LazyComponent />
</Suspense>
```



- `fallback`: 用于定义在组件加载期间显示的fallback视图。
- `<LazyComponent />`: 表示需要异步加载的组件。

**示例**

```jsx
import Inula, { lazy, Suspense } from 'openinula';

// 异步加载组件
const AsyncComponent = lazy(() => import('./AsyncComponent'));

const App = () => (
  <div>
    {/* 在加载异步组件时显示fallback元素 */}
    <Suspense fallback={<div>Loading...</div>}>
      <AsyncComponent />
    </Suspense>
  </div>
);

// <AsyncComponent> 组件可以是一个普通的函数式组件或类组件
export default AsyncComponent = () => {
  // 随意模拟一个异步加载的操作
  setTimeout(() => {
    console.log('AsyncComponent is loaded.');
  }, 2000);

  return <div>Async Component</div>;
};
```



在上面的示例中，使用lazy函数来异步加载一个组件`<AsyncComponent>`。然后，使用`<Suspense>`组件来包裹异步组件，并在加载组件时显示fallback元素。在这个示例中，fallback元素是一个简单的“Loading...”文本。

## 钩子函数

在`Inula`中，钩子函数使用来在函数组件中添加状态和其他Inula特性函数。可以让我们在不编写类组件的情况下使用状态和`Inula`的其他功能。通过使用钩子函数，我们可以更方便地编写和管理React组件的状态和行为，使代码更简洁、可读性更高。

钩子函数可以让我们在函数组件中使用状态管理、生命周期方法、副作用等功能。常用的钩子函数包括`useState`、`useEffect`、`useContext`、`useReducer`等。

> 注意：
>
> 1. 使用钩子函数只能在组件的顶层 或自己的 Hook 中调用它。
> 2. 不能在循环语句或条件语句中调用它。

### useState

**功能介绍**

`useState`是一个用于在函数组件中添加状态(`state`)的钩子函数。使用`useState`可以让函数组件拥有类组件的状态管理能力。

**接口定义**

```jsx
const [state, setState] = useState(initialState);
```



- `initialState`：状态的初始值，在组件挂载时使用。

`useState`的返回值：

- `state`：表示当前状态的值。
- `setState`：用于更新状态的函数，可以传递新的状态值或一个函数来计算新的状态值。

**示例**

```jsx
import Inula, { useState } from "openinula";

function App() {
  const [count, setCount] = useState(0);

  const increment = () => {
    setCount(prevCount => prevCount + 1);
  };

  const decrement = () => {
    setCount(prevCount => prevCount - 1);
  };

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={increment}>Increment</button>
      <button onClick={decrement}>Increment</button>
    </div>
  );
}
```



### useCallback

**功能介绍**

`useCallback` 是一个用于优化性能的钩子函数，它可以用来缓存函数引用，避免在每次渲染时重新创建函数。这对于将函数作为 `props` 传递给子组件时特别有用，可以避免不必要的重新渲染。

**接口定义**

```tsx
const cachedFn = useCallback(callback: T, deps: DependencyList)
```



- `callback`：要缓存的回调函数。
- `deps`：一个数组，包含了所有可以作为依赖项的值，当依赖项发生变化时，`callback` 函数会被重新创建。如果依赖项列表为空，那么每次渲染都会返回相同的缓存函数。

`useCallback`的返回值：

- `cachedFn`: 若是初次渲染，返回已传入的`callback`函数，当依赖项`deps`无变化时，返回上次渲染缓存的`callback`函数，当依赖项`deps`有变化时，则返回这次渲染传入的`callback`函数

**示例**

```jsx
import Inula, { useState, useCallback } from 'openinula';

function App() {
  const [count, setCount] = useState(0);

  // 使用 useCallback 缓存 handleIncrement 函数
  const handleIncrement = useCallback(() => {
    setCount(count + 1);
  }, [count]); // 仅当 count 发生变化时，才会重新创建函数

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={handleIncrement}>Increment</button>
    </div>
  );
}
```



> 注意：使用 `useCallback`，可以确保每次渲染时都使用相同的函数引用，从而减少不必要的函数重新创建，提高性能。这在传递回调函数给子组件或在使用 `useEffect` 时尤为有用，因为它可以防止因为函数的重新创建而导致子组件的不必要重新渲染。

### useContext

**功能介绍**

`useContext` 用于在组件之间共享状态。通过使用 `useContext`，可以避免在组件树中手动传递 `props`，从而更方便地访问共享的数据。

**接口定义**

```tsx
const value = useContext(context: Context<T>)
```



- `context`: 是`Inula`提供的上下文，可以通过`createContext`创建，`useContext`接收上下文并返回该上下文当前值。

`useContext`的返回值：

- `value`: 其返回值是一个包含Provider的值，改`value.Provider`是由最近的上层上下文提供，如果没有找到匹配的上层上下文，则会使用上下文对象的默认值。

**示例**

```jsx
import Inula, { createContext, useContext, useState } from 'openinula';

// 创建一个上下文对象
const Context = createContext();

// 组件A：向上下文中提供一个共享的数据
const ComponentA = () => {
  const [count, setCount] = useState(0);

  return (
    <Context.Provider value={count}>
      <button onClick={() => setCount(count + 1)}>增加</button>
      <ComponentB />
    </Context.Provider>
  );
}

// 组件B：从上下文中获取共享的数据
const ComponentB = () => {
  const count = useContext(Context);

  return <div>当前计数：{count}</div>;
}

// 主组件
const App = () => {
  return <ComponentA />;
}
```



在这个示例中，`<ComponentA>` 创建了一个共享的数据 `count`，它使用 `useState` 来进行状态管理，并将数据存储在上下文对象` Context.Provider` 中。`<ComponentB>` 使用 `useContext` 从 `Context` 中获取共享的数据 `count`。
最后，`<App>` 组件作为根组件渲染 `<ComponentA>`，这样就能够在整个组件树中共享 `count` 数据。

> 说明：在函数组件中，`useContext`代替了类组件中的`Consumer`。

### useEffect

**功能介绍**

`useEffect` 是一个用于处理副作用（如数据获取、订阅、DOM 操作等）的钩子函数。它在组件的生命周期中执行，并且可以用于在组件渲染后执行一些操作，或在组件卸载前执行一些清理工作。

**接口定义**

```tsx
useEffect(create: EffectCallBack, deps?: DependencyList): void
```



- `create`：一个函数，用于定义副作用操作。该函数在每次渲染后都会被调用。
- `deps`：一个数组，指定了哪些依赖项的变化会触发 `create` 的重新执行。如果不传递这个数组，那么 `create` 将在每次渲染后都被调用。如果传递了空数组 []，`create` 将只在组件挂载和卸载时执行，不会有其他依赖项触发重新执行。

**示例**

- 示例一

```jsx
import Inula, { useState, useEffect } from "openinula";

function App() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    document.title = `点击次数：${count}`;
  }, [count]);

  return (
    <div>
      <p>点击次数: {count}</p>
      <button onClick={() => setCount(count + 1)}>增加</button>
    </div>
  );
}
```



在上述示例中，`useEffect`可以用于数据的获取，当点击`<button>`时，就会更新标题的点击次数。

- 示例二：

```tsx
import Inula, { useState, useEffect } from "openinula";

function Timer() {
  const [time, setTime] = useState(0);

  useEffect(() => {
    const intervalId = setInterval(() => {
      setTime(time + 1);
    }, 1000);

    return () => {
      clearInterval(intervalId);
    };
  }, [time]);

  return <div>Time: {time}</div>;
}

function App() {
  const [showTimer, setShowTimer] = useState(true);

  return (
    <div>
      {showTimer && <Timer />}
      <button onClick={() => setShowTimer(!showTimer)}>
        {showTimer ? 'Hide Timer' : 'Show Timer'}
      </button>
    </div>
  );
}
```



在示例二中，`useEffect`可以用于设置和清理定时器。

- 示例三：

```tsx
useEffect(() => {
  // 更新元素样式
  document.getElementById('myElement').style.color = 'red';

  // 添加元素
  const newElement = document.createElement('div');
  document.body.appendChild(newElement);

  // 移除元素
  return () => {
    document.body.removeChild(newElement);
  };
}, []);
```



上述示例展示`useEffect`的DOM操作，可以在`useEffect`中进行DOM操作，比如更新元素的样式、添加/移除元素等。

- 示例四：

根据依赖项的变化，`useEffect` 的行为会有所不同。在函数组件中，通常使用`useEffect`来模拟组件的生命周期。

```tsx
useEffect(() => {
  // componentDidMount
  console.log('Component mounted');

  // componentDidUpdate
  return () => {
    // componentWillUnmount
    console.log('Component unmounted');
  };
}, []);

useEffect(() => {
  // componentDidUpdate
  console.log('Component updated');
}, [data]);
```



上述示例中，使用`useEffect`来模拟组件的生命周期方法，比如`componentDidMount`、`componentDidUpdate`和`componentWillUnmount`。

### useLayoutEffect

**功能介绍**

`useLayoutEffect` 是一个钩子函数，用于在 DOM 更新完成后同步执行副作用操作。与 `useEffect` 不同，`useLayoutEffect` 会在 DOM 更新之后、浏览器绘制之前同步执行，因此适用于需要立即操作 DOM 的情况。

**接口定义**

```jsx
useLayoutEffect(create: EffectCallBack, deps?: DependencyList): void;
```



- `create`：一个函数，用于定义副作用操作。该函数会在 DOM 更新完成后、浏览器绘制之前立即执行。
- `deps`：一个数组，指定了哪些依赖项的变化会触发 `create` 的重新执行。如果不传递这个数组，那么 `create` 将在每次渲染后都被调用。如果传递了空数组 []，`create` 将只在组件挂载和卸载时执行，不会有其他依赖项触发重新执行。

**示例**

```jsx
import Inula, { useState, useLayoutEffect } from 'openinula';

function App() {
  const [width, setWidth] = useState(0);

  useLayoutEffect(() => {
    const handleResize = () => {
      setWidth(window.innerWidth);
    };

    window.addEventListener('resize', handleResize);
    handleResize(); // 初始渲染时执行一次

    return () => {
      window.removeEventListener('resize', handleResize);
    };
  }, []); // 仅在挂载和卸载时执行

  return (
    <div>
      <p>Window Width: {width}</p>
    </div>
  );
}
```



在上述示例中，`useLayoutEffect` 用于更新页面上窗口的宽度。它添加了一个 `resize` 事件监听器，在窗口大小变化时同步更新 width 状态。注意，这里使用了空数组作为依赖项，这意味着 `useLayoutEffect` 只在组件挂载和卸载时执行。

> 注意：
> `useEffect` 和 `useLayoutEffect`都用于在组件渲染时执行副作用操作，但是主要区别与执行的时机。`useEffect` 会在组件渲染完成后异步执行，不会阻塞组件的渲染过程。而 `useLayoutEffect` 会在组件渲染完成后同步执行，会阻塞组件的渲染过程。因此，如果需要在组件渲染完成后立即执行某些操作，可以使用 `useLayoutEffect`，否则可以使用 `useEffect`。

```tsx
import Inula, { useState, useEffect, useLayoutEffect } from 'openinula';

const App = () => {
  const [count, setCount] = useState(0);

  useEffect(() => {
    console.log('useEffect');
    document.title = `Count: ${count}`;
  }, [count]);

  useLayoutEffect(() => {
    console.log('useLayoutEffect');
    document.title = `Count: ${count}`;
  }, [count]);

  return (
    <div>
      <h1>Count: {count}</h1>
      <button onClick={() => setCount(count + 1)}>Increment</button>
    </div>
  );
};
```



在上面的例子中，若使用`useEffect`来更新页面标题，它会在组件渲染完成后执行，并且只有在`count`发生变化时才会执行。若使用`useLayoutEffect`来更新页面标题，它会在组件渲染完成后立即执行，并且只有在`count`发生变化时才会执行。

> 注意：
> 由于 `useLayoutEffect` 是同步执行的，如果操作的是 DOM，可能会影响页面性能。在大多数情况下，使用 `useEffect` 就足够了，因为它会在 DOM 更新完成后异步执行副作用，不会阻塞浏览器的渲染。只有在需要即时操作 DOM 以获取测量值等情况下，才考虑使用 `useLayoutEffect`。

### useMemo

**功能介绍**

`useMemo` 是一个用于性能优化的钩子函数，它用于在组件重新渲染时，缓存和返回一个计算结果，以避免不必要的重复计算。

**接口定义**

```tsx
const cachedValue = useMemo(create, deps)
```



- `create`：一个函数，用于计算需要缓存的值。
- `deps`：一个数组，指定了哪些依赖项的变化会触发重新计算 `create`。当依赖项发生变化时，`create` 将会被重新执行，返回一个新的缓存值。如果依赖项列表为空，那么` create` 只在组件挂载时执行一次。

`useMemo`的返回值：

- `cachedValue`: 要缓存计算值的函数，是任意类型，如果deps没有发生变化，会直接返回相同的值，如果deps发生变化，则调用create，并返回新的结果，然后缓存这个结果。

**示例**

```jsx
import Inula, { useState, useMemo } from 'openinula';

function ExpensiveComponent({ number }) {
  // 模拟一个昂贵的计算
  const expensiveCalculation = () => {
    console.log("Calculating...");
    let result = 0;
    for (let i = 0; i < number * 1000000; i++) {
      result += i;
    }
    return result;
  };

  // 使用 useMemo 缓存计算结果
  const memoizedValue = useMemo(() => expensiveCalculation(), [number]);

  return (
    <div>
      <p>Number: {number}</p>
      <p>Calculated Value: {memoizedValue}</p>
    </div>
  );
}

function App() {
  const [count, setCount] = useState(1);

  return (
    <div>
      <button onClick={() => setCount(count + 1)}>Increment</button>
      <ExpensiveComponent number={count} />
    </div>
  );
}
```



在这个示例中，`ExpensiveComponent` 组件包含一个昂贵的计算操作，该计算在每次渲染时都会执行。通过使用 `useMemo`，可以缓存计算结果，只有在 `number` 发生变化时才重新计算。这可以有效避免在每次渲染时都执行昂贵的计算，从而提高性能。

> 注意：`useMemo` 可以在性能优化方面发挥作用，但不应该被滥用。在某些情况下，过度使用 `useMemo` 可能会导致代码更难理解。只有在确实需要避免重复计算的情况下，才应该使用 `useMemo`。

### useReducer

**功能介绍**

`useReducer` 是一个用于管理状态的钩子函数，它结合了`useState` 和自定义的状态更新逻辑。通常用于管理复杂的状态逻辑。

**接口定义**

```jsx
const [state, dispatch] = useReducer(reducer, initialState, init?);
```



- `reducer`：一个函数，用于定义状态更新逻辑。它接收当前状态 state 和一个表示动作的对象 action，并返回新的状态。
- `initialState`：初始状态的值， 计算逻辑取决于init参数。
- `init`:可选参数，用于计算初始状态值的函数，如果存在，则使用`init(initialState)`的执行结果作为初始值，否则使用`initialState`

`useReducer`有两个返回值：

- `state`：当前状态的值。
- `dispatch`：一个函数，用于触发状态更新。它接收一个表示动作的对象，并将该对象传递给 reducer，从而更新状态.

**示例**

```jsx
import Inula, { useReducer } from 'openinula';

// 定义 reducer 函数
const counterReducer = (state, action) => {
  switch (action.type) {
    case 'INCREMENT':
      return { count: state.count + 1 };
    case 'DECREMENT':
      return { count: state.count - 1 };
    default:
      return state;
  }
};

function App() {
   // 使用 useReducer 来管理状态
  const [state, dispatch] = useReducer(counterReducer, { count: 0 });
  return (
    <div>
      <p>Count: {state.count}</p>
      <button onClick={() => dispatch({ type: 'INCREMENT' })}>Increment</button>
      <button onClick={() => dispatch({ type: 'DECREMENT' })}>Decrement</button>
    </div>
  );
}
```



在上述示例中，使用 `useReducer` 来管理计数器的状态。`counterReducer` 函数定义了状态更新的逻辑，根据不同的动作类型（'INCREMENT' 和 'DECREMENT'）更新状态。通过 dispatch 函数触发状态更新，传递一个表示动作的对象，该对象包含了要执行的操作类型。

> 注意：相比使用多个`useState`来管理状态，`useReducer` 适用于具有复杂状态逻辑的情况，它能够更好地组织和管理状态变化。

### useRef

**功能介绍**

`useRef` 用于创建一个引用对象，可以用来存储组件的引用或持久化的值。与 `useState` 不同，`useRef` 创建的引用对象在更新时不会触发组件重新渲染。

**接口定义**

```jsx
const refContainer = useRef(initialValue);
```



- `initialValue`：引用对象的初始值，可以是任何值，通常设置为 null。

`useRef`的返回值：

- `refContainer`：一个引用对象，其中的 current 属性可以被用于存储和访问值。
  **示例**

```jsx
import Inula, { useRef } from 'openinula';

function App() {
  const inputRef = useRef(null);

  const handleButtonClick = () => {
    // 使用 ref 获取 input 元素的引用，并设置焦点
    inputRef.current.focus();
  };

  return (
    <div>
      <input type="text" ref={inputRef} />
      <button onClick={handleButtonClick}>设置焦点</button>
    </div>
  );
}
```



上面的示例中，创建了一个函数组件 `RefDemo`，使用 `useRef` 钩子创建了一个名为 `inputRef` 的 `ref` 对象。在 JSX 中，将这个 `ref` 对象绑定到输入框元素上。

当按钮被点击时，`handleButtonClick` 函数会使用 inputRef.current 来获取输入框的 DOM 元素，并调用 `focus()` 方法来设置焦点。这样，点击按钮后，输入框就会自动获得焦点。

> 注意：`useRef` 不仅适用于获取 DOM 元素的引用，还可以在函数组件中存储和访问任意可变值。与 `state`一样，`ref` 可以指向任何东西：比如字符串、对象甚至函数。与 `state` 不同的是，`ref` 是一个带有当前属性的普通对象，可以读取和修改。

**存储变量**

```tsx
import Inula, { useState, useRef, useCallback } from 'openinula'

export default function ClickWatch() {
  let ref = useRef(0);

    const handleClick = useCallback(() => {
        ref.current = ref.current + 1;
        alert('You clicked ' + ref.current + ' times!');
    }, [])

  return (
    <button onClick={handleClick}>
      Click me!
    </button>
  );
}
```



在上述示例中，将在每次点击后增加 `ref.current`。

> 注意：组件不会在每次递增时都触发重新渲染。 像 `state` 一样，`ref` 在 重新渲染时被 `Inula` 保留。然而，`setState`将会重新渲染组件，而设置 `ref` 则不会。

### useImperativeHandle

**功能介绍**

`useImperativeHandle` 是一个用于定制在父组件中访问子组件实例方法的钩子函数。通常情况下，`Inula`鼓励使用 `props` 和状态来进行组件之间的通信，但在某些情况下，可能需要从父组件中直接调用子组件的方法。

**接口定义**

```jsx
useImperativeHandle(ref, createHandle, deps?)：void;
```



- `ref`：一个`Inula`引用对象，通常通过 `createRef()` 创建。
- `createHandle`：一个函数，用于创建子组件实例中需要暴露给父组件的方法或属性。
- `deps`：可选参数，一个依赖数组，当依赖项变化时，会重新计算 `createHandle`。通常在这里指定子组件的内部状态或其他信息，以确保暴露给父组件的方法在这些依赖项变化时得到更新。

**示例**

```jsx
import Inula, { useRef, useImperativeHandle, forwardRef } from 'openinula';

// 子组件
const ChildComponent = forwardRef((props, ref) => {
  const inputRef = useRef();

  // 暴露给父组件的方法
  useImperativeHandle(ref, () => ({
    focusInput: () => {
      inputRef.current.focus();
    }
  }));

  return <input ref={inputRef} />;
});

// 父组件
function App() {
  const childRef = useRef();

  const handleFocusInput = () => {
    childRef.current.focusInput();
  };

  return (
    <div>
      <ChildComponent ref={childRef} />
      <button onClick={handleFocusInput}>Focus Input</button>
    </div>
  );
}
```



在这个例子中，`<ChildComponent>` 使用 `useImperativeHandle` 来创建一个名为 `focusInput` 的方法，父组件可以通过` ref` 调用这个方法来聚焦输入框。通过这种方式，你可以在需要时从父组件中控制子组件的行为。

> 注意：`useImperativeHandle` 应该避免过度使用，因为它破坏了组件之间的封装性，通常情况下应该优先考虑使用 `props` 和状态来实现组件之间的通信。

## APIs

### render

**功能介绍**

在指定的DOM元素下渲染`Inula`组件。

**接口定义**

```tsx
function render(children: any, container: Container, callback?: any): Element | Text | null
```



* `children`：是需要挂载的Inula组件
* `container`：是DOM元素
* `callback`：是挂载后的回调函数

**示例**

```tsx
import { render } from 'openinula';

function App() {
  return <h1>hello</h1>;
}

render(<App />, document.getElementById('div'));
```



上述示例展示在DOM元素`<div>`下挂载了组件`<App>`，组件`<App>`返回了一个html标题元素。

### createElement

**功能介绍**

当无法使用jsx语法时，可以使用`createElemet`创建一个Inula元素。

**接口定义**

```tsx
function createElement(type: any, setting: any, ...children: any[]): {
    [x: string]: any;
    vtype: number;
    src: null;
    type: any;
    key: any;
    ref: any;
    props: any;
}
```



* `type`是组件类型。
* `setting`是创建元素的`props`对象。
* `...children`是零个或多个子节点。

**示例**

```tsx
import { createElement } from 'openinula';

function Welcome() {
  return createElement(
    'h1',
    { className: 'welcome' },
    '你好'
  );
}
```



在上述示例中，创建了一个`<Welcome>`函数组件，这个组件返回了一个`h1`元素，其中包含了一个`className`属性，属性值为'welcome'，并包含文本内容为'你好'。

### createPortal

**功能介绍**

`createPortal`可以将某些元素的子元素渲染到DOM中的不同位置。

**接口定义**

```tsx
function createPortal(children: any, realNode: any, key?: string): PortalType

type PortalType = {
  vtype: number;
  key: null | string;
  realNode: any;
  children: any;
};
```



* `children`：是需要渲染的Inula组件
* `readNode`：是某个已经存在的DOM节点
* `key`：可选参数，用作 portal key的独特字符串或数字。

**示例**

```tsx
import { createPortal } from 'openinula';

function Component() {
  return (
    <div>
      <p>这个子节点被放置在父节点 div 中</p>
      {createPortal(
        <p>这个子节点被放置在 document body 中</p>,
        document.body
      )}
    </div>
  );
}
```



在这段代码中，`createPortal`函数传入的第一个参数是一个`<p>`标签，第二个参数是`document.body`，意味着这个`<p>`标签将被创建并插入到文档的`body`部分中。这样，即使父组件的`<div>`被其他元素覆盖，这个`<p>`标签也会被放置在`body`的最顶层，而不会被覆盖。

### createRef

**功能介绍**

`createRef`用于在类组件中声明一个`ref`对象。

**接口定义**

```tsx
function createRef(): RefType

type RefType = {
  current: any;
};
```



**示例**

```tsx
import { Component, createRef } from 'openinula';

class App extends Component {
  inputRef = createRef();

  handleClick = () => {
    this.inputRef.current.value++;
  }

  render() {
    return (
      <>
        <input ref={this.inputRef} />
        <button onClick={this.handleClick}>
          点击增加
        </button>
      </>
    );
  }
}
```



上述示例创建了一个 `App` 组件，通过调用 `createRef()` 函数创建一个对象 `inputRef`。`ref` 对象可以用来引用DOM元素或组件实例。在渲染时，`handleClick` 方法会被调用，它会通过 `this.inputRef.current.value` 来增加 `input` 元素的值。`input` 元素通过 `ref` 属性引用 `inputRef`。这样就可以通过 `this.inputRef` 来操作输入框。

### forwardRef

**功能介绍**

`forwardRef`可以使得组件可以通过参数中的ref将一个DOM节点暴露给父组件。

**类型定义**

```
function forwardRef(render: Function): {
    vtype: number;
    $$typeof: number;
    render: Function;
}
```



* `render`: 是一个组件渲染函数，可以接收props和ref作为参数。

**示例**

```tsx
import { forwardRef, useRef } from 'openinula';

const MyInput = forwardRef(function MyInput(props, ref) {
  const { label, ...otherProps } = props;
  return (
    <label>
      {label}
      <input {...otherProps} ref={ref} />
    </label>
  );
});

export default function Form() {
  const ref = useRef(null);

  function handleClick() {
    ref.current.value++;
  }

  return (
    <form>
      <MyInput label="Auto add:" ref={ref} />
      <button type="button" onClick={handleClick}>
        Edit
      </button>
    </form>
  );
}
```



上述示例中，`MyInput` 组件通过 `forwardRef` 将输入框暴露给父组件 `Form`，这样 `Form` 可以通过该 `Ref` 操作 `Inupt`。

### lazy

**功能介绍**

`lazy`声明一个懒加载组件，直到该组件第一次被加载前，才会加载该组件的代码。

**接口定义**

```tsx
function lazy<T>(promiseCtor: () => PromiseType<{
    default: T;
}>): LazyComponent<T, LazyContent<T>>
```



* `promiseCtor`是一个返回`Promise`对象的函数，第一次渲染组件前，`Inula`才会调用该函数获取组件。

**示例**

```tsx
import { lazy } from 'openinula';

const LazyComp = lazy(() => import { App } from './App');
```



在上述示例中，使用`lazy`函数来异步加载`<App>`组件。`lazy` 函数接受一个函数，该函数返回一个`import()`语句，用于动态加载组件模块。

### memo

**功能介绍**

使用 `memo`` 函数包裹组件，通常情况下，只要该组件的 props 没有改变，可以防止组件的重新渲染。

**接口定义**

```tsx
function memo<Props>(type: any, compare?: ((oldProps: Props, newProps: Props) => boolean) | undefined): {
    vtype: number;
    $$typeof: number;
    type: any;
    compare: ((oldProps: Props, newProps: Props) => boolean) | null;
}
```



* `Vtype`是Inula Node。
* `typeof`：是需要适配react Node。
* `type`是需要进行记忆化的组件。
* `compare`：可选参数，是一个函数，接收一个旧`props`和新`props`，如果旧的`props`和新`props`完全相同，则返回true，反之，返回false。

**示例**

```tsx
import { memo, useState } from 'openinula';


const Component = ({ name }) => {
  return (
    <div>
      Hello, {name}!
    </div>
  );
};

// 使用memo将MyComponent包装
const MemoizedComponent = memo(MyComponent);

const App = () => {
  const [count, setCount] = React.useState(0);

  const handleClick = () => {
    setCount(prevCount => prevCount + 1);
  };

  return (
    <div>
      {/* 每次更新会重新渲染MyComponent */}
      <MyComponent name="Alice" />

      {/* 使用MemoizedComponent，只有在name发生变化时才会重新渲染 */}
      <MemoizedComponent name="Bob" />

      <div>
        Current count: {count}
        <button onClick={handleClick}>Increment</button>
      </div>
    </div>
  );
};
```



在上述示例中，创建了两个组件，一个是普通Component组件，一个是使用memo包装的MemoizedComponent。使用memo包装的MemoizedComponent只会在props的name属性发生变化时重新渲染。由于name属性没有变化，所以在点击按钮时，MemoizedComponent不会重新渲染，也不会执行Component函数。

> 注意：在实际场景中，memo可以有效地避免无需更新的组件重新渲染，提高应用的性能。

### createContext

**功能介绍**

`createContext`创建一个可供不同组件调用的上下文对象，这样各层组件之间不需要通过逐层传递信息。

**接口定义**

```tsx
function createContext<T>(val: T): ContextType<T>
```



* `val`: 是context的初始值。

**示例**

```tsx
import { createContext } from 'openinula';

const LanguageContext = createContext('zh-cn');


function Header() {
  const locale = useContext(LanguageContext);
  // ...
}

function Footer() {
  const locale = useContext(LanguageContext);
  // ...
}
```



上述示例展示了createContext方法的使用方式，可以通过createContext创建一个供全局组件使用的LanguageContext，组件之间可以直接获取，不需要通过逐级传值的方式进行获取。

### cloneElement

**功能介绍**

`cloneElement` 可以从一个已有元素创建一个新的Inula元素。

**接口定义**

```jsx
function cloneElement(element: any, setting: any, ...children: any[]): {
    [x: string]: any;
    vtype: number;
    src: null;
    type: any;
    key: any;
    ref: any;
    props: any;
}
```



* `element`是一个Inula元素。
* `setting`是克隆后元素的props。
* `...children`是零个或多个子节点，如果不传入，将使用原始`element.props.children`。

**示例**

```jsx
import { cloneElement } from 'openinula';

function Time({ time }) {
  return createElement(
    'h1',
    { className: 'clock' },
    'Time is ',
    createElement('i', null, time),
  );
}
function Clock() {
  const time = new Date().toLocaleTimeString();
  return createElement(
    Time,
    { time: time }
  );
}
```



### findDOMNode

**功能介绍**

`findDOMNode`获取类式组件实例对应的浏览器 DOM 节点。

**接口定义**

```tsx
function findDOMNode(domOrEle?: Element | undefined): null | Element | Text
```



* `domOrEle`是一个Component子类的实例。

**示例**

```jsx
import { Component, findDOMNode } from 'openinula';

class App extends Component {
  componentDidMount() {
    const input = findDOMNode(this);
    input.select()
  }

  render() {
    return <input defaultValue="你好" />
  }
}
```



在上述代码中，通过`findDOMNode`获取组件中的DOM节点。在`componentDidMount`生命周期方法中，通过调用`findDOMNode(this)`来获取组件实例对应的DOM节点，并将其赋值给`input`变量。然后，通过调用`input.select()`来选中输入框中的文本。

### flushSync

**功能介绍**

`flushSync`是一种同步更新界面的方法，它可以确保在当前渲染中的所有状态更新都被同步处理，并且能在更新完毕后立即执行其他代码。

**接口定义**

```tsx
function flushSync(fn: any): any
```



- `fn`：表示一个函数作为参数，在这个回调函数中执行需要同步处理的更新操作。

**示例**

```jsx
import Inula, { useState, flushSync } from 'openinula';

function App() {
  const [count, setCount] = useState(0);

  const handleClick = () => {
    flushSync(() => {
      setCount(count + 1);
      console.log(count); // 输出0，因为更新还未同步处理完成
    });

    console.log(count); // 输出1，因为更新已经同步处理完成
  };

  return (
    <div>
      <button onClick={handleClick}>Increment</button>
      <p>Count: {count}</p>
    </div>
  );
}
```



在上面的代码中，使用`useState`来创建一个状态`count`，然后在按钮的点击事件处理函数`handleClick`中使用`flushSync`来确保`setCount`的更新操作被同步处理。注意，在调用`flushSync`之后，可以立即访问到最新的`count`值。

### unmountComponentAtNode

**功能介绍**

从 DOM 中移除一个已挂载的 Inula 组件。

**接口定义**

```tsx
function unmountComponentAtNode(container: Container): boolean
```



* `container`是一个DOM节点。

**示例**

```jsx
import { render, unmountComponentAtNode } from 'openinula';

function App() {
  return <h1>hello</h1>;
}

const rootNode = document.getElementById('root');

render(<App />, rootNode);

// ...
unmountComponentAtNode(rootNode);
```



在上述代码中，定义了一个App组件，并通过render函数将其渲染到rootNode节点下，最后通过unmountComponentAtNode 函数可以将渲染在rootNode节点下的组件卸载，即从DOM中移除组件。

## Tools

### isFragment

**功能介绍**

`isFragment` 用于检查给定的 `Inula` 组件是否是一个 `Fragment` 。

**接口定义**

```tsx
function isFragment(ele: any): ele is InulaElement;

interface InulaElement<
  P = any,
  T extends string | JSXElementConstructor<any> = string | JSXElementConstructor<any>
> {
  type: T;
  props: P;
  key: Key | null;
}

type JSXElementConstructor<P> =
  | ((props: P) => InulaElement<any, any> | null)
  | (new (props: P) => Component<any, any>);
```



`ele`：输入任意待检查组件。

**示例**

```typescript
import Inula, { isFragment } from 'openinula';

function FragmentComponent(props) {
  if (isFragment(props.children)) {
    return <>{props.children}</>;
  } else {
    return <div>{props.children}</div>;
  }
}

function App() {
  return (
    <FragmentComponent>
      <p>Some text</p>
    </FragmentComponent>
  );
}
```



### isElement

**功能介绍**

`isElement` 用于检查给定的值是否是一个 `Inula`组件。

**接口定义**

```tsx
function isElement(ele: any): ele is InulaElement;
```



**参数介绍**
`ele`：输入的任意待检查元素。

**示例**

```tsx
import Inula, { isElement } from 'openinula';

const componentElement = <InulaComponent />;
const htmlElement = <div>Hello, World!</div>;

console.log(isElement(componentElement)); // 输出: true
console.log(isElement(htmlElement));      // 输出: true
console.log(isElement('string'));         // 输出: false
```



### isValidElement

**功能介绍**

`isValidElement` 用于检测结构体是否为合法的 `Element` 组件

**接口定义**

```tsx
function isValidElement<P>(element: KVObject | null | undefined): element is HorizonElement<P>;
type KVObject = Record<string, any>;
```



`ele`：输入的任意待检查元

**示例**

```tsx
import Inula, { isValidElement } from 'openinula';

const element = <div>Hello, World!</div>;

console.log(isValidElement(element));          // 输出: true
console.log(isValidElement('Hello World!'));   // 输出: false
```



### isValidElementType

**功能介绍**

`isValidElementType` 用于检查给定的值是否是有效的 `Inula`组件类型

**接口定义**

```tsx
function isValidElementType(type: any): boolean;
```



`type`：输入的任意待检查元素类型。

**示例**

```tsx
import Inula, { isValidElementType } from 'openinula';

console.log(isValidElementType('div'));           // 输出: false
console.log(isValidElementType(InulaComponent));  // 输出: true
console.log(isValidElementType(() => {}));        // 输出: false
console.log(isValidElementType('string'));        // 输出: true
```



### isForwardRef

**功能介绍**

`isForwardRef` 用于检查给定的 `Inula` 元素是否是一个 `ForwardRef`（转发引用）对象。

**接口定义**

```tsx
function isForwardRef(ele: any): ele is InulaElement;
````



`ele`：输入的任意待检查元素。
```

**示例**

```tsx
import Inula, { forwardRef, isForwardRef } from 'openinula';

const ForwardedComponent = forwardRef((props, ref) => (
  <div ref={ref}>Forwarded Component</div>
));

console.log(isForwardRef(ForwardedComponent));  // 输出: true
console.log(isForwardRef(() => {}));            // 输出: false
```



### isLazy

**功能介绍**

`isLazy` 用于检查给定的值是否是一个懒加载组件。

**接口定义**

```tsx
function isLazy(ele: any): ele is LazyComponent<any>;

type LazyComponent<T extends ComponentType<any>> = ExoticComponent<ComponentPropsWithRef<T>> & {
  readonly _result: T;
};

interface ExoticComponent<P = KVObject> {
  (props: P): InulaElement | null;
}
```

`ele`：输入的任意待检查元素。

**示例**

```tsx
import Inula, { lazy, isLazy } from 'openinula';

const LazyComponent = lazy(() => import('./LazyComponent'));

console.log(isLazy(LazyComponent)); // 输出: true
console.log(isLazy(() => {}));      // 输出: false
```

### isMemo

**功能介绍**

`isMemo` 用于检查给定的值是否是一个 `memoized` 组件

**接口定义**

```tsx
function isMemo(ele: any): ele is MemoComponent<any>;

type MemoComponent<T extends ComponentType<any>> = ExoticComponent<ComponentPropsWithRef<T>> & {
  readonly type: T;
  displayName?: string;
};
```



`ele`：输入的任意待检查元素。

**示例**

```tsx
import Inula, { memo, isMemo } from 'openinula';

const MemoizedComponent = memo(() => (
  <div>Memoized Component</div>
));

console.log(isMemo(MemoizedComponent)); // 输出: true
console.log(isMemo(() => {}));          // 输出: false
```



### isPortal

**功能介绍**

`isPortal` 用于检查给定的值是否是一个 `Portal` 组件

**接口定义**

```tsx
function isPortal(ele: any): ele is InulaElement;
````



`ele`：输入的任意待检查元素

**示例**

```tsx
import Inula, { isPortal, Component, createPortal } from 'openinula';

class PortalComponent extends Component {
  render() {
    return createPortal(
      this.props.children,
      document.getElementById('root')
    );
  }
}

console.log(isPortal(<PortalComponent />)); // 输出: true
console.log(isPortal(() => {}));            // 输出: false
```



### isContextProvider

**功能介绍**

`isContextProvider` 用于检查给定的值是否是一个 `Provider` 组件

**接口定义**

```tsx
function isContextProvider(ele: any): ele is InulaElement;
````



`ele`：输入的任意待检查元素

**示例**

```tsx
import Inula, { createContext, isContextProvider } from 'openinula';

const MyContext = createContext();

console.log(isContextProvider(MyContext.Provider)); // 输出: true
console.log(isContextProvider(() => {}));           // 输出: false
```



### isContextConsumer

**功能介绍**

`isContextConsumer` 用于检查给定的值是否是一个 `Context` 组件

**接口定义**

```tsx
function isContextConsumer(ele: any): ele is InulaElement;
````



`ele`：输入的任意待检查元素

**示例**

```tsx
import Inula, { createContext, isContextProvider } from 'openinula';

const MyContext = createContext();

console.log(isContextProvider(MyContext.Consumer)); // 输出: true
console.log(isContextProvider(() => {}));           // 输出: false
```