<script setup>
import { ref } from 'vue';

const audio = ref(null)

const displayTime = ref("00:00:00")
const displayDuration = ref("00:00:00")
const duration = ref(100)
const time = ref(0)

const { file, onChangeTime } = defineProps({
    file: String,
    onChangeTime: Function
})

const load = (e) => {
    duration.value = e.currentTarget.duration
    displayDuration.value = formatTime(e.currentTarget.duration)
}

const changeTime = (e) => {
    onChangeTime(e.currentTarget.currentTime)
    time.value = e.currentTarget.currentTime
    displayTime.value = formatTime(e.currentTarget.currentTime)
}

const play = () => {
    if (audio.value.paused) {
        audio.value.play();
        return
    }
    audio.value.pause();
}

const changeTimeValue = (e) => {
    if (audio.value) {
        audio.value.currentTime = e.target.value
    }
}

const formatTime = (d) => {
    d = Number(d);
    var h = Math.floor(d / 3600);
    var m = Math.floor(d % 3600 / 60);
    var s = Math.floor(d % 3600 % 60);
    return [h.toString().padStart(2, "0"), m.toString().padStart(2, "0"), s.toString().padStart(2, "0")].join(':')
}

</script>

<template>
    <div class="my-4 sticky top-2">
        <audio class="audio" ref="audio" :src="file" @canplay="load($event)" @timeupdate="changeTime($event)" />

        <div class="audio">
            <div class="audio-control">
                <div class="audio-buttons">
                    <button @click="play">
                        <i class="bi bi-play"></i>
                    </button>
                </div>
                <div class="bar">
                    <div class="bar-bg"></div>
                    <div :style="{ width: (time * 100) / duration + '%' }" class="bar-current"></div>
                    <input class="bar" type="range" :value="time" @change="changeTimeValue" @input="changeTimeValue"
                        :max="duration" step="0.01">
                </div>
            </div>
            <div class="audio-text">
                <p>{{ displayTime }}</p>
                <p>{{ displayDuration }}</p>
            </div>
        </div>
    </div>
</template>

<style>
.audio {
    background-color: #262626;
    width: 100%;
    heigth: 200px;
    padding: 5px;
}

.audio .audio-control {
    display: flex;
    gap: 5px;
    align-items: center;
}

.audio .audio-control button {
    background-color: #353535;
    padding-inline: 5px;
    border-radius: 20px;
    transition: .5s;
}

.audio .audio-control .bar {
    width: 100%;
    position: relative;
}

.audio .audio-control .bar .bar-bg {
    background-color: #171717;
    width: 100%;
    height: 5px;
}

.audio .audio-control .bar .bar-current {
    background-color: #2464EA;
    height: 5px;
    position: absolute;
    top: 0;
}

.audio .audio-control button:hover {
    background-color: #171717;
}

.audio {
    width: 100%;
    height: 100%;
}

.audio .audio-control .bar input {
    -webkit-appearance: none;
    width: 100%;
    height: 100%;
    background: transparent;
    outline: none;
    -webkit-transition: .2s;
    position: absolute;
    top: 0;
    transition: opacity .2s;
}

.audio .audio-control .bar input:hover {
    opacity: 1;
}

/* Thumb: webkit */
.audio .audio-control .bar input[type="range"]::-webkit-slider-thumb {
    /* removing default appearance */
    -webkit-appearance: none;
    appearance: none;
    /* creating a custom design */
    height: 10px;
    width: 10px;
    background-color: #2464EA;
    border-radius: 50%;
    border: 2px solid #262626;
}

/* Thumb: Firefox */
.audio .audio-control .bar input[type="range"]::-moz-range-thumb {
    height: 15px;
    width: 10px;
    background-color: #2464EA;
    border-radius: 5px;
    border: 1px solid #262626;
}

.audio .audio-text {
    display: flex;
    justify-content: space-between;
    align-items: center;
}
</style>