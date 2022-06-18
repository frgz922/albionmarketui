<template>
    <div class="relative min-h-full flex flex-col">
        <!-- Navbar -->
        <Disclosure as="nav" class="flex-shrink-0 bg-gradient-to-r from-sky-800 to-slate-800">
            <div class="max-w-7xl mx-auto px-2 sm:px-4 lg:px-8">
                <div class="relative flex items-center justify-between h-16">
                    <div class="flex justify-start">
                        <h1 class="text-stone-100">
                            ALBION MARKET UI
                        </h1>
                    </div>
                    <!-- Search section -->
                    <!-- <div class="flex-1 flex justify-center lg:justify-center">
                        <div class="w-96 px-2 lg:px-6">
                            <div class="relative text-indigo-200 focus-within:text-gray-400">
                                <div class="absolute inset-y-0 left-0 pl-3 flex items-center pointer-events-none">
                                    <SearchIcon class="h-5 w-5" aria-hidden="true" />
                                </div>
                                <input id="search" name="search" @change="onSearch"
                                    class="block w-full pl-10 pr-3 py-2 border border-transparent rounded-md leading-5 bg-indigo-400 bg-opacity-25 text-indigo-100 placeholder-indigo-200 focus:outline-none focus:bg-white focus:ring-0 focus:placeholder-gray-400 focus:text-gray-900 sm:text-sm"
                                    placeholder="Buscar Item" type="search" v-model="search"  />
                            </div>
                        </div>
                    </div> -->

                    <div class="flex items-center justify-end">
                        <!-- Profile dropdown -->
                        <span class="relative z-0 inline-flex shadow-sm rounded-md">
                            <button type="button" @click="selectLang(langs['english'])"
                                class="rounded-l inline-block px-6 py-2.5 bg-blue-700 text-white font-medium text-xs leading-tight uppercase hover:bg-sky-700 focus:bg-sky-700 focus:outline-none focus:ring-0 active:bg-blue-800 transition duration-150 ease-in-out">{{
                                        langs['english'].text
                                }}</button>
                            <button type="button" @click="selectLang(langs['spanish'])"
                                class="rounded-r inline-block px-6 py-2.5 bg-blue-700 text-white font-medium text-xs leading-tight uppercase hover:bg-sky-700 focus:bg-sky-700 focus:outline-none focus:ring-0 active:bg-blue-800 transition duration-150 ease-in-out">{{
                                        langs['spanish'].text
                                }}</button>
                        </span>
                    </div>
                </div>
            </div>
        </Disclosure>
    </div>
</template>

<script lang="ts">
import { defineComponent } from "vue";
import {
    Disclosure,
    DisclosureButton,
    DisclosurePanel,
    Menu,
    MenuButton,
    MenuItem,
    MenuItems,
} from "@headlessui/vue";
import {
    BadgeCheckIcon,
    ChevronDownIcon,
    ChevronRightIcon,
    CollectionIcon,
    SortAscendingIcon,
    StarIcon,
    SearchIcon,
} from "@heroicons/vue/solid";
import { MenuAlt1Icon, XIcon } from "@heroicons/vue/outline";

export default defineComponent({
    setup() { },
    components: {
        SearchIcon,
        Disclosure,
        DisclosureButton,
        DisclosurePanel,
        Menu,
        MenuButton,
        MenuItem,
        MenuItems,
        BadgeCheckIcon,
        ChevronDownIcon,
        ChevronRightIcon,
        CollectionIcon,
        SortAscendingIcon,
        StarIcon,
    },
    props: {
        langs: Object
    },
    data() {
        return {
            search: null,
        }
    },
    methods: {
        selectLang(lang) {
            this.$emit("selectLanguage", lang);
        },
    }
});
</script>
