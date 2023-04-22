---
title: Vue3 + ts 使用总结
date: 2023-02-23 00:24:19
---
![Vue3 + setup + ts 使用总结](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/da056d54340b485a9978d72ac897ec88~tplv-k3u1fbpfcp-zoom-1.image)

# 阅读vue的英文官网

Vue的中文官网质量比起Vue的英文官网，你会发现质量有点差距，不过这种情况很容易理解，因为Vue是服务于全球的开源项目之一。

因此，作为程序员，学好英语是提高生产力的重要因素。

对于开发者来说，掌握英语是很有必要的，因为它是全球通用的编程语言之一。在学习和掌握新技术、阅读文档、与其他程序员交流和协作时，能够流利地使用英语将会使工作更加高效和便捷。

总之，学好英语是程序员必备的技能之一，它将为我们的职业发展和成长带来重要的帮助和机会。

所以程序员的第一生产力还是**英语**，重要的话说三遍，英语，英语，还是英语！

另外不管学什么都要去获取第一手资料，不要看别人啃剩下的东西，直接去看[英文官网](https://link.juejin.cn/?target=https%3A%2F%2Fvuejs.org%2Fguide%2Fessentials%2Fapplication.html%23app-configurations "https://vuejs.org/guide/essentials/application.html#app-configurations")

# vite初始化项目

1.  pnpm create vite

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/323bec716250417cba6cbeca304212ed~tplv-k3u1fbpfcp-zoom-1.image)

2.  选择vue，再选择typescript
2.  启动项目

```
  cd test1
  pnpm install
  pnpm run dev
复制代码
```

这里选择pnpm，pnpm相对npm和yarn做了一些改进，感兴趣的可以去搜下，切换的心智成本也不高。

# vscode的vue代码片段

根据自己的使用习惯，设置vscode的vue代码片段，推荐使用[snippet-generator.app](https://link.juejin.cn/?target=https%3A%2F%2Fsnippet-generator.app "https://snippet-generator.app")

```
"vue3模版": {

"prefix": "vue3",

"body": [

   "<template>",

       " <div class='${1:box}'></div>",

   "</template>",

   " ",

   "<script setup lang='ts'>",

   " import {ref,reactive} from "vue";",

   " ${3}",

   "</script>",

   " ",

   "<style lang="scss" scoped>",

   " .${2:box} {",

   " }",

   "</style>"

],

"description": "vue3模版"

}
复制代码
```

另外vscode的不仅可以设置vue的代码片段，理论上你在vscode上写的任何代码，都可以设置成代码片段，方便自己以后使用。

这个自己根据自己的个人习惯，自己挖掘。

另外因为使用vscode开发vue的typescript项目，vscode还需要安装对应的插件，比如TypeScript Vue Plugin

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/0859faff8aac481ba9c7df363da5bc71~tplv-k3u1fbpfcp-zoom-1.image)

这里随着chatGpt的大火，你会发现你写代码的方式会慢慢改变，现在一些有一些尝试，比如Github Compilot

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e28b8ab0081547829509cf84d94b2ef5~tplv-k3u1fbpfcp-zoom-1.image)

Github Compilot 可以根据你写的代码，自动学习，然后当你写其它代码的时候，会自动给出你提示，如果你去试用，你会发现太牛逼了😄。

另外在工作中，一些重复的工作，尽量想着如何去优化，去节省自己的时间，比如一些模版代码的编写，一些增删改查的工作，能用工具就用工具。

比如如果你使用umi，你会发现很多重复的工作其实都给你简化成了一个命令，比如创建页面，比如初始化prettier等。

总之就是能用工具处理的，就用工具处理，如果自己觉得做了，都不会提升自己，就想办法自动化去处理。

# Vue组件引入

当使用setup的时候，组件直接引入就可以了，不需要再自己手动注册.

下面就是直接把HelloWorld这个组件在App组件里边引入，直接使用就可以了，不需要再像以前那样注册。

```
<script setup lang="ts">
import HelloWorld from "./components/HelloWorld.vue";
</script>

<template>
  <HelloWorld msg="Vite + Vue" />
</template>
复制代码
```

如果不使用setup，你会发现引入一个组件，还是比较麻烦的，可以对比一下代码，自己感受一下😄

```
<template>
  <div>
    <HelloWorld msg="Hello, Vue 3!" />
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue'
import HelloWorld from './HelloWorld.vue'

export default defineComponent({
  components: {
    HelloWorld
  }
})
</script>
复制代码
```

# defineProps的使用

defineProps 在有两种定义方式，你可以任意选择其中一种，但是不能两种都使用

官方说明

However, it is usually more straightforward to define props with pure types via a generic type argument:

第一种"runtime declaration"

```
const props = defineProps({
  foo: { type: String, required: true },
  bar: Number,
});
复制代码
```

第二种"type-based declaration"

```
<script setup lang="ts"> 
    interface Props { 
        foo: string 
        bar?: number 
    } 
    const props = defineProps<Props>() 
</script>
复制代码
```

这两种定义方式没多大区别，可以任意选择一种使用，但是不能两种同时使用。

同时我们有些情况下，也希望props能够有默认值，可以如下使用：

```

// 第二种带默认值props
export interface ChildProps {
  foo: string
  bar?: number
}
const props = withDefaults(defineProps<ChildProps>(), {
   foo: "1qsd"
   bar?: 3
})

复制代码
```

当然如果遇到特别复杂的对象，需要使用ts定义的时候，可以这样使用：

```
<script setup lang="ts">
interface Book {
  title: string;
  author: string;
  year: number;
}

const props = defineProps<{
  book: Book;
}>();
</script>
复制代码
```

或者使用runtime那种方式

```
import type { PropType } from 'vue'

interface Book {
  title: string;
  author: string;
  year: number;
}

const props = defineProps({
  book: Object as PropType<Book>
})

复制代码
```

# defineEmits和defineProps获取父组件传过来值和事件

```

// 第一种获取事件方法
const emit = defineEmits<{
  (e: 'change', id: number): void
  (e: 'update', value: string): void
}>()

// 第二种获取事件方法
const emit = defineEmits(["dosth"])

复制代码
```

# ref和reactive

ref一般用于基本的数据类型，比如string，boolean

reactive一般用于对象

使用reactive的注意事项：

1.  reactive不能用于string，number，boolean

vue官方网站说明如下： It cannot hold [primitive types](https://link.juejin.cn/?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fen-US%2Fdocs%2FGlossary%2FPrimitive "https://developer.mozilla.org/en-US/docs/Glossary/Primitive") such as `string`, `number` or `boolean`

2.  不能修改reactive设置的值

比如：

```
let state = reactive({ count: 0 }) 
// the above reference ({ count: 0 }) is no longer being tracked (reactivity connection // is lost!) 
// 这里state如果重新赋值以后，vue就不能双向绑定
state = reactive({ count: 1 })
复制代码
```

ref的底层实现，其实也是调用的reactive实现的，有点类似react hooks的useState和useReducer；

# 使用useAttrs和useSlots

useAttrs 可以获取父组件传过来的id和class等值。 useSlots 可以获得插槽的内容。 例子中，我们使用useAttrs获取父组件传过来的id和class，useSlots获取插槽的内容。

父组件：

```
<template>

    <div class="father">{{ fatherRef }}</div>

    <Child :fatherRef="fatherRef" @changeVal="changeVal" class="btn" id="111">

        <template #test1>

        <div>1223</div>

        </template>

    </Child>

</template>

<script setup lang="ts">

import { ref } from "vue";

import Child from "./Child.vue";

const fatherRef = ref("1");

function changeVal(val: string) {

    fatherRef.value = val;

}

</script>

<style lang="scss" scoped>

.father {

    margin-top: 40px;

    margin-bottom: 40px;

}

.btn {

    font-size: 20px;

    color: red;

}

</style>
复制代码
```

子组件：

```
<template>

    <!-- <div class="child">{{ props.fatherRef }}</div> -->

    <div v-bind="attrs">

        <slot name="test1">11</slot>

        <input type="text" v-model="inputVal" />

    </div>

</template>

<script setup lang="ts">

import { computed, useAttrs, useSlots } from "vue";

const props = defineProps<{

    fatherRef: string;

}>();

const emits = defineEmits(["changeVal"]);

const slots = useSlots();

const attrs = useAttrs();

console.log(122, attrs, slots);

const inputVal = computed({

    get() {

        return props.fatherRef;

    },

    set(val: string) {

        emits("changeVal", val);

    },

});

</script>


<style lang="scss" scoped>

.child {

}

</style>

复制代码
```

# 使用自定义指令

在setup里边自定义指令的时候，只需要遵循`vNameOfDirective` 这样的命名规范就可以了

比如如下自定义focus指令，命名就是vMyFocus，使用的就是v-my-focus

自定义指令

```
<script setup lang="ts">
const vMyFocus = {
  onMounted: (el: HTMLInputElement) => {
    el.focus();
    // 在元素上做些操作
  },
};
</script>
<template>
  <input v-my-focus value="111" />
</template>


复制代码
```

# 使用defineExpose子组件传父组件

子组件

```
<template>

    <div class="child"></div>

</template>


<script setup lang="ts">

import { ref, reactive } from "vue";

function doSth() {

    console.log(333);

}

defineExpose({ doSth });

</script>


<style lang="scss" scoped>

.child {

}

</style>

复制代码
```

父组件

```
<template>

<div class="father" @click="doSth1">222</div>

    <Child ref="childRef"></Child>

</template>

<script setup lang="ts">

import { ref, reactive } from "vue";

import Child from "./Child.vue";

const childRef = ref();

function doSth1() {

    childRef.value.doSth();

}

</script>

<style lang="scss" scoped>

.father {

}

</style>

复制代码
```

# 父组件传子组件

父组件

```
<template>

    <div class="father"></div>

    <Child @click="doSth"></Child>

</template>

<script setup lang="ts">

import { ref, reactive } from "vue";

import Child from "./Child.vue";

function doSth() {

    console.log(112);

}

</script>

<style lang="scss" scoped>

.father {

}

</style>

复制代码
```

子组件

```
<template>

    <div class="child">2222</div>

</template>

<script setup lang="ts">

import { ref, reactive, onMounted } from "vue";

const emits = defineEmits(["doSth"]);

onMounted(() => {

    emits("doSth");

});

</script>

<style lang="scss" scoped>

.child {

}

</style>

复制代码
```

# toRefs

当从父组件向子组件传props的时候，必须使用toRefs或者toRef进行转一下，这是为什么呢？

这里是因为如果不使用toRefs转一次的话，当父组件中的props改变的时候，子组件如果使用了Es6的解析，会失去响应性。

可以看下如下例子

父组件

```
<template>

<div class="father" @click="changeVal">{{ fatherRef }}</div>

    <Child :fatherRef="fatherRef"></Child>

</template>

<script setup lang="ts">

import { ref, reactive } from "vue";

import Child from "./Child.vue";

const fatherRef = ref(1);

function changeVal() {

    fatherRef.value = 2;

}

</script>

<style lang="scss" scoped>

.father {

    margin-bottom: 40px;

}

</style>

复制代码
```

子组件

```
<template>

    <div class="child" @click="changeVal">{{ fatherRef }}</div>

</template>

<script setup lang="ts">

import { ref, reactive, onMounted, toRefs } from "vue";

const props = defineProps<{

    fatherRef: any;

}>();

const { fatherRef } = props;

function changeVal() {

    fatherRef.value = 34;
}

</script>

<style lang="scss" scoped>

.child {

}

</style>

复制代码
```

可以看到当父组件如果点击之后，因为使用const { fatherRef } = props;进行解析，就失去了响应性

所以当父组件变成2的时候，子组件还是1。

这里有两种解决办法

1.  使用const { fatherRef } = toRefs(props);
1.  在模版中中使用props.fatherRef

# 子组件使用v-model

## 1. 可以在子组件中使用computed，实现双向绑定

父组件

```
<template>

    <div class="father">{{ fatherRef }}</div>

    <Child :fatherRef="fatherRef" @changeVal="changeVal"></Child>

</template>

<script setup lang="ts">

import { ref } from "vue";

import Child from "./Child.vue";

const fatherRef = ref("1");

function changeVal(val: string) {

    fatherRef.value = val;

}

</script>


<style lang="scss" scoped>

.father {

    margin-top: 40px;

    margin-bottom: 40px;

}

</style>

复制代码
```

子组件

```
<template>

    <!-- <div class="child">{{ props.fatherRef }}</div> -->

    <input type="text" v-model="inputVal" />

</template>

<script setup lang="ts">

import { computed } from "vue";

const props = defineProps<{

    fatherRef: string;

}>();

const emits = defineEmits(["changeVal"]);


const inputVal = computed({

    get() {

        return props.fatherRef;

    },

    set(val: string) {

        emits("changeVal", val);

    },

});

</script>

<style lang="scss" scoped>

.child {

}

</style>

复制代码
```

## 2 可以从父组件传递值和改变值的方法，然后子组件也可以使用v-model

例子中父组件传递 modelValue和update:modelValue方法 父组件：

```
<template>

    <Child :modelValue="searchText" @update:modelValue="changeVal"> </Child>

</template>

<script setup lang="ts">

import { ref } from "vue";

import Child from "./Child.vue";

const searchText = ref(1);

function changeVal(val: number) {

    searchText.value = val;

}

</script>

<style lang="scss" scoped>

.father {

    margin-top: 40px;

    margin-bottom: 40px;

}

.btn {

    font-size: 20px;

    color: red;

}

</style>
复制代码
```

子组件：

```
<template>

    <!-- <div class="child">{{ props.fatherRef }}</div> -->

    <!-- <div v-bind="attrs">

        <slot name="test1">11</slot>

        <input type="text" v-model="inputVal" />

    </div> -->

    <input v-model="modelValue" />

</template>


<script setup lang="ts">

import { computed, useAttrs, useSlots } from "vue";

const props = defineProps<{

    modelValue: number;

}>();

// const emits = defineEmits(["changeVal"]);

</script>


<style lang="scss" scoped>

.child {

}

</style>

复制代码
```

# 递归组件

组件本身是可以调用组件自身的，也就是递归。 比如名为 `Child.vue` 的组件可以在其模板中用 `<Child/>` 引用它自己。这里需要注意的是需要设置条件语句，用来中断递归，不然递归会无限递归下去。

父组件

```
<template>
  <Child :modelValue="searchText" @update:modelValue="changeVal"> </Child>
</template>

<script setup lang="ts">
import { ref } from "vue";
import Child from "./Child.vue";
const searchText = ref(1);
function changeVal(val: number) {
  searchText.value = val;
}
</script>

<style lang="scss" scoped>
.father {
  margin-top: 40px;
  margin-bottom: 40px;
}
.btn {
  font-size: 20px;
  color: red;
}
</style>

复制代码
```

子组件

```
<template>
  <input v-model="modelValue" />
  <Child
    :modelValue="test"
    @update:modelValue="changeTest"
    v-if="modelValue > 2"
  ></Child>
</template>

<script setup lang="ts">
import { computed, useAttrs, useSlots, ref } from "vue";
const props = defineProps<{
  modelValue: number;
}>();
const test = ref(0);
function changeTest(val: number) {
  test.value = val;
}

// const emits = defineEmits(["changeVal"]);
</script>

<style lang="scss" scoped>
.child {
  position: relative;
}
</style>
```