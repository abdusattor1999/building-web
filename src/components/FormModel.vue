<template>
  <div class="modal-wrapper">
    <div class="modal">
      <div class="modal__success">
        <div v-if="successText">
          <header class="modal__header">
            <h3 class="modal__title">{{ successText }}</h3>
          </header>
          <div class="success-checkmark">
            <div class="check-icon">
              <span class="icon-line line-tip"></span>
              <span class="icon-line line-long"></span>
              <div class="icon-circle"></div>
              <div class="icon-fix"></div>
            </div>
          </div>
          <div class="modal__btns-box">
            <Button class="modal__btn btn-green" @click="closeApp">Chiqish</Button>
          </div>
        </div>
        <div v-if="errorText || closeSign" class="centrski">
          <h3 class="modal__title">
            {{ errorText || closeSign }}
          </h3>
          <Button v-if="errorText" class="modal__btn btn-green" @click="closeModel"
            >Berkitish</Button
          >
          <div v-else-if="closeSign" class="modal__btns-box">
            <Button class="modal__btn btn-green" @click="closeApp">Chiqish</Button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
<script setup lang="ts">
defineProps(['successText', 'errorText', 'closeModel', 'closeSign'])
const closeApp = () => {
  window.Telegram.WebApp.close()
}
</script>
<style scoped>
.modal-wrapper {
  position: fixed;
  inset: 0;
  display: flex;
  align-items: center;
  justify-content: center;
  background: rgba(0, 0, 0, 0.5);
  z-index: 9999;
}
.modal {
  position: relative;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  width: 90%;
  height: 360px;
  background: #fff;
  padding: 25px;
  border-radius: 20px;
}
.btn-green {
  background: #4caf50;
  color: #fff;
  border: none;
  border-radius: 3px;
  box-shadow: 0 5px 15px rgba(76, 175, 80, 0.5);
}
.modal__header {
  text-align: center;
}
.modal__title {
  margin-bottom: 10px;
  text-align: center;
  font-size: clamp(1.2rem, calc(1.5vw + 1rem), 1.5rem);
  font-weight: 600;
  color: #333;
}
.modal__description {
  color: #555;
  font-size: 18px;
}
.modal__btns-box {
  display: flex;
  flex-wrap: wrap;
  align-items: center;
  justify-content: center;
  gap: 20px;
  margin-top: 15px;
}
.modal__btn {
  flex: 1;
  padding: 15px 25px;
  font-weight: 600;
  font-size: 17px;
  border-radius: 5px;
  transition: box-shadow 0.3s ease-out;
}
.modal__btn--cancel {
  background: rgb(216, 216, 216);
}
.modal__btn--empty {
  background: rgb(223, 47, 47);
  color: #fff;
}
.modal__text {
  margin: 20px 0;
  font-weight: 600;
  color: #333;
}

.modal__success {
  max-height: 400px;
  overflow-y: scroll;
}
.modal__btns-box {
  margin-top: 15px;
  text-align: center;
}
.modal__item {
  border: 2px solid red;
  color: red;
  font-weight: 600;
  border-radius: 5px;
  padding: 10px;
  cursor: pointer;
}
.modal__item:not(:last-child) {
  margin-bottom: 10px;
}

/* Success Message */
.success-checkmark {
  width: 80px;
  height: 115px;
  margin: 0 auto;
}
.check-icon {
  width: 80px;
  height: 80px;
  position: relative;
  border-radius: 50%;
  box-sizing: content-box;
  border: 4px solid #4caf50;
}
.check-icon::before {
  top: 3px;
  left: -2px;
  width: 30px;
  transform-origin: 100% 50%;
  border-radius: 100px 0 0 100px;
}
.check-icon::after {
  top: 0;
  left: 30px;
  width: 60px;
  transform-origin: 0 50%;
  border-radius: 0 100px 100px 0;
  animation: rotate-circle 4.25s ease-in;
}

.check-icon::before,
.check-icon::after {
  content: '';
  height: 100px;
  position: absolute;
  background: #ffffff;
  transform: rotate(-45deg);
}

.icon-line {
  height: 5px;
  background-color: #4caf50;
  display: block;
  border-radius: 2px;
  position: absolute;
  z-index: 10;
}
.icon-line.line-tip {
  top: 46px;
  left: 14px;
  width: 25px;
  transform: rotate(45deg);
  animation: icon-line-tip 0.75s;
}

.icon-line.line-long {
  top: 38px;
  right: 8px;
  width: 47px;
  transform: rotate(-45deg);
  animation: icon-line-long 0.75s;
}

.icon-circle {
  top: -4px;
  left: -4px;
  z-index: 10;
  width: 80px;
  height: 80px;
  border-radius: 50%;
  position: absolute;
  box-sizing: content-box;
  border: 4px solid rgba(76, 175, 80, 0.5);
}

.icon-fix {
  top: 8px;
  width: 5px;
  left: 26px;
  z-index: 1;
  height: 85px;
  position: absolute;
  transform: rotate(-45deg);
  background-color: #ffffff;
}
.centrski {
  /* display: flex; */
  text-align: center;
  align-items: center;
}
@keyframes rotate-circle {
  0% {
    transform: rotate(-45deg);
  }
  5% {
    transform: rotate(-45deg);
  }
  12% {
    transform: rotate(-405deg);
  }
  100% {
    transform: rotate(-405deg);
  }
}

@keyframes icon-line-tip {
  0% {
    width: 0;
    left: 1px;
    top: 19px;
  }
  54% {
    width: 0;
    left: 1px;
    top: 19px;
  }
  70% {
    width: 50px;
    left: -8px;
    top: 37px;
  }
  84% {
    width: 17px;
    left: 21px;
    top: 48px;
  }
  100% {
    width: 25px;
    left: 14px;
    top: 45px;
  }
}

@keyframes icon-line-long {
  0% {
    width: 0;
    right: 46px;
    top: 54px;
  }
  65% {
    width: 0;
    right: 46px;
    top: 54px;
  }
  84% {
    width: 55px;
    right: 0px;
    top: 35px;
  }
  100% {
    width: 47px;
    right: 8px;
    top: 38px;
  }
}
</style>
