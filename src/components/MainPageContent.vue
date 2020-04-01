<template>
  <div class="main-page">
    <div v-if="page == 1">
      <myFlashcards :refresh="refreshToken" />
    </div>
    <div v-if="page == 2">
      <globals
        :refresh="refreshToken"
      />
    </div>
    <div id="popup-black">
      <loginPage
        v-if="!logged"
      />
      <addFlashcard
        v-if="flashcardStatus"
        @load="refreshTokenFunc"
        @exit="showAddFlashcard"
      />
      <ShowUser
        v-if="userStatus"
        @exit="showUser"
      />
      <administrationPanel
        v-if="adminPanelStatus"
        @exit="showAdminPanel"
      />
    </div>
  </div>
</template>

<script>
import globals from '../components/main-page-components/globals.vue'
import myFlashcards from '../components/main-page-components/myFlashcards.vue'
import addFlashcard from '../components/popups/addFlashcard.vue'
import administrationPanel from '../components/popups/administrationPanel.vue'
import ShowUser from '../components/popups/ShowUser.vue'
import loginPage  from '../components/popups/loginPage.vue'
export default {
  name: 'MainPageContent',
  components: {
    globals,
    myFlashcards,
    addFlashcard,
    administrationPanel,
    ShowUser,
    loginPage,
  },
  props: {
    changepage: {
      type: [Number, String],
      default: 2,
    },
    addflash: {
      type: [Number, String],
      default: null,
    },
    showuser: {
      type: [Number, String],
      default: null,
    },
    showadminpanel: {
      type: [Number, String],
      default: null,
    },
  },
  data() {
    return {
      page: 2,
      userStatus: false,
      logged: false,
      flashcardStatus: false,
      adminPanelStatus: false,
      refreshToken: null,
    }
  },
  watch: {
    changepage: function() {
      this.page = this.changepage;
    },
    addflash: function() {
      this.showAddFlashcard();
    },
    showuser: function() {
      this.showUser();
    },
    showadminpanel: function() {
      this.showAdminPanel();
    },
  },
  mounted() {
    this.checkToken();
  },
  methods: {
    refreshTokenFunc() {
      let date = new Date().getTime();
      this.refreshToken += date;
    },
    checkToken() {
      if (localStorage.login != undefined) {
        this.logged = true;
      }

      else {
        let pop = document.getElementById('popup-black');
        pop.style.display = 'flex';
      }
    },
    showUser() {
      if (this.userStatus) {
        let pop = document.getElementById('popup-black');
        pop.style.display = 'none';

        this.userStatus = false;
      }

      else {
        let pop = document.getElementById('popup-black');
        pop.style.display = 'flex';

        this.userStatus = true;
      }
    },
    showAdminPanel() {
      if (this.adminPanelStatus) {
        let pop = document.getElementById('popup-black');
        pop.style.display = 'none';

        this.adminPanelStatus = false;
      }

      else {
        let pop = document.getElementById('popup-black');
        pop.style.display = 'flex';

        this.adminPanelStatus = true;
      }
    },
    showAddFlashcard() {
      if (this.flashcardStatus) {
        let pop = document.getElementById('popup-black');
        pop.style.display = 'none';

        this.flashcardStatus = false;
      }

      else {
        let pop = document.getElementById('popup-black');
        pop.style.display = 'flex';

        this.flashcardStatus = true;
      }
    },
  }
}
</script>

<style lang="scss" scoped>
  .box-tile {
    color: white;
    padding: 20px;
    padding-top: 10px;
    padding-bottom: 10px;
    width: fit-content;
    border: 4px solid #92589E;
    border-radius: 5px;
    background-color: #92589E;
    padding-bottom: 5px;
    height: fit-content;
  }

  #popup-black {
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

  .main-page {
    margin-top: 30px;
  }
</style>