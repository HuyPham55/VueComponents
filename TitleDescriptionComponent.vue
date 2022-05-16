<!--Laravel multiple languages Vue component-->
<!--User guide below-->
<template>
    <multi-lang-bootstrap-item-manager :has-data="hasData" :columns="columns" :items="items"
                                       @addItem="addItem()" :langs="langs">
        <template v-slot:main="main">
            <div :class="`input-group col-${columns[0].col}`">
                <input class="form-control" type="text" v-model="main.item.data.title[main.langKey]"
                       :name="`${main.langKey}[seo_description][${main.item.id}][title]`" :placeholder="langs[main.langKey]">
            </div>
            <div :class="`input-group col-${columns[1].col}`">
                <input class="form-control" type="text" v-model="main.item.data.subtitle[main.langKey]"
                       :name="`${main.langKey}[seo_description][${main.item.id}][subtitle]`" :placeholder="langs[main.langKey]">
            </div>
            <div :class="`input-group col-${columns[2].col}`">
                <input type="number"
                       :id="`${main.item.id}-${main.langKey}`"
                       :name="`${main.langKey}[seo_description][${main.item.id}][url]`"
                       v-model="main.item.data.url[main.langKey]"
                       autocomplete="off"
                       placeholder=""
                       class="form-control">
            </div>
        </template>
    </multi-lang-bootstrap-item-manager>
</template>

<script>
import MultiLangBootstrapItemManager from "./Components/MultiLangBootstrapItemManager";

export default {
    name: "TitleDescriptionComponent",
    components: {
        MultiLangBootstrapItemManager,
    },
    props: {
        langs: {
            type: Object,
        },
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
                    title   : 'Subtitle',
                    col: 3,
                },
                {
                    title   : 'URL',
                    col: 3,
                },
                //total col should be equals to 12
            ],
            langs: JSON.parse(this.langs)
        }
    },
    methods: {
        fetchData: async function () {
            await axios.post(fetchRoute, {
            })
                .then(response => {
                    response = response.data;
                    Object.keys(response).forEach((langKey) => {
                        let itemCount = 0;
                        for (let id in (response[langKey])) {
                            let item = (response[langKey][id]);
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
                                        url: {},
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
                .catch(function(error) {
                })
            this.initializeFileManager();
        },
        isAddDisabled: function () {
            return false;
        },
        hasData: function (item) {
            for (let lang in JSON.parse(this.langs)) {
                if (item.data['title'][lang]) return true;
            }
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
                url: {},
            }
            for (let lang in JSON.parse(this.langs)) {
                data.title[lang] = '';
                data.subtitle[lang] = '';
                data.url[lang] = '';
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

<style scoped>

</style>

<!--
Simple component that makes use of MultiLangBootstrapItemManager Base component
An item contain:
    1. Title
    2. Description
    3. URL

Feel free to change where needed

Blade template
<div class="card-body">
    <div class="form-group row" id="app">
        <title-description-component
            langs='@json(config('lang'))'
            class="col-sm-12"></title-description-component>
    </div>
</div>
-->