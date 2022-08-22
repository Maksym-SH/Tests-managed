<template>
  <section class="student-section">
    <div class="container">
      <div class="info">
        <h4>Прохождение тестов</h4>
      </div>
      <div class="card-wrapper">
        <test
          v-for="(test, index) in testList"
          :key="index"
          :role="'student'"
          :card="test"
          @card-click="openPopupConfirmTest(test.id)"
        ></test>
      </div>
      <popup
        :title="popupTitle"
        v-if="popupActive"
        @confirm="startTest"
        @cancel="popupActive = false"
      ></popup>
      <b-modal
        ref="test"
        :title="popupTestTitle"
        hide-header-close
        no-close-on-backdrop
        ok-only
        @ok="closeTest"
        ok-title="Отправить"
        :ok-disabled="!showEndTime"
      >
        <div class="py-2">
          <h6 align="center" v-if="!showEndTime">
            Времени осталось: {{ timeForTest }}
          </h6>
          <h6 align="center" class="end-time" v-else-if="endTime">
            Время закончилось
          </h6>
        </div>
        <div class="py-2" v-if="!showEndTime">
          <div v-for="(item, index) in selectCard.questions" :key="index">
            <label class="w-100" :for="`question-${index}`">{{
              item.name
            }}</label>
            <select
              class="select-answer"
              :name="`question-${index}`"
              v-model="answerCollection[index]"
              ><option
                v-for="(answer, index) in item.answer"
                :key="index"
                :value="answer"
                >{{ answer }}</option
              ></select
            >
          </div>
        </div>
        <div class="py-2" v-else>
          <p>
            Ваши правильные ответы {{ rightAnswers }}/{{
              selectCard.questions.length
            }}
          </p>
          <h6 align="center">Правильные ответы</h6>
          <div v-for="(item, index) in selectCard.questions" :key="index">
            <p>
              <span
                class="answer-status"
                :class="[
                  item.answer[item.right] === answerCollection[index]
                    ? 'answer-status-right'
                    : 'answer-status-wrong',
                ]"
              ></span
              >Вопрос: {{ item.name }}. <b>ответ</b>
              {{ item.answer[item.right] }}
            </p>
          </div>
        </div>
        <b-button
          variant="success"
          class="popup__button-check"
          @click="showEndTest"
          :disabled="checkButtonDisabled"
          >Проверить</b-button
        >
      </b-modal>
    </div>
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
      testList: [],
      popupTitle: "",
      selectCard: {},
      popupActive: false,
      timeForTest: "",
      answerCollection: [],
      showEndTime: false,
      rightAnswers: 0,
      intervalId: 0,
      sendButtonActive: false,
      endTime: false,
      popupTestTitle: "",
    };
  },
  computed: {
    saveButtonDisabled() {
      if (Object.keys(this.selectCard).length) {
        return (
          this.answerCollection.length !== this.selectCard.questions.length
        );
      } else {
        return this.sendButtonActive;
      }
    },
    checkButtonDisabled() {
      if (Object.keys(this.selectCard).length && !this.showEndTime) {
        return (
          this.answerCollection.length !== this.selectCard.questions.length
        );
      } else {
        return true;
      }
    },
  },
  methods: {
    showEndTest() {
      this.showEndTime = true;
      this.selectCard.questions.forEach((item, index) => {
        if (
          item.answer[this.selectCard.questions[index].right] ===
          this.answerCollection[index]
        ) {
          this.rightAnswers++;
        }
      });
      clearInterval(this.intervalId);
      this.sendButtonActive = true;
    },
    showTime(targetDate) {
      let currentDate = new Date().getTime();
      let secondsLeft = (targetDate - currentDate) / 1000;
      let minutes = parseInt(secondsLeft / 60);
      minutes = minutes < 10 ? "0" + minutes : minutes;
      let seconds = parseInt(secondsLeft % 60);
      seconds = seconds < 10 ? "0" + seconds : seconds;
      this.timeForTest = `${minutes}:${seconds}`;
      if (this.timeForTest === "00:00") {
        clearInterval(this.intervalId);
        this.showEndTest();
        this.endTime = true;
      }
    },
    startTest() {
      this.popupActive = false;
      this.popupTestTitle = `Прохождение теста "${this.selectCard.name}"`;
      this.$refs.test.show();
      this.showTime();
      this.timeForTest =
        this.selectCard.time <= 9
          ? `0${this.selectCard.time}:00`
          : `${this.selectCard.time}:00`;
      let targetDate =
        new Date().getTime() + 1000 * 3600 * (this.selectCard.time / 60);
      let idInterval = setInterval(
        () => this.showTime(targetDate, idInterval),
        1000
      );
      this.intervalId = idInterval;
    },
    closeTest() {
      this.selectCard = {};
      this.showEndTime = this.endTime = this.sendButtonActive = false;
      this.rightAnswers = 0;
      this.answerCollection = [];
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
    openPopupConfirmTest(id) {
      this.selectCard = this.testList.find((item) => item.id === id);
      this.timeForTest =
        this.selectCard.time <= 9
          ? `0${this.selectCard.time}:00`
          : `${this.selectCard.time}:00`;
      this.popupTitle = `Хотите пройти тест "${this.selectCard.name}?"\nВыделенное время для прохождения теста ${this.timeForTest}`;
      this.popupActive = true;
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
};
</script>
<style scoped>
.student-section {
  width: 100%;
  padding-top: 100px;
  background-color: #3691a8;
}
.select-answer {
  width: 100%;
  padding: 5px;
  border-radius: 7px;
}
.student-section h4 {
  text-align: center;
  color: #fff;
}
.card-wrapper {
  margin-top: 20px;
  display: flex;
  justify-content: center;
  flex-wrap: wrap;
}
.end-time {
  color: #c00;
}
.answer-status {
  width: 20px;
  height: 20px;
  display: inline-block;
  margin: 0 4px -2px 0;
  border-radius: 50%;
}
.answer-status-right {
  background-color: rgb(9, 213, 43);
}
.answer-status-wrong {
  background-color: rgb(204, 0, 0);
}
.popup__button-check {
  position: absolute;
  bottom: -55px;
  right: 140px;
}
</style>
