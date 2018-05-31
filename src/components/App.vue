<template>
  <div id="app">
    <div class="columns">
      <div class="column is-1 is-offset-11">
        <a href="https://github.com/sergiix/fast-typing-vuejs" target="_blank">Source code</a>
      </div>
    </div>
    <div class="columns">
      <div class="column">
        <div class="notification">  
          <div class="words">
            <span 
              v-for="(word,index) in words" 
              v-bind:class="{word, current: index == currentWordIndex, error:index == currentWordIndex && error}"
              >{{word}}<span> </span></span>
          </div>
        </div>
        <div v-if="scene == 'menu'">
          <button class='button' @click="startTimer">Start</button>
        </div>
        <div v-if="scene == 'timer'">
          Start in {{left}} sec
        </div>
        <div v-if="scene == 'typing'">
          <div class="field">
            <div class="control">
              <input ref="input" autofocus @input="change" v-model="inputText" v-bind:class="{ 'is-danger': error}" class="input" />
            </div>
          </div>
        </div>
        <div v-if="scene == 'gameOver'">
          <div>Speed: {{speed}} words per minute.</div>
          <br />
          <button class='button' @click="startTimer">Start again</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
  let startInSeconds = 5
  export default {
    name: 'app',
    data() {
      return {
        text: 'It occurred to him that he had never, even for one moment, looked at the city through which they had been passing. He looked about now, a touch wildly. The buildings were low, but it was a cold planet - most of the structures were probably underground.',
        words: [],
        countWords: 0,
        currentWordIndex: -1,
        inputText: '',
        error: false,
        typing: false,
        beginTime: null,
        endTime: null,
        speed: 0,
        scene: 'menu',
        left: 0
      }
    },
    created() {
      this.words = this.text.split(' ')
      this.countWords = this.words.length
    },
    methods: {
      startTimer() {
        this.scene = 'timer'
        this.left = startInSeconds
        let id = setInterval(() => {
          if (--this.left > 0)
            return
          this.beginTyping()
          clearInterval(id)
        }, 1000)
      },

      beginTyping() {
        this.inputText = ''
        this.currentWordIndex = 0
        this.scene = 'typing'
        this.beginTime = +new Date()
        this.$nextTick(() => this.$refs.input.focus())
      },

      endTyping() {
        this.currentWordIndex = -1
        this.scene = 'gameOver'
        this.endTime = +new Date()
        this.speed = parseInt(this.countWords / ((this.endTime - this.beginTime) / 1000) *  60)
      },

      change(event) {
        let currentWord = this.words[this.currentWordIndex]
        
        if (this.currentWordIndex == this.countWords-1) {
          if (currentWord === this.inputText) {
            return this.endTyping()
          }
        }
        if (event.data === ' ') {
          if (currentWord + ' ' === this.inputText) {
            this.currentWordIndex++
            this.inputText = ''
            this.error = false
          } else 
            this.error = true
        }

        this.error = currentWord.indexOf(this.inputText) !== 0
      }
    }
  }
</script>

<!-- CSS libraries -->
<!--<style src="normalize.css/normalize.css"></style>-->
<style src="bulma/css/bulma.min.css"></style>

<!-- Global CSS -->
<style></style>

<!-- Scoped component css -->
<!-- It only affect current component -->
<style scoped>
  #app {
    margin: 1em;
  }

  .words {
    font-size: 1.5em;
    display: flex;
    flex-direction: row;
    flex-wrap: wrap;
  }
  .word {
    padding: 0 .125em;
  }
  .padding {

  }

  .current {
    color: blue;
    background: #FFEBCD;
  }
  .error {
    color: white;
    background: red;
  }
</style>
