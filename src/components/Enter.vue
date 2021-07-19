<template>
  <modals1 title="Авторизация" @close="clear">
    <div slot="enter">
      <form @submit.prevent="onSubmit1">
        <!-- email -->
        <div :class="{ errorInput: $v.email.$error,
        errorInput1: (userVerification1 && userVerificationString1 === email),
         }">
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
            Email введен некорректно.
          </div>
          <div
            class="errorText1"
            v-if="userVerification1 && userVerificationString1 === email"
          >
            Пользователь с таким email не существует.
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
        <!-- <button> -->
        <button class="btn btnPrimary">Войти</button>
        <div class="modal-link">
          <p @click="transitionBetweenModal">Мне нужен аккаунт</p>
        </div>
      </form>
    </div>
  </modals1>
</template>

<script>
import modals1 from "@/components/UI/Modal.vue";
import { required, minLength, email } from "vuelidate/lib/validators";

export default {
  components: {
    modals1,
  },
  props: {
    users: Array,
    required: true,
  },
  data() {
    return {
      email: "",
      password: "",
      userVerification1: false,
      userVerificationString1: "",
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
  },
  methods: {
    clear() {
      this.email = "";
      this.password = "";
      this.$v.$reset();
      this.$emit("close");
    },
    transitionBetweenModal() {
      this.userVerification1 = false;
      this.userVerificationString1 = "";
      this.email = "";
      this.password = "";
      this.$v.$reset();
      this.$emit("link");
    },
    onSubmit1() {
      this.$v.$touch();
      if (!this.$v.$invalid) {
        const user = {
          email: this.email,
          password: this.password,
        };

        for (let i in this.users) {
          //   console.log(this.users[i].email + " " + this.users[i].password);

          if (this.users[i].email === user.email) {
            if (this.users[i].password === user.password) {
                // console.log("мы все таки сюда попали")
              this.$emit("loginUser", i);
              this.$emit("close");
              this.userVerification1 = false;
              this.userVerificationString1 = "";
              this.email = "";
              this.password = "";
              this.$v.$reset();
              return;
            }
          } else if (this.users[i].email !== user.email) {
            //  console.log("пользователя с таким ником не существует");
            this.userVerification1 = true;
            this.userVerificationString1 = this.email;
            
          }
        }
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
.errorText1{
    display: none;
}
.errorInput1 .errorText1{
   color: #de4040;
  
  display: block;
}
.errorInput .errorText {
  color: #de4040;
  
  display: block;
}
.errorInput1 input{
    border: 1px solid #de4040;
}
.modal-link p {
  text-decoration: underline;
  color: #494ce8;
  font-weight: 700;
  cursor: pointer;
}
</style>