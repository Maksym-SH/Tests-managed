<template>
  <header class="header">
    <div class="logo-name">
      <span><b>Система</b> создания тестов</span>
    </div>
    <div class="auth" v-if="!role">
      <b-button variant="info" @click="$emit('sign-out')"
        >Выйти из аккаунта</b-button
      >
    </div>
  </header>
</template>

<script>
export default {
  props: {
    role: {
      type: Boolean,
      required: false,
      default: false,
    },
  },
};
</script>

<style scoped>
.header {
  background-color: #000000;
  z-index: 10;
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 10px;
  color: #fff;
}
</style>
