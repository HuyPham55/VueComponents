<!--User guide at the bottom-->
<template>
    <div>
        <slot name="header">
            <div class="row px-3 pt-3 h5">
                <div class="row col">
                    <div :class="'col'+ (column.col ? `-${column.col}`:'') "
                         v-for="column in columns">
                        <label class="control-label" :title="column.title | ucFirst">
                            {{ column.title | ucFirst }}
                        </label>
                    </div>
                </div>
                <div :class="'col-'+colDivision">
                    <label class="control-label" title="Remove button">
                        Remove
                    </label>
                </div>
            </div>
        </slot>
        <section class="tab-pane container p-0">
            <transition-group name="fade">
                <div
                    v-for="(item, index) in items"
                    :key="item.id"
                    :class="['row', 'p-3', item.isHovered?'bg-light':'']"
                    @mouseover.stop="isHover(index)"
                    @mouseleave.stop="isNotHover(index)">
                    <div class="row col">
                        <slot class=row name="main" :item="item"/>
                    </div>
                    <remove-button
                        @click="removeItem(index)"
                        :has-data="hasData(item)"
                        :col-division="colDivision"
                        :label="removeColumn.title"
                        :hover-label="removeColumn.hoverTitle"/>
                </div>
            </transition-group>
        </section>
        <bootstrap-add-button @click="$emit('addItem')" :is-disabled="disableAdd"/>
    </div>
</template>

<script>
import removeButton from "./SubComponents/removeButton";
import bootstrapAddButton from "./SubComponents/bootstrapAddButton";

export default {
    name: "bootstrapItemListManager",
    components: {
        bootstrapAddButton,
        removeButton,
    },
    props: {
        items: {
            type: Array,
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
    data() {
        return {
            //The col-{result} for "Remove" column
            colDivision: this.removeColumn.col || (12 - (this.columns.reduce((previousValue, currentValue) => {
                return previousValue + currentValue.col;
            }, 0) | 0)),
        }
    },
    methods: {
        removeItem: function (index) {
            this.$delete(this.items, index);
        },
        isHover: function (index) {
            this.$set(this.items[index], 'isHovered', true);
        },
        isNotHover: function (index) {
            this.$set(this.items[index], 'isHovered', false);
        }
    },
    filters: {
        ucFirst: function (sInput) {
            return sInput.charAt(0).toUpperCase() + sInput.slice(1);
        }
    },
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

section div {
    transition: background 0.25s ease-in;
}
</style>
<!--Bootstrap-compatible item list manager -->
<!-- Structure
Data:
this.items.push({
    id: {{$id??0}},
    data: {
        //item data
    }
})

Language: Single language only

Columns:
All columns except for "Remove" column
Each column has "col" and "title" properties
The total number of col is on scale of 12

disableAdd:
Option to disable "Add" button

hasData:
Option to toggle state of "Remove button"

Inject: removeColumn {
    title: "Translated title for remove button",
    hoverTitle: "Translated title for confirm action",
    col: "The number of col on scale of 12"
}
Note: Slot props is accessible via "main"
-->

<!--Example: 3 columns, [5-5]-2 column division -> [6-6]-2 -->
