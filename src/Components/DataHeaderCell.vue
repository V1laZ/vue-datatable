<template>
    <th>
        <a
            href="javascript:void(0);"
            class="d-block mb-1"
            @click.prevent="onSort"
        >
            <BsIcon
                v-show="sortable"
                :icon="sortIconName"
            /> {{ text }}
        </a>
    </th>
</template>

<script setup lang="ts" generic="TRowData extends Record<string, any> = Record<string, any>">
import type { DotPaths } from '@/interfaces'
import BsIcon from '../Icons/bsIcon.vue'
import { computed } from 'vue'

const props = withDefaults(defineProps<{
    i18n: Record<string, string>
    text: string
    dataField: DotPaths<TRowData>
    sort?: DotPaths<TRowData> | null
    sortable?: boolean
    sortDirection?: string | null
}>(), {
    sort: null,
    sortable: false,
    sortDirection: null
})

const emit = defineEmits<{
    sort: [dataField: DotPaths<TRowData>]
}>()

const sortIconName = computed(() => {
    if (props.sort === props.dataField) {
        return (props.sortDirection === 'ASC') ? 'arrow-down' : 'arrow-up'
    }
    return 'arrows-expand'
})

function onSort(): void {
    if (props.sortable) {
        emit('sort', `${props.dataField}`)
    }
}
</script>
