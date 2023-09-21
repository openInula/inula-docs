---
sidebar_position: 2
id: inulaX
hide_title: true
---

inulaX是集成在`openinula`框架中用于状态管理的库。它能集中式地存储、管理应用中所有组件的状态，并通过相应的规则(`actions`)保证状态(`state`)以可预测的方式发生变化。

## createStore()

**功能介绍**

`createStore`是`openinula`状态管理器中用于创建[Store对象](#核心概念--Store对象)的函数。一个`openinula`应用中可以创建多个`Store`对象，不同的`Store`需使用不同的id值。

**接口定义**

```tsx
function createStore<S, A, C>(config: StoreConfig): () => StoreObj<S, A, C>

type StoreConfig<S, A, C> = {
  id?: string;
  state?: S;
  actions?: A;
  computed?: C;
  options?: {
    isReduxAdapter?: boolean;
  };
}
```



**参数说明**

- `config`：一个包含`id`,`state`,`actions`,`computed`等部分的Store对象

**返回值**

`createStore`返回一个函数，调用该函数得到`StoreObj`对象，`StoreObj`对象的结构如下：

```tsx
type StoreObj<S, A, C> = {
  $s: S;
  $a: {[K in keyof A]: Action<A[K], S>};
  $c: {[K: string]: (state: S) => any}
  $queue: {[K in keyof A]: AsyncAction<A[K], S>};
  $listeners;
  $subscribe: (listener: (mutation) => void) => void;
  $unsubscribe: (listener: (mutation) => void) => void;
} & { [K in keyof S]: S[K] } & { [K in keyof A]: Action<A[K], S> } & { [K in keyof C]: ReturnType<C[K]> };
```



- 可通过`$s`访问Store对象中的`state`
- 可通过`$a`访问Store对象中的`actions`
- 可通过`$c`访问Store对象中的`computed`

### 核心概念--Store对象

**功能介绍**
`Store`是`inulaX`用于存储`state`、`action`、`computed`的集合，Store通过id来标识不同的对象。
一个Store对象主要由`id`,`state`,`actions`,`computed`这4个部分，下面是一个Store对象的整体示例：

**示例**

```tsx
const store = {
  // id 用于标识一个Store对象
  id: 'user',

  // state 用于存放数据
  state: {
    data: {
      list: [
        { id: 1, name: 'liming', age: 18 },
        { id: 2, name: 'xiaohong', age: 20 },
      ],
    },
    loading: false,
    selectUsers: [1],
  },

  // action 用于修改state中的数据
  actions: {
    addUser(state, payload) {
      state.data.list.push(payload);
    },

    addSelectedUser(state, payload) {
      state.selectUsers.push(payload);
    },

    setLoading(state) {
      state.loading = true;
    },
  },

  // computed 用于获取state状态变量的计算属性
  computed: {
    users: state => state.data.list,

    userCount(states) {
      // 通过this获取其他computed的值
      return this.users.length;
    },
  },
};
```



#### state

`state`是`Store`中存储数据的部分，通常把组件间需要共享的状态存放在state中。state属性是响应式的，当使用`actions`修改了`state`的数据，使用到这个数据的组件会更新，state具有精细的数据监听的粒度，例如：组件A使用了`name`属性，如果组件B使用了`age`属性，当`name`属性被修改时，组件A会重选渲染，组件B则不会重新渲染。

```tsx
const getStore = createStore(
  {
    id: 'value',
    state: {
      val: 0,
    },
    actions: {
      plusOne(state) {
        state.val += 1;
      },
      plusValue(state, value) {
        state.val += value;
      },
    },
  },
);
```



- 修改state

> state的修改只应该在`actions`中进行，不应该在`actions`之外修改`state`。

```tsx
const getStore = createStore(
  {
    id: 'value',
    state: {
      val: 0,
    },
    actions: {
      plusOne(state) {
        state.val += 1;
      },
      plusValue(state, value) {
        state.val += value;
      },
    },
  },
);

function App() {
  const store = getStore();

  return (
    <>
      <h1>{store.val}</h1>
      // 修改state值
      <button onClick={() => store.plusOne()}>+1</button>
    </>
  );
}
```



#### actions

`action`s是`state`中处理数据逻辑的部分，对`state`的修改都应该在`actions`中进行。

`action`分为同步`action`和异步`action`，如果`action`中使用了异步函数，则需要通过`async`将该`action`声明异步`action`。

- 同步action

```tsx
const getStore = createStore({
  id: 'user',
  state: {
    visible: true
  },
  actions: {
    update(state, payload) {
      // 更新user信息是同步方法，action为同步action
      const data = updateUsersync(payload);
      if (data.success) {
        // 可以通过this调用该store其他的action
        this.hideModal();
      }
    },
    hideModal(state) {
      state.visible = false;
    },
  },
});
```



- 异步action

```tsx
const getStore = createStore({
  id: 'user',
  state: {
    visible: true
  },
  actions: {
    async update(state, payload) {
      // 更新user信息是异步方法，action为异步action
      const data = await updateUser(payload);
      if (data.success) {
        // 可以通过this调用该store其他的action
        this.hideModal();
      }
    },
    hideModal(state) {
      state.visible = false;
    },
  },
});
```



#### computed

**功能介绍**
`computed`是一种用来获取`state`状态变量的计算属性，它可以根据依赖关系进行缓存，当它的相关依赖发生改变时才会重新计算。

**示例**

```tsx
const getStore = createStore({
    // id 用于标识一个Store对象
    id: 'user',
    // state 用于存放数据
    state: {
      data: {
        list: [
          { id: 1, name: 'liming', age: 18 },
          { id: 2, name: 'xiaohong', age: 20 },
        ],
      },
      loading: false,
    },

    computed: {
      users: state => state.data.list,

      userCount(states) {
        // 通过this获取其他computed的值
        return this.users.length;
      },
    },
  },
);

function User() {
  const st = getStore();

  return (
    <div>
      {'loading status is ' + st.loading} // 也可以使用st.$s.val
      {'user count is ' + st.userCount} // 也可以使用st.$c.userCount
    </div>
  );
}
```



#### $subscribe

**功能介绍**
使用`$subscribe`方法将监听器`listener`绑定到`Store`上以监听`state`的变化

**接口定义**

```tsx
store.$subscribe(listener: () => void)
```



**示例**

```tsx
import * as Inula from 'openinula';
const getStore = createStore({
    // id 用于标识一个Store对象
    id: 'user',
    // state 用于存放数据
    state: {
      data: {
        list: [
          { id: 1, name: 'liming', age: 18 },
          { id: 2, name: 'xiaohong', age: 20 },
        ],
      },
      loading: false,
    },

    computed: {
      users: state => state.data.list,

      userCount(states) {
        // 通过this获取其他computed的值
        return this.users.length;
      },
    },
  },
);

class User extends Inula.Component {
  constructor(props) {
    super(props);
    this.store = getStore();
  }

  listener = () => {
    console.log('store changed');
  };

  componentDidMount() {
    this.store.subscribe(this.listener);
  }

  componentWillUnmount() {
    this.store.$unsubscribe(this.listener);
  }

  render() {
    return (
      <div>
        {'loading status is ' + st.loading} // 也可以使用st.$s.val
        {'user count is ' + st.userCount} // 也可以使用st.$c.userCount
      </div>
    );
  }
}
```



#### $unsubscribe

**功能介绍**
移除`Store`对象上的监听器，如果`listener`绑定在某个组件上，那其应在`useEffect`钩子函数中完成，并在返回函数中解除与`listener`的绑定，防止`listener`不断增加。 另外`listener`必须被附加在相同的`Store`实例上。

**接口定义**

```tsx
store.$unsubscribe(listener: ()=>void)
```



**示例**

```tsx
import { useEffect } from 'openinula'
const getStore = createStore({
    // id 用于标识一个Store对象
    id: 'user',
    // state 用于存放数据
    state: {
      data: {
        list: [
          { id: 1, name: 'liming', age: 18 },
          { id: 2, name: 'xiaohong', age: 20 },
        ],
      },
      loading: false,
    },
  },
);

function User() {
  const store = getStore();

  useEffect(() => {
    // 将listener用变量存储起来，用于绑定到store上和从store上解除绑定
    const listener = () => {
      console.log('store changed');
    };
    // 对于每个User组件中获取到的st对象都应该重新绑定监听器
    store.$subscribe(listener);

    // 当User组件卸载时，解除Store上listener的绑定
    return () => {
      store.$unsubscribe(listener);
    };
  });

  return (
    <div>
      {'loading status is ' + st.loading} // 也可以使用st.$s.val
      {'user count is ' + st.userCount} // 也可以使用st.$c.userCount
    </div>
  );
}
```



#### $queue[action]

**功能介绍**
`store`对象的`action`也可以使用`$queue`命名空间来调用，这将把`action`添加到执行队列中。正常情况下`actions`是并行调用的，不需要等待先前的`action`。当`action`被添加到队列中时，它将等待执行队列前面的所有`action`先执行完成。

**示例**

```tsx
export const getStore = createStore({
  state: {
    val: 2,
  },
  actions: {
    wait: async () => {
      return new Promise(resolve => {
        setTimeout(resolve, 2000);
      });
    },
    increment: (state) => {
      state.val++;
    },
  },
});

function SlowComponent() {
  const st = getStore();

  const onClickEvent = () => {
    st.$queue.increment(); // 这个action将会立即执行
    st.$queue.wait(); // 这个action将会在两秒后执行
    st.increment(); // 这个action将会立即执行
    st.$queue.increment(); // 这个action将会在两秒后执行，在wait action执行完成后执行
  };

  return (
    <div>
      <button onClick={onClickEvent}></button>
    </div>
  );
}
```



## useStore()

**功能介绍**

`useStore`是openinula中的一个Hooks函数，用于在函数式组件中获取[Store对象](#核心概念--Store对象)

**接口定义**

```tsx
function useStore<S, A, C>(id: string): StoreObj<S, A, C>
```



**参数说明**

- `id`：要使用的Store对象的id值

## clearStore()

**功能介绍**

从openinula应用中移除对应的[Store对象](#核心概念--Store对象)

**接口定义**

```tsx
function clearStore(id: string): void
```

**参数说明**

- `id`：要删除的Store对象的id值