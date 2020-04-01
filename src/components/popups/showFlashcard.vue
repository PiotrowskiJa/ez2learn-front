<template>
  <div
    class="scroll tile-pop"
  >
    <div>
      <div class="words-head">
        <input
          v-model="title"
          class="input"
          type="text"
          style="margin-top: 20px; margin-bottom: 10px;"
          :disabled="userId != flashcard.user_id"
          placeholder="Nazwa"
          onfocus="this.placeholder = ''"
          onblur="this.placeholder = 'Nazwa'"
        >
        <input
          v-model="topic"
          class="input"
          type="text"
          style="margin: auto; color: lightgray !important; text-align: center; display: block; font-weight: normal; font-size: 1em;"
          :disabled="userId != flashcard.user_id"
          placeholder="Temat"
          onfocus="this.placeholder = ''"
          onblur="this.placeholder = 'Temat'"
        >
        <div
          class="flags"
          style="display: flex;"
        >
          <img
            :src="images[flashcard.at_language_id]"
            width="60px"
            height="40px"
          >
          <i
            class="fas fa-arrow-right"
            style="display: flex; align-content: center"
          />
          <img
            :src="images[flashcard.to_language_id]"
            width="60px"
            height="40px"
          >
        </div>
        <div
          class="icons-user"
        >
          <i
            v-if="flashcard.topic != topic && userId == flashcard.user_id || title != flashcard.title && userId == flashcard.user_id"
            :class="{
              'red-save': topic == null || topic == '' || title == null || title == ''
            }"
            class="fas fa-save"
            title="Zapisz zmiany"
            @click="edit()"
          />
          <i
            v-if="userId == flashcard.user_id"
            class="fas fa-trash"
            title="Usuń zestaw"
            @click="deleteFunc()"
          />
          <div
            class="exit-icon"
            @click="exit()"
          >
            <div
              class="exit-icons"
            />
          </div>
        </div>
      </div>
      <div
        class="footer-word"
        :class="{
          'completed': array.length == words.length
        }"
      >
        <span class="visible">Nauczone słówka: <span>{{ array.length }}/{{ words.length }}</span></span>
        <span class="visible">Procent ukończenia: <span>{{ (array.length/words.length).toFixed(2) * 100 }}%</span></span>
        <span class="hidden">Nauczone: <span>{{ array.length }}/{{ words.length }}</span></span>
        <span class="hidden">Ukończono: <span>{{ (array.length/words.length).toFixed(2) * 100 }}%</span></span>
      </div>
      <div class="words">
        <div style="position: sticky; top: 0;">
          <h3 v-if="words.length != 0">
            Słówka: {{ words.length }}
          </h3>
          <h3
            v-else
            style="margin-bottom: 0px;"
          >
            Dodawanie słówka
          </h3>
          <button
            v-if="words.length != 0"
            class="accept"
            @click="showWordFunc()"
          >
            <span class="visible">Rozpocznij</span>
            <span class="hidden">Start</span>
          </button>
          <div
            class="starter"
            style="position: absolute; right: 10px; top: 0px;"
          >
            <i
              v-if="userId == flashcard.user_id"
              class="fas"
              :class="{
                'fa-plus': !addStatus,
                'fa-times': addStatus
              }"
              @click="addStatusFunc()"
            />
          </div>
        </div>
        <div>
          <div
            v-if="addStatus"
            class="word-row"
            style="padding-bottom: 6px"
          >
            <textarea
              v-model="at_language"
              placeholder="z"
              onfocus="this.placeholder = ''"
              onblur="this.placeholder = 'z'"
            />
            <textarea
              v-model="to_language"
              placeholder="na"
              onfocus="this.placeholder = ''"
              onblur="this.placeholder = 'na'"
            />
            <i
              v-if="to_language != null && to_language != '' && at_language != null && at_language != ''"
              class="fas fa-check-circle"
              style="font-size: 20px; position: absolute; right: 40px; top: 21px; cursor: pointer;"
              title="Utwórz pozycję"
              @click="saveWord()"
            />
          </div>
          <hr
            style="width: 90%; margin: auto; background-color: #92589E; height: 3px; border: none;"
          >
        </div>
        <div style="height: 50vh; overflow-y: auto;">
          <template v-for="(word,index) of words">
            <wordRow
              :key="word.id"
              :word="word"
              :learned="array"
              :index="index"
              :token="token"
              :userid="flashcard.user_id"
              @reload="load"
            />
            <hr
              v-if="index + 1 != words.length"
              :key="word.id + 'x'"
              style="width: 80%; margin: auto; background-color: lightgray; height: 3px; border: none;"
            >
          </template>
        </div>
      </div>
    </div>
    <div
      id="popup-black3"
      @click="showWordFunc"
    >
      <showWord
        v-if="wordStatus"
        :words="words"
        :learned="array"
        :imgat="images[flashcard.at_language_id]"
        :imgto="images[flashcard.to_language_id]"
        @exit="showWordFunc"
        @update="sendUpdate"
      />
    </div>
  </div>
</template>

<script>
import wordRow  from '../wordRow.vue';
import showWord from '../showWord.vue';
export default {
  name: 'showFlashcard',
  components: {
    wordRow,
    showWord,
  },
  props: {
    id: {
      type: [Number, String],
      default: null,
    }
  },
  data() {
    return {
      flashcard: [],
      words: [],
      images: [
        'xd',
        'http://127.0.0.1/api/files/english.png',
        'http://127.0.0.1/api/files/polish.png',
        'http://127.0.0.1/api/files/german.png',
      ],
      at_language: '',
      to_language: '',
      addStatus: false,
      wordStatus: false,
      topic: '',
      title: '',
      createFlag: false,
      learned: [],
      token: 'x',
      userId: '',
      array: [],
    }
  },
  mounted() {
    this.load();
    this.userId = localStorage.user_id;
  },
  methods: {
    loadWords() {
      var formData1 = new FormData();
      formData1.append('flashcard_id', this.id);
      formData1.append('user_id', localStorage.user_id);

      fetch('http://127.0.0.1/api/learn/read.php',{
        method: 'POST',
        body: formData1
      })
      .then(response=> response.json())
      .then(response =>{
        if (response.length == 0) {
          this.createFlag = true;
          this.learned = [];
        }
        else {
          this.learned = response[0].words_ids.split(',');
          this.array = [];

          for (let i = 0; i < this.learned.length; i++) {
            for (let j = 0; j < this.words.length; j++) {
              if (this.words[j].id == this.learned[i]) {
                this.array.push(this.learned[i]);
              }
            }
          }
          this.createFlag = false;
        }

        this.token = 'x' + new Date().getTime();
      })
    },
    sendUpdate(array) {
      var formData = new FormData();
      formData.append('words_ids', array);
      formData.append('flashcard_id', this.id);
      formData.append('user_id', localStorage.user_id);

      if (this.createFlag) {
        fetch('http://127.0.0.1/api/learn/create.php',{
          method: 'POST',
          body: formData
        })
        .then(()=> {
          this.loadWords();
        })
      }
      else {
        fetch('http://127.0.0.1/api/learn/update.php',{
          method: 'POST',
          body: formData
        })
        .then(()=> {
          this.loadWords();
        })
      }
    },
    addStatusFunc() {
      if (this.addStatus) {
        this.addStatus = false;
      }
      else {
        this.addStatus = true;
      }
    },
    showWordFunc(event) {
      if (this.wordStatus) {
        if (event.target.id == 'popup-black3') {
          let pop = document.getElementById('popup-black3');
          pop.style.display = 'none';

          this.wordStatus = false;
        }
      }
      else {
        let pop = document.getElementById('popup-black3');
        pop.style.display = 'flex';

        this.wordStatus = true;
      }
    },
    exit() {
      this.$emit('exit');
    },
    load() {
      var formData = new FormData();
      formData.append('id', this.id);

      fetch('http://127.0.0.1/api/flashcard/readOne.php',{
        method: 'POST',
        body: formData
      })
      .then(response=> response.json())
      .then(response =>{
        this.flashcard = response[0];
        this.topic = this.flashcard.topic;
        this.title = this.flashcard.title;
      })

      fetch('http://127.0.0.1/api/word/read.php',{
        method: 'POST',
        body: formData
      })
      .then(response=> response.json())
      .then(response =>{
        this.words = response;
        this.loadWords();

        if (this.words.length == 0) {
          this.addStatusFunc();
        }
      })
    },
    edit() {
      if (this.topic == null || this.topic == '' || this.title == null || this.title == '') {
        return 0;
      }

      var formData = new FormData();
      formData.append('id', this.flashcard.id);
      formData.append('topic', this.topic);
      formData.append('title', this.title);

      fetch('http://127.0.0.1/api/flashcard/update.php',{
        method: 'POST',
        body: formData
      })
      .then(()=> {
        this.load();
      })
    },
    saveWord() {
      var formData = new FormData();

      formData.append('flashcard_id', this.flashcard.id);
      formData.append('at_language', this.at_language);
      formData.append('to_language', this.to_language);

      fetch('http://127.0.0.1/api/word/create.php',{
        method: 'POST',
        body: formData
      })
      .then(() =>{
        this.load();
        this.at_language = '';
        this.to_language = '';
      })
    },
    deleteFunc() {
      var formData = new FormData();
      formData.append('id', this.flashcard.id);

      fetch('http://127.0.0.1/api/word/deleteSet.php',{
        method: 'POST',
        body: formData
      })
      .then(()=> {
         fetch('http://127.0.0.1/api/learn/delete.php',{
          method: 'POST',
          body: formData
        })
        .then(()=> {
          fetch('http://127.0.0.1/api/flashcard/delete.php',{
            method: 'POST',
            body: formData
          })
          .then(()=> {
            this.exit()
          })
        })
      })
    }
  }
}
</script>

<style lang="scss" scoped>
  .footer-word {
    display: flex;
    justify-content: space-around;
    background: white;
    padding: 10px;
    padding-left: 5px;
    padding-right: 5px;
    -webkit-box-shadow: 0 2px 2px 0 rgba(0, 0, 0, 0.14), 0 1px 5px 0 rgba(0, 0, 0, 0.12), 0 3px 1px -2px rgba(0, 0, 0, 0.2);
    box-shadow: 0 2px 2px 0 rgba(0, 0, 0, 0.14), 0 1px 5px 0 rgba(0, 0, 0, 0.12), 0 3px 1px -2px rgba(0, 0, 0, 0.2);
    border-radius: 10px;
    -webkit-transition: all 0.3s ease-in-out;
    -o-transition: all 0.3s ease-in-out;
    transition: all 0.3s ease-in-out;
    width: 95%;
    max-width: 750px;
    margin: auto;
    margin-top: 15px;

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
    padding-left: 5px;
    padding-right: 5px;
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
    margin-top: 20px;
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
    max-height: 93vh;
    padding: 0px;
    scroll-behavior: smooth;
    width: 95%;
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

  @media (max-width: 600px) {
    .words-head {
      input:first-of-type {
        margin-top: 40px !important;
      }
    }
  }

  @media (min-width: 490px) {
    .hidden {
      display: none;
    }

    .visible {
      display: inline-block;
    }
  }

  @media (max-width: 490px) {
    .hidden {
      display: inline-block;
    }

    .visible {
      display: none;
    }

    .accept {
      left: 10px;
      display: flex;
      flex-direction: column;
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
</style>