<template>
    <thead class="__datatable-header">
        <tr>
            <th
                v-if="selectableRows && selectableRowsCheckboxes"
                class="vue-datatable-vertical-align-middle"
            >
                <div class="custom-control custom-checkbox">
                    <label class="d-flex align-items-center align-content-center gap-1">
                        <input
                            v-model="selectAllState"
                            type="checkbox"
                            class="custom-control-input"
                        />
                        <span>{{ selectedCount }}</span>
                    </label>
                </div>
            </th>
            <th v-if="actions && actionsOnLeft"></th>
            <DataHeaderCell
                v-for="(cell, index) in header"
                :key="`header-${index}`"
                :text="cell.text"
                :data-field="cell.data"
                :sort="sort"
                :sort-direction="sortDirection"
                :filterable="cell.filterable"
                :filter="filter"
                :sortable="cell.sortable"
                :i18n="i18n"
                @sort="onSort"
            />
            <th v-if="actions && !actionsOnLeft"></th>
        </tr>
        <tr v-if="header.some(item => item.filterable === true)">
            <th v-if="selectableRows && selectableRowsCheckboxes"></th>
            <th v-if="actions && actionsOnLeft"></th>
            <th
                v-for="(cell, index) in header"
                :key="`header-filter-${index}`"
            >
                <DataHeaderCellFilter
                    v-if="cell.filterable"
                    :data-field="cell.data"
                    :filter="filter"
                    :i18n="i18n"
                    @filter="onFilter"
                    @select.stop
                />
            </th>
            <th v-if="actions && !actionsOnLeft"></th>
        </tr>
    </thead>
</template>

<script setup lang="ts" generic="TRowData extends Record<string, any> = Record<string, any>">
import type { ColumnDefinition, DotPaths, SortDirection } from '@/interfaces'
import DataHeaderCell from './DataHeaderCell.vue'
import DataHeaderCellFilter from './DataHeaderCellFilter.vue'
import { computed, ref, watch } from 'vue'

const props = withDefaults(defineProps<{
    selectableRows?: boolean
    selectableRowsCheckboxes?: boolean
    header: ColumnDefinition<TRowData>[]
    actionsOnLeft?: boolean
    actions: boolean
    sort?: DotPaths<TRowData> | null
    sortDirection?: SortDirection
    filter?: Record<string, string>
    i18n: Record<string, string>
    selectedCount?: number
}>(), {
    selectableRows: false,
    selectableRowsCheckboxes: false,
    actionsOnLeft: false,
    sort: null,
    sortDirection: null,
    filter: () => ({}),
    selectedCount: 0
})

const selectAll = ref(false)

const selectAllState = computed({
    get() {
        return selectAll.value
    },
    set(value) {
        selectAll.value = value
        if (value) {
            emit('selectAll')
        } else {
            emit('selectNone')
        }
    }
})

watch(computed(() => props.selectedCount), (value) => {
    if (value === 0) {
        selectAllState.value = false
    }
})

const emit = defineEmits<{
    filter: [filter: Record<string, string>]
    sort: [dataField: DotPaths<TRowData>]
    selectAll: []
    selectNone: []
}>()

function onFilter(data: Record<string, string>): void {
    emit('filter', data)
}

function onSort(data: DotPaths<TRowData>): void {
    emit('sort', data)
}
</script>
