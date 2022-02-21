<template>
<!-- <h1>{{isShown}}</h1> -->
    <div class="camera-container">
        <video autoplay ref="video" :class="[activeCamera]"></video>
    </div>

    <canvas ref="canvas" id="canvas"></canvas>
</template>

<script lang="ts">

import {
    defineComponent, onMounted,
    onUnmounted, PropType, ref,
}
    from 'vue';

import { Resolution } from './resolution';

export default defineComponent({
    name: 'PhotoCamera',
    emits: [
        'snapshot',
    ],

    props: {
        resolution: {
            type: Object as PropType<Resolution>,
            default: () => { return { width: screen.width, height: screen.height * 0.8 }; },
        },
        autoplay: {
            type: Boolean,
            default: false,
        },
        constraints: {
            type: Object,
            required: false,
        },

        isShown: {
            type: Boolean,
        },
    },

    setup (props, { emit }) {
        onMounted(() => {
            if (!navigator.mediaDevices) throw new Error('Media devices not available');
            if (props.autoplay) start();
        });

        onUnmounted(() => stop());
        const video = ref<HTMLVideoElement>();
        const canvas = ref<HTMLCanvasElement>();
        const stream = ref<MediaStream>();
        const activeCamera = ref('video');
        const noCamera = ref('camera-off');
        const constraints = props.constraints || {
            video: {
                width: props.resolution.width,
                height: props.resolution.height,
            },
            audio: false,
        };

        const start = async (): Promise<void> => {
            stream.value = await navigator.mediaDevices.getUserMedia(
                constraints,
            );
            if (!video.value) throw new Error('Video ref is null');
            video.value.srcObject = stream.value;
        };
        const snapshot = (
            resolution: Resolution = props.resolution,
            type = 'image/png',
        ): Promise<Blob | any> => {
            if (!video.value) throw new Error('Video ref is null');
            if (!canvas.value) throw new Error('Canvas ref is null');

            const { width, height } = resolution;

            canvas.value.width = width;
            canvas.value.height = height;
            canvas.value.getContext('2d')?.drawImage(video.value, 0, 0, width, height);

            return new Promise((resolve) => {
                canvas.value?.toBlob(
                    (blob: Blob | any) => {
                        emit('snapshot', blob);
                        resolve(blob);
                    },
                    type,
                );
            });
        };

        return {
            start,
            video,
            snapshot,
            canvas,
            stream,
            activeCamera,
            noCamera,
            // isShown
        };
    },
});
</script>

<style scoped>
.camera-container {
    position: relative;
    width: 100%;
    height: 100%;
}

.camera-off{
    display: none;
}

.video {
    /* display: none; */
    width: 100%;
    height: 100%;
    position: relative;
}
#canvas {
    display: none;
}
</style>
