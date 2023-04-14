# pinia
## 入口引入 createPinia

createPinia 不接受任何参数，它会返回一个 pinia 实例。

```javascript
import { createApp } from "vue";
import { createPinia } from "pinia";
import App from "./App.vue";

const pinia = createPinia();
const app = createApp(App);

app.use(pinia).mount("#app");
```

- 实现一个 createPinia 需要什么功能
- 返回一个 pinia 实例子
  - \_p 一个数组存放所有的插件
  - 首先需要 一个 install 方法
  - \_s 能够管理所有的 store => 用一个 map 来存放所有的 store
  - 能让所有组件都能获取 pinia 实例
    - app.provide(piniaSymbol, pinia); // 所有组件都可以通过 app.inject(piniaSymbol)
    - app.config.globalProperties.$pinia = pinia; // 让vue2的组件实例也可以共享，// this.$pinia

```javascript
// 存的是createPinia这个ap
import { ref, effectScope } from "vue";
import { piniaSymbol } from "./rootStore";
export let activePinia; // 全局变量
export const setActivePinia = (pinia) => (activePinia = pinia);
export function createPinia() {
  const scope = effectScope();
  const state = scope.run(() => ref({})); // 用来存储每个store的state的
  // scope.stop() 可以通过一个方法全部停止响应式

  // 状态里面 可能会存放 计算属性， computed
  const _p = [];
  const pinia = {
    use(plugin) {
      _p.push(plugin);
      return this;
    },
    _p,
    _s: new Map(), // 这里用这个map来存放所有的store   {counter1-> store,counter2-> store}
    _e: scope,
    install(app) {
      setActivePinia(pinia);
      // 对于pinia而言，我们希望让它去管理所有的store
      // pinia 要去收集所有store的信息 , 过一会想卸载store
      // 如何让所有的store 都能获取这个pinia 对象
      app.provide(piniaSymbol, pinia); // 所有组件都可以通过 app.inject(piniaSymbol)
      // this.$pinia
      app.config.globalProperties.$pinia = pinia; // 让vue2的组件实例也可以共享
    },
    state,
  };
  return pinia;
}
```

## 两种定义 store 方式

### options API

```javascript
import { defineStore } from "pinia";
export const useCounterStore1 = defineStore("counter1", {
  state: () => {
    return {
      count: 0,
    };
  },
  getters: {
    double() {
      return this.count * 2;
    },
  },
  actions: {
    increment(payload) {
      this.count += payload;
    },
  },
});
//or
const useUserStore = defineStore({
  id: "counter",
  state: () => ({
    count: 0,
  }),
  getters: {
    double() {
      return this.count * 2;
    },
  },
  actions: {
    increment(payload) {
      this.count += payload;
    },
  },
});
```

### composition API

```javascript
import { defineStore } from "pinia";
import { ref, computed } from "vue";
export const useCounterStore2 = defineStore("counter2", () => {
  const count = ref(0);
  const double = computed(() => {
    return count.value * 2;
  });
  const increment = () => {
    count.value *= 2;
  };
  return { count, increment, double };
});
```

## defineStore

- definStore 需要什么样的能力
  - 接受两个参数(idOrOptions，steup)
    - 如果第一个参数类型是字符串，那么他就是 stroeId，如果第一个参数类型不是字符串，那么从整个 idOrOptions 去取 id
    - 如果 setup 是一个函数，说明是 compition API，否则肯定是 options API
    - const isSetupStore = typeof setup === 'function'

```javascript
export function defineStore(idOrOptions, setup) {
  let id;
  let options;

  if (typeof idOrOptions === "string") {
    id = idOrOptions;
    options = setup;
  } else {
    options = idOrOptions;
    id = idOrOptions.id;
  }
  // 可能setup是一个函数，这个稍后处理

  const isSetupStore = typeof setup === "function";

  return useStore; // 用户最终拿到是这个store
}
```

### useStore

- 定义一个 useStore() 并返回，用户最终拿到是这个 store
  - 先获取全局的 pinia 对象 let pinia = inject(**_piniaSymbol_**)
  - 判断当前 id 在不在 pinia.\_s 中，
    - 如果存在，说明被注册过，直接返回 const store = pinia.\_s.get(id) return store
    - 如果不存在，没存在过，创建 store 并注册在 pinia.\_s 中
    - 根据 isSetupStore 执行 createSetupStore(id, setup, pinia) 或者 createOptionsStore(id, options, pinia)

```javascript
function useStore() {
  // 在这里我们拿到的store 应该是同一个
  let instance = getCurrentInstance();
  let pinia = instance && inject(piniaSymbol);

  if (pinia) {
    setActivePinia(pinia);
  }
  pinia = activePinia; // 将全局变量给你 这个一定存在
  if (!pinia._s.has(id)) {
    // 第一次useStore
    if (isSetupStore) {
      createSetupStore(id, setup, pinia);
    } else {
      // 如果是第一次 则创建映射关系
      createOptionsStore(id, options, pinia);
    }
  }
  // 后续通过id 获取对应的store返回
  const store = pinia._s.get(id);
  return store;
}
```

### createSetupStore

#### 接收四个参数

- id 定义 strore 的 id
- setup 一个可以返回 state 的函数
- pinia Pinia 实例
- isOption

#### 创建一个 partialStore

store 是用 reactive 包装的一个响应式对象，reactive 所包装的对象是由 partialStore 通过 Object.assign 进行复制的。partialStore 中定义了很多方法，这些方法都是暴露给用户操作 store 的一些接口，如$onAction可设置actions的回调、$patch 可更新 store 中的 state、$dispose 可销毁 store。

- $patch 更新 store 中的 state
- $subscribe
- $onAction
- $dispose

```javascript
const partialStore = {
  $patch,
  $subscribe(callback, options = {}) {
    // 每次状态变化都会触发此函数
    scope.run(() =>
      watch(
        pinia.state.value[id],
        (state) => {
          callback({ storeId: id }, state);
        },
        options
      )
    );
  },
  $onAction: addSubscription.bind(null, actionSubscriptions),
  $dispose() {
    scope.stop(); // 清除响应式
    actionSubscriptions = []; // 取消订阅
    pinia._s.delete(id);
  },
};

const store = reactive(partialStore); // store就是一个响应式对象而已
```

#### 初始化

```javascript
const initialState = pinia.state.value[id]; // 对于setup api 没有初始化过状态

if (!initialState && !isOption) {
  // setup API
  pinia.state.value[id] = {};
}

// 父亲可以停止所有 , setupStore 是用户传递的属性和方法
const setupStore = pinia._e.run(() => {
  scope = effectScope(); // 自己可以停止自己
  return scope.run(() => setup());
});
```

<br>
  
> 语雀地址 https://www.yuque.com/yuqueyonghuyv23kd/xebk9e/xig7kt