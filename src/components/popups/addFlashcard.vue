<template>
  <div
    class="scroll tile-pop"
  >
    <div>
      <div class="words-head">
        <input
          v-model="title"
          :class="{
            'not-valid': title == null && validate || title == '' && validate
          }"
          class="input"
          type="text"
          style="margin-top: 20px; margin-bottom: 10px;"
          placeholder="Nazwa"
          onfocus="this.placeholder = ''"
          onblur="this.placeholder = 'Nazwa'"
        >
        <input
          v-model="topic"
          :class="{
            'not-valid': topic == null && validate || topic == '' && validate
          }"
          class="input"
          type="text"
          style="margin: auto; color: gray; text-align: center; display: block; font-weight: normal; font-size: 1em; margin-bottom: 20px"
          placeholder="Temat"
          onfocus="this.placeholder = ''"
          onblur="this.placeholder = 'Temat'"
        >
        <div class="word-selection">
          <div>
            <select
              v-model="fromLanguage"
              :class="{
                'not-valid': fromLanguage == toLanguage && validate
              }"
            >
              <option
                v-for="lang of languageArray"
                :key="lang.id"
                :value="lang"
              >
                {{ lang.name }}
              </option>
            </select>
          </div>
          <div style="width: 26px;" />
          <div>
            <select
              v-model="toLanguage"
              :class="{
                'not-valid': fromLanguage == toLanguage && validate
              }"
            >
              <option
                v-for="lang of languageArray"
                :key="lang.id"
                :value="lang"
              >
                {{ lang.name }}
              </option>
            </select>
          </div>
        </div>
        <div
          class="flags"
          style="display: flex;"
        >
          <img
            :src="images[fromLanguage.id]"
            width="60px"
            height="40px"
          >
          <i
            class="fas fa-arrow-right"
            style="display: flex; align-content: center"
          />
          <img
            :src="images[toLanguage.id]"
            width="60px"
            height="40px"
          >
        </div>
        <div
          class="icons-user"
        >
          <i
            class="fas fa-save"
            title="Utwórz zestaw"
            @click="send()"
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
      >
        <input
          id="remind-check"
          v-model="publicStatus"
          type="checkbox"
          style="display:none"
        >
        Prywatny
        <label
          for="remind-check"
          class="toggle"
        >
          <span />
        </label>
        Publiczny
      </div>
      <div class="words">
        <div style="position: sticky; top: 0;">
          <h3
            style="margin-bottom: 0px;"
          >
            Dodawanie słówka
          </h3>
        </div>
        <div>
          <div
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
              @click="addRow()"
            />
          </div>
          <hr
            style="width: 90%; margin: auto; background-color: #92589E; height: 3px; border: none;"
            :class="{
              'not-valid': words.length == 0 && validate
            }"
          >
        </div>
        <div style="height: 40vh; overflow-y: auto;">
          <template v-for="(word,index) of words">
            <wordRowAdd
              :key="index"
              :word="word"
              :index="index"
              @delete="delRow"
              @edit="editRow"
            />
            <hr
              v-if="index + 1 != words.length"
              :key="index + 'x'"
              style="width: 80%; margin: auto; background-color: lightgray; height: 3px; border: none;"
            >
          </template>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import wordRowAdd  from '../wordRowAdd.vue';
export default {
  name: 'addFlashcard',
  components: {
    wordRowAdd,
  },
  data() {
    return {
      publicStatus: true,
      languageArray: [],
      words: [],
      flashcard: [],
      images: [
        'xd',
        'http://127.0.0.1/api/files/english.png',
        'http://127.0.0.1/api/files/polish.png',
        'http://127.0.0.1/api/files/german.png',
      ],
      toLanguage: 1,
      fromLanguage: 2,
      at_language: '',
      to_language: '',
      wordStatus: false,
      topic: '',
      title: '',
      token: 'x',
      validate: false,
    }
  },
  mounted() {
    this.loadSelects();
  },
  methods: {
    addStatusFunc() {
      if (this.addStatus) {
        this.addStatus = false;
      }
      else {
        this.addStatus = true;
      }
    },
    addRow() {
      this.words.push({
        at_language: this.at_language,
        to_language: this.to_language,
      })

      this.at_language = null,
      this.to_language = null;
    },
    delRow(index) {
      this.words.splice(index, 1);
    },
    editRow(index, at_language, to_language) {
      this.words[index].at_language = at_language;
      this.words[index].to_language = to_language;
    },
    exit() {
      this.$emit('exit');
    },
    loadSelects() {
      fetch('http://127.0.0.1/api/lang/read.php',{
        method: 'POST'
      })
      .then(response=> response.json())
      .then(response =>{
        this.languageArray = response;
        this.toLanguage = response[0];
        this.fromLanguage = response[1];
      });
    },
    validFunc() {
      this.validate = true;
    },
    async send() {
      await this.validFunc();

      if (document.querySelectorAll('.not-valid').length == 0) {
        var formData = new FormData();

        formData.append('title', this.title);
        formData.append('topic', this.topic);
        formData.append('user_id', localStorage.user_id);
        formData.append('at_language_id', this.fromLanguage.id);
        formData.append('to_language_id', this.toLanguage.id);

        if (this.publicStatus) {
          formData.append('public', 1);
        }
        else {
          formData.append('public', 0);
        }


        fetch('http://127.0.0.1/api/flashcard/create.php',{
          method: 'POST',
          body: formData
        })
        .then(response => response.json())
        .then(response => {
          this.$emit('load');

          for (let i = 0; i < this.words.length; i++) {
            let formDataWord = new FormData();
            formDataWord.append('flashcard_id', response.id);
            formDataWord.append('at_language', this.words[i].at_language);
            formDataWord.append('to_language', this.words[i].to_language);

            fetch('http://127.0.0.1/api/word/create.php',{
              method: 'POST',
              body: formDataWord
            })
          }

          this.exit();
        })
      }
    }
  }
}
</script>

<style lang="scss" scoped>
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
    padding-left: 5px;
    padding-right: 5px;

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
    padding-left: 5px;
    padding-right: 5px;
    border-radius: 10px;
    width: 95%;
    margin: auto;
    margin-top: 10px;
    margin-bottom: 0px;
    max-width: 750px;
    position: relative;
    min-height: 50vh;
    background: white;

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

  @media (max-width: 600px) {
    .words-head {
      input:first-of-type {
        margin-top: 40px !important;
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