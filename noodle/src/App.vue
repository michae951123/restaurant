<script setup>
import { ref, computed } from 'vue'
import MenuItemCard from './components/MenuItemCard.vue'
import OptionSelect from './components/OptionSelect.vue'

// 分類與餐點資料
const currentCategory = ref('all')
const menuItems = ref([
  { 
    id: 1, 
    name: '招牌紅燒牛肉麵', 
    price: 160, 
    category: 'noodle', 
    tag: '人氣招牌',
    icon: '🥩',
    description: '熬煮 72 小時的濃郁紅燒湯頭，搭配半筋半肉頂級口感。' 
  },
  { 
    id: 2, 
    name: '清燉牛肉麵', 
    price: 165, 
    category: 'noodle', 
    tag: '老饕首選',
    icon: '🍲',
    description: '嚴選牛大骨清燉，湯頭清甜不膩，肉質軟嫩。' 
  },
  { 
    id: 3, 
    name: '榨菜肉絲麵', 
    price: 90, 
    category: 'noodle', 
    tag: '',
    icon: '🍜',
    description: '古早味經典搭配，爽脆榨菜與手工肉絲的完美交織。' 
  },
  { 
    id: 4, 
    name: '黃金泡菜', 
    price: 45, 
    category: 'side', 
    tag: '開胃必點',
    icon: '🥬',
    description: '獨家配方醃製，酸甜開胃解膩良伴。' 
  },
  { 
    id: 5, 
    name: '手工滷豆干', 
    price: 35, 
    category: 'side', 
    tag: '',
    icon: '🍱',
    description: '老滷汁慢火入味，口感扎實越嚼越香。' 
  },
  { 
    id: 6, 
    name: '古早味紅茶', 
    price: 30, 
    category: 'drink', 
    tag: '解膩冷飲',
    icon: '🥤',
    description: '香甜順口，懷舊的古早茶香。' 
  }
])

// 狀態控制
const selectedItem = ref(null)
const cart = ref([])
const isCartOpen = ref(false)

// 送出訂單後的完成彈窗狀態
const completedOrder = ref(null)

// 過濾分類
const filteredItems = computed(() => {
  if (currentCategory.value === 'all') return menuItems.value
  return menuItems.value.filter(item => item.category === currentCategory.value)
})

// 加入購物車
function addToCart(itemWithOption) {
  const existIndex = cart.value.findIndex(i => 
    i.id === itemWithOption.id && 
    JSON.stringify(i.options) === JSON.stringify(itemWithOption.options)
  )

  if (existIndex > -1) {
    cart.value[existIndex].quantity += 1
  } else {
    cart.value.push({ ...itemWithOption, quantity: 1 })
  }
  selectedItem.value = null
}

// 調整數量
function updateQuantity(index, change) {
  const item = cart.value[index]
  item.quantity += change
  if (item.quantity <= 0) {
    cart.value.splice(index, 1)
  }
}

// 刪除單項
function removeFromCart(index) {
  cart.value.splice(index, 1)
}

// 計算總份數與總金額
const totalCount = computed(() => {
  return cart.value.reduce((sum, item) => sum + item.quantity, 0)
})

const totalPrice = computed(() => {
  return cart.value.reduce((sum, item) => sum + (item.price * item.quantity), 0)
})

// 送出訂單動作
function submitOrder() {
  if (cart.value.length === 0) return

  // 生成訂單資料（包含隨機號碼）
  completedOrder.value = {
    orderNo: 'A-' + Math.floor(100 + Math.random() * 900),
    items: [...cart.value],
    totalPrice: totalPrice.value,
    time: new Date().toLocaleTimeString('zh-TW', { hour: '2-digit', minute: '2-digit' })
  }

  // 重置購物車與關閉側邊欄
  cart.value = []
  isCartOpen.value = false
}
</script>

<template>
  <div class="min-h-screen bg-orange-50/30 text-gray-800 font-sans pb-16">
    <!-- Header 頂部導覽列 -->
    <header class="bg-white border-b border-orange-100 sticky top-0 z-40 shadow-xs">
      <div class="max-w-4xl mx-auto px-4 py-4 flex justify-between items-center">
        <div class="flex items-center space-x-3">
          <div class="w-10 h-10 bg-rose-100 rounded-2xl flex items-center justify-center text-xl">🍜</div>
          <div>
            <h1 class="font-black text-xl text-gray-900 tracking-wide">巷口傳奇麵食館</h1>
            <p class="text-xs text-gray-500">巷弄間的美味傳說 · 匠心手作</p>
          </div>
        </div>
        
        <!-- 購物車按鈕 -->
        <button @click="isCartOpen = true" class="relative p-2.5 bg-orange-100 hover:bg-orange-200 text-orange-700 rounded-2xl transition-colors">
          🛒
          <span v-if="totalCount > 0" class="absolute -top-1 -right-1 bg-rose-600 text-white text-xs w-5 h-5 rounded-full flex items-center justify-center font-bold">
            {{ totalCount }}
          </span>
        </button>
      </div>
    </header>

    <!-- 主要內容區 -->
    <main class="max-w-4xl mx-auto px-4 py-6">
      <!-- 分類標籤列 -->
      <div class="flex space-x-2 overflow-x-auto pb-4 mb-4 no-scrollbar">
        <button 
          v-for="cat in [{id: 'all', name: '全部餐點'}, {id: 'noodle', name: '主食麵類'}, {id: 'side', name: '經典小菜'}, {id: 'drink', name: '湯品飲品'}]" 
          :key="cat.id"
          @click="currentCategory = cat.id"
          :class="currentCategory === cat.id ? 'bg-rose-600 text-white shadow-md' : 'bg-white text-gray-600 hover:bg-orange-100 border border-orange-100'"
          class="px-5 py-2.5 rounded-2xl text-sm font-medium whitespace-nowrap transition-all">
          {{ cat.name }}
        </button>
      </div>

      <!-- 餐點列表格狀排版 -->
      <div class="grid grid-cols-1 sm:grid-cols-2 gap-4">
        <MenuItemCard 
          v-for="item in filteredItems" 
          :key="item.id" 
          :item="item" 
          @select="selectedItem = item" 
        />
      </div>
    </main>

    <!-- 規格選擇彈窗 -->
    <OptionSelect 
      v-if="selectedItem" 
      :item="selectedItem" 
      @add-to-cart="addToCart" 
      @close="selectedItem = null" 
    />

    <!-- 購物車側邊欄 -->
    <div v-if="isCartOpen" class="fixed inset-0 bg-black/50 z-50 flex justify-end">
      <div class="bg-white w-full max-w-md h-full p-6 flex flex-col justify-between shadow-2xl">
        <div>
          <div class="flex justify-between items-center mb-6">
            <h2 class="text-xl font-bold text-gray-800">您的點餐清單</h2>
            <button @click="isCartOpen = false" class="text-gray-400 hover:text-gray-600 text-xl font-bold">✕</button>
          </div>

          <div v-if="cart.length === 0" class="text-center py-20 text-gray-400">
            購物車目前是空的，快去挑選美味餐點吧！
          </div>

          <div v-else class="space-y-3 overflow-y-auto max-h-[60vh]">
            <div v-for="(item, index) in cart" :key="index" class="p-4 bg-orange-50/50 rounded-2xl border border-orange-100">
              <div class="flex justify-between items-start mb-2">
                <div>
                  <h4 class="font-bold text-gray-800">{{ item.name }}</h4>
                  <p class="text-xs text-gray-500 mt-0.5">
                    {{ item.options.portion }} / {{ item.options.noodle }} / {{ item.options.spicy }}
                  </p>
                  <p v-if="item.options.note" class="text-xs text-orange-600 font-medium mt-1">
                    備註：{{ item.options.note }}
                  </p>
                </div>
                <button @click="removeFromCart(index)" class="text-gray-300 hover:text-rose-500 text-xs p-1">
                  🗑️ 刪除
                </button>
              </div>

              <!-- 數量控制加減鍵 -->
              <div class="flex justify-between items-center mt-3 pt-2 border-t border-orange-100/60">
                <span class="font-bold text-rose-600 text-sm">NT$ {{ item.price * item.quantity }}</span>
                <div class="flex items-center space-x-2 bg-white rounded-xl border border-orange-200 px-2 py-1">
                  <button @click="updateQuantity(index, -1)" class="w-6 h-6 flex items-center justify-center text-orange-600 font-bold hover:bg-orange-50 rounded">-</button>
                  <span class="text-sm font-bold w-6 text-center">{{ item.quantity }}</span>
                  <button @click="updateQuantity(index, 1)" class="w-6 h-6 flex items-center justify-center text-orange-600 font-bold hover:bg-orange-50 rounded">+</button>
                </div>
              </div>
            </div>
          </div>
        </div>

        <div class="border-t border-gray-100 pt-4">
          <div class="flex justify-between items-center mb-4 text-lg font-bold">
            <span>總金額</span>
            <span class="text-rose-600">NT$ {{ totalPrice }}</span>
          </div>
          <button 
            @click="submitOrder"
            :disabled="cart.length === 0"
            :class="cart.length === 0 ? 'bg-gray-300 cursor-not-allowed' : 'bg-rose-600 hover:bg-rose-700 shadow-md'"
            class="w-full py-3.5 text-white font-medium rounded-2xl transition-colors">
            確認送出訂單
          </button>
        </div>
      </div>
    </div>

    <!-- 訂單完成彈窗 (Order Success Modal) -->
    <div v-if="completedOrder" class="fixed inset-0 bg-black/60 backdrop-blur-xs z-50 flex items-center justify-center p-4">
      <div class="bg-white w-full max-w-sm rounded-3xl p-6 shadow-2xl text-center">
        <div class="w-16 h-16 bg-green-100 text-green-600 rounded-full flex items-center justify-center text-3xl mx-auto mb-4">
          ✓
        </div>
        <h3 class="text-xl font-black text-gray-800">下單成功！</h3>
        <p class="text-xs text-gray-500 mt-1">廚房已收到您的點餐，請稍後片刻</p>

        <!-- 取餐號碼牌 -->
        <div class="my-5 py-3 bg-orange-50 rounded-2xl border border-orange-200">
          <span class="text-xs text-orange-600 font-bold block">取餐號碼</span>
          <span class="text-3xl font-black text-orange-600">{{ completedOrder.orderNo }}</span>
        </div>

        <!-- 明細摘要 -->
        <div class="text-left text-xs text-gray-600 bg-gray-50 p-3 rounded-xl mb-5 space-y-1">
          <div class="flex justify-between font-bold border-b pb-1 mb-1">
            <span>餐點明細</span>
            <span>時間：{{ completedOrder.time }}</span>
          </div>
          <div v-for="(item, i) in completedOrder.items" :key="i" class="flex justify-between">
            <span>{{ item.name }} x{{ item.quantity }}</span>
            <span>NT$ {{ item.price * item.quantity }}</span>
          </div>
          <div class="flex justify-between font-bold text-rose-600 pt-1 border-t mt-1">
            <span>合計</span>
            <span>NT$ {{ completedOrder.totalPrice }}</span>
          </div>
        </div>

        <button 
          @click="completedOrder = null"
          class="w-full py-3 bg-rose-600 hover:bg-rose-700 text-white font-bold rounded-2xl shadow-md transition-colors">
          完成 / 回到菜單
        </button>
      </div>
    </div>

  </div>
</template>