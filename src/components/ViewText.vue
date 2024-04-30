<script setup>
import { ref, watch } from 'vue'

const props = defineProps({
    words: {
        type: Object,
        default: []
    },
    dialog: {
        type: Object,
        required: false,
        default: []
    },
    view: {
        type: String,
        required: false,
        default: 'words'
    },
    time: {
        type: Number,
        required: false,
        default: 0
    }
})

const pos = ref('dialog--1');

watch(() => props.time, () => {
    // console.log("Timpo: " + props.time)
    const currInde = props.dialog.findIndex(item => props.time > item.starTime && props.time < item.endTime)
    if (currInde !== -1) {
        if (pos.value !== `dialog-${currInde}`) {
            pos.value = `${props.view}-${currInde}`
        }
    }
})

watch(pos, () => {
    const currentPos = window.document.getElementById(pos.value)
    if (currentPos) {
        window.scrollTo({ top: currentPos.offsetTop - 100, behavior: 'smooth' })
    }
})

</script>

<template>

    <div v-if="props.view === 'words'" class="my-5">
        <h3 class="text-3xl mt-2">Transcripción</h3>
        <div role="textbox" class="dialog">
            <span v-for="(palabra, index) in props.words" :id="'words-'+index" :key="index"
                :class="{ 'word': time > palabra.start && time < palabra.end }">{{ palabra.text + " " }}</span>
        </div>
    </div>

    <div v-if="props.view === 'dialog'">
        <p>Hablantes</p>
        <ul class="dialog text-justify">
            <li v-for="(item, index) in props.dialog" :id="'dialog-' + index" :key="index"
                class="p-4 my-3 rounded-md transition"
                :class="{ 'bg-active': props.time > item.starTime && props.time < item.endTime }">
                <div class="border-b border-neutral-600 mb-2 pb-2 flex items-center">
                    <p class="text-xl w-full">{{ item.speker.replace('spk', "Persona") }}</p>
                    <div class="text-sm text-neutral-800 text-center py-1 px-2 rounded-md bg-neutral-500">
                        {{ item.starTime }}s
                    </div>
                </div>
                <p>
                    <!-- {{ item.words }} -->
                    <span v-for="(palabra, index) in item.text" :id="palabra.text" :key="index"
                        :class="{ 'word': props.time > palabra.start && props.time < palabra.end }">{{ palabra.text + " "
                        }}</span>
                </p>
            </li>
        </ul>
    </div>

</template>

<style>
.dialog .bg-active {
    background-color: #2464EA;
}

.dialog .word {
    background-color: #DD4B39;
}
</style>