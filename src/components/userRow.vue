

<template>
  <div
    class="user-row"
    :class="{
      'active': activeUser,
    }"
  >
    <span class="index-user">
      {{ index + 1 + '.' }}
    </span>
    <div class="menu-user">
      <i
        v-if="logged == 1"
        class="fas fa-check-circle"
        title="zalogowany"
      />
      <i
        :class="`fas ${role.icon}`"
        style="margin-right: 5px;"
        :title="role.name"
      />
      <div
        class="status-circle"
        :style="`background-color: ${status.background}`"
        :title="status.name"
      />
      <i
        class="fas fa-user"
        style="margin-right: 5px;"
        title="Pokaż profil"
        @click="showProfile()"
      />
    </div>
    <div
      class="word-selection"
      style="cursor: pointer;"
      @click="showUser()"
    >
      <h4>{{ email }}</h4>
    </div>
    <div v-if="activeUser">
      <div class="user-edit">
        <div>
          <h4>Rola:</h4>
          <template
            v-for="row of statuses"
          >
            <span
              :key="row.id"
              class="role"
              :class="{
                'not-selected': row != status
              }"
              :style="`color: ${row.color}; background-color: ${row.background}`"
              @click="changeStatus(row)"
            >
              <i
                :class="'fas ' + row.icon"
              />
              {{ row.name }}
            </span>
          </template>
        </div>
        <div>
          <h4>Status:</h4>
          <template
            v-for="row of roles"
          >
            <span
              :key="row.id"
              class="role"
              :class="{
                'not-selected': row != role
              }"
              :style="`color: ${row.color}; background-color: ${row.background}`"
              @click="changeRole(row)"
            >
              <i
                :class="'fas ' + row.icon"
              />
              {{ row.name }}
            </span>
          </template>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'userRow',
  props: {
    user: {
      type: Object,
      default: null,
    },
    statuses: {
      type: Array,
      default: null,
    },
    roles: {
      type: Array,
      default: null,
    },
    index: {
      type: [Number, String],
      default: null,
    },
  },
  data() {
    return {
      logged: 0,
      email: '',
      role: '',
      status: '',
      activeUser: false,
    }
  },
  watch: {
    statuses: function() {
      this.setSelects();
    },
    roles: function() {
      this.setSelects();
    },
  },
  mounted() {
    this.logged = this.user.logged;
    this.email = this.user.email;
    this.createdAt = this.user.created_at;

    this.setSelects();
  },
  methods: {
    showProfile() {
      this.$emit('showprofile', this.user.id);
    },
    changeRole(id) {
      this.role = id;
      this.edit();
    },
    changeStatus(id) {
      this.status = id;
      this.edit();
    },
    setSelects() {
      for (let i = 0; i < this.statuses.length; i++) {
        if (this.statuses[i].id == this.user.status_id) {
          this.status = this.statuses[i];

          break;
        }
      }

      for (let i = 0; i < this.roles.length; i++) {
        if (this.roles[i].id ==  this.user.role_id) {
          this.role = this.roles[i];

          break;
        }
      }
    },
    showUser() {
      if (this.activeUser) {
        this.activeUser = false;
      }
      else {
        this.activeUser = true;
      }
    },
    edit() {
      var formData = new FormData();
      formData.append('id', this.user.id);
      formData.append('role_id', this.role.id);
      formData.append('status_id', this.status.id);

      fetch('http://127.0.0.1/api/user/update.php',{
        method: 'POST',
        body: formData
      })
      .then(()=> {
        this.$emit('load');
      })
    },
  }
}
</script>

<style lang="scss" scoped>
  .status-circle {
    width: 20px;
    height: 20px;
    border-radius: 50%;
    display: inline-block;
    margin-right: 5px;
  }

  .active {
    color: white;
    background: rgba(#92589E,.8);
    border-radius: 5px;

    h4 {
      font-weight: normal !important;
    }
  }

  .not-selected {
    background: lightgrey !important;
    color: gray !important;
  }

  .user-row {
    position: relative;
    padding-top: 6px;
    max-width: 80%;
    margin: auto;
    padding-bottom: 20px;

    &:first-of-type {
      margin-top: 10px;
    }
  }

  @media (max-width: 700px){
    .user-row {
      max-width: 100%;

      span {
        left: 27px;
      }
    }

    .user-edit {
      div {
        flex-direction: column;
        display: flex;
        align-items: center;
      }

      h4 {
        margin: 0;
      }
    }

    .word-selection {
      h4 {
        margin-top: 60px;
      }
    }
  }

  .user-edit {
    display: flex;
    flex-direction: column;
    align-items: center;

    span {
      position: static;
    }

    h4 {
      display: inline-block;
      margin-right: 10px;
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
    font-size: 14px;
    cursor: pointer;
  }

  textarea {
    border: none;
    padding: 10px;
    padding-bottom: 3px;
    font-size: 16px;
    resize: none;
    text-align: center;

    &:focus {
      outline: none;

      border-bottom: 2px solid #92589E;
    }

    &:disabled {
      background-color: white !important;
    }
  }

  .menu-user {
    font-size: 20px;
    position: absolute;
    right: 20px;
    top: 21px;
    display: flex;
    align-items: center;
    cursor: pointer;

    .yel {
      color: #FFCC00 !important;
    }

    .fa-check-circle {
      color: limegreen;
      margin-right: 5px;
    }
  }

  span {
    font-size: 20px;
    position: absolute;
    left: 30px ;
    top: 21px;
  }

  .word-selection {
    width: 100%;
    justify-content: center;
    display: flex;

    select {
      font: inherit;
      color: black;
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
      font-weight: normal;
      border: none;
      margin: auto;
      text-align: center;
      padding-bottom: 6px;
      padding-top: 6px;
      border-bottom: 2px solid lightgrey;
      background: white;
      min-width: 150px;

      &:focus {
        outline: none;
        border-bottom: 2px solid #92589E;
      }

      &:disabled {
        background-color: white !important;
      }
    }
  }
</style>