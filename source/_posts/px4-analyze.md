title: Pixhawk log日志分析 by MissionPlanner
date: 2015-11-24 13:21:28
tags: pixhawk
---
![](http://7xk7fp.com1.z0.glb.clouddn.com/powerOn.png)
![](http://7xk7fp.com1.z0.glb.clouddn.com/start.png)
从上电到开始飞行，说明等待其初始化时间，包括飞控初始化，GPS锁星，错误解除等。

<!--more-->
从CTUN:ThrIn和ThrOut的曲线可以详细看出操作手的操作过程和飞行情况。
![](http://7xk7fp.com1.z0.glb.clouddn.com/ThrInvsThrOut.png)
第一次第二次的自稳飞行都没有太大的问题。

仔细观察高度和输入输出的油门曲线：
![](http://7xk7fp.com1.z0.glb.clouddn.com/heightCompare.png)
设定为定高模式起飞的时候，遥控油门上推，但是四轴油门并没有跟踪遥控油门，导致四轴飞不起来.
A点时刻，飞机突然升到1.6米高，所以操作手快速减少了油门，飞机掉落一点，然后又稍稍加大油门，飞机油门输出开始不受控制，表现为飞机抖动，切换成自稳模式后，飞机油门重新恢复跟踪遥控油门，所以猜想原因是定高模式下油门跟踪不灵敏。
下一个时刻的真实情况是飞机撞到了墙壁，浆被摔断。所以飞机开始失去平衡。并掉落下来。
从加速度计的Roll的情况可以看出：
![](http://7xk7fp.com1.z0.glb.clouddn.com/accCompare.png)
初步分析为，定高模式的跟踪不够灵敏，或者电机导致。

> 定高模式是由气压计进行测量的。高度每升高9米，气压大概降低100Pa，我们知道了海平面的气压是101325Pa和当前的气压值，就可以根据差值大概计算当前高度。

> 加速度计和气压计协同定高就像陀螺仪和加速度计协同进行姿态解算一样，各有各优缺点。加速度计有静差，而且加速度计进行二次积分后数据漂移过大，但是加速度计进行积分短时间内是精准的，就像姿态解算中陀螺仪的积分短时间内是非常精准的。气压计由于测量精度有限，只能达到10cm，所以气压计输出的数据直接转化为高度的话会有几十厘米的浮动，直接用于高度控制显然是不现实的，但是气压计长时间来看，其值又是非常具有参考性的，因为它的值在真实值附近浮动。基于此，我们可以将加速度计的值进行积分后与气压计的值进行互补滤波或卡尔曼滤波，这样就能取长补短，获得较好的效果。

所以加速度计的抖动可能会导致高度测量不准确，使定高模式的飞机一飞冲天。

正如官网上所说：

> Vibrations
High vibrations cause the Copter’s accelerometer based altitude and horizontal position estimates to drift far off from reality which leads to problems with alt hold (normally rocketing into the sky) or Loiter (drifting).

> 大幅度振动使得依赖于加速度计的高度和水平位置的估测不准确，会有飘逸，远离真实值，将导致定高出现问题（像火箭一样冲天）和定点出现问题（漂移）

也就是官网上给出的原因是加速度计的振动。且官方给出的范围是X，Y不要超过+-3，Z轴为-15至-5.

我的log：
![](http://7xk7fp.com1.z0.glb.clouddn.com/vib.png)
在前面的自稳飞行是正常的。即使是定高模式，也处于正常范围内。后面的大幅度抖动应该是由于撞落瞬间导致的。所以我认为我的飞机有一飞冲天的趋势的原因与加速度z轴的抖动无关。

官网关于消除抖动的说明：

> My overpowered copter rockets into the air can’t be brought down unless I cut the throttle

> Very over powered multicopters can require so little throttle to hover that even the minimum throttle (13%) is enough for them climb. In addition, the default Rate roll/pitch gains, which are meant for weaker copter, are too high for a powerful copter and can lead to roll/pitch instability. The Copter firmware may try to compensate for this roll/pitch instability by increasing the overall throttle which can lead to the copter climbing further (Note: in this particular case Copter increasing the overall throttle is counter-productive but in a properly tuned quad it helps in cases where there is a motor failure).

> The possible solutions are:

> 
* add extra weight to your frame to bring the hover throttle closer to mid stick
* reduce the THR_MIN parameter (default is 130 or 13%) to the lowest value that will still keep all motors spinning reliably
* set the rate roll/pitch gains low (perhaps 1/2 the default) during the first flight and increase them from there. Consider using the CH6 tuning feature to allow you to tune your roll/pitch gains for the first few flights.

官网关于定高说明的介绍：

> 如果油門在40% ~ 60%之間，飛行器會維持當時高度。
在油門的中間無效區之外（40%以下或60%以上），飛行器會傷聲或下降，視搖桿偏向而定。搖桿向下打到底，飛行器以2.5公尺/秒速度下降，油門向上打到頂，飛行器以2.5公尺/秒速度上升。這些速度可以由PILOT_VELZ_MAX參數調整。

参数调节

> Altitude Hold P值是用來把高度誤差（要求高度和實際高度的差距）換算為上升或下降速度。較高的P值會比較靈敏地維持高度，但是設得太高會使油門反應好像抽筋一樣。

> Throttle Rate (通常不需要調校) 把要求的上升或下降速度換算為要求的向上或向下的加速度。

> Throttle Accel PID 值把加速度誤差（要求加速度和實際加速度的誤差）換算為馬達的輸出。如果你要修改P跟I這兩個參數時應該要維持P:I為1:2 的比例(I是P的兩倍)。這些數值應該永遠不會去增加，但如果您的飛行品的動力非常大時將它們各降低50%將會反應更為良好(就是P降為0.5，I降為1.0)。


也就是说如果从自稳时的悬停切换到定高，如果此时油门在40%~60%之间，飞行器一般就能维持现在的高度。这时他有一个死区，就是油门在40%-60%,无论如何操作，飞行器应该高度不变。

但如果你自稳模式下，悬停的油门低于40%(exmple:30%)，切换到定高时，假设油门杆在30%，低于死区，所以他会猛然下降。高于60%的话，切换到定高时，假设油门杆在70%，高于死区，所以他会猛然上升。其速率由Altitude Hold P控制(待验证)。

所以解决办法就是，避免用定高起飞，先用自稳飞到悬停时的那个油门（尽量调整在操作杆的40%，60%位置，如果高或者低用以下[方法](http://copter.ardupilot.com/wiki/ac_throttlemid/)调整油门中位，或者可以调整死区范围），保证悬停后，再切换定高模式。

附GPS飞行路线图
![](http://7xk7fp.com1.z0.glb.clouddn.com/Image%206.jpg)