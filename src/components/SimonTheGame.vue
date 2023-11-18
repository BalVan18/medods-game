<template>
  <div class="hello">
    <h1>Simon</h1>
    <div class="container">
      <div class="game-bord" ref="gameBoard" :class="{ 'disabled': this.isGameBoardDisabled}">
        <div class="simon">
          <ul>
            <li class="tile red" ref="1" data-id="1" @click="clickHandler($event.target)" :class="{ 'active': this.isFirstTileActive}"></li>
            <li class="tile blue" ref="4" data-id="4" @click="clickHandler($event.target)" :class="{ 'active': this.isFourthTileActive}"></li>
            <li class="tile yellow" ref="3" data-id="3" @click="clickHandler($event.target)" :class="{ 'active': this.isThirdTileActive}"></li>
            <li class="tile green" ref="2" data-id="2" @click="clickHandler($event.target)" :class="{ 'active': this.isSecondTileActive}"></li>
          </ul>
        </div>
      </div>
      <div class="game-wrap">
        <div class="game-info">
          <h2>Уровень: <span>{{ this.roundNumber }}</span></h2>
          <button class="btn" @click="init()" >Играть</button>
          <p class="game-info__error" :class="{ 'lose': this.isGameLost }">Вы проиграли после <span>{{ this.roundNumber }}</span> уровня!</p>
        </div>
        <div class="game-options">
          <h2>Режим игры:</h2>
          <label>
            Лёгкий
            <input type="radio" checked name="difficult" value="1500" ref="easy" @click="changeMode($event.target)">
          </label>
          <label>
            Средний
            <input type="radio" name="difficult" value="1000" ref="normal" @click="changeMode($event.target)">
          </label>
          <label>
            Сложный
            <input type="radio" name="difficult" value="400" ref="hard" @click="changeMode($event.target)">
          </label>
        </div>
      </div>
    </div>
    <div class="sound">
      <audio ref="audioElement" :src="audioFile1" autoplay v-if="isFirstAudioPlay" ></audio>
      <audio ref="audioElement" :src="audioFile2" autoplay v-if="isSecondAudioPlay" ></audio>
      <audio ref="audioElement" :src="audioFile3" autoplay v-if="isThirdAudioPlay" ></audio>
      <audio ref="audioElement" :src="audioFile4" autoplay v-if="isFourthAudioPlay" ></audio>
    </div>
  </div>
</template>

<script>


export default {
  name: 'SimonTheGame',
  
  data() {
    return {
      isGameBoardDisabled: false,
      isFirstTileActive: false,
      isSecondTileActive: false,
      isThirdTileActive: false,
      isFourthTileActive: false,
      isFirstAudioPlay: false,
      isSecondAudioPlay: false,
      isThirdAudioPlay: false,
      isFourthAudioPlay: false,
      delay: 1500,
      isGameStarted: false,
      isGameLost: false,
      roundNumber: 0,
      clicks: 0,
      sequence: [],
      audioFile1: require('@/assets/sound/1.mp3'),
      audioFile2: require('@/assets/sound/2.mp3'),
      audioFile3: require('@/assets/sound/3.mp3'),
      audioFile4: require('@/assets/sound/4.mp3')
    }
  },

  methods: {
    init() {
      if (this.isGameStarted) {
        this.endGame()
        return
      }
      this.roundNumber = 1
      this.isGameLost = false
      if (this.$refs.easy.checked) this.delay = Number(this.$refs.easy.value)
      if (this.$refs.normal.checked) this.delay = Number(this.$refs.normal.value)
      if (this.$refs.hard.checked) this.delay = Number(this.$refs.hard.value)
      this.gameStart();
    },

    clickHandler(target) {
      let colorId
      if ( this.isGameStarted ) {
        this.clicks++
        colorId = target.dataset.id
        if (Number(colorId) === this.sequence[this.clicks - 1]) {
          this.toggleSound(colorId)
          setTimeout( () => this.toggleSound(colorId), 600) 
          if (this.sequence.length === (this.clicks)) {
            this.clicks = 0
            this.nextRound()
          }
        } else {
          this.endGame()
        }
      } else {
        this.init()
      }
    },

    async nextRound() {
      this.clicks = 0
      this.roundNumber++
      await this.sleep(1000)
      this.gameStart()
    },

    endGame() {
      for (let i = 0; i < this.sequence.length; i++) {
        this.removeColor(i)
      }
      this.isGameStarted = false
      this.isGameLost = true
      this.clicks = 0
      this.sequence = []
    },

    toggleGameBoardState() {
      this.isGameBoardDisabled = !this.isGameBoardDisabled
    },

    randomNumber() {
      return Math.floor((Math.random()*4)+1)
    },

    async gameStart() {
      this.isGameStarted = true
      this.sequence.push(this.randomNumber())
      this.toggleGameBoardState()
      for (let i = 0; i < this.sequence.length; i++) {
        this.toggleColor(i)
        this.toggleSound(i, 'gameStart')
        setTimeout(() => {
          this.toggleColor(i)
          this.toggleSound(i, 'gameStart')
        }, 600)
        await this.sleep(this.delay + 600)
      }
      this.toggleGameBoardState()
    },

    changeMode(target) {
      this.delay = Number(target.value)
    },

    sleep(ms) {
      return new Promise(resolve => setTimeout(resolve, ms))
    },

    toggleColor(i) {
      switch(this.sequence[i]) {
        case 1:
          this.isFirstTileActive = !this.isFirstTileActive 
          break

        case 2:
          this.isSecondTileActive = !this.isSecondTileActive
          break

        case 3:
          this.isThirdTileActive = !this.isThirdTileActive
          break

        case 4:
          this.isFourthTileActive = !this.isFourthTileActive
          break
      }
    },

    removeColor(i) {
      switch(this.sequence[i]) {
        case 1:
          this.isFirstTileActive = false
          break

        case 2:
          this.isSecondTileActive = false
          break

        case 3:
          this.isThirdTileActive = false
          break

        case 4:
          this.isFourthTileActive = false
          break
      }
    },

    toggleSound(i, event) {
      let currentAudio
      
      if (event === 'gameStart') {
        currentAudio = this.sequence[i]
      } else {
        currentAudio = Number(i)
      }
      switch(currentAudio) {
        case 1:
          this.isFirstAudioPlay = !this.isFirstAudioPlay
          break

        case 2:
          this.isSecondAudioPlay = !this.isSecondAudioPlay
          break

        case 3:
          this.isThirdAudioPlay = !this.isThirdAudioPlay
          break

        case 4:
          this.isFourthAudioPlay = !this.isFourthAudioPlay
          break
      }
    },
  },
}
</script>

<style lang="sass">

</style>
