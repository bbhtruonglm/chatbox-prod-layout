<template>
  <!-- Icon hiển thị trạng thái online/offline của user -->
  <span
    v-tooltip.bottom="is_online ? $t('Đang online') : $t('Không online')"
    class="inline-flex items-center justify-center flex-shrink-0"
  >
    <!-- Chấm tròn biểu thị trạng thái -->
    <span
      class="rounded-full"
      :class="[size_class, is_online ? 'bg-green-500' : 'bg-gray-400']"
    ></span>
  </span>
</template>

<script setup lang="ts">
import { computed } from 'vue'

/** Props của component */
const $props = withDefaults(
  defineProps<{
    /** Trạng thái online hay không - undefined/false = offline */
    is_online?: boolean
    /** Kích thước của icon: 'sm' | 'md' | 'lg' */
    size?: 'sm' | 'md' | 'lg'
  }>(),
  {
    is_online: false,
    size: 'sm',
  }
)

/** Tính toán class cho size dùng Tailwind */
const size_class = computed(() => {
  /** Mapping size với Tailwind class tương ứng */
  const SIZE_MAP: Record<string, string> = {
    /** size-2 = 8px - dùng cho list conversation */
    sm: 'size-2',
    /** size-3 = 12px - dùng cho header */
    md: 'size-3',
    /** size-4 = 16px - dự phòng */
    lg: 'size-4',
  }
  return SIZE_MAP[$props.size] || 'size-2'
})
</script>
