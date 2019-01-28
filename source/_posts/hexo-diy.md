title: Hexo-DIY
date: 2015-10-03 21:19:55
tags: hexo
---

### Change column-number

**Compare**:

![before](http://7xk7fp.com1.z0.glb.clouddn.com/archive-before.png)
![after](http://7xk7fp.com1.z0.glb.clouddn.com/archive-after.png)

**Reason**:Column 3 makes me feel that it's not clear about time line
<!--more-->
**File path**:themes\landscape\source\css\_partial\archive.styl
```
.archives
  column-gap: 10px
  @media mq-tablet
    column-count: 2
  @media mq-normal
    column-count: 1 //Before is 3 Now is 1

```

### Chang the archive
**Compare**:
![before](http://7xk7fp.com1.z0.glb.clouddn.com/head-before.png)
![after](http://7xk7fp.com1.z0.glb.clouddn.com/head-after.png)
**Reason**:The Archives and Home link is not apparent above some background image.I want to make it outstanding

**File path**: themes\landscape\source\css\_partial\header.styl

```
#header
height: banner-height
position: relative
border-bottom: 1px solid color-border
&:before, &:after
content: ""
position: absolute
left: 0
right: 0
height: 50px
&:before
top: 0
background: linear-gradient(rgba(0, 0, 0, 0.5), transparent)//Before is 0.2 Now is 0.5
&:after
bottom: 0
background: linear-gradient(transparent, rgba(0, 0, 0, 0.2))

$nav-link
font-size:subtitle-size//Before is font-size now is subtitle-size
//you can find more about font-size in css/_variables.style
float: left
color: #fff
opacity: 0.9//Before is 0.6 Now is 0.9
text-decoration: none
text-shadow: 0 1px rgba(0, 0, 0, 0.2)
transition: opacity 0.2s
display: block
padding: 20px 15px
&:hover
opacity: 1
```
### Change markdown blockquote style

**Compare**:
![before](http://7xk7fp.com1.z0.glb.clouddn.com/blockquote-before.png)
![after](http://7xk7fp.com1.z0.glb.clouddn.com/blockquote-after.png)

**Reason**:its ugly

**File path**:themes\landscape\source\css\_partial\article.styl
```
blockquote
    padding: 0 15px//add padding
    color: #777//add color
    border-left: 4px solid #ddd//add border
    font-family: font-serif
    font-size: 1.0em
    margin: line-height 20px
    text-align: left

```

