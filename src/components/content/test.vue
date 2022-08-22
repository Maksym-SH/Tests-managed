<template>
  <div
    class="one-block"
    :style="`border:2px solid ${card.border}`"
    :class="[card.name === 'add' ? 'add-test' : 'test-ready']"
    @click="$emit('card-click', card.id)"
  >
    <span>{{ card.name !== "add" ? card.name : "+" }}</span>
    <span class="one-block__description" v-if="card.description">{{
      card.description
    }}</span>
    <b-button
      class="delete-test"
      variant="danger"
      @click.stop="$emit('confirm-task', card.id)"
      v-if="card.name !== 'add' && role === 'teacher'"
      >Удалить</b-button
    >
  </div>
</template>
<script>
export default {
  props: {
    card: {
      type: Object,
      default: () => {},
    },
    role: {
      type: String,
      default: "",
    },
  },
};
</script>
<style scoped>
.one-block {
  width: 31%;
  min-width: 200px;
  min-height: 150px;
  background-color: transparent;
  cursor: pointer;
  position: relative;
  text-align: center;
  border-radius: 10px;
  padding: 30px 0;
  margin: 10px;
}
.one-block__description {
  width: 100%;
  display: block;
  text-transform: capitalize;
  padding: 5px;
  font-size: 15px;
}
.delete-test {
  position: absolute;
  top: 0px;
  left: 0px;
  border-radius: 7px 0 10px 0;
  font-size: 11px;
}
.add-test {
  font-size: 50px;
  border: 2px dashed #318ce7;
}
.test-ready {
  color: #fff;
  font-size: 25px;
}
.add-test span {
  color: #318ce7 !important;
}
@media (max-width: 868px) {
  .one-block {
    width: 30%;
    max-width: 260px;
  }
}
@media (max-width: 660px) {
  .one-block {
    width: 100%;
    max-width: 280px;
  }
}
</style>
