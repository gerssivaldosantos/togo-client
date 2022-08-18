<template>
  <q-page class="row items-center justify-evenly">
    <q-table style="min-width: 300px; max-width: 1024px" :dense="$q.screen.lt.md" :columns="columns" :rows="data"
      row-key="link" />
  </q-page>
</template>

<script setup lang="ts">
import axios from 'axios'
import { QTableColumn } from 'quasar'
import { onMounted, ref } from 'vue'
import { UrlEnum } from '../enums/urls'

type vacation = {
  title: string
  link: string,
  time: string,
  keywords: Array<string>,
  owner: string
}

const columns: QTableColumn[] = [
  {
    name: 'title',
    required: true,
    label: 'Title',
    align: 'left',
    field: 'title',
    sortable: true
  },
  {
    name: 'link',
    required: true,
    label: 'Created at',
    align: 'left',
    field: 'time',
    sortable: true
  },
  {
    name: 'keywords',
    required: true,
    label: 'Tags',
    align: 'left',
    field: (row: vacation) => row.keywords.join(', '),
    sortable: true
  }
]

onMounted(async () => await fillTable())

const fillTable = async () => {
  const response = await getData()
  data.value = response.vacations
}

const getData = async () => {
  return (await axios({
    method: 'post',
    data: {
      url: UrlEnum.BACKEND_VAGAS_GITHUB_URL,
      keywords: ['node'],
      actualPage: 1
    },
    url: `${process.env.API}/issue-page`,
    responseType: 'json'
  })).data
}

const data = ref<Record<string, unknown>[]>([])

</script>
