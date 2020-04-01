

<template>
  <div
    class="word-row"
  >
    <span>
      {{ index + 1 + '.' }}
    </span>
    <textarea
      v-model="at_language"
      :disabled="userid != userId"
      placeholder="z"
      onfocus="this.placeholder = ''"
      onblur="this.placeholder = 'z'"
    />
    <textarea
      v-model="to_language"
      :disabled="userid != userId"
      placeholder="na"
      onfocus="this.placeholder = ''"
      onblur="this.placeholder = 'na'"
    />
    <div>
      <i
        v-if="word.to_language != to_language || word.at_language != at_language"
        :class="{
          'red-save': to_language == null || to_language == '' || at_language == null || at_language == ''
        }"
        class="fas fa-save"
        style="margin-right: 5px;"
        title="Zapisz zmiany"
        @click="edit()"
      />
      <i
        v-if="learnedFlag"
        class="fas fa-check-circle"
      />
      <i
        v-if="userid == userId"
        class="fas fa-trash"
        title="Usuń pozycje"
        @click="deleteWord()"
      />
    </div>
  </div>
</template>

<script>
export default {
  name: 'wordRow',
  props: {
    word: {
      type: Object,
      default: null,
    },
    userid: {
      type: [Number, String],
      default: null,
    },
    index: {
      type: [Number, String],
      default: null,
    },
    learned: {
      type: [Object, Array],
      default: null,
    },
    token: {
      type: [Number, String],
      default: null,
    }
  },
  data() {
    return {
      at_language: '',
      to_language: '',
      learnedFlag: false,
      userId: '',
    }
  },
  watch: {
    token: function() {
      this.checker();
    }
  },
  mounted() {
    this.checker()
    this.to_language = this.word.to_language;
    this.at_language  = this.word.at_language;
    this.userId = localStorage.user_id;
  },
  methods: {
    edit() {
      if (this.to_language == null || this.to_language == '' || this.at_language == null || this.at_language == '') {
        return 0;
      }

      var formData = new FormData();
      formData.append('id', this.word.id);
      formData.append('at_language', this.at_language);
      formData.append('to_language', this.to_language);

      fetch('http://127.0.0.1/api/word/update.php',{
        method: 'POST',
        body: formData
      })
      .then(()=> {
        this.$emit('reload');
      })
    },
    deleteWord() {
      var formData = new FormData();
      formData.append('id', this.word.id);

      fetch('http://127.0.0.1/api/word/delete.php',{
        method: 'POST',
        body: formData
      })
      .then(()=> {
        this.$emit('reload');
      })
    },
    checker() {
      for (let i = 0; i < this.learned.length; i++) {
        if (this.learned[i] == this.word.id) {
          this.learnedFlag = true;

          return 0;
        }
      }

      this.learnedFlag = false;
    },
  }
}
</script>

<style lang="scss" scoped>
  .word-row {
    position: relative !important;
    right: 0;
  }

  @media(max-width: 700px) {
    .word-row {
      flex-direction: column;
    }

    div {
      font-size: 20px;
      position: absolute;
      right: 10px !important;
      top: 21px;
      cursor: pointer;

      .yel {
        color: #FFCC00 !important;
      }

      .fa-check-circle {
        color: green;
        margin-right: 5px;
      }
    }

    span {
      font-size: 20px;
      position: absolute;
      left: 30px !important;
      top: 21px;
    }
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

  div {
    font-size: 20px;
    position: absolute;
    right: 20px;
    top: 21px;
    cursor: pointer;

    .yel {
      color: #FFCC00 !important;
    }

    .fa-check-circle {
      color: green;
      margin-right: 5px;
    }
  }

  span {
    font-size: 20px;
    position: absolute;
    left: 60px;
    top: 21px;
  }
</style>