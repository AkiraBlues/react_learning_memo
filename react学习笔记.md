暂时记录React的学习笔记，待日后整理。

所有东西都是入门级的资料。

##### 大前端现状

现在大前端是这样的：

- web，有VUE和REACT和ANGULAR，还有其他大大小小的前端框架，FLUTTER也有WEB版本但是还不流行
- 安卓和IOS，REACT-NATIVE和FLUTTER，就这两个
- 桌面应用，ELECTRON是平台，底层可以支持用JS前端框架开发，基于这个原理有REACT-NATIVE和FLUTTER的对应PC开发环境

所以只学VUE，范围就很窄，只能用于WEB，安卓，IOS，PC都要有学习成本，相对来说REACT和FLUTTER都是比较好的选择，但REACT还有WEB版本所以综合来说REACT很流行，加上字节全面站队REACT也说明这个东西应该要学。

但是2019年之后FLUTTER也开始追赶，到2022年开发占有率已经超过了REACT NATIVE(RN)，而且有人说它性能更好，所以等于FLUTTER也要学习。

总之大公司竞争，工具会持续更新，所以都要学。

##### 渐进式入门

React（以下简称R）

安装，通过`create-react-app`，注意要结合TS来学习，因此搭建项目的时候要选TS模板。早期是用全局安装的方式搭建的，现在改成npx xxxx了，这样搭建的时候下载构建，搭建完成后会自动删除，不占用全局空间。

模板约定，以下文件必须存在（或许后面可以改）：

- public/index.html
- src/index.js或src/index.tsx

react-scripts，这个就是react的CLI，类似WEBPACK，VITE等的作用，底层是WEBPACK支持，**在START和BUILD命令下都是引入WEBPACK，所以可以说是基于WEBPACK的封装**。

和VUE一样R也有组件的概念，因此也会面临和VUE一样的问题，在浏览器中观察组件状态，因此也需要开发者工具，谷歌插件已支持，叫React Developer Tools。安装好之后开一个DEV SERVER，然后控制台可以看到原子电子的那个标志就是，它还分了组件和PROFILER。

严格模式下R会渲染两次以便发现潜在问题，默认脚手架搭建之后就是严格模式，这个渲染两次叫做double render，

##### R的组件

组件是R的核心概念，应该和VUE差不多，即封装了UI和业务逻辑的功能模块，组件都是函数，在R这里组件就是返回了一个HTML和业务逻辑的函数，有点像用JS的FUNCTION做NEW OBJECT的意思，因此函数名是构造函数，首字母大写：

```jsx
function MyCpn() {
  return (
    <h1>Hello React</h1>
  );
}

export default MyCpn;
```

组件要导出，参考VUE的SFC，核心是单一职责和关注点分离，因此一个组件对应一个文件，不需要命名导出更方便一点。

然后在入口的App.tsx内引入，和VUE一样，记得**REACT的组件都是首字母大写**：

```jsx
import logo from './logo.svg';
import './App.css';

// 这里可以随意命名，但是后面引用组件的时候要用你在这里写的名字，首字母一定要大写
import MyCpn2 from './MyCpn';

function App() {
  return (
    <div className="App">
      <header className="App-header">
        <img src={logo} className="App-logo" alt="logo" />
        <MyCpn2 />
      </header>
    </div>
  );
}

export default App;
```

这个理念很有意思，**R的HTML模板直接写在代码内，比起VUE封装了一下TEMPLATE更返璞归真**（VUE的TEMPLATE也是通过元编程转为RENDER函数，然后执行RENDER函数来更新组件的），所以TEMPLATE内的东西本质也是JS代码。

##### JSX语法基础

构造函数返回一个括号，括号内是HTML模板，HTML模板只能有一个根标签，根标签可以是具体的div或者section之类的，也可以是一个空标签，即`<></>`这种，这种处理是因为JSX语法的缘故，即**HTML代码要有一个明确的开始和结束的标志**，以便于解析器的处理，如果根标签是多标签那么这种开始和结束就不确定了，因为解析器不确定下一行代码是标签还是代码，增加解析器的判断工作量，所以为了便于它工作，相当于是约定好根标签就一个，这点VUE的SFC的template标签也是一样。

另外，**所有单标签都要用`<singletag />`这种形式结尾**，以便R进行解析，双标签一定要收尾这个是确定的。

**标签属性大部分情况下要转为驼峰**，比如class要用className代替，onclick要用onClick代替。

单括号引用外部变量，单括号本身就是escape的意思，即内部的代码视为JS或TS代码，或者对应的表达式，不转义为HTML。

组件引用使用标签名，大体上类似VUE。

CSS的处理，没有官方规范，由于官方是用WEBPACK处理的因此官方可能会推荐用CSS模块的方式，但这样也就限制死了构建工具，实际上代码应该和构建工具脱钩。

CSS的引入是REACT里面的一个难题，不像VUE直接在SFC内有完美方案，后续慢慢研究，CSS模块和**styles-components**是目前的两个最佳实践。

学习的时候暂时用最简单的CSS来写，然后用TAILWIND.CSS或styles-components。

数据处理，DATA本身就是单纯的对象，用单括号写法引入HTML片段。

条件渲染，R不支持VUE的指令，也不支持IF语句，原因是JSX本质上会被转化为ReactDOM.render()的参数：

```jsx
// JSX语法
const id = "xyz";
<div id={id}>Hello World!</div>

// 转换后写法
React.createElement("div", {id: "xyz"}, "Hello World!")
```

注意到JSX实际上做的失去就是把HTML转为虚拟DOM，至于**大括号内的则是处理为expression，即非代码块，而是JS表达式**。注意IF写法即使不加代码快它也不是表达式，比如：

```js
if (condition) console.log(true);
```

**这个东西不是表达式，这个是JS引擎规定的**，它属于JS语法结构中的**语句**，虽然它可以写成一行。

所以如果我们把id换成IF语句就是这样：

```jsx
// JSX语法
<div id={if (true) return 'xyz'}>Hello World!</div>

// 转换后写法
React.createElement("div", {id: if(true) return 'xyz'}, "Hello World!")
```

这种代码在哪里都是不能执行的，语法上就错了，如果这样可以那么常规的：

```
const name = if (true) return 'arc';
```

实际上这样写就是错的！所以**JSX的大括号内只能写表达式，不能写语句**。

因此R的条件渲染很原始：

```jsx
function MyCpn() {
  return (
    <h1>{Math.random() > 0.5 ? 'Hello World' : 'Hello React'}</h1>
  );
}

export default MyCpn;
```

更优雅的方式，只能做到这样：

```jsx
function MyCpn() {
  return (
    <>
    	{condition ? (<template1 />) : (<template2 />)}
    </>
  );
}

export default MyCpn;
```

条件渲染用map：

```jsx
function MyCpn() {
  return (
    <>
    {list.map(ele => <template>{ele}</template>}
    </>
  );
}

export default MyCpn;
```

如果必须要用语句来控制变量，只能这样：

```jsx
let var;

// 复杂的语句把var值确定好

<div>{var}</div>
```

事件处理，R做了封装，比如原生是onclick，全小写，在JSX里面是`onClick`，驼峰写法，做了封装。

JSX语法会被编译为`React.createElement`，结果是虚拟DOM。

##### JSX语法设计思想

HTML，CSS和JS，前端三剑客，之前基于关注点分离原则，是分别处理的，写在不同的文件内。但是随着JS功能越来越强大，网页的交互越来越多，JS的主导地位也越来越大。所以基于JS的主导地位，R提出了JSX语法，就是**说在JSX中，HTML沦为了JS的渲染语法糖之类的东西**，目的是确保JS的主导地位，而且确保当JS逻辑变动时，HTML也会被牵连到（R认为JS和HTML就应该保持耦合，因为JS对HTML的操作过于频繁，耦合才是对的，这里再思考一下）。

**R的设计核心理念是可预测**。一个组件就是一个构造函数，不需要考虑VUE的响应式副作用问题，不需要区分一个变量应该是响应式的还是非响应式的还是计算属性的，组件的所有逻辑都是线性的，这样分析一下：

```tsx
// react组件思路
import type { Dispatch } from "react";

interface MyCpnProps {
  count: number;
  setCount: Dispatch<number>;
}

function MyCpn({count, setCount}: MyCpnProps) {

  const countStr = `prefix-${count}-suffix`;

  return (
    <>
      <h1>Hello React {countStr}</h1>
      <button onClick={() => setCount(++count)}>点我更新</button>
    </>
  );
}

export default MyCpn;
```

计算属性用普通变量声明就可以，然后调用setter，不再需要考虑变量本身是否要用计算属性表示。

而VUE在编写过程中需要考虑响应式的问题：

```vue
<script setup>
const val = ref(0);

const valStr = computed(() => `prefix-${val.value}-suffix`);

val.value++;
</script>

<template>
	<h6>{{ valStr }}</h6>
</template>
```

**在VUE中，声明变量，声明计算属性，然后修改变量，由于变量是响应式的，还要回头去思考计算属性能否执行，最后才是渲染**。思路上不能按照自上而下的顺序阅读代码，要经常考虑副作用，从而使得**一旦忽略这个副作用，当代码规模增长后，预测某处修改是否会引发副作用将变得非常困难**。

**所以R最大的优势是可预测，组件的代码逻辑是线性的**。

**基于这个线性逻辑，JS应该主导一切**，所以在JSX中，HTML模板是JS的附属品，甚至你可以把HTML模板理解为静态模板，虽然它实际上是虚拟DOM。**模板是为JS服务的，所以不应该为模板单独创建一套语法，**不管是模仿HANDLEBARS还是FREEMARKER，模板要尽可能简单，贴近原生。

JSX和关注点分离的讨论，有没有可能，**分离视图和业务逻辑，本身就是一种过时的MVC思想了？**

另外，JSX语法的本质就是用HTML范式的标记语言去表示视图，而实际上这个视图可以基于硬件平台做变换，比如安卓组件或者IOS组件等等，我们要记住的就是HTML范式本身，然后对应平台的具体标签就可以了。

比如REACT NATIVE，REACT CANVAS等都是这个理念的产物。

##### useState研究

整体思路是这样的：

- useState(initalVal)会返回一个数组，第一个元素是getCurrentVal（可以理解为getter），但是不可直接改变，只能直接使用，第二个元素是setVal，即setter
- 通过setter修改val，然后把val传入到函数构造器的template内即可

```tsx
import { useState } from "react";

function MyCpn() {
  const [count, setCount] = useState<number>(0);
  setTimeout(() => {
    setCount(count + 1);
  }, 1000);
  
  return (
    <h1>Hello React {count}</h1>
  );
}

export default MyCpn;
```

注意到页面会不断更新，即好像反复调用了这个方法（所以不需要用setInterval就能实现无限循环），后面再研究。

无限循环的BUG，**简单来说就是不要在构造函数内不经过事件就调用useState的setState**。调用setState会导致R创建当前组件的副本以生成虚拟DOM进行DOM比较，但是当前组件被重复调用时就会触发再次调用setState，所以循环就来了：

```
构造阶段setState => 重新构造组件 => 重新执行setState => 重新构造组件 => 重新执行setState => 无限循环
```

所以setState要用于事件就是这个原因。

TSX下的setter类型声明：

```tsx
import type { Dispatch } from 'react';
```

##### 组件代码规范

我自己觉得这种写法很好：

```tsx
function MyCpn(props) {
  // internal state
  // internal function
  return (<>template</>);
}

export default MyCpn;
```

就是说充分利用JS的语言特性，在一个大的组件构造函数内声明函数，如果需要可以外部引入通用函数，然后最后输出这个函数作为组件，函数返回值就是组件模板。

**组件使用的时候统一首字母大写，这是为了和HTML标签做区隔**。

当然也可以多个组件写在一起，此时组件都很小，写在一起比较好维护，然后导出就要用命名导出。

##### 组件和纯函数

R的组件是构造器，意味着状态是所有组件共享的，**因此不应该在组件内引入非PROPS的状态并在组件内使用**，这会导致组件的作用域链指向外部变量，并且引发副作用，除非你确定且需要所有组件共享这个外部变量，你知道自己在干什么。

比如这种：

```tsx
let privateCounter = 0;
function TestCpn() {
  privateCounter++;

  return (
    <>
      <h6>我的私有计数器{privateCounter}</h6>
    </>
  );
}

export default TestCpn;
```

这种组件在构造的时候依赖了非PROPS外部私有变量，当组件被复用的时候，实际上这个私有变量是被所有组件共享的，因为ESM模块化是直接引用的，所以是闭包，函数被引用了，它内部的作用域链也就包括此私有变量，所以这个私有变量的值被缓存了，并随着每个组件的初始化而变动。而且当setState被调用的时候这些组件即使不需要重绘也还是会被调用构造函数，因此这个值会不停变动，引发越来越多的副作用。

所以在R里面倾向于写纯函数组件，即所有变量只从形参和内部声明中实现，重复执行会具有稳定的结果：

```tsx
function TestCpn() {
  let privateCounter = 0; // 这样就稳定了，每次调用构造函数都会重新初始化
  privateCounter++;

  return (
    <>
      <h6>我的私有计数器{privateCounter}</h6>
    </>
  );
}

export default TestCpn;
```

##### 组件生命周期，交互逻辑

R的组件也是自洽的，因为它本质是构造器，使用标签引入就相当于解析出一次NEW对象的操作。

**R的生命周期回调函数都以use开头，即useXXX写法**。

组件也支持props，非常简单，就是构造器传参，思路是这样的：

- 整体风格建议使用函数式编程（有待商榷）
- getter和setter由父组件提供
- 子组件通过构造器来获取getter和setter，这后面细说
- 子组件通过getter就可以渲染了，调用setter可以触发变动，但是由于这个getter是从父组件来的，因此它的变动会影响到其他组件

一个简单的例子：

```tsx
// 子组件
interface MyCpnProps {
  count: number;
  handler: (count: number) => void;
  interval: number;
}

function MyCpn({count, handler, interval}: MyCpnProps) {
  if (interval !== -1) {
    setTimeout(() => {
      handler(count + 1);
    }, interval);
  }
  
  return (
    <h1>Hello React {count}</h1>
  );
}

export default MyCpn;

// 父组件
function App() {
  const [count, setCount] = useState(0);

  const handler = (val: number) => {
    setCount(val);
  };

  return (
    <div className="App">
      <header className="App-header">
        <img src={logo} className="App-logo" alt="logo" />
        <MyCpn count={count} interval={1000} handler={handler} />
        <MyCpn count={count} interval={-1} handler={handler} />
      </header>
    </div>
  );
}

export default App;
```

当然还有更简单的版本，就是不让子组件修改，只传入：

```tsx
function App() {
  const [count, setCount] = useState(0);
  
  setTimeout(() => {
    setCount(count + 1);
  }, 1000);

  return (
    <div className="App">
      <header className="App-header">
        <img src={logo} className="App-logo" alt="logo" />
        <MyCpn count={count} />
        <MyCpn count={count} />
      </header>
    </div>
  );
}
```

简单谈一下R和VUE的PROPS设计区别：

- 两者都是通过标签属性的方式从父传入具体值
- VUE通过选项式API或者defineProps来声明
- R通过构造器传参来声明
- 两者都不希望子组件可以修改，但是都确保了一定程度的放权
- VUE的放权更少，因为VUE不存在setter，所有行为都隐藏在PROXY背后，而从设计上就杜绝了子组件直接修改PROPS的可能性，所以基本上HANDLER是父组件处理的，如果子组件有这方面需求，一般都是冒泡事件，让父组件处理，所以VUE是一个中央集权的系统，有点像SVN。
- **R从设计上就倾向于放权**，可以直接把setter给子组件，子组件可以自己基于这个setter再封装，useState返回的第二个值就是setter，所以R的意思就是setter全部由父组件分发，子组件可以自行获取并调用，类似分布式的系统。

##### 组件嵌套

在常规HTML标签内引入组件这个好理解，但是R组件也有类似VUE插槽的机制，即允许组件内开放接口来嵌套其他组件。

组件声明不要嵌套，这个很明显，组件的管理应该是水平的，使用的时候嵌套可以。

##### 前端路由

有React Router。

##### 状态管理

R本身就把组件的数据分为状态和PROPS，前者是自己维护的，后者是外部传入的。

##### 其他常见问题和支持

格式化校验，用TS和ESLINT，和VUE一样。

国际化，有对应的il8n库，也可以自己简单实现。

安卓和IOS开发，用REACT NATIVE，语法和REACT类似，等于方便前端的直接迁移做安卓开发，在安卓里面可能原生的KOTLIN和FLUTTER也有使用场景，都是科技巨头的阵地之争，脸书 VS 谷歌。

##### 封装UI组件

这个和VUE的生态圈一样，R也有自己的UI组件生态圈：

- MATERIAL-UI
- MANTINE
- CHAKRA-UI

- ANT DESIGN



