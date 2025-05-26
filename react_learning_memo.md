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

- 声明在组件内部开头，顺序很重要，因为是REACT在帮助组件管理它的内部状态，而REACT在管理时只是给每个组件定义了一个数组用于保存各个状态，**如果HOOKS需要依赖本地变量，则可以在HOOKS之前声明这些变量，但是也必须是无条件的**
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



#### 输入框的事件处理和REACT的管理机制（非常重要）

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

另外，如果在一个组件内声明一个不需要使用的变量，并且反复修改它，也会触发强制渲染：

```jsx
import { useState, useEffect } from 'react';

export default function ForceRender() {
  const [, setTrigger] = useState(false);

  useEffect(() => { // 这个暂时理解为可以观察组件是否重新渲染
    console.log('cpn rendered');
  });

  return (
    <>
      <h1>this is a force render cpn</h1>
      <button onClick={() => setTrigger(val => !val)} className="border-2 px-1" >clike me to force render</button>
    </>
  )
}
```

上述代码，虽然没有用到任何状态变量，但是通过一个按钮不停修改状态变量，也会触发组件的强制渲染。



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



#### REACT的父子组件通信设计

VUE对于副作用的理解和REACT不一样，所以这就使得从VUE转过来学习REACT的人，在理解REACT的父子组件通信设计上，会有障碍，这里梳理一下，在VUE里面，父子组件通过监听，或者事件来沟通：

- 父通知子的场景下，父组件把一个属性传递给子组件，子组件监听这个属性，当它变化时，执行代码，不管这个代码是否有副作用
- 子通知父的场景下，要么子组件冒泡一个事件，父组件捕获并处理，要么父组件直接把对应的通知函数传递给子组件，子组件在需要的场合进行调用，并注入参数，之后执行的逻辑都会是父组件的逻辑

**VUE的思路是：监听事件 => 事件发生 => 执行函数 => 修改状态 => 结果，REACT关注的是状态 => 结果**，可以看出VUE更关注过程，而REACT需要开发者摒弃这种思维，直接从状态和结果的角度思考：

- 一般的状态变化，如果它直接影响UI，则在编写JSX的时候考虑到所有可能的状态和对应的UI，做好声明式即可
- 如果状态变化需要触发额外代码，通常这部分代码要作为副作用来看待，因此应该使用`useEffect`来管理
- 父通知子时，还是通过属性传递给子，子基于属性可能的值，设置对应的UI，做好声明式，或者需要触发副作用时，使用`useEffect`，把父的属性作为依赖，执行副作用代码，如果需要跳过首次渲染，加一个`useRef`记录是首次渲染
- 子通知父时，把父的副作用函数传入，子直接调用即可，之后父有2种处理逻辑，1是父的副作用函数是`setState`，子修改后会直接触发父的状态变化和重新渲染，2是父的副作用函数是`useEffect`，则子直接调用就类似事件的处理

总结：编写REACT的父子组件通信时，尽量从**状态 => 结果**的角度思考，建议的思考顺序：

1. 遵循自上而下单向的数据流，父子通信也尽量不要违背这个原则
2. 为什么需要父子通信，它用来解决什么问题
3. 父子通信后，什么状态变化了
4. 如何把状态变化体现在UI或者副作用函数上



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



#### `useEffect`--追加阅读

[这篇文章](https://overreacted.io/a-complete-guide-to-useeffect/#dont-lie-to-react-about-dependencies)解释得很好。

里面的关键点这里记录一下：

- 每次渲染，组件内的状态，组件内的函数，函数内引用的组件内变量，其实都是不可变的，即使函数是异步的，在回调开始前组件进入了下一轮渲染，上一轮的函数内使用的组件内变量依然还是上一轮的，不会串台到下一轮，用作者的话来说，**函数只能看见它所在这轮渲染创造出的变量，看不到上一轮也看不到下一轮**
- `useEffect`内的副作用函数也是函数，因此也受制于这个规则，**副作用函数和它的清理函数也只能看见它所在这轮渲染创造出的变量**
- 原则1：应该把依赖的部分写完整，不然会导致副作用函数的基础功能不正常
- 原则2：在不违反原则1的前提下，可以**对副作用函数本身进行优化，使得它需要的依赖变少，但是也同样可以完成任务**
- 原则2的核心，`const [state, dispatch] = useReducer(reducerFn, initialState)`，注意，**`dispatch`也是一个不会变化的状态修改函数，因此可以让`useEffect`直接依赖它，以实现在副作用内修改组件状态，但同时保持副作用函数不重开**

这篇文章之所以重要，是因为它解决了最常见的一个开发场景：

1. 使用`useEffect`获取API数据
2. 在获取数据后，依然在`useEffect`内，修改组件状态以把数据展示到UI上
3. 因为在`useEffect`内修改了状态，所以理论上需要把这些状态添加到依赖内，但实际上业务层不需要，所以可以用`dispatch`来防止在`useEffect`内修改状态的行为会打乱副作用函数

以下给出一个定时器的例子，它不间断地修改一个COUNTER，但是每次修改不会触发定时器重新启动，而且还支持在保持定时器的同时修改其他状态以更改COUNTER的计数规则：

```jsx
import { useReducer, useEffect } from 'react';

function loopReducer(curState, payload) {
  switch (payload.action) {
    case 'default': {
      return { ...curState, counter: curState.counter + curState.step }
    }
    case 'change_step': {
      return { ...curState, step: payload.step };
    }
    case 'modify_counter': {
      return { ...curState, counter: payload.counter }
    }
  }
}
export default function UseEffectDemo3() {
  const [state, dispatch] = useReducer(loopReducer, { step: 1, counter: 0 });
  useEffect(() => {
    console.log('begin loop');
    const loop = setInterval(() => {
      dispatch({
        action: 'default',
      });
    }, 1000);
    return () => clearInterval(loop);
  }, [dispatch]);

  function resetCounter() {
    dispatch({ action: 'modify_counter', counter: 0 });
  }

  function changeStep() {
    const step = state.step;
    dispatch({ action: 'change_step', step: 0 - step });
  }

  function bumpStep() {
    const step = state.step;
    const newStep = step > 0 ? step + 1 : step - 1;
    dispatch({ action: 'change_step', step: newStep });
  }

  function resetStep() {
    const step = state.step;
    const newStep = step > 0 ? 1 : -1;
    dispatch({ action: 'change_step', step: newStep });
  }

  return (
    <>
      <h4 className="text-center">counter is {state.counter}</h4>
      <div className="flex justify-center">
        <MyButton text="reset counter" clickHandler={resetCounter} />
        <MyButton text="reverse speed" extraClass="ml-2" clickHandler={changeStep} />
        <MyButton text="speed up" extraClass="ml-2" clickHandler={bumpStep} />
        <MyButton text="reset speed" extraClass="ml-2" clickHandler={resetStep} />
      </div>
    </>
  )
}


function MyButton({ text, clickHandler, extraClass }) {
  return (
    <button className={`border-2 px-2 py-0.5 hover:cursor-pointer ${extraClass}`} onClick={clickHandler}>
      {text}
    </button>
  );
}
```



#### `useEffect`--常见设计范式

- 计算属性不需要使用它
- 事件交互不需要使用它
- 重置所有状态，要么通过`useReducer`手动实现，要么修改子组件的`key`
- 一些数据请求应该通过事件交互而非自动执行
- 首次的数据请求可以放到`useEffect`内
- 后续的数据请求如果依赖事件或者状态变化，则需要处理好debounce（防抖）或throttle（节流），并做好在多个请求同时发生时，如何避免返回数据的乱序问题（通常称为race condition）
- 全局初始化一次的业务逻辑，可以直接在根组件外面执行对应逻辑，但如果逻辑必须在组件内完整，则可以在根组件外存储一个是否初始化标记，并且在执行初始化时判断一下此标记，以避免严格模式下渲染2次的问题
- 如果一个副作用里面涉及到使用某个状态，但是又不涉及修改这个状态，则可以使用`useRef`来手动同步这个状态，并把ref加入依赖，由于ref本身是不会变的因此不会导致副作用被打断后重开，另外官方有一个实验性的API，`useEffectEvent`来解决这个问题，它的底层就是用`useRef`



#### 副作用函数的执行时机和组件生命周期的关系

任何REACT组件的生命周期都是一样的：

- 首次渲染后挂载DOM
- 重新渲染后修改DOM
- 销毁时也从DOM卸下

**`useEffect`副作用函数的执行时机严格来说和组件的渲染是不同步的**，即使组件销毁了，只要不执行清理函数，它还是可以执行的（比如它是一个定时器或者轮询）**。由于`useEffect`的触发和清理的执行时机是基于依赖数组的变动，所以可能的情况是这样：

- 没有依赖数组，每次组件重新渲染后都会执行
- 有依赖数组，组件重新渲染后，依赖可能会变也可能不会变，因此组件重新渲染后，副作用函数可能不会重新执行
- 只有空的依赖数组，只会在首次渲染后执行一次

注意依赖数组，它其实可以依赖组件内的临时变量，如果**临时变量是基于随机数产生，或者基于外部prop的计算属性，那么临时变量也是具有可变性的，因此也会影响副作用函数的执行和清理**。

所以完整的REACT和副作用函数的执行顺序是这样的：

- 首次渲染后挂载DOM，然后执行副作用函数
- 重新渲染后修改DOM，然后**判断副作用函数的依赖有没有变化，如果变化了，执行上一个清理函数，然后执行下一个副作用函数**
- 组件销毁时，从DOM卸下，然后执行副作用函数的上一个清理函数

注意**副作用函数的重新触发时机一定是在组件渲染之后**，所以理论上我们可以让它依赖一个全局变量，然后在组件内修改这个全局变量，但是**由于修改全局变量不会触发组件的重新渲染，因此也不会给副作用函数的另一次触发的机会**。



#### 自定义HOOKS

自定义HOOKS的本质是希望HOOKS可以复用。

本身HOOK就是组件内部的状态，只能在组件内部声明，但是如果有多个组件需要同一套HOOK（注意，不是状态，而是HOOK，要管理副作用的），那么可以考虑把多个副作用整合到一个自定义的副作用函数内，然后使用这个函数作为一个新的副作用函数，举例：

```jsx
'use client';

import { useEffect } from "react";

// 需要在组件挂载时记录一下
function useMountLog(cpnName) {
  useEffect(() => {
    console.log(`${cpnName} mounted`);
  }, []);
}

// 注册window的resize事件
function useWindowEvent(cpnName) {
  useEffect(() => {
    window.addEventListener('resize', () => {
      console.log('window resized', cpnName);
    });
    return () => {
      console.log('remove resize event', cpnName);
      window.removeEventListener('resize');
    };
  }, []);
}

export default function CustomHookDemo() {
  return (
    <>
      <ChildCpn key="1" cpnName="ChildCpn1" />
      <ChildCpn key="2" cpnName="ChildCpn2" />
    </>
  );
}

function ChildCpn({ cpnName }) {
  useMountLog(cpnName);
  useWindowEvent(cpnName);
  return (
    <h4>child cpn</h4>
  );
}
```

上述代码给子组件使用了2个自定义HOOKS（实际上可以写到一起），然后在父组件内声明了2个这样的子组件，效果是，每个子组件分别都执行了对应的副作用代码，**即各自输出了挂载信息，并各自绑定了window事件**。

这里可以给出自定义HOOK的语法规范：

- 函数名称使用`useFoo`，这是REACT的建议，表示它是一个HOOK，**use后面第一个字母必须大写，之后采用驼峰写法**
- 可以接收入参或者没有入参
- **内部必须引入其他的HOOK来管理副作用，HOOK的调用顺序和条件，和直接在组件内使用一样，必须是无条件的放在函数内部开头**，由于自定义HOOK可以使用其他自定义HOOK，因此**本质上所有的HOOK都需要在函数内部开头无条件使用**
- 可以有返回值或者没有返回值，可以返回一个或者多个值（以数组或者对象的形式）

注意到上述代码，2个组件都在window全局对象上添加了事件，说明**自定义HOOKS内的状态和副作用都是单独的，而非全局的**，比如自定义HOOKS内使用了`useState`，则不同的组件使用这个HOOK会获取到不同的状态，即使它们的初始值是一样的。

如果我们需要只有一个组件添加全局事件成功，则可以这样处理：

- 把自定义HOOKS写到一个单独的文件内，并在函数外侧声明一个悲观锁用来控制，只有第一个执行函数的组件可以注册成功，之后加锁，后续组件就无法注册
- 另一个方案，把是否执行的逻辑交给开发者，比如在自定义HOOKS里面加一个入参，是否绑定全局事件，开发者可以声明多个组件，并且只在一个组件内传入true，其他组件由开发者控制传入false，这个方案需要开发者内部协调一致

通常来说，像涉及`online`，`offline`之类的事件，可以交给各个组件独立去注册，因为各个组件在实现的时候可以有不同的处理和展示，但是需要各个组件在清理时只清理它自身注册的handler，而不是全部清除。

自定义HOOKS必须和一般的组件一样，尽量避免给整个系统造成副作用，如果产生了副作用，及时做好清理。

只有当副作用业务足够频繁，而且在多个组件内都会用到时，才建议考虑使用自定义HOOK，只执行一次的，简单的，不需要跨组件使用的，不需要抽象为自定义HOOK。

另一个自定义HOOK的例子：

```js
import { useState, useEffect } from 'react';

export default function useCounter() {
  const [count, setCount] = useState(0);
  useEffect(() => {
    const id = setInterval(() => {
      setCount(c => c + 1);
    }, 1000);
    return () => clearInterval(id);
  }, []);
  return count;
}
```



#### 其他常用HOOKS

`useCallback`，允许开发者缓存一个函数，并指明一个依赖，当依赖没有变化时，函数不会被重新创造，而是返回之前的：

```javascript
const cachedFn = useCallback((args) => {
  // function body
}, [dependency]);
```

建议的使用场景：

1. 性能优化，因为默认的组件内函数是每次渲染时重新创建的，如果可以缓存，会提升组件的渲染性能，考虑这样一个场景，如果有一个组件内部包含了5个函数，而这个组件是一个列表的子组件，而这个列表可能有100个这样的子组件，**如果缓存了每个列表元素（子组件）的这5个函数，对渲染整个列表会有明显的性能提升**
2. 组件内逻辑复用的同时，减少`useEffect`的依赖，**增加依赖的层级，以避免所有的依赖直接作用于`useEffect`，**比如下面的代码：

```js
function SearchResults() {
  const [query, setQuery] = useState('default_query');

  // 创建一个基于query状态拼接不同URL的函数
  const getFetchUrl = useCallback(() => {
    return 'https://hn.algolia.com/api/v1/search?query=' + query;
  }, [query]);  // 状态变化后函数也会变化
 
  useEffect(() => {
    const url = getFetchUrl();
    // 获取数据并处理
  }, [getFetchUrl]); // 只有当query变化后这个函数才会变化
 
  // ...
}
```

`useCallback`适用于需要频繁渲染的场景，即**重新渲染的频率非常高，使得任何开销都会影响到帧数时，建议使用**。

`useMemo`，如果组件内有大量数据或者复杂计算，但是这个计算结果并非每次都需要执行，因为它只依赖组件的部分状态变化，则可以使用`useMemo`来缓存计算结果，例子如下：

```jsx
const [newTodo, setNewTodo] = useState('');
const visibleTodos = useMemo(() => {
  // Does not re-run unless todos or filter change
  return getFilteredTodos(todos, filter);
}, [todos, filter]);
```

上述代码的写法和`useEffect`很像，但是它是同步执行的，而且只当依赖发生变动后才会重新计算，如果依赖没有变化，则跳过计算，直接返回上次的计算结果。**`useMemo`内部的函数必须是纯函数，不能产生副作用的那种**。

从HOOKS特性分析，它是一个既保存代码逻辑，又保存代码执行结果，同时通过依赖控制重新计算，进而影响重新渲染时机的HOOK。

以下是`useMemo`和`useEffect`的区别：

| 对比项   | useMemo                  | useCallback                             |
| -------- | ------------------------ | --------------------------------------- |
| 使用缓存 | 缓存函数的执行结果       | 缓存函数本身                            |
| 使用依赖 | 依赖变化后会重新计算结果 | 依赖变化后会返回新函数                  |
| 可代替性 | 不可用`useCallback`代替  | `useMemo(() => cachedFn, [dependency])` |

使用`useMemo`返回一个函数，就是模拟了`useCallback`，但是反过来不行。

后续慢慢补充……



#### REACT的设计思想和开发思考

REACT的设计考虑了几个因素：

- 前端行业发展使得JS的重要性大大提高，JS不再是简单的交互脚本，而是负责创建整个页面的重要指挥
- 既然JS是主导地位，而且交互逻辑和渲染逻辑不可分割，那么就应该**把HTML视为JS的渲染语法糖之类的结构**，以JS为核心，兼容HTML
- 组件化是系统设计的最佳范式，JSX应该支持
- 单向的数据流和良好的代码可读性

REACT的核心理念是尽量简单，所以在数据流向的设计上需要单向，从父到子，而不是从子到父的可双向。另外代码要具有高可读性，因此不能搞对象的**mutation**那套。

**在VUE中，声明变量，声明计算属性，然后修改变量，由于变量是响应式的，还要回头去思考计算属性能否执行，最后才是渲染**。思路上不能按照自上而下的顺序阅读代码，要经常考虑副作用，从而使得**一旦忽略这个副作用，当代码规模增长后，预测某处修改是否会引发副作用将变得非常困难**。

REACT不存在此类问题，HOOKS都是在渲染完成后才执行的，整体的逻辑是组件的状态不可变性，**因此所有涉及计算属性的部分，都是在它声明的时候就已经要进行计算了，因为它是不可变的，只有声明然后赋值这一个环节**。所有的计算完成后返回一个JSX，阅读REACT的代码不需要经常返回到上文去推演。

使用REACT的时候，时刻注意状态变化，**把一个交互看作是一个连续的状态变化，以最小变化间隔进行思考，考虑什么变化了，什么没有变化，变化如何体现在UI上**。





## NEXTJS学习笔记

这里进入NEXTJS的学习，参考[官网教程](https://nextjs.org/learn/dashboard-app)，另外还要结合油管的教程，由于REACT已经支持了SERVER COMPONENT，之前学习的REACT部分知识点可能需要更新，还可能加入一些WEB3的开发场景用例。



#### 初始化项目

工作空间内执行以下代码：

```powershell
npx create-next-app@latest nextjs-dashboard --example "https://github.com/vercel/next-learn/tree/main/dashboard/starter-example"
```

会安装一个NEXTJS的模板项目，以这个为前提开始学习。

严格模式从NEXTJS的13.5版本之后自动启用。

执行`npm run dev`看效果。

VSCODE安装一个**alias-tool**插件，参考它的配置，可以支持在TSX内引入CSS时，按住CTRL + 鼠标左键点击可以跳转到对应的CSS文件。

TAILWINDCSS兼容性相关配置（以VS CODE为例）：

安装**PostCSS Language Support(csstools)**插件，然后配置：

```
"files.associations": {
    ".css": "postcss"
},
"css.validate": false
```

这样配置就默认所有CSS都会被TAILWINDCSS侵入，使用它的规则和语法，比如`@apply`指令。



#### 几个关键文件

NEXTJS的根目录里有几个文件夹的名字是固定的，不可更改，比如：

- app，这个是APP ROUTER的根路径，用于存放APP ROUTER模式下的页面

- pages，这个是PAGES ROUTER（简单理解就是传统的SPA）模式的根路径，里面存放的都必须是传统的在客户端执行的REACT页面和组件
- public，这个是构建后放在网站根目录的文件和文件夹，比如`favicon.ico`就可以放在这里
- src，它有点特殊，从一般意义上它表示存放源码的文件夹，可以不创建，不使用，**名字通常可以修改，但是如果根节点没有app和pages文件夹，且src里面有时，这个src文件夹的名字就不能改了，它就表示存放项目源码的文件夹**

app里面的pages.tsx和layout.tsx是根节点组件，layout包裹page，看源码可以发现**layout就是一个布局组件，开放一个children注入，而page里面的默认导出组件就会被注入到layout的children内**。



#### TAILWINDCSS V3基本配置

注意看这个`global.css`文件，它里面有以下引入：

```css
@tailwind base;
@tailwind components;
@tailwind utilities;
```

这个`@tailwind`是一个指令，在V4版本已经移除。



#### 用TAILWINDCSS实现一个三角形

```html
<div className="border-l-[15px] border-r-[15px] border-b-[26px] border-l-transparent border-r-transparent border-b-black"></div>
```

`border-l-[15px]`表示左侧是15px宽，这个宽度可以自己写。

`border-l-transparent`表示左侧边框透明。

上述代码的意思是，构建一个盒子，左侧右侧边框15PX且透明，底部边框26PX且颜色是黑色。



#### 抽离可复用的样式

CSS本身支持模块化，比如声明一个`foo.module.css`，必须以`.module.css`结尾，然后在REACT组件内使用，这个就是一个模块，但是TAILWINDCSS不建议这样做，因为**它会破坏TAILWINDCSS对DOM样式的感知**，更好的策略是直接写在`global.css`内，然后团队成员做好区分就可以，比如上述的三角形：

```css
.triangle-normal-black {
  @apply border-l-[15px] border-r-[15px] border-b-[26px] border-l-transparent border-r-transparent border-b-black;
}
```

其实就是声明了一个CSS样式，加上了`@apply`以套用TAILWINDCSS的语法，然后直接在组件内使用：

```html
<div className="triangle-normal-black"></div>
```

后续可以复用的样式都可以这样写，团队协作时做好沟通和业务分离就可以。



####  引入CLSX

CLSX用于处理条件渲染样式的问题，原生的REACT面对这个问题是这样处理的：

```jsx
<button className={ isActive ? 'bg-blue-500 text-white' : 'bg-gray-100 text-black' }>
  Click me
</button>
```

而CLSX是这样处理的：

```jsx
import clsx from 'clsx';

<button className={clsx(
  'px-4 py-2 rounded', 
  isActive ? 'bg-blue-500 text-white' : 'bg-gray-100 text-black',
  isDisabled && 'opacity-50 cursor-not-allowed'
)}>
  Click me
</button>
```

相比原生REACT来说有一些优化，首先它内部支持通过逗号来声明多个样式，如果是原生REACT，要支持一部分公用一部分条件渲染，则必须写为：

```jsx
className={`common-style ${condition ? 'style-a' : 'style-b'}`}
```

需要结合字符串模板，JSX的表达式，以及注意引号位置，而CLSX对此进行了简化：

```jsx
className={clsx('common-style', condition ? 'style-a' : 'style-b', condition && 'style-c')}
```

相比原生REACT写法，更加接近原生的CSS，同时兼顾了条件渲染，因此结合TAILWINDCSS推荐使用，参考[文档](https://github.com/lukeed/clsx)可以看到，这个工具非常灵活，可以支持多个风格混合使用。

结合上述的抽离可复用的样式，可以这样写：

```tsx
<div className={clsx("relative w-0 h-0", true && "triangle-normal-black")}></div>
```

推荐使用CLSX，可以大大简化样式的操作难度。



#### 添加自定义字体

NEXTJS支持开发者从一些预定义的库里面去下载字体，然后直接打包到最终产品内，这样用户访问页面时可以直接下载最终产品内的字体，而不用去第三方库内下载字体，**本质上用户还是要下载字体的**。

NEXTJS里面，使用字体有2种方式：

- 如果要用的是谷歌字体，NEXTJS有内建的支持，可以在开发和构建时去谷歌字体库里面下载
- 如果要用其他三方字体，需要自己下载并引入到项目内，然后作为asset打包到最终产品内

这里只考虑使用[谷歌字体](https://fonts.google.com/)的场景，可以自己去浏览选择喜欢的。

选择了字体后，新建一个TS文件，引入字体和配置：

```typescript
import { LXGW_WenKai_Mono_TC } from 'next/font/google';

export const lxgw_wenkai_tc = LXGW_WenKai_Mono_TC({
  weight: '300',
  style: 'normal',
});
```

然后在根节点使用这个配置：

```tsx
import { lxgw_wenkai_tc } from './ui/fonts';

// ......
<body className={clsx(lxgw_wenkai_tc.className, 'antialiased')} >{children}</body>
// ......
```

`antialiased`是一个TAILWINDCSS用于平滑字体的样式，建议加上，之后重新启动项目，编译就会发现效果了。注意去看具体的CSS里面的字体文件路径，可以发现是下载到本地，存放在`.next\static\media`路径内。

注意项目内允许多个字体，使用第二个字体的方法和上述一样，只是需要放在不同层级组件的根路径。



#### 使用图片组件以优化图片展示

NEXTJS通过一个`<Image>`组件可以实现图片的优化，具体来说：

- 一般的网页，图片加载后才会知道其尺寸，然后浏览器再重新计算布局，最简单的例子就是如果你打开一个老的带多图的网页，基本上当需要展示图片的时候，文字的部分会被图片挤到下面，随着图片加载，文字的部分会被越来越挤到下面，导致用户需要频繁滚动到底部才能看到文字，图片组件可以避免这个问题
- 尺寸问题，在移动端展示的图片可以降低一些分辨率以节省带宽
- 图片懒加载，这个应该运用到所有页面上
- 图片格式优化，比如用WEBP代替传统的JPG / PNG，或者在支持的浏览器上使用AVIF

使用NEXTJS的图片组件，注意图片一般都放在PUBLIC路径内，由于PUBLIC是构建后的根路径，因此可以直接写为`/foo.png`：

```tsx
import Image from 'next/image';

<Image src="/hero-desktop.png" width={1000} height={760} className="hidden md:block" alt="dashborad screenshots" />
```

注意NEXTJS组件对所有引入的图片都有必填属性的要求。



#### 路由设计和组件设计（路由的部分后续还要补充，包括shallow）

前端行业自从加入了模块化，工具流后，就在逐步往前端路由的方向走，往客户端独大的方向走，后端纯粹就是API和数据交互，用户交互和路由控制完全放在前端，这个阶段下SPA，WPA非常流行。

但是这种重客户端的演化不是没有代价的：

- 首次渲染时间拉长，因为需要先下载JS代码，然后执行JS代码请求后端数据，最后把后端数据整合到HTML内，如果页面较多，那么JS文件也会偏大，当然前端有一些对应的优化技术，但是解决不了根本问题
- 性能问题，因为完全依赖客户端的计算，在CPU较弱的设备上运行效果会更差
- 对SEO不友好，因为爬虫爬到的并不是直接的包含了具体内容的HTML，而是一个JS脚本文件和一个页面骨架（一般就是一个空的根节点加一个id属性），爬虫只有能具备执行脚本，生成HTML的能力后，才能获取真实页面

所以后面前端行业也开始反思，就是能不能保留SPA的优点（丰富的交互性，后端纯粹作为API提供支持），同时增加传统的服务端渲染的优点，这个思考就催生出了NEXTJS的几个设计：

- 服务端组件和客户端组件
- hydration（注水，水合）技术
- 基于文件系统的路由

NEXTJS里面的组件，默认是服务端组件，但是也可以写客户端组件。服务端组件不能包含具有交互的内容，只能像传统的服务端模板那样，调用数据接口获取数据，然后嵌入到JSX内。

客户端组件则是传统的REACT组件，可以包含所有的交互功能。

服务端端组件负责渲染数据，然后通过`children`属性注入一个可交互的客户端组件（有时候这个很难区分，比如某些页面既要包含数据但是又非常注重交互，这个很挑战开发者的设计和解耦的能力），NEXTJS希望开发者尽量把内容都放到服务端组件内，所以客户端组件应该尽可能地原子化。

当用户通过浏览器请求NEXTJS部署的网站后，服务端组件会在服务端渲染好为HTML后，直接响应给用户，以减少首次渲染时间，并快速输出内容，客户端组件也会作为JS代码发给用户，在用户的浏览器上完成构建，最后整合（水合，注水，hydration）到现有的HTML内，实现了一个完整具有交互性的HTML。**最后还会加上一个前端路由以优化后续跳转的问题**。

后续的页面跳转（这里指的是用户通过和页面元素进行交互的跳转，一般都是点击按钮或者链接，而不是URL输入一个新链接来强制页面渲染），并不是走SPA的老路，也不是走服务端渲染的老路，而是混合路线，简单来说，NEXTJS只允许以下情形：

1. 根节点是服务端组件 + 子节点客户端组件
2. 纯服务端组件
3. 纯客户端组件

如果是情况1（最常见），那么后续请求后，服务端会返回一个RSC（React Server Component，服务端组件）打包JSON，浏览器拿到这个JSON后可以快速渲染出页面结构，此外**这个JSON还包含了需要注入的子客户端组件的位置和对应的构建后的JS代码路径**，REACT解析后，会发起请求去获取这部分子组件的代码，并在客户端的DOM构建后，再注入子组件，所以情况1，本质上还是一个继续注水的操作。

情况2，服务端还是返回RSC的JSON，只是不包含了客户端的代码和位置，因此不需要注水。

情况3，参考传统的SPA的操作，服务端直接返回JS代码，由客户端执行并构建组件。

**NEXTJS禁止由客户端组件作为根节点，包含服务端组件作为子节点的行为，**因为这样会增加系统复杂度，而且NEXTJS服务端不具备执行客户端代码的能力。

**NEXTJS禁止在同一个文件内混合声明服务端组件和客户端组件，一个文件只能全部声明为服务端组件，或者在文件头加上`use client`以支持全部声明为客户端组件**。

NEXTJS的路由也是2套系统，一套叫APP ROUTER，一套叫PAGES ROUTER，前者对应优化后的版本，即服务端渲染+客户端子组件水合的模式，后者对应传统的SPA模式。**虽然理论上2套路由系统可以同时使用（确保不要有路径冲突），但是NEXTJS官方建议不要混合使用，除非你在一个老的SPA项目上希望整合新的APP ROUTER，可以作为一个过渡期的方案**。

PAGES ROUTER这里不做过多介绍，就是在根节点创建一个`pages`文件夹（名称不能修改），然后写组件就可以，页面跳转，状态控制什么的都是老版本的REACT-ROUTER支持。

APP ROUTER直接基于文件系统映射，比如：

- `/home`，就是对应`app/home/page.tsx`
- `/home/about`，`app/home/about/page.tsx`

简单来说所有路径都是基于文件夹的树结构进行映射，根路径，即`/`，必须对应app每个文件夹内提供一个`page.js`或者`page.tsx`作为当前页面的入口，当然这个文件内也可以引入其他模块。

每个`page.tsx`必须默认导出一个组件，组件名称随意：

```tsx
export default function MyFooBarBarzPage() {
  // return jsx here
}
```

注意这个组件按照NEXTJS的支持场景，只能是纯客户端组件或者服务端组件包含或不包含客户端组件。

页面控制的跳转，需要引入`next/link`组件，以避免使用A标签带来的页面强制全局刷新，比如可以通过layout.tsx设置一个所有子页面的共同父页面，然后在它上面加LINK，点击后就可以跳转到不同的子页面，具体例子：

```tsx
import Link from 'next/link';

interface LinkEle {
  name: string;
  href: string;
}

const links: Array<LinkEle> = [
  { name: 'Home', href: '/dashboard'},
  { name: 'Invoices', href: '/dashboard/invoice' },
  { name: 'Customers', href: '/dashboard/customer'},
];

export default function Layout({ children}: { children: React.ReactNode }) {
  return (
    <div className="flex h-screen flex-row md:overflow-hidden px-4 py-4 ">
      <div className="flex flex-col mr-10 w-64">
        <h4>side bar</h4>
        <ul>
          {
            links.map(ele => {
              return (
                <li className="flex grow">
                  <Link className="flex items-center justify-center h-[48px]" key={ele.name} href={ele.href}>
                    <span>{ele.name}</span>
                  </Link>
                </li>
              );
            })
          }
        </ul>
      </div>
      <div className="w-full">{children}</div>
    </div>
  );
}
```

引入LINK组件后，直接使用它，传入href和key，内部注入路由跳转的具体组件，它最后会被渲染为a组件，但是点击后通过观察控制台可以看到，请求的是RSC，而非整个页面。所以说明**LINK组件是一个客户端组件，它启用了事件机制，拦截了默认的行为**。

有了页面跳转还不够，有时候需要在组件内判断当前处于哪个页面，比如最简单的如果构建一个超链接侧栏，那么可以高亮当前所在的页面对应的跳转按钮，使用`usePathname`来实现这个功能，注意它是一个NEXTJS的HOOK，因此使用它的地方必须转为客户端组件，代码用例：

```tsx
'use client';

import Link from 'next/link';
import { usePathname } from 'next/navigation'; // 引入获取当前的页面路径
import clsx from 'clsx';

const links = [
  { name: 'Home', href: '/dashboard'},
  { name: 'Invoices', href: '/dashboard/invoice'},
  { name: 'Customers', href: '/dashboard/customer'},
];

export default function NavLinks() {
  const pathname = usePathname();
  return (
    <>
      {links.map((link) => {
        return (
          <Link
            key={link.name}
            href={link.href}
            className={
              clsx(
                'flex h-[48px] grow items-center justify-center gap-2 rounded-md',
                'bg-gray-50 p-3 text-sm font-medium hover:bg-sky-100 hover:text-blue-600',
                'md:flex-none md:justify-start md:p-2 md:px-3',
                pathname === link.href && 'text-red-600 hover:text-red-600',
              )
            }
          >
            <p className="hidden md:block">{link.name}</p>
          </Link>
        );
      })}
    </>
  );
}
```

`usePathname`拿到的根路径，如果是在首页则是`/`，之后就是一个以`/foo`开头的路径，不会包含QUERY的部分。



#### 客户端路由跳转方案

- 使用link组件，预先写好HREF，用户点击后就可以跳转
- 使用`useRouter`，进行手动控制的跳转
- 如果客户端组件是表单，提交表单后，客户端会预期收到响应码303，即下一个跳转地址，服务端可以通过`redirect`命令控制跳转地址，客户端收到地址后就会跳转



#### 动态文件路由

假设我们的路由具有传参功能，但是不是基于QUERY传参而是希望参数写在URL的PATH里面，可以使用`[param_name]`的方式创建文件夹，比如`[id]`，就表示PATH的某个部分是ID入参。

页面组件获取此属性使用props.params.id获取，因此声明的时候是这样的：

```tsx
export default async function Page(props: { params: Promise<{ id: string }> }) {
  //
}
```





#### `usePathname`参考

| URL                                     | `usePathname()`的返回值 |
| --------------------------------------- | ----------------------- |
| `/about`                                | `/about`                |
| `/about.html`                           | `/about.html`           |
| `/products/shoes`                       | `/products/shoes`       |
| `/search?q=boots`                       | `/search`               |
| `/blog/post-1#comments`                 | `/blog/post-1`          |
| `/user/settings?tab=security#section-1` | `/user/settings`        |

结论：

- 不会包括query和前缀问号符
- 不会包括hash以及它后面的部分
- 上述2个条件都会同时成立



#### 路由分组

一般来说NEXTJS的路由对应文件系统，但有一个特例，就是文件名称带括号的，比如`app/home/(etc)/about`，它实际对应的URL还是`/home/about`，中间的文件夹因为带括号所以不会被列入到URL内。这个其实是路由组，用于管理对应的路由，比如开发者希望按照业务对路由进行文件分组时（用户登录页面，后台管理登录页面等等），但是又不希望这个文件分组体现在URL上时，就可以用`(group_name)`创建一个文件夹进行分组，当然也可以直接在它里面放page.tsx以使得它是一个真实页面，但是不在URL上体现出来。



#### 编写API

还是基于文件系统，在一个文件夹内部创建一个`route.ts`文件，提供HTTP通用的方法：

```typescript
export async function GET() {}
export async function POST() {}
export async function PUT() {}
export async function DELETE() {}
export async function PATCH() {}
```

就可以通过访问对应的路径，构造请求方式和入参，就可以把NEXTJS本身作为一个服务器来使用。

下面给出一个例子：

```tsx
export async function GET() {
  return Response.json({
    message: 'hello world this is a get method',
  });
}
```

假设这个文件的路径是：`/app/api/query/route.ts`，那么访问它的路径就是：`/api/query`。如果访问成功就可以看到响应。



#### 设置数据库并做简单查询

NEXTJS为开发中小全栈型应用提供了连接数据库的功能，从快速开发迭代的角度看这个行为是可以理解的，但是任何上了规模的应用估计都不会用，只会把NEXTJS作为前端服务器。

要使用数据库的功能，又不想本地搭建数据库，则可以使用VERCEL的服务，整体流程是：

1. 把NEXTJS项目代码上传到GITHUB
2. 注册一个VERCEL账号，关联此GITHUB代码仓
3. 在VERCEL上部署此NEXTJS项目
4. 在此VERCEL关联的GITHUB项目上开启一个存储，选择NEON，它使用的是POSTGRES数据库，创建好后复制一下连接信息，包括地址，用户名，密码等等
5. 连接数据库，写入初始化数据，之后开始正常的CRUD操作

如果要观察数据库的数据，可以直接在VERCEL管理台查看。

连接数据库和进行查询的简单例子：

```tsx
import postgres from 'postgres'; // 这个库已经做了防SQL注入

const sql = postgres(process.env.POSTGRES_URL!, { ssl: 'require' });

async function listInvoices() {
	const data = await sql`
    SELECT invoices.amount, customers.name
    FROM invoices
    JOIN customers ON invoices.customer_id = customers.id
    WHERE invoices.amount = 666;
  `;
	return data;
}

export async function GET() {
  try {
    return Response.json(await listInvoices());
  } catch (error) {
    return Response.json({ error }, { status: 500 });
  }
}
```

注意上述代码获取的`sql`实际上是一个函数，后续的调用方法是JS语法内的TAGGED TEMPLATE LIERALS写法，是合法的，这里的`sql`变量相当于一个连接对象，**NEXTJS在这方面也参考了后端语言的设计，也有连接池的库，如果需要考虑性能优化是可以用的**。



#### 服务端组件的数据查询和渲染

NEXTJS本身是一个服务端的前端，它处理数据获取的方式，和一般的SPA不一样，一般的SPA，只能通过后端提供的API来获取数据，而且如果涉及到安全，要么需要用户进行验证，比如登录以获得授权的SESSION_ID，要么就是需要在请求信息内封装一些验证密钥，但是这种方式就很容易暴露，除非这个API是公用的，可以直接开放并且在后端搭建各种DDOS防御。

而NEXTJS本身作为服务端，就可以代替传统意义上后端的作用了，它查询数据可以这样：

- 还是之前的方案，直接返回API地址给客户端组件，让客户端和API直接对接
- 作为客户端的中转，要么自身去连接数据库查询，要么自身去链接API，NEXTJS可以创建服务端组件，使用这些组件进行数据查询
- 安全问题，如果是封闭的API，依然可以采用登录验证，如果是开放的API，NEXTJS自身也可以部署防止DDOS的组件以进行某些拦截，最大的好处是，**不管哪种方式，NEXTJS都不会开放真正连接所需的密码密钥给用户**

NEXTJS使用服务端组件进行查询的例子，注意服务端组件不能使用`useEffect`所以回归到传统的`async / await`机制下：

```tsx
async function getData() {
  const res = await fetch('https://api.example.com/data', { cache: 'no-store' });
  return res.json();
}

export default async function Page() { // 服务端组件可以用async修饰
  const data = await getData(); // 内部的代码逻辑更清晰，直接await，相当于先查询，再渲染，而不是客户端组件的先渲染，然后副作用查询，然后修改状态
  return <pre>{JSON.stringify(data, null, 2)}</pre>;
}
```

上述代码是一个简化版，但是在实际业务中其实过程都是类似的，而且考虑到REACT的单向数据流，一般都是这样搞：

```jsx
export default async function Page() {
  const [data1, data2, data3] = await promise.all([ // 或者使用Promise.allSettled以允许部分REJECT场景
    fetchData1(),
    fetchData2(),
    fetchData3()
  ]);
  return (
    <>
      <ChildCpn1 data={data1} />
      <ChildCpn2 data={data2} />
      <ChildCpn3 data={data3} />
    </>
  );
}
```

即父组件负责查询所有的数据，通过Promise.all（或者`Promise.allSettled`以允许部分REJECT的场景，因为`Promise.all`只允许所有的都是成功）进行并行查询，全部返回结果后再直接提供给子组件，子组件自身不负责数据查询。



#### 静态渲染和动态渲染入门

NEXTJS的**开发模式，即`next dev`命令，默认都是动态渲染**，即所有数据都是实时查询，之后再渲染为组件的。

NEXTJS的构建和部署，默认是静态渲染模式，即所有数据都是在构建时进行查询和生成的，之后通过`next build`构建为服务端的JS代码（内部实际上就是服务端组件的JS代码，可以让服务器以很低的开销生成HTML，因为直接生成HTML的话还要读取到内存再作为文件流输出，以JS代码保存更方便，而且还要考虑客户端的REACT运行时和水合功能），再通过`next start`来访问。

如何观察当前是静态渲染？很简单，修改数据库，然后刷新一下看页面是否变化，如果没有变化，就是静态渲染，反之就是动态渲染，静态渲染一般也是耗时最少的，理论上只有静态网页文件的传输耗时，没有数据查询的耗时。

对于那些需要传参的查询，NEXTJS默认是可以在构建时对入参进行枚举，以穷举所有的可能性，对于每个可能的值，NEXTJS会生成对应的独立页面，以减少干扰。但是注意，**由于NEXTJS具备依赖入参的查询也可以在构建时生成静态数据的能力，它不会基于代码去推断某个查询需要生成静态页面还是动态渲染**。换言之，**开发者必须手动配置当前页面（注意是页面，不是组件）是否需要静态渲染或动态渲染，以及做好对应的配置以防止运行时报错**。



#### 静态渲染

NEXJS的默认构建策略是静态渲染，这表示如果我们不做任何事情，构建时创建的所有页面就会是静态的。

另外NEXTJS还会采用prefetch技术，针对页面上的链接跳转到的页面预先下载，这样当用户访问后续页面时就不需要后续下载JS了。

这里后续一定要补充`revalidatePath`以及相关的知识点。



#### 串流技术

串流技术一般用于动态渲染的场景，目的是解决一个页面需要查询多个数据并返回多个组件的耗时问题，通常来说我们使用`Promise.all`来并行查询，但是**最终页面的返回取决于耗时最长的那个查询**，在某些场景下这是不友好的。

比如一个设置页面，大部分设置信息都是本地存储或者依赖非常高效的本地数据库，但是涉及网络和会员等的设置，或者运营方的设置，需要请求额外的API，以及更长的耗时，此时，我们可以优先把已经完成的信息返回给用户，耗时更长的信息可以在获取后再串流返回，这个就是串流技术的作用。

简单来说串流技术是这样使用的：

- 基于HTTP1.1及以后的一个协议，叫`Transfer-Encoding: chunked`，这个协议**支持分块传输响应**，一般用于文件下载，但是从REACT18版本开始，NEXTJS也支持了，目的就是用于串流服务端组件，即服务器可以先传输一部分信息（比如预渲染的加载动画或者本地静态渲染的数据），之后再传输另一部分信息（耗时更长的动态渲染数据），在此期间客户端和服务器的连接会一直保持
- NEXTJS服务端先返回一些耗时短的HTML，对于耗时长的组件，先返回一个占位符的HTML，这样客户端可以快速展示
- 之后NEXTJS继续完成耗时长的查询，并返回后续的服务端组件
- 允许在客户端的REACT，接收到这部分信息后，执行简单的替换代码（或许会用到水合，如果服务端返回的是客户端组件），把对应的占位符HTML替换为真实的服务端组件
- **数据查询的逻辑需要迁移到子组件内，因为只有子组件本身负责初始化才能避免同时请求多个数据时耗时最长的那个拖慢整体响应的问题，**否则还是在父组件层级进行等待，其他细节后面会提到
- SEO友好问题，墙内的百度就免谈了，全球来说，这种串流技术对谷歌的爬虫是友好的，因为**谷歌的爬虫可以执行JS，而且只要后续在一个合理耗时内返回动态渲染的组件，那么谷歌的爬虫是愿意等待的，也会在等待后执行JS以拿到真实的页面**，当然如果某个组件耗时太长，不要说SEO了，用户都会提意见

具体的代码编写思路是这样的：

- 在页面层面，可以声明一个`loading.tsx`，用于直接返回结果，NEXTJS的约定配置是，基于文件系统的页面中，如果包含`loadint.tsx`，则优先返回它，之后当耗时的真实渲染完成后，再返回`page.tsx`
- 在组件层面，可以通过`Suspense`组件来实现相同的效果，先直接返回占位的组件，再返回渲染好的真实组件

比如页面层面，这个是NEXTJS自带的功能，基于约定大于配置的原则，声明好了就可以，比如：

```tsx
import DashboardSkeleton from '@/app/ui/skeletons';

export default function Loading() {
  return <DashboardSkeleton />;
}
```

默认的规则是，如果`page.tsx`是需要异步的，则先展示loading再展示page，反之如果`page.tsx`是同步的，直接展示page。此外，NEXTJS规定**这个`loading.tsx`必须只能执行同步代码，只能是同步的立刻渲染**。

要验证也很简单，在page里面加一段延迟渲染的wait代码即可，可以发现优先展示了骨架图，之后再展示page。打开调试工具也可以看到首个page请求的瀑布流耗时持续到了最后，请求头里面有`Transfer-Encoding:chunked`。

组件的单个串流，父组件的部分这样写：

```jsx
import { Suspense } from 'react'; 
import { RevenueChartSkeleton } from '@/app/ui/skeletons';
import RevenueChart from '@/app/ui/dashboard/revenue-chart';

export default function MyPage() {
  return (
    <Suspense fallback={<RevenueChartSkeleton />}>
      <RevenueChart />
    </Suspense>
  );
}
```

至于子组件，内部要写请求数据的逻辑，因此要用`async / await`语法。

总结：

- 父组件用Suspense包裹子组件，并提供子组件渲染完成之前的效果
- **父组件内不要写数据请求逻辑，转移到子组件内完成，确保子组件需要耗时来渲染**
- 子组件渲染完成并返回后，服务端会把这部分响应给客户端，之后客户端的REACT会负责把这部分替换掉之前的渲染部分



#### 局部预渲染（Partial Prerendering）【待补充】

这个功能目前还是实验性质的，但是后续很有可能会列入正式版，因此官方教程先建议开发者要掌握。

由于是实验性质功能，因此需要把当前的稳定版NEXTJS切换为canary版本，切换命令如下：

```
npm install next@canary
```

这个命令相当于直接把当前的稳定版升级为canary版本。

NEXTJS的稳定版目前只支持基于页面的静态渲染或动态渲染，不支持基于组件的静态或动态，因此如果一个页面大部分是静态的，少部分是动态的，也只能视为动态渲染。

而这个特性会通过`Suspense`来处理静态和动态内容，本质上是页面分层设计，把静态内容抽离到顶层渲染，动态的部分用`Suspense`确保可以直接执行并生成占位DOM，之后还是通过分块传输的机制，和客户端REACT合作把动态的部分传输过去并替换。

要开启这个特性先进行配置：

```ts
import type { NextConfig } from 'next';
 
const nextConfig: NextConfig = {
  experimental: {
    ppr: 'incremental'
  }
};
 
export default nextConfig;
```

之后在涉及的页面（一般是某个根页面，一定要确认是部分可以静态部分必须动态的那个页面）加一个配置：

```ts
export const experimental_ppr = true;
```

这样就可以了。



#### 关键词搜索和分页查询

基本上这2个搜索是任何一个稍微正常一点的网站都会具有的功能。

一般涉及到搜索，如果是SPA模式，会倾向于直接用一个MODEL去保存搜索关键词，但是**这种交互会使得用户的搜索结果无法通过URL分享给其他用户，所以后续的一些应用，包括地图类的，都倾向于直接把搜索信息存入URL内**，NEXTJS也倡导这种做法。

搜索的交互和设计思路：

- 搜索参数完全存放到URL内，因为URL有长度限制，因此搜索参数不宜过多

- 考虑搜索结果需要进行SEO优化，即提供给爬虫，所以服务端直接返回包含搜索结果的渲染好的组件
- 后续搜索应该以客户端组件为主，所以需要返回一部分客户端组件，此时服务器应该转为提供API
- 后续搜索需要同步修改URL以更新搜索参数，但是不能触发页面的完全刷新（NEXTJS的APP ROUTER模式可以做到，但是也有代价，会频繁触发局部渲染和RSC下载），也不触发服务器返回新的服务端组件

上述设计是一个完整的业界认为的最佳实践，它兼顾了用户体验和SEO，因此实现起来难度也更大。

比如获取用户的输入，这个在之前的REACT学习里面已经做过了：

```tsx
<input onChange={e => {handleSearch(e.target.value)}} />

const handleSearch = useCallback((keywords: string) => {
  console.log(keywords);
}, []);
```

之后的流程是，基于用户输入，拼接新的URL，然后触发页面的局部刷新：

- `useSearchParams`获取URL的QUERY部分
- `usePathname`获取URL的非QUERY和HASH部分，NEXTJS因为不建议SPA模式所以路由也不推荐用HASH
- 基于用户输入，进行URL拼接
- 最后用`useRouter`获取路由对象，调用`router.replace(newURL)`跳转页面就可以，这个跳转和LINK跳转本质上是一样的，都是会在有LAYOUT组件的前提下，进行局部刷新

PAGE组件也支持传参，比如可以接收`searchParams`，它就表示当然URL的QUERY传参，当然写法是这样的：

```tsx
export default async function Page(props: {
  searchParams?: Promise<{query?: string; page?: string;}>
}) {
  // 省略
}
```

注意上述代码，**如果PAGE组件是async，所以searchParams的类型是PROMISE，反之，如果PAGE是同步组件，则不需要这个PROMISE**。

分页查询也是类似的逻辑，需要先从数据库或者API返回总数量（最好是基于列表查询一并返回），然后基于每页展示数量计算页数，最后构造一个分页控件，点击后触发LINK的跳转效果，分解是这样：

1. 基于当前查询条件拿到总记录数
2. 基于总记录数和总分页数计算出总页数和页码，这个纯计算过程可以放到分页组件内
3. 生成分页UI，设置按钮的点击跳转功能，或者用LINK控件配置HREF，HREF需要基于当前的查询条件和页码生成，比如当前是page2，那么上一页的页码就是1，需要把1加入到HREF内，下一页就是3，依此类推

具体实践中，**允许服务端组件把内部值传入到它的子客户端组件**，这个在某些场景下是有效的，比如列表查询的条件，可以直接传递给分页组件，这样分页组件就不再需要直接从URL内解析出查询条件和获取页码（虽然让分页组件再写一遍会使得它有更高的独立性）。



#### 数据操作（CRUD里面的C和U和D）和SERVER ACTION

传统的SPA里面是这样操作数据的：

- 使用状态模型或者其他方式获取表单的数据
- 阻止表单的默认行为，把表单数据通过请求工具，直接提交到后端API上
- 基于后端的响应，进行对应的UI处理

而NEXJS是一个全栈式的框架，因此它不能把接收到数据后的行为外包给后端去做，必须它自己做，所以使用了叫做SERVER ACTION的技术，设计如下：

- 声明一个表单组件，可以是服务端的，也可以是客户端的，考虑到后续的表单校验和异常处理，用客户端组件更好
- 服务端生成表单对应的SERVER ACTION函数
- 把表单组件的ACTION属性绑定到这个ACTION函数上
- 客户端执行表单提交时，请求就会被这个ACTION函数拦截，并执行对应业务逻辑
- 一般的页面跳转可以由服务端控制，表单校验，异常处理，可以在客户端完成，但需要服务端配合返回对应错误信息而非直接抛出一个异常

原理如下：

- 不管表单组件是服务端还是客户端，最后它的action属性不会带有一个URL，顶多是一个兼容性的JS代码
- 提交的时候客户端的REACT会拦截表单的默认行为并把表单数据发送给服务端
- 表单数据里带了一个隐藏的name是`FOOXXXX_ACTION_ID_BARXXXX`的字段，没有值
- 服务端根据这个值来定位具体的ACTION函数，执行并给出响应
- 如果服务端的响应不包含REDIRECT的部分，则客户端只会局部刷新当前页面，如果包含了REDIRECT的部分，就会返回303响应码，这样客户端可以执行类似LINK跳转，如果返回了FORM STATE，且表单是客户端组件，则可以进行各种UI操作，比如给出服务端的表单校验结果等等

原理是表单在HTML内，就是一个空的`action`，类型是POST，然后表单里面加了一个隐藏字段，名称就是对应服务端的这个ACTION，在提交数据的时候服务端可以解析出这个隐藏字段（它的name属性就是它的值），基于字段找到对应的ACTION并处理。

好处是，假设当前的表单是客户端的，它需要下载和执行额外JS以保证前端的表单校验，但是**即使这部分JS由于网络原因没有下载完成，表单还是可以直接提交给后端的**（因为对应的ACTION_ID已经放在提交数据内了，只要确保后端做了校验即可），在一些弱网场景下表现会更好。

ACTION函数的默认处理就是执行CUD操作，之后的处理有几种：

- 默认不处理，就是只操作CUD，之后不管，此时客户端会重新局部刷新当前页面，如果页面初始化时有数据查询，那么应该会体现出修改后的结果
- 通过`redirect`进行处理，这个逻辑的核心是，服务端的响应是包含一个303状态码的，这样客户端就知道要跳转到的下一个页面是什么了
- 直接抛出异常（不推荐），此时会进入到最近的ERROR.TSX页面，这个过程是客户端REACT的路由控制的，它知道怎么处理错误的响应，当然也可以交给客户端组件处理，由此引出了一个问题， SERVER ACTION只能是服务端编写，但是表单组件可以是客户端组件，而且也可以用到SERVER ACTION的功能。
- 如果需要服务端校验表单并处理异常，则服务端不能直接抛出异常，而是应该给出一个表单提交结果对象，也叫FormState，服务端必须要在SERVER ACTION内返回这个FORM STATE，这样客户端表单组件才可以用这个来处理
- 另外即使是客户端表单组件，也可以接收服务端的redirect响应并做跳转，即它的表现和服务端渲染出来的表单控件是一样的

所以得出表单操作的最佳实践，以及NEXTJS的开发最佳实践：

- 按照业务划分，每个开发应该都要负责全栈，即前后端，不同的开发应该负责不同的业务
- 表单操作是SERVER ACTION配合客户端表单组件，因为总是需要前端校验的，而全栈开发也需要负责后端校验，有些校验代码甚至可以复用
- 开发在写前端校验和表单控件时，主要负责UI交互的功能，不涉及页面跳转，开发在写后端表单校验时，应该通过REDIRECT控制页面跳转，这样一来既保证了表单的灵活性，又保证了路由的安全性

代码用例简化版：

```tsx
// 声明一个action函数，必须是async的
export async function createItemAction(formData: FormData) {
  const name = formData.get('name');
  const age = formData.get('age');
  updateUser(name, age);
}

// 表单组件内部的form，关联一下action属性，默认的提交类型也会改为POST
export function MyForm() {
  return (
    <form action={createItemAction}>
      {/* 具体的表单JSX */}
    </form>
  )
}
```

SERVER ACTION必须是ASYNC，因为**NEXTJS需要把这个响应任务加入到异步队列里面**，如果它是同步执行的，即使在业务上可行，也会因为NEXTJS无法把它加入异步队列，**导致NEXTJS没办法给出后续的响应**。

表单编辑过程中的ID问题，假设现在要编辑一个表单，首先可以写一个SQL把对应的记录基于ID查询出来，ID可以是UUID也可以是自然数，但是如何保证我们提交表单的时候，用户不能篡改ID呢？即使我们把ID作为隐藏元素，用户通过开发模式还是可以找到这个表单的（和ACTION_ID一样都可以在控制台看到），所以NEXTJS提供了一个方案，就是表单本身不包含ID信息，提交之前用REACT运行时进行拦截，并让REACT管理这部分信息，把ID添加上，写法如下：

```jsx
'use server';
export function myUpdateAction(id, formData) {
    
}

export function MyForm({data}) {
  const id = data.id;
  const myUpdateActionWithId = myUpdateAction.bind(null, id); // 这里把server action的2个入参通过bind传参改为只需要1个入参的server action
  return (
    <form action={myUpdateActionWithId}>
    </form>
  )
}
```

表单的`action`属性只能接收经过`use server`标记的SERVER ACTION，换言之直接声明一个匿名函数并传入到表单内是不被允许的，NEXTJS需要在编译期就获取到对应的SERVER ACTION。**BIND虽然也是动态创建函数，但是它保留了对原SERVER ACTION的引用，因此NEXTJS可以基于这个动态创建的函数追踪到原本的SERVER ACTION函数**。

删除也是一样，由于FORM ACTION的唯一入参是FORM_DATA，而删除只需要传入ID，因此也需要BIND转换一下。



#### 补充--SERVER ACTION的规定和限制

为什么要通过`use server`注释来使得函数变为SERVER ACTION？因为NEXTJS需要给对应的函数进行标记，所以它必须是：

- 一个非动态生成的函数，需要在解析阶段就能知道它的存在，而非在运行时才能知道它的存在
- 带名字的函数，需要通过名字辅助定位
- 它所在的模块是用`use server`修饰的

所以这就解释了为什么匿名函数不能做SERVER ACTION，因为它没有名字，而且最重要的，它是在运行时期创建的，因此NEXTJS无法在编译期感知到它，它也不是来源于一个`use server`修饰的模块。

那么用`bind`修饰一个SERVER ACTION为什么不会破坏它呢？因为BIND实际上是创造一个对原函数的引用，虽然新的函数也是在运行时期创建的，**但它引用的那个函数是SERVER ACTION，所以NEXTJS是可以追踪的**。

**当通过一个ACTION={SERVER_ACTION}进行绑定时，NEXTJS只会传入一个唯一的入参，就是FORM_DATA**，换言之如果我们声明的SERVER ACTION的首个入参不是FORM_DATA，而是ID或者其他入参，那么本质上都要通过BIND来确保它的首个入参是FORM_DATA或者它所需的入参都已经满足，即使不依赖FORM_DATA也没有关系。



#### 异常处理

涉及到服务端的操作，如果有可能产生运行时异常的，一般来说用TRY--CATCH处理更好。

还有一种处理是直接声明一个`error.tsx`文件，注意这个文件是可以存在多个的，它会按照最近的路由的处理，比如当前的路由是`/a/b`，里面有一个page.tsx，那么如果加一个error.tsx，当在这个路由发生错误且没有做好处理时，就会展示当前路由内的error.tsx，如果当前路由没有，会继续向上查找，直到最后返回一个全局的error.tsx。

error.tsx里面的组件必须是客户端组件，当然一般来说如果不想细粒度的处理异常，又希望异常可以被用户感知到，则可以采用这个方案。（一般是新闻博客类网站会采用这种设计）

一个常见的error.tsx写法：

```tsx
'use client'; // 必须是客户端组件
 
import { useEffect } from 'react';
 
export default function Error({
  error,
  reset,
}: {
  error: Error & { digest?: string };
  reset: () => void;
}) {
  useEffect(() => {
    // Optionally log the error to an error reporting service
    console.error(error);
  }, [error]);
 
  return (
    <main className="flex h-full flex-col items-center justify-center">
      <h2 className="text-center">Something went wrong!</h2>
      <button
        className="mt-4 rounded-md bg-blue-500 px-4 py-2 text-sm text-white transition-colors hover:bg-blue-400"
        onClick={
          // Attempt to recover by trying to re-render the invoices route
          () => reset()
        }
      >
        Try again
      </button>
    </main>
  );
}
```

当然，表单的处理一般会用useAction来控制，以便用户可以在不中断的情况下修改提交数据以通过校验。

另外一种处理异常的方式是使用`notFound`，这个和error.tsx一样，使用思路是：

- 类似error.tsx的需要先产生异常或者手动throw异常，notFound也需要先手动调用一次noutFound()
- 类似error.tsx，也要声明一个notFound.tsx文件

是直接在业务代码中使用的，比如：

```tsx
import { notFound } from 'next/navigation';

export default async function Page() {
  if (condition) {
    return normalJSX;
  } else {
    notFound();
  }
}
```

在一个路由内如果同时有noutFound.tsx和error.tsx，则需要开发者去适配，因为notFound必须是手动调用的，假设开发者不触发这个函数，那么只会有error.tsx生效。



#### 表单校验和`useActionState`

表单的客户端校验，有很多三方库，当然最简单的就是required属性，比如：

```tsx
<input
  id="amount"
  name="amount"
  type="number"
  step="0.01"
  placeholder="Enter USD amount"
  required
/>
```

当然更多的时候需要使用服务端校验，服务端把结果返回给客户端，而不是简单的抛出一个错误并展示error.tsx。为此需要用到`useStateAction`，它的使用思路是这样的：

- 表单必须是客户端组件
- 表单引入`useStateAction`，关联服务端的SERVER ACTION
- **服务端修改SERVER ACTION，在其中加上校验的逻辑，如果出现异常，返回一个错误对象**
- **由于表单是客户端组件，在收到错误对象后就会执行类似重新渲染的逻辑，此时开发需要把错误对象内的必要信息展示出来**

这里以表单校验库`zod`为例，它可以定义服务端的校验规则，并且基于表单数据，生成一个校验结果对象，里面可以包含校验结果，以及校验失败的错误信息：

```tsx
export type State = {
  message: string;
  errors?: {
    customerId?: string;
    amount?: string;
    status?: string;
  }
};

const invoiceFormSchema = z.object({
  id: z.string(),
  customerId: z.string({
    invalid_type_error: 'please select a customer'
  }),
  amount: z.coerce.number().gt(0, { message: 'please input a number greater than $0' }),
  status: z.enum(['pending', 'paid'], {
    invalid_type_error: 'please choose an invoice status'
  }),
  date: z.string()
}).omit({id: true, date: true});

function _parseInvoiceFormData(formData: FormData) {
  const _validateStatus = invoiceFormSchema.safeParse({
    customerId: formData.get('customerId'),
    amount: formData.get('amount'),
    status: formData.get('status'),
  });
  if (_validateStatus.success) {
    const amount = _validateStatus.data.amount;
    _validateStatus.data.amount = amount * 100;
  }
  return _validateStatus;
}

export async function createInvoiceAction(prevState: State, formData: FormData): Promise<State> {
  const validateStatus = _parseInvoiceFormData(formData);
  if (!validateStatus.success) {
    return {
      message: 'field validation failed',
      errors: validateStatus.error.flatten().fieldErrors
    } as State;
  }
  const _formData = validateStatus.data;
  const date = new Date().toISOString().split('T')[0];
  await sql`
    INSERT INTO invoices (customer_id, amount, status, date)
    VALUES (${_formData.customerId}, ${_formData.amount}, ${_formData.status}, ${date})
  `;
  _updateAndBackInvoiceList(true);
  return {message: 'ok'};
}
```

TS内需要先定义好State类型，它代表表单校验的结果，这里的设计是通过一个全局的message字段来进行快速判断，当然也可以加其他字段比如pass表示是否通过校验，之后是各个表单字段的单独校验结果，在一个真实的校验环境中，有可能需要同时展示多个表单字段校验失败的结果。

之后在通过z.object创建校验工具的时候，需要把校验错误的信息也设置进去，参考ZOD3的文档进行设置，针对不同的错误场景可以设置不同的提示信息。

最后通过`safeParse`返回校验结果，如果成功，可以拿到data属性，如果失败，可以拿到error属性，把失败和成功的结果都要返回。

SERVER ACTION函数转为返回`Promise<State>`，其中第一个入参需要改为`prevState`，这个是客户端使用`useStateAction`的前提。

上面的部分是服务端的处理，完成后每次提交表单，服务端都会返回一个State类型的结果，之后客户端的组件会重新渲染，我们需要客户端的组件拿到这个State并且基于它的结果展示正确或者错误的信息：

```tsx
import { useActionState } from 'react';
import { createInvoiceAction, type State } from '@/app/lib/actions';

export default function Form({ customers }: { customers: CustomerField[] }) {
  const [state, formAction] = useActionState(createInvoiceAction, {} as State);
  // ... 省略
  return (
    <form action={formAction}>
      <input id="amount" name="amount" type="number" step="0.01" placeholder="Enter USD amount" aria-describedby="amount-error" />
      <div id="amount-error" aria-live="polite" aria-atomic="true">
        {state.errors?.amount && (
          <p className="mt-2 text-sm text-red-500" key={state.errors.customerId}>
            {state.errors.amount}
          </p>
        )}
      </div>
    </formAction>
  )
}
```

useActionState需要2个参数，SERVER ACTION以及初始的结果状态，当然如果希望把校验信息展示出来，或者在用户首次打开表单的时候展示一些提示，则可以把初始状态补充完整。它返回2个值，第一个State就是上一次的表单校验状态（或者也可以理解为当前的表单校验状态），第二个是经过封装的SERVER ACTION，应该把它用于form的action属性内。

之后假设我们提交了表单，服务端校验失败返回了新的State并重新渲染了表单组件，此时state就不再是初始值了，那么我们就可以用它来展示错误信息，上述代码通过aria-describedby这个属性来把表单和错误信息的DOM绑定。

总结useActionState：

- 服务端需要定义异常状态对象，以及把SERVER ACTION改为返回`Promise<State>`
- 客户端需要用useActionState获取当前状态和SERVER ACTION的封装函数，并且基于当前状态里面的错误信息，进行条件渲染



####  开发过程常见错误



##### module-not-found

如果一些很常见的模块，比如fs，net等都无法找到，大概率是在一个服务端组件使用了`use client`写法，比如：

```js
'use client';

import fs from 'fs';
```

因为客户端组件目前还不支持完整的NODEJS服务端运行环境，因此无法这样写。

所以也给出一个习惯，没事别TM瞎写`use client`，默认都用服务端组件先开发，实在无法满足需求了再切到客户端组件。



## TAILWINDCSS查缺补漏



#### 默认尺寸单位是1/4 REM

默认的尺寸单位是1/4 ，即0.25 REM，比如`px-4`表示`padding-left: 1rem; padding-right: 1rem`，这个是TAILWINDCSS的特性。

如果要使用真实PX单位，可以这样写：`px-[4px]`。



#### 响应式设计

基于屏幕尺寸设置断点，当超过某个断点时展示B样式，否则展示A样式，可以设置多个断点，以使得一套APP适配多个尺寸的屏幕，特别是最近还有什么折叠屏之类的设备。

`[screen-scale]:[className] `，比如`md:block`，这个表示在屏幕尺寸是`md`**及以上**的断点上，设置样式是`block`，即块元素。

注意断点相当于最低要求，**只要满足了（相等也可以）就可以**，除非声明了多个断点，此时以最难满足的那个为主要特性，断点有：

| 断点 | 尺寸（PX表示） | 尺寸（REM表示） |
| ---- | -------------- | --------------- |
| sm   | 640            | 40              |
| md   | 768            | 48              |
| lg   | 1024           | 64              |
| xl   | 1280           | 80              |
| 2xl  | 1536           | 96              |

**一般的桌面和移动端设备上，1 REM = 16 PX**，可以通过修改HTML节点的`font-size`，用比例来表示。

注意移动设备的CSS是逻辑像素。

另外这套规则本质上是基于断点来控制的，换言之移动设置的横屏和竖屏模式，也会有不同的CSS像素宽度，因此可以不修改REM和PX的比例，直接基于断点设计响应式页面就好。

一般的响应式控制是这样的：

```
className="hidden md:block"
```

hidden表示`display: none`即默认隐藏，然后当断点是md时，展示为块元素，如果小于md，则不展示。

比如一个只能在移动端展示的样式一般会这样写：

```
className="sm:block md:hiden"
```

即小于md断点等于或高于sm断点时展示为块，等于和超出md时不展示。
