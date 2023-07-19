<script setup>
import {reactive} from "vue";
import SyncMultiLangBootstrapItemListManager from "@/components/components/SyncMultiLangBootstrapItemListManager.vue";

const langs = {
    en: "English",
    zh: '繁中',
}
const columns = [
    {
        title: 'Description',
        col: 10,
    }
]

const items = reactive({
    data: {
        en: [],
        zh: [],
    },
    loading: false,
})

function randomGenerator() { //id generator (optional)
    return Date.now();
}

const addNewItem = function () {
    let data = {
        title: ""
    }
    addItem(null, data);
}

function addItem(id, data) {
    id = id ? id : randomGenerator();
    for (let lang in (langs)) {
        // console.log(items.data[lang])
        items.data[lang].push({
            id,
            data: JSON.parse(JSON.stringify(data)),
        })
    }
}

const props = defineProps(['id']);

function fetchData() {
    let id = props.id | 0;
    if (id) {
        axios
            .post('./fetch', {
                id,
            })
            .then(response => {
                items.data = response.data
            })
            .catch(exception => {

            })
    }
}
fetchData();
</script>

<template>
    <SyncMultiLangBootstrapItemListManager
        :langs="langs"
        :items="items.data"
        :columns="columns"
        :hasData="() => false"
        @addItem="addNewItem">
        <template #main="{item, langKey}">
            <div :class="`input-group col-${columns[0].col}`">
                <input class="form-control" type="text"
                       v-model="item.data.title"
                       :name="`data[${langKey}][][data][title]`"
                       :placeholder="langs[langKey]"/>
            </div>
        </template>
    </SyncMultiLangBootstrapItemListManager>
</template>

<style scoped>

</style>

<!--
For use with PHP
foreach ($this->lang as $key => $title) {
  $data[$key] = json_decode($page->getTranslation('meta', $key, false));
}
return response()->json($data);
-->
