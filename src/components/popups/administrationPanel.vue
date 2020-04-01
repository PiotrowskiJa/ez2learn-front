<template>
  <div
    class="scroll tile-pop"
  >
    <div class="words">
      <h3>Użytkownicy</h3>
      <div class="search">
        <input
          v-model="search"
          type="text"
          @input="load"
        >
        <i
          class="fas fa-search"
          style="padding-left: 5px;"
        />
      </div>
      <div
        class="icons-user"
        @click="exit()"
      >
        <div
          class="exit-icon"
        >
          <div
            class="exit-icons"
          />
        </div>
      </div>
      <hr
        style="width: 90%; margin: auto; background-color: #92589E; height: 3px; border: none;"
      >
      <div style="height: 80vh; overflow-y: auto;">
        <template v-for="(user,index) of users">
          <userRow
            :key="user.id"
            :user="user"
            :index="index"
            :roles="roles"
            :statuses="statuses"
            @showprofile="showUser"
            @load="load()"
          />
          <hr
            v-if="index + 1 != users.length"
            :key="index + 'x'"
            style="width: 80%; margin: auto; background-color: lightgray; height: 3px; border: none;"
          >
        </template>
        <div
          v-if="users.length == 0"
          class="flashcard-empty"
        >
          Brak wyników
        </div>
      </div>
      <div id="popup-black4">
        <ShowUser
          v-if="userStatus"
          :userid="userId"
          @exit="showUser"
        />
      </div>
    </div>
  </div>
</template>

<script>
import ShowUser from './ShowUser.vue'
import userRow  from '../userRow.vue';
export default {
  name: 'administrationPanel',
  components: {
    userRow,
    ShowUser,
  },
  data() {
    return {
      userStatus: false,
      users: [],
      roles: [],
      statuses: [],
      userId: null,
      search: '',
    }
  },
  mounted() {
    this.load()
    this.loadSelects();
  },
  methods: {
    exit() {
      this.$emit('exit');
    },
    load() {
      var formData = new FormData();
      formData.append('id', localStorage.user_id);

      if (this.search.length > 2) {
        formData.append('email', this.search);

        fetch('http://127.0.0.1/api/user/readSearch.php',{
          method: 'POST',
          body: formData,
        })
        .then(response => response.json())
        .then(response => {
          this.users = response;
        })
      }
      else {
        fetch('http://127.0.0.1/api/user/read.php',{
          method: 'POST',
          body: formData,
        })
        .then(response => response.json())
        .then(response => {
          this.users = response;
        })
      }
    },
    loadSelects() {
      fetch('http://127.0.0.1/api/role/read.php',{
        method: 'POST',
      })
      .then(response=> response.json())
      .then(response =>{
        this.roles = response;
      })

      fetch('http://127.0.0.1/api/status/read.php',{
        method: 'POST',
      })
      .then(response=> response.json())
      .then(response =>{
        this.statuses = response;
      })
    },
    showUser(id = null) {
      this.userId = id;

      if (this.userStatus) {
        let pop = document.getElementById('popup-black4');
        pop.style.display = 'none';
        this.userId = null;

        this.userStatus = false;
      }

      else {
        let pop = document.getElementById('popup-black4');
        pop.style.display = 'flex';

        this.userId = id;

        this.userStatus = true;
      }
    },
  }
}
</script>

<style lang="scss" scoped>
  .flashcard-empty {
    justify-content: center;
    align-items: center;
    color: grey;
    height: 100px;
    display: flex;
    font-size: 23px;
    padding: 10px;
    margin-bottom: 5px;
    border-radius: 10px;
    background: white;
    position: relative;
  }

  .search {
    margin-top: 10px;
    position: relative;
    width: fit-content;
    margin: auto;
    margin-bottom: 5px;
    margin-top: -10px;

    i {
      position: absolute;
      top: 5px;
      font-size: 20px;
      right: 0px;
      color:grey !important;
    }

    input {
      font: inherit;
      color: black;
      letter-spacing: inherit;
      word-spacing: inherit;
      text-transform: none;
      text-indent: 0px;
      -webkit-rtl-ordering: unset;
      text-rendering: unset;
      width: fit-content;
      display: block;
      font-size: 1em;
      margin-block-start: 0.83em;
      margin-block-end: 0.83em;
      margin-inline-start: 0px;
      margin-inline-end: 0px;
      font-weight: normal;
      border: none;
      -webkit-appearance: unset;
      margin: auto;
      text-align: center;
      padding-bottom: 6px;
      padding-top: 6px;
      background: transparent;
      border-bottom: 2px solid lightgray;

      &:focus {
        outline: none;
        border-bottom: 2px solid #92589E;
      }
    }
  }

  #popup-black4 {
    position: fixed;
    top: 0;
    background-color: rgba(0, 0, 0, 0.5);
    width: 100%;
    height: 100%;
    left: 0;
    z-index: 100;
    display: none;
    justify-content: center;
  }
  .completed {
    background: green !important;
    color: white !important;
  }

  .toggle {
    position: relative;
    display: block;
    width: 40px;
    height: 20px;
    cursor: pointer;
    -webkit-tap-highlight-color: transparent;
    transform: translate3d(0,0,0);
    margin-right: 20px;
    margin-left: 20px;

    &:before {
      content: "";
      position: relative;
      top: 3px;
      left: 3px;
      width: 34px;
      height: 14px;
      display: block;
      background: #9A9999;
      border-radius: 8px;
      transition: background .2s ease;
    }

    span {
      position: absolute;
      top: 0;
      left: 0;
      width: 20px;
      height: 20px;
      display: block;
      background: white;
      border-radius: 10px;
      box-shadow: 0 3px 8px rgba(#9A9999,.5);
      transition: all .2s ease;

      &:before {
        content: "";
        position: absolute;
        display: block;
        margin: -18px;
        width: 56px;
        height: 56px;
        background: rgba(#62B435,.5);
        border-radius: 50%;
        transform: scale(0);
        opacity: 1;
        pointer-events: none;
      }
    }
  }

  #remind-check:checked + .toggle {
    &:before {
      background: #9EDA7E;
    }

    span {
      background: #62B435;
      transform: translateX(20px);
      transition: all .2s cubic-bezier(.8,.4,.3,1.25), background .15s ease;
      box-shadow: 0 3px 8px rgba(#62B435,.2);

      &:before {
        transform: scale(1);
        opacity: 0;
        transition: all .4s ease;
      }
    }
  }

  .footer-word {
    display: flex;
    justify-content: center;
    background: #92589E;
    padding: 10px;
    -webkit-box-shadow: 0 2px 2px 0 rgba(0, 0, 0, 0.14), 0 1px 5px 0 rgba(0, 0, 0, 0.12), 0 3px 1px -2px rgba(0, 0, 0, 0.2);
    box-shadow: 0 2px 2px 0 rgba(0, 0, 0, 0.14), 0 1px 5px 0 rgba(0, 0, 0, 0.12), 0 3px 1px -2px rgba(0, 0, 0, 0.2);
    border-radius: 10px;
    -webkit-transition: all 0.3s ease-in-out;
    -o-transition: all 0.3s ease-in-out;
    transition: all 0.3s ease-in-out;
    width: 95%;
    max-width: 750px;
    margin: auto;
    margin-top: 10px;
    color: white;

    span {
      span {
        font-weight: bold;
      }
    }
  }

  .completed {
    background: green !important;
    color: white !important;
  }

  #popup-black3 {
    position: fixed;
    top: 0;
    background-color: rgba(0, 0, 0, 0.7);
    width: 100%;
    height: 100%;
    left: 0;
    z-index: 100;
    display: none;
    justify-content: center;
  }

  .words-head {
    -webkit-box-shadow: 0 2px 2px 0 rgba(0, 0, 0, 0.14), 0 1px 5px 0 rgba(0, 0, 0, 0.12), 0 3px 1px -2px rgba(0, 0, 0, 0.2);
    box-shadow: 0 2px 2px 0 rgba(0, 0, 0, 0.14), 0 1px 5px 0 rgba(0, 0, 0, 0.12), 0 3px 1px -2px rgba(0, 0, 0, 0.2);
    width: 95%;
    max-width: 750px;
    margin: auto;
    background: #92589E;
    border-radius: 10px;
    padding: 10px;
    color: white;

    input {
      background: #92589E;
      color: white !important;
    }
  }

  .words {
    -webkit-box-shadow: 0 2px 2px 0 rgba(0, 0, 0, 0.14), 0 1px 5px 0 rgba(0, 0, 0, 0.12), 0 3px 1px -2px rgba(0, 0, 0, 0.2);
    box-shadow: 0 2px 2px 0 rgba(0, 0, 0, 0.14), 0 1px 5px 0 rgba(0, 0, 0, 0.12), 0 3px 1px -2px rgba(0, 0, 0, 0.2);
    padding: 10px;
    border-radius: 10px;
    width: 95%;
    margin: auto;
    margin-top: 10px;
    margin-bottom: 0px;
    max-width: 750px;
    position: relative;
    min-height: 60vh;
    background: white;
    padding-left: 5px;
    padding-right: 5px;

    h3 {
      text-align: center;
    }

    .word-row {
      display: flex;
      justify-content: space-around;
      padding-top: 15px;
      padding-bottom: 37px;
      position: relative;

      div {
        text-align: center;
        width: 50%;
      }
    }
  }

  .tile-pop {
    position: relative;
    border-radius: 3px;
    padding: 20px;
    padding-top: 0px;
    margin-bottom: 1px;
    -webkit-transition: all 0.3s ease-in-out;
    -o-transition: all 0.3s ease-in-out;
    transition: all 0.3s ease-in-out;
  }

  .scroll {
    position: relative;
    margin-top: 30px;
    overflow-y: auto;
    height: fit-content;
    max-height: 95vh;
    padding: 0px;
    scroll-behavior: smooth;
    width: 90%;
    max-width: 740px;
    flex-direction: column;
    color:black;
  }

  .icons-user {
    display: flex;
    font-size: 26px;
    position: absolute;
    top: 30px;
    right: 40px;
    cursor: pointer;

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

  .flags {
    margin-top: 20px;
    justify-content: center;

    * {
      align-self: center;
    }

    i {
      margin-left: 10px;
      margin-right: 10px;
      font-weight: bolder;
    }

    img {
      border-radius: 6px;
    }
  }

  i {
    font-size: 25px;
    cursor: pointer;
  }

  textarea {
    border: none;
    padding: 10px;
    padding-bottom: 3px;
    font-size: 16px;
    resize: none;
    text-align: center;
    border-bottom: 2px solid lightgrey;

    &:focus {
      outline: none;

      border-bottom: 2px solid #92589E;
    }
  }

  .void {
    text-align: center;
    font-size: 25px;
    padding-top: 25px;
    height: 100px;
    background-color: white;
    margin-bottom: 1rem;
    margin-top: 10vh;
    color: gray;
  }

  .input {
    font: inherit;
    color: black;
    letter-spacing: inherit;
    word-spacing: inherit;
    text-transform: none;
    text-indent: 0px;
    -webkit-rtl-ordering: unset;
    text-rendering: unset;
    width: fit-content;
    display: block;
    font-size: 1.5em;
    margin-block-start: 0.83em;
    margin-block-end: 0.83em;
    margin-inline-start: 0px;
    margin-inline-end: 0px;
    font-weight: bold;
    border: none;
    -webkit-appearance: unset;
    margin: auto;
    text-align: center;
    padding-bottom: 6px;
    padding-top: 6px;
    border-bottom: 2px solid lightgrey;

    &:focus {
      outline: none;
      border-bottom: 2px solid white;
    }

    &:disabled {
      background-color: #92589E !important;
    }

    &::placeholder {
      color: lightgrey;
      opacity: 1;
    }

    &:-ms-input-placeholder {
      color: lightgrey;
    }

    &::-ms-input-placeholder {
      color: lightgrey;
    }
  }

  .accept {
    position: absolute;
    right: 30px;
    top: -2px;
    margin-right: 10px;
    background-color: green;
    padding: 5px;
    font-size: 15px;
    font-weight: bold;
    color: white;
    border: 1px solid green;
    border-radius: 5px;
    opacity: 0.8;
    cursor: pointer;

    &:hover {
      box-shadow: 0 0 0 0.2rem rgba(52, 144, 220, 0.25);
      opacity: 1;
    }
  }

  .word-selection {
    justify-content: center;
    display: flex;

    select {
      font: inherit;
      color: white;
      letter-spacing: inherit;
      word-spacing: inherit;
      text-transform: none;
      text-indent: 0px;
      text-rendering: unset;
      width: fit-content;
      display: block;
      font-size: 1em;
      margin-block-start: 0.83em;
      margin-block-end: 0.83em;
      margin-inline-start: 0px;
      margin-inline-end: 0px;
      font-weight: bold;
      border: none;
      margin: auto;
      text-align: center;
      padding-bottom: 6px;
      padding-top: 6px;
      border-bottom: 2px solid lightgrey;
      background: #92589E;

      &:focus {
        outline: none;
        border-bottom: 2px solid white;
      }

      &:disabled {
        background-color: #92589E !important;
      }
    }
  }
</style>