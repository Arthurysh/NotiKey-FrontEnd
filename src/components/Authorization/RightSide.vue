<template>
  <div class="container-auth">
    <div class="back-to-main-page">
      <img src="@/assets/arrow-left.svg" />
      <a href="#">На главную</a>
    </div>
    <div class="right-side-block">
      <div class="success-update-data-modal" id="successDataUpdateModal">
             <p>Вы успешно зарегистрировались</p>
             </div>
      <div class="right-side-block-items">
        <div v-if="this.isElVisible">
          <div class="registration-block">
            <h3 class="right-side-block-title">Welcome</h3>
            <div class="input-registration-block">
              <my-input
                :placeholderValue="'Имя'"
                id="firstNameField"
                @input="form.name = $event.target.value"
                v-bind:value="form.name"
              ></my-input>
              <span class="text-danger" v-if="errors.name">
                {{ errors.name[0] }}
              </span>
              <my-input
                :placeholderValue="'Фамилия'"
                id="lastNameField"
                @input="form.surname = $event.target.value"
                v-bind:value="form.surname"
              ></my-input>
              <span class="text-danger" v-if="errors.surname">
                {{ errors.surname[0] }}
              </span>
              <my-input
                :placeholderValue="'Номер телефона'"
                id="numberField"
                @input="form.phone = $event.target.value"
                v-bind:value="form.phone"
              ></my-input>
              <span class="text-danger" v-if="errors.phone">
                {{ errors.phone[0] }}
              </span>
              <my-input
                :placeholderValue="'Почта'"
                id="emailField"
                @input="form.email = $event.target.value"
                v-bind:value="form.email"
              ></my-input>
              <span class="text-danger" v-if="errors.email">
                {{ errors.email[0] }}
              </span>
              <my-input
                type="password"
                :placeholderValue="'Пароль'"
                id="passwordField"
                @input="form.password = $event.target.value"
                v-bind:value="form.password"
              ></my-input>
              <span class="text-danger" v-if="errors.password">
                {{ errors.password[0] }}
              </span>
              <my-input
                type="password"
                :placeholderValue="'Повторите пароль'"
                id="passwordField"
                @input="form.password_confirmation = $event.target.value"
                v-bind:value="form.password_confirmation"
              ></my-input>
              <span class="text-danger" v-if="errors.password_confirmation">
                {{ errors.password_confirmation[0] }}
              </span>
            </div>
            <div class="checkbox-registr">
              <my-check-box></my-check-box>
              <p>Я соглашаюсь с пользовательским соглашением</p>
            </div>
            <div class="input-registration-block-button">
              <my-button
                class="btn-registration-in-reg"
                @click.prevent="register"
                >Зарегистрироваться</my-button
              >
              <my-button class="btn-log-in-reg" @click="toggleElement"
                >Вход</my-button
              >
            </div>
          </div>
        </div>
        <div v-else >
          <div class="block-log-in">
            <h3 class="right-side-block-title">Welcome</h3>
            <div class="input-registration-block">
              <my-input
                :placeholderValue="'Логин'"
                id="passwordField"
                @input="loginform.email = $event.target.value"
                v-bind:value="loginform.email"
              ></my-input>
              <span class="text-danger" v-if="errors.email">
                {{ errors.email[0] }}
              </span>
              <my-input 
                type="password"
                :placeholderValue="'Пароль'"
                id="passwordField"
                @input="loginform.password = $event.target.value"
                v-bind:value="loginform.password"
              ></my-input>
              <span class="text-danger" v-if="errors.password">
                {{ errors.password[0] }}
              </span>
            </div>
            <div class="checkbox-registr">
              <my-check-box></my-check-box>
              <p>Запомнить меня</p>
            </div>
            <div class="input-registration-block-button">
              <my-button class="btn-log-in" @click.prevent="login"
                >Войти</my-button
              >
              <my-button class="btn-registration" @click="toggleElement"
                >Регистрация</my-button
              >
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import MyInput from "@/components/UI/MyInput.vue";
import MyButton from "@/components/UI/MyButton.vue";
import MyCheckBox from "@/components/UI/MyCheckBox.vue";
import User from "@/apis/User";

export default {
  components: {
    MyInput,
    MyButton,
    MyCheckBox,
    User,
  },
  props: {
    isRegistration: {
      type: Boolean,
      required: true,
    }
  },
  data() {
    return {
      form: {
        name: "",
        surname: "",
        phone: "",
        email: "",
        birthday: "0",
        password: "",
        password_confirmation: "",
        user_role: "User",
      },
      errors: [],

      loginform: {
        email: "",
        password: "",
      },

      isElVisible: this.isRegistration,
    };
  },
  methods: {
    toggleElement() {
      this.isElVisible = !this.isElVisible;
    },

    register() {
      User.register(this.form)
        .then((response) => {
          if (response.status == 200) {
            document
              .getElementById("successDataUpdateModal")
              .classList.add("success-update-data-modal-active");

            setTimeout(() => {
              document
                .getElementById("successDataUpdateModal")
                .classList.remove("success-update-data-modal-active");
            }, 3000);
          }
        })
        .catch((error) => {
          if (error.response.status === 422) {
            this.errors = error.response.data.errors;
          }
        });
        this.toggleElement()
    },

    login() {
      User.login(this.loginform)
        .then(() => {
          this.$root.$emit("Login", true);
          localStorage.setItem("auth", "true");
          this.$router.push('/Profile'); 
        })
        .catch((error) => {
          if (error.response.status === 422) {
            this.errors = error.response.data.errors;
          }
        });
    },
  },
};
</script>

<style>
.success-update-data-modal {
  padding: 20px 30px;
  position: absolute;
  top: 30px;
  text-align: center;
  border: 2px solid #6fa86e;
  background: #c0f1bf;
  color: #6fa86e;
  border-radius: 8px;
  z-index: 4;
  left: 50%;
  transform: translateX(-50%);
  display: none;
  opacity: 0;
  transition: opacity 0.5s;
}

.success-update-data-modal-active {
  display: block;
  opacity: 1;
  transition: opacity 0.5s;
}
.right-side-block {
  width: 25vw;
  float: right;
  text-align: center;
}
.right-side-block-title {
  font-size: 40px;
}
.input-registration-block {
  text-align: center;
}
.input-registration-block input {
  width: 300px;
  height: 45px;
  margin-top: 20px;
  text-align: left;
}
.input-registration-block-button button {
  width: 300px;
  height: 45px;
  border-radius: 5px;
  margin-top: 20px;
}
.checkbox-registr input {
  width: 15px;
  height: 15px;
  margin-right: 5px;
  display: inline-block;
}
.checkbox-registr {
  margin-top: 20px;
  text-align: center;
  width: 300px;
  margin-bottom: 10px;
  display: block;
  margin-left: auto;
  margin-right: auto;
}
.checkbox-registr p {
  display: inline-block;
  width: 270px;
  text-align: left;
  vertical-align: top;
}
.input-registration-block-button .btn-registration {
  background-color: #2657b1;
}
.input-registration-block-button .btn-registration-in-reg {
  background-color: #2657b1;
}
.btn-log-in-reg {
  background-color: #4599f5;
}
.block-log-in {
  position: absolute;
  top: 50%;
  transform: translate(0, -50%);
}
.registration-block {
  padding-top: 20px;
}
.text-danger{
  color: red;
  display: block;
}
@media screen and (min-width: 3000px) {
  .input-registration-block input {
  width: 400px;
  height: 55px;
  
}
.input-registration-block-button button {
  width: 400px;
  height: 55px;
}
.checkbox-registr input {
  width: 20px;
  height: 20px;
  margin-left: -100px;

}
}
@media screen and (min-width: 2720px) {
  .right-side-block{
    width: 20vw;
}
}
@media screen and (max-width: 2720px) {
  .right-side-block{
    width: 20vw;
}
}
@media screen and (max-width: 2390px) {
  .right-side-block  {
    width: 25vw;
  }
}
@media screen and (max-width: 1240px) {
  .right-side-block {
    width: 30vw;
  }
}
@media screen and (max-width: 1024px) {
  .right-side-block {
    width: 40vw;
  }
}
@media screen and (max-width: 770px) {
  .right-side-block {
    width: 50vw;
  }
}
@media screen and (max-width: 620px) {
  .right-side-block {
    width: 100vw;
    float: none;
    margin: 0;
    position: absolute;
    top: 50%;
    left: 50%;
    margin-right: -50%;
    transform: translate(-50%, -50%);
  }
  .container-auth {
    width: 100vw;
    height: 100vh;
    background: url("../../assets/background-regist.png") no-repeat;
    background-size: cover;
  }
  .right-side-block-title {
    color: white;
  }
  .checkbox-registr p {
    color: white;
  }
}
@media screen and (max-width: 310px) {
  .input-registration-block-button button {
    width: 250px;
  }
  .input-registration-block input {
    width: 250px;
  }
}
@media screen and (max-height: 641px) {
  .right-side-block-title {
    font-size: 25px;
  }
  .input-registration-block input {
    margin-top: 10px;
  }
  .input-registration-block-button button {
    margin-top: 10px;
  }
  .checkbox-registr {
    display: block;
    margin-left: auto;
    margin-right: auto;
  }
}
</style>