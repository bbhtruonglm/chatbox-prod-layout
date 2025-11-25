<template>
  <div
    id="chat__message-reaction"
    class="text-xxs text-slate-500 absolute group w-max z-20 -bottom-4"
    :class="sender_id === fb_page_id ? 'right-0' : 'left-0'"
  >
    <!-- Trigger icon -->
    <span
      class="flex items-center justify-center cursor-pointer text-base rounded-full relative opacity-0 group-hover:opacity-100 transition-opacity duration-150"
      :class="[
        selected_reaction
          ? 'px-3 py-0.5 bg-white border border-slate-200 rounded-full opacity-100'
          : 'px-3 py-1 bg-white border border-slate-200 rounded-full',
      ]"
      @mouseenter="openReactions"
      @mouseleave="closeReactions"
      ref="trigger_icon"
    >
      <!-- Nếu đã chọn reaction, hiện emoji UTF-8, ngược lại Lucide icon -->
      <span
        v-if="selected_reaction"
        class="text-sm flex items-center justify-center gap-1"
      >
        {{ selected_reaction.map(r => r.icon).join(' ') }}
      </span>
      <ThumbsUpIcon
        v-else
        class="size-3 text-blue-500"
      />

      <!-- Reaction popup: hiện khi hover trigger icon -->
      <div
        v-if="is_open"
        ref="reactionPopup"
        class="absolute z-50 bg-white border border-slate-200 rounded-full shadow-lg p-0.5 flex gap-1 transition-all duration-150"
        :style="popup_style"
        @mouseenter="hovering = true"
        @mouseleave="closeReactions"
      >
        <span
          v-for="reaction in MAIN_REACTIONS"
          :key="reaction.code"
          class="cursor-pointer hover:scale-125 transition-transform size-7 rounded-full flex items-center justify-center text-xl"
          @click="selectReaction(reaction)"
        >
          {{ reaction.icon }}
        </span>
      </div>
    </span>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import { ThumbsUpIcon } from 'lucide-vue-next'

/** Trạng thái mở popup reaction */
const is_open = ref(false)

/** Trạng thái hover lên popup (để tránh đóng ngoài ý muốn) */
const hovering = ref(false)

/** Style position của popup (cập nhật theo icon trigger) */
const popup_style = ref({} as any)

/** Ref tới icon để tính vị trí hiển thị popup */
const trigger_icon = ref<HTMLElement | null>(null)

/**
 * Danh sách reaction user đã chọn:
 * - null → chưa chọn
 * - [] → đã chọn nhưng bị remove
 * - array Reaction → chọn nhiều reaction
 */
const selected_reaction = ref<{ code: string; icon: string }[] | null>(null)

/** Kiểu Reaction */
interface Reaction {
  code: string
  icon: string
  color: string
}

/**
 * 6 reaction mặc định
 * code: text gửi lên API
 * icon: emoji hiển thị
 */
const MAIN_REACTIONS: Reaction[] = [
  { code: '/-strong', icon: '👍', color: '#ffffff' },
  { code: '/-heart', icon: '❤️', color: '#ffffff' },
  { code: ':>', icon: '😆', color: '#ffffff' },
  { code: ':o', icon: '😮', color: '#ffffff' },
  { code: '/-bome', icon: '😢', color: '#ffffff' },
  { code: ':-h', icon: '😡', color: '#ffffff' },
]

/**
 * Mở popup → đồng thời cập nhật vị trí theo icon
 */
function openReactions() {
  is_open.value = true
  updatePopupPosition()
}

/**
 * Đóng popup:
 * - delay 100ms để k bị tắt khi rê chuột nhanh
 * - chỉ đóng nếu user không hover vào popup
 */
function closeReactions() {
  setTimeout(() => {
    if (!hovering.value) {
      is_open.value = false
    }
  }, 100)

  hovering.value = false
}

/**
 * Cập nhật vị trí của popup theo vị trí icon
 * - dùng getBoundingClientRect để lấy toạ độ icon
 * - giới hạn không vượt ra ngoài màn hình
 */
function updatePopupPosition() {
  if (trigger_icon.value) {
    const rect = trigger_icon.value.getBoundingClientRect()

    const top = rect.top - 35
    const left = Math.min(window.innerWidth - 220, Math.max(10, rect.left - 20))

    popup_style.value = {
      top: `${top}px`,
      left: `${left}px`,
      position: 'fixed',
    }
  }
}

/**
 * User click 1 reaction:
 * - Nếu chưa chọn → thêm
 * - Nếu chọn rồi → gỡ
 * - Nếu gỡ hết → reset về null
 */
function selectReaction(reaction: Reaction) {
  if (!selected_reaction.value) {
    selected_reaction.value = [reaction]
  } else {
    const index = selected_reaction.value.findIndex(
      r => r.code === reaction.code
    )

    if (index === -1) {
      selected_reaction.value.push(reaction)
    } else {
      selected_reaction.value.splice(index, 1)

      if (selected_reaction.value.length === 0) {
        selected_reaction.value = null
      }
    }
  }

  console.log('Selected reactions:', selected_reaction.value)

  /** Chọn xong thì đóng popup */
  is_open.value = false
}

/** Props: dùng để biết sender là ai, đang ở page nào */
const $props = withDefaults(
  defineProps<{
    sender_id?: string
    fb_page_id?: string
  }>(),
  {}
)
</script>

<style scoped>
/* Optional: transition mềm cho icon trigger */
</style>
