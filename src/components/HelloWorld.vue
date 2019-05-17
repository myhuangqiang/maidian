<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    <button @click="onClickBeforeButton">before button</button>
    <button @click="onClickAfterButton">after button</button>
    <button @click="onClickOnceButton">once button</button>
    <button @click="PerformButton(100)">another once button</button>
    <div class="boxs" @mouseover="move"></div>
  </div>
</template>

<script>
import { before, after, once, evolve, composeWith, createCounter, time, identity, track } from 'trackpoint-tools'
export default {
  name: 'HelloWorld',
  data () {
    return {
      msg: 'Welcome to Your Vue.js App'
    }
  },

  methods: {
    @track(before(() => console.log("click before")))
    onClickBeforeButton() {
      console.log("click before button", this.msg)
    },
    @track(after(() => console.log("click after")))
    onClickAfterButton() {
      console.log("click after button", this.msg)
    },
    @track(before(once(() => console.log("click once"))))
    onClickOnceButton() {
      console.log("once", this.msg)
    },
    @track(before(() => console.log("move before")))
    move() {
      console.log(222)
    },
    @track(composeWith(
      performinfo => (...args) => { 
        console.log('params', args);
        console.log('evalCount = ', performinfo.evalCount);
        // 如果是异步， 函数执行结束时间，以及函数结束返回结果
        performinfo.evalTime.then((time) => {
          console.log('evalTime = ', time)
        });
        performinfo.evalResult.then((result) => {
          console.log('evalResult = ', result);
        });
      }, 
      evolve({
        evalTime: time,
        evalCount: createCounter(),
        evalResult: identity
      })
    ))
    PerformButton(ns) {
      return new Promise(resolve => setTimeout(() => resolve('result'), ns))
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1, h2 {
  font-weight: normal;
}
.boxs {
  width: 150px;
  height: 150px;
  background-color: red;
}
</style>
