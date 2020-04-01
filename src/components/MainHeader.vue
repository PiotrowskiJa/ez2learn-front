<template>
  <header>
    <div class="title">
      <img
        src="http://127.0.0.1/api/files/ez2learn2.png"
        style="width: 60px; height: 60px; margin-top: 5px; border-radius: 6px;"
      >
    </div>
    <div class="header-resp">
      <nav class="menu">
        <div>
          <i
            v-if="adminPanel"
            class="fas fa-user-shield"
            @click="showAdminPanel()"
          />
        </div>
        <div>
          <i
            class="fas fa-book"
            @click="changePage(1)"
          />
        </div>
        <div>
          <i
            class="fas fa-globe"
            @click="changePage(2)"
          />
        </div>
        <div v-if="addFlashcard">
          <i
            class="fas fa-plus-circle"
            @click="addFlash()"
          />
        </div>
        <div>
          <i
            class="fa fa-user"
            @click="showUser()"
          />
        </div>
        <div>
          <i
            class="fas fa-sign-out-alt"
            @click="logout()"
          />
        </div>
      </nav>
    </div>
  </header>
</template>

<script>

export default {
  name: 'MainHeader',
  data() {
    return {
      adminPanel: false,
      addFlashcard: false,
    }
  },
  mounted() {
    this.resize();
    window.addEventListener('resize', this.resize);

    if (localStorage.role_id != undefined && localStorage.role_id == 2) {
      this.adminPanel = true;
    }

    if (localStorage.status_id != undefined && localStorage.status_id == 1) {
      this.addFlashcard = true;
    }
  },
  methods: {
    changePage(page) {
      this.$emit('changepage', page);
    },
    showUser() {
      this.$emit('showuser');
    },
    showAdminPanel() {
      this.$emit('showadminpanel');
    },
    addFlash() {
      this.$emit('addflash');
    },
    resize() {
      let status = window.innerWidth;

      if (status > 1185) {
        this.menuStatus = true;
      }
      else {
        this.menuStatus = false;
      }
    },
    logout() {
      var formData = new FormData();
      formData.append('user_id', localStorage.user_id);

      fetch('http://127.0.0.1/api/user/logout.php',{
        method: 'POST',
        body: formData
      })
      .then(response=> response.json())
      .then(response =>{
        localStorage.clear();
        if (response.logout != undefined) {
          this.$swal.fire({
            title: response.message,
            icon: 'success'
          })
          .then(() => {
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
  }
}
</script>

<style lang="scss" scoped>
  .menu {
    display: flex;

    div {
      margin-top: 5px;
      padding: 10px;
      padding-bottom: 15px;
    }
  }

  .exit-menu {
    padding: 10px 10px 0px 10px;
    margin-left: 10px;
    margin-top: 10px;
    cursor: pointer;

    .exit-icon {
      height: 30px;
      width: 4px;
      margin-left: 12px;
      background-color: white;
      border-radius: 10px;
      transform: rotate(45deg);
    }

    .exit-icons {
      height: 30px;
      width: 4px;
      background-color: white;
      border-radius: 10px;
      transform: rotate(90deg);
      -ms-transform: rotate(90deg);
      -webkit-transform: rotate(90deg);
    }
  }

  .header-resp {
    display: flex;
    justify-content: flex-end;
    width: 100%;
  }

  .main-navigation {
    background-color: #92589E;
    position: fixed;
    height: 100%;
    width: 20%;
    margin-top: 20px;
    min-width: 210px;
    max-width: 290px;

    ul {
      margin-top: 30px;
      list-style-type: none;
      padding-inline-start: 0px;

      li {
        font-size: 20px;
        padding: 10px 10px 20px 20px;

        i {
          width: 20px;
          font-size: 20px;
        }
      }
    }

    .link-active {
      background-color: #F9D791;

      a {
        color: #92589E;
      }
    }
  }

  i {
    cursor: pointer;
    font-size: 30px;
    margin-right: 20px;

    &:hover {
      color: #F9D791;
    }
  }

  .hamburger {
    margin-top: 5px;
    padding: 15px;
    cursor: pointer;

    div {
      width: 30px;
      height: 4px;
      background-color: white;
      border-radius: 5px;
      margin-top: 5px;

      &::after {
        display: block;
        width: 30px;
        height: 4px;
        background-color: white;
        border-radius: 5px;
        content: '';
        margin-top: 5px;
      }

      &::before {
        display: block;
        width: 30px;
        height: 4px;
        background-color: white;
        border-radius: 5px;
        content: '';
        margin-top: 5px;
      }
    }
  }

  header {
    width: 100%;
    color: white;
    display: flex;
    justify-content: space-between;
    background-color: #92589E;
    position: sticky;
    z-index: 98;
    top: 0;
  }

  .header-left {
    background-color: #92589E;
    height: 70px;
    width: 20%;
    max-width: 290px;
  }

  a {
    &:hover {
      color:#F9D791;
    }
  }

  .side-title {
    background-color: #92589E;
    border: 5px solid #92589E;
    padding-top: 15px;
    font-size: 30px;
    margin-left: 20px;
  }

  header {
    background-color:#92589E;
    height: auto;
  }

  .hamburger {
    margin-top: 8px;
  }

  .main-navigation {
    border: none;

    ul {
      li {
        font-size: 15px;

        i {
          width: 20px;
          font-size: 20px;
        }
      }
    }
  }

  .header-left {
    border: none;
  }

  .header-right {
    min-width: 100px;
    display: flex;
    justify-content: flex-end;
    border: none;

    div {
      padding-left: 5px;
      padding-right: 0px;
      margin: 10px 10px 0px 0px;

      i {
        margin-right: 30px;
      }
    }
  }

  .title {
    align-self: center;
    float: left;
    margin-left: 30px;
    font-size: 25px;
    color: gold
  }

  @media (max-width: 442px) {
    .menu {
      div {
        i {
          font-size: 22px;
          margin-right: 10px;
        }
      }
    }
  }

  @media (max-width: 519px) {
    .title {
      display: none;
    }
  }
</style>