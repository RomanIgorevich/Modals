<template>
  <modals title="Регистрация" @close="clear">
    <div slot="checkIn">
      <form @submit.prevent="onSubmit">
        <!-- email -->
        <div :class="{ errorInput: $v.email.$error }">
          <label>Email</label>
          <input
            :class="{ errorInput: $v.email.$error }"
            v-model="email"
            @change="$v.email.$touch()"
          />
          <div class="errorText" v-if="!$v.email.required">
            Необходим email.
          </div>
          <div class="errorText" v-if="!$v.email.email">
            Email введен некоректно.
          </div>
          <div
            class="errorText"
            v-if="userVerificationBool && userVerificationString === email"
          >
            Пользователь с таким email уже существует.
          </div>
        </div>
        <!-- password -->
        <div :class="{ errorInput: $v.password.$error }">
          <label>Пароль</label>
          <input
            :class="{ errorInput: $v.password.$error }"
            v-model.trim="$v.password.$model"
          />
          <div class="errorText" v-if="!$v.password.required">
            Необходим пароль.
          </div>
          <div class="errorText" v-if="!$v.password.minLength">
            Пароль должен иметь не менее
            {{ $v.password.$params.minLength.min }} символов.
          </div>
        </div>
        <div :class="{ errorInput: $v.repeatPassword.$error }">
          <label>Повторите пароль</label>
          <input
            :class="{ errorInput: $v.repeatPassword.$error }"
            v-model.trim="$v.repeatPassword.$model"
          />
          <div class="errorText" v-if="!$v.repeatPassword.sameAsPassword">
            Пароли должны быть идентичными.
          </div>
          <tree-view
            :data="$v"
            :options="{ rootObjectKey: '$v', maxDepth: 2 }"
          ></tree-view>
        </div>
        <!-- <button> -->
        <button class="btn btnPrimary">Отправить</button>
        <div class="modal-link">
          <p @click="transitionBetweenModal">У меня уже есть аккаунт</p>
        </div>
      </form>
    </div>
  </modals>
</template>

<script>
import modals from "@/components/UI/Modal.vue";
import { required, sameAs, minLength, email } from "vuelidate/lib/validators";

export default {
  components: {
    modals,
  },
  props: {
    users: Array,
    required: true,
  },
  data() {
    return {
      email: "",
      password: "",
      repeatPassword: "",
      userVerificationBool: false,
      userVerificationString: "",
    };
  },
  validations: {
    email: {
      required,
      email,
    },
    password: {
      required,
      minLength: minLength(6),
    },
    repeatPassword: {
      sameAsPassword: sameAs("password"),
    },
  },
  methods: {
    clear() {
      this.email = "";
      this.password = "";
      this.repeatPassword = "";
      this.$v.$reset();
      this.$emit("close");
    },
    transitionBetweenModal() {
      this.email = "";
      this.password = "";
      this.repeatPassword = "";
      this.userVerification = false;
      this.userVerificationString = "";
      this.$v.$reset();
      this.$emit("link");
    },
    onSubmit() {
      this.$v.$touch();
      if (!this.$v.$invalid) {
        for (let r in this.users) {
          if (this.users[r].email === this.email) {
            // console.log("такой пользователь уже существует");
            this.userVerificationBool = true;
            this.userVerificationString = this.email;
            // console.log( this.userVerificationString);
            return;
          }
        }
        const user = {
          email: this.email,
          password: this.password,
        };
        // console.log(user);
        this.$emit("addUser", user);

        this.email = "";
        this.password = "";
        this.repeatPassword = "";
        this.userVerification = false;
        this.userVerificationString = "";
        this.$v.$reset();
        this.$emit("close");
      }
    },
  },
};
</script>


<style lang="scss">
.errorInput {
  border-color: #de4040;
}
.errorText {
  display: none;
}
.modal-link {
  border-top: 1px solid black;
  padding-top: 20px;
}
.errorInput .errorText {
  color: #de4040;
  display: block;
}
.modal-link p {
  text-decoration: underline;
  color: #494ce8;
  font-weight: 700;
  cursor: pointer;
}
</style>