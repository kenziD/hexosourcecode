title: min-height:100%
date: 2015-10-08 23:05:10
tags: css
---
[quick learn form here](http://www.tutwow.com/htmlcss/quick-tip-css-100-height/)

如果想要子元素页面100%
父元素要设为height:100%,子元素设为min-height:100%.当然也可以子元素设为height:100%,不过这样高度就不会随着内容的增多而被撑高。

如果父元素高度也是min-height:100%,子元素也设置为min-height:100%,子没办法是真正的满屏。

<!--more-->

>Incidentally, the reason why you have to specify height and min-height to html and body respectively is because neither element has any intrinsic height. **Both are height: auto by default.** It is the viewport that has 100% height, so height: 100% is taken from the viewport, then applied to body as a minimum to allow for scrolling of content.


>using min-height: 100% on both, doesn't cause #child to expand to the full height of #father because min-height with a percentage doesn't work on #child unless #father has an explicit height.

```
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8" />
        <title>ppt</title>
        <style type="text/css">
            html, body {
                height: 100%;
                width: 100%;
                margin: 0;
                padding: 0;
                background: red;
            }
            #father {
                min-width: 100%;
                min-height: 100%;
                background: #AEEEEE;
            }
            #child {
                min-height: 100%;
                min-width: 100%;
                background:#FFC0CB;
            }
        </style>
    </head>
    <body>
        <div id="father">
            <div id="child">
                Lorem ipsum dolor sit amet, consectetur adipisicing elit. Debitis beatae quasi ipsum nihil perferendis rerum ex sit, asperiores officia totam eveniet magnam, repellat animi unde commodi, perspiciatis obcaecati cupiditate tempora.
            </div>
        </div>
    </body>
</html>

```
![father:min-height:100%](http://7xk7fp.com1.z0.glb.clouddn.com/min-height.png)

如果把father的min-height改为height:100%，child就可以满屏啦。

效果如图：

![father:min-height:100%](http://7xk7fp.com1.z0.glb.clouddn.com/height.png)