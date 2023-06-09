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
  <div class="container-fluid container-md">
    <div class="card border-0 shadow-sm mt-2 mt-md-5 ">
      <h2>Random Grind 75</h2>
      <div class="d-flex">
        <div class="d-flex flex-wrap mt-2 justify-content-center">
          <div class="px-2">
            <div>
              <h4>Easy</h4>
            </div>
            <div>
              <input type="number" v-model="select.easy" />
            </div>
          </div>
          <div class="px-2">
            <div>
              <h4>Medium</h4>
            </div>
            <div>
              <input type="number" v-model="select.medium" />
            </div>
          </div>
          <div class="px-2">
            <div>
              <h4>Hard</h4>
            </div>
            <div>
              <input type="number" v-model="select.hard" />
            </div>
          </div>
        </div>
      </div>
      
      
      <div class="mt-4 d-grid gap-2">
        <button type="button" v-if="!counting" class="btn btn-primary" @click="random(select)">Generate Questions</button>
        <button v-if="questions.length && seconds > 0 && !counting" type="button" class="btn btn-warning" @click="startTimer()">Start!</button>
        <button v-if="counting" type="button" class="btn btn-danger" @click="endTimer()">Stop!</button>
      </div>

      <div v-if="questions.length">
        <hr>

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
    
  </div>
 
</template>

<style scoped>

</style>