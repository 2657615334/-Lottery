<script setup>
import {ref} from 'vue'
  let list =ref( [
        { label: "机会 +1", val: 1, img: "", index: 0, active: false },
        { label: "旺仔牛奶 ", val: 2, img: "", index: 1, active: false },
        { label: "AD钙奶", val: 3, img: "", index: 2, active: false },
        { label: "奶茶", val: 4, img: "", index: 3, active: false },
        { label: "果冻", val: 5, img: "", index: 4, active: false },
        { label: "薯片", val: 6, img: "", index: 5, active: false },
        { label: "巧克力", val: 7, img: "", index: 6, active: false },
        { label: "奥利奥", val: 8, img: "", index: 7, active: false },
        { label: "辣条", val: 9, img: "", index: 8, active: false },
        { label: "酸奶", val: 10, img: "", index: 9, active: false },
        { label: "棒棒糖", val: 11, img: "", index: 11, active: false },
        { label: "话梅", val: 12, img: "", index: 12, active: false },
      ])
  let isAnimate =ref(false)  // 为 true时代表正在抽奖，当前抽奖未结束时点击抽奖按钮无效
  let jumpIndex= ref(Math.floor(Math.random() * 13))  // 抽奖轮跳时的索引
  let jumpingTime=ref(0)  // 正在轮跳的时间
  let jumpingTotalTime= ref(0)  // 轮跳的时间总量，基于 duration 变量加上一个 0~1000 之间的随机数组成
  let jumpingChange=ref(0) // 轮跳速率峰值，基于 velocity 变量加上一个 0~3 之间的随机数组成
  let duration=ref(2500) // 持续时间
  let velocity=ref(350) // 速率
  let chanceNum = ref(5) // 抽奖次数
  let prizeList = ref([]) //存放奖品的数组
  let flag = ref(false) // 是否关闭弹窗
  function handleClick() {
      if(isAnimate.value) return;
      jumpingTime.value = 0;
      jumpingTotalTime.value = Math.random() * 1000 + duration.value;
      jumpingChange.value = Math.random() * 3 + velocity.value;
      if(chanceNum.value==0){
        alert('没有机会啦，小屁孩')
        return
      }else{
        chanceNum.value--
        animateRound(jumpIndex.value);
      }

    }
  function animateRound(index) {
      isAnimate.value = true; 

      if(jumpIndex.value < list.value.length - 1 )  jumpIndex.value ++;
      if(jumpIndex.value >= list.value.length - 1 )  jumpIndex.value = 0;

      jumpingTime.value += 100;  // 每一帧执行 setTimeout 方法所消耗的时间

      // 如果当前时间大于时间总量后, 退出动画,  清算奖品
      if(jumpingTime.value >= jumpingTotalTime.value){
        isAnimate.value = false; 
        if(index == 0) {
          chanceNum.value += 2
        }
        else{
          prizeList.value.push(list.value[index].label)
        }
        return
      }

      // 轮训动画
      if (index >= list.value.length-1) {
        index = 0;
        list.value[11].active = false;
        list.value[index].active = true;
        index -= 1;
      } else {
        list.value[index].active = false;
        list.value[index + 1].active = true;
      }

      setTimeout(() => {
        animateRound(index + 1);
      }, easeOut(jumpingTime.value, 0, jumpingChange.value, jumpingTotalTime.value));
    }

    /**
     * 缓动函数，由快到慢
     * @param {Num} t 当前时间
     * @param {Num} b 初始值
     * @param {Num} c 变化值
     * @param {Num} d 持续时间
     */
  function easeOut(t, b, c, d) {
      if ((t /= d / 2) < 1) return (c / 2) * t * t + b;
      return (-c / 2) * (--t * (t - 2) - 1) + b;
  }
  function closeDialog(){
    flag.value = false
  }
  function showPrize(){
    flag.value=true
  }
</script>

<template>
  <div class="home">
    <div class="showPrize" @click="showPrize">点击查看奖品</div>
    <div class="home-container">
      <div class="home-container-line">
        <div
          class="home-container-line-box"
          v-for="item in list.slice(0, 5)"
          :key="item.index"
          :class="{ selected: item.active }"
        >
          {{ item.label }}
        </div>
      </div>
      <div class="home-container-line">
        <div
          class="home-container-line-box"
          v-for="item in list.slice(11, 12)"
          :key="item.index"
          :class="{ selected: item.active }"
        >
          {{ item.label }}
        </div>
        <div class="home-container-line-btn" @click="handleClick" :disable="isAnimate">
          <span>剩余机会: {{chanceNum}}</span>
        </div>
        <div
          class="home-container-line-box"
          v-for="item in list.slice(5, 6)"
          :key="item.index"
          :class="{ selected: item.active }"
        >
          {{ item.label }}
        </div>
      </div>
      <div class="home-container-line">
        <div
          class="home-container-line-box"
          v-for="item in Array.prototype.reverse.call(list.slice(6, 11))"
          :key="item.index"
          :class="{ selected: item.active }"
        >
          {{ item.label }}
        </div>
      </div>
    </div>
    <div class="Dialogbox" v-if="flag">
      <div class="close-body">
        <div class="close-header">
          <div class="close-title">当前奖品</div>
        </div>
        <div class="close-main">
          <div v-for="v,index in prizeList" :key="index" class="close-main-item">
              {{v}}
          </div>
        </div>
        <div class="close-footer">
          <button size="small" @click="closeDialog">确认关闭</button>
        </div>
      </div>
    </div>
  </div>
</template>

<style lang="scss" scoped>
.center {
  display: flex;
  justify-content: center;
  align-items: center;
}
.home {
  padding: 0;
  margin: 0;
  width: 100%;
  height: calc(100vh - 16px);
  background-image: linear-gradient(25deg, #30007c, #464995, #4d83ad, #41bfc4);
  @extend .center;
  position: relative;
  .showPrize{
    position: absolute;
    top: 12%;
    left: 24%;
    font-size: 20px;
    &:hover{
      font-size: 25px;
      color: #fff;
      border-bottom: 1px solid #fff;
      transition: all 0.3s;

    }
  }
  &-container {
    width: 1000px;
    height: 600px;
    &-line {
      width: 100%;
      height: 198px;
      display: flex;
      &-box {
        flex: 1;
        overflow: hidden;
        margin: 5px 3px 5px 3px;
        @extend .center;
        background: #fff;
        transition: all .3s;
      }
      &-btn {
        position: relative;
        flex: 3;
        overflow: hidden;
        margin: 5px 3px 3px 3px;
        @extend .center;
        box-shadow: 0 1px 10px 0px #cf5531;
        background-image: linear-gradient(
          25deg,
          #cf5531,
          #d0853a,
          #cdaf43,
          #c4d84d
        );
        cursor: pointer;
        &:active {
          background-image: linear-gradient(
            25deg,
            #3f3e41,
            #6d6340,
            #9a8b39,
            #c9b629
          );
        }
        &::before {
          position: absolute;
          content: "点击抽奖";
          font-size: 2.5rem;
          color: #fff;
          font-weight: bold;
        }
        span{
          margin-top: 9rem;
          font-size: 1rem;
        }
      }
    }
  }
}
.selected {
  background: rgba($color: #f6e58d, $alpha: 0.5);
  animation-name: twinkle;
  animation-duration: 3s;
  animation-iteration-count: infinite;
}
@keyframes twinkle {
  0%   {background:#ffbe76;}
	100% {background:#f6e58d;}
}
.Dialogbox{
  position: fixed;
  left: 0;
  right: 0;
  top: 0;
  bottom: 0;
  z-index: 10;
  backdrop-filter: blur(2px);
  background-color: rgba(0, 0, 0, 0.3);
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  .close-body{
    width: 540px;
    height: 350px;
    display: flex;
    flex-direction: column;
    border-radius: 6px;
    overflow: hidden;
    background-color: rgba(255, 255, 255, 0.9);
    backdrop-filter: blur(20px);
    .close-header {
      display: flex;
      flex-direction: row;
      padding: 20px;
      font-size: 18px;
      align-items: center;
      .close-title {
        flex: 1;
        width: 0;
      }
    }
    .close-main {
      flex: 1;
      height: 0;
      display: flex;
      flex-wrap: wrap;
      &-item{
        @extend .center;
        width: 20%;
        height: 70px;
        background: rgb(238,174,202);
        background: radial-gradient(circle, rgba(238,174,202,1) 0%, rgba(148,187,233,1) 100%);
        margin: 10px;
        font-size: 20px;
      }
    }

    .close-footer {
      padding: 20px;
      text-align: right;
      button{
        border: none;
        width: 90px;
        height: 40px;
        color: #eee;
        background-image: linear-gradient( 135deg, #FCCF31 10%, #F55555 100%);
      }
    }
  }
}
</style>
