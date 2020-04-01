<template>
  <div class="scroll tile">
    <div class="info-user">
      <img src="http://127.0.0.1/api/files/ez2learn2.png">
    </div>
    <div v-if="loginStatus">
      <h3 style="text-align: center">
        LOGOWANIE
      </h3>
      <div class="info-user">
        <label for="">
          Email
        </label>
        <input
          v-model="login"
          :class="{
            'not-valid' : check(login) && validate,
          }"
          type="text"
        >
      </div>
      <div
        style="margin-top: 10px;"
        class="info-user"
      >
        <label for="">
          Hasło
        </label>
        <input
          v-model="password"
          :class="{
            'not-valid' : password.length < 8 && validate,
          }"
          type="password"
        >
      </div>
      <div style="display:flex; justify-content: center; margin-top: 20px; margin-bottom:30px;">
        <button
          class="login"
          @click="loginReq()"
        >
          Zaloguj
        </button>
        <button
          class="register"
          @click="changeLoginStatus(false)"
        >
          Rejestracja
        </button>
      </div>
    </div>
    <div v-else>
      <h3 style="text-align: center">
        REJESTRACJA
      </h3>
      <div class="info-user">
        <label for="">
          Email
        </label>
        <input
          v-model="email"
          :class="{
            'not-valid': check(email) && validate,
          }"
          type="text"
        >
      </div>
      <div
        style="margin-top: 10px;"
        class="info-user"
      >
        <label for="">
          Hasło
        </label>
        <input
          v-model="password"
          :class="{
            'not-valid' : password.length < 8 && validate,
          }"
          type="password"
        >
      </div>
      <div
        style="margin-top: 10px;"
        class="info-user"
      >
        <label for="">
          Powtórz hasło
        </label>
        <input
          v-model="passwordMatch"
          :class="{
            'not-valid' : password != passwordMatch,
          }"
          type="password"
        >
      </div>
      <div style="display:flex; justify-content: center; margin-top: 20px; margin-bottom:30px;">
        <button
          class="login"
          @click="registerReq()"
        >
          Zarejestruj
        </button>
        <button
          class="register"
          @click="changeLoginStatus(true)"
        >
          Logowanie
        </button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'loginPage',
  data() {
    return {
      login: '',
      email: '',
      password: '',
      passwordMatch: '',
      validate: false,
      loginStatus: true,
    }
  },
  methods: {
    loginReq() {
      this.validate = true;

      setTimeout(()=> {
        if (document.querySelectorAll('.not-valid').length == 0) {
          var formData = new FormData();
          formData.append('password', this.password);
          formData.append('email', this.login);
          formData.append('login', new Date().getTime() + 7358);

          fetch('http://127.0.0.1/api/user/login.php',{
            method: 'POST',
            body: formData
          })
          .then(response=> response.json())
          .then(response =>{
            if (response.logged != undefined) {
              localStorage.setItem('login', response.logged);
              localStorage.setItem('user_id', response.user_id);
              localStorage.setItem('role_id', response.role_id);
              localStorage.setItem('status_id', response.status_id);
              location.reload(true);
            }
            else {
              this.$swal.fire({
                title: response.message,
                icon: 'error'
              })
            }
          })
        }
      }, 5)
    },
    registerReq() {
      this.validate = true;

      setTimeout(()=> {
        if (document.querySelectorAll('.not-valid').length == 0) {
          var formData = new FormData();
          formData.append('password', this.password);
          formData.append('email', this.email);

          fetch('http://127.0.0.1/api/user/create.php',{
            method: 'POST',
            body: formData
          })
          .then(response=> response.json())
          .then(response =>{
            if (response.created != undefined) {
              let date = new Date().getTime();
              var formDataProf = new FormData();

              formDataProf.append('user_id', response.user_id);
              formDataProf.append('unique_id', '#' + date);

              fetch('http://127.0.0.1/api/profile/create.php',{
                method: 'POST',
                body: formDataProf
              })

              this.$swal.fire({
                title: response.message,
                icon: 'success'
              })
              .then(()=>{
                location.reload(true)
              })
            }
            else {
              this.$swal.fire({
                title: response.message,
                icon: 'error'
              })
            }
          })
        }
      }, 5)
    },
    check(string) {
      // eslint-disable-next-line
      let patt = /^(([^<>()\[\]\\.,;:\s@"]+(\.[^<>()\[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/;
      let valid = patt.test(String(string));

      if(valid) {
        return false;
      }

      else {
        return true;
      }
    },
    changeLoginStatus(status) {
      this.loginStatus = status;
    }
  }
}
</script>

<style lang="scss" scoped>
  .login {
    margin-left: 10px;
    background-color: steelblue;
    padding: 10px;
    font-size: 15px;
    font-weight: bold;
    color: white;
    border: 1px solid steelblue;
    border-radius: 5px;
    opacity: 0.8;

    &:hover {
      box-shadow: 0 0 0 0.2rem rgba(52, 144, 220, 0.25);
      opacity: 1;
    }
  }

  .register {
    margin-left: 10px;
    background-color: gray;
    padding: 10px;
    font-size: 15px;
    font-weight: bold;
    color: white;
    border: 1px solid gray;
    border-radius: 5px;
    opacity: 0.8;

    &:hover {
      box-shadow: 0 0 0 0.2rem rgba(255, 255, 255, 0.25);
      opacity: 1;
    }
  }

  .role {
    margin-right: 10px;
    margin-bottom: 5px;
    margin-top: 10px;
    background-color: #6C757D;
    color: white;
    width: auto;
    padding: 10px;
    padding-left: 10px;
    border-radius: 50px;
    display: inline-block;
    cursor: default;
  }

  .footer {
    position: absolute;
    bottom: 10px;
    right: 10px;

    .accept {
      margin-left: 10px;
      background-color: green;
      padding: 10px;
      font-size: 15px;
      font-weight: bold;
      color: white;
      border: 1px solid green;
      border-radius: 5px;
      opacity: 0.8;

      &:hover {
        box-shadow: 0 0 0 0.2rem rgba(52, 144, 220, 0.25);
        opacity: 1;
      }
    }

    .discard {
      background-color: #DC143C;
      padding: 10px;
      font-size: 15px;
      font-weight: bold;
      color: white;
      border: 1px solid #DC143C;
      border-radius: 5px;
      opacity: 0.8;

      &:hover {
        box-shadow: 0 0 0 0.2rem rgba(52, 144, 220, 0.25);
        opacity: 1;
      }
    }
  }

  textarea {
    width: 80%;
    max-width: 350px;
    margin: auto;
    margin-top: 30px;
    height: 70px;
    padding: 10px;
    overflow: hidden;
    resize: none;
    line-height: 1.5;
    color: #495057;
    background-color: #FFF;
    background-clip: padding-box;
    border: 2px solid #ced4da;
    border-radius: 4px;
    -webkit-transition: border-color 0.15s ease-in-out, -webkit-box-shadow 0.15s ease-in-out;
    transition: border-color 0.15s ease-in-out, -webkit-box-shadow 0.15s ease-in-out;
    -o-transition: border-color 0.15s ease-in-out, box-shadow 0.15s ease-in-out;
    transition: border-color 0.15s ease-in-out, box-shadow 0.15s ease-in-out;
    transition: border-color 0.15s ease-in-out, box-shadow 0.15s ease-in-out, -webkit-box-shadow 0.15s ease-in-out;

    &:disabled {
      border: none;
      background-color: transparent;
    }
  }

  .exit-user {
    cursor: pointer;
  }

  .info-user {
    display: flex;
    justify-content: center;
    margin-top: 20px;
    flex-direction: column;
    text-align: center;

    input {
      max-width: 90%;
      text-align: center;
      width: 300px;
      margin: auto;
      height: 30px;
      padding: 4px 6px;
      font-size: 15px;
      font-weight: bold;
      line-height: 1;
      color: #495057;
      background-color: #FFF;
      background-clip: padding-box;
      border: 2px solid #ced4da;
      border-radius: 4px;
      -webkit-transition: border-color 0.15s ease-in-out, -webkit-box-shadow 0.15s ease-in-out;
      transition: border-color 0.15s ease-in-out, -webkit-box-shadow 0.15s ease-in-out;
      -o-transition: border-color 0.15s ease-in-out, box-shadow 0.15s ease-in-out;
      transition: border-color 0.15s ease-in-out, box-shadow 0.15s ease-in-out;
      transition: border-color 0.15s ease-in-out, box-shadow 0.15s ease-in-out, -webkit-box-shadow 0.15s ease-in-out;

      &:disabled {
        border: none;
        background-color: transparent;
      }
    }

    img {
      margin: auto;
      width: 180px;
      height: 180px;
      border-radius: 50%;
      border: 5px solid black;
    }
  }

  .header {
    padding: 20px;
    padding-top: 10px;
    padding-bottom: 2px;
    top: 0px;
    position: sticky;
    height: 80px;
  }

  .icons-user {
    display: flex;
    font-size: 26px;
    position: absolute;
    top: 20px;
    right: 30px;

    i {
      cursor: pointer;
      margin-right: 8px;
    }

    .exit-icon {
      height: 30px;
      width: 4px;
      margin-left: 12px;
      background-color: black;
      border-radius: 10px;
      transform: rotate(45deg);
    }

    .exit-icons {
      height: 30px;
      width: 4px;
      background-color: black;
      border-radius: 10px;
      transform: rotate(90deg);
      -ms-transform: rotate(90deg);
      -webkit-transform: rotate(90deg);
    }
  }

  .tile {
    position: relative;
    background: #f9f9f9;
    border-radius: 3px;
    padding: 20px;
    padding-top: 10px;
    -webkit-box-shadow: 0 2px 2px 0 rgba(0, 0, 0, 0.14), 0 1px 5px 0 rgba(0, 0, 0, 0.12), 0 3px 1px -2px rgba(0, 0, 0, 0.2);
    box-shadow: 0 2px 2px 0 rgba(0, 0, 0, 0.14), 0 1px 5px 0 rgba(0, 0, 0, 0.12), 0 3px 1px -2px rgba(0, 0, 0, 0.2);
    margin-bottom: 1px;
    -webkit-transition: all 0.3s ease-in-out;
    -o-transition: all 0.3s ease-in-out;
    transition: all 0.3s ease-in-out;
  }

  .tile-black {
    position: relative;
    background: #E6D4E9;
    color: white !important;
    border-radius: 3px;
    padding: 20px;
    padding-top: 10px;
    -webkit-box-shadow: 0 2px 2px 0 rgba(0, 0, 0, 0.14), 0 1px 5px 0 rgba(0, 0, 0, 0.12), 0 3px 1px -2px rgba(0, 0, 0, 0.2);
    box-shadow: 0 2px 2px 0 rgba(0, 0, 0, 0.14), 0 1px 5px 0 rgba(0, 0, 0, 0.12), 0 3px 1px -2px rgba(0, 0, 0, 0.2);
    margin-bottom: 1px;
    -webkit-transition: all 0.3s ease-in-out;
    -o-transition: all 0.3s ease-in-out;
    transition: all 0.3s ease-in-out;
  }

  .scroll {
    position: relative;
    margin-top: 60px;
    overflow-y: auto;
    height: fit-content;
    max-height: 77vh;
    padding: 0px;
    scroll-behavior: smooth;
    width: 90%;
    max-width: 400px;
    flex-direction: column;
    color:black;
  }

  @media (max-width: 700px) {
    img {
      width: 130px !important;
      height: 130px !important;
      border-radius: 50%;
    }

    .header {
      height: 20px;
    }

    textarea {
      margin-top: 10px;
    }
  }

  .title {
    text-align: center;
    font-size: 25px;
    color: gold;
    position: absolute;
    font-weight: bold;
    width: 100%;
  }
</style>