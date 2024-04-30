<script setup>
import { computed, ref } from 'vue'
import ViewText from './ViewText.vue';
import MediaComponent from './MediaComponent.vue';

import test1 from './audios/test1.mp3'
import resultado1 from "./transcripciones/test1.json"
import test2 from './audios/test2.mp3'
import resultado2 from "./transcripciones/test2.json"
// import test3 from './audios/test3.mp3'
// import resultado3 from "./transcripciones/test3.json"

const allAudios = [test1, test2]
const resultados = [resultado1, resultado2]

const info = ref("files");
const listTranscribe = ref([])
const palabras = ref([])

const transcribe = ref("")

const isLoading = ref(false)

const view = ref("words")

const file = ref("");
const time = ref(0)
const ejemplos = ref([
  {
    name:"Test 1"
  },
  {
    name:"Test 2"
  }
])

// const submitTranscribe = () => {
//   isLoading.value = true
//   if (keyValue.value) {
//     axios.get('/transcribe/result?key=' + keyValue.value).then(async res => {
//       const result = await axios.get(res.data.url).then(({ data }) => {
//         transcribe.value = data.results.transcripts.map((e) => e.transcript).join('')
//         palabras.value = data.results.items.filter(e => e.type === "pronunciation").map((e, index) => {
//           const palabra = {
//             start: e.start_time,
//             end: e.end_time,
//             text: e.alternatives.map(al => al.content).join(' ')
//           }
//           return palabra
//         })
//         return data.results.items
//       }).catch(console.log)
//       var lastInfo = ""
//       var palabrasTemp = []
//       var palabrasTemp2 = []
//       var starTime = 0
//       var endTime = 0
//       const final = []
//       result.forEach((re) => {
//         if (!lastInfo) {
//           lastInfo = re.speaker_label
//           palabrasTemp.push(re.alternatives.map((e) => e.content).join(" "))
//           palabrasTemp2.push({
//             start: re.start_time,
//             end: re.end_time,
//             text: re.alternatives.map(al => al.content).join(' ')
//           })
//           if (re.start_time) {
//             starTime = re.start_time
//           }
//           else {
//             starTime = endTime
//           }

//           if (re.end_time) {
//             endTime = re.end_time
//           }
//         } else {
//           if (lastInfo === re.speaker_label) {
//             palabrasTemp.push(re.alternatives.map((e) => e.content).join(" "))
//             palabrasTemp2.push({
//               start: re.start_time ?? endTime,
//               end: re.end_time ?? endTime,
//               text: re.alternatives.map(al => al.content).join(' ')
//             })
//             if (re.end_time) {
//               endTime = re.end_time
//             }
//           }
//           else {
//             final.push({
//               speker: lastInfo,
//               words: palabrasTemp.join(" "),
//               current: false,
//               starTime: starTime,
//               endTime: re.end_time ?? endTime,
//               text: palabrasTemp2
//             })
//             lastInfo = re.speaker_label
//             palabrasTemp = []
//             palabrasTemp2 = []
//             if (re.start_time) {
//               starTime = re.start_time
//             }
//             else {
//               starTime = endTime
//             }

//             if (re.end_time) {
//               endTime = re.end_time
//             }
//           }
//         }
//       });
//       final.push({
//         speker: lastInfo,
//         words: palabrasTemp.join(" "),
//         current: false,
//         starTime: starTime,
//         endTime: endTime,
//         text: palabrasTemp2
//       })
//       console.log(final)
//       listTranscribe.value = final
//       isLoading.value = false
//     })
//   }
// }

const handleJson = (event) => {
  isLoading.value = true
  let reader = new FileReader();
  reader.onload = e => {
    console.log(e.target.result);
    let data = JSON.parse(e.target.result);
    transcribe.value = data.results.transcripts.map((e) => e.transcript).join('')
    palabras.value = data.results.items.filter(e => e.type === "pronunciation").map((e, index) => {
      const palabra = {
        start: e.start_time,
        end: e.end_time,
        text: e.alternatives.map(al => al.content).join(' ')
      }
      return palabra
    })
    var result = data.results.items
    var lastInfo = ""
    var palabrasTemp = []
    var palabrasTemp2 = []
    var starTime = 0
    var endTime = 0
    const final = []
    result.forEach((re) => {
      if (!lastInfo) {
        lastInfo = re.speaker_label
        palabrasTemp.push(re.alternatives.map((e) => e.content).join(" "))
        palabrasTemp2.push({
          start: re.start_time,
          end: re.end_time,
          text: re.alternatives.map(al => al.content).join(' ')
        })
        if (re.start_time) {
          starTime = re.start_time
        }
        else {
          starTime = endTime
        }

        if (re.end_time) {
          endTime = re.end_time
        }
      } else {
        if (lastInfo === re.speaker_label) {
          palabrasTemp.push(re.alternatives.map((e) => e.content).join(" "))
          palabrasTemp2.push({
            start: re.start_time ?? endTime,
            end: re.end_time ?? endTime,
            text: re.alternatives.map(al => al.content).join(' ')
          })
          if (re.end_time) {
            endTime = re.end_time
          }
        }
        else {
          final.push({
            speker: lastInfo,
            words: palabrasTemp.join(" "),
            current: false,
            starTime: starTime,
            endTime: re.end_time ?? endTime,
            text: palabrasTemp2
          })
          lastInfo = re.speaker_label
          palabrasTemp = []
          palabrasTemp2 = []
          if (re.start_time) {
            starTime = re.start_time
          }
          else {
            starTime = endTime
          }

          if (re.end_time) {
            endTime = re.end_time
          }
        }
      }
    });
    final.push({
      speker: lastInfo,
      words: palabrasTemp.join(" "),
      current: false,
      starTime: starTime,
      endTime: endTime,
      text: palabrasTemp2
    })
    listTranscribe.value = final
    isLoading.value = false
  };
  reader.readAsText(event.target.files[0]);
}

const handleJsonSimple = (event) => {
  let data = event;
  transcribe.value = data.results.transcripts.map((e) => e.transcript).join('')
  palabras.value = data.results.items.filter(e => e.type === "pronunciation").map((e, index) => {
    const palabra = {
      start: e.start_time,
      end: e.end_time,
      text: e.alternatives.map(al => al.content).join(' ')
    }
    return palabra
  })
  var result = data.results.items
  var lastInfo = ""
  var palabrasTemp = []
  var palabrasTemp2 = []
  var starTime = 0
  var endTime = 0
  const final = []
  result.forEach((re) => {
    if (!lastInfo) {
      lastInfo = re.speaker_label
      palabrasTemp.push(re.alternatives.map((e) => e.content).join(" "))
      palabrasTemp2.push({
        start: re.start_time,
        end: re.end_time,
        text: re.alternatives.map(al => al.content).join(' ')
      })
      if (re.start_time) {
        starTime = re.start_time
      }
      else {
        starTime = endTime
      }

      if (re.end_time) {
        endTime = re.end_time
      }
    } else {
      if (lastInfo === re.speaker_label) {
        palabrasTemp.push(re.alternatives.map((e) => e.content).join(" "))
        palabrasTemp2.push({
          start: re.start_time ?? endTime,
          end: re.end_time ?? endTime,
          text: re.alternatives.map(al => al.content).join(' ')
        })
        if (re.end_time) {
          endTime = re.end_time
        }
      }
      else {
        final.push({
          speker: lastInfo,
          words: palabrasTemp.join(" "),
          current: false,
          starTime: starTime,
          endTime: re.end_time ?? endTime,
          text: palabrasTemp2
        })
        lastInfo = re.speaker_label
        palabrasTemp = []
        palabrasTemp2 = []
        if (re.start_time) {
          starTime = re.start_time
        }
        else {
          starTime = endTime
        }

        if (re.end_time) {
          endTime = re.end_time
        }
      }
    }
  });
  final.push({
    speker: lastInfo,
    words: palabrasTemp.join(" "),
    current: false,
    starTime: starTime,
    endTime: endTime,
    text: palabrasTemp2
  })
  listTranscribe.value = final
  isLoading.value = false
}

const changeView = (event) => {
  view.value = event.target.value
}

const changeTime = (e) => {
  time.value = e
}

const handleFileUpload = (event) => {
  file.value = URL.createObjectURL(event.target.files[0])
}

const selectTest = (event) => {
  handleJsonSimple(resultados[event]);
  file.value = allAudios[event];
}

</script>

<template>
  <div class="my-5">

    <div class="my-5">
      <select v-model="info"
        class="block w-full bg-neutral-800 rounded-md border-0 py-1.5 pl-4 pr-20 text-white ring-1 ring-inset ring-transparent placeholder:text-gray-400 focus:ring-2 focus:ring-inset focus:ring-indigo-600 sm:text-sm sm:leading-6 outline-none transition">
        <option value="default">Ejemplos</option>
        <option value="files">Archivos</option>
      </select>
    </div>

    <div v-if="info === 'files'" class="my-3 w-full flex gap-x-2">
      <div class="w-full">
        <label for="archivoAudio" class="p-1 my-1">Archivo de audio</label>
        <input id="archivoAudio" type="file"
          class="block w-full bg-neutral-800 rounded-md border-0 py-1.5 pl-4 pr-20 text-white ring-1 ring-inset ring-transparent placeholder:text-gray-400 focus:ring-2 focus:ring-inset focus:ring-indigo-600 sm:text-sm sm:leading-6 outline-none transition"
          accept="audio/*" @change="handleFileUpload($event)" />
      </div>
      <div class="w-full">
        <label for="yeison" class="p-1 my-1">Archivo de transcripción</label>
        <input id="yeison" type="file"
          class="block w-full bg-neutral-800 rounded-md border-0 py-1.5 pl-4 pr-20 text-white ring-1 ring-inset ring-transparent placeholder:text-gray-400 focus:ring-2 focus:ring-inset focus:ring-indigo-600 sm:text-sm sm:leading-6 outline-none transition"
          accept="application/JSON" @change="handleJson($event)" />
      </div>
    </div>

    <div v-if="info === 'default'">
      <ul class="max-w-60 mx-auto flex gap-2">
        <li v-for="(exe, index) in ejemplos" @click="selectTest(index)"
          class="hover:bg-neutral-700 text-center bg-neutral-800 w-full my-2 rounded-md cursor-pointer">
          {{ exe.name }}
        </li>
      </ul>
    </div>

  </div>

  <div class="my-5">
    <select @change="changeView"
      class="block bg-neutral-800 rounded-md border-0 py-1.5 pl-4 pr-20 text-white ring-1 ring-inset ring-transparent placeholder:text-gray-400 focus:ring-2 focus:ring-inset focus:ring-indigo-600 sm:text-sm sm:leading-6 outline-none transition">
      <option value="words">Texto</option>
      <option value="dialog">Cuadros de texto</option>
    </select>
  </div>

  <MediaComponent :file="file" :on-change-time="changeTime" />

  <ViewText :words="palabras" :dialog="listTranscribe" :view="view" :time="time" />
</template>

<style scoped></style>
