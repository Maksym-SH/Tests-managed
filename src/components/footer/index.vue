<template>
  <footer class="footer" v-if="role">
    <h6>
      Правила {{ role === "Преподаватель" ? "создания" : "проходжения" }} тестов
    </h6>
    <div class="footer__info-wrapper">
      <div class="info-item" v-for="item in selectRules" :key="item.number">
        <span>{{ item.number }}</span>
        <p>{{ item.text }}</p>
      </div>
    </div>
    <div class="footer-copyright">
      <h6>&copy; Все права защищены</h6>
    </div>
  </footer>
</template>
<script>
export default {
  props: {
    role: {
      type: String,
      require: true,
      default: "",
    },
  },
  watch: {
    role(value) {
      this.checkRole(value);
    },
  },
  methods: {
    checkRole(value) {
      value === "Преподаватель"
        ? (this.selectRules = this.rulesForTeacher)
        : (this.selectRules = this.rulesForStudent);
    },
  },
  data() {
    return {
      selectRules: [],
      rulesForTeacher: [
        {
          number: 1,
          text: "Чтобы создать тест, нажмите на синий блок добавления теста.",
        },
        {
          number: 2,
          text: "Введите данные теста в открытом окне создания теста.",
        },
        {
          number: 3,
          text:
            "Введите 4 варианта ответа на вопрос, выберите правильный ответ.",
        },
        {
          number: 4,
          text: 'Нажмите кнопку "Добавить новый вопрос", нажмите кнопку "ОК".',
        },
      ],
      rulesForStudent: [
        {
          number: 1,
          text: "Выберите один из тестов созданных вашим преподавателем.",
        },
        {
          number: 2,
          text: "В открытом окне подтвердите проходжение выбранного теста.",
        },
        {
          number: 3,
          text:
            "На прохождение теста будет выделено время, указанное преподавателем.",
        },
        {
          number: 4,
          text:
            "После прохождения теста, будет выведен результат и оценка ваших ответов.",
        },
      ],
    };
  },
  mounted() {
    this.checkRole(this.role);
  },
};
</script>
<style scoped>
.footer {
  user-select: none;
  display: block;
  margin-top: auto;
  padding-top: 40px;
  width: 100%;
  background-color: transparent;
}
.footer > h6 {
  text-align: center;
  color: #fff;
}
.footer__info-wrapper {
  width: 100%;
  display: flex;
  justify-content: center;
  flex-wrap: wrap;
}
.info-item {
  width: 200px;
  margin: 10px;
  text-align: center;
  color: #fff;
  font-weight: 300;
}
.footer-copyright {
  width: 100%;
  padding: 10px 0;
  border-top: 1px solid #fff;
  color: #fff;
}
.footer-copyright > h6 {
  text-align: center;
}
</style>
