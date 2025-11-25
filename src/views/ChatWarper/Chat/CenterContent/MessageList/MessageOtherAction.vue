<template>
  <div
    id="chat__message-other-action"
    class="text-xxs text-slate-500 absolute z-10 h-fit group"
    :class="sender_id === fb_page_id ? '-left-[72px]' : '-right-[72px]'"
    style="bottom: 0"
  >
    <div
      class="flex gap-1 border border-slate-100 items-center top-3 w-fit bg-slate-100 rounded-lg opacity-0 group-hover:opacity-100 cursor-pointer"
    >
      <span
        v-tooltip="t('Trả lời')"
        class="hover:bg-slate-300 rounded-lg p-0.5"
        ><ArrowUturnLeftIcon class="size-4"
      /></span>
      <span
        v-tooltip="t('Chuyển tiếp')"
        class="hover:bg-slate-300 rounded-lg p-0.5"
        ><ArrowUturnRightIcon class="size-4"
      /></span>
      <span class="hover:bg-slate-300 rounded-lg p-0.5"
        ><EllipsisHorizontalIcon class="size-4"
      /></span>
    </div>
  </div>
</template>
<script setup lang="ts">
import { DateHandle } from '@/utils/helper/DateHandle'
import {
  ArrowUturnLeftIcon,
  ArrowUturnRightIcon,
  EllipsisHorizontalIcon,
} from '@heroicons/vue/24/outline'
import { Ellipsis } from 'lucide-vue-next'

import { container } from 'tsyringe'
import { useI18n } from 'vue-i18n'

/** i18n */
const { t } = useI18n()

const $props = withDefaults(
  defineProps<{
    /**thời gian của tin nhắn */
    time?: string
    /**dữ liệu hiển thị thêm, vd tên nv gửi tin nhắn */
    info?: string
    /**dữ liệu đính kèm của tin nhắn */
    meta?: string
    /**tin nhắn này có phải bị sửa không */
    is_edit?: boolean
    /**số thời gian tin nhắn này được rep */
    duration?: string
    /**có hiển thị thời gian tin nhắn không */
    is_show_duration?: boolean
    /** có phải tin nhắn của AI gửi hay không */
    is_ai?: boolean
    /** platform type : loại platform */
    platform_type?: string
    /** sender id : id người gửi */
    sender_id?: string
    /** Tên người gửi nếu có */
    group_client_name?: string
    /** page_id trong tin nhắn */
    fb_page_id?: string
    /** message_type */
    message_type?: string
  }>(),
  {}
)
console.log('$props.sender_id', $props.sender_id)

const $date_handle = container.resolve(DateHandle)
</script>
