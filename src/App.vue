<template>
  <p>贪吃蛇</p>
  <div class="gamBox">

    <div class="screen">
      <div class="snake">
        <div class="snake-head"
             :style="'left:'+snakeSeat.left+'px;top:'+snakeSeat.top+'px'">

          <span>😡</span>

        </div>
        <div class="snake-body">
          <div v-for="item in snakeLength"
               :key="item"
               :style="'left:'+item.left+'px;top:'+item.top+'px'"></div>
        </div>
      </div>

      <div class="food"
           :style="'left:'+foodSeat.left+'px;top:'+foodSeat.top+'px'">
        <span>🐷</span>
      </div>
    </div>
    <div class="integral">
      <div>等级（grade）{{register.grade}}</div>
      <div>分数（score）{{register.score}}</div>
    </div>
  </div>
</template>

<script setup>
import { onMounted, reactive, ref } from "vue";

/***
 * 公用变量
 * @foundationNumber 基础倍数：移动、食物随机位置量的倍数
 * @maxGrade 最大的等级
 */

const foundationNumber = 20
const maxGrade = 10



/***
 * 食物相关
 */
const randomNumber = (min, max) => {
  return Math.floor(Math.random() * (max - min)) + min
}
let foodSeat = reactive({
  top: 10,
  left: 10
})
const changeFoodSeat = () => {
  foodSeat.top = randomNumber(0, 20) * foundationNumber
  foodSeat.left = randomNumber(0, 20) * foundationNumber
}


/***
 * 等级、分数相关
 */
let register = reactive({
  score: 0,
  grade: 1
})
const chageScore = () => {
  register.score++
  if (register.grade < 10) {
    register.grade = Math.ceil(register.score / 10)
  }
}


/***
 * 蛇相关
 */

let direction = ''//移动方向
let snakeLength = ref([])
let snakeSeat = reactive({
  top: 0,
  left: 0
})
const handleWatchEnter = e => {
  let previousTop = snakeSeat.top
  let previousLeft = snakeSeat.left
  snakeLength.value.forEach((ele, index) => {

    let temporaryTop = ele.top
    let temporaryLeft = ele.left

    ele.top = previousTop
    ele.left = previousLeft
    previousTop = temporaryTop
    previousLeft = temporaryLeft
  });


  switch (direction) {
    case 'ArrowUp':
      snakeSeat.top -= 20
      break;
    case 'ArrowLeft':
      snakeSeat.left -= 20
      break;
    case 'ArrowRight':
      snakeSeat.left += 20
      break;
    case 'ArrowDown':
      snakeSeat.top += 20
      break;
  }

  if (snakeSeat.top == foodSeat.top && snakeSeat.left == foodSeat.left) {
    chageScore()
    changeFoodSeat()
    snakeLength.value.push({
      top: previousTop,
      left: previousLeft
    })

  }


  if (snakeSeat.top < 0 || snakeSeat.left < 0 || snakeSeat.left > 380 || snakeSeat.top > 380) {
    alert('撞墙身亡')
    snakeSeat.top = 0
    snakeSeat.left = 0
    direction = ''
    snakeLength.value = []
    init()

    return
  }


  let bodySeats = snakeLength.value.map(item => JSON.stringify(item))

  if (bodySeats.indexOf(JSON.stringify({ top: snakeSeat.top, left: snakeSeat.left })) != -1) {
    alert('把自己撞死了')
    snakeSeat.top = 0
    snakeSeat.left = 0
    snakeLength.value = []
    direction = ''
    init()


    return
  }

  setTimeout(() => {
    handleWatchEnter()
  }, 400 - (register.grade - 1) * 15);
}


onMounted(res => {
  document.addEventListener('keydown', (e) => {

    if (e.code == 'ArrowUp' || e.code == 'ArrowLeft' || e.code == 'ArrowRight' || e.code == 'ArrowDown') {



      direction = e.code
    }
  });
  init()
})


const init = () => {
  changeFoodSeat()
  handleWatchEnter()
}

</script>

<style lang="less" scoped>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
p {
  text-align: center;
  font-size: 23px;
  font-weight: 600;
}
.gamBox {
  width: 500px;
  height: 500px;
  border: 10px solid #000;
  margin: 20px auto;
  background-color: #4d7d2b;
  border-radius: 20px;
  display: flex;
  flex-direction: column;
  justify-content: space-around;
  align-items: center;
  //屏幕样式
  .screen {
    width: 400px;
    height: 400px;
    // background-color: #fff;
    border: 1px solid #000;
    position: relative;
    //蛇的样式
    .snake {
      //头
      .snake-head {
        width: 20px;
        height: 20px;
        border: 1px solid #4d7d2b;
        background-color: #000;
        position: absolute;
        top: 0;
        left: 0;
        span {
          font-size: 17px;
          position: absolute;
          left: -2.7px;
          top: -3px;
        }
      }
      .snake-body {
        div {
          position: absolute;
          top: 0;
          left: 0;
          width: 20px;
          height: 20px;
          border: 1px solid #4d7d2b;
          background-color: #000;
        }
      }
    }

    //食物的样式

    .food {
      width: 20px;
      height: 20px;
      // background-color: #fff;
      font-size: 8px;
      text-align: left;
      position: absolute;
      top: 10px;
      left: 0;
      span {
        font-size: 17px;
        position: absolute;
        left: -1.7px;
        top: -2px;
      }
    }
  }
  //下方等级、得分盒子
  .integral {
    width: 398px;
    height: 25px;
    display: flex;
    justify-content: space-between;
    font-size: 16px;
    font-family: "Trebuchet MS", "Lucida Sans Unicode", "Lucida Grande",
      "Lucida Sans", Arial, sans-serif;
    font-weight: 700;
  }
}
</style>