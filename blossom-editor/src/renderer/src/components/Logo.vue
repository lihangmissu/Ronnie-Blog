<template>
  <div class="logo-root">
    <div class="logo-img">
      <img :src="collapse_logo" class="logo" />
    </div>
    <div class="project-name">奥沙利文行</div>
  </div>
</template>

<script setup lang="ts">
import { ref, watch } from 'vue'
import { useDark } from '@vueuse/core'
import { getPrimaryColor } from '@renderer/scripts/global-theme'

const collapse_logo = ref(new URL(`../../../../resources/imgs/it.png`, import.meta.url).href)
const isDark = useDark()
const primaryColor = ref(getPrimaryColor().color2)

const props = defineProps({
  name: {
    type: Boolean,
    default: false
  }
})

watch(
  () => isDark.value,
  () => {
    primaryColor.value = getPrimaryColor().color2
  }
)
</script>

<style scoped lang="scss">
.logo-root {
  @include flex(column, flex-start, flex-start);
  @include box(100%, 80px);

  .logo-img {
    @include box(60px, 58px);
    filter: var(--bl-drop-shadow-star);
    padding: 5px;
    margin-bottom: 5px;
    img {
      width: 50px;
      height: 50px;
    }
  }

  .project-name {
    @include font(12px, 700);
    text-shadow: var(--bl-text-shadow);
    width: 100%;
    color: var(--el-color-primary);
    text-align: center;
    letter-spacing: 0;
  }
}
</style>
