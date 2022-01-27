<!--User instruction below-->
<template>
    <bootstrap-item-list-manager
        :columns="columns"
        :items="items"
        @addItem="addItem()"
        :disable-add="isAddDisabled()"
        :has-data="hasData">
        <template v-slot:main="main">
            <div :class="`col-${columns[0].col}`">
                <select class="form-select form-control"
                        v-model="main.item.data.category_id"
                        :required="parseInt(categoryRequired)?true:undefined"
                        :name="`categories[${main.item.id}][category_id]`">
                    <option value="" selected>Select</option>
                    <option
                        v-for="(category) in categories"
                        :selected="main.item.data.category_id==category.id"
                        :value="category.id"
                        :title="category.title"
                        v-show="isShown(category)">
                        {{ category.title }}
                    </option>
                </select>
            </div>
            <div :class="`col-${columns[1].col}`">
                <input class="form-control" min="0" type="number" max="e10"
                       v-model="main.item.data.sorting"
                       :name="`categories[${main.item.id}][sorting]`">
            </div>
        </template>
    </bootstrap-item-list-manager>
</template>
<script>
import bootstrapItemListManager from "./Components/bootstrapItemListManager";

export default {
    name: "itemsCategoriesSorting",
    components: {
        bootstrapItemListManager,
    },
    props: {
        items: {
            type: Array(),
        },
        categories: {
            type: Array(),
        },
        columns: {
            type: Array(),
            required: true,
        },
        categoryRequired: {
            type: null,
            required: false,
            default: 1,
        }
    },
    methods: {
        randomGenerator() { //id generator (optional)
            return Date.now();
        },
        addItem: function () {
            // let data = {
            //     status: 1,
            //     percentage: 100,
            //     title: {}
            // }
            // for (let lang in this.langs) {
            //     data.title[lang] = ''
            // }
            this.items.push({
                id: this.randomGenerator(),
                data: {
                    category_id: '',
                    sorting: '',
                }
            })
        },
        isAddDisabled: function () {
            return (this.items.length === this.categories.length);
        },
        hasData: function (item) {
            return parseInt(item.data.category_id + item.data.sorting);
        },
        isShown: function (category) {
            return !this.items.map(item => item.data.category_id).includes(category.id);
        },
    },
    mounted() {
        if (this.items.length===0 && parseInt(this.categoryRequired)) {
            this.addItem();
        }
    }
}
</script>

<style scoped>

</style>
<!--User instruction-->
<!--
    data: {
        categories: [
            {
                title: "Category 1",
                id: 1
            }
            ,
            {
                title: "Category 2",
                id: 2
            }
            ,
            {
                title: "Category 3",
                id: 3
            }

        ],
        items: [],
        columns: [
            {
                title: 'Categories',
                col: 8,
            },
            {
                title: 'Sorting',
                col: 4,
            }
        ]
    },
    provide: {
        removeColumn: {
            title: 'Delete',
            hoverTitle: 'Confirm delete?',
            col: 3,
        }
    }

    <items-categories-sorting
    :items="items"
    :columns="columns"
    category-required="0"
    :categories="categories">
    </items-categories-sorting>
-->
