<template>
  <div
    class="tile-pop-word"
  >
    <template v-if="!topStatus">
      <div
        v-show="!rotateStatus"
        class="to-lang"
        :class="{
          'learned': learnedFlag,
          'not-learned': !learnedFlag,
        }"
      >
        <i
          v-if="position != 0"
          class="fas fa-chevron-left"
          @click="back()"
        />
        <h2 @click="rotateFlash">
          <div class="options">
            <i
              class="fas fa-exchange-alt"
              title="Zmień ustawienie zestawu"
              @click="topFunc()"
            />
            <img
              :src="imgat"
            >
            <i
              v-if="!learnedFlag"
              class="fas fa-plus"
              title="Nauczone"
              @click="addLearned()"
            />
            <i
              v-else
              class="fas fa-times"
              title="Nienauczone"
              @click="delLearned()"
            />
          </div>
          <span>{{ words[position].at_language }}</span>
        </h2>
        <i
          v-if="position != words.length-1"
          class="fas fa-chevron-right"
          @click="next()"
        />
      </div>
      <div
        v-show="rotateStatus"
        class="to-lang"
        :class="{
          'learned': learnedFlag,
          'not-learned': !learnedFlag,
        }"
      >
        <i
          v-if="position != 0"
          class="fas fa-chevron-left"
          @click="back()"
        />
        <h2 @click="rotateFlash">
          <div class="options">
            <i
              class="fas fa-exchange-alt"
              @click="topFunc()"
            />
            <img
              :src="imgto"
            >
            <i
              v-if="!learnedFlag"
              class="fas fa-plus"
              @click="addLearned()"
            />
            <i
              v-else
              class="fas fa-times"
              @click="delLearned()"
            />
          </div>
          <span>{{ words[position].to_language }}</span>
        </h2>
        <i
          v-if="position != words.length-1"
          class="fas fa-chevron-right"
          @click="next()"
        />
      </div>
    </template>
    <template v-else>
      <div
        v-show="!rotateStatus"
        class="to-lang"
        :class="{
          'learned': learnedFlag,
          'not-learned': !learnedFlag,
        }"
      >
        <i
          v-if="position != 0"
          class="fas fa-chevron-left"
          @click="back()"
        />
        <h2 @click="rotateFlash">
          <div class="options">
            <i
              class="fas fa-exchange-alt"
              title="Zmień ustawienie zestawu"
              @click="topFunc()"
            />
            <img
              :src="imgto"
            >
            <i
              v-if="!learnedFlag"
              class="fas fa-plus"
              title="Nauczone"
              @click="addLearned()"
            />
            <i
              v-else
              class="fas fa-times"
              title="Nienauczone"
              @click="delLearned()"
            />
          </div>
          <span>{{ words[position].to_language }}</span>
        </h2>
        <i
          v-if="position != words.length-1"
          class="fas fa-chevron-right"
          @click="next()"
        />
      </div>
      <div
        v-show="rotateStatus"
        class="to-lang"
        :class="{
          'learned': learnedFlag,
          'not-learned': !learnedFlag,
        }"
      >
        <i
          v-if="position != 0"
          class="fas fa-chevron-left"
          @click="back()"
        />
        <h2 @click="rotateFlash">
          <div class="options">
            <i
              class="fas fa-exchange-alt"
              title="Zmień ustawienie zestawu"
              @click="topFunc()"
            />
            <img
              :src="imgat"
            >
            <i
              v-if="!learnedFlag"
              class="fas fa-plus"
              title="Nauczone"
              @click="addLearned()"
            />
            <i
              v-else
              class="fas fa-times"
              title="Nienauczone"
              @click="delLearned()"
            />
          </div>
          <span>{{ words[position].at_language }}</span>
        </h2>
        <i
          v-if="position != words.length-1"
          class="fas fa-chevron-right"
          @click="next()"
        />
      </div>
    </template>
    <div
      class="footer-word"
      :class="{
        'completed': learnedArray.length == words.length
      }"
    >
      <span class="visible">Nauczone słówka: <span>{{ learnedArray.length }}/{{ words.length }}</span></span>
      <span class="visible">Procent ukończenia: <span>{{ (learnedArray.length/words.length).toFixed(2) * 100 }}%</span></span>
      <span class="hidden">Nauczone: <span>{{ learnedArray.length }}/{{ words.length }}</span></span>
      <span class="hidden">Ukończono: <span>{{ (learnedArray.length/words.length).toFixed(2) * 100 }}%</span></span>
    </div>
  </div>
</template>

<script>
export default {
  name: 'showWord',
  props: {
    words: {
      type: [Object, Array],
      default: null,
    },
    imgat: {
      type: String,
      default: null,
    },
    imgto: {
      type: String,
      default: null,
    },
    learned: {
      type: [Object, Array],
      default: null,
    }
  },
  data() {
    return {
      position: 0,
      rotateStatus: false,
      learnedArray: [],
      learnedFlag: false,
      topStatus: false,
    }
  },
  watch: {
    learnedArray: function() {
      this.$emit('update', this.learnedArray);
    }
  },
  mounted() {
    this.learnedArray = this.learned;
    this.checker();
  },
  methods: {
    checker() {
      for (let i = 0; i < this.learnedArray.length; i++) {
        if (this.learnedArray[i] == this.words[this.position].id) {
          this.learnedFlag = true;

          return 0;
        }
      }

      this.learnedFlag = false;
    },
    addLearned() {
      this.learnedArray.push(this.words[this.position].id);

      this.checker();
    },
    delLearned() {
      for (let i = 0; i < this.learnedArray.length; i++) {
        if (this.learnedArray[i] == this.words[this.position].id) {
          this.learnedArray.splice(i,1);

          break;
        }
      }

      this.checker();
    },
    rotateFlash(event) {
      if (event.target.tagName == 'H2') {
        if (this.rotateStatus) {
          this.rotateStatus = false;
        }
        else {
          this.rotateStatus = true;
        }
      }
    },
    back() {
      this.position--;
      this.rotateStatus = false;
      this.checker();
    },
    next() {
      this.position++;
      this.rotateStatus = false;
      this.checker();
    },
    topFunc() {
      if (this.topStatus) {
        this.topStatus = false;
      }
      else {
        this.topStatus = true;
      }
    }
  }
}
</script>

<style lang="scss" scoped>
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

  h2 {
    height: 100%;
    display: flex;
    align-items: center;
    width: 100%;
    justify-content: center;
    flex-direction: column;

    span {
      margin-top: 20px;
      display: block;
      font-weight: bold;
    }
  }

  .tile-pop-word {
    align-self: center;
    overflow: hidden;

    .to-lang {
      transition-duration: 2s;
      border-radius: 3px;
      padding: 20px;
      padding-top: 0px;
      -webkit-box-shadow: 0 2px 2px 0 rgba(0, 0, 0, 0.14), 0 1px 5px 0 rgba(0, 0, 0, 0.12), 0 3px 1px -2px rgba(0, 0, 0, 0.2);
      box-shadow: 0 2px 2px 0 rgba(0, 0, 0, 0.14), 0 1px 5px 0 rgba(0, 0, 0, 0.12), 0 3px 1px -2px rgba(0, 0, 0, 0.2);
      margin-bottom: 1px;
      -webkit-transition: all 0.3s ease-in-out;
      -o-transition: all 0.3s ease-in-out;
      transition: all 0.3s ease-in-out;
      border-radius: 10px;
      background: #FFCC00;
      height: 50vh;
      width: 50vh;
      max-width: 80vw;
      display: flex;
      justify-content: center;
      align-items: center;
      position: relative;
      cursor: pointer;

      .fa-chevron-left, .fa-chevron-right {
        font-size: 40px;
        color: white;
        position: absolute;
        height: 100%;
        display: flex;
        align-items: center;
        padding-left: 20px;
        padding-right: 20px;
        top: 0px;
        cursor: pointer;

        &:hover {
          color: white !important;
          background-color: rgba(0, 0, 0, 0.4);
        }
      }

      .fa-chevron-left {
        left: 0;
        border-top-left-radius: 10px;
        border-bottom-left-radius: 10px;
      }

      .fa-chevron-right {
        right: 0;
        border-top-right-radius: 10px;
        border-bottom-right-radius: 10px;
      }

      h2 {
        color: block;
        font-weight: normal;
      }
    }
  }

  img {
    display: inline-block;
    border-radius: 5px;
    width: 70px;
    height: 45px;
  }

  .options {
    position: absolute;
    display: flex;
    bottom: 5px;
    height: fit-content;
    background: rgba(0, 0, 0, 0.4);
    padding: 10px;
    border-radius: 10px;
    cursor: auto;
    color: white;

    i {
      font-size: 20px;
      margin-left: 10px;
      margin-right: 10px;
      cursor: pointer;
      display: flex;
      align-items: center;
    }
  }

  .not-learned {
    background: white !important;
  }

  .learned {
    background: green !important;
    color: white !important;

    .fa-chevron-left, .fa-chevron-right {
      color: green !important;
    }
  }

  .completed {
    background: green !important;
    color: white !important;
  }

  .footer-word {
    display: flex;
    justify-content: space-around;
    background: white;
    padding: 15px;
    -webkit-box-shadow: 0 2px 2px 0 rgba(0, 0, 0, 0.14), 0 1px 5px 0 rgba(0, 0, 0, 0.12), 0 3px 1px -2px rgba(0, 0, 0, 0.2);
    box-shadow: 0 2px 2px 0 rgba(0, 0, 0, 0.14), 0 1px 5px 0 rgba(0, 0, 0, 0.12), 0 3px 1px -2px rgba(0, 0, 0, 0.2);
    border-radius: 10px;
    margin-top: 10px;
    -webkit-transition: all 0.3s ease-in-out;
    -o-transition: all 0.3s ease-in-out;
    transition: all 0.3s ease-in-out;

    span {
      span {
        font-weight: bold;
      }
    }
  }
</style>