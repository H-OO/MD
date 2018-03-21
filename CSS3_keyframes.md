# @keyframes

常用的属性  
* animation-name              @keyframes 动画的名称
* animation-duration          动画的持续时间，默认值为0s
* animation-timing-function   动画的运动曲线，默认值为ease
* animation-delay             动画延时执行，默认值为0s
* animation-iteration-count   动画执行的次数，默认值为1
* animation-direction         动画在下周期是否逆向播放，默认值为normal
* animation-fill-mode         动画完成后的状态，默认值为none

注意：简写时与时间相关的属性都要携带单位

```css
@keyframes test {
  0% {
    /* todosomething */
  }
  100% {
    /* todosomething */
  }
}

/* 将test动画捆绑给选择器 */
div {
  /* @keyframes 动画的名称 */
  animation-name: test;
  /* 动画的持续时间 */
  animation-duration: 2s;
  /* 动画的运动曲线 */
  animation-timing-function: linear;
  /* 动画延时执行 */
  animation-delay: 0s;
  /* 动画执行的次数 infinite无限次的执行 */
  animation-iteration-count: infinite;
  /* 动画在下周期是否逆向播放 normal正常播放 alternate逆向播放 */
  animation-direction: alternate;
  /* 动画完成后的状态 none还原到初始状态 forwards定格在动画最后一帧 */
  animation-fill-mode: forwards;
}

/* 简写的动画 animation 属性 */
div {
  animation: test 2s linear 0s infinite alternate forwards;
}
```

