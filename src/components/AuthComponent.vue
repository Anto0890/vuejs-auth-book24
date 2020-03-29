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
          v-model.lazy="$v.fio.$model"
          placeholder="Фамилия Имя Отчество"
        />
         <span v-if="$v.fio.$dirty && !$v.fio.validFormat">
          Фамилия и имя обязательны
        </span>
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
          v-bind:class="{'disabled': formIsValid}"
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
import { Component, Prop, Vue, Watch, Mixins } from "vue-property-decorator";
import _ from "lodash";
const axios = require("axios").default;
import { validationMixin } from 'vuelidate'


import { Validate } from 'vuelidate-property-decorators';
import { required } from 'vuelidate/lib/validators'

@Component
export default class AuthComponent extends Mixins(validationMixin) {
  @Prop() private msg!: string
  @Watch("email") onEmailChanged(value: string, oldValue: string) {
    console.log("email changed from " + oldValue, value)
    this.debouncedCheckEmail();
  }

  @Watch("message") onMsgChanged(value: string) {
    console.log("msg ", value);
  }


  public message = "";

  public email = "";

  @Validate({
    validFormat: (val: string) => /^[А-я]+ [А-я-]+ *[А-я]*$/.test(val),
  }) public fio = "";

  public password = "";
  public phone = "";

  public formIsValid = false
  public doLogin = false;
  public doRegister = true;
  public forgotPassword = false;

  private debouncedCheckEmail: any;
  private apiUrl = 'https://demo.book24.ru/api/v1'

  // lifecyle hook
  created() {
    // this.checkAuthorization() // пока не нужно, так как форма показывается, если пользователь не авторизован
    this.debouncedCheckEmail = _.debounce(this.checkEmail, 400); // задержка 400 мс
  }

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

  public checkEmail() {
    // email regexp with unicode 
    const re = /^(([^<>()[\].,;:\s@"]+(\.[^<>()[\].,;:\s@"]+)*)|(".+"))@(([^<>()[\].,;:\s@"]+\.)+[^<>()[\].,;:\s@"]{2,})$/i;
    if (!re.test(this.email.toLowerCase())) {
      this.message = "invalid email " + this.email;
      return;
    }
    axios
      .post(this.apiUrl + '/user/check-email-availability/', 
            {EMAIL: this.email.trim()},
            {headers: {"X-TOKEN": "330d207892855dbd5abd5147ea562094", 
                      "Authorization": "Basic ZGVtby5ib29rMjQ6Ym9vazI0"},
                      // http status == 400 - для email, запрещенных для регистрации
            validateStatus:  (status: number) => { return (status >= 200 && status < 300) || status === 400 }
            })
      .then((response: any) => {
        console.log('api responce', response)
        if (!response.data.success) {
          const err =  response.data.errors || []
          this.message = '' + err.map((er: {message: string }) =>  er.message).join('; ') // сливаем все сообщения в одну строку
        }
      })
      .catch((error: string) => {
        this.message = "Error! Could not reach the API. " + error;
      });
  }
  private checkAuthorization() {
    axios
      .get(this.apiUrl + '/user-session/', 
            {params: {TYPE_PLATFORM: 'desktop'},
             headers: {"X-TOKEN": "330d207892855dbd5abd5147ea562094", 
                      "Authorization": "Basic ZGVtby5ib29rMjQ6Ym9vazI0"},
                      // http status == 400 - для email, запрещенных для регистрации
            validateStatus:  (status: number) => { return (status >= 200 && status < 300) || status === 400 }
            })
      .then((response: any) => {
        console.log('user-session api responce', response)
        if (!response.data.success) {
          console.log('пользователь не авторизован')
        } else {
          console.log('пользователь авторизован')
        }
      })
      .catch((error: string) => {
        this.message = "Error! Could not reach the API. " + error;
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