<script setup>
import { ref, onMounted, computed, watch } from 'vue'
import PriceForm from './components/PriceForm.vue'
import PriceSearch from './components/PriceSearch.vue'
import PriceTable from './components/PriceTable.vue'

const prices = ref([])
const searchQuery = ref('')
const loading = ref(false)

const fetchPrices = async (query = '') => {
  loading.value = true
  try {
    const url = query ? `/api/prices?q=${encodeURIComponent(query)}` : '/api/prices'
    const resp = await fetch(url)
    if (resp.ok) {
      prices.value = await resp.json()
    }
  } catch (err) {
    console.error('Fetch error:', err)
  } finally {
    loading.value = false
  }
}

const addPrice = async (newRecord) => {
  try {
    const resp = await fetch('/api/prices', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(newRecord),
    })
    if (resp.ok) {
      await fetchPrices(searchQuery.value)
    } else {
      const err = await resp.json()
      alert('新增失敗: ' + (err.error || resp.statusText))
    }
  } catch (err) {
    alert('網路錯誤，請稍後再試')
  }
}

const deletePrice = async (id) => {
  if (!confirm('確定要刪除嗎？')) return
  try {
    const resp = await fetch(`/api/prices/${id}`, { method: 'DELETE' })
    if (resp.ok) {
      await fetchPrices(searchQuery.value)
    } else {
      alert('刪除失敗')
    }
  } catch (err) {
    alert('網路錯誤')
  }
}

onMounted(() => {
  fetchPrices()
})

watch(searchQuery, (newVal) => {
  fetchPrices(newVal)
})

const recordCount = computed(() => prices.value.length)
</script>

<template>
  <div class="container">
    <h1>珍奶價格觀察站</h1>

    <PriceForm @add="addPrice" />

    <hr />

    <PriceSearch v-model="searchQuery" />

    <hr />

    <div id="recordCount" style="margin: 12px 0; font-weight: 600; color: #333;">
      目前共有 {{ recordCount }} 筆珍奶價格紀錄
    </div>

    <PriceTable :prices="prices" @delete="deletePrice" />
  </div>
</template>
