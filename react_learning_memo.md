## REACT学习笔记

之前学过一段时间REACT，后面又生疏了，这里从新开始。



#### Hello World

这里先从[PLAYGROUND](https://playcode.io/react)开始吧。

比如一个最简单的写法：

```jsx
function MyButton({btnText}) { // 这里声明了一个自定义按钮组件，并提供了一个外部设置按钮文字的属性
  return (
    <button>{btnText}</button> // 这里用大括号{}来包裹HTML内的JS表达式
  )
}

export function App(props) {
  const btnText = "I am a button"; // 声明组件属性，后续用{}进行组件传参
  return (
    <div className='App'>
      <h1>Hello React.</h1>
      <h2>Start editing to see some magic happen!</h2>
      <MyButton btnText={btnText} />
    </div>
  );
}
```

这种`function Cpn() { return (<div>html</div>) }`语法，在JS代码内直接混入HTML代码的风格，就是JSX。它这样设计背后的思想是，UI交互逻辑本来就不应该和表现层分开，因此混在一起进行组件化更好，关注点分离是逻辑分离而非技术栈分离。

上述代码内，**实际JSX的部分指的是括号内的，而非全部的代码**，一般是在`return`后面的部分，比如`(<h1>Hello</h1>)`，当然实际上在组件函数的任意位置也可以声明JSX，并赋值给变量，比如：

```jsx
export default function MyCpn() {
  const line1 = (<h1>this is line one</h1>); // 这里直接声明了JSX并赋值给变量
  const line2 = (<h2>this is line two</h2>);
  return (
    <div>
      {line1}
      {line2}
    </div>
  );
}
```



#### 本地开发环境搭建

之前的教程都有点落后了，这里给出一个2025在WINDOWS上的最新版本。

以下命令需要在POWERSHELL内执行：

```powershell
# Download and install fnm:
winget install Schniz.fnm

# Download and install Node.js:
fnm install 22 --fnm-dir E:\nodejs_fnm

# use specific node version
fnm use 22
```

注意可以在环境变量内设置FNM_DIR，来控制后续安装NODEJS的路径。

后续还要继续设置POWERSHELL，而且FNM目前还不兼容CMD，因此后续建议都用POWERSHELL运行fnm相关命令。

首先确认POWERSHELL的版本，执行：

```powershell
$PSVersionTable.PSVersion
```

主流是5或6。

然后确认一下PWOERSHELL是否有脚本文件，执行：

```powershell
nodepad $profile
```

如果弹出的记事本提示找不到路径，说明没有创建POWERSHELL配置脚本，需要手动创建，脚本的作用是每次打开POWERSHELL的时候可以自动执行，因此可以做一些初始化配置。要创建脚本，执行：

```powershell
if (!(Test-Path -Path $PROFILE)) { New-Item -ItemType File -Path $PROFILE -Force }
```

然后再执行`nodepad $profile`就可以看到这个文件被创建了，它的位置和POWERSHELL版本有关。

创建好配置文件后，重启POWERSHELL，并执行：

```powershell
Get-ExecutionPolicy
```

如果提示是限制，说明当前的POWERSHELL出于OS的安全策略，不能主动执行脚本，所以这样修改，**先以管理员身份启动POWERSHELL**，然后执行：

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted
```

有时候WINDOWS出于安全策略会再次提示是否更改，这里要确定。

然后找到POWERSHELL的脚本文件，加上下面内容：

```powershell
# set fnm command for powershell
fnm env --use-on-cd --shell powershell | Out-String | Invoke-Expression
```

然后再次重启POWERSHELL，执行：

```powershell
node -v
npm -v
```

如果有信息输出，说明脚本执行成功了，到此FNM和NODEJS就安装完成。

P.S. 上述操作真的展示了在WINDOWS下开发有多么不方便，不管是RUST还是NODEJS还是SOLIDITY都是LINUX更友好。

之后进入工作空间，执行以下命令：

```powershell
npx create-next-app@latest
```

之后按照提示进行配置，安装完成后启动`npm run dev`，浏览器3000端口就可以看到效果了。

后续开发测试就可以直接使用这个环境，建议初学的时候不要用TS。

插件部分不用刻意去安装JSX相关的，因为最近版本的VSCODE已经原生支持了，只是NEXTJS默认使用JS文件来编写JSX语法，如果需要对当前项目某个路径内的JS文件生效，可以打开VSCODE的当前项目（workspace）设定，加上下面的内容：

```json
"files.associations": {
    "**/src/app/**/*.js": "javascriptreact"
}
```

然后重启IDE就可以看到对应路径下的所有JS都默认是JSX了。

另外如果之前配置了POWERSHELL，也需要重启IDE以使得其生效。

然后把NEXTJS的文件路径改一下：

```
|---root
  |---src
    |---layout.js 这个可以视为总页面组件
    |---home 通过/home路径访问
       |---page.js 页面组件
    |---test 通过/test路径访问
       |---page.js 页面组件
```

目前以学习REACT为主，因此测试的部分可以直接在test/page.js内去写，或者也可以写多个DEMO页面，注意每新开一个页面都要通过创建一个文件夹+page.js的方式完成，这个是NEXTJS规定的，无法不创建文件夹就创建新页面。而且每个page.js内只能有一个默认导出`export default`，不能有任何命名导出，这样NEXTJS才知道怎么去渲染。

 另外测试页面先在头部统一加上以下代码，**以确保当前页面进入客户端渲染模式，以支持完整的REACT场景，比如组件添加事件等等**：

```js
'use client';

// export default function MyPage() {}
```



#### 安装TAILWINDCSS

上述部分完成后就可以开始写组件了，至于CSS的部分，有2个方法，一是传统的声明一个CSS文件，必须以`.module.css`结尾，然后在page.js内引入，并且在不同DOM上标记，这样做可以避免CSS冲突。另一个方法就是用tailwind.css，目前看好像算是标配了。

NEXTJS项目内安装TAILWINDCSS的方法，已经进入TAILWINDCSS V4版本了，POWERSHELL执行：

```powershell
npm install tailwindcss @tailwindcss/postcss postcss
```

项目根目录创建一个`postcss.config.mjs`文件，这个就是POSTCSS的配置文件，这样写：

```js
/** @type {import('tailwindcss').Config} */
const config = {
  plugins: {
    "@tailwindcss/postcss": {},
  },
};
export default config;
```

注意从V4开始不需要额外声明一个TAILWINDCSS配置文件，它有默认配置，如果需要调整后面再说，然后全局CSS文件global.css里面引入这个CSS：

```css
@import "tailwindcss";
```

最后确保全局的layout.js内引入了这个global.css：

```js
import "./globals.css";
```

然后就可以开始写了，比如：

```jsx
export default function Index() {
  return (
    <h1 className="text-3xl font-bold underline text-center">there is nothing here, try using /home or /test</h1>
  );
}
```



#### 声明组件

组件是REACT的核心概念，通过函数声明组件，首字母大写，所以一般的函数还是传统的camelCase写法，REACT组件的函数使用首字母大写的UpperCamelCase写法以区分：

```jsx
function MyCpn() {
  return (
    <h1>Hello React</h1>
  );
}

export default MyCpn; // 注意如果分模块写则组件要导出
```

如果组件的HTML是单行足够表示，比如`<h1>Hello React</h1>`，则可以省略括号。

但是所有组件都必须返回单一DOM节点，即不能写成：

```jsx
function MyCpn() {
  return (
    {/* 这样会报错 */}
    <h1>Hello React</h1>
    <h2>another node</h2> 
  );
}

export default MyCpn;
```

REACT这样设计纯粹是方便考量，它需要执行括号内的部分以构建虚拟DOM，如果每个JSX都只返回一个节点，则它不需要考虑JSX前后的代码是否仍然是JSX的范围的问题（因为JSX使用括号，这个符号在JS代码内太普遍了，会增加解析器的难度），而**如果返回多个节点，则解析器需要判断一个JSX结束的位置，在多行场景下这个判断会需要额外的工作量，因此降低编译和渲染效率**，因此REACT禁止开发者返回多个节点，如果需要返回多个节点，用一个容器节点，比如DIV，或者用一个虚拟DOM专用节点`<></>`来包裹，比如：

```jsx
function MyCpn() {
  return (
    {/* 这样是可以的 */}
    <>
      <h1>Hello React</h1>
      <h2>another node</h2>
    </>
  );
}

export default MyCpn;
```

处理代码内的不同部分的开始和结束问题一直是前端的一个难点，比如VUE使用`<template>`标签来控制开始和结束，在这点上设计思路和REACT是一样的。

使用组件就如同使用一个ESM模块一样简单，举例：

```jsx
export default function MyCpn() {
  return (
    <div>
      {/* 这里是作为组件而非HTML标签引入的，因此组件名称首字母大写，引入外部组件也是同理 */}
      <Profile />
      <ProfileDesc />
    </div>
  );
}

function Profile() {
  return (
    <img
      src="https://i.imgur.com/MK3eW3As.jpg"
      alt="Katherine Johnson"
    />
  );
}

function ProfileDesc() {
  return (
    <h4>a scientist</h4>
  )
}
```

如果是使用ESM引入，则变量名可以自定义，但是需要保证变量首字母必须大写，因为后续它要作为一个组件嵌入到JSX内，而**JSX通过标签首字母大写来判断它是一个原生的DOM节点还是一个组件节点**。



#### JSX基础语法和注释写法

- 使用`([HTML内容])`，括号包裹HTML表示一个JSX
- 括号内必须用一个单节点包括一切，单节点可以不是容器节点，比如`span`，也可以是虚拟节点比如`<></>`，当然也可以是真实容器节点比如`<div></div>`
- **如果HTML的部分是可以单行返回的单节点，则可以省略括号**
- 使用`{}`大括号在JSX内编写JS表达式，注意JSX内使用`{}`无法编写代码，因此**无法在JSX内编写逻辑控制语句，只能使用二元表达式**
- 单标签必须使用`/>`结尾，这个本身也是HTML规范
- **标签属性大部分情况下要转为驼峰，此外，一些标签属性必须用另外的名称代替，比如class要用className代替，onclick要用onClick代替**，因为JSX本质还是JS代码， 而JS内`class`是关键字用于声明类

注释写法：

```jsx
export default function MyCpn() {
  return (
    // 单行的可以直接用，但是需要独占一行，此行不能有HTML代码
    <div>{/* 使用大括号可以在任意位置编写一行或多行注释 */}
    {/* 
      这是一个多行注释
      换行没有问题哦
    */}
      <Profile />
      <ProfileDesc />
    </div>
  );
}
```

大括号表达式的一些例子，比如在标签属性内引入变量：

```jsx
export default function Avatar() {
  const avatar = 'https://i.imgur.com/7vQD0fPs.jpg';
  const description = 'Gregorio Y. Zara';
  return (
    <>
      <lable>{description}</lable>
      <img
        className="avatar"
        src={avatar}
        alt={description}
      />
    </>
  );
}
```

 大括号本质上支持的是JS表达式，因此任何可以形成表达式的语句都可以放到JSX大括号内，比如：

```jsx
<span>name: {person.name}; age: {person.age}</span>

<h1>current time is: {formatDate(Date.now()}</h1>

<h2>{isConditionTrue ? 'true' : 'false'}</h2>

<ul style={{backgroundColor: 'black', color: 'pink'}}></ul>
```

注意样式如果希望直接写，需要转为对象写法，即`style={styleObject}`，比如`style={{backgroundColor: 'black', color: 'pink'}}`，看上去是双大括号写法，而且原生CSS内的`foo-bar`写法需要转为`fooBar`。



#### 组件传参

REACT组件本质上是一个可执行的函数，它返回JSX以便REACT可以解析和渲染。这个函数只能在形式上接收一个入参，而且REACT会把这个入参转为对象，即使外部传入的是一个简单的数字或者字符串，举例：

```jsx
export default function MyCpn() {
  return (
    <div>
      <ProfileDesc desc="some empty string" />
    </div>
  );
}

function ProfileDesc(desc) { // 这里试图声明一个形参，但REACT会把它转为对象，因此实际使用的时候还是写为对象.属性
  return (
    <h4>{desc.desc}</h4>
  )
}
```

REACT之所以设计组件只能接收一个入参，是为了简化开发，多个入参会使得确认实参注入的顺序，以及维护这个顺序变得复杂，对象本身方便修改，也支持使用`...`语法进行展开，在某些场合下可以减少代码量。

所以通常的做法是对组件的形参进行对象展开：

```jsx
export default function MyCpn() {
  return (
    <div>
      <ProfileDesc desc="some empty string" />
    </div>
  );
}

function ProfileDesc({desc}) { // 这里表示const {desc} = props，即直接展开形参
  return (
    <h4>{desc}</h4>
  )
}
```

一个更加具有实战性的例子：

```jsx
export default function MyCpn() {
  const imageBoxProps = {
    imageSrc: 'https://i.imgur.com/MK3eW3As.jpg',
    alt: "empty string",
    onClickHandler: function () {
      alert('clicked');
    }
  };
  return (
    <div>
      <ImageBox {...imageBoxProps} />
    </div>
  );
}

function ImageBox({imageSrc, alt, onClickHandler}) {
  return (
    <div>
      <img src={imageSrc} alt={alt} onClick={onClickHandler} />
      <span>{alt}</span>
    </div>
  )
}
```

如果一个组件是这样注入实参的：

```jsx
<MyCpn foo={props.foo} bar={props.bar} barz={props.batz} />
```

则可以改为属性展开写法：

```jsx
<MyCpn {...props} />
```

组件还支持设置默认值，只针对外部未传入或者传入`undefined`时生效（外部传入`null`不会生效），比如：

```jsx
function Avatar({ person, size = 100 }) { // 这里外部使用时可以不传入size，默认会是100
  // ...
}
```

当然组件也支持透传，比如：

```jsx
function MyTable({rowCount, pageSize}) {
  <MyTableCore row={rowCount} pageSize={pageSize} >
}
```

注意REACT渲染机制使得**组件的所有入参都是实质上不可变的，即使直接修改入参也不会触发重新渲染**。



#### 组件嵌套

组件传参场景下，注意一个特殊属性，`children`，它是REACT规定的用于组件嵌套的特殊属性，换言之**如果组件要传参，应该禁止把`children`属性用于非嵌套场景的参数传递**，举例：

```jsx
export default function MyCpn() {
  return (
    <div>
      <ContentWithTitle>
        <h4 className="text-center font-light">a small sub title</h4>
      </ContentWithTitle>
    </div>
  );
}

function ContentWithTitle({children}) {
  return (
    <div>
      <h1 className="text-center font-extrabold underline">anything below is out of my control</h1>
      {children}
    </div>
  )
}
```

使用组件嵌套时，父组件必须在解构入参时显式声明`children`属性，然后把它放在组件内的位置作为插槽。**使用时，需要把组件拆为双标签写法，在内部注入HTML或者JSX**。

当然children本质上也是组件的一个属性，因此它也支持传入默认值，具体可以自行尝试。



下面的部分慢慢改……



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



