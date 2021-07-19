<template>
  <div class="wrapper">
    <div class="wrapper-content">
      <section>
        <div class="container">
          <!-- регистрация -->
          <button class="btn btnPrimary" @click="modalBool = !modalBool">
            Регистрация
          </button>
          <checkIn
            v-show="modalBool"
            @link="(modalBool = false), (modalBool1 = true)"
            @close="modalBool = false"
            @addUser="addUser"
            :users="users"
          />
          <!-- авторизация -->
          <button class="btn btnPrimary" @click="modalBool1 = !modalBool1">
            Авторизация
          </button>
          <enter
            v-show="modalBool1"
            @link="(modalBool1 = false), (modalBool = true)"
            @close="modalBool1 = false"
            :users="users"
            @loginUser="loginUser"
          />
          <!-- отображение пользователя -->
          <div v-for="(user, index) in users" :key="index">
            <div class="user" v-if="user.temp">
              <p> Вы успешно зашли в свои аккаунт.</p><br>
              <p>Ваш логин: {{ user.email }}</p>
              <p>Ваш пароль: {{ user.password }}</p>
              <button class="btn btnPrimary" @click="user.temp = false">
                Выйти из аккаунта
              </button>
            </div>
          </div>
        </div>
      </section>
    </div>
  </div>
</template>

<script>
import modal from "./components/UI/Modal.vue";
import checkIn from "./components/CheckIn.vue";
import enter from "./components/Enter.vue";

export default {
  name: "App",
  components: {
    modal,
    checkIn,
    enter,
  },
  data() {
    return {
      modalBool: false,
      modalBool1: false,

      users: [
        {
          email: "admin@mail.ru",
          password: "123456",
          temp: false,
        },
        {
          email: "roman@gmail.com",
          password: "123456",
          temp: false,
        },
        {
          email: "admin@gmail.ru",
          password: "123456",
          temp: false,
        },
      ],
    };
  },
  computed: {},
  methods: {
    addUser(i) {
       console.log("логин: " +i.email +"; пароль: "+ i.password +";");
      this.users.push({
        email: i.email,
        password: i.password,
        temp: false,
      });
    },
    loginUser(i) {
      this.users[i].temp = true;
    },
  },
};
</script>

<style>
.inner {
  display: flex;
  justify-content: space-between;
  align-items: center;
}
.app__title {
  font-size: 20px;
  font-weight: 700;
  margin-bottom: 30px;
}
.user {
  background: #b4b5bb;
  text-align: center;
  border-radius: 10px;
  padding: 20px 15px 0px;
}
</style>
