---
title: TypeScript，初次见面
date: 2021-08-03 19:45:38
---
## 为什么用 TS ?

说实话，最开始并没有想把 TS 用到实际项目中来，一来是感觉“类型”会限制 JS 的优势（好吧，就是浪写浪惯了）；二来听闻 TS + Redux 的酸爽滋味，有点望而却步；三来 TS 环境使用的库需要加类型的声明，很多库并不支持，有点担心推进的流畅度 ...

这个时候，就需要有一股无形的力量推你一把。推我的是团队正在日益普及 TS, 我希望推动你的可以是这篇文章 ~

接下来，会有 React + TS 的项目为背景，介绍我在初学 TS 开发项目中遇到的一些问题，希望对你有所帮助。

## 初学者的困惑

**一. 如何优雅的声明类型**

1.  **基础**

不就是比 JS 多了一个类型声明吗？老夫撸起袖子拎起键盘就是一梭子：

```
interface Basic {
  num: number;
  str: string | null;
  bol?: boolean;
}
```

轻轻松松，五种 JS 值类型就声明好了。那数组、函数呢？再来：

```
interface Func {
  func(str: string): void;
}

interface Arr {
  str: string[];
  mixed: Array<string | number>;
  fixedStructure: [string, number];
  basics: Basic[];
}
```

除此之外，竟然还可以定义自己的类型呢，比如常用的回调函数，在声明处需要指定回调函数的类型：

```
event.on('change', function() {});
```

那这个 `on` 方法需要如何声明呢？试试看 `Function`当 cb 函数的类型呢

```
on(type: string, cb: Function): {}
```

然后就恭喜了，你会得到一个 tslint error :

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/473c0103e8c8494c99cd062438fb3f34~tplv-k3u1fbpfcp-zoom-1.image)

庆幸的是，在这个 error 里面它告诉了你应该怎么做：声明一个专用的函数类型就可以了：

```
type Cb = () => void;

on(type: string, cb: Cb);
```

至此，我们的 TS 人生算是起步了

另外，枚举类型也是很常用的，比如声明一个状态机的各个状态：

```
enum Status {
  Draft,
  Published
}

// 也可指定值
enum Status {
  Draft = 'Draft',
  Published = 'Published'
}
```

在使用枚举的时候，常会遇到如何将枚举和原始数据类型相互转换的需求，比如接口请求到的 status 是 `Draft` 字符串，但是代码中声明的 status 是 Enum 类型，如何转换呢？

```
// string to enum
const str = 'Draft';
const status: Status = Status[str];

// enum to string
Status[Status.Published] === 'Published'
```

**2. 糅合**

独立的类型或接口声明看起来似乎并没有那么难，到项目中糅合一下呢？

-   可能会有几十个类型声明；
-   类型声明可能出现在**接口入参出参**中、**React 组件的 Props 和 State** 中、**函数方法**中；
-   当项目到达一定规模，可以抽象出独立的库的时候，类型也需要抽象；
-   ...

你可能遇到各种情况，会打破你对 TS 的掌控。如何是好？

先说我们实践下来的结论：**独立声明**、**就近声明**、**按职责分组**、**杜绝“硬凑”关联**、**有限抽象。**

**- 独立声明**

一个 ts 文件只声明一个类型或者接口，文件名为需要暴露的类型名称，方便检索和管理。

**- 就近声明**

当一个声明没有被外部引用或者依赖时，可以考虑就近放在使用的地方，典型的场景是 React 组件的 Props 和 State 的类型声明。

**- 按职责分组**

在项目中，需要声明类型的可大致分为两类：一类是 model，也就是接口请求相关的，包括入参和出参；另一类是 view，界面渲染相关的。因此，我在 **独立声明** 的基础上，可以类型按照model 和 view 的维度进行分组，相互独立。

那么问题来了，如果是独立的类型声明的话，怎么把 model 的数据应用到 view 呢？ 可能你需要一个 adapter 来做类型的的转换：DTOTypes -> adapter -> ViewTypes, 完成类似于**将接口中的字符串映射成枚举类型**这之类的转换。

**- 杜绝“硬凑”关联**

不要硬凑两个接口或者类型的关系，比如一个接口的创建和更新，可能字段都是一样，区别是一个有 id 另一个没有，于是我们可能就想着写一个类型然后 id 可选就好了。这样是少写了一个类型，但是可能会带来另外一些麻烦，比如带 id 的数据传给了新建的接口，但是 ts 检查不出来。所以，建议不要怕麻烦，直接拆分成 `CreateInputDTO` 和 `UpdateInputDTO`.

**- 有限抽象**

在**杜绝“硬凑”关联**的基础上，我们可以抽象出通用的声明。

基于上述原则，解决了我作为一个初学者在类型声明上的困扰，如有不对的或者更好的建议，欢迎指正~

**3. 万能药膏 any**

不是所有的类型声明都能一马平川的，当遇到确实解决不了的类型报错的时候，`as any` 能带给你不一样的快感，但是不建议使用啊...

**二. 如何引用外部库**

接下来聊聊第三方库在 TS 环境下的使用。

在 JS 中，npm 上有丰富的海量的库帮我们完成日常的编码，可能并不是所有的库都能完全被应用到 TS 中，因为有些缺少类型声明。

比如，在 TS 中使用 `react` , 你会得到这样的一个类型检查错误：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e41022927f714442969f5f0d519d2e84~tplv-k3u1fbpfcp-zoom-1.image)

因为 react 的库中并没有类型声明。

现在比较通用的做法是，能力实现和类型实现独立成两个库，也就是你需要再安装类型声明的库: `@types/react`.

当遇到上述问题的时候，尝试安装一下 `@types/[package]`.

然而，并不是所有的库都有类型声明的实现，也会有很多不支持 TS 的存在，然而又必须得使用这个库的时候该怎么办？

**自己写声明！**

以 `progressbar.js`为例，基本使用方法是：

```
import * as ProgressBar from 'progressbar.js';

new ProgressBar.Circle(this.$progress, {
  strokeWidth: 8,
  trailColor: '#e5e4e5',
  trailWidth: 8,
  easing: 'easeInOut'
});
```

我们需要对库中暴露出的 api 去做声明，对上述例子做个分解：暴露了 Circle 类，Circle 构造函数包含两个参数，一个 HTMLElement，一个 options. OK, come on~

```
// 首先声明一下模块：
declare module 'progressbar.js' {
  // 模块中暴露了 Circle 类
  export class Circle {
    constructor(container: HTMLElement, options: Options);
  }

  // 构造函数的 Options 需要单独声明 
  interface Options {
    easing?: string;
    strokeWidth?: number;
    trailColor?: string;
    trailWidth?: number;
  }
}
```

如此我们便完成了一个简单的声明，当然实际使用中的 API 肯定比上述情况复杂，根据使用情况，用了哪些 API 或者参数，就补充那些的声明即可。

**三. 如何组织一个 TS 项目**

TS 项目的目录组织上，跟 JS 项目一样，补充好 types 的声明就可以了。

需要注意的是，将你希望对外暴露的能力相关的类型声明都暴露出去，不友好的声明会让接入你项目的人非常的痛苦，同时，在 package.json 中需要指定 type 的 path, 比如：`"types": "dist/types/index.d.ts"`

另外，务必加上 `tslint`, 更规范的去用 TS 实现功能，对于入门而言尤为重要。

## TS 带来的改变

接触 TS 一个月的感受上来说，过了磨合期的痛苦，就能慢慢感受到 TS 带来的便利。

比如，有一个类型你记得名字是 ABC，你在 VSCode 中输入 A，然后发现，竟然能找到我的声明，按一下回车，卧槽，自动给你 import 进来了，不用在一个个字的输入 ../../../../，不用算目录层级是否正确了，是不是很爽。

另外，强类型并不是没有好处啊，浪写惯了可能还是会留隐患的，有点约束也好 ...

虽然你每天要多敲很多 `import * as xx from 'xx'`, 但是你的代码也更为可靠了不是。

与君共勉，提前感受下一代 ES 标准，TS 用起来吧~