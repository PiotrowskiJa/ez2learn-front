<template>
  <div id="app">
    <MainHeader
      @changepage="changePage"
      @addflash="addFlash"
      @showuser="showUser"
      @showadminpanel="showAdminPanel"
    />
    <MainPageContent
      :changepage="page"
      :addflash="addToken"
      :showuser="showToken"
      :showadminpanel="showAdminToken"
    />
    <footer
      v-if="showFooter"
      class="footer"
    >
      <div>
        &copy;	Jan Piotrowski {{ date }}
      </div>
    </footer>
  </div>
</template>

<script>
import MainHeader from './components/MainHeader.vue'
import MainPageContent from './components/MainPageContent.vue'

export default {
  name: 'app',
  components: {
    MainHeader,
    MainPageContent,
  },
  data() {
    this.date = new Date().getFullYear();

    return {
      page: 2,
      addToken: null,
      showToken: null,
      showAdminToken: null,
      showFooter: true,
    }
  },
  mounted() {
    window.addEventListener('scroll', this.showFooterFunc)
  },
  methods: {
    showFooterFunc() {
      if (window.scrollY == 0) {
        this.showFooter = true;
      }
      else {
        this.showFooter = false;
      }
    },
    changePage(page) {
      this.page = page;
    },
    addFlash() {
      let date = new Date().getTime();
      this.addToken += date;
    },
    showUser() {
      let date = new Date().getTime();
      this.showToken += date;
    },
    showAdminPanel() {
      let date = new Date().getTime();
      this.showAdminToken += date;
    }
  }
}
</script>

