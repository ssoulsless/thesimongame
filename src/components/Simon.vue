<template>
	<div>
		<audio
			ref="sound1"
			src="https://s3.amazonaws.com/freecodecamp/simonSound1.mp3"
		></audio>
		<audio
			ref="sound2"
			src="https://s3.amazonaws.com/freecodecamp/simonSound2.mp3"
		></audio>
		<audio
			ref="sound3"
			src="https://s3.amazonaws.com/freecodecamp/simonSound3.mp3"
		></audio>
		<audio
			ref="sound4"
			src="https://s3.amazonaws.com/freecodecamp/simonSound4.mp3"
		></audio>

		<div class="simon-game">
			<h1>Simon game!</h1>
			<div class="score">
				<h4 class="current-score">Score : {{ count }}</h4>
			</div>
			<div class="difficulty-selection">
				<p>Выберите уровень сложности:</p>
				<select id="difficulty" v-model="difficulty">
					<option value="400">Легкий</option>
					<option value="1000">Средний</option>
					<option value="1500">Тяжелый</option>
				</select>
			</div>
			<p v-if="showError" class="error">Вы допустили ошибку!</p>
			<div class="game-pad">
				<button
					class="pad green"
					@click="input(1)"
					:class="{ highlight: highlightGreen }"
					id="green"
				></button>
				<button
					class="pad red"
					@click="input(2)"
					:class="{ highlight: highlightRed }"
					id="red"
				></button>
				<button
					class="pad yellow"
					@click="input(3)"
					:class="{ highlight: highlightYellow }"
					id="yellow"
				></button>
				<button
					class="pad blue"
					@click="input(4)"
					:class="{ highlight: highlightBlue }"
					id="blue"
				></button>
			</div>
			<button class="start input" @click="start">Start game!</button>
		</div>
	</div>
</template>
<script>
export default {
	data() {
		return {
			difficulty: 1000,
			isGame: false,
			count: 0,
			series: [],
			userInput: [],
			playingSeries: false,
			highlightRed: false,
			highlightYellow: false,
			highlightGreen: false,
			highlightBlue: false,
			allowInput: false,
			showError: false,
		};
	},
	computed: {
		displayCount() {
			return this.count === 0 ? '- -' : this.count;
		},
	},
	methods: {
		reset() {
			this.difficulty = 1000;
			this.isGame = false;
			this.count = 0;
			this.series = [];
			this.userInput = [];
			this.playingSeries = false;
			this.highlightRed = false;
			this.highlightYellow = false;
			this.highlightGreen = false;
			this.highlightBlue = false;
			this.allowInput = false;
			this.isWin = false;
		},
		input(tone) {
			if (!this.allowInput) return;
			this.playTone(tone);
			this.userInput.push(tone);
			// Check if this was the wrong input
			if (
				this.userInput[this.userInput.length - 1] !=
				this.series[this.userInput.length - 1]
			) {
				this.userInput = [];
				this.allowInput = false;
				this.showError = true;
				setTimeout(this.reset, 1000);
				return;
			}
			if (this.userInput.length == this.series.length) {
				let self = this;
				this.userInput = [];
				setTimeout(function() {
					self.addTone();
					self.playSeries();
				}, 1000);
			}
		},
		start() {
			if (this.count == 0) {
				this.isGame = true;
				this.addTone();
				this.playSeries();
				this.showError = false;
			}
		},
		addTone() {
			this.count++;
			this.series.push(this.randomTone());
		},
		playSeries() {
			this.allowInput = false;
			this.playingSeries = true;
			let delay = +this.difficulty;
			let initialDelay = +this.difficulty;
			let self = this;
			this.series.forEach(function(tone, index, array) {
				if (index == array.length - 1)
					setTimeout(function() {
						if (self.isGame) {
							self.playTone(tone);
							self.allowInput = true;
							self.playingSeries = false;
						}
					}, delay);
				else
					setTimeout(function() {
						self.playTone(tone);
					}, delay);
				console.log(delay);
				delay = initialDelay * (index + 2);
			});
			this.playingSeries = false;
		},
		clearHighlights() {
			this.highlightGreen = false;
			this.highlightRed = false;
			this.highlightYellow = false;
			this.highlightBlue = false;
		},
		playTone(tone) {
			switch (tone) {
				case 1:
					this.$refs.sound1.pause();
					this.$refs.sound1.currentTime = 0;
					this.$refs.sound1.play();
					this.highlightGreen = true;
					break;
				case 2:
					this.$refs.sound2.pause();
					this.$refs.sound2.currentTime = 0;
					this.$refs.sound2.play();
					this.highlightRed = true;
					break;
				case 3:
					this.$refs.sound3.pause();
					this.$refs.sound3.currentTime = 0;
					this.$refs.sound3.play();
					this.highlightYellow = true;
					break;
				case 4:
					this.$refs.sound4.pause();
					this.$refs.sound4.currentTime = 0;
					this.$refs.sound4.play();
					this.highlightBlue = true;
					break;
			}
			setTimeout(this.clearHighlights, this.difficulty - 100);
		},
		randomTone() {
			return Math.floor(Math.random() * 4) + 1;
		},
	},
};
</script>
<style scoped>
.game-pad {
	margin: 2rem auto 0 auto;
	max-width: 680px;
	min-width: 680px;
	max-height: 680px;
	min-height: 680px;
	display: flex;
	align-items: center;
	justify-content: center;
	position: relative;
	display: flex;
	flex-wrap: wrap;
	max-width: 620px;
	min-width: 620px;
	justify-content: space-between;
}
h1 {
	margin-top: 20px;
	text-align: center;
	width: auto;
	margin: 0 auto;
}
.pad {
	cursor: pointer;
	min-width: 300px;
	min-height: 300px;
	max-width: 300px;
	max-height: 300px;
}
.green {
	background-color: green;
}
.red {
	background-color: red;
}
.yellow {
	background-color: yellow;
}
.blue {
	background-color: blue;
}

#green.highlight {
	background-color: lightgreen;
}
#red.highlight {
	background-color: lightcoral;
}
#yellow.highlight {
	background-color: lightyellow;
}
#blue.highlight {
	background-color: lightblue;
}
.error {
	text-transform: uppercase;
	font-size: 36px;
	color: red;
}
</style>
