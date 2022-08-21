<template>
  <q-dialog position="bottom" ref="dialogRef" @hide="onDialogHide">
    <q-card class="q-dialog-plugin" style="width: 350px">
      <q-card-actions style="padding: 20px" align="center">
        <q-btn
          v-for="option in buttonsOptions"
          v-bind:key="option.href"
          v-bind="option"
        />
      </q-card-actions>
    </q-card>
  </q-dialog>
</template>

<script setup lang="ts">
import { ref } from 'vue'
const props = defineProps({
  url: {
    type: String,
    default: ''
  }
})

const dialogRef = ref()

const buttonsOptions = ref([
  {
    href: `https://www.linkedin.com/shareArticle?mini:true&url:${props.url}`,
    target: '_blank',
    flat: true,
    round: true,
    icon: 'fa-brands fa-linkedin',
    size: 'medium'
  },
  {
    href: `https://telegram.me/share/url?url=${props.url}`,
    icon: 'fa-brands fa-telegram',
    target: '_blank',
    flat: true,
    round: true,
    size: 'medium'
  },
  {
    href: `whatsapp://send?text=${props.url}`,
    icon: 'fa-brands fa-whatsapp',
    target: '_blank',
    flat: true,
    round: true,
    size: 'medium'
  },
  {
    href: `https://www.facebook.com/sharer/sharer.php?u=${props.url}`,
    icon: 'fa-brands fa-facebook',
    target: '_blank',
    flat: true,
    round: true,
    size: 'medium'
  }
])

const onDialogHide = () => {
  dialogRef.value.hide()
}
</script>

<style lang="scss" scoped></style>
