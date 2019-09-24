title: '从vue源码角度分析v-text和{{}}插值的区别'
date: 2019-01-20 00:02:20
tags:
---
## 问题描述：

在做生成表格的需求时，遇到需要手动在单元格输入文字，并生成表头：即实现可编辑效果的功能。发现可以通过设置标签的contenteditable属性来实现任意html元素可编辑,并且把用户输入赋值给变量。这时，在vue框架下，就想到了双向数据绑定绑定输入值。但因为这不是input表单标签，无法直接使用v-model指令，一般是使用创建组件的方式实现双向数据绑定。但我当时第一直觉想到的是双花括号方式绑定。并遇到了一个坑，在此记录下来。

<!--more-->

### 为什么不能使用v-model

如果在contenteditable的元素上使用了v-model会报以下错误:

>v-model is not supported on this element type. If you are working with contenteditable, it's recommended to wrap a library dedicated for that purpose inside a custom component.

v-model仅仅支持input、textarea、select表单元素,这点以及报错信息从源码上也可以看出来:

```
if (el.component) {
      genComponentModel(el, value, modifiers);
      // component v-model doesn't need extra runtime
      return false
    } else if (tag === 'select') {
      genSelect(el, value, modifiers);
    } else if (tag === 'input' && type === 'checkbox') {
      genCheckboxModel(el, value, modifiers);
    } else if (tag === 'input' && type === 'radio') {
      genRadioModel(el, value, modifiers);
    } else if (tag === 'input' || tag === 'textarea') {
      genDefaultModel(el, value, modifiers);
    } else if (!config.isReservedTag(tag)) {
      genComponentModel(el, value, modifiers);
      // component v-model doesn't need extra runtime
      return false
    } else {
      warn$1(
        "<" + (el.tag) + " v-model=\"" + value + "\">: " +
        "v-model is not supported on this element type. " +
        'If you are working with contenteditable, it\'s recommended to ' +
        'wrap a library dedicated for that purpose inside a custom component.'
      );
    }
```

### 使用v-text

先说解决方案:

{% jsfiddle kenzid/txohLkfd js,html,css,result light %}

其实我一开始并没有想到用v-text的方式解决这个问题。而是想到了使用双花括号mastache的方式。但是这会出现一个匪夷所思的问题:这里是[issue传送带1](https://github.com/vuejs/vue/issues/5234),[issue传送带2](https://github.com/vuejs/vue/issues/5805)

{% jsfiddle kenzid/416s3tnb/2 js,html,css,result light %}

我们发现，contenteditable元素中的文字在失去焦点时重复了两遍！为什么会这样?为什么使用v-text就能绕开这个问题?
虽然官方文档上说两者一样:
![](http://ww1.sinaimg.cn/large/70665107gy1g0efoslxzfj20u60ly40t.jpg)

但尤雨溪在[issue](https://github.com/vuejs/vue/issues/5805)中简要回答了花括号和v-text区别:


[插播:写到这里时我想上传一个vue官方解释v-text的图片，发现七牛云回收了它自己的域名，导致我的图图外链都失效了，我又不想自己去花钱注册一个备案域名。所以想用rp先生的vps搭建一个自己的图床。找到了lychee荔枝图床，然后说需要先安装阿帕奇，php，mysql。。。。我自闭了。下面说使用docker安装最为简单。我又想去看看docker了。。。我的思绪啊.在服务器上按照操作步骤装了半天，发现openvz的vps装不了docker啊。摔。[告诉大家一个坑，openVZ的VPS装不了Docker](https://meta.discoursecn.org/t/topic/1227)，我回来了。已是2小时以后。]

> This type of usage will be a wontfix. v-text or v-html works by wiping away the pre-existing content with completely new nodes; however when you use mastache bindings Vue will attempt to diff the text nodes - which will likely be made out of sync by contenteditable because how browsers handle/split the text nodes are completely unpredictable.

翻译一下:

> 这种用法导致的问题将不会修复。使用v-text和v-html将会把元素之前存在的content完全用(vue生成的)新的nodes替换掉;然而如果使用花括号绑定vue将会尝试比较文本节点，由于浏览器操作拆分文本节点的行为完全无法预测，所以使用contenteditable属性(dom和变量)极有可能无法同步。

这到底是啥意思？

## 从源码上分析花括号的行为

我们知道在 Vue.js 中我们可以采用简洁的模板语法来声明式的将数据渲染为 DOM[[1]](https://ustbhuangyi.github.io/vue-analysis/data-driven/)。这个步骤又分为两步，第一步是将我们定义的模板语法转成Vnode,第二步将Vnode渲染成实际的dom。
![](http://ww1.sinaimg.cn/large/70665107gy1g03xkdposaj21ma0xggrj.jpg)

根据图，从步骤1中生成获取vnode的函数参数vm.$createElement,从步骤2中生成获取vnode的render函数。这个render函数就是允许用户手写的render函数。这里推荐一个网站，可以清晰的看到模版和render函数的对应关系：。生成的vnode如下:

```
{
    "tag": "div",
    "data":
    {
        "attrs":
        {
            "id": "app"
        }
    },
    "children": [
    {
        "tag": "div",
        "data":
        {
            "attrs":
            {
                "contenteditable": "true"
            },
            "on":
            {}
        },
        "children": [
        {
            "text": "",
            "raw": false,
            "isStatic": false,
            "isRootInsert": true,
            "isComment": false,
            "isCloned": false,
            "isOnce": false,
            "isAsyncPlaceholder": false
        }],
        "context":{},
        "raw": false,
        "isStatic": false,
        "isRootInsert": true,
        "isComment": false,
        "isCloned": false,
        "isOnce": false,
        "isAsyncPlaceholder": false
    }],
    "context":{},
    "raw": false,
    "isStatic": false,
    "isRootInsert": true,
    "isComment": false,
    "isCloned": false,
    "isOnce": false,
    "isAsyncPlaceholder": false
}
```

从vnode中可以看出，根元素id为app的div元素下，是可用户输入的contenteditable的div元素，div元素下还有一个元素，它没有tag标签，这个元素就是文本节点textnode.就是因为这个vnode的存在，所以vue初始化后的dom表现为div内部有一个text为空的文本节点。我们可以验证一下:

![](http://ww1.sinaimg.cn/large/70665107gy1g040uio5quj21fq0xsjxz.jpg)

然后用户进行输入的时候，又会创建一个新的textnode，所以由用户输入创建的textnode和vue初始化的textnode同时存在，所以就表现为输入值重复了。

而v-text这种方式并不会在初始化时创建textnode节点，用户输入创建的节点是唯一的text节点，数据更新后，dom上也只有一个textnode节点，所以值没有重复。间接解决了这个问题。
### reference:
[1.Vue.js 技术揭秘--数据驱动](https://ustbhuangyi.github.io/vue-analysis/data-driven/)
