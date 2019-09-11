<template>
  <div id="app" class="h-100 dark d-flex flex-column noselect">
    <b-navbar toggleable="lg" type="dark">
      <b-navbar-nav>
        <b-nav-item @click="togglePlayback">
          <template v-if="playing"><i class="fa fa-lg fa-pause"></i></template>
          <template v-else><i class="fa fa-lg fa-play"></i></template>
        </b-nav-item>
      </b-navbar-nav>
      <b-navbar-toggle target="nav-collapse"></b-navbar-toggle>
      <b-collapse id="nav-collapse" is-nav>

        <b-navbar-nav class="ml-auto">
          <b-nav-text class="mx-2"><i class="fad fa-lg fa-stopwatch"></i> <span class="d-lg-none"> Speed </span></b-nav-text>
          <b-nav-form>
            <b-form-input type="range" @change="onChange" :min="timeout.min" :max="timeout.max" :step="timeout.step" v-model.number="timeout.value"></b-form-input>
          </b-nav-form>
          <b-nav-text class="mx-2">{{ this.timeout.value.toFixed(1) }} sec</b-nav-text>
        </b-navbar-nav>

        <b-navbar-nav class="ml-auto">
          <b-nav-text><i class="fad fa-lg fa-text-size"></i> <span class="d-lg-none"> Font Size </span></b-nav-text>
          <b-nav-form class="mx-2">
            <b-form-input type="range" :min="fontsize.min" :max="fontsize.max" :step="fontsize.step" v-model.number="fontsize.value"></b-form-input>
          </b-nav-form>
          <b-nav-text>{{ this.fontsize.value.toFixed(1) }} rem</b-nav-text>
        </b-navbar-nav>

        <b-navbar-nav class="ml-auto">
          <b-nav-text><i class="fad fa-lg fa-sort-amount-down"></i> <span class="d-lg-none"> Word Length </span></b-nav-text>
          <b-nav-form class="mx-2">
            <b-form-input type="range" @change="onChange" :min="letters.min" :max="letters.max" :step="letters.step" v-model.number="letters.value"></b-form-input>
          </b-nav-form>
          <b-nav-text>{{ this.letters.value.toFixed(0) }} chars</b-nav-text>
        </b-navbar-nav>

        <b-navbar-nav class="ml-auto">
          <b-nav-text><i class="fad fa-lg fa-filter"></i> <span class="d-lg-none"> Grade </span></b-nav-text>
          <b-nav-form class="mx-2">
            <b-form-input type="range" @change="onChange" :min="grade.min" :max="grade.max" :step="grade.step" v-model.number="grade.value"></b-form-input>
          </b-nav-form>
          <b-nav-text>{{ this.grade.value.toFixed(0) }} Grade</b-nav-text>
        </b-navbar-nav>
        
        <b-navbar-nav class="ml-auto">
          <b-nav-form class="mx-2">
            <b-form-checkbox v-model="difficulty" @change="onChange" value="TRUE" unchecked-value="FALSE">Difficult</b-form-checkbox>
          </b-nav-form>
        </b-navbar-nav>

      </b-collapse>
    </b-navbar>
    <div class="flex-grow-1 d-flex align-items-center justify-content-center" :style="`font-size:${fontsize.value}rem`">
      <span class="word mt-n5" @click="randomize">
        <template v-if="word">{{ word.Word }}</template>
        <template v-else>Oops</template>
      </span>
    </div>
  </div>
</template>

<script>
import data from './assets/data.json'
import { setInterval, clearInterval } from 'timers';
export default {
  name: "app",
  data () {
    return {
      playing: false,
      timeout: {
        min: 0.2,
        max: 2,
        step: 0.1,
        value: 1
      },
      fontsize: {
        min: 5,
        max: 15,
        step: 0.5,
        value: 10,
      },
      letters: {
        min: 1,
        max: 9,
        step: 1,
        value: 5,
      },
      grade: {
        min: 1,
        max: 5,
        step: 1,
        value: 3,
      },
      difficulty: 'FALSE',
      word: null,
      words: [],
      interval: null
    }
  },
  methods: {
    randomize() {
      var words = this.words.filter(function (el) {
        return el.Length == this.letters.value && 
               el.Difficult == this.difficulty && 
               el.Grade == this.grade.value;
      }.bind(this));
      this.word = words[Math.floor(Math.random() * words.length)]
    },
    togglePlayback() {
      if (this.playing) {
        clearInterval(this.interval)
      } else {
        this.interval = setInterval(this.randomize, this.timeout.value * 1000)
      }
      this.playing = !this.playing
    },
    onChange () {
      if (this.playing) {
        clearInterval(this.interval)
        this.interval = setInterval(this.randomize, this.timeout.value * 1000)
      } else {
        this.randomize()
      }
    },
    round(value, precision) {
      var multiplier = Math.pow(10, precision || 0);
      return Math.round(value * multiplier) / multiplier;
    }
  },
  mounted () {
    this.words = data.words
    this.randomize()
  }
}
</script>

<style>
html,
body {
  height: 100%;
  color: lightgray;
  background-color: #000;
}
#app {
  box-shadow: inset 0 0 5rem black;
  position: relative; 
}
.dark { 
  color: lightgray;
  background-color: #212121;
}
.word { cursor: pointer; text-shadow: 0 0 1rem black; }
.noselect {
  -webkit-touch-callout: none; /* iOS Safari */
    -webkit-user-select: none; /* Safari */
     -khtml-user-select: none; /* Konqueror HTML */
       -moz-user-select: none; /* Firefox */
        -ms-user-select: none; /* Internet Explorer/Edge */
            user-select: none; /* Non-prefixed version, currently
                                  supported by Chrome and Opera */
}
</style>
