<template>
  <div
    class="relative border border-gray-300 rounded-md px-3 py-1 shadow-sm focus-within:ring-1"
    :class="[
      this.$attrs.disabled ? 'bg-gray-100' : 'bg-' + bgColor,
      (errors?.length??0) == 0 ? 'focus-within:ring-blue-600 focus-within:border-blue-600' :
      'border-red-300 focus-within:ring-red-600 focus-within:border-red-600'
    ]">
    <label
      :for="$attrs.name??'none'"
      class="absolute -top-2 left-2 -mt-px inline-block px-1 text-xs font-medium text-gray-900"
      :class="['bg-' + bgColor]">
      {{ required ? '(*) ' : '' }} {{ label }}</label>
    <div
      class="w-full pt-1.5 inline-flex text-gray-400"
      :class="[this.$attrs.disabled ? 'bg-gray-100' : 'bg-' + bgColor]">
      <slot name="leftIcon"/>
      <textarea
        type="text"
        :name="this.$attrs.name??'none'"
        v-bind="$attrs"
        v-model="model"
        @input="(e) => $emit('update:modelValue', e.target.value)"
        class="block w-full border-0 p-0 text-gray-900 placeholder-gray-500 focus:ring-0 sm:text-sm text-area-resize-none"
        :class="[$slots.leftIcon ? 'ml-2' : '', this.$attrs.disabled ? 'bg-gray-100' : 'bg-' + bgColor]"
        :placeholder="this.$attrs.placeholder + (!required && !hideOptional ? ' (Opcional)' : '')"
        v-maska="mask"
        v-debounce="debounce"
      />
      <div class="text-right">
        <slot name="rightIcon"/>
      </div>
      <div
        v-if="(errors?.length??0) > 0"
        class="absolute inset-y-0 right-0 pr-3 flex items-center pointer-events-none">
        <ExclamationCircleIcon
          class="h-5 w-5 text-red-500"
          aria-hidden="true"/>
      </div>
    </div>
  </div>
  <p
    v-for="(error, index) in errors"
    :key="index"
    class="ml-3 mt-2 text-sm text-red-600"
    id="email-error">{{ error.$message }}</p>
</template>

<script>
import {
  computed,
  defineComponent,
} from 'vue';

import { maska } from 'maska';

import { ExclamationCircleIcon } from '@heroicons/vue/outline';

export default defineComponent({
  name: 'Input',
  inheritAttrs: false,
  emits: ['update:modelValue'],
  components: {
    ExclamationCircleIcon,
  },
  directives: {
    maska,
  },
  props: {
    modelValue: {
      type: String,
      default: () => '',
    },
    label: {
      type: String,
      default: () => '',
    },
    mask: {
      type: Object,
      default: null,
    },
    bgColor: {
      type: String,
      default: () => 'white',
    },
    required: {
      type: Boolean,
      default: () => false,
    },
    hideOptional: {
      type: Boolean,
      default: () => false,
    },
    errors: {
      type: Array,
      default: () => [],
    },
    debounce: {
      type: Number,
      default: () => 100,
    },
  },
  setup(props, { emit }) {
    const model = computed({
      get: () => props.modelValue,
      set: (val) => emit('update:modelValue', val),
    });

    return {
      model,
    };
  },
});
</script>
