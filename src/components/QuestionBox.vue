<template>
	<div>
		<b-jumbotron>
			<template v-slot:lead>
				{{ currentQuestion.question}}
			</template>

			<hr class="my-4">

			<b-list-group>
				<b-list-group-item 
					v-for="(answer, index) in answers" 
					:key="index"
					@click.prevent="selectAnswer(index)"
					:class="selectAnswerClass(index)"
				>
					 {{ answer }}
				</b-list-group-item>
			</b-list-group>

			<b-button
			 variant="primary"
			 @click="submitAnswer"
			 :disabled="selectedIndex === null || answered"
		 	>
			Submit
		</b-button>&nbsp;
			<b-button @click="next" variant="success" href="#">Next</b-button>
		</b-jumbotron>
	</div>
</template>

<script >
	import _ from 'lodash'

	export default{
		props: {
			currentQuestion: Object,
			next: Function,
			increment: Function
		},
		data() {
			return {
				selectedIndex: null,
				correctIndex: null,
				shuffledAnswers: [],
				answered: false
			}
		},
		computed: {
			answers() {
				let answers = [...this.currentQuestion.incorrect_answers]
				answers.push(this.currentQuestion.correct_answer)
				return answers
			}
		},
		watch: {
			currentQuestion:{
				immediate: true,
				handler() {
					this.selectedIndex = null
					this.shuffleAnswers()
					this.answered = false
				} 
			}
		},
		methods: {
			selectAnswer(index) {
				this.selectedIndex = index
			},
			shuffleAnswers() {
					let answers = [...this.currentQuestion.incorrect_answers, this.currentQuestion.correct_answer]
					this.shuffledAnswers = _.shuffle(answers)
					this.correctIndex = this.shuffledAnswers.indexOf(this.currentQuestion.correct_answer)
			},
			submitAnswer(){
				let isCorrect = false
				if (this.selectedIndex === this.correctIndex){
					isCorrect = true
				}
				this.increment(isCorrect)
				this.answered = true
			},
			selectAnswerClass(index) {
				if (!this.answered && this.selectedIndex === index) {
					return 'selected'
				}
				else if (this.answered && this.correctIndex === index){
					return 'correct'
				}
				else if (this.answered && this.correctIndex !== index && this.selectedIndex === index) {
					return 'incorrect'
				}
				else{
					return ''
				}		
			}
		},
	}
</script>

<style scoped>
	.list-group{
		margin-bottom: 15px;
	}

	.list-group-item:hover{
		background: #dfd8d8;
		cursor: pointer;
		color: black;
	}

	.selected {
		background-color: blue;
		color: white;
	}

	.correct {
		background-color: lightgreen;
	}

	.incorrect {
		background-color: red;
	}
</style>