<script lang="ts" setup>
import { useI18n } from 'vue-i18n'
import SectionWrapper from '@/components/SectionWrapper.vue'

const { t } = useI18n()
</script>

<template>
  <div><SectionWrapper :title="t('label.wrapperShape')">123 </SectionWrapper></div>
</template>
