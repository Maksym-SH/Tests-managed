<template>
  <transition name="popup-show">
    <div class="popup" v-if="title" @click.self="buttonAction('cancel')">
      <div class="popup-window">
        <h6 class="mb-2">{{ title }}</h6>
        <div class="popup-group-button">
          <b-button @click="buttonAction('cancel')">Отмена</b-button>
          <b-button variant="primary" @click="buttonAction('confirm')"
            >Подтвердить</b-button
          >
        </div>
      </div>
    </div>
  </transition>
</template>
<script>
export default {
  props: {
    title: {
      type: String,
      required: false,
      default: "",
    },
  },
  methods: {
    buttonAction(str) {
      this.$emit(str);
    },
  },
};
</script>
<style scoped>
.popup {
  position: absolute;
  background-color: rgba(0, 0, 0, 0.752);
  inset: 0;
  z-index: 100;
}
.popup-show-enter-active,
.popup-show-leave-active {
  transition: opacity 0.2s ease;
}
.popup-show-enter,
.popup-show-leave-to {
  opacity: 0;
}
.popup-window {
  padding: 40px;
  width: 100%;
  max-width: 310px;
  border-radius: 10px;
  background-color: #fff;
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
}

.popup-window h6 {
  text-align: center;
  width: calc(100% + 50px);
  margin-left: -25px;
}
.popup-group-button {
  display: flex;
  margin-top: 20px;
  justify-content: center;
}
.popup-group-button button {
  min-width: 121px;
  margin: 3px;
}
</style>
