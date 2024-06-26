<!-- eslint-disable @typescript-eslint/no-non-null-assertion -->
<script setup lang="ts">
import { useResizeObserver } from '@vueuse/core'
import { NButton, NCard, NEl, NFormItem } from 'naive-ui'
import { type CSSProperties, computed, onActivated, onDeactivated, onMounted, reactive, ref, shallowRef } from 'vue'
import type { FormItemConfig } from '../../form/src/types'
import type { AtFormInst } from '../../form'
import { AtForm } from '../../form'
import type { Recordable } from '../../types'

defineOptions({
  name: 'GpSearch',
})

const props = withDefaults(
  defineProps<{
    labelWidth?: string
    loading?: boolean
    minWidth?: string
    configs: FormItemConfig[]
    bgCls?: string
    cardStyle?: CSSProperties
    defaultOpen?: boolean
  }>(),
  {
    labelWidth: '100px',
    minWidth: '320px',
    bgCls: 'bg-base',
    defaultOpen: false,
    cardStyle: () => ({
      padding: '16px',
      paddingRight: 0,
    }),
  },
)

const emit = defineEmits<{
  (e: 'search'): void
}>()

const model = defineModel<Recordable>('model', { required: true })

const defaultHeight = 34
const emptyBoxCount = ref(0)
const $atForm = shallowRef<AtFormInst>()
const isShowToggleBtn = ref(true) // 控制切换按钮显示隐藏
const searchBoxWidth = ref(0) // 控制 searchBar 的宽度：保证能遮挡住一个 form-item
const currentHeight = ref(defaultHeight) // 控制 searchBar 的高度
const controlObserver = ref(false) // 解决组件失活报错的问题
onDeactivated(() => {
  controlObserver.value = true
})
onActivated(() => {
  controlObserver.value = false
})
const totalRealCount = ref(0)
const isOpen = ref(props.defaultOpen)
function toggle() {
  isOpen.value = !isOpen.value
}

onMounted(async () => {
  // 真实的表单控件个数
  const el = $atForm.value?.getEl()
  if (!el)
    return
  totalRealCount.value = (el as HTMLDivElement).childElementCount - 1
  useResizeObserver(el, async (entries) => {
    if (controlObserver.value)
      return
    const { borderBoxSize } = entries[0]!
    if (!borderBoxSize?.[0])
      return
    // 取整，这个 blockSize 可能为小数
    currentHeight.value = Math.round(borderBoxSize![0].blockSize)
    if (currentHeight.value === defaultHeight) {
      // 如果相等说明只有一行，不需要显示『展开』『收起』按钮
      isShowToggleBtn.value = false
    }
    else {
      isShowToggleBtn.value = true
    }
    // 取整，这个 blockSize 可能为小数
    currentHeight.value = Math.round(borderBoxSize![0].blockSize)
    // 拿到每一行允许的最大个数
    const perLineMaxCount = Math.floor(borderBoxSize![0].inlineSize / Number.parseInt(props.minWidth)) || 1
    // 计算得到需要补充的空盒子的数量
    if (totalRealCount.value >= perLineMaxCount) {
      // 有多行
      emptyBoxCount.value = perLineMaxCount - (totalRealCount.value % perLineMaxCount)
    }
    else {
      emptyBoxCount.value = perLineMaxCount - totalRealCount.value
    }
    // 计算得到 searchBar 的宽度
    searchBoxWidth.value = borderBoxSize![0].inlineSize / perLineMaxCount
  })
})

const wrapperStyle = computed<CSSProperties>(() => ({
  overflow: 'hidden',
  padding: '2px', // 防止水波纹看不到
  transition: '0.3s',
  position: 'relative',
  boxSizing: 'content-box',
  height: `${isOpen.value ? currentHeight.value : defaultHeight}px`,
}))
function onSearch() {
  emit('search')
}

function onReset() {
  $atForm.value?.resetValue()
  onSearch()
}

defineExpose(reactive({ $atForm }))
</script>

<template>
  <NEl>
    <NCard :content-style="cardStyle" class="bd-base!" :class="[bgCls]">
      <div :style="wrapperStyle" @keyup.enter="onSearch">
        <AtForm ref="$atForm" class="at-search-form" :n-form-props="{ inline: true, labelPlacement: 'left', labelWidth, labelAlign: 'right', showFeedback: false }" :configs="configs" :model="model">
          <template #search>
            <NFormItem v-for="item in emptyBoxCount" :key="item" />
            <div
              flex="~ items-start justify-end gap-2"
              class="absolute bottom-0 right-0 z-2"
              :class="bgCls"
              :style="{ width: `${searchBoxWidth}px`, height: `${defaultHeight + 2}px`, marginRight: '16px' }"
            >
              <NButton secondary type="tertiary" :loading="props.loading" @click="onReset">
                重置
              </NButton>
              <NButton type="primary" :loading="props.loading" @click="onSearch">
                查询
              </NButton>
              <NButton v-if="isShowToggleBtn" @click="toggle">
                {{ isOpen ? "收起" : "展开" }}
              </NButton>
            </div>
          </template>
        </AtForm>
      </div>
    </NCard>
  </NEl>
</template>

<style lang="scss">
.at-search-form {
    flex-wrap: wrap;
    row-gap: 16px;

    .n-form-item {
        box-sizing: border-box;
        flex: 1;
        min-width: v-bind("props.minWidth");
        margin-right: 0 !important;
        padding-right: 16px !important;
    }

    .n-date-picker {
        width: 100%;
    }
}
</style>
