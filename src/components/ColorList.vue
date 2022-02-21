<template>
    <footer>
        <Carousel :settings='settings'
                  :breakpoints='breakpoints'
                  v-if="currentSnapshot">
            <Slide v-for="(image, index) in images"
                  :key="image">
                <div class="carousel__item">
                  <img :src='image' width='230' alt="" @click="onImageClick(index)" @keypress="image"/>
                </div>
            </Slide>
            <template #addons>
              <Navigation />
            </template>
        </Carousel>
    </footer>
</template>

<script lang="ts">
import { defineComponent } from 'vue';
import { Carousel, Navigation, Slide } from 'vue3-carousel';

import 'vue3-carousel/dist/carousel.css';

export default defineComponent({
    components:
    {
        Carousel,
        Slide,
        Navigation,
    },
    name: 'ColotList',
    props: [
        'currentSnapshot',
    ],

    data: () => ({
    // carousel settings
        settings:
        {
            itemsToShow: 1,
            snapAlign: 'center',
        },
        // breakpoints are mobile first
        // any settings not specified will fallback to the carousel settings
        breakpoints:
        {
            // 700px and up
            700:
            {
                itemsToShow: 3,
                snapAlign: 'center',
            },
            // 1024 and up
            1024:
            {
                itemsToShow: 5,
                snapAlign: 'start',
            },
        },

        images: [] as string[],
        fullWidthImageIndex: null,
    }),

    watch:
    {
        currentSnapshot () {
            this.images.unshift(this.currentSnapshot);
            // console.log(this.images)
        },
    },

    emits: ['on-image-click'],

    methods: {
        onImageClick (index) {
            this.$emit('on-image-click', index);
            // emitter.emit("img-index", index);
            // eslint-disable-next-line @typescript-eslint/ban-ts-comment
            // @ts-ignore
            // URLSearchParams.set("img-index", index)
        },
    },
});
</script>

<style scoped>
/* .carousel__prev--in-active,
.carousel__next--in-active {
  display: none;
} */

/* .fullWidthImage {
  width: 500px !important;
  height: 300px !important;

  display: flex;
  position: relative;
} */

img{
  width: 220px;
  height:140px;
  display: flex
}
</style>
