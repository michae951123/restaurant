<script setup>
defineProps({
  item: {
    type: Object,
    required: true
  }
})
defineEmits(['select'])
</script>

<template>
  <div class="bg-white rounded-3xl border border-orange-100/80 overflow-hidden shadow-xs hover:shadow-xl transition-all duration-300 flex flex-col justify-between group hover:-translate-y-1">
    
    <div>
      <!-- 採用溫潤漸層色塊 + 大 Emoji 圖示 -->
      <div class="relative h-40 w-full bg-gradient-to-br from-orange-100 via-amber-50 to-rose-100 flex items-center justify-center overflow-hidden">
        <span class="text-6xl group-hover:scale-110 transition-transform duration-300 select-none">
          {{ item.icon || '🍜' }}
        </span>
        
        <!-- 推薦標籤 -->
        <span 
          v-if="item.tag" 
          class="absolute top-3 left-3 bg-rose-500/90 backdrop-blur-md text-white text-xs font-bold px-3 py-1 rounded-full shadow-md">
          🔥 {{ item.tag }}
        </span>
      </div>

      <!-- 餐點內容區 -->
      <div class="p-4">
        <div class="flex justify-between items-start mb-1.5">
          <h3 class="font-bold text-lg text-gray-800 group-hover:text-rose-600 transition-colors">
            {{ item.name }}
          </h3>
          <div class="flex items-baseline space-x-0.5">
            <span class="text-xs font-bold text-rose-600">NT$</span>
            <span class="text-xl font-extrabold text-rose-600 tracking-tight">{{ item.price }}</span>
          </div>
        </div>
        <p class="text-gray-500 text-xs leading-relaxed line-clamp-2">
          {{ item.description }}
        </p>
      </div>
    </div>

    <!-- 選擇規格按鈕 -->
    <div class="px-4 pb-4 pt-1">
      <button 
        @click="$emit('select', item)"
        class="w-full py-2.5 bg-gradient-to-r from-orange-500 to-rose-500 hover:from-orange-600 hover:to-rose-600 text-white font-bold rounded-2xl shadow-md shadow-orange-500/20 active:scale-95 transition-all text-sm flex items-center justify-center space-x-1">
        <span>選擇規格</span>
        <span class="text-xs">➔</span>
      </button>
    </div>

  </div>
</template>