<template>
    <div class="text-base">
        <label @click="focusSelect()" class="select-search"
            >{{ label }}
            <div
                class="cursor-pointer w-full min-h-[38px] p-2 mb-2 border border-gray-200 rounded-sm relative focus:outline-none focus:border-purple-400 content-center"
                :class="{ 'border-purple-400': visibleOption }"
            >
                <div class="text-sm text-slate-400" v-show="!selected">{{ placeholder }}</div>
                <div>
                    <div class="text-sm" v-if="!multipleSelect">
                        {{ selected?.label }}
                    </div>
                    <div v-else>
                        <div
                            class="h-[30px] border border-gray-200 rounded-lg px-2 inline-block content-center mr-2 mb-2"
                            v-for="(item, index) in selected"
                            :key="index"
                        >
                            {{ item.label }}
                            <FontAwesomeIcon :icon="['fas', 'xmark']" @click="removeItem(item)" />
                        </div>
                    </div>
                </div>
                <span class="absolute right-[10px] top-[7px]">
                    <FontAwesomeIcon v-show="visibleOption" :icon="['fas', 'caret-up']" />
                    <FontAwesomeIcon v-show="!visibleOption" :icon="['fas', 'caret-down']" />
                </span>
                <div
                    class="option-items absolute top-[115%] left-[0] z-20 bg-white min-w-full rounded-sm border border-gray-200"
                    v-if="visibleOption"
                >
                    <input
                        ref="select-input"
                        class="w-full text-sm p-2 border-b border-gray-200 focus:outline-none"
                        v-model="searchValue"
                        placeholder="Search"
                    />
                    <div class="max-h-[200px] overflow-auto">
                        <div
                            class="hover:bg-gray-100 p-2"
                            :class="{ 'bg-gray-200': handleSelected(option) }"
                            v-for="(option, index) in optionsFilter"
                            :key="index"
                            @click="handleClickOption(option)"
                        >
                            <p class="text-md">{{ option.label }}</p>
                            <p class="text-sm">{{ option.description }}</p>
                        </div>
                        <p class="text-sm p-2" v-show="optionsFilter.length == 0">
                            No matches found
                        </p>
                    </div>
                </div>
            </div>
        </label>
    </div>
</template>

<script lang='ts'>
import { computed, ref } from 'vue'
import { library } from '@fortawesome/fontawesome-svg-core'
import { faCaretUp, faCaretDown, faXmark } from '@fortawesome/free-solid-svg-icons'
import type { Option } from '@/types/TSelect'

export default {
    name: 'CInput',
    props: {
        modelValue: {
            type: [String, Number, Array],
            default: ''
        },
        label: {
            type: String,
            default: ''
        },
        options: {
            type: null,
            default: [] as Option[]
        },
        placeholder: {
            type: String,
            default: ''
        },
        multipleSelect: {
            type: Boolean,
            default: false
        }
    },
    components: {},
    computed: {
        optionsFilter: function () {
            if (this.searchValue.length == 0) {
                return this.options
            }
            const filteredOptions = this.options.filter((obj: Option) => {
                return obj.label.toLowerCase().indexOf(this.searchValue.toLowerCase()) >= 0
            })

            return filteredOptions
        }
    },
    setup(props, { emit }) {
        library.add({ faCaretUp, faCaretDown, faXmark })
        const visibleOption = ref(false)
        const selected = ref()
        if (props.multipleSelect) {
            selected.value = []
        }

        const searchValue = ref('')

        return { selected, visibleOption, searchValue }
    },

    methods: {
        focusSelect() {
            this.visibleOption = true
        },
        blurSelect() {
            this.visibleOption = false
        },
        handleClickOption(item: Option) {
            if (this.multipleSelect) {
                // console.log('select', this.selected)
                // const data = this.selected.filter((obj) => {
                //     return obj === option
                // })
                console.log('selected', this.selected)

                this.selected.push(item)
                console.log('this.selected', this.selected)
            } else {
                this.$emit('update:modelValue', item.value)
                this.selected = item
            }
            this.visibleOption = true
        },
        handleSelected(item: Option) {
            if (this.multipleSelect) {
                // this.selected.filter((obj) => {
                //     return obj === option
                // })
                // return !!this.selected.indexOf(option)
            }

            return this.selected == item
        },
        removeItem(item: Option) {
            this.selected = this.selected.filter((obj: Option) => {
                return obj != item
            })
        }
    }
}
</script>

<style lang="scss" scoped>
.select-search:has(input:focus) option-items {
    display: block;
}
/* width */
::-webkit-scrollbar {
    width: 8px;
}

/* Track */
::-webkit-scrollbar-track {
    border-radius: 8px;
}

/* Handle */
::-webkit-scrollbar-thumb {
    background: rgb(184, 184, 184);
    border-radius: 8px;
}
</style>
