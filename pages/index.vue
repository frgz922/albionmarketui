<template>
    <Nav :langs="langs" @select-language="selectLang" />
    <div class="container mx-auto px-4 sm:px-6 lg:px-8 mt-8">
        <div class="grid grid-cols-1 gap-4 items-start lg:grid-cols-4 lg:gap-8">
            <!-- Left column -->
            <div class="grid grid-cols-1 gap-4 lg:col-span-2 h-3/5">
                <div class="rounded-lg bg-white overflow-hidden shadow overflow-scroll overflow-x-auto scrollbar">
                    <div class="px-6 pt-4">
                        <label for="email" class="block text-lg font-medium text-gray-900">Buscar Item</label>
                        <div class="mt-1 flex rounded-md shadow-sm w-96">
                            <div class="relative flex items-stretch flex-grow focus-within:z-10">
                                <div class="absolute inset-y-0 left-0 pl-3 flex items-center pointer-events-none">
                                    <FilterIcon class="h-5 w-5 text-gray-400" aria-hidden="true" />
                                </div>
                                <input v-model="search" id="search" name="search" type="text" v-on:keyup="searchItems"
                                    class="focus:ring-sky-500 focus:border-sky-500 block w-full rounded-none rounded-l-md pl-10 sm:text-sm border-sky-500"
                                    placeholder="Introduce el item a consultar" />
                            </div>
                            <button type="button" @click="cleanResults"
                                class="-ml-px relative inline-flex items-center space-x-2 px-4 py-2 border border-red-500 text-sm font-medium rounded-r-md text-gray-700 bg-red-500 hover:bg-red-600 focus:outline-none focus:ring-1 focus:ring-red-600 focus:border-red-600 hover:border-red-600">
                                <XIcon class="h-5 w-5 text-white" aria-hidden="true" />
                            </button>
                        </div>
                    </div>
                    <div class="p-6">
                        <table class="min-w-full divide-y divide-gray-300 border border-slate-200">
                            <thead class="bg-gray-50">
                                <tr>
                                    <th scope="col"
                                        class="py-3.5 pl-4 pr-3 text-left text-sm font-semibold text-gray-900 sm:pl-6">
                                        Nombre</th>
                                    <th scope="col"
                                        class="py-3.5 pl-4 pr-3 text-left text-sm font-semibold text-gray-900 sm:pl-6">
                                        Tier</th>
                                    <th scope="col"
                                        class="py-3.5 pl-4 pr-3 text-left text-sm font-semibold text-gray-900 sm:pl-6">
                                        Encantamiento</th>
                                </tr>
                            </thead>
                            <tbody class="divide-y divide-gray-200 bg-white">
                                <tr v-for="(item, key) in currentItems" :key="item.itemName.toLowerCase() + key"
                                    :class="key % 2 === 0 ? undefined : 'bg-gray-100'">
                                    <td class="whitespace-nowrap py-2 text-sm font-medium text-gray-900 sm:pl-6">
                                        {{ item.itemName }}</td>
                                    <td class="whitespace-nowrap py-2 text-sm font-medium text-gray-900 sm:pl-6">
                                        {{ item.tier }}</td>
                                    <td class="whitespace-nowrap py-2 text-sm font-medium text-gray-900 sm:pl-6">
                                        <RadioGroup v-model="selectedEnchantment">
                                            <div class="flex items-center space-x-3">
                                                <RadioGroupOption as="template" v-for="color in item.itemData"
                                                    :key="getEnchanmentColor(color).name" :value="color.UniqueName"
                                                    v-slot="{ active, checked }">
                                                    <div
                                                        :class="[getEnchanmentColor(color).selectedColor, active && checked ? 'ring ring-offset-1' : '', !active && checked ? 'ring-2' : '', '-m-0.5 relative p-0.5 rounded-full flex items-center justify-center cursor-pointer focus:outline-none']">
                                                        <RadioGroupLabel as="span" class="sr-only">{{
                                                                getEnchanmentColor(color).name
                                                        }}
                                                        </RadioGroupLabel>
                                                        <span aria-hidden="true"
                                                            :class="[getEnchanmentColor(color).bgColor, 'h-8 w-8 border border-black border-opacity-10 rounded-full']" />
                                                    </div>
                                                </RadioGroupOption>
                                            </div>
                                        </RadioGroup>
                                    </td>
                                    <td class="whitespace-nowrap py-2 text-sm font-medium text-gray-900 sm:pl-6">
                                        <button type="button" @click="getItemPriceData(item.itemData)"
                                            class="inline-flex items-center px-3 py-2 border border-transparent shadow-sm text-sm leading-4 font-medium rounded-md text-white bg-sky-600 hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500">
                                            Buscar
                                            <SearchIcon class="ml-2 -mr-0.5 h-4 w-4" aria-hidden="true" />
                                        </button>
                                    </td>
                                </tr>
                            </tbody>
                        </table>
                    </div>
                    <div class="text-center" v-if="allItems">
                        <nav class="px-4 py-3 flex items-center justify-between border-t border-gray-200 sm:px-6 max-w-3xl mx-auto px-4 sm:px-6 lg:max-w-7xl lg:px-8"
                            aria-label="Pagination">
                            <div class="hidden sm:block">
                                <p class="text-sm text-gray-700">
                                    Mostrando
                                    {{ ' ' }}
                                    <span class="font-medium">{{ currentPage * perPage - 4 }}</span>
                                    {{ ' ' }}
                                    a
                                    {{ ' ' }}
                                    <span class="font-medium">{{ currentPage * perPage < allItems.length ? currentPage *
                                            perPage : allItems.length
                                    }}</span>
                                            {{ ' ' }}
                                            de
                                            {{ ' ' }}
                                            <span class="font-medium">{{ allItems.length }}</span>
                                            {{ ' ' }}
                                            resultados
                                </p>
                            </div>
                            <div class="flex-1 flex justify-between sm:justify-end">
                                <button @click="previousPage" :class="[isFirstPage ? 'bg-slate-400' : 'bg-cyan-600']"
                                    class="mr-2 relative inline-flex items-center px-4 py-2 border border-gray-300 text-sm font-medium rounded-md text-white"
                                    :disabled="isFirstPage">Anterior</button>
                                <button @click="nextPage" :class="[isLastPage ? 'bg-slate-400' : 'bg-cyan-600']"
                                    class="relative inline-flex items-center px-4 py-2 border border-gray-300 text-sm font-medium rounded-md text-white"
                                    :disabled="isLastPage">Siguiente</button>
                            </div>
                        </nav>
                    </div>
                </div>
            </div>

            <!-- Right column -->
            <div class="grid grid-cols-1 gap-4 lg:col-span-2 h-3/5">
                <div class="rounded-lg bg-white overflow-hidden shadow overflow-scroll overflow-x-auto scrollbar">
                    <div class="border border-gray-200 rounded-lg shadow-sm">
                        <div class="px-6 pt-4">
                            <h2 class="text-lg leading-6 font-medium text-gray-900">Resultados de la Busqueda</h2>
                        </div>
                        <div class="p-6 scrollbar overflow">
                            <div v-for="(value, key) in itemsData" :key="key">
                                <div class="flex items-center">
                                    <h2
                                        class="flex-shrink-0 pr-4 bg-white text-sm tracking-wider font-semibold uppercase text-sky-600">
                                        {{ getQualityName(value.quality) }}</h2>
                                    <div class="flex-1 border-t-2 border-gray-200" />
                                </div>
                                <div class="flex m-2">
                                    <div class="flex-none w-20">
                                        <img :src="`https://render.albiononline.com/v1/item/${value.prices[0].item_id}.png?quality=${value.quality}`"
                                            alt="">
                                    </div>
                                    <ul role="list"
                                        class="space-y-5 lg:space-y-0 lg:grid lg:grid-cols-4 gap-8 lg:gap-y-5 flex-initial">
                                        <li class="flex items-start lg:col-span-1" v-for="(price, key) in value.prices"
                                            :key="key">
                                            <div class="flex-shrink-0">
                                                <LocationMarkerIcon class="h-5 w-5 text-sky-400" aria-hidden="true" />
                                            </div>
                                            <div class="flex flex-col justify-center gap-1 text-center">
                                                <h3 class="pl-2 text-sm font-medium text-gray-900">{{ price.city }}</h3>
                                                <span>{{ price.sell_price_min > 0 ? price.sell_price_min : 'N/A'
                                                }}</span>
                                            </div>
                                        </li>

                                    </ul>
                                </div>
                            </div>
                        </div>

                    </div>
                </div>

            </div>
        </div>
    </div>

</template>

<script lang="ts">
import { defineComponent } from 'vue'
import data from '~/static/items.json'
import { Menu, MenuButton, MenuItem, MenuItems } from '@headlessui/vue'
import { ChevronDownIcon, ChevronLeftIcon, ChevronRightIcon } from '@heroicons/vue/solid'
import _ from "lodash"
import { RadioGroup, RadioGroupLabel, RadioGroupOption } from '@headlessui/vue'
import { SearchIcon, LocationMarkerIcon, FilterIcon, XIcon } from '@heroicons/vue/solid'

export default defineComponent({
    setup() {
        useHead({
            bodyAttrs: {
                class: 'bg-gray-200 h-screen scrollbar'
            }
        })
    },
    components: {
        Menu,
        MenuButton,
        MenuItem,
        MenuItems,
        ChevronDownIcon,
        ChevronLeftIcon,
        ChevronRightIcon,
        RadioGroup,
        RadioGroupLabel,
        RadioGroupOption,
        SearchIcon,
        LocationMarkerIcon,
        FilterIcon,
        XIcon
    },
    watch: {
        qualities: {
            immediate: true,
            handler(qualities) {
                // initialise to the "supp_name" from options
                /* this.quality = qualities[
                    this.langs[this.defaultLang].text.toLowerCase()
                ].map(({ text }) => text) */
            }
        }
    },
    data() {
        return {
            selectedEnchantment: null,
            currentPage: 1,
            perPage: 5,
            langs: {
                english: {
                    text: 'EN',
                    local: 'EN-US',
                    default: 'english',
                },
                spanish: {
                    text: 'ES',
                    local: 'ES-ES',
                    default: 'spanish',
                },
            },
            defaultLang: 'spanish',
            marketItems: null,
            qualities: {
                en: [
                    {
                        value: 0,
                        text: 'All',
                    },
                    {
                        value: 1,
                        text: 'Normal',
                    },
                    {
                        value: 2,
                        text: 'Good',
                    },
                    {
                        value: 3,
                        text: 'Outstanding',
                    },
                    {
                        value: 4,
                        text: 'Excellent',
                    },
                    {
                        value: 5,
                        text: 'Masterpiece',
                    },
                ],
                es: [
                    {
                        value: 0,
                        text: 'Todas',
                    },
                    {
                        value: 1,
                        text: 'Normal',
                    },
                    {
                        value: 2,
                        text: 'Good',
                    },
                    {
                        value: 3,
                        text: 'Outstanding',
                    },
                    {
                        value: 4,
                        text: 'Excellent',
                    },
                    {
                        value: 5,
                        text: 'Masterpiece',
                    },
                ],
            },
            quality: 0,
            tiers: {
                en: [
                    {
                        value: 'ALL',
                        text: 'All',
                    },
                    {
                        value: 'T1',
                        text: 'Tier 1',
                    },
                    {
                        value: 'T2',
                        text: 'Tier 2',
                    },
                    {
                        value: 'T3',
                        text: 'Tier 3',
                    },
                    {
                        value: 'T4',
                        text: 'Tier 4',
                    },
                    {
                        value: 'T5',
                        text: 'Tier 5',
                    },
                    {
                        value: 'T6',
                        text: 'Tier 6',
                    },
                    {
                        value: 'T7',
                        text: 'Tier 7',
                    },
                    {
                        value: 'T8',
                        text: 'Tier 8',
                    },
                    {
                        value: 'T1',
                        text: 'Tier 1',
                    },
                ],
                es: [
                    {
                        value: 'ALL',
                        text: 'Todos',
                    },
                    {
                        value: 'T1',
                        text: 'Tier 1',
                    },
                    {
                        value: 'T2',
                        text: 'Tier 2',
                    },
                    {
                        value: 'T3',
                        text: 'Tier 3',
                    },
                    {
                        value: 'T4',
                        text: 'Tier 4',
                    },
                    {
                        value: 'T5',
                        text: 'Tier 5',
                    },
                    {
                        value: 'T6',
                        text: 'Tier 6',
                    },
                    {
                        value: 'T7',
                        text: 'Tier 7',
                    },
                    {
                        value: 'T8',
                        text: 'Tier 8',
                    },
                ],
            },
            tier: 'ALL',
            enchaments: {
                en: [
                    {
                        value: 0,
                        text: 'No Enchantment',
                    },
                    {
                        value: 1,
                        text: 'Enchatment 1',
                    },
                    {
                        value: 2,
                        text: 'Enchatment 2',
                    },
                    {
                        value: 3,
                        text: 'Enchatment 3',
                    },
                ],
                es: [
                    {
                        value: 0,
                        text: 'S/ Encantamiento',
                    },
                    {
                        value: 1,
                        text: 'Encantamiento 1',
                    },
                    {
                        value: 2,
                        text: 'Encantamiento 2',
                    },
                    {
                        value: 3,
                        text: 'Encantamiento 3',
                    },
                ],
            },
            enchament: 0,
            localLang: null,
            itemsData: null,
            uniqueNames: [],
            itemName: null,
            currentItems: null,
            arrowCounter: -1,
            filteredItems: [],
            search: null,
        }
    },
    async beforeMount() {
        this.localLang = this.langs[this.defaultLang].local

        this.marketItems = _.chain(data.filter(item => !item.UniqueName.includes('QUESTITEM')))
            .groupBy(`LocalizedNames.${this.localLang}`)
            .map((value, key) => ({ itemName: key, itemData: value, tier: value[0].UniqueName.split('_')[0] }))
            .value()

        this.currentItems = this.allItems.slice((this.currentPage - 1) * this.perPage, this.currentPage * this.perPage)

        await this.getItemPriceData(this.currentItems[0].itemData)

    },
    computed: {
        isFirstPage() {
            return this.currentPage === 1 ?? true;
        },
        isLastPage() {
            return this.currentItems.length === this.allItems.length ?? true;
        },
        allItems() {
            return this.search ? this.filteredItems : this.marketItems
        },
    },
    methods: {
        selectLang(lang) {
            this.defaultLang = lang.default
            this.localLang = this.langs[this.defaultLang].local

            this.marketItems = _.chain(data.filter(item => !item.UniqueName.includes('QUESTITEM')))
                .groupBy(`LocalizedNames.${this.localLang}`)
                .map((value, key) => ({ itemName: key, itemData: value, tier: value[0].UniqueName.split('_')[0] }))
                .value()

            this.currentItems = this.allItems.slice((this.currentPage - 1) * this.perPage, this.currentPage * this.perPage)
        },
        searchItems() {
            this.localLang = this.langs[this.defaultLang].local
            this.currentItems.splice(0)
            let result = []
            this.currentPage = 1

            if (this.search) {
                result = this.marketItems.filter(item => item.itemName.toLowerCase().normalize('NFD')
                    .replace(/[\u0300-\u036f]/g, '').includes(this.search.toLowerCase().normalize('NFD')
                        .replace(/[\u0300-\u036f]/g, '')))

            } else {
                result = this.marketItems
            }

            this.filteredItems = result

            this.currentItems = result.slice((this.currentPage - 1) * this.perPage, this.currentPage * this.perPage)
        },
        cleanResults() {
            this.search = null
            this.filteredItems = []
            this.currentPage = 1
            this.currentItems = this.marketItems.slice((this.currentPage - 1) * this.perPage, this.currentPage * this.perPage)
        },
        nextPage() {
            this.currentPage++
            this.currentItems = this.allItems.slice((this.currentPage - 1) * this.perPage, this.currentPage * this.perPage)

        },
        previousPage() {
            if (this.currentPage >= 1) {
                this.currentPage--
                this.currentItems = this.allItems.slice((this.currentPage - 1) * this.perPage, this.currentPage * this.perPage)
            }
        },
        getEnchanmentColor(item) {
            if (item.UniqueName.includes('@1')) {
                return { bgColor: 'bg-green-500', selectedColor: 'ring-green-500', name: item.UniqueName }
            } else if (item.UniqueName.includes('@2')) {
                return { bgColor: 'bg-blue-500', selectedColor: 'ring-blue-500', name: item.UniqueName }
            } else if (item.UniqueName.includes('@3')) {
                return { bgColor: 'bg-purple-500', selectedColor: 'ring-purple-500', name: item.UniqueName }
            } else {
                return { bgColor: 'bg-gray-500', selectedColor: 'ring-gray-500', name: item.UniqueName }
            }
        },
        async getItemPriceData(item) {
            let itemID = '';

            if (this.selectedEnchantment) {
                itemID = this.selectedEnchantment
            } else {
                itemID = item[0].UniqueName
            }

            const url = `https://www.albion-online-data.com/api/v2/stats/prices/${itemID}.json?locations=Bridgewatch,Caerleon,Fort Sterling,Lymhurst,Martlock,Thetford&qualities=1,2,3,4,5`

            const result = await $fetch(url)

            this.itemsData = _.chain(result)
                .groupBy("quality")
                .map((value, key) => ({ quality: key, prices: value }))
                .value()


        },
        getQualityName(value) {
            switch (value) {
                case '1':
                    return this.qualities[
                        this.langs[this.defaultLang].text.toLowerCase()
                    ][1].text

                case '2':
                    return this.qualities[
                        this.langs[this.defaultLang].text.toLowerCase()
                    ][2].text

                case '3':
                    return this.qualities[
                        this.langs[this.defaultLang].text.toLowerCase()
                    ][3].text

                case '4':
                    return this.qualities[
                        this.langs[this.defaultLang].text.toLowerCase()
                    ][4].text

                case '5':
                    return this.qualities[
                        this.langs[this.defaultLang].text.toLowerCase()
                    ][5].text


                default:
                    return this.qualities[
                        this.langs[this.defaultLang].text.toLowerCase()
                    ][1].text

            }
        }
    }
})
</script>

<style lang="scss">
.container-height {
    height: 390px;
}
</style>