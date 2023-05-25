<script setup lang="ts">
import Header from '@/layouts/Header.vue'
import Footer from '@/layouts/Footer.vue'
import Sider from '@/layouts/Sider.vue'
import Container from '@/layouts/Container.vue'
import VueColorAvatar, { type VueColorAvatarRef } from '@/components/VueColorAvatar.vue'
import Configurator from '@/components/Configurator.vue'
import ActionBar from '@/components/ActionBar.vue'
import { useI18n } from 'vue-i18n'
import { ref } from 'vue'
import { useAvatarOption } from './hooks'
import { getRandomAvatarOption } from './utils'
import { ActionType } from './enums'
import { useStore } from './store'
import { UNDO, REDO } from './store/mutation-type'
import { recordEvent } from './utils/ga'
import { DOWNLOAD_DELAY, NOT_COMPATIBLE_AGENTS } from './utils/constant'
import { name as appName } from '../package.json'

const { t } = useI18n()
const [avatarOption, setAvatarOption] = useAvatarOption()
const store = useStore()

const colorAvatarRef = ref<VueColorAvatarRef>()
const flipped = ref(false)

const handleAction = (actionType: ActionType) => {
  if (actionType === ActionType.Flip) {
    flipped.value = !flipped.value
  }

  if (actionType === ActionType.Undo) {
    store[UNDO]()
  }

  if (actionType === ActionType.Redo) {
    store[REDO]()
  }
}

const handleRandomAction = () => {
  const item = getRandomAvatarOption()
  setAvatarOption(item)
}

const downloadModalVisible = ref(false)
const downloading = ref(false)
const imageDataURL = ref('')

async function handleDownload() {
  try {
    downloading.value = true
    const avatarEle = colorAvatarRef.value?.avatarRef

    const userAgent = window.navigator.userAgent.toLowerCase()
    const notCompatible = NOT_COMPATIBLE_AGENTS.some((agent) => userAgent.indexOf(agent) !== -1)

    if (avatarEle) {
      const html2canvas = (await import('html2canvas')).default
      const canvas = await html2canvas(avatarEle, {
        backgroundColor: null
      })
      const dataURL = canvas.toDataURL()

      if (notCompatible) {
        imageDataURL.value = dataURL
        downloadModalVisible.value = true
      } else {
        const trigger = document.createElement('a')
        trigger.href = dataURL
        trigger.download = `${appName}.png`
        trigger.click()
      }
    }

    recordEvent('click_download', {
      event_category: 'click'
    })
  } finally {
    setTimeout(() => {
      downloading.value = false
    }, DOWNLOAD_DELAY)
  }
}
</script>

<template>
  <main>
    <Container>
      <div class="content-warpper">
        <div class="content-view">
          <Header />
          <div class="playground">
            <div class="avatar-wrapper">
              <VueColorAvatar
                ref="colorAvatarRef"
                :size="280"
                :style="{
                  transform: `rotateY(${flipped ? -180 : 0}deg)`
                }"
              />
            </div>

            <ActionBar @action="handleAction" />
            <div class="action-group">
              <button type="button" class="action-btn action-randomize" @click="handleRandomAction">
                {{ t('action.randomize') }}
              </button>

              <button
                type="button"
                class="action-btn action-download"
                :disabled="downloading"
                @click="handleDownload"
              >
                {{ t('action.download') }}
              </button>

              <button type="button" class="action-btn action-multiple">
                {{ t('action.downloadMultiple') }}
              </button>
            </div>
          </div>
          <Footer />
        </div>
      </div>
    </Container>
    <Sider>
      <Configurator />
    </Sider>
  </main>
</template>

<style lang="scss" scoped>
@use 'src/styles/var';

main {
  width: 100%;
  height: 100%;
  overflow: hidden;
  color: var.$color-text;
  background-color: var.$color-page-bg;

  .content-warpper {
    height: 100%;
    transform: scale(1);

    .content-view {
      position: relative;
      z-index: 110;
      display: flex;
      flex-direction: column;
      height: 100%;
      overflow-y: auto;
    }
  }
}

.playground {
  display: flex;
  flex: 1;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  padding: 2rem 0;

  .avatar-wrapper {
    display: flex;
    align-items: center;
    justify-content: center;

    @media screen and (max-width: var.$screen-sm) {
      transform: scale(0.85);
    }
  }

  .action-group {
    display: flex;
    align-items: center;
    justify-content: center;
    margin-top: 4rem;
    column-gap: 1rem;

    @supports not (column-gap: 1rem) {
      .action-btn {
        margin: 0 0.5rem;
      }
    }

    .action-btn {
      min-width: 6rem;
      height: 2.5rem;
      padding: 0 1rem;
      color: var.$color-text;
      font-weight: bold;
      background: var.$color-gray;
      border-radius: 0.6rem;
      cursor: pointer;
      transition: color 0.2s;
      user-select: none;

      &:hover {
        color: lighten(var.$color-text, 10);
      }

      &:disabled,
      &[disabled] {
        color: rgba(var.$color-text, 0.5);
        cursor: default;
      }
    }

    @media screen and (max-width: var.$screen-sm) {
      .action-multiple {
        display: none;
      }
    }
  }
}

@supports (filter: blur(4rem)) or (-webkit-filter: blur(4rem)) or (-moz-filter: blur(4rem)) {
  .gradient-bg {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;

    @mixin gradient-style($color) {
      position: absolute;
      width: 100vh;
      height: 100vh;
      background-image: radial-gradient(
        rgba($color, 0.8) 20%,
        rgba($color, 0.6) 40%,
        rgba($color, 0.4) 60%,
        rgba($color, 0.2) 80%,
        transparent 100%
      );
      border-radius: 50%;
      opacity: 0.2;
      filter: blur(4rem);
    }

    .gradient-top {
      @include gradient-style(var.$color-secondary);

      top: -50%;
      right: -20%;
    }

    .gradient-bottom {
      @include gradient-style(var.$color-accent);

      bottom: -50%;
      left: -20%;
    }
  }
}
</style>
