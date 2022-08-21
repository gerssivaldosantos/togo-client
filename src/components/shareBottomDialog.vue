<template>
  <q-dialog position="bottom" ref="dialogRef" @hide="onDialogHide">
    <q-card class="q-dialog-plugin" style="width: 350px">
      <q-card-actions align="center">
        <q-btn
          :href="`https://www.linkedin.com/shareArticle?mini=true&url=${props.url}`"
          target="_blank"
          flat
          round
          icon="fa-brands fa-linkedin"
        />
        <q-btn
        :href="`https://telegram.me/share/url?url=${props.url}`"
        target="_blank"
        flat
        round
        icon="fa-brands fa-telegram"
        />
        <q-btn
          :href="`whatsapp://send?text=${props.url}`"
          target="_blank"
          data-action="share/whatsapp/share"
          flat
          round
          icon="fa-brands fa-whatsapp"
        />
        <q-btn
          :href="`https://www.facebook.com/sharer/sharer.php?u=${props.url}`"
          target="_blank"
          flat
          round
          icon="fa-brands fa-facebook"
        />
      </q-card-actions>
    </q-card>
  </q-dialog>
</template>

<script setup lang="ts">
import { ref } from 'vue'

const dialogRef = ref()

const onDialogHide = () => {
  dialogRef.value.hide()
}

const props = defineProps({
  url: {
    type: String,
    default: ''
  }
})
</script>
