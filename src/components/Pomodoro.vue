<template>
  <div class="container">
    <svg version="1.1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px" style="display: none">
      <symbol id="wave">
        <path d="M420,20c21.5-0.4,38.8-2.5,51.1-4.5c13.4-2.2,26.5-5.2,27.3-5.4C514,6.5,518,4.7,528.5,2.7c7.1-1.3,17.9-2.8,31.5-2.7c0,0,0,0,0,0v20H420z"></path>
        <path d="M420,20c-21.5-0.4-38.8-2.5-51.1-4.5c-13.4-2.2-26.5-5.2-27.3-5.4C326,6.5,322,4.7,311.5,2.7C304.3,1.4,293.6-0.1,280,0c0,0,0,0,0,0v20H420z"></path>
        <path d="M140,20c21.5-0.4,38.8-2.5,51.1-4.5c13.4-2.2,26.5-5.2,27.3-5.4C234,6.5,238,4.7,248.5,2.7c7.1-1.3,17.9-2.8,31.5-2.7c0,0,0,0,0,0v20H140z"></path>
        <path d="M140,20c-21.5-0.4-38.8-2.5-51.1-4.5c-13.4-2.2-26.5-5.2-27.3-5.4C46,6.5,42,4.7,31.5,2.7C24.3,1.4,13.6-0.1,0,0c0,0,0,0,0,0l0,20H140z"></path>
      </symbol>
    </svg>
    <div class="rest" v-on:click="showModal">{{ rest }}</div>
    <button class="start"
            v-bind:class="{ active: startClick }"
            v-on:click="handleClick">{{ isPause ? '开始' : '暂停' }}</button>
    <div class="water" v-bind:style="{ transform: 'translate(0px, '+ down +'%)', overflow: restSeconds === 0 ? 'hidden' : '' }">
      <svg class="wave back" viewBox="0 0 560 20">
        <use xmlns:xlink="http://www.w3.org/1999/xlink" xlink:href="#wave"></use>
      </svg>
      <svg class="wave front" viewBox="0 0 560 20">
        <use xmlns:xlink="http://www.w3.org/1999/xlink" xlink:href="#wave"></use>
      </svg>
    </div>
    <div class="modal" v-bind:class="{ active: isModalOpen, leaving: isLeaving }">
      <div class="modal-body">
        <span class="close" v-on:click="hideModal">
          <i class="el-icon-close"></i>
        </span>
        <div class="operate-container">
          <!-- <button class="operate" v-on:click="handleBreak(1)">+</button> -->
          <div class="textfield">
            <span class="label">休息时间</span>
            <el-input-number class="input-number" v-model="breakLen" @change="handleBreak" :min="1" :max="60" label=""></el-input-number>
            <!-- <input v-model="breakLen" type="number"> -->
            <div class="underline"></div>
          </div>
          <!-- <button class="operate" v-on:click="handleBreak(-1)">-</button> -->
        </div>
        <div class="operate-container">
          <!-- <button class="operate" v-on:click="handleSession(1)">+</button> -->
          <div class="textfield">
            <span class="label">专注时间</span>
            <el-input-number class="input-number" v-model="session" @change="handleSession" :min="1" :max="60" label=""></el-input-number>
            <!-- <input v-model="session" type="number"> -->
            <div class="underline"></div>
          </div>
          <!-- <button class="operate" v-on:click="handleSession(-1)">-</button> -->
        </div>


      </div>
      <div class="modal-bg" v-on:click="hideModal"></div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'pomodoro',
  props: ['onSessionFinish', 'onBreakFinish'],
  data() {
    return {
      isModalOpen: false,
      isLeaving: false,
      down: 0,
      startClick: false,
      breakLen: 5,
      session: 25,
      isBreak: false,
      isPause: true,
      rest: '25:00',
      restSeconds: 25 * 60
    }
  },
  mounted: () => {
    const textfieldList = document.querySelectorAll('.textfield')
    textfieldList.forEach((element) => {
      const inputNode = element.querySelector('input')
      const underlineNode = element.querySelector('.underline')
      inputNode.addEventListener('focus', () => {
        underlineNode.className = 'underline focus'
      }, false)
      inputNode.addEventListener('blur', () => {
        underlineNode.className = 'underline'
      }, false)
    })
  },
  methods: {
    showModal() {
      if (!this.isPause) return
      this.isModalOpen = true
    },
    hideModal() {
      const that = this
      this.isLeaving = true
      setTimeout(() => {
        that.isLeaving = false
        that.isModalOpen = false
      }, 300)
    },
    changeStartAction() {
      this.startClick = !this.startClick
    },
    handleBreak(value) {
      if (!this.isPause) return
      this.breakLen = value
    },
    handleSession(value) {
      if (!this.isPause) return
      this.session = value
      this.rest = `${(this.session / 100).toFixed(2).split('.')[1]}:00`
      this.restSeconds = this.session * 60
      this.down = 0
    },
    handleClick() {
      const { isPause } = this
      if (isPause) {
        this.isPause = !isPause
        this.changeStartAction()
        setTimeout(this.changeStartAction, 300)
        this.timer = setInterval(this.setRestTime, 1000)
      } else {
        clearInterval(this.timer)
        this.isPause = !isPause
        this.changeStartAction()
        setTimeout(this.changeStartAction, 300)
      }
    },
    setRestTime() {
      const { restSeconds } = this
      if (restSeconds === 0) {
        if (this.timer) { clearInterval(this.timer) }
        // TODO 添加专注和休息完成回调
        if (this.isBreak) {
          if (this.onBreakFinish) this.onBreakFinish()
          this.isBreak = !this.isBreak
          this.restSeconds = this.session * 60
          this.rest = this.session
          this.timer = setInterval(this.setRestTime, 1000)
        } else {
          if (this.onSessionFinish) this.onSessionFinish()
          this.isBreak = !this.isBreak
          this.restSeconds = this.breakLen * 60
          this.rest = this.breakLen
          this.timer = setInterval(this.setRestTime, 1000)
        }
        this.setRestTime()
        return
      }
      const newRestSeconds = restSeconds - 1
      const restMinu = Math.floor(newRestSeconds / 60)
      const restSec = Math.floor(newRestSeconds % 60)
      this.rest = `${(restMinu / 100).toFixed(2).split('.')[1]}:${(restSec / 100).toFixed(2).split('.')[1]}`
      this.restSeconds = newRestSeconds
      if (this.isBreak) {
        this.down = 100 - Math.floor((newRestSeconds / (this.breakLen * 60)) * 100)
      } else {
        this.down = 100 - Math.floor((newRestSeconds / (this.session * 60)) * 100)
      }
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

input[type=number]::-webkit-inner-spin-button,
input[type=number]::-webkit-outer-spin-button {
    -webkit-appearance: none;
    margin: 0;
}

.container {
  position: relative;
  display: flex;
  justify-content: center;
  align-items: center;
  width: 100%;
  height: 100%;
  overflow: hidden;
}

.rest {
  position: absolute;
  top: 50%;
  left: 0;
  display: flex;
  justify-content: center;
  align-items: center;
  width: 100%;
  height: 84px;
  font-size: 6rem;
  font-weight: 300;
  line-height: 1;
  transform: translateY(-50%);
  z-index: 9;
  cursor: pointer;
}
.start {
  position: absolute;
  width: 50%;
  bottom: 8%;
  left: 25%;
  color: #fff;
  border: 2px solid #fff;
  border-radius: 1.5rem;
  text-align: center;
  text-transform: uppercase;
  font-size: 1.2rem;
  padding: .7rem 0;
  background: none;
  font-weight: 400;
  cursor: pointer;
  z-index: 10;
}

.start:before {
  content: '';
  position: absolute;
  width: 100%;
  height: 100%;
  top: 0;
  left: 0;
  opacity: 0;
  border: inherit;
  border-radius: inherit;
  transition: all .2s;
}

.start.active:before {
  animation: button-scale .3s;
}


.start:focus {
  outline: none;
}

.modal {
  display: none;
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-color: transparent;
  z-index: 99;
}
.modal.active {
 display: block;
}
.modal-body {
  position: absolute;
  top: 50%;
  left: 50%;
  padding: 30px 45px;
  display: flex;
  flex-direction: column;
  justify-content: space-around;
  width: 80%;
  height: 220px;
  font-size: 3rem;
  font-weight: 300;
  transition: all .3s;
  transform: translate(-50%, -25%);
  background-color: #fff;
  box-shadow: 0 10px 50px rgba(0, 0, 0, 0.3);
  border-radius: 5px;
  z-index: 110;
  opacity: 0;
}

.active .modal-body {
  animation: modal-in .3s;
  opacity: 1;
  transform: translate(-50%, -50%);
}
.active.leaving .modal-body {
  animation: modal-out .3s;
}
.label {
  position: absolute;
  left: 0;
  top: -17px;
  font-size: 10px;
  color: #dadada;
}
.close {
  position: absolute;
  top: 15px;
  right: 15px;
  display: flex;
  justify-content: center;
  align-items: center;
  color: #555;
  font-size: 1.5rem;
  cursor: pointer;
  transition: all .3s;
  transform: rotate(0) scale(1, 1);
}
.close:hover {
  transform: rotate(180deg) scale(1.1, 1.1);
}

.modal-bg {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-color: transparent;
  z-index: 100;
}

.operate-container {
  display: flex;
  justify-content: space-around;
  align-items: center
}
.textfield {
  position: relative;
  display: flex;
  justify-content: center;
  align-items: center;
  width: 100%;
  height: 100%;
}
.textfield input {
  width: 100%;
  height: 100%;
  font-size: 2.2rem;
  text-align: center;
  color: #212121;
  border: 0;
  outline: none;
  background: transparent;
  transition: all .2s;
}
.underline {
  content: '';
  position: absolute;
  bottom: 0;
  display: block;
  width: 90%;
  height: 1px;
  background-color: #dadada;
  transition: all .2s;
  transform: translateY(4px);
}

.underline.focus {
  width: 100%;
  background-color: #9c9c9c;
  transform: translateY(1px);
}
.water {
  position: absolute;
  height: 100%;
  width: 100%;
  z-index: 5;
  top: 0;
  left: 0;
  background: #32bafa;
  transition: all .2s linear;
  transform: translate(0, 0);
}
.wave {
  width: 200%;
  position: absolute;
  bottom: 100%;
}
.wave.back {
  right: 0;
  fill: #2c7fbe;
  animation: wave-back 2s infinite linear;
}
.wave.front {
  left: 0;
  fill: #32bafa;
  margin-bottom: -1px;
  animation: wave-front 1s infinite linear;
}

@keyframes modal-in {
  0% {
    opacity: 0;
    transform: translate(-50%, -25%);
  }
  100% {
    opacity: 1;
    transform: translate(-50%, -50%);
  }
}

@keyframes modal-out {
  0% {
    opacity: 1;
    transform: translate(-50%, -50%);
  }
  100% {
    opacity: 0;
    transform: translate(-50%, -25%);
  }
}

@keyframes button-scale {
  0% {
    opacity: 1;
    transform: scale(1);
  }
  100% {
    opacity: 0;
    transform: scale(1.4);
  }
}


@keyframes wave-front {
  0% {
    transform: translate(0, 0);
  }
  100% {
    transform: translate(-50%, 0);
  }
}

@keyframes wave-back {
  0% {
    transform: translate(0, 0);
  }
  100% {
    transform: translate(50%, 0);
  }
}


</style>
