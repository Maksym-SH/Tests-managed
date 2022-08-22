<template>
  <section class="teacher-section">
    <div class="info">
      <h4>Создание тестов</h4>
    </div>
    <div class="card-wrapper-position">
      <div class="card-wrapper">
        <test
          v-for="(test, index) in testList"
          :key="index"
          :card="test"
          :role="'teacher'"
          @card-click="openCard"
          @confirm-task="openPopupdeleteTest"
        ></test>
      </div>
    </div>
    <b-modal
      ref="test"
      :title="popupName"
      :ok-disabled="newTestButtonDisable"
      @ok="testAction"
      hide-header-close
    >
      <div class="pt-2">
        <label for="test-name">Новое имя теста</label>
        <input
          type="text"
          class="w-100"
          name="test-name"
          v-model="newTestName"
        />
      </div>
      <div class="pt-2">
        <label for="test-desc">Описание теста (Не обязательно)</label>
        <input type="text" class="w-100" name="test-desc" v-model="testDesc" />
      </div>
      <div class="pt-2">
        <label for="test-time">Время для прохождения теста в минутах</label>
        <div class="small-input-wrapper">
          <input
            type="number"
            class="small-input"
            name="test-time"
            min="1"
            v-model="testTime"
          />
          <span class="ml-2">минут</span>
        </div>
      </div>
      <div class="pt-3 small-input-wrapper">
        <label for="select-border-color">Выберите цвет рамки</label>
        <input
          type="color"
          name="select-border-color"
          class="small-input input-color"
          v-model="testBorderColor"
        />
      </div>
      <div class="pt-2">
        <div>
          <label for="input-new-question">Новый вопрос</label>
          <input
            autocomplete="false"
            type="text"
            class="w-100"
            name="input-new-question"
            v-model="testName"
          />
        </div>
        <div>
          <label>Варианты ответа</label>
          <ul>
            <li v-for="item of 4" :key="item" class="pt-2">
              <input
                autocomplete="false"
                name="check"
                :value="item"
                type="radio"
                class="test-radio"
                v-model="checkBoxQuestions"
              />
              <input
                type="text"
                class="w-75"
                v-model="testQuestions[item - 1]"
              />
            </li>
          </ul>
        </div>
        <b-button
          variant="success"
          size="sm"
          :disabled="addTestDisabled"
          @click="addTest()"
          >Добавить новый вопрос</b-button
        >
        <ul v-if="showAnswer" class="mt-2">
          <li v-for="(item, index) in selectCard.questions" :key="index">
            Вопрос теста:<br />
            - {{ item.name }}<br />
            Варианты ответа
            <h6 v-for="(question, idxQuest) in item.answer" :key="idxQuest">
              - {{ question }}
            </h6>
            <p class="right-answer">
              Правильный ответ: {{ item.answer[item.right] }}<br />
              <b-button
                size="sm"
                class="mt-2"
                variant="danger"
                @click="deleteQuestion(index)"
                >Удалить вопрос</b-button
              >
            </p>
          </li>
        </ul>
        <p v-else class="mt-2">Вопросы отсутствуют</p>
      </div>
    </b-modal>
    <popup
      :title="popupTitle"
      v-if="popupActive"
      @confirm="deleteTest"
      @cancel="popupActive = false"
    ></popup>
  </section>
</template>

<script>
export default {
  components: {
    test: () => import("./test.vue"),
    popup: () => import("../popup"),
  },
  data() {
    return {
      testList: [
        {
          id: 0,
          name: "add",
          questions: [],
          time: 0,
        },
      ],
      selectCard: {},
      testQuestions: [],
      checkBoxQuestions: 0,
      testName: "",
      newTestName: "",
      popupName: "",
      testTime: 0,
      popupActive: false,
      popupTitle: "",
      testDesc: "",
      testBorderColor: "#000000",
    };
  },
  methods: {
    testAction() {
      this.selectCard.id ? this.saveTest() : this.addNewTest();
    },
    openCard(id) {
      this.selectCard = this.testList.find((item) => item.id === id);
      if (id) {
        this.popupName = `Изменение теста "${this.selectCard.name}"`;
      } else {
        this.popupName = "Создание нового теста";
      }
      this.testTime = this.selectCard.time;
      this.testDesc = this.selectCard.description;
      if (this.selectCard.name !== "add")
        this.newTestName = this.selectCard.name;
      this.testBorderColor = this.selectCard.border;
      this.$refs.test.show();
    },
    saveTest() {
      if (this.newTestName) {
        this.selectCard.name = this.newTestName;
        this.newTestName = "";
      }
      if (this.testDesc) {
        this.selectCard.description = this.testDesc;
      }
      this.selectCard.border = this.testBorderColor;
      this.selectCard.time = this.testTime;
      let idForFetch =
        this.selectCard.id === 0 ? this.testList.length : this.selectCard.id;
      this.fetchData(
        `http://localhost:3000/tests/${idForFetch}`,
        JSON.stringify(this.selectCard),
        "PUT"
      );
      this.selectCard = {};
    },
    addTest() {
      this.selectCard.questions.push({
        name: this.testName,
        answer: this.testQuestions,
        right: this.checkBoxQuestions - 1,
        time: this.testTime,
      });
      this.testName = "";
      this.testQuestions = [];
      this.checkBoxQuestions = 0;
    },
    deleteQuestion(index) {
      this.selectCard.questions.splice(index, 1);
    },
    openPopupdeleteTest(id) {
      this.selectCard = this.testList.find((item) => item.id === id);
      this.popupActive = true;
      this.popupTitle = "Вы действительно хотите удалить выбранный вами тест?";
    },
    deleteTest() {
      this.popupActive = false;
      this.fetchData(
        `http://localhost:3000/tests/${this.selectCard.id}`,
        "",
        "DELETE"
      ).then(() => {
        this.testList = this.testList.filter(
          (item) => item.id !== this.selectCard.id
        );
      });
    },
    fetchData(url, body, type) {
      if (type !== "GET") {
        return fetch(url, {
          method: type,
          body,
          headers: {
            "Content-type": "application/json; charset=UTF-8",
          },
        });
      } else {
        return fetch(url);
      }
    },
    addNewTest() {
      this.selectCard = Object.assign({}, this.selectCard);
      this.selectCard = {
        ...this.selectCard,
        name: this.newTestName,
        id: this.testList.length,
      };
      this.fetchData(
        "http://localhost:3000/tests",
        JSON.stringify(this.selectCard),
        "POST"
      ).then(() => this.testList.unshift(this.selectCard));
    },
  },
  async mounted() {
    await this.fetchData("http://localhost:3000/tests", "", "GET")
      .then((item) => item.json())
      .then((item) => {
        item.forEach((item) => {
          this.testList.unshift(item);
        });
      });
  },
  computed: {
    addTestDisabled() {
      return (
        this.testQuestions.length <= 3 ||
        !this.checkBoxQuestions ||
        !this.testName ||
        !this.testTime
      );
    },
    newTestButtonDisable() {
      return (
        !this.selectCard.name ||
        !this.selectCard.questions.length ||
        !this.testTime
      );
    },
    showAnswer() {
      if (this.selectCard.questions) {
        return this.selectCard.questions.length;
      } else {
        return false;
      }
    },
  },
};
</script>
<style scoped>
.teacher-section {
  width: 100%;
  padding-top: 100px;
  background-color: #111d1d;
}
.info {
  text-align: center;
  color: #fff;
}
.card-wrapper-position {
  margin-top: 20px;
  display: flex;
  justify-content: center;
}
.card-wrapper {
  display: flex;
  flex-wrap: wrap;
  width: 100%;
}
@media (max-width: 660px) {
  .card-wrapper {
    flex-direction: column;
    align-items: center;
  }
}
.popup {
  color: #000 !important;
}
.test-radio {
  margin-right: 10px;
}
.right-answer {
  padding-bottom: 10px;
  border-bottom: 1px solid #038484;
}
.small-input-wrapper {
  display: flex;
  flex-wrap: nowrap;
  align-items: flex-end;
}
.small-input {
  width: 40px;
  height: 30px;
}
input {
  border-radius: 5px;
  outline: none;
  border: 1px solid #000;
}
.input-color {
  border: none;
  background-color: transparent;
  margin: 0 0 2px 4px;
}
</style>
