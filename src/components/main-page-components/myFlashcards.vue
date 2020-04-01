<template>
  <div class="container">
    <div class="header-flash">
      <h1><i class="fas fa-book" /> Moje zestawy</h1>
    </div>
    <div
      v-for="(row,index) of rows"
      :key="row.id"
      class="flashcard-row"
      @click="showFlashcard(row.id)"
    >
      <div class="head">
        <h2>{{ index + 1 }}.</h2>
        <span>
          <div class="align-center">
            {{ row.pseudonym }}
            <img
              class="avatar"
              :src="`http://127.0.0.1/api${row.file_path.split('..').join('')}`"
            >
          </div>
        </span>
      </div>
      <div class="center">
        <h3>
          {{ row.title }}<br>
          <span>({{ row.topic }})</span>
        </h3>
      </div>
      <div
        class="flags"
        style="display: flex;"
      >
        <img
          :src="images[row.at_language_id]"
          width="60px"
          height="40px"
        >
        <i
          class="fas fa-arrow-right"
          style="display: flex; align-content: center"
        />
        <img
          :src="images[row.to_language_id]"
          width="60px"
          height="40px"
        >
      </div>
      <div class="date">
        {{ row.created_at.split(' ')[0] }}
        <span>{{ row.created_at.split(' ')[1] }}</span>
      </div>
    </div>
    <div id="popup-black2">
      <showFlashcard
        v-if="flashcardStatus"
        :id="id"
        @exit="showFlashcard"
      />
    </div>
  </div>
</template>

<script>
import showFlashcard  from '../popups/showFlashcard.vue'
export default {
  name: 'myFlashcards',
  components: {
    showFlashcard,
  },
  props: {
    refresh: {
      type: [Number, String],
      default: null,
    }
  },
  data() {
    return {
      rows: [],
      languageArray: [],
      images: [
        'xd',
        'http://127.0.0.1/api/files/english.png',
        'http://127.0.0.1/api/files/polish.png',
        'http://127.0.0.1/api/files/german.png',
      ],
      id: null,
      flashcardStatus: false
    }
  },
  watch: {
    refresh: function() {
      this.load();
    }
  },
  mounted() {
    this.loadSelects();
    this.load();
  },
  methods: {
    load() {
      var formData = new FormData();
      formData.append('user_id', localStorage.user_id);

      fetch('http://127.0.0.1/api/flashcard/readUsersFlash.php',{
        method: 'POST',
        body: formData
      })
      .then(response=> response.json())
      .then(response =>{
        this.rows = response;
      })
    },
    showFlashcard(id) {
      if (this.flashcardStatus) {
        let pop = document.getElementById('popup-black2');
        pop.style.display = 'none';

        this.load();
        this.id = null;
        this.flashcardStatus = false;
      }
      else {
        let pop = document.getElementById('popup-black2');
        pop.style.display = 'flex';

        this.id = id;
        this.flashcardStatus = true;
      }
    },
    loadSelects() {
      fetch('http://127.0.0.1/api/lang/read.php',{
        method: 'POST'
      })
      .then(response=> response.json())
      .then(response =>{
        this.languageArray = response;
      });
    },
  }
}
</script>

<style lang="scss" scoped>
  .date {
    position: absolute;
    bottom: 5px;
    right: 10px;
  }

  .align-center {
    display: flex;
    align-items: center;
    justify-content: flex-end !important;
    font-weight: bold;
    margin-bottom: 8px;
    color: white !important;
  }

  #popup-black2 {
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

  .header-flash {
    -webkit-box-shadow: 0 2px 2px 0 rgba(0, 0, 0, 0.14), 0 1px 5px 0 rgba(0, 0, 0, 0.12), 0 3px 1px -2px rgba(0, 0, 0, 0.2);
    box-shadow: 0 2px 2px 0 rgba(0, 0, 0, 0.14), 0 1px 5px 0 rgba(0, 0, 0, 0.12), 0 3px 1px -2px rgba(0, 0, 0, 0.2);
    padding: 0px;
    margin-bottom: 10px;
    border-radius: 10px;
    display: flex;
    justify-content: center;
    color: white;
    background: #92589E;
    position: sticky;
    top: 60px;
    z-index: 97;


    i {
      margin-right: 10px;
    }
  }

  .container {
    width: 90%;
    max-width: 800px;
    margin: auto;
  }

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
    -webkit-box-shadow: 0 2px 2px 0 rgba(0, 0, 0, 0.14), 0 1px 5px 0 rgba(0, 0, 0, 0.12), 0 3px 1px -2px rgba(0, 0, 0, 0.2);
    box-shadow: 0 2px 2px 0 rgba(0, 0, 0, 0.14), 0 1px 5px 0 rgba(0, 0, 0, 0.12), 0 3px 1px -2px rgba(0, 0, 0, 0.2);
    position: relative;
  }

  .flashcard-row {
    padding: 10px;
    margin-bottom: 5px;
    border-radius: 10px;
    background:rgba(72, 61, 139, 0.4);
    -webkit-box-shadow: 0 2px 2px 0 rgba(0, 0, 0, 0.14), 0 1px 5px 0 rgba(0, 0, 0, 0.12), 0 3px 1px -2px rgba(0, 0, 0, 0.2);
    box-shadow: 0 2px 2px 0 rgba(0, 0, 0, 0.14), 0 1px 5px 0 rgba(0, 0, 0, 0.12), 0 3px 1px -2px rgba(0, 0, 0, 0.2);
    position: relative;
    color: white;

    &:hover {
      background: #92589E;
      cursor: pointer;
    }

    .avatar {
      width: 50px !important;
      height: 50px !important;
      border-radius: 50% !important;
      margin-left: 10px;
      margin-right: 10px;
    }

    .head {
      display: flex;
      justify-content: space-between;
      position: relative;
      margin-bottom: 20px;
    }

    h3 {
      text-align: center;
      margin-top: 0px;

      span {
        font-size: 14px;
      }
    }

    div {
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

    @media (max-width: 600px) {
      .flags {
        margin-bottom: 30px;
      }
    }

    @media (min-width: 600px) {
      .center {
        position: absolute;
        left: 50%;
        top: 35px;
      }

      h3 {
        position: relative;
        left: -50%;
      }
    }
  }

  span {
    color: lightgrey;
  }

  h1 input {
    font: inherit;
    color: white;
    letter-spacing: inherit;
    word-spacing: inherit;
    text-transform: none;
    text-indent: 0px;
    -webkit-rtl-ordering: unset;
    text-rendering: unset;
    width: fit-content;
    display: block;
    font-size: 0.6em;
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
      border-bottom: 2px solid white;
    }
  }

  .search {
    margin-top: 10px;
    position: relative;

    i {
      position: absolute;
      top: 10px;
      font-size: 20px;
      right: 20px;
    }
  }
</style>