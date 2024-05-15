## animation-name

动画名称默认为`none`，表示不应用动画。

动画命名规则：`a-z`、`A-Z`、`0-9`、连字符（-）、下划线（__）、`ISO 10646`字符集中`U+00A0-U+10FFFF`的字符。不能是数字和2个连字符开头。开头是一个连字符时，后面紧跟的不能是数字。如果真要这么做，需要进行转义。比如：`2s`不能作为动画名称，但是转义后的`\2s`可以作为动画名称。

## animation-duration

动画播放时间，或者叫持续时间。值为数字，单位为`s`或者`ms`，默认为0s。

## animation-direction

取值：normal | reverse | alternate | alternate-reverse

动画方向是很重要的属性，`animation-timing-funcion`和`animation-fill-mode`都依赖该属性

`normal`: 0% -> 100%

`reverse`: 100% -> 0%

`alternate`: 0% -> 100% -> 0%...循环

单数次迭代由0%帧指向100%帧

偶数次迭代由100%帧指向0%帧

`alternate-reverse`：100% -> 0% -> 100%...循环

单数次迭代由100%帧指向0%帧

偶数次迭代由0%帧指向100%帧

注意：`alternate`和`alternate-reverse`都需要`animation-iteration-count`数大于1，否则与`normal`和`reverse`效果一样。其次`alternate`和`alternate-reverse`的往返运动的`animation-timing-function`是相反的效果。

## animation-iteration-count

动画迭代次数表明动画运行几次。迭代次数为大于等于0的数（包括小数），也可以配置成关键字`infinate`，表明无限循环播放动画。小数可以换算成百分比，表明运行到了哪个帧的动画。

当`animation-direction`的值为`alternate`或者`alternate-reverse`时，`animation-iteration-count`的值必须大于1。因为一次完整的`alternate`表明动画迭代了2次。

## animation-delay

动画延迟表明延迟多久后开始播放动画。值为数字，单位为`s`或者`ms`。如果`animation-iteration-count`的值大于1，动画只在第一次迭代时进行延迟，后续不延迟。

`animation-delay`可以配置为负数，表明提前了多少时间执行了动画，相当于将动画时间轴往前推移了多少时间。例如：1个动画的`animation-duration`为10s，`animation-delay`为-4s，动画会立即播放，没有延迟，并且相当于已经提前执行了4s，动画开始时会从4s的位置即40%关键帧处开始往后执行。

## animation-timing-function

动画时序函数用来描述动画怎么呈现。包含2个大类：贝赛尔曲线和步进时序函数。

#### 贝塞尔曲线

css预置了5个贝塞尔曲线值，及一个自定义贝塞尔曲线函数。

| 时序函数        | 三次方贝塞尔值                          | 表现形式                   |
| ----------- | -------------------------------- | ---------------------- |
| ease        | cubic-bezier(0.25, 0.1, 0.25, 1) | 开头慢，中间加速，结尾变慢          |
| linear      | cubic-bezier(0, 0, 1, 1)         | 保持全程匀速                 |
| ease-in     | cubic-bezier(0.42, 0, 1, 1)      | 开头慢，突然加快，结尾戛然而止        |
| ease-out    | cubic-bezier(0, 0, 0.58, 1)      | 开头加快，逐渐减慢，是ease-in的逆过程 |
| ease-in-out | cubic-bezier(042, 0, 0.58, 1)    | 与eas类似，但开头加速更快         |

`cubic-bezier`的参数是2个手柄端点位置的`x`和`y`坐标，即`cubic-bezier(x1, y1, x2, y2)`。`x`坐标位于0到1之间，`y`坐标没有要求，甚至可以为负数。记住一点：曲线越陡，运动越快；曲线越平，运动越慢。

#### 步进时序函数

`css`内置了3个步进时序函数，`steps`、`step-start`、`step-end`，其中`step-start`和`step-end`是`steps`的特殊值。

`steps`函数有2个参数，第一个参数是分几步完成动画，第二个参数是变化点位置。

举例：

假如一个动画要将元素像右移动1000px，需要10s完成，同时`animation-timing-function`设置成`steps(5, end)`。由定义可知动画会分5步完成，每一步需要2s，同时每一步需要移动200px。动画过程就是每过2s，向有移动200px，直到10s结束。

变化点取值`start`和`end`。`start`表明在第一步不用等待这一步的时间结束，立马移动到这一步的结尾位置。`end`表明在每一步等待时间结束后，移动到这一步结束位置。如果上面例子改成`step(5, start)`，动画开始时立马向右移动到200px的位置，而不是等2s才往右移动。然后开始每过2s移动200px往右移动，感觉上就是只移动了4步，花了8s。

`step-start`是`steps(1, start)`的特殊形式。表示整个过程分1步完成，没有等待时间，动画开始时立马移动到这一步结束位置。

`step-end`是`steps(1, end)`的特殊形式。表示整个过程分1步完成，等待整个动画时间结束后（因为就1步，所以这一步的时间等于`animation-duration`），移动到这一步的结束位置。

## animation-play-state

动画播放状态，包含`running`和`paused`状态。好理解，不过多描述。

## animation-fill-mode

取值：none | forwards | backwards | both

表明是否将动画的开始帧选择器的属性或者结束帧选择器的属性赋值给应用动画的元素。同时`forwards `、`backwards`、`both`受`animation-direction`的影响。

`none`：为默认值，表示动画结束后，回到属性初始状态（而不是0%帧选择器的属性，受方向影响，也可能是100%帧的选择器属性）。

`forwards`：表示动画结束后，将最后一帧的属性赋值给元素。如果`animation-iteration-count`为奇数，同时`animation-direction`为`normal`或者`alternates`时，即100%帧选择器的属性赋值给元素；如果`animation-iteration-count`为奇数，同时`animation-direction`为`reverse`或者`alternate-reverse`时，即0%帧选择器的属性赋值给元素。如果为小数，则将换算后的最后一帧，即动画停止位置的帧数据赋值给元素。

`backwards`： 将动画初始帧的数据立马赋值给元素，即使元素设置了`animation-delay`为大于0的数。当`animation-direction`为`normal`或者`alternate`时，即将0%帧的数据赋值给元素；当`animation-direction`为`reverse`或者`alternate-reverse`时，即将100%帧的数据赋值给元素。

`both`：表明同时启用`forwards`和`backwards`。

## animation简写

`animation`可以简写8个动画属性。注意`animation-duration`的时间配置在`animation-delay`之前。

## animation事件

`animationstart`：动画开始事件。如果有`animation-delay`，且大于0，则等延迟结束后触发该事件，如果小于0，则立马触发。

`animationend`：动画结束事件。如果`animation-iteration-count`配置成`infinate`，并且`animation-duration`大于0，则永远不会触发该事件。如果`animation-iteration-count`配置成`infinate`，并且`animation-duration`等于0，会同时触发`animationstart`和`animationend`事件。

`animationiteration`：动画迭代事件。动画迭代多次时触发，详细点就是当前一个迭代结束后一个迭代开始时触发。如果`animation-iteration-count`等于1时，只迭代一次，没有下次迭代，就不会触发该事件。
