<template>
    <div class='app'>
        <div class='main'>
            <div id='camera-container'>
                <PhotoCamera ref='camera' autoplay></PhotoCamera>
                <PictureButton @btn-click='snapshot' :isShown="isShown"/>
                <div class='single-image'>
                <!-- <img v-if="index >= 0" :src="items[index]" > -->
                </div>
            </div>
        </div>
        <div class='bottom'>
            <ColorList @on-image-click="onImageClick" :currentSnapshot="currentSnapshot"/>
        </div>
    </div>
</template>

<script lang='ts'>

import { defineComponent, ref } from 'vue';

import PhotoCamera from '@/components/PhotoCamera.vue';
import ColorList from '@/components/ColorList.vue';
import PictureButton from '@/components/PictureButton.vue';

export default defineComponent({
    name: 'App',
    components: {
        PhotoCamera,
        ColorList,
        PictureButton,
    },

    setup () {
        const camera = ref<InstanceType<typeof PhotoCamera>>();
        const items: string[] = [];
        const photos = {};
        const isShown = ref<boolean>(false);
        const index = ref<number>(-1);
        const snapshot = async () => {
            console.log(camera.value);
            const blob = await camera.value?.snapshot({
                width: screen.width,
                height: screen.height * 0.8,
            });

            currentSnapshot.value = URL.createObjectURL(blob);
            items.unshift(currentSnapshot.value);
            console.log(typeof blob);
        };

        const currentSnapshot = ref();

        return {
            camera,
            snapshot,
            currentSnapshot,
            items,
            photos,
            index,
            isShown,
        };
    },

    methods: {
        onImageClick (index) {
            this.index = index;
            this.isShown = !this.isShown;
            console.log(this.isShown);
        },
    },

    provide () {
        return { Shown: this.isShown };
    },
});
</script>

<style scoped>
*{
  box-sizing: border-box;
}
.app{
  border: 2px solid red;
  /* background-color: blue; */
  max-width: 100%;
  height: 789px;
}

.main{
  max-width:100%;
  height: 80%;
  display: flex;
  align-items: center;
  justify-content: center;
  /* box-sizing: border-box; */
}

.bottom{
  max-width:100%;
  height: 20%;
  padding: 10px
}

@media only screen and (max-width: 912px){
  .app{
    border: 2px solid red;
    max-width: 100%;
    height: 1368px;
  }
}
</style>
