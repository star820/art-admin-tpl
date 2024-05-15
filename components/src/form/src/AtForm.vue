<script setup lang="ts">
import { cloneDeep, isArray, mergeWith, omit } from 'lodash-unified'
import { type FormInst, NForm, NFormItem, NFormItemGi, NGrid, NGridItem } from 'naive-ui'
import { computed, reactive, shallowRef } from 'vue'
import { EXTRA_FORM_ITEM_PROPS } from './utils'
import type { AtFormProps } from './types'
import { FORM_FIELDS } from './fields/fields'

defineOptions({
  name: 'AtForm',
})
const props = withDefaults(defineProps<AtFormProps>(), {
  scrollToFirstError: true,
  layout() {
    return { xGap: 16 }
  },
})

const finalConfigs = computed(() => {
  return props.configs.map((item) => {
    return {
      ...item,
      path: item.field,
    }
  })
})

const formRef = shallowRef<FormInst>()

async function validate(...args: any[]) {
  try {
    await formRef.value?.validate(...args)
    return Promise.resolve()
  }
  catch (error: any) {
    if (Array.isArray(error) && props.scrollToFirstError) {
      const t = document.querySelector(`[target="${error[0][0].field}"]`)
      t?.scrollIntoView({ block: 'center', behavior: 'smooth' })
    }
    return Promise.reject(error)
  }
}

async function setValue(newValue: any) {
  mergeWith(props.model, newValue, (oldVal, newVal) => {
    // 重置对象数组类型的表单需要对齐数组的长度
    if (isArray(oldVal) && isArray(newVal) && oldVal.length > newVal.length)
      oldVal.splice(newVal.length - oldVal.length, Number.POSITIVE_INFINITY)
  })
}

const cloneInitVal = cloneDeep(props.model)
async function resetValue() {
  setValue(props.initValue ?? cloneInitVal)
  restoreValidation()
}

function restoreValidation() {
  formRef.value?.restoreValidation()
}

function isString(thing: any) {
  return typeof thing === 'string'
}
defineExpose(
  reactive({
    validate,
    resetValue,
    setValue,
    restoreValidation,
    getEl() { return (formRef.value as any).$el },
  }),
)
</script>

<template>
  <NForm ref="formRef" :model="model" v-bind="props.nFormProps">
    <!-- for search component -->
    <NGrid v-if="!props.nFormProps?.inline" v-bind="layout">
      <template v-for="config in finalConfigs" :key="config.field">
        <template v-if="config.type !== 'titleBar'">
          <NFormItemGi
            v-if="!config.hide"
            v-bind="omit(config, EXTRA_FORM_ITEM_PROPS)"
            :span="config.span ?? 24"
            :path="config.field"
            :target="config.field"
          >
            <template v-if="config.label" #label>
              <div flex items-center gap4>
                <span v-if="isString(config.label)">{{ config.label }}</span>
                <Component :is="config.label" v-else />
              </div>
            </template>
            <Component :is="FORM_FIELDS[config.type]" :item="config" :model="model" />
          </NFormItemGi>
        </template>
        <NGridItem v-else :span="24">
          <div
            class="text-16px font-bold mb4 relative flex items-center gap2 flex-auto rounded-base of-hidden bg-gray/20"
            :class="titleBarCls"
          >
            <span class="h9 w1 bg-primary" />
            <span v-if="isString(config.label)">{{ config.label }}</span>
            <Component :is="config.label" v-else />
          </div>
        </NGridItem>
      </template>
    </NGrid>
    <!-- normal form -->
    <template v-for="config in finalConfigs" v-else :key="config.field">
      <NFormItem
        v-if="!config.hide"
        v-bind="omit(config, EXTRA_FORM_ITEM_PROPS)"
        :span="config.span ?? 24"
        :path="config.field"
        :target="config.field"
      >
        <template v-if="config.label" #label>
          <div flex items-center gap1>
            <span v-if="isString(config.label)">{{ config.label }}</span>
            <Component :is="config.label" v-else />
          </div>
        </template>
        <Component :is="FORM_FIELDS[config.type]" :item="config" :model="model" />
      </NFormItem>
    </template>
    <slot name="search" />
  </NForm>
</template>