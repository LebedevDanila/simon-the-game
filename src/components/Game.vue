<template>
  <div class="game">
    <div class="game__circle">
      <div
        class="game__circle-part"
        v-for="(tile, idx) in tiles"
        :class="{[tile.color]: true, active: tile.isActive}"
        :key="idx"
        @click="pickTile(idx)"
        :disabled="tilesIsDisabled"
      ></div>
    </div>

    <div class="game__content">
      <div class="game__info">
        <div class="game__info-round">Round: {{quantityRound}}</div>
        <button
          class="game__info-start"
          @click="start"
          :disabled="startBtnIsDisabled"
        >
          {{isLose ? 'Restart' : 'Start'}}
        </button>
        <div class="game__info-lose" v-if="isLose">Sorry, you lost after {{quantityRound}} rounds!</div>
      </div>

      <div class="game__options">
        <div class="game__options-title">Game options:</div>
        <div class="game__options-item" v-for="(option, idx) in options" :key="idx">
          <input type="radio" name="complexity" :value="idx" checked v-model="pickedOption">
          <span>{{option.name}}</span>
        </div>
      </div>
    </div>
  </div>
</template>

<script>

export default {
  name: 'Game',
  data: () => ({
    tiles: [
      {color: 'red', isActive: false}, 
      {color: 'blue', isActive: false}, 
      {color: 'yellow', isActive: false}, 
      {color: 'green', isActive: false}
    ],
    options: [
      {name: 'ease', delay: 1500},
      {name: 'middle', delay: 1000},
      {name: 'hard', delay: 400}
    ],
    startBtnIsDisabled: false,
    tilesIsDisabled: true,
    pickedOption: 1,
    sequence: [],
    quantityRound: 0,
    userClicks: 0,
    isLose: false
  }),
  computed: {
    delay() {
      return this.options[this.pickedOption].delay
    }
  },
  methods: {
    start() {
      // if user lose last game reset round and boolean lose flag
      if(this.isLose) {
        this.isLose = false
        this.quantityRound = 0
      }

      this.startRound()
    },
    startRound() {
      // disable start button
      this.startBtnIsDisabled = true

      // delay before the round
      setTimeout(() => {
        // reset user clicls in the last round
        this.userClicks = 0

        this.quantityRound++

        this.calculateSequence()
        this.roundAnimation()
      }, 750)
    },
    roundAnimation() {
      // turn off tiles until the end of the animation
      this.tilesIsDisabled = true

      let i = 0
      let timerId = setInterval(() => {
        this.lightAndAudio(this.sequence[i])

        i++
        if (i >= this.sequence.length) {
          clearInterval(timerId)
          // to include tiles as well as animation ended
          this.tilesIsDisabled = false
        }
      }, this.delay)
    },
    pickTile(id) {
      if(this.tilesIsDisabled) return false
      
      this.lightAndAudio(id)

      if(!(this.sequence[this.userClicks] === id)) {
        this.isLose = true

        this.startBtnIsDisabled = false
        this.tilesIsDisabled = true

        return false
      }

      // if the user clicked on the tiles one after the other, we move them to the next round
      if(this.userClicks === this.sequence.length-1) {
        this.startRound()
      }

      this.userClicks++
    },
    lightAndAudio(id) {
      (new Audio(`sounds/${id}.mp3`)).play();

      this.tiles[id].isActive = true

      setTimeout(() => {
        this.tiles[id].isActive = false
      }, this.delay / 1.7)
    },
    calculateSequence() {
      const calculatedSequence = []
      
      for(let i = 0; i < this.quantityRound; i++)
        calculatedSequence.push(this.randomNumber())
    
      this.sequence = calculatedSequence
    },
    randomNumber() {
      return Math.floor((Math.random() * this.tiles.length))
    }
  }
}
</script>
