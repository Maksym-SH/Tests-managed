<template>
  <div class="back-loader">
    <h6>
      Загрузка
      <span v-for="item in 3" :key="item" :class="`show-point-${item}`">.</span>
    </h6>
  </div>
</template>
<style scoped>
.back-loader {
  background-color: rgba(255, 255, 255, 0.578);
  inset: 0;
  position: absolute;
  z-index: 100;
}
.back-loader > h6 {
  position: absolute;
  user-select: none;
  top: 50%;
  width: 100%;
  transform: translate(0, -50%);
  color: #fff;
  text-align: center;
}
.show-point-1 {
  animation: show-point 0.3s linear 0s infinite alternate-reverse none;
}
.show-point-2 {
  animation: show-point 0.6s linear 0s infinite alternate-reverse none;
}
.show-point-3 {
  animation: show-point 0.9s linear 0s infinite alternate-reverse none;
}
@keyframes show-point {
  0% {
    opacity: 0;
  }
  100% {
    opacity: 1;
  }
}
</style>
