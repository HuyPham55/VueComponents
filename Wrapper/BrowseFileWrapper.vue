<script setup>
import {onMounted} from "vue";

const props = defineProps(["modelValue", "id", "name", 'note'])

const emits = defineEmits(["update:modelValue"])

function initializeFileManager() {
    // noinspection JSUnresolvedReference
    $('.lfm-file').filemanager('file');
}

onMounted(initializeFileManager)

let clearInput = function () {
    emits('update:modelValue', '')
}

let onUserInput = function(event) {
    emits('update:modelValue', event.target.value)
}
</script>

<template>
    <div class="input-group">
        <input type="text"
               :name="name"
               :value="modelValue"
               autocomplete="off"
               @input="onUserInput"
               class="form-control">
        <div class="input-group-append">
            <button :data-input="id"
                    class="btn btn-primary lfm-file"
                    type="button">
                <i class="fas fa-folder-open mr-2"></i>
                <slot>
                    Select
                </slot>
            </button>
            <button class="btn btn-danger" type="button" @click="clearInput">
                <i class="fas fa-eraser"></i>
            </button>
        </div>
        <p class="text-muted" v-if="note">
            {{ note }}
        </p>
    </div>
</template>

<style scoped>

</style>
