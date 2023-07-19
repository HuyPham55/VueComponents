<script setup>
import MultiLangBootstrapItemManager from "@/components/components/MultiLangBootstrapItemManager.vue";
import {nextTick, provide, reactive} from "vue";

const langs = {
    en: "English",
    zh: '繁中',
}
const columns = [
    {
        title: 'Title',
        col: 3,
    },
    {
        title: 'Description',
        col: 4,
    },
    {
        title: 'Icon',
        col: 3,
    }
];

const items = reactive({
    data: [],
    loading: false,
})

function randomGenerator() { //id generator (optional)
    return Date.now();
}

function initializeFileManager() {
    $('.lfm-image').filemanager('image');
}

const addItem = function () {
    let data = {
        title: {},
        description: {},
        icon: {},
    }
    for (let lang in (langs)) {
        data.title[lang] = ''
        data.description[lang] = ''
        data.icon[lang] = ''
    }
    items.data.push({
        id: randomGenerator(),
        data,
    })
    nextTick(initializeFileManager)
}

//Responsive file manager
function listenToFileManager() {
    window.addEventListener('message', (event) => {
        let data = event.data;
        if (data.hasOwnProperty('type') && data.type === 'lfm') {
            let path = data.path;
            let key = data.key;
            key = key.split("-");
            try {
                let id = parseInt(key[0]);
                let lang = key[key.length - 1]
                let item = items.data.find(item => item["id"] === id)
                let index = items.data.indexOf(item);

                items.data[index]['data']['icon'][lang] = path
            } catch (e) {
                console.log(e);
            }
        }
    });
}
listenToFileManager();

function fetchItem() {

}
fetchItem()
</script>

<template>
    <MultiLangBootstrapItemManager
        :langs="langs"
        :items="items.data"
        :columns="columns"
        :hasData="() => false"
        @addItem="addItem">
        <template #main="{item, langKey}">
            <div :class="`input-group col-${columns[0].col}`">
                <input class="form-control" type="text"
                       v-model="item.data.title[langKey]"
                       :name="`${langKey}[data][${item.id}][title]`"
                       :placeholder="langs[langKey]">
            </div>
            <div :class="`input-group col-${columns[1].col}`">
                <input class="form-control" type="text"
                       v-model="item.data.description[langKey]"
                       :name="`${langKey}[data][${item.id}][description]`"
                       :placeholder="langs[langKey]">
            </div>
            <div :class="`input-group d-flex align-items-center col-${columns[2].col}`">
                <input type="text"
                       :name="`${langKey}[data][${item.id}][icon]`"
                       v-model="item.data.icon[langKey]"
                       autocomplete="off"
                       class="form-control">
                <div class="input-group-append">
                    <button :data-input="`${item.id}-${langKey}`"
                            :data-preview="`image_preview-${item.id}`"
                            class="btn btn-primary lfm-image"
                            type="button">
                        <i class="fas fa-folder-open"></i>
                    </button>
                </div>
                <img class="img-fluid mt-4" :src="item.data.icon[langKey]"/>
            </div>
        </template>
    </MultiLangBootstrapItemManager>
</template>

<style scoped>

</style>
