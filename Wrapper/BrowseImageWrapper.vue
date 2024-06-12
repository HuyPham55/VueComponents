<script setup>

import BrowseFileWrapper from "./BrowseFileWrapper.vue";
import {reactive, watch, computed} from "vue";

const props = defineProps({
    "modelValue": {
        type: String,
    },
    note: null,
    name: null,
    id: null,
})
const emits = defineEmits(["update:modelValue"])


const computedExtension = computed(() => {
    return props.modelValue.length
        ? String(props.modelValue).match(new RegExp('[^.]+$')).pop()
        : ""
})

const updateModelValue = function (newValue) {
    emits("update:modelValue", newValue)
}

const isImage = computed(() => ['jpg', 'jpeg', 'png', 'webp'].indexOf(computedExtension.value) > -1)


</script>

<template>
    <div class="row">
        <div class="col-4 text-center"
             v-show="!!modelValue && isImage">
            <a :href="modelValue" target="_blank">
                <img class="img-fluid img-preview"
                     :src="modelValue"
                     :alt="modelValue"/>
            </a>
        </div>
        <div class="col">
            <BrowseFileWrapper
                :modelValue="modelValue"
                @update:modelValue="updateModelValue"
                :id="id"
                :name="name"
                :note="note">
                <slot/>
            </BrowseFileWrapper>
        </div>
    </div>
</template>

<style scoped>
.img-preview {
    max-height: 5rem;
}

</style>
