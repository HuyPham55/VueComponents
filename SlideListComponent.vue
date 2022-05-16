<!--Laravel multiple languages Vue component-->
<!--User guide below-->
<template>
    <multi-lang-bootstrap-item-manager :has-data="hasData" :columns="columns" :items="items"
                                       @addItem="addItem()" :langs="langs">
        <template v-slot:main="main">
            <div :class="`input-group col-${columns[0].col}`">
                <div class="mb-2">
                    <input class="form-control" type="text" v-model="main.item.data.title[main.langKey]"
                           :name="`${main.langKey}[content][${main.item.id}][title]`"
                           :placeholder="langs[main.langKey]">
                </div>
                <div>
                    <input class="form-control" type="text" v-model="main.item.data.subtitle[main.langKey]"
                           :name="`${main.langKey}[content][${main.item.id}][subtitle]`"
                           :placeholder="langs[main.langKey]">
                </div>
            </div>
            <div :class="`input-group d-flex align-items-center col-${columns[1].col}`">
                <textarea class="form-control"
                          :name="`${main.langKey}[content][${main.item.id}][content]`"
                          v-model="main.item.data.content[main.langKey]"
                ></textarea>
            </div>
            <div :class="`input-group d-flex align-items-center col-${columns[2].col}`">
                <input type="text"
                       :id="`${main.item.id}-${main.langKey}`"
                       :name="`${main.langKey}[content][${main.item.id}][image]`"
                       v-model="main.item.data.image[main.langKey]"
                       autocomplete="off"
                       placeholder=""
                       class="form-control">
                <div class="input-group-append ">
                    <button :data-input="`${main.item.id}-${main.langKey}`"
                            :data-preview="`image_preview-${main.item.id}`"
                            class="btn btn-primary lfm-image"
                            type="button">
                        <i class="fas fa-folder-open"></i>
                    </button>
                </div>
                <img :id="`image_preview-${main.item.id}`" class="img-fluid mt-4" :src="main.item.data.image[main.langKey]"/>

            </div>
        </template>
    </multi-lang-bootstrap-item-manager>
</template>

<script>
import axios from 'axios';
import MultiLangBootstrapItemManager from "./Components/MultiLangBootstrapItemManager";
export default {
    name: "SlideListComponent",
    components: {
        MultiLangBootstrapItemManager,
    },
    props: {
        langs: {
            type: Object,
        },
    },
    provide: {
        removeColumn: {
            title: 'Delete',
            hoverTitle: 'Confirm delete?',
            col: 2, //on scale of 12
        }
    },
    data() {
        return {
            items: Array(),
            columns: [
                {
                    title   : 'Title',
                    col: 3,
                },
                {
                    title   : 'Content',
                    col: 4,
                },
                {
                    title   : 'Image',
                    col: 3,
                },
                //total col should be equals to 12
            ],
            langs: JSON.parse(this.langs)
        }
    },
    methods: {
        fetchData: async function () {
            await axios.post(fetchRoute, {})
                .then(response => {
                    response = response.data;
                    Object.keys(response).forEach((langKey, index) => {
                        let itemCount = 0;
                        for (let id in JSON.parse(response[langKey])) {
                            let item = JSON.parse(response[langKey])[id];
                            if (typeof (this.items[itemCount]) != 'undefined') {
                                //if initiated
                                for (let property in item) {
                                    this.items[itemCount]['data'][property][langKey] = item[property];
                                }
                            } else {
                                //if not initiated
                                let temp = {
                                    id: itemCount,
                                    data: {
                                        title: {},
                                        subtitle: {},
                                        content: {},
                                        image: {},
                                    }
                                };
                                for (let property in item) {
                                    temp['data'][property][langKey] = item[property];
                                }
                                this.items[itemCount] = temp;
                            }
                            itemCount++;
                        }
                    });
                })
                .catch(function (error) {
                })
            this.initializeFileManager();
        },
        isAddDisabled: function () {
            return false;
        },
        hasData: function (item) {
            return false;
        },
        randomGenerator() { //id generator (optional)
            return Date.now();
        },
        initializeFileManager() {
            $('.lfm-image').filemanager('image', {prefix: route_prefix});
        },
        addNewItem: function() {
            let data = {
                title: {},
                subtitle: {},
                image: {},
                content: {},
            }
            for (let lang in JSON.parse(this.langs)) {
                data.title[lang] = '';
                data.subtitle[lang] = '';
                data.image[lang] = '';
                data.content[lang] = '';
            }
            this.items.push({
                id: this.randomGenerator(),
                data,
            })
        },
        addItem: async function () {
            await this.addNewItem();
            this.initializeFileManager();
        }
    },
    created() {
        this.fetchData();
    },
}
</script>

<!--
CAUTION: May have some kind of image distortion with 16:9 images. Use with care

1. Give it a "ref" so external JavaScript can access to the component
2. Blade template where it's used.
Example usage:

<div class="card-body">
    <div class="form-group row" id="app">
        <slide-list-component langs='@json(config('lang'))'
                              ref="infolist"
                              class="col-sm-12"></slide-list-component>
    </div>
</div>

3. Data mutation in external JS:

//Vue interaction
if (typeof document.app != 'undefined') {
    if (typeof document.app.$refs.infolist != 'undefined' && typeof document.app.$refs.infolist.$data.items != undefined) {
        let id = inputKey.split('-')[0];
        let langKey = inputKey.split('-')[1];
        let target = document.app.$refs.infolist.$data.items.find((item) => {
            return item.id == id;
        });
        if (target) {
            target['data']['image'][langKey] = file_path;
        }
    }
}
-->

