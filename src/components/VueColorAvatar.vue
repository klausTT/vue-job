<template>
  <div
    ref="avatarRef"
    class="vue-color-avatar"
    :style="{
      width: `${avatarSize}px`,
      height: `${avatarSize}px`
    }"
    :class="getWrapperShapeClassName()"
  >
    <Background :color="avatarOption.background.color" />

    <div class="avatar-payload" v-html="svgContent" />
  </div>
</template>

<script lang="ts">
export interface VueColorAvatarRef {
  avatarRef: HTMLDivElement
}
</script>

<script lang="ts" setup>
import { ref, toRefs, watchEffect } from 'vue'

import { WidgetType, WrapperShape } from '@/enums'
import type { AvatarOption } from '@/types'
import { getRandomAvatarOption } from '@/utils'
import { AVATAR_LAYER, NONE } from '@/utils/constant'
import { widgetData } from '@/utils/dynamic-data'

import Background from './widgets/Background.vue'

interface VueColorAvatarProps {
  option: AvatarOption
  size?: number
}

const props = withDefaults(defineProps<VueColorAvatarProps>(), {
  option: () => getRandomAvatarOption(),
  size: 280
})

const { option: avatarOption, size: avatarSize } = toRefs(props)

const avatarRef = ref<VueColorAvatarRef['avatarRef']>()

defineExpose({ avatarRef })

function getWrapperShapeClassName() {
  return {
    [WrapperShape.Circle]: avatarOption.value.wrapperShape === WrapperShape.Circle,
    [WrapperShape.Square]: avatarOption.value.wrapperShape === WrapperShape.Square,
    [WrapperShape.Squircle]: avatarOption.value.wrapperShape === WrapperShape.Squircle
  }
}

const svgContent = ref('')
</script>

<style lang="scss" scoped>
.vue-color-avatar {
  position: relative;
  overflow: hidden;

  &.circle {
    border-radius: 50%;
  }

  &.squircle {
    // TODO: Radius should adapt to the avatar size
    border-radius: 25px;
  }

  .avatar-payload {
    position: relative;
    z-index: 2;
    width: 100%;
    height: 100%;
  }
}
</style>
