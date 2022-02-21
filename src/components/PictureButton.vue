<template>
    <div :class="[isShown ? 'button-off': 'active-button']">
        <button @click="$emit('btn-click')">Take a picture</button>
    </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue';

export default defineComponent({
    name: 'PictureButton',
    components: {
    },

    props: {
        isShown: {
            type: Boolean,
        },
    },
});
</script>

<style scoped>
.button-off{
    display: none;
}
</style>
