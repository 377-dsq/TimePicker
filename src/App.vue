<template>
  <div id="app">
    <div class="time">
      <span>The time is</span>
      <input type="text" v-model="hour">：
      <input type="text" v-model="min">
    </div>
    <button @click="show=true">Show TimePicker</button>
    <time-picker :show='show' @getTime='getTime' @close='close'></time-picker>
  </div>
</template>

<script>
import TimePicker from './components/TimePicker'

export default {
  name: 'DSQTimePicker',
  components: {
    TimePicker
  },
  data () {
    return {
      show: true,
      hour: '00',
      min: '00'
    }
  },
  methods: {
    getTime (hour, min) {
      this.hour = hour
      this.min = min
      this.show = false
    },
    close () {
      this.show = false
    }
  }
}
</script>

<style>
body, html{
  margin: 0;
  padding: 0;
  overflow-y: hidden;
}
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #dd7733;
  margin-top: 20px;
}
.time{
  padding: 10px;
}
input{
  width: 10%;
  padding: 0 1%;
  color: #dd7733;
  outline-style: none;
  border: 0px;
  border-bottom: 1px dashed #aaa;
  text-align: center;
}
button{
  margin: 10px;
  outline: none;
  padding: 5px 10px;
  color: #fff;
  background-color: #dd7733;
  border: 1px solid #ea6;
  border-radius: 5px;
  cursor: pointer;
}
</style>
