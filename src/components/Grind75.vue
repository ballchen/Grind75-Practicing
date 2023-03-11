<script setup lang="ts">
import { ref } from 'vue';
import Questions from '../data/questions.json';

const questions = ref([] as Array<any>);
const originalSeconds = ref(90 * 60);
const seconds = ref(90 * 60);
const counting = ref(false);
const startTime = ref(0);
const select = ref({
  easy: 1,
  medium: 1,
  hard: 0,
});
let timer: any;

function shuffle(array: any[]) {
  let currentIndex = array.length,  randomIndex;

  while (currentIndex != 0) {

    randomIndex = Math.floor(Math.random() * currentIndex);
    currentIndex--;

    [array[currentIndex], array[randomIndex]] = [
      array[randomIndex], array[currentIndex]];
  }

  return array;
}


function startTimer() {
  counting.value = true;
  startTime.value = Date.now();
  timer = setInterval(() => {
    const currentTime = Date.now();
    seconds.value = originalSeconds.value - Math.floor((currentTime - startTime.value) / 1000);
    if (seconds.value <= 0) {
      seconds.value = 0;
      counting.value = false;
      clearInterval(timer);
    }
    
  }, 1000);
}

function endTimer() {
  counting.value = false;
  if(timer) {
    clearInterval(timer);
  }
}

function formatSeconds(s: number) {
  const minutes = Math.floor(s / 60);
  const seconds = s % 60;
  return `${minutes}:${seconds < 10 ? '0' : ''}${seconds}`;
}

function random({easy=0, medium=2, hard=0}) {
  const shuffledQuestions = shuffle(Questions);
  const easyQuestions = shuffledQuestions.filter(q => q.difficulty === 'Easy');
  const mediumQuestions = shuffledQuestions.filter(q => q.difficulty === 'Medium');
  const hardQuestions = shuffledQuestions.filter(q => q.difficulty === 'Hard');

  originalSeconds.value = (easy * 30 + medium * 30 + hard * 50) * 60;
  seconds.value = (easy * 30 + medium * 30 + hard * 50) * 60;

  questions.value = [
    ...easyQuestions.slice(0, easy),
    ...mediumQuestions.slice(0, medium),
    ...hardQuestions.slice(0, hard),
  ];
}



</script>

<template>
  <div class="container">
    <div class="grind75">
      <h1>Grind 75</h1>
      <div class="d-flex mt-2">
        <div class="px-2">
          <div>
            <h3>Easy</h3>
          </div>
          <div>
            <input type="number" v-model="select.easy" />
          </div>
        </div>
        <div class="px-2">
          <div>
            <h3>Medium</h3>
          </div>
          <div>
            <input type="number" v-model="select.medium" />
          </div>
        </div>
        <div class="px-2">
          <div>
            <h3>Hard</h3>
          </div>
          <div>
            <input type="number" v-model="select.hard" />
          </div>
        </div>
      </div>
      
      <div class="mt-2 d-flex justify-content-center">
        <div class="px-2" v-if="!counting">
          <button type="button" class="btn btn-primary" @click="random(select)">Generate Questions</button>
        </div>
        <div class="px-2" v-if="questions.length && seconds > 0 && !counting" type="button">
          <button class="btn btn-warning" @click="startTimer()">Start!</button>
        </div>
        <div class="px-2" v-if="counting">
          <button type="button" class="btn btn-danger" @click="endTimer()">Stop!</button>
        </div>
      </div>

      <div class="mt-3 text-center">
        <h2 v-if="seconds > 0" :class="{ 'text-danger': counting }">{{ formatSeconds(seconds) }}</h2>
      </div>
      


      <div class="question-section mt-3" v-for="question in questions" :key="question.id">
        <div class="question-title">
          <a class="fs-4 fw-semibold" :href="question.link" target="_blank">{{ question.title }}</a> 
        </div>
        <div class="question-difficulty">
          <p class="text-muted fw-semibold">{{ question.difficulty }}</p>
        </div>
        
      </div>
    </div>
  </div>
 
</template>

<style scoped>

</style>