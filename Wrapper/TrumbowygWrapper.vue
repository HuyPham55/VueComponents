<script setup>
import {onMounted, defineProps} from "vue"

const props = defineProps(['modelValue', 'id'])

const emits = defineEmits(["update:modelValue"])

let onUserInput = function (event) {
    emits('update:modelValue', event.target.value)
}

function trumbowygTextInitializer() {
    // noinspection JSUnresolvedReference
    jQuery(`#${props.id}.trumbowyg`).trumbowyg({
        btns: [
            ['strong', 'em', 'underline'],
            ['foreColor', 'backColor'],
            ['fontsize'],
            ['removeformat'],
            ['link'],
            ['unorderedList', 'orderedList'],
            ['justifyLeft', 'justifyCenter', 'justifyRight', 'justifyFull'],
        ],
        tagsToRemove: ['script', 'link'],
        resetCss: true,
        autogrow: true
    })
        .on('tbwchange ', function (e) {
            onUserInput(e)
        });
}

onMounted(trumbowygTextInitializer);
</script>

<template>
    <textarea class="trumbowyg" :id="id">{{modelValue}}</textarea>
</template>

<style scoped>

</style>
