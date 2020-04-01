<template>
  <div
    class="scroll tile"
  >
    <div class="header">
      <div class="icons-user">
        <i
          v-if="profile.pseudonym != pseudonym || profile.description != description"
          class="fas fa-save"
          title="Zapisz zmiany"
          :class="{
            'red-save': pseudonym == null || pseudonym == ''
          }"
          @click="edit()"
        />
        <div
          class="exit-user"
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
      </div>
    </div>
    <div class="info-user">
      <label
        v-if="profile.file_path.split('..')"
        for="image"
      >
        <img :src="`http://127.0.0.1/api${profile.file_path.split('..').join('')}`">
      </label>
      <input
        id="image"
        ref="file"
        name="file"
        type="file"
        class="input-hide"
        @change="onFileChange"
      >
    </div>
    <div class="info-user">
      <input
        v-model="pseudonym"
        type="text"
        placeholder="pseudonim"
        onfocus="this.placeholder = ''"
        onblur="this.placeholder = 'pseudonim'"
      >
    </div>
    <div class="info-user">
      <div>
        <span style="color: black; font-size: 14px;"> {{ uniqueId }} </span>
      </div>
    </div>
    <div
      style="margin-top: 10px;"
      class="info-user"
    >
      <textarea
        v-model="description"
        placeholder="Twój opis"
        onfocus="this.placeholder = ''"
        onblur="this.placeholder = 'Twój opis'"
      />
    </div>
    <div
      class="info-user"
      style="margin-bottom: 30px;"
    >
      <span
        class="role"
        :style="`color: ${role.color}; background-color: ${role.background}`"
      >
        <i
          :class="'fas ' + role.icon"
        />
        {{ role.name }}
      </span>
    </div>
  </div>
</template>

<script>
export default {
  name: 'ShowUser',
  props: {
    userid: {
      type: [String, Number],
      default: null,
    }
  },
  data() {
    return {
      profile: [],
      roles: [],
      role: [],
      pseudonym: '',
      description: '',
      uniqueId: '',
      userId: '',
    }
  },
  mounted() {
    if (this.userid != null) {
      this.userId = this.userid;
    }
    else {
      this.userId = localStorage.user_id;
    }

    this.load();
    var formData = new FormData();
    formData.append('id', this.userId);

    fetch('http://127.0.0.1/api/role/read.php',{
      method: 'POST',
    })
    .then(response=> response.json())
    .then(response =>{
      this.roles = response;

      fetch('http://127.0.0.1/api/user/readOne.php',{
        method: 'POST',
        body: formData
      })
      .then(response=> response.json())
      .then(response =>{
        for (let i = 0; i < this.roles.length; i++) {
          if (this.roles[i].id == response[0].role_id) {
            this.role = this.roles[i];

            break;
          }
        }
      });
    });
  },
  methods: {
    load() {
      var formData = new FormData();
      formData.append('user_id', this.userId);

      fetch('http://127.0.0.1/api/profile/read.php',{
        method: 'POST',
        body: formData
      })
      .then(response=> response.json())
      .then(response =>{
        this.profile = response[0];
        this.pseudonym = response[0].pseudonym;
        this.uniqueId = response[0].unique_id;
        this.description = response[0].description;
      });
    },
    edit() {
      if (this.pseudonym != null && this.pseudonym != '') {
        var formData = new FormData();
        formData.append('user_id', this.userId);
        formData.append('pseudonym', this.pseudonym);
        formData.append('description', this.description);

        fetch('http://127.0.0.1/api/profile/update.php',{
          method: 'POST',
          body: formData
        })
        .then(()=>{
          this.load()
        });
      }
    },
    onFileChange(e) {
      var formData = new FormData();

      formData.append('file', e.target.files[0], e.target.files[0].name);
      formData.append('user_id', this.userId);

      fetch('http://127.0.0.1/api/profile/saveFile.php',{
        method: 'POST',
        body: formData
      })
      .then(()=>{
        this.load()
      });
    },
    exit() {
      this.$emit('exit');
    },
  }
}
</script>

<style lang="scss" scoped>
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

    &:focus {
      outline: none;
      border-bottom: 2px solid #92589E;
    }

    &:disabled {
      background-color: white !important;
    }
  }

  textarea {
    border: none;
    padding: 10px;
    padding-bottom: 3px;
    font-size: 16px;
    resize: none;
    text-align: center;
    height: 25vh;
    width: 60%;
    margin-top: 20px;

    &:focus {
      outline: none;

      border-bottom: 2px solid #92589E;
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

  .exit-user {
    cursor: pointer;
  }

  .info-user {
    display: flex;
    justify-content: center;

    img {
      width: 180px;
      height: 180px;
      border-radius: 50%;
      border: 1px solid lightgrey;
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

  .input-hide {
    width: 0.1px;
    height: 0.1px;
    opacity: 0;
    overflow: hidden;
    position: absolute;
    z-index: -1;
  }

  .tile {
    position: relative;
    background: white;
    border-radius: 10px;
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
    max-height: 90%;
    padding: 0px;
    scroll-behavior: smooth;
    width: 90%;
    max-width: 500px;
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
</style>