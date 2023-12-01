<script setup>
import { ref, onMounted } from 'vue'

const canvas = ref(null)
const video = ref(null)
const ctx = ref(null)

const constraints = ref({
    audio: false,
    video: true,
});

onMounted(async () => {
    if(video.value && canvas.value){
        ctx.value = canvas.value.getContext("2d");

        await navigator.mediaDevices
            .getUserMedia(constraints.value)
            .then(SetStream)
            .catch(e =>{
                console.error(e)
            })
    }
})

const SetStream = (stream) =>{
    video.value.srcObject = stream;
    video.value.play();

    requestAnimationFrame(Draw);
}

const Draw = ()=>{
    ctx.value.drawImage(video.value, 0, 0, canvas.value.width, canvas.value.height)
    
    requestAnimationFrame(Draw)
}

const TakePicture = () =>{
    let link = document.createElement('a')
    link.download = `vue-camera-${new Date().toISOString()}.png`
    link.href = canvas.value.toDataURL();
    link.click();
}
</script>

<template>
    <div>
        <video ref="video" autoplay playsinline webkit-playsinline muted hidden></video>

        <canvas ref="canvas" width="1280" height="620" class="bg-black rounded-3x1"></canvas>
   
        <div class="flex items-center justify-center py-4">
            <button 
                @click="TakePicture"
                class="
                    px-6 py-4 bg-green-500 rounded
                    text-white text-2xl uppercase font-bold
                    hover:bg-green-600
                ">
                Сфотографуватися
            </button>
        </div>
    </div>
</template>

