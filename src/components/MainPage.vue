<template>
  <div class="container jumbotron px-5">
    <h1 class="display-4">Hızlı Yazma Yarışması</h1>
    <p class="lead">Ne kadar hızlı klavye kullandığını test et
    </p>
    <div class="">
      Doğru Sayısı: {{ trueCount }}
      Yanlış Sayısı: {{ falseCount }}
    </div>
    <hr class="my-4">
    <div class="card ">
      <div class="card-body ">
        <span class="words" :class="key!=0 || hataliKelime " v-for="(word,key) in words" :key="key"> {{ word }}</span>
      </div>
    </div>
    <div class="card">
      <div class="card-body bg-secondary">
        <div class="input-group input-group-lg">
          <input type="text" class="form-control" v-model="word">
          <div class="input-group-append input-group-lg" id="button-addon4">
            <button class="btn btn-light" type="button" @click="timeProcess">{{ timer }} sn.</button>
            <button class="btn btn-light" type="button">Yenile</button>
          </div>

        </div>
      </div>
    </div>
  </div>

</template>

<script setup>
import {computed, ref, watch} from "vue";

const words = ref([
  'Elma', 'Kiraz', 'Portakal'
])
const word = ref(null)
const isTrue = ref(true)
const trueCount = ref(0)
const falseCount = ref(0)
const timer = ref(5)
const interval = ref(false)
const isRunning = ref(false)

watch(word, (val) => {
  if(!isRunning.value) toggleTimer()

  const wordSlc = words.value[0].slice(0, val.length)
  const userWord = val.replace(' ', '')
  isTrue.value = wordSlc === userWord

  if (val.indexOf(' ') !== -1) {
    isTrue.value ? trueCount.value++ : falseCount.value++
    words.value.shift()
    word.value = ''
    console.log('Doğru :', trueCount.value, 'Yanlış :', falseCount.value)
  }

})
const hataliKelime = computed(() => {
  return isTrue.value ? 'writing-word' : 'writing-word bg-danger'
})
const toggleTimer = (() => {
  isRunning.value = true
  interval.value = setInterval(timeProcess, 1000)
})
const timeProcess = (() => {
  if(timer.value===1){
    clearInterval(interval.value)
  }
  timer.value--
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