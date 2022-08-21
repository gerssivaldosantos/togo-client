<template>
  <q-dialog
    ref="dialogRef"
    persistent
    :maximized="maximizedToggle"
    transition-show="slide-up"
    transition-hide="slide-down"
    @hide="onDialogHide"
  >
    <q-card class="q-dialog-plugin" style="width: 100%">
    <q-bar>
      <q-space />

      <q-btn
        dense
        flat
        icon="minimize"
        @click="maximizedToggle = false"
        :disable="!maximizedToggle"
      >
        <q-tooltip v-if="maximizedToggle" class="bg-white text-primary"
          >Minimize</q-tooltip
        >
      </q-btn>
      <q-btn
        dense
        flat
        icon="crop_square"
        @click="maximizedToggle = true"
        :disable="maximizedToggle"
      >
        <q-tooltip v-if="!maximizedToggle" class="bg-white text-primary"
          >Maximize</q-tooltip
        >
      </q-btn>
      <q-btn dense flat icon="close" v-close-popup>
        <q-tooltip class="bg-white text-primary">Close</q-tooltip>
      </q-btn>
    </q-bar>
      <div id="issue-dialog-body"></div>
    </q-card>
  </q-dialog>
</template>

<script setup lang="ts">
import axios from 'axios'
import { onMounted, ref } from 'vue'
import { useQuasar } from 'quasar'

const maximizedToggle = ref(true)

const $q = useQuasar()

const dialogRef = ref()

const props = defineProps({
  url: {
    type: String,
    default: ''
  }
})

onMounted(async () => {
  $q.loading.show()

  const response = (
    await axios({
      method: 'post',
      data: {
        url: props.url
      },
      url: `${process.env.API}/issue`,
      responseType: 'json'
    })
  ).data
  const wrapperEl = document.querySelector('#issue-dialog-body')
  if (wrapperEl) wrapperEl.innerHTML = response.body
  $q.loading.hide()
})

const onDialogHide = () => {
  dialogRef.value.hide()
}
</script>

<style lang="scss" scoped>
#issue-dialog-body {
  display: flex;
  flex-direction: column;
  flex-wrap: wrap;
  width: 100%;
  padding: 30px;
}
</style>
