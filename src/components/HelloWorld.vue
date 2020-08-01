<template>
  <div>
    <button @click="play"><span v-if="this.range.length===0 || gameOver">Start</span><span v-else>Restart</span></button>
    <div id="level">Simon level: <span>{{range.length}}</span></div>
    <div id="pie" ref="pie">
      <div :class="{'disabled': disabled, '': !disabled}" @click="tapBtn($event.target.id)" class="piece" id="1"></div>
      <div :class="{'disabled': disabled, '': !disabled}" @click="tapBtn($event.target.id)" class="piece" id="2"></div>
      <div :class="{'disabled': disabled, '': !disabled}" @click="tapBtn($event.target.id)" class="piece" id="3"></div>
      <div :class="{'disabled': disabled, '': !disabled}" @click="tapBtn($event.target.id)" class="piece" id="4"></div>
    </div>
    <div :key="e.value" v-for="e in complexity.values">
      <input :disabled="disableOptions" :id='e.text' :value="e.value" type="radio" v-model="complexity.selectedValue">
      <label :for="e.text">{{e.text}}</label>
    </div>
    <div style="color: red; font-size: 30px;" v-if="gameOver">
      Игра закончена, вы проиграли на {{range.length}} раундe.
    </div>
  </div>
</template>

<script>
export default {
	name: 'HelloWorld',
	components: {},
	data: () => {
		return {
			range: [],
			clickValue: 0,
			disabled: true,
      disableOptions: false,
			gameOver: false,
			complexity: {
				values: [{ text: 'Лёгкий', value: 1500 }, { text: 'Средний', value: 1000 }, { text: 'Сложный', value: 400 }],
				selectedValue: '1500'
			},
		}
	},
	methods: {
		tapBtn(e) {
			new Audio(`https://raw.githubusercontent.com/kellyk/javascript-simon/master/sounds/${e}.mp3`).play()
			this.clickValue++
			if (this.range[this.clickValue - 1] == e && this.clickValue === this.range.length) {
				// setTimeout(() => {this.setSeries()}, +this.complexity.selectedValue + 400)
				// если убрать +400 то получится "мёрж" звуков на сложной, но и задержка на других сложностях между раундами
				// либо же сделать так
				if (this.complexity.selectedValue == 400) {
					setTimeout(() => {this.setSeries()}, +this.complexity.selectedValue + 400)
				} else {
					setTimeout(() => {this.setSeries()}, +this.complexity.selectedValue)
				}
				this.disabled = true
			} else if (this.range[this.clickValue - 1] != e) {
				this.gameOver = true
				this.disabled = true
				this.disableOptions=false
			}
		},
		setSeries() {
			this.clickValue = 0
			let random = Math.floor(Math.random() * (5 - 1) + 1)
			this.range.push(random)
			this.range.forEach((e, i, a) => {
				setTimeout(() => {
					this.$refs.pie.childNodes[e - 1].style.opacity = '0.1'
					new Audio(`https://raw.githubusercontent.com/kellyk/javascript-simon/master/sounds/${e}.mp3`).play()
					setTimeout(() => {
						this.$refs.pie.childNodes[e - 1].style.opacity = '1'
						this.$refs.pie.childNodes[e - 1].style.opacity = ''
						if (i + 1 === a.length) {
							this.disabled = false
						}
					}, 300)
				}, this.complexity.selectedValue * i + 1)
			})
		},
		play() {
			this.range = []
			this.disableOptions = true
			this.gameOver = false
			setTimeout(() => this.setSeries(), this.complexity.selectedValue)
		}
	},

}
</script>
<style scoped lang="sass">

.disabled
  pointer-events: none


button
  border-radius: 2vw
  border: 0.5vw solid #ccc
  padding: 1vw 2.5vw
  cursor: pointer
  text-transform: uppercase


button:focus
  outline: none


#level
  font-size: 3.8vw


#pie
  width: 20vw
  height: 20vw
  min-width: 300px
  min-height: 300px
  margin: 2vw auto
  padding: 1.5vw
  background-color: #ddd
  border-radius: 50%
  /* Removing Gaps Between Inline child elements */
  font-size: 0


#pie.avoid-tap
  pointer-events: none



/* Landscape (default is portrait) */


.piece
  width: 50%
  height: 50%
  display: inline-flex
  cursor: pointer


.piece:hover
  opacity: 0.7


button:active,
.piece:active
  transform: scale(0.9)



/* green */

.piece:nth-child(1)
  border-radius: 100% 0 0 0
  background-color: #9BC53D


.piece:nth-child(1):active
  transform-origin: bottom right



/* blue */

.piece:nth-child(2)
  border-radius: 0 100% 0 0
  background-color: #00A8E8


.piece:nth-child(2):active
  transform-origin: bottom left



/* orange */

.piece:nth-child(3)
  border-radius: 0 0 0 100%
  background-color: #FF9F1C


.piece:nth-child(3):active
  transform-origin: top right



/* red */

.piece:nth-child(4)
  border-radius: 0 0 100% 0
  background-color: #E71D36


.piece:nth-child(4):active
  transform-origin: top left



/* black */

.piece.fail
  background-color: #50514F
  content: "sdf"


.piece.blink
  animation: 1s blink ease-in-out 1


@keyframes blink
  0%
    opacity: 1

  50%
    opacity: 0

  100%
    opacity: 1

</style>
