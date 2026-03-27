<template>
    <BFormInput
        v-model="localFilterValue"
        size="sm"
        :placeholder="i18n.search"
        @update:model-value="debouncedSetFilterValue"
    />
</template>

<script setup lang="ts" generic="TRowData extends Record<string, any> = Record<string, any>">
import { ref } from 'vue'
import BFormInput from './ui/BFormInput.vue'
import type { DotPaths } from '@/interfaces'
import { useDebounceFn } from '@vueuse/core'

const props = defineProps<{
    i18n: Record<string, string>
    dataField: DotPaths<TRowData>
    filter: Record<string, string>
}>()

const emit = defineEmits<{
    filter: [filter: Record<string, string>]
}>()

const localFilterValue = ref<string>(((props.filter.hasOwnProperty(props.dataField)) ? (props.filter[props.dataField] ?? '') : ''))

const debouncedSetFilterValue = useDebounceFn(() => {
    const filter = JSON.parse(JSON.stringify(props.filter))
    filter[props.dataField] = `${localFilterValue.value}`
    emit('filter', filter)
}, 500)

</script>
