<script setup>
import { ref } from 'vue'

const props = defineProps({
  item: {
    type: Object,
    required: true
  }
})

const emit = defineEmits(['add-to-cart', 'close'])

// 預設選項狀態
const selectedPortion = ref('正常')
const selectedNoodle = ref('細麵')
const selectedSpicy = ref('不辣')
const customNote = ref('') // 客製化備註

function handleConfirm() {
  emit('add-to-cart', {
    ...props.item,
    options: {
      portion: selectedPortion.value,
      noodle: selectedNoodle.value,
      spicy: selectedSpicy.value,
      note: customNote.value.trim()
    }
  })
}
</script>

<template>
  <div class="fixed inset-0 bg-black/50 flex items-center justify-center z-50 p-4">
    <div class="bg-white w-full max-w-md rounded-3xl p-6 shadow-xl animate-scale-up">
      <div class="flex justify-between items-center mb-4">
        <h2 class="text-xl font-bold text-gray-800">{{ item.name }} - 規格選擇</h2>
        <button @click="$emit('close')" class="text-gray-400 hover:text-gray-600 text-xl font-bold px-2">✕</button>
      </div>

      <div class="space-y-4 mb-6">
        <!-- 份量選擇 -->
        <div>
          <label class="block text-sm font-semibold text-gray-700 mb-2">份量大小</label>
          <div class="grid grid-cols-2 gap-2">
            <button 
              v-for="p in ['正常', '大碗 (+NT$ 20)']" :key="p"
              @click="selectedPortion = p"
              :class="selectedPortion === p ? 'bg-orange-500 text-white shadow-sm' : 'bg-gray-100 text-gray-600 hover:bg-gray-200'"
              class="py-2 rounded-xl text-sm font-medium transition-all">
              {{ p }}
            </button>
          </div>
        </div>

        <!-- 麵條選擇 -->
        <div>
          <label class="block text-sm font-semibold text-gray-700 mb-2">麵條種類</label>
          <div class="grid grid-cols-3 gap-2">
            <button 
              v-for="n in ['細麵', '寬麵', '冬粉']" :key="n"
              @click="selectedNoodle = n"
              :class="selectedNoodle === n ? 'bg-orange-500 text-white shadow-sm' : 'bg-gray-100 text-gray-600 hover:bg-gray-200'"
              class="py-2 rounded-xl text-sm font-medium transition-all">
              {{ n }}
            </button>
          </div>
        </div>

        <!-- 辣度選擇 -->
        <div>
          <label class="block text-sm font-semibold text-gray-700 mb-2">辣度調整</label>
          <div class="grid grid-cols-3 gap-2">
            <button 
              v-for="s in ['不辣', '微辣', '大辣']" :key="s"
              @click="selectedSpicy = s"
              :class="selectedSpicy === s ? 'bg-orange-500 text-white shadow-sm' : 'bg-gray-100 text-gray-600 hover:bg-gray-200'"
              class="py-2 rounded-xl text-sm font-medium transition-all">
              {{ s }}
            </button>
          </div>
        </div>

        <!-- 特殊需求備註 -->
        <div>
          <label class="block text-sm font-semibold text-gray-700 mb-2">特殊需求備註</label>
          <input 
            v-model="customNote"
            type="text" 
            placeholder="例如：去蔥、去香菜、麵硬一點..." 
            class="w-full px-3 py-2 bg-gray-50 border border-gray-200 rounded-xl text-sm focus:outline-none focus:border-orange-500"
          />
        </div>
      </div>

      <div class="flex space-x-3">
        <button @click="$emit('close')" class="w-1/2 py-3 bg-gray-100 hover:bg-gray-200 text-gray-700 font-medium rounded-2xl transition-colors">
          取消
        </button>
        <button @click="handleConfirm" class="w-1/2 py-3 bg-rose-600 hover:bg-rose-700 text-white font-medium rounded-2xl shadow-md transition-colors">
          加入購物車
        </button>
      </div>
    </div>
  </div>
</template>