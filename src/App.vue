<template>
  <div
    id="app"
    :class="{
      'background-teatcher': teacherRole,
      'background-auth': needAuth,
      'background-student': studentRole,
    }"
  >
    <template v-if="!preloader">
      <header-component
        :role="needAuth"
        @sign-out="showPopupSignOut"
      ></header-component>
      <main>
        <teather-component v-if="teacherRole"></teather-component>
        <student-component v-else-if="studentRole"></student-component>
        <authorization
          @auth="checkAuth"
          v-if="needAuth"
          :data-fail="failLoadResorse"
        ></authorization>
      </main>
      <footer-component :role="showFooterForRole"></footer-component>
      <popup
        :title="signOutTitle"
        v-if="showSignOutPopup"
        @cancel="showSignOutPopup = !showSignOutPopup"
        @confirm="signOut"
      ></popup>
    </template>
    <loader v-if="preloader"></loader>
  </div>
</template>

<script>
export default {
  components: {
    "header-component": () => import("./components/header"),
    "footer-component": () => import("./components/footer"),
    "teather-component": () =>
      import("./components/content/teacherSection.vue"),
    authorization: () => import("./components/actor"),
    popup: () => import("./components/popup"),

    loader: () => import("./components/preloader"),
    "student-component": () =>
      import("./components/content/studentSection.vue"),
  },
  data() {
    return {
      teacherRole: false,
      studentRole: false,
      needAuth: true,
      showSignOutPopup: false,
      signOutTitle: "Вы действительно хотите выйти из аккаунта?",
      preloader: false,
      failLoadResorse: false,
    };
  },
  computed: {
    showFooterForRole() {
      let role = "";
      if (this.teacherRole) {
        role = "Преподаватель";
      } else if (this.studentRole) {
        role = "Студент";
      }
      return role;
    },
  },
  methods: {
    showPopupSignOut() {
      this.showSignOutPopup = true;
    },
    signOut() {
      localStorage.removeItem("user");
      this.needAuth = true;
      this.showSignOutPopup = false;
      this.teacherRole = this.studentRole = false;
    },
    fetchData(url, body, type) {
      this.preloader = true;
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
    checkAuth(role) {
      localStorage.setItem("user", role.name);
      setTimeout(() => {
        this.needAuth = false;
        role.name === "Преподаватель"
          ? (this.teacherRole = true)
          : (this.studentRole = true);
      }, 2500);
    },
  },
  mounted() {
    if (localStorage.getItem("user")) {
      this.needAuth = false;
      if (localStorage.getItem("user") === "Преподаватель")
        this.teacherRole = true;
      else if (localStorage.getItem("user") === "Студент")
        this.studentRole = true;
      else this.needAuth = true;
      return;
    }
    this.needAuth = true;
  },
};
</script>

<style>
body {
  margin: 0;
}
#app {
  min-height: 100vh;
  display: flex;
  flex-direction: column;
}
.background-teatcher {
  background-color: #111d1d;
}
.background-auth {
  background-color: #1a1a23;
}
.background-student {
  background-color: #3691a8;
}
a {
  color: #f0ead6;
}
a:hover {
  text-decoration: none;
}
ul {
  list-style: none;
  padding: 0;
  margin: 0;
}
</style>
