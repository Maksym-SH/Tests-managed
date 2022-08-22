<template>
  <section class="auth">
    <h5>Авторизация</h5>
    <form>
      <div class="input-group">
        <label for="select-role">Роль</label>
        <select class="input-auth" type="text" name="login" v-model="role">
          <option v-for="item in roles" :key="item.value" :value="item.value">{{
            item.name
          }}</option>
        </select>
      </div>
      <div class="input-group">
        <label for="password">Пароль</label>
        <div class="show-password">
          <input
            class="input-auth"
            type="password"
            name="password"
            v-model="password"
          />
          <span></span>
        </div>
      </div>
      <b-button
        class="auth-btn"
        :disabled="loginButtonDisabled"
        @click="checkAuth"
        variant="primary"
        >Войти</b-button
      >
    </form>
    <div class="login-info-group">
      <p class="login-info login-success" v-if="loginSuccess && !loginWrong">
        Вы успешно вошли в аккакунт
      </p>
      <p class="login-info login-wrong" v-if="loginWrong && !loginSuccess">
        Вы ввели неправильный пароль, повторите попытку
      </p>
      <p class="login-info" v-if="dataFail">
        Нет ответа от сервера, повторите попытку позже.
      </p>
    </div>
  </section>
</template>
<script>
export default {
  props: {
    dataFail: {
      type: Boolean,
      required: false,
      default: false,
    },
  },
  data() {
    return {
      role: "",
      password: "",
      roles: [
        { name: "Преподаватель", value: 1, password: "t123" },
        { name: "Студент", role: 2, password: "s123" },
      ],
      loginSuccess: false,
      loginWrong: false,
    };
  },
  methods: {
    checkAuth() {
      const user = this.roles.find((item) => item.value === this.role);
      if (user.password === this.password) {
        this.loginSuccess = true;
        setTimeout(() => this.$emit("auth", { name: user.name }), 2000);
      } else {
        this.loginWrong = true;
        setTimeout(() => {
          this.loginWrong = false;
        }, 5000);
      }
    },
  },
  computed: {
    loginButtonDisabled() {
      return !this.role && !this.password;
    },
  },
};
</script>
<style scoped>
.auth {
  padding-top: 150px;
  background-color: #1a1a23;
  color: #fff;
}
.auth-btn {
  width: 100%;
  display: block;
  margin: 0 auto;
}
.login-info {
  display: block;
  text-align: center;
}
.auth > h5 {
  margin-bottom: 20px;
  text-align: center;
}
.input-group {
  width: 100%;
  max-width: 290px;
  display: flex;
  justify-content: space-between;
  margin-bottom: 10px;
}
form {
  padding: 20px;
  border-radius: 8px;
  max-width: 300px;
  margin: 20px auto;
  border: 1px solid #fff;
  box-shadow: 0 0 5px #fff;
}
.input-auth {
  width: 200px;
  background-color: transparent;
  color: #fff;
  border-radius: 8px;
  padding-left: 10px;
  outline: none;
  border: 1px solid #fff;
}
select {
  padding-left: 5px !important;
}
option {
  color: #000;
}
.show-password {
  position: relative;
  display: inline;
}
.show-password span {
  width: 20px;
  height: 20px;
  border-radius: 50%;
  background-color: #a52e2e;
}
.login-success {
  color: #00c69b;
}
.login-wrong {
  color: #c00;
}
</style>
