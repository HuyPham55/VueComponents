<script setup>
import {onMounted, defineProps} from "vue"

const props = defineProps(['value'])

function initializer() {
    // noinspection JSUnresolvedReference
    jQuery('textarea.editor').ckeditor(options);
    let submitButton = document.querySelector("form button[type='submit']")
    if (submitButton) {
        submitButton.addEventListener("click", function () {
            document.body.onbeforeunload = () => {
            }
        })
    }
}

onMounted(initializer);
</script>

<template>
    <textarea class="form-control editor">{{value}}</textarea>
</template>

<style scoped>

</style>
