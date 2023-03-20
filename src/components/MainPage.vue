<template>
  <div class="container jumbotron px-5">
    <h1 class="display-4">Hızlı Yazma Yarışması</h1>
    <p class="lead">Ne kadar hızlı klavye kullandığını test et
    </p>


    <hr class="my-4">
    <div v-if="isFinish" class="alert alert-primary">
      <h1>Oyun Bitti</h1>
      <p class="display-4">{{dks}} DKS </p>
      <span>Doğruluk Yüzdesi : %{{truePercent}} </span><br>
      <span>Doğru Kelime : {{trueCount}} </span><br>
      <span>Yanlış Kelime : {{falseCount}} </span>
    <button @click="newGame" class="d-flex btn btn-success btn-lg  mt-5 " >Yeni Oyun</button>
    </div>

    <div v-else>
      <div class="card ">
        <div class="card-body ">
          <span class="words" :class="key!=0 || hataliKelime "
                v-for="(word,key) in words.filter((data,index)=> index<20)" :key="key"> {{ word }}</span>
        </div>
      </div>
      <div class="card">
        <div class="card-body bg-secondary">
          <div class="input-group input-group-lg">
            <input type="text" class="form-control" v-model="word">
            <div class="input-group-append input-group-lg" id="button-addon4">
              <button class="btn btn-light" type="button" @click="timeProcess">{{ timer }} sn.</button>
              <button :disabled="isRunning" class="btn btn-light" type="button" @click="getWord">Yenile</button>
            </div>

          </div>
        </div>
      </div>
    </div>

  </div>

</template>

<script setup>
import {computed, onMounted, ref, watch} from "vue";
import wordList from '@/assets/words.json'

const words = ref([])
const word = ref(null)
const isTrue = ref(true)
const trueCount = ref(0)
const falseCount = ref(0)
const timer = ref(30)
const interval = ref(false)
const isRunning = ref(false)
const isFinish = ref(false)
const kelimeListe = ref(wordList)

onMounted(() => {
  getWord()
})

watch(word, (val) => {
  if (!val || val === ' ') {
    word.value = ''
    return
  }
  if (!isRunning.value) toggleTimer()
  const wordSlc = words.value[0].slice(0, val.length)
  const userWord = val.replace(' ', '').toLowerCase()
  isTrue.value = wordSlc === userWord

  if (val.indexOf(' ') !== -1) {
    isTrue.value ? trueCount.value++ : falseCount.value++
    words.value.shift()
    word.value = ''
    isTrue.value = true
  }

})
const getWord = (() => {
  words.value = kelimeListe.value.sort(() => Math.random() - 0.5).splice(0, 20)
})
const hataliKelime = computed(() => {
  return isTrue.value ? 'writing-word' : 'writing-word bg-danger'
})
const dks = computed(() => {
  return 300 - words.value.length
})
const truePercent = computed(() => {
  const percent = (100 / dks.value)
  const val = (percent * trueCount.value)
  return isNaN(val) ? 0 : val.toFixed(2)
})

const toggleTimer = (() => {
  isRunning.value = true
  interval.value = setInterval(timeProcess, 1000)
})
const timeProcess = (() => {
  if (timer.value === 1) {
    clearInterval(interval.value)
    isFinish.value = true
  }
  timer.value--
})

const newGame =(()=>{
  getWord()
  isFinish.value = false
  timer.value = 30
  isTrue.value = true
  isRunning.value = false
  word.value = ''
})

</script>

<style scoped>
.jumbotron {
  background-color: #e9ecef;
  padding-top: 80px;
  padding-bottom: 50px;
}

.words {
  margin-left: 8px;
  font-size: 25px;
  font-weight: 400;
}

.writing-word {
  background-color: slategrey;
  color: white;
  padding: 5px;
  border-radius: 5px;
}
</style>