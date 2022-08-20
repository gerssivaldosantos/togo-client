<template>
  <q-page style="padding: 20px" class="column items-center justify-evenly">
    <div class="header">
      <q-select
        style="width: 100%"
        @update:model-value="(value: repositorieSelect) => value ? fillTable(value.request) : null"
        outlined
        v-model="repositorie"
        clearable
        :options="repositories"
        option-value="request"
        option-label="label"
        label="Repositories"
      />
      <div class="search-wrapper">
        <q-input outlined v-model="rawKeywords" label="Keywords" />
        <q-btn @click="fillTable(repositorie?.request)" color="primary" label="Search" />
      </div>
      <h6 v-show="!!data" style="color: grey">
        {{ data?.issuesAmount }} Vacations for {{ repositorie?.label }}
      </h6>
    </div>
    <q-table
      no-data-label="Select an repository"
      grid
      auto-width
      :dense="$q.screen.lt.md"
      :columns="columns"
      :rows="data?.vacations"
      row-key="link"
    />
  </q-page>
</template>

<script setup lang="ts">
import axios from 'axios'
import { QTableColumn, useQuasar } from 'quasar'
import { computed, reactive, ref } from 'vue'
import { UrlEnum } from '../enums/urls'

const $q = useQuasar()

type issueRow = {
  title: string
  link: string
  time: string
  keywords: Array<string>
  owner: string
}
type issuePage = {
  url: string
  issuesAmount: number
  actualPage: number
  pages: number
  vacations: issueRow[]
}

type requestScrapPage = {
  url: string
  keywords: string[]
  actualPage?: number
}

type repositorieSelect = {
  request: requestScrapPage
  label: string
}

const repositorie = ref<repositorieSelect>()

const rawKeywords = ref<string>('')

const keywords = computed(() =>
  rawKeywords.value.split(',').map((keyword) => keyword.trim())
)

const actualPage = ref<number>(1)

const repositories = reactive<repositorieSelect[]>([
  {
    label: 'Backend',
    request: {
      url: UrlEnum.BACKEND_VAGAS_GITHUB_URL,
      keywords: [],
      actualPage: actualPage.value
    }
  },
  {
    label: 'Frontend',
    request: {
      url: UrlEnum.FRONTEND_VAGAS_GITHUB_URL,
      keywords: [],
      actualPage: actualPage.value
    }
  },
  {
    label: 'Ux Design',
    request: {
      url: UrlEnum.UXDESIGN_VAGAS_GITHUB_URL,
      keywords: [],
      actualPage: actualPage.value
    }
  },
  {
    label: 'Android',
    request: {
      url: UrlEnum.ANDROID_VAGAS_GITHUB_URL,
      keywords: [],
      actualPage: actualPage.value
    }
  },
  {
    label: 'IOS',
    request: {
      url: UrlEnum.IOS_VAGAS_GITHUB_URL,
      keywords: [],
      actualPage: actualPage.value
    }
  },
  {
    label: 'PHP',
    request: {
      url: UrlEnum.PHP_VAGAS_GITHUB_URL,
      keywords: [],
      actualPage: actualPage.value
    }
  },
  {
    label: 'Vue.Js',
    request: {
      url: UrlEnum.VUEJS_VAGAS_GITHUB_URL,
      keywords: [],
      actualPage: actualPage.value
    }
  },
  {
    label: 'GoLang',
    request: {
      url: UrlEnum.GOLANG_VAGAS_GITHUB_URL,
      keywords: [],
      actualPage: actualPage.value
    }
  },
  {
    label: 'Flutter',
    request: {
      url: UrlEnum.FLUTTER_VAGAS_GITHUB_URL,
      keywords: [],
      actualPage: actualPage.value
    }
  },
  {
    label: 'ReactNative',
    request: {
      url: UrlEnum.REACTNATIVE_VAGAS_GITHUB_URL,
      keywords: [],
      actualPage: actualPage.value
    }
  }
])

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
    field: (row: issueRow) => row.keywords.join(', '),
    sortable: true
  }
]

const fillTable = async (params?: requestScrapPage) => {
  if (!params) {
    return null
  }
  try {
    $q.loading.show({
      delay: 0
    })
    params.keywords = keywords.value
    const result = await doRequest(params)
    data.value = result
  } catch (err) {
    console.error(err)
    $q.notify({ type: 'negative', caption: 'Server Error' })
  }
  $q.loading.hide()
}

const doRequest = async (params?: requestScrapPage): Promise<issuePage> => {
  if (!params) throw new Error('The params are undefined').stack
  return (
    await axios({
      method: 'post',
      data: params,
      url: `${process.env.API}/issue-page`,
      responseType: 'json'
    })
  ).data
}

const data = ref<issuePage>()
</script>

<style lang="scss">
.header {
  width: 100%
}
.search-wrapper {
  display: flex;
  flex-direction: column;
  width: 100%;
  justify-content: space-between;
  align-items: flex-end;
  padding-top: 20px
}

.search-wrapper > * {
  width: 100%;
  margin-bottom: 20px
}

</style>
