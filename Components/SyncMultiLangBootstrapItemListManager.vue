<template>
    <div>
        <ul class="nav nav-tabs">
            <li class="nav-item" v-for="(value, key, index) in langs">
                <a :class="['nav-link', index===0?'active':'']" data-toggle="tab"
                   :href="'#'+key">
                    {{ langs[key] }}
                </a>
            </li>
        </ul>
        <slot name="header">
            <div class="row px-3 pt-3">
                <div v-for="(column, index) in columns"
                     :class="'col'+ (column.col ? `-${column.col}`:'')">
                    <label class="control-label" :title="column.title">
                        {{ column.title }}
                    </label>
                </div>
                <div :class="'col-'+colDivision">
                    <label class="control-label" :title="removeTitle">
                        {{ removeTitle }}
                    </label>
                </div>
            </div>
        </slot>
        <div class="tab-content">
            <section v-for="(value, key, index) in langs"
                     :id="key"
                     :class="`tab-pane fade p-0 ${index===0?'active show':''}`">
                <div class="col text-center p-3 bg-light font-italic" v-show="this.items[key].length===0">
                    No items found.
                </div>
                <transition-group name="fade" mode="out-in" tag="div">
                    <div v-for="(item, index) in items[key]"
                         :class="['row', 'p-3', item.isHovered?'bg-light':'']"
                         :key="index+key"
                         @mouseover.stop="isHover(index)"
                         @mouseleave.stop="isNotHover(index)">
                        <slot name="main" :item="item" :langKey="key"/>
                        <remove-button
                            @click="removeItem(index)"
                            :has-data="hasData(item)"
                            :col-division="colDivision"
                            :label="removeTitle"
                            :hover-label="hoverTitle"/>
                    </div>
                </transition-group>
            </section>
        </div>
        <div class="row justify-content-between">
            <bootstrap-add-button
                @click="$emit('addItem')" :is-disabled="disableAdd"/>
            <clearButton @click="clearAll"/>
        </div>
    </div>
</template>

<script>


import bootstrapAddButton from "@/components/components/components/bootstrapAddButton.vue";
import removeButton from "@/components/components/components/removeButton.vue";
import clearButton from "@/components/components/components/clearButton.vue";

export default {
    name: "SyncMultiLangBootstrapItemListManager",
    components: {
        clearButton,
        bootstrapAddButton,
        removeButton,
    },
    props: {
        langs: {
            type: Object,
        },
        items: {
            type: Object,
            required: true,
        },
        columns: {
            type: Array,
            required: true,
        },
        disableAdd: {
            type: Boolean,
            default: false,
        },
        hasData: {
            type: Function,
            default: false,
            required: true,
        },
    },
    inject: ['removeColumn'],
    methods: {
        removeItem: function (index) {
            // this.$delete(this.items, index);
            for (let lang in this.langs) {
                this.items[lang].splice(index, 1);
            }
        },
        isHover: function (index) {
            typeof this.items[index] != 'undefined' ? this.items[index]['isHovered'] = true : '';
        },
        isNotHover: function (index) {
            typeof this.items[index] != 'undefined' ? this.items[index]['isHovered'] = false : '';
        },
        clearAll: function() {
            for (let lang in this.langs) {
                this.items[lang].splice(0, this.items[lang].length)
            }
        }
    },
    data() {
        return {
            //The col-{result} for "Remove" column
            colDivision: typeof this["removeColumn"] != "undefined" ? this["removeColumn"].col : (12 - (this.columns.reduce((previousValue, currentValue) => {
                return previousValue + currentValue.col;
            }, 0) | 0)),
        }
    },
    computed: {
        removeTitle: function () {
            return typeof this["removeColumn"] != 'undefined' ? this["removeColumn"].title : 'Remove';
        },
        hoverTitle: function () {
            return typeof this["removeColumn"] != 'undefined' ? this["removeColumn"].removeTitle : 'Confirm delete?';
        }
    },
    mounted() {
    }
}
</script>

<style scoped>
.fade-enter-active, .fade-leave-active {
    transition: opacity .5s;
}

.fade-enter, .fade-leave-to /* .fade-leave-active below version 2.1.8 */
{
    opacity: 0;
}

</style>
<!--
Update 1/4/2022
Reduce component dependence on parent component, especially on removeColumn provide
Added friendly message when no items present
-->
