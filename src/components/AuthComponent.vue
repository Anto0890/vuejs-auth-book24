<template>
  <div class="login-form">
    <form action="/examples/actions/confirmation.php" method="post">
      <h2 class="text-center">Log in</h2>
      <div class="form-group">
        <label class="pull-left">Email</label>
        <input
          type="text"
          class="form-control"
          v-model="email"
          placeholder="username@domain.ru"
          required="required"
        />
      </div>
      <div class="form-group" v-if="doRegister">
        <label class="pull-left">ФИО</label>
        <input
          type="text"
          class="form-control"
          v-model="fio"
          placeholder="Фамилия Имя Отчество"
          required="required"
        />
      </div>

      <div class="form-group" v-if="doLogin">
        <label class="pull-left">Пароль</label>
        <input
          type="password"
          class="form-control"
          v-model="password"
          placeholder="Password"
          required="required"
        />
      </div>

      <div class="form-group" v-if="doRegister">
        <label class="pull-left">Телефон</label>
        <input
          type="text"
          class="form-control"
          v-model="phone"
          placeholder="89123456789"
          required="required"
        />
      </div>

      <div class="form-group">
        <button
          type="submit"
          class="btn btn-primary btn-block"
          v-if="!forgotPassword && !doRegister"
          v-on:click="login"
        >Войти</button>
      </div>

      <div class="form-group">
        <button
          type="submit"
          class="btn btn-primary btn-block"
          v-if="doRegister"
          v-on:click="register"
        >Зарегистрироваться</button>
      </div>

      <div class="form-group">
        <button
          type="submit"
          class="btn btn-primary btn-block"
          v-if="forgotPassword"
          v-on:click="sendPassword"
        >Отправить пароль на email</button>
      </div>

      <div class="clearfix">
        <a href="#" v-on:click="toggleForgotPassword">Забыли пароль?</a>
      </div>
    </form>
  </div>
</template>


<script lang="ts">
import { Component, Prop, Vue } from "vue-property-decorator";

@Component
export default class AuthComponent extends Vue {
  @Prop() private msg!: string;

  public email = "";
  public fio = "";
  public password = "";
  public phone = "";

  public doLogin = false;
  public doRegister = true;
  public forgotPassword = false;

  public toggleForgotPassword() {
    this.forgotPassword = true;
  }

  public sendPassword() {
    console.log("send password for", this.email);
  }

  public login() {
    console.log("login user ", { email: this.email, password: this.password });
  }

  public register() {
    console.log("register user ", {
      email: this.email,
      fio: this.fio,
      phone: this.phone
    });
  }
}
</script>

<style scoped lang="scss">
.login-form {
  width: 340px;
  margin: 50px auto;
  form {
    margin-bottom: 15px;
    background: #f7f7f7;
    box-shadow: 0px 4px 4px rgba(0, 0, 0, 0.3);
    padding: 30px;
  }
  h2 {
    margin: 0 0 15px;
  }
}
.form-control,
.btn {
  min-height: 38px;
  border-radius: 2px;
}
.btn {
  font-size: 15px;
  font-weight: bold;
}
</style>