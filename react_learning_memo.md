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
- **JSX支持用`null`表示，即不渲染任何节点**
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

注意REACT渲染机制使得**组件的所有入参都是实质上不可变的，即使直接修改入参也不会触发重新渲染**。只有依赖REACT的HOOK机制，改变外部传入组件的实参后要求重新渲染，才会使得组件出现变化。

进一步拓展，比如下面的代码：

```jsx
export default function Button() {
  let bgClz = "";
  function handleClick() {
    bgClz = "bg-red-100";
  }
  return (
    <button className={`border-2 ${bgClz}`} onClick={handleClick}>Click me</button>
  );
}
```

点击按钮，尝试修改其背景色，会导致变化吗？**结论是不会**，所以不仅是修改入参不会触发重新渲染，**通过事件直接修改组件内涉及渲染的临时变量，也不会触发重新渲染**，后续介绍的REACT的HOOK机制会解释这个现象。



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

这里还有一个问题，children本质上是父组件在编写时对嵌套部分的不确定的抽象，因此**父组件也无法直接把自身属性或者数据提供给通过children占位的子组件**，这个问题，会在后面的组件间数据共享部分解决。



#### 条件渲染和表达式JSX互相嵌套

组件支持基于不同条件渲染出不同的结果，比如最简单的：

```jsx
function MyCpn({condition}) {
  if (condition) {
    return <h1>condition true</h1>
  } else {
    return <h2>condition false</h2>  
  }
}
```

上述代码是全局的通过if else进行控制，但是更多时候需要在一个大组件内部的不同部分进行条件渲染，最简单的，比如展示一个人的履历，如果它有头像则展示头像，否则展示默认图片，如果它有学历或者简介则展示，否则不展示，则不能使用if else了，**只能使用{}结合二元表达式，并且把结果继续表述为JSX**，比如：

```jsx
export default function MyCpn() {
  const props = {
    avatar: 'https://i.imgur.com/MK3eW3As.jpg',
    name: "some random guy",
  };
  return (
    <Profile {...props} />
  );
}

function Profile({avatar, name}) {
  const validString = (str) => {
    return typeof str === 'string' && str.length > 0;
  };

  return (
    <>
      {validString(avatar) ? (<Avatar imgSrc={avatar} />) : null}
      {validString(name) ? (<NameTag name={name} />) : null}
    </>
  )
}

function Avatar({imgSrc}) {
  return (
    <div className="flex justify-center"><img src={imgSrc} /></div>
  )
}

function NameTag({name}) {
  return (
    <h4 className="text-center font-bold">{name}</h4>
  )
}
```

注意二元表达式本身需要写在`{}`内，而它的结果往往是组件或者JSX，因此建议用`()`包裹JSX，所以一个完整的二元表达式写法是：

```jsx
{condition ? (<TrueJSX />) : (<FalseJSX />)}
```

当然JSX本身也支持在内部继续声明`{}`，所以很多时候会见到`{}`JS表达式包裹JSX，然后JSX内部继续声明`{}`JSX表达式的场景，有点相互嵌套的意思。

**如果false的场景不需要渲染时，当然可以把二元表达式简化为逻辑短路**，就可以直接：

```jsx
{validString(avatar) && (<Avatar imgSrc={avatar} />)}
```

额外阅读，为什么`{}`表达式内不允许使用if else语法：

```
任何{}本质上会视为表达式，并把运算结果传入ReactDOM.createElement()内，比如：

// 这是一个组件
function Sample() {
  const id = "xyz";
  return <div id={id}>Hello World!</div>
}

// 经过REACT转换时，会把{}内的内容进行运算并传参
React.createElement("div", {id: "xyz"}, "Hello World!")

而熟悉JS语法的都知道，if else是语句，statement，不是表达式，即使不加代码快它也不是表达式，比如：
if (condition) console.log(true);
所以记住，{}内只能编写JS表达式，所以也只能用二元表达式或者逻辑短路来进行条件渲染。
```



#### 列表渲染

注意REACT内只能使用`{}`JS表达式来创建JSX，因此拿到一个集合后，无法使用for循环，或者forEach等方式进行遍历，因为它们本质都是语句，可以操作集合，但是不能输出结果。

唯一的途径就是用map：

```jsx
function MyCpn() {
  return (
    <>
      {list.map(ele => (<ChildCpn ...ele />)}
    </>
  );
}

export default MyCpn;
```

当然对数组进行中间阶段的处理，比如filter，或者reduce也是可以的，只是如果要把处理后的集合渲染出来，只能用map。

列表渲染常见问题是操作列表，比如一个TODOLIST，可能需要添加或者删除，此时可以用`key`属性给每个元素的根节点（或者子组件）进行标记，KEY必须是唯一的，以帮助REACT减少重复渲染的次数，复用已经渲染出的DOM。如果需要使用虚拟根节点`<></>`添加KEY，则需要改为`<Fragment key={item.id}></Fragment>`，**因为`<></>`不支持添加属性，它就是`<Fragment></Fragment>`的语法糖**。



#### 副作用和严格模式

一般来说组件都应该设计为纯函数，即非常简单的：

```
const renderedDOM = MyCpn(prop1, prop2, prop3);
```

只要入参给定，执行组件函数代码后，渲染出的DOM就是确定的，而且执行多次函数也是一样的结果，不会修改系统的其他状态。

即使通过事件，手动修改参与渲染DOM的变量，比如修改入参，或者修改内部的临时变量，也不会导致DOM的变化，这是因为**执行组件函数代码后，DOM就已经确定了，后续只要不触发重新渲染，所有变动都只会发生并停留在组件函数的内部，不会触及REACT并引发任何可见的变动效果**。

一个纯函数组件有以下好处：

- 可以在客户端和服务端同时运行，因为不依赖入参之外的因素，所以可适配的环境会更多
- 如果对入参进行了缓存，那么重新渲染时，如果检测到这个组件的入参没有变化，这部分组件就可以跳过销毁 => 重新渲染的过程
- 如果重新渲染时检测到入参的变动，那么可以直接放弃后续的渲染，从头开始，不用考虑组件销毁会带来额外问题

但是现实开发中是不可能的，一些组件需要承担系统初始化的任务，这就意味着它必须要修改系统的状态，比如请求后端数据，然后把数据交给其他组件来渲染。还有一些组件需要持续地和系统进行沟通，基于用户操作或者自动持续地修改系统状态。**REACT把组件会修改系统状态的行为视为组件的副作用**。由于REACT本质是一个前端UI库，因此**任何修改系统，但是不会最终导致UI变化的行为，本质上来说并不能算是副作用**。比如和外部系统的同步，如果这个同步是必要的，会带来UI的变化（比如获取新的数据），它就是一个副作用，而如果它只是把用户的行为上报给外部系统，用于产品和运营决策，则虽然从更广义上看产品和运营决策可能会导致UI的重新设计，但是在短期看这个行为并不能算一个副作用。

更恶心的例子，比如将来如果浏览器支持某种边缘计算，则用户使用前端UI时，它不仅会窃取用户数据，还会用浏览器的一部分算力去支持其他业务的计算，那么这样的功能本质上也不是副作用。

这里谈到的副作用，就是**以UI为核心的，会最终导致UI变化的，和组件之外的系统进行交互的行为**。

副作用通常来说需要开发者进行控制，因为如果不控制，比如多次初始化，多次请求数据，多次自动同步，都会对系统造成预测之外的影响，或者产生BUG。所以开发的时候一般会启用严格模式，它的作用就是每个组件会渲染两次（中间会执行一次销毁）以确保有副作用的组件会多次执行，如果这里的副作用没有被开发者控制好，执行2次一般就会产生BUG。

关于副作用，通常有几种表现方式：

- 从其他系统获取数据
- 直接操作DOM（有些需求，直接操作DOM会比使用REACT框架更有效）
- 自动化行为，比如定时，或者轮询
- 绑定事件，比如添加用户操作事件，或者接收端口消息后触发事件
- 访问WEB存储，比如cookie，session，storage等等

后续介绍REACT的HOOKS的时候会提到怎么在这些场景控制副作用。

一般开发时，为了确保组件的行为符合预期，REACT设计了一个严格模式来帮助开发者定位组件内存在的问题，它的设计思路很简单，组件渲染两次，具体来说是**每次状态变化时，REACT都会执行渲染 => 销毁 => 再次渲染的流程**，如果过程中组件在销毁时没有处理好副作用，则再次渲染通常会触发BUG，比如重复的副作用，或者副作用不触发等等。

在NEXTJS里面，通过在配置文件内添加`reactStrictMode: true`开启严格模式。



#### 事件交互入门

原生HTML通常使用`dom.addEventListener`或者内联事件`<button onclick="clickHandler()">a button</button>`来绑定事件，MDN文档推荐用`addEventListener`的方式绑定事件，但是无论是REACT还是VUE都认为事件应该直接在DOM上可见，以方便阅读，所以JSX虽然也支持通过副作用的方式绑定事件，但是最佳实践也是使用内联事件：

```jsx
export default function Button() {
  function handleClick() {
    alert('You clicked me!');
  }
  
  return (
    <button className="border-2 bg-amber-50"  onClick={handleClick}>Click me</button>
  );
}
```

注意JSX内，内联事件的写法有区别，比如原生写法是`onclick`，在JSX内要写为`onClick`，驼峰命名，此外原生内联事件是`onclick="run_javascriptcode();"`，属性值是直接的代码执行语句，而**JSX内是传入事件变量，不需要执行**，比如`onClick={clickHandler}`。

这里注意到上述写法看上去有些问题，因为绑定事件会带来副作用，而上述代码无论我们执行多少次都不会触发副作用，因为REACT的事件绑定机制并不是看上去那样简单。

看上去REACT会把`onClick`转为`onclick`，但实际上不是，它创建出DOM后，会**先把所有能冒泡的事件绑定归结到根节点，然后通过事件委托的方式逐个绑定到具体的DOM上**，伪代码如下：

```js
// 假设某个DOM加了onClick事件

rootNode.addEventListener('click', (event) => {
  let target = event.target;

  // 从最底层的目标开始触发它绑定的事件，然后逐层冒泡
  while (target) {
    const handlers = eventRegistry.get(target);
    if (handlers && handlers['click']) {
      handlers['click'](event);
      break;
    }
    target = target.parentNode;
  }
});
```

如果一个事件不能冒泡，比如mouseEnter，那么REACT会直接绑定到对应的DOM上。

上述代码简单理解就是REACT给每个事件绑定做了一个映射关系，KEY是DOM，VALUE是事件处理函数，能冒泡的事件都会被放到根节点进行委托，基于事件目标确认当前DOM是否绑定了映射关系，如果有就触发，然后逐层冒泡。

**REACT销毁组件时，会自动解除所有注册的事件，以防止在多次渲染时事件注册多次**，不过一般来说事件会绑定在在叶子组件上，而实际上的绑定是发生在根节点，**因此每个叶子组件的销毁和重新渲染时，REACT只会更新它的DOM和事件处理函数的映射关系，根节点不需要解除注册**。对于不能冒泡的事件，REACT会直接绑定到对应的组件上，因此如果这些组件被销毁和重新创建了，那么REACT会先销毁事件，然后再重新注册，这就是即使在严格模式下，绑定事件也不会导致副作用的原因。

和原生的内联事件一样，**REACT的事件处理函数通常也只接收一个入参，即`event`，事件本身**。

参考组件传参，也可以把事件处理函数作为组件入参，由外部传入。



#### 组件的状态性以及HOOKS入门（非常重要）

HTTP协议是无状态的，但是在WEB2之后通常都需要服务端记录用户状态，以提供个性化服务，所以产生了cookie和session。

同样的道理，REACT倾向于开发者使用纯函数组件，但同时也意味着**组件本身是无状态的**，它在首次渲染后只能保持不变，即使它具有一定的交互能力，但本质上它无法记录和用户的交互历史，并在重新渲染后体现出这个历史。而显然在WEB2时代之后丰富的UI交互能力是前端领域的发展趋势之一。

所以REACT团队设计了一套HOOKS，来让组件具有状态，但是这个状态基本上是在组件内部的，因此是一个可控制的副作用。从严格意义上看，组件不再是纯函数，因为它的状态变量一直存活到了下次渲染，直到组件被彻底销毁。而从广义上看，组件的状态只在组件内部使用（大部分时候），因此也可以把它视为组件的一部分（实际上不是，后续会解释），所以组件依然是纯函数。

**REACT的HOOKS看上去有很多，但实际上归纳总结后，可以找出它们的核心设计思路：**

1. **把代码（业务逻辑），或者状态，进行保存，使得它们可以独立于组件的重新渲染**
2. **控制组件重新渲染的时机，可以基于各种条件，或者对状态的观察，或者干脆不再重新渲染**
3. 只能在组件内部声明

后续学习的所有HOOKS都可以使用上述思路进行分析。比如最简单的一个HOOK，`useState`，它的作用是这样：

1. 保存组件的内部状态，使得其可以独立于组件的重新渲染，即组件具有了状态性
2. 提供了修改状态后立刻预约组件重新渲染的功能`setState`

举例：

```jsx
import { useState } from "react";

export default function Button() {
  const [counter, setCounter] = useState(0);

  function reduce() {
	setCounter(val => val - 1);
  }
  function add() {
    setCounter(val => val + 1);
  }
  function reset() {
    setCounter(0);
  }

  return (
    <div className="flex justify-center">
	  <button className="border-2 p-2 mr-2" onClick={reduce}>-</button>
	  <span>counter is {counter}</span>
	  <button className="border-2 p-2 ml-2" onClick={add}>+</button>
	  <button className="border-2 p-2 ml-2" onClick={reset}>reset</button>
	</div>
  );
}
```

 使用HOOKS的几个原则：

- 声明在组件内部开头，顺序很重要，因为是REACT在帮助组件管理它的内部状态，而REACT在管理时只是给每个组件定义了一个数组用于保存各个状态
- 禁止使用条件语句进行声明，所有的HOOKS必须是无条件声明
- 所有HOOKS均以`use`开头，由于REACT支持自定义HOOKS，因此声明这些HOOKS时也应该遵循此命名规范

上述代码最核心的就是`const [counter, setCounter] = useState(0)`，这里的`useState`就是一个HOOK，它支持传入一个值，或者一个函数调用，当然也可以直接把函数作为值传入。如果传入函数调用，则它表示运算这个函数并获得其结果作为初始值。

之后它返回一个数组，第一个元素是当前值，没有任何封装，第二个元素是一个函数，它的作用是修改当前值并在修改后计划一次重新渲染，由此也可以看出上述代码中的`setCounter`是一个异步函数，虽然counter是同步修改的但是它不会在组件UI上提现，只有重新渲染后才会体现。

还注意上述代码的JSX部分使用了counter的值，但是不使用也可以，**REACT没有SVELTE的静态语法分析，也没有VUE的依赖搜集，即使JSX内不使用counter，调用`setCounter`依然会触发重新渲染，所以在某些时候可以把`setCounter`视为一个只触发渲染，不保存数据的功能函数**。

注意到`setCounter`可以接收一个具体的值，也可以接收一个函数，即`val = > val + 1`表示对当前值进行修改，并返回修改的结果。

**必须要经过一定间隔地调用setCounter**， 否则会进入无限地重新渲染，导致堆栈溢出，这就是为什么上述例子是用事件来触发调用。

执行上述代码可以发现counter修改后触发了重新渲染，如果需要检测组件的重新渲染，可以使用`useEffect`，它是另一个HOOK，用于把一个函数的执行独立于组件渲染，用法如下：

```jsx
useEffect(() => {
  console.log("cpn re-rendered");
});
```

注意它也是HOOK，因此也必须声明在组件内部开头，应该放在`useState`后面，也需要`import { useState } from "react"`。

虽然`useState`在设计上是作为组件内部状态的管理方法，但是也可以把这个值和函数传递给子组件使用（这样会进一步破坏组件的纯函数特性，使得子组件具有了修改父组件状态并触发重新渲染的能力）， 比如：

```jsx
// 父组件顶部声明
const [counter, setCounter] = useState(0);

function CounterCpn({counter, setCounter}) {
  // 直接使用父组件传递过来的，避免拥有自己的状态
}
```

虽然组件的状态是跟随组件的，这表示如果组件被销毁了则组件的状态也会跟着销毁，但是下面的代码实际上会保留组件的状态：

```jsx
export default function Demo() {
  const [trigger, setTrigger] = useState(false);

  return (
    <>
      <div className="flex justify-center">
        <button className="border-2" onClick={() => setTrigger(!trigger)}>click trigger</button>
      </div>
      {trigger ? (<Counter styled={trigger} />) : (<Counter styled={trigger} />)}
    </>
  );
}

function Counter({ styled }) {
  const [counter, setCounter] = useState(0);
  return (
    <div className="flex justify-center mb-4">
      {styled ? (<span>yes it is styled</span>) : (<h4>no style</h4>)}
      <button className="border-2 px-2 mr-2" onClick={() => setCounter(val => val - 1)}> - </button>
      <span>{counter}</span>
      <button className="border-2 px-2 ml-2" onClick={() => setCounter(val => val + 1)}> + </button>
    </div>
  )
}
```

注意上述代码的组件结构：

```
|---ROOT
  |---DIV
  |  |---BUTTON
  |---[COUNTER_CPN1 || COUNTER_CPN2]
```

通过条件渲染，在同一个位置展示2个相同类型，但是不同实例的组件，如果按照之前的观点，组件的状态是跟随组件生命周期的，所以上述结构中，2个COUNTER应该具有各自的状态，当组件1销毁切换到组件2时，组件1的状态应该也销毁，组件2的状态应该从0开始。

但是实际是什么呢？看上去组件2“继承”了组件1的状态！实际是，组件的状态并不是保存在组件内的，而是保存在REACT渲染的虚拟DOM内的，**如果在同一个结构的相同位置有相同类型的组件被销毁和创建，则REACT会视为这个位置并没有组件的实际销毁，而是组件的过渡，因此REACT会让新创建的组件使用上一个销毁组件的状态**。

**注意一定要同一个结构的相同位置**，所以如果上述代码的组件结构有轻微变动，比如：

```
COUNTER_CPN1 => <div>COUNTER_CPN2</div>
```

或者：

```
<section>COUNTER_CPN1</section> => <div>COUNTER_CPN2</div>
```

那么对于REACT来说，上述状态变动后，新创建的组件结构上就变化了，比如多了一层节点，或者整个节点本身的父标签更换了，即使外部看上去一样，REACT也会把这个行为视为组件的销毁和创建，因而不会保留销毁组件的状态。

还有一个隐藏的问题，组件类型必须相同，以下面的代码为例，每次执行后实际上会产生不同类型的组件：

```jsx
function MyCpn() {
  function TempChild() {
    return <span>temp child</span>;
  }
  
  return (
    <div><TempChild /></div>
  );
}
```

这个就是**不应该在组件内定义子组件**的原因，因为组件函数内定义的子组件，在REACT看来每次执行后它都是一个新的组件，属于不同的类型，**因为组件函数也是有引用地址的，REACT通过比较组件函数的引用地址来判断组件是否相同类型**。



#### 组件渲染机制和状态不可变性

总体渲染机制可以简化为：

```
触发 => [执行组件函数创建虚拟DOM] => DIFFING => 修改真实DOM 
```

触发过程不一定是通过setState触发，实际上对输入框的修改，或者对表单标签的交互，都会触发REACT开始新一轮渲染机制。

注意创建虚拟DOM不是必须的，因为REACT会保存组件的状态，**如果组件是无状态的，则REACT不会在首次执行组件函数后后再次执行**。**如果组件是有状态的，则REACT会帮助管理其状态，并且判断只有在状态变化后才再次执行函数**。

执行组件函数会产生虚拟DOM，**它的顺序是从父到子的**，如果是首次渲染，会先创建根节点虚拟DOM，然后发现它包含子组件函数，进一步执行子组件函数以创建子组件虚拟DOM，逐步从上往下，直到所有的叶子组件虚拟DOM都被创建好。重新渲染也是这个顺序。

注意首次渲染的时候，创建出来的虚拟DOM会被直接用于创建真实DOM，而后续更新时，REACT会在必要时创建新的虚拟DOM，并且和老的虚拟DOM进行比较（对，此时内存中会同时存在2个虚拟DOM，但是鉴于JS的引用特性，对于没有必要更新的组件来说，它的上一个版本的虚拟DOM会被复用，因此这2个虚拟DOM往往会有交集），即DIFFING，**这个比较的目的就是找出两个虚拟DOM的最小差异点**，最后用这个差异点去更新真实DOM。最后是浏览器的环节，真实DOM更新后，浏览器会执行REFLOW和REPAINT，简单来说就是重新计算布局以及填充像素，以展示更新后的UI。

`setState`这个HOOK其实就是起到触发的作用，**它是异步的，即它不会修改当前结果中的状态**，比如还是counter的例子，当调用了`setCounter`，它本质上只是触发了再次渲染，并修改了再次渲染时所需的counter值，但是**如果它后面还有代码，拿到的counter依然是当前的渲染结果，除非手动修改它**。

REACT官方文档对此的解释是，每次渲染后，你拿到的state实际上只是它真正的state的一个副本（对于对象来说也是副本），所以你修改副本没有意义，即使你修改成功了，即使你用定时异步的方式修改成功了，只要不触发渲染就没有意义，就不会提现在UI上。

所以应该把组件函数内的状态都视为不可变的，只有通过HOOKS修改后才会发生变动，但是那只会在下次的渲染后才体现，在当前修改后的代码，其状态依然是修改前的结果。



#### 连续的状态更新

调用`setState`多次的时候有以下特性：

- 如果在同步代码内连续调用了多次，REACT会进行批处理，如果通过微任务调用多次（比如Promise的多个then调用），基于版本差异，18及以后会批处理，18以前不会，宏任务内调用肯定不会批处理
- 由于`setState`是异步的再次渲染，因此设置一个固定值无论多少次，都会以最后一次为准
- 通过传入`val => val + 1`这样的函数，可以做到累积修改，实际上REACT会在异步时把这些函数依次执行，因此**传入函数后，也不是立刻执行，而是把累积修改加入到异步队列内，然后依次执行**

如果有多个状态，也希望同时更新时，虽然可以多次调用各自的setState，但是当UI交互变得更复杂时，推荐使用`useReducer`以进一步明确操作意图和状态修改之间的关系，这个HOOK后面会提到。



#### 对象和集合的状态更新

简单一句话，**对象和集合的修改，都需要返回一个新对象，由于JS的引用特性，REACT实际上需要的是拿到新对象的引用地址，以确保每次渲染后，开发者只能基于新的应用地址进行修改**。

比如对象，假设有如下代码：

```jsx
const [obj, setObj] = setState({name: 'Arc', age: 10});
```

还记得状态不可变性吗？我们拿到的状态本质上是一个副本，即使它是对象也一样，直接修改是没有意义的。所以`obj.name = 'Foo'`没有意义，解决办法是：

```jsx
const newObj = {name: 'Foo', ...obj}; // 只修改必须的属性
setObj(newObj);
```

集合也是一样的道理，比如数组，它的方法，有些是原地修改，有些会返回新数组，**我们只能用返回新数组的方式来修改数组**。

由于对象和集合可以有很深的结构，**在创建对象状态时，尽量做到扁平化，集合也是同样的道理**。



#### 输入框的事件处理和REACT的管理机制

在REACT中，输入框一般是指INPUT框或者TEXTAREA框，它们的标准交互范式如下：

```jsx
const [text, setText] = useState('default input');

<textarea value={text} onChange={e => setText(e.target.value)} />
```

注意到，必须要在每次输入发生改变的时候，执行`setText`以触发副作用，看上去很繁琐，但是有必要，以下是测试：

测试1，**把`onChange`完全去掉，只保留`value={text}`，结果是不能输入任何信息。**

测试2，**还是去掉`onChange`，也去掉`value`，改为`defaultValue={text}`，结果是可以输入信息，但是REACT放弃了对此输入框的管理，它的输入信息无法自动同步给其他需要用到的地方**。

为什么会这样呢？

尝试用`useEffect`在测试1场景进行监控，发现没有任何输出，说明虽然不能输入，但是组件也没有重新渲染。输入框本身也没有禁止输入，那么只能得出一个可能，当**在输入框内添加`value={foo}`，而非`defaultValue={foo}`，结果是把此输入框纳入REACT的管理**。**REACT会在修改真实DOM的环节把输入框这个DOM内的`value`属性改为组件内的值**，即：

```js
inputDom.value = text;
```

在REACT看来，没有调用`setText`，组件的状态没有变化，因此不需要重新渲染，也就没有执行`useEffect`的必要，但是用户确实输入了，因此需要修正真实DOM，所以无论用户怎么输入，REACT都会顽强地在用户输入后立刻进行修正，以确保用户无法输入。

如何检测到这个环节呢？REACT直接修改了`value`属性，因此只能用JS的属性描述符来进行监控了，需要在`value`属性的`setter`环节进行拦截，具体代码如下：

```jsx
import { useState, useRef, useEffect } from "react";

export default function Button() {
  const [input, setInput] = useState('input here');
  const ref = useRef(null);
    
  useEffect(() => {
    const node = ref.current;
	if (node) {
      // Grab the original property descriptor for 'value'
      const proto = Object.getPrototypeOf(node);
      const descriptor = Object.getOwnPropertyDescriptor(proto, 'value');

      // Patch the setter
      Object.defineProperty(node, 'value', {
        get: descriptor.get,
        set(newVal) {
          console.log('React set value →', newVal);
          descriptor.set.call(this, newVal);
        },
        configurable: true,
        enumerable: true,
      });

      // Cleanup: restore original descriptor
      return () => {
        Object.defineProperty(proto, 'value', descriptor);
      };
    }
    return;
  }, []);

  return (
    <div className="flex justify-center">
      <span>your input here</span>
      <input type="text" className="border-1 ml-4" value={input} ref={ref} />
    </div>
  );
}
```

上述代码中涉及HOOKS的部分，细节后面会提到，这里只需要明白，只能使用属性描述器进行拦截，才能观察到REACT做了什么，执行代码后，尝试修改输入框，会发现REACT在不停地尝试把输入框的value改回组件内原本的值，这样从外部看来输入框虽然可以编辑，但是却不能修改。



#### 强制组件渲染和状态重置

之前提到过，在同一个结构的同一个位置，销毁和创建同一个组件，会导致新创建的组件复用旧组件的状态。

但是如果需要强制废弃旧组件的状态呢？参考集合渲染，可以使用`key`关键字来强制标记组件，如果组件的key变动了，则即使是同一位置同一类型，也会被REACT视为新组件：

```jsx
export default function Demo() {
  const [trigger, setTrigger] = useState(false);
  const cpnKey = Math.floor(Math.random() * 1e7); // 搞个随机的KEY就可以强制REACT放弃旧组件的状态

  return (
    <>
      <div className="flex justify-center">
        <button className="border-2" onClick={() => setTrigger(!trigger)}>click trigger</button>
      </div>
      {trigger ? (<Counter key={cpnKey} styled={trigger} />) : (<Counter key={cpnKey} styled={trigger} />)}
    </>
  );
}

function Counter({ styled }) {
  const [counter, setCounter] = useState(0);
  return (
    <div className="flex justify-center mb-4">
      {styled ? (<span>yes it is styled</span>) : (<h4>no style</h4>)}
      <button className="border-2 px-2 mr-2" onClick={() => setCounter(val => val - 1)}> - </button>
      <span>{counter}</span>
      <button className="border-2 px-2 ml-2" onClick={() => setCounter(val => val + 1)}> + </button>
    </div>
  )
}
```



#### 多个原子化状态的协同更新

一个丰富交互的UI，**通常在设计时会希望用户按照意图操作，而非按照状态操作**，一个简单的例子，比如注册场景，一个用户友好的注册场景一般是这样的，给出基础表单A，用户输入完成后下一步，跳转到基础表单B，同时把表单A的数据进行校验和保存，之后再进一步跳转到表单C，最后完成注册。在这个场景中，**表单校验，处理，和下一个表单的交互需要在流程上视为同一个状态变化**，而实际上我们应该用不同的状态保存不同的用户数据，这就使得**开发者需要按照一定规则同时操作多个原子化的状态，**

REACT提供了一个HOOKS，叫`useReducer`来解决这个问题，它的使用思路是这样的：

- 最佳场景是每次都需要对多个原子化的状态进行协同操作，所以首先要把组件内所有原子化状态视为一个整体，**每次修改状态时，不应该单独修改某个状态，而应该把所有状态的预期值都进行提交**
- 编写一个意图处理函数，它应该接收2个参数，一个是当前的组件内所有原子化状态，一个是需要修改的所有状态，为了操作方便，需要修改的所有状态内，还应该额外包含一个业务逻辑的修改意图，这个意图可以理解为CRUD
- 区分不同的修改意图，对当前的状态进行修改，注意REACT的状态不可变性，返回修改后的新状态作为一个整体对象

伪代码是这样的：

```jsx
// 意图处理函数最好抽离出来
function intentionHandler(curState, update) {
  const intent = update.intent;
  switch(intent) {
    case 'intent1': {
      return {...curState, update.id};
    }
    case 'intent2': {
      return {...curState, update.name, update.age};
    }
  }
}

// 组件
export default function MyCpn() {
  const [totalState, setTotalState] = useReducer(intentionHandler, initialTotalState);
  
  function doIntent1(id) {
    setTotalState({intent: 'intent1', id: id});
  }
    
  return (<Jsx />);
}
```

操作流程是：

- 绑定交互事件
- 每个事件都通过一个`setTotalState`（真实名称是`dispatch`）传入需要协同修改的状态
- 编写处理所有意图所有场景的协同修改函数`intentionHandler`，在里面基于意图进行不同的操作，每个操作最后都应该返回预期的状态
- 把`intentionHandler`传入到`useReducer`内，以便REACT进行管理
- REACT会在`setTotalState`时调用`intentionHandler`，进行处理，拿到新状态后，再按照`useState`的流程规划重新渲染

以下给出一个更好的例子，以之前提到的多步骤表单注册为例：

```jsx
import { useReducer } from 'react';

const STEP_1 = 1;
const STEP_2 = 2;
const ACTION_UPDATE = 'update_field';
const ACTION_NEXT = 'go_next';
const ACTION_PREV = 'go_back';

const INITIAL_STATE = {
  step: STEP_1,
  username: '',
  password: '',
  email: '',
  bio: '',
  interests: '',
};

function signUpReducer(state, action) {
  switch (action.type) {
    case ACTION_UPDATE:
      return { ...state, [action.field]: action.value };
    case ACTION_NEXT:
      return { ...state, step: STEP_2 };
    case ACTION_PREV:
      return { ...state, step: STEP_1 };
    default:
      return state;
  }
}

function Form1({ form, dispatch }) {
  function handleChange(e) {
    dispatch({
      type: ACTION_UPDATE,
      field: e.target.name,
      value: e.target.value
    });
  };

  return (
    <>
      <div className="space-y-3">
        <h2 className="text-xl font-bold">Step 1: Account Info</h2>
        <input name="username" placeholder="Username" className="w-full p-2 border rounded"
          value={form.username} onChange={handleChange} />
        <input name="password" type="password" className="w-full p-2 border rounded" placeholder="Password"
          value={form.password} onChange={handleChange} />
        <button type="button" onClick={() => dispatch({ type: ACTION_NEXT })}
          className="bg-blue-500 text-white px-4 py-2 rounded">
          Next
        </button>
      </div>
    </>
  )
}

function Form2({ form, dispatch }) {
  function handleChange(e) {
    dispatch({
      type: ACTION_UPDATE,
      field: e.target.name,
      value: e.target.value
    });
  };
  return (
    <div className="space-y-3">
      <h2 className="text-xl font-bold">Step 2: Profile Info</h2>
      <input name="email" type="email" className="w-full p-2 border rounded"
        placeholder="Email" value={form.email} onChange={handleChange} />
      <textarea
        name="bio"
        placeholder="Short Bio"
        value={form.bio}
        onChange={handleChange}
        className="w-full p-2 border rounded"
      />
      <input
        name="interests"
        placeholder="Interests (comma-separated)"
        value={form.interests}
        onChange={handleChange}
        className="w-full p-2 border rounded"
      />
      <div className="flex justify-between">
        <button type="button" onClick={() => dispatch({ type: ACTION_PREV })}
          className="bg-gray-400 text-white px-4 py-2 rounded">
          Back
        </button>
        <button type="submit" className="bg-green-500 text-white px-4 py-2 rounded">
          Submit
        </button>
      </div>
    </div>
  );
}

export default function SignUpForm() {
  const [form, dispatch] = useReducer(signUpReducer, INITIAL_STATE);

  const handleSubmit = (e) => {
    e.preventDefault();
    console.log(JSON.stringify(form));
    alert('submitted');
  };

  return (
    <form onSubmit={handleSubmit} className="max-w-md mx-auto p-4 border rounded-xl shadow space-y-4">
      {form.step === STEP_1 && (<Form1 form={form} dispatch={dispatch} />)}
      {form.step === STEP_2 && (<Form2 form={form} dispatch={dispatch} />)}
    </form>
  );
}
```

编写顺序总结：

1. 设计组件的总体状态以及允许的操作意图
2. 编写具体的reducer函数，它从新的状态中获取操作意图，基于操作意图和当前的状态，返回一个新的状态
3. 使用`const [state, dispatch] = useReducer(reducerFn, initialState)`来获取整体状态以及状态修改函数
4. 组件内绑定事件，基于事件触发dispatch，传入意图和需要修改的状态

相比`useState`来说，多了一个编写reducer函数，以及把状态修改抽象为操作意图的过程。**注意reducer函数必须是纯函数，因为它的入参1是当前状态，入参2是需要修改的新状态，已经图灵完备，所以不应该有任何副作用**。



#### 兄弟组件间状态共享

注意这里的前提，是兄弟组件的状态共享，即它们有一个相同父组件且到达父组件的路径比较短，在此场景下，直接把需要共享的状态放在父组件就可以了，比如：

```jsx
import { useState } from "react";

export default function CpnShareDemo() {
  const [input, setInput] = useState('input here');

  return (
    <>
      <InputBox hint="first input box" input={input} setInput={setInput} />
      <InputBox hint="second input box" input={input} setInput={setInput} />
    </>
  );
}

function InputBox({ hint, input, setInput }) {
  return (
    <div className="flex justify-center mb-4">
      <span>{hint}</span>
      <input type="text" className="border-1 ml-4" value={input} onChange={e => setInput(e.target.value)} />
    </div>
  )
}
```

注意上述代码直接在父组件声明输入内容状态，然后让2个子组件接收和具有修改权限，这样任一子组件的修改都会导致另一个子组件的状态变动。这个设计还是比较常见的，比如一个App，通常会给出多个设置入口，在A入口内的设置会影响到B入口看到的结果。

另外组件共享还引出了一个问题，如何在组件销毁后保存它的私有状态？有几个解决方案：

- 禁止组件销毁，而只做组件隐藏，对于轻度UI来说可以，重度UI会导致性能问题
- 组件状态放到父组件管理，子组件销毁或者初始化都可以，因为数据在父组件
- 借用外部存储，比如API，或者浏览器本地缓存等等，比如写邮件或者博客，把信息保存在本地缓存，因此新开编辑窗口也可以展示之前的草稿



#### 更通用的组件间状态共享方法

兄弟间组件状态共享只能解决一部分问题，比如有以下场景：

```
        ROOT
       /   \
      /     \
   CHILD1  CHILD2
             |
             |
             |
           CHILD3
```

如果需要CHILD1和CHILD3共享数据，那么只能把数据放在ROOT里，那么CHILD2本质上需要透传，这个过程可以更复杂，使得需要透传的组件越来越多，这样组件不仅臃肿而且使得中间层组件和叶子组件的耦合会很严重。

解决办法是使用`Context`，它的设计思路是这样的：

- REACT的设计思路是状态都要放在组件内部以防止副作用，所以**这个context在使用时也应该放在某个组件内部**而非像VUE的STORE模式那样是一个完全独立的模块

- 区分出上下级关系，因为REACT建议单向的数据流通，因此上级是context的提供者，负责创建context作用域，下级是context的消费者（使用者），**只有在context内的下级组件才能消费** ，如果下级组件不在context作用域内，则只能拿到默认值，无法感知到context变动

- 使用`createContext`构造一个**桥梁**，通常它需要单独一个文件，以便上级组件和下级组件都可以单独引入
- 上级组件使用context桥梁划定作用域并注入数据或者方法（如果可以放权下级组件进行修改时），在作用域内的下级组件可以消费，之外的不行，**context桥梁对于上级组件来说就是一个容器组件**，或者叫作用域
- 下级组件引入context桥梁并使用`useContext`订阅（消费）它，以获取上级组件注入的数据和方法

样例代码：

```jsx
import { createContext, useContext, useState } from "react";

// themeContext.js，建议单独一个文件
const THEME_DARK = "theme_dark";
const THEME_LIGHT = "theme_light";
const ThemeContext = createContext(THEME_LIGHT); // 这个就是context桥梁

// 上级组件
export default function ContextDemo() {
  const [theme, setTheme] = useState(THEME_DARK);

  function changeTheme() {
    setTheme(theme === THEME_DARK ? THEME_LIGHT : THEME_DARK);
  }

  return (
    <>
      <h1>PARENT TITLE</h1>
      <button className="border-2 px-1" onClick={changeTheme}>change theme</button>
      <ThemeContext value={theme}> {/* 这里把context桥梁作为单向的theme获取通道 */}
        <Child />
      </ThemeContext>
    </>
  );
}

// 下级组件
function Child() {
  const curTheme = useContext(ThemeContext); // 下级组件只能拿到上级组件传递过来的值
  return (
    <h4>current theme is {curTheme}</h4>
  );
}
```

`createContext`不是一个HOOK，所以它可以在组件外声明。它返回的就是context桥梁，当上级组件导入这个桥梁并作为容器标签使用时，**本质上是省略了`.Provider`的容器组件**，即上述代码的上级组件也可以写为`<ThemeContext.Provider>`。上级组件的JSX内可以设置多个这样的容器组件以创建多个作用域，不过一般来说context会适用于购物车，用户登录状态，主题，系统语言，前端路由等适用于全局的场景。但是如果上级通过`value`注入了修改函数，则意味着任何下级都可以修改它，因此面临潜在的混乱场景。

下级组件使用`useContext(桥梁)`来监听和访问共享数据，这个`useContext`是一个HOOK所以它只能在组件内使用。

context就是一个桥梁，一个项目可以有不同的桥梁，负责传递不同类型的数据，也可以基于业务进行分类。当然最简单的做法是把所有需要共享的数据都放在一个context内，然后这个context放在根组件位置以便所有子组件都可以消费。

还有很多很灵活的用法，比如组件相关的context，或者某个业务相关的context，它内部或许有复杂结构，或许需要在不同的深度子组件各自修改一部分，此时就可以结合context和reducer，基于意图来操作一个复杂状态。



#### REACT唯一可变性设计`useRef`

`useRef`是REACT的唯一可变性HOOK，它的特性如下：

- 状态的独立保存
- 单独修改它永远不会触发再次渲染
- 因为永远不会再次渲染，所以REACT只能打破原则采取可变性设计，它是所有HOOKS中唯一采用了可变性设计的
- 此HOOK只在首次渲染时执行一次，换言之它创建的对象的引用地址是固定的（**严格模式下组件会渲染销毁再渲染，因此它也会执行2次**）

简单用例：

```jsx
import { useRef, useState } from 'react';

export default function UseRefDemo() {
  const feedCount = useRef(0);
  const [, setTrigger] = useState(false); // 这个写法表示只是需要强制渲染，但并不需要用到具体值

  function forceRerender() {
    setTrigger(val => !val);
  }

  return(
    <>
      <button className="border-2 px-1 mr-2" onClick={() => feedCount.current++} >feed</button>
      <span>prev feed count is {feedCount.current}</span>
      <button className="border-2 px-1 ml-2" onClick={forceRerender}>update feed count</button>
    </>
  );
}
```

上述代码通过`useRef`创建了一个可变性对象，所有REF对象都有唯一默认根属性叫`current`，即使初始传入的是对象也一样，它只会变成`current`属性对应的对象。之后可以通过事件或者其他方法进行修改，但是因为它是可变的，所以REACT默认不会触发重新渲染。如果需要强制重新渲染，可以使用`useState`频繁修改一个不使用的变量即可。

`useRef`不仅可以保存数据，还可以保存函数，比如定时函数，或者轮询函数等，以便手动控制倒计时或者轮询，比如这个例子：

```jsx
import { useState, useRef } from 'react';

export default function Stopwatch() {
  const [startTime, setStartTime] = useState(null);
  const [now, setNow] = useState(null);
  const intervalRef = useRef(null);

  function handleStart() {
    setStartTime(Date.now());
    setNow(Date.now());

    clearInterval(intervalRef.current);
    intervalRef.current = setInterval(() => {
      setNow(Date.now());
    }, 10);
  }

  function handleStop() {
    clearInterval(intervalRef.current);
  }

  let secondsPassed = 0;
  if (startTime != null && now != null) {
    secondsPassed = (now - startTime) / 1000;
  }

  return (
    <>
      <h1>Time passed: {secondsPassed.toFixed(3)}</h1>
      <button onClick={handleStart}>
        Start
      </button>
      <button onClick={handleStop}>
        Stop
      </button>
    </>
  );
}
```

基于`useRef`的状态可变性以及不触发重新渲染，REACT设计了一个`ref`属性**用于在组件内获取组件自身的DOM对象或者子组件的DOM对象**。它的用法是这样的：

- 组件内声明一个`const domRef = useRef(null)` 用于存储DOM对象
- 在JSX内，对需要注入的DOM添加一个`ref = {domRef}`标记
- 组件渲染后，真实DOM挂载时（注意不是在全部过程完成，而是刚刚在真实DOM挂载后），对应标记了ref属性的DOM，会自动把它自身注入到`domRef`变量内，这样当前渲染就拿到了DOM

样例代码：

```jsx
import { useRef, useState } from 'react';

export default function UseRefDemo() {
  const domRef = useRef(null);
  const [counter, setCounter] = useState(0);

  const newId = Math.floor(Math.random() * 1e7);
  console.log('newId is', newId);
  if (domRef.current) {
    console.log(domRef.current.id); // 后续渲染时ref存储了上次的DOM，因此会执行这里，注意ID是上次渲染的结果
    console.log(domRef.current);
  } else {
    console.log('first render'); // 首次渲染时会执行此处
  }

  return(
    <>
      <button className="border-2 px-1 mr-2" onClick={() => setCounter(val => val - 1)} > - </button>
      <span>counter is</span><input className="border-2 px-1 ml-2" key={newId} id={newId} ref={domRef} value={counter} />
      <button className="border-2 px-1 ml-2" onClick={() => setCounter(val => val + 1)}> + </button>
    </>
  );
}
```

上述代码的执行顺序是这样的：

1. 首次渲染执行`useRef`，`domRef`初始化为`{current: null}`
2. JSX转换为虚拟DOM并挂载为真实DOM，此时执行`ref={domRef}`，把它修改为指向本轮渲染的真实DOM
3. 用户点击按钮触发`setState`，引发重新渲染
4. 重新渲染时，`console.log`展示的是上次挂载的真实DOM，**如果当前的DOM发生了销毁（比如通过key的变动），则不会指向当前的DOM**
5. 执行完成，获取JSX，转为虚拟DOM，并挂载真实DOM，此时执行`ref={domRef}`，把它修改为指向本轮渲染的真实DOM

所以当绑定DOM时，不应该在组件函数内直接通过`useRef`获取DOM，因为它拿到的是上轮渲染完成的DOM，这个问题需要用一个新的HOOK来解决，后续会提到。

如果需要对一个列表的DOM进行存储，则可以这样操作：

1. 声明一个useRef对象，可以用map或者其他方式存储
2. 列表DOM采用map方法返回JSX，在返回的JSX内，通过`ref(node => { //manual save or clean up})`来处理单独节点的保存和销毁问题，注意一定要加上销毁的逻辑，因为即使一个最简单的TODO LIST都需要频繁创建新的列表和销毁列表，如果不销毁则会导致内存泄漏
3. 当列表DOM加载后，ref的函数内node入参返回该DOM节点，如果它销毁，则此node会返回NULL，可以基于这个进行存储或者销毁的操作

样例代码：

```jsx
import { useRef, useState } from 'react';

export default function UseRefDemo() {
  const domRef = useRef(new Map());
  const [list, setList] = useState([
    {id: 1, name: 'book'},
    {id: 2, name: 'pencil'},
    {id: 3, name: 'paper'},
    {id: 4, name: 'eraser'},
    {id: 5, name: 'ruler'}
  ]);

  function deleteFromList(id) {
    const filtered = list.filter(ele => ele.id !== id);
    setList(filtered);
  }

  function saveListNode(id, node) { // 这里用map保存列表DOM，key是列表元素的ID
    if (domRef.current) {
      if (node) {
        domRef.current.set(id, node);
      } else {
        domRef.current.delete(id);
      }
      console.log(domRef.current);
    }
  }

  return(
    <div className="pl-10">
      <h4>item list</h4>
      <ul className="list-disc pl-10">
        {list.map(ele => {
          return (
            <div className="flex mb-4" ref={node => saveListNode(ele.id, node)}>
              <li key={ele.id}>{ele.name}</li>
              <button className="border-2 px-1 ml-4 hover:cursor-pointer hover:bg-blue-100 transition duration-200" onClick={() => deleteFromList(ele.id)}>
                delete me
              </button>
            </div>
          );
        })}
      </ul>
    </div>
  );
}
```

上述代码，在列表元素中用`ref={node => saveListNode(ele.id, node)}`处理列表的DOM引用问题，当用户点击按钮删除列表元素时，事件是这样发生的：

1. 创建删除后的新列表，并触发渲染
2. 渲染完成，DIFFING完成，REACT准备操作真实DOM
3. REACT操作真实DOM，移除对应要删除的节点，**并执行两次ref函数（对的你没看错，即使通过KEY保留不需要操作的DOM，也会执行2次）**，第一次会传入null以执行删除旧节点的逻辑，第二次会传入当前刚刚挂载好的新节点以重新保存

简单来说，**`ref`的回调函数对于任何DOM都会执行两次，即使这个DOM通过KEY绑定了，而且没有任何变化**。REACT这样做是为了100%保证ref绑定的节点一定是最新的。

如果需要获取子组件的DOM对象，则子组件可以把父组件的domRef作为属性传入，并且设置好即可，比如：

```jsx
function ParentCpn() {
  const childDomRef = useRef(null);
  
  return (
    <ChildCpn domRef={childDomRef} />
  );
}

function ChildCpn({domRef}) {
  return (
    <input type="text" ref={domRef} />
  )
}
```

在REACT18及之前还有一个`forwardRef`函数用于解决上述问题，从19开始已经进入废弃，后续也不会再用。



#### `useEffect`--通用灵活的副作用管理HOOK

REACT有一个通用的副作用管理HOOK，提到副作用，这里回顾一下它通常的表现形式（实际业务中会更多）：

- 组件的状态管理和状态共享

- 从其他系统获取数据
- 直接操作DOM（有些需求，直接操作DOM会比使用REACT框架更有效）
- 自动化行为，比如定时，或者轮询
- 绑定事件，比如添加用户操作事件，或者接收端口消息后触发事件
- 访问WEB存储，比如cookie，session，storage等等

一些副作用可以通过事件来处理，比如最常见的counter状态管理，就是通过用户事件来增加或者减少的，但是其他副作用无法通过用户事件来完成，此时就是`useEffect`起作用的时候，它可以被视为REACT的通用副作用管理HOOK。

它的设计思路如下：

- 组件的属性不可变性要更优先，因此**副作用的处理需要放在渲染和DOM操作完成之后**
- 会触发副作用的函数放在`useEffect`内执行，副作用应该和组件绑定
- 考虑到有些副作用只需要触发一次，有些需要触发多次，有些需要基于状态变量的变动来控制，因此还需要加一个配置项以控制不同副作用的触发时机和频率
- 当组件销毁时，理论上它产生的副作用也应该销毁，以保证其副作用不会对系统的其他部分产生影响，因此还应该要求开发者针对每个副作用都提供一个清理机制

从HOOKS设计思路上看，`useEffect`虽然涉及保留代码，但本质上是控制UI的渲染时机，而且是通过用户事件以外的方式，**时机控制更多时候依赖开发者的设计而非用户自身的意志**。

使用案例，以定时器为例，加一个自动修改状态的机制：

```jsx
import { useState, useEffect } from 'react';

export default function UseEffectBasicDemo() {
  const [time, setTime] = useState(Date.now());
  useEffect(() => {
    console.log('begin loop');
    const loopId = setInterval(() => {
      console.log('change time');
      setTime(Date.now());
    }, 1000);
    return () => {
      console.log('stop loop');
      clearInterval(loopId); 
    };
  });

  function formatDateTime(time) {
    return new Intl.DateTimeFormat('zh-CN', {dateStyle: 'medium',timeStyle: 'long'}).format(time);
  }

  return(
    <div>
      <h4>current time is {formatDateTime(time)}</h4>
    </div>
  );
}
```

上述代码定义了一个时间状态，并且通过一个轮询定期修改。定时器属于副作用的一种，因此需要用`useEffect`进行管理，核心代码是这样的：

```
useEffect(side_effect_code, [dependency])
```

dependency依赖的部分后面再说，这里先解释side_effect_code，它表示会产生副作用的代码，编写方法如下：

```js
() => {
  // do side effect
  return () => { // clean up code };
}
```

简单来说，任何一个副作用函数，都需要清理。任何副作用代码，本质上并不被REACT管理和追踪，而是交给了JS运行环境，REACT只负责转交过程以及在组件销毁时再执行一次清理函数，因此，**即使一个组件销毁了，只要它不执行清理，写在它的`useEffect`内的会产生副作用的代码，就可以一直运行**，代码举例：

```jsx
import { useState, useEffect } from 'react';

export default function UseEffectDemo2() {
  const [isCpn2, setCpn] = useState(true);

  return (
    <div className="flex justify-center">
      { isCpn2 ? <Cpn2 /> : <Cpn1 /> }
      { isCpn2 && <button className="border-2 px-1 ml-2" onClick={() => setCpn(false)}>toggle</button> }
    </div>
  )
}

function Cpn1() {
  return (<h4>this is cpn1</h4>);
}

function Cpn2() {
  const id = 3;
  useEffect(() => {
    setTimeout(() => {
      console.log('cpn2 side effect done', id); // 注意组件在此之前销毁了但是这个回调依然会执行
    }, 6000);
  }, []);
  return (<h4>this is cpn2</h4>);
}
```

上述代码演示了一个组件切换的机制，CPN2有一个一次性的倒计时的副作用，但是没有清理，当点击按钮从CPN2切换到CPN1时，如果CPN2的倒计时没有完成，则它依然会继续倒计时，并随后触发回调。

所以**任何副作用函数必须返回一个清理函数**。清理的原则就是让组件销毁时系统切换到某个之前的状态，或者让正在进行的任务停止，或者无法立刻停止时，不要让这个任务影响到系统其他部分。

此外**副作用函数和清理函数内引用的变量也是有作用域概念的，基本上遵循了REACT的状态不可变性**，即每次渲染，或者每次清理，其引用的变量都是当前这次渲染的，假设系统从A切换到状态B，则清理函数会使用A状态下的变量进行清理，而不会使用当前的状态B内的同名变量。

依赖的部分，简单来说它**表示订阅的状态**，当订阅的状态发生变化时，会执行`useEffect`内部副作用函数，可以有3种形态：

- 不传入，即`useEffect(() => { // side effect code })`，表示**没有任何订阅 / 依赖，因此每次重新渲染都会执行**
- 传入空数组，即`useEffect(() => { // side effect code }, [])`，表示**空订阅，而且因为“空”也是一种状态，是一种不变的状态，因此只会执行一次**
- 传入非空数组，即`useEffect(() => { // side effect code }, [someState, someProp, someContext, someRef.current, someFunction])`，基本上副作用函数内涉及到的外部变量（非当前作用域声明的）都可以订阅，**任何一个元素的变动都会触发副作用函数在渲染和DOM操作完成后，重新执行**

比如上述时间的例子，它依赖的就是time状态，所以也可以这样写：`useEffect(() => { // 省略 }, [time])`。如果再加一个counter的例子，但是依赖依然保留在time，然后尝试修改counter，虽然会触发组件重新渲染，但是可以观察到副作用函数不会因为counter的变动而执行。

原则上，**任何在副作用函数内使用了的外部变量，都应该放在依赖内，以保证每次变动都会触发副作用，使得副作用的执行结果是准确的，但这个设计也带来了频繁的“状态变化 => 副作用终止 => 副作用启动 => 状态变化”这一流程，如果开启和关闭副作用开销较大，则并不是最优解**，更优雅的方案是**把副作用和状态变化分离**，即状态变化不会影响到副作用，而副作用可以时刻感知到新的状态。



#### `useEffect`追加阅读

[这篇文章](https://overreacted.io/a-complete-guide-to-useeffect/#dont-lie-to-react-about-dependencies)解释得很好。

里面的关键点这里记录一下：

- 每次渲染，组件内的状态，组件内的函数，函数内引用的组件内变量，其实都是不可变的，即使函数是异步的，在回调开始前组件进入了下一轮渲染，上一轮的函数内使用的组件内变量依然还是上一轮的，不会串台到下一轮，用作者的话来说，**函数只能看见它所在这轮渲染创造出的变量，看不到上一轮也看不到下一轮**
- `useEffect`内的副作用函数也是函数，因此也受制于这个规则，**副作用函数和它的清理函数也只能看见它所在这轮渲染创造出的变量**
- 一般来说，应该把依赖的部分写完整，但是，还有一个策略是**对副作用函数本身进行优化，使得它需要的依赖变少，但是也同样可以完成任务**，某些场景下，还可以实现副作用函数不受状态影响，但又可以感知到新的状态
- `const [state, dispatch] = useReducer(reducerFn, initialState)`，注意，**`dispatch`也是一个不会变化的状态修改函数，因此可以让`useEffect`直接依赖它，以实现在副作用内修改组件状态，但同时保持副作用函数不重开**
- 

这篇文章之所以重要，是因为它解决了最常见的一个开发场景：

1. 使用`useEffect`获取API数据
2. 在获取数据后，依然在`useEffect`内，修改组件状态以把数据展示到UI上
3. 因为在`useEffect`内修改了状态，所以理论上需要把这些状态添加到依赖内，但实际上业务层不需要，所以可以用`dispatch`来防止在`useEffect`内修改状态的行为会打乱副作用函数



逃生舱本质上就是类似REACT的后门，就是一些增加开发者权限的接口，主要是针对副作用的， 所以还是HOOKS的特性：

- 函数或者状态的独立保存
- 控制组件渲染时机



#### JSX语法设计思想（这部分放到最后来，等全部特性都介绍完成后再提）

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



