<template>
  <div :class="{'container':show, 'hide': !show}">
    <div class="header">{{message}}</div>
    <div class="gradient_line"></div>
    <div class="control_panel">
      <span class="confirm" @click='submit'>{{confirm}}</span>
      <span class="cancel" @click='close'>{{cancel}}</span>
    </div>
    <div class="picker" ref='picker'>
      <div class="hour" @mousedown="onmousedown">
        <ul ref="scrollhour">
          <li v-for="(item,index) in hourList()" :key="index">{{item}}</li>
        </ul>
      </div>
      <div class="colon">:</div>
      <div class="min" @mousedown="onmousedown">
        <ul ref="scrollmin">
          <li v-for="(item,index) in minList()" :key="index">{{item}}</li>
        </ul>
      </div>
      <div class="mask-top"></div>
      <div class="mask-bottom"></div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'TimePicker',
  props: {
    message: {
      type: String,
      default: "Dusiqi's TimePicker"
    },
    confirm: {
      type: String,
      default: 'confirm'
    },
    cancel: {
      type: String,
      default: 'cancel'
    },
    show: {
      type: Boolean,
      default: false
    }
  },
  data () {
    return {
      height: 0, // 滚动区域的范围
      step: 0, // 滚动区域每一栏的高度
      node: [],
      isScroll: [false, false], // 是否可以滚动
      startY: [0, 0], // 点击时的Y值
      origTop: [0, 0], // ul起始的top值
      index: [12, 30] // 选中的时间索引
    }
  },
  methods: {
    hourList () {
      let arr = []
      for (let i = 0; i < 24; i++) {
        if (i < 10) arr.push('0' + i)
        else arr.push(i.toString())
      }
      return arr
    },
    minList () {
      let arr = []
      for (let i = 0; i < 60; i++) {
        if (i < 10) arr.push('0' + i)
        else arr.push(i.toString())
      }
      return arr
    },
    submit () {
      let hour = this.hourList()[this.index[0]]
      let min = this.minList()[this.index[1]]
      console.log(hour, min)
      this.$emit('getTime', hour, min)
    },
    close () {
      this.$emit('close')
    },
    onmousedown (e) {
      let i
      let node = e.currentTarget.childNodes[0]
      if (node === this.node[0]) i = 0
      if (node === this.node[1]) i = 1
      this.isScroll[i] = true
      this.startY[i] = e.clientY
      this.origTop[i] = this.node[i].offsetTop
      window.addEventListener('mousemove', this.onmousemove, false)
      window.addEventListener('mouseup', this.onmouseup, false)
    },
    onmousemove (e) {
      if (!this.isScroll[0] && !this.isScroll[1]) return
      let i
      if (this.isScroll[0]) i = 0
      if (this.isScroll[1]) i = 1
      let height = e.clientY - this.startY[i]
      let top = this.origTop[i] + height
      let len = this.node[i].childNodes.length
      if (top <= this.step * 2.5 && top >= -this.step * (len - 2.5)) {
        this.scroll(this.node[i], top)
      }
    },
    onmouseup (e) {
      if (!this.isScroll[0] && !this.isScroll[1]) return
      let i
      if (this.isScroll[0]) i = 0
      if (this.isScroll[1]) i = 1
      this.isScroll[i] = false
      let top = this.node[i].offsetTop
      if (top % this.step !== 0) {
        let temp = Math.abs(top % (this.step))
        if (temp < this.step / 2) top -= top % (this.step)
        else if (top > 0) top += (this.step - top % this.step)
        else top = top - this.step - top % this.step
      }
      this.index[i] = Math.round(2 - top / this.step)
      let len = this.node[i].childNodes.length
      if (top <= this.step * 2 && top >= -this.step * (len - 2)) {
        this.scroll(this.node[i], top)
      }
    },
    scroll (node, top) {
      node.style.top = top + 'px'
      let list = node.childNodes
      for (let i = 0; i < list.length; i++) {
        let deg = this.step * (i - 2) + top
        if (deg > -90 && deg < 90) {
          list[i].style.transform = `rotateX(${deg}deg)`
        }
      }
    },
    init () {
      this.height = this.$refs.picker.clientHeight
      this.step = this.height / 5
      this.node = [this.$refs.scrollhour, this.$refs.scrollmin]
      let hourTop = (2 - this.index[0]) * this.step
      let scrollHour = this.$refs.scrollhour
      this.scroll(scrollHour, hourTop)
      let minTop = (2 - this.index[1]) * this.step
      // eslint-disable-next-line no-trailing-spaces
      let scrollMin = this.$refs.scrollmin 
      this.scroll(scrollMin, minTop)
    }
  },
  mounted () {
    this.init()
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
ul, li{
  padding: 0;
  margin: 0;
  list-style: none;
}
.container{
  width: 100%;
  position: absolute;
  height: 50vh;
  bottom: 0;
  left: 0;
  border: 1px solid #aaa;
  animation: container 1s ease;
}
.hide{
  position: absolute;
  width: 100%;
  height: 50vh;
  bottom: -60vh;
  left: 0;
  border: 1px solid #aaa;
  animation: hide 1s ease
}
@keyframes container {
  from {bottom: -60vh}
  to {bottom: 0}
}
@keyframes hide {
  from {bottom: 0;}
  to {bottom: -60vh;}
}
.header{
  font-size: 4vh;
  color: #d73;
  height: 10vh;
  padding: 10px 0;
  box-sizing: border-box;
  text-align: center;
}
.gradient_line{
  height: 0.5vh;
  width: 100%;
  background: linear-gradient(left, #fff -4%,#d73 50%,#fff 100%);
}
.control_panel{
  display: flex;
  justify-content: space-between;
  height: 9vh;
  font-size: 4vh;
  padding: 2% 15%;
  box-sizing: border-box;
  color: #8c8c8c;
}
.picker{
  position: relative;
  height: 30vh;
  display: flex;
  justify-content: space-between;
  padding: 0;
  margin: 0;
  overflow: hidden;
}
.colon{
  line-height: 30vh;
  font-size: 5vh;
  font-weight: border;
}
.hour, .min, .confirm, .cancel{
  width: 48%;
  text-align: center;
  cursor: pointer;
}
.hour, .min{
  position: relative;
  transition-timing-function:cubic-bezier(0.23, 1, 0.320, 1);
  transition-duration:0ms;
  user-select: none;
}
ul{
  position: absolute;
  width: 100%;
  left: 0;
}
li{
  line-height: 6vh;
  height: 6vh;
}
.mask-top, .mask-bottom{
  height: 12vh;
  width: 90%;
  position: absolute;
  background-color: #fff;
  left: 50%;
  transform: translateX(-50%);
  opacity: 0.5;
  pointer-events: none;
}
.mask-top{
  top: 0;
  border-bottom: 1px dashed #ccc;
}
.mask-bottom{
  bottom: 0;
  border-top: 1px dashed #ccc;
}
</style>
