<template>
  <q-page style="padding: 20px" class="column items-center justify-evenly">
    <div class="header">
      <div class="search-wrapper">
        <q-select
          @update:model-value="(value: repositorieSelect) => value ? fillTable(value.request) : null"
          outlined
          v-model="repositorie"
          clearable
          :options="repositories"
          option-value="request"
          option-label="label"
          label="Repositories"
        />
        <q-input outlined v-model="rawKeywords" label="Keywords" />
        <q-btn
          @click="fillTable(repositorie?.request)"
          color="primary"
          label="Search"
        />
      </div>
      <h6 v-show="!!data" style="color: grey">
        {{ data?.issuesAmount }} Vacations for {{ repositorie?.label }}
      </h6>
    </div>
    <div class="content">
      <q-table
        no-data-label="Select an repository"
        grid
        auto-width
        :dense="$q.screen.lt.md"
        :columns="columns"
        :rows="data?.vacations"
        row-key="link"
      >
        <template v-slot:item="props">
          <q-card class="my-card vacation-card">
            <div class="header">
              <div class="title-wrapper">
                <h6 style="font-size: medium; opacity: 65%">
                  {{
                    props.row.title.length > 100
                      ? props.row.title.substring(0, 100) + '...'
                      : props.row.title
                  }}
                </h6>
              </div>
              <div class="close-button-wrapper">
                <q-btn
                @click="detailIssue(props.row.link)"
                  round
                  color="grey"
                  size="small"
                  flat
                  icon="fa-solid fa-arrow-up-right-from-square"
                />
              </div>
            </div>
            <div class="content">
              <div v-if="!!props.row.keywords.length" class="keywords">
                <q-chip
                  v-for="keyword in props.row.keywords"
                  v-bind:key="keyword"
                  color="primary"
                  text-color="white"
                >
                  <span>
                    {{ keyword }}
                  </span>
                </q-chip>
              </div>
              <div v-else class="no-keywords">
                <q-chip outline icon="warning" color="primary">
                  without keywords
                </q-chip>
              </div>
            </div>
            <div class="bottom">
              {{ new Date(props.row.time).toLocaleDateString('en') }}
              <q-card-actions align="right">
                <q-btn flat round color="primary" icon="fa-regular fa-heart" />
                <q-btn
                  flat
                  round
                  color="primary"
                  icon="fa-regular fa-bookmark"
                />
                <q-btn
                  @click="shareVacation(props.row.link)"
                  flat
                  round
                  color="teal"
                  icon="share"
                />
              </q-card-actions>
            </div>
          </q-card>
        </template>
      </q-table>
    </div>
  </q-page>
</template>

<script setup lang="ts">
import axios from 'axios'
import { QTableColumn, useQuasar } from 'quasar'
import issueDetailsVue from 'src/components/issueDetails.vue'
import shareBottomDialogVue from 'src/components/shareBottomDialog.vue'
import { computed, reactive, ref } from 'vue'
import { UrlEnum } from '../enums/urls'

const detailIssue = (url: string) => {
  $q.dialog({
    component: issueDetailsVue,
    componentProps: {
      url
    }
  })
}

const shareVacation = (url: string) => {
  $q.dialog({
    component: shareBottomDialogVue,
    componentProps: {
      url
    }
  })
    .onOk(() => null)
    .onCancel(() => null)
    .onDismiss(() => null)
}

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
    format: (value: string) => new Date(value).toLocaleDateString('en-US'),
    required: true,
    label: 'Release',
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

<style scoped lang="scss">
.header {
  width: 100%;
}
.search-wrapper {
  display: flex;
  flex-direction: column;
  width: 100%;
  justify-content: space-between;
  align-items: flex-end;
  padding-top: 20px;
}

.search-wrapper > * {
  width: 100%;
  margin-bottom: 20px;
}

.vacation-card {
  display: flex;
  flex-direction: column;
  align-items: flex-end;
  margin-bottom: 20px;
  width: 100%;
  padding: 20px;
}

.vacation-card > .keywords > .header {
  display: flex;
  flex-direction: row;
  align-items: center;
  justify-content: space-between;
  width: 100%;
}

.vacation-card > .content {
  width: 100%;
  display: flex;
  justify-items: flex-start;
  flex-wrap: wrap;
  justify-content: flex-start;
  flex-direction: row;
}

.vacation-card > .header {
  display: flex;
  flex-direction: row;
  align-items: start;
  justify-content: space-between;
  width: 100%;
}

.vacation-card > .header > .close-button-wrapper {
  width: fit-content;
}

.vacation-card > .header .title-wrapper {
  width: 100%;
}

.vacation-card > .bottom {
  width: 100%;
  display: flex;
  justify-content: space-between;
  align-items: center;
}
</style>
