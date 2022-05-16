<!--Laravel multiple languages Vue component-->
<!--User guide below-->
<template>
    <multi-lang-bootstrap-item-manager :has-data="hasData" :columns="columns" :items="items"
                                       @addItem="addItem()" :langs="langs">
        <template v-slot:main="main">
            <div :class="`input-group col-${columns[0].col}`">
                <img hidden :id="`video_preview-${main.item.id}`"/>
                <input type="text"
                       :id="`${main.item.id}-${main.langKey}`"
                       :name="`${main.langKey}[video][]`"
                       v-model="main.item.data.video[main.langKey]"
                       autocomplete="off"
                       :placeholder="`Required (${langs[main.langKey]})`"
                       class="form-control" required>
                <div class="input-group-append">
                    <button :data-input="`${main.item.id}-${main.langKey}`"
                            :data-preview="`video_preview-${main.item.id}`"
                            class="btn btn-primary lfm-file"
                            type="button">
                        <i class="fas fa-folder-open"></i>
                    </button>
                </div>
            </div>
        </template>
    </multi-lang-bootstrap-item-manager>
</template>

<script>
import axios from 'axios';
import MultiLangBootstrapItemManager from "./Components/MultiLangBootstrapItemManager";

export default {
    name: "VideoListComponent",
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
            col: 3,
        }
    },
    data() {
        return {
            items: Array(),
            columns: [
                {
                    title   : 'Video',
                    col: 9,
                }
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
                for (let langKey in response) {
                    if (response[langKey].length !== 0) {
                        response[langKey].forEach((item, index, arr) => {
                            if (typeof (this.items[index]) != 'undefined') {
                                this.items[index]['data']['video'][langKey] = item;
                            } else {
                                let temp = {
                                    id: index,
                                    data: {
                                        video: {
                                        }
                                    }
                                };
                                temp['data']['video'][langKey] = item;
                                this.items[index] = temp;
                            }
                        })
                    }
                }
            })
            .catch(function(error) {
            });
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
        addNewItem: function() {
            let data = {
                video: {}
            }
            for (let lang in JSON.parse(this.langs)) {
                data.video[lang] = ''
            }
            this.items.push({
                id: this.randomGenerator(),
                data,
            })
        },
        initializeFileManager: function(){
            $('.lfm-file').filemanager('file', {prefix: route_prefix});
        },
        addItem: async function () {
            await this.addNewItem();
            this.initializeFileManager();
        }
    },
    created() {
        this.fetchData();
    },
    mounted() {
    }
}
</script>

<style scoped>

</style>


<!--
1. Give it a "ref" so external JavaScript can access to the component
2. Blade template where it's used.
Example usage:
<div class="card-body">
    <div class="form-group row" id="app">
            <video-list-component langs='@json(config('lang'))'
                                  ref="video"
                                  class="col-sm-12"></video-list-component>
    </div>
</div>

3. Data mutation in external JS:

//Vue interaction
if (typeof document.app != 'undefined') {
  if (typeof document.app.$refs.video != 'undefined' && typeof document.app.$refs.video.$data.items != undefined) {
      let id = inputKey.split('-')[0];
      let langKey = inputKey.split('-')[1];
      let target = document.app.$refs.video.$data.items.find((item) => {
          return item.id == id;
      });
      if (target) {
          target['data']['video'][langKey] = file_path;
      }
  }
}

-->