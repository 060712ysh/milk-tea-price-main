<script setup>
import { ref, onMounted } from 'vue'

const emit = defineEmits(['add'])

const year = ref('')
const month = ref('')
const day = ref('')
const product = ref('')
const price = ref('')

const yearInputRef = ref(null)
const monthInputRef = ref(null)
const dayInputRef = ref(null)

const onlyDigits = (value) => String(value || '').replace(/\D/g, '')

const focusAndSelect = (el) => {
  if (el) {
    el.focus()
    el.select()
  }
}

const handleYearInput = (e) => {
  year.value = onlyDigits(year.value).slice(0, 4)
  if (year.value.length === 4) {
    focusAndSelect(monthInputRef.value)
  }
}

const handleMonthInput = (e) => {
  month.value = onlyDigits(month.value).slice(0, 2)
  if (month.value.length === 2) {
    focusAndSelect(dayInputRef.value)
  }
}

const handleDayInput = (e) => {
  day.value = onlyDigits(day.value).slice(0, 2)
}

const handleYearKeydown = (ev) => {
  if (ev.key === 'Enter' && year.value.length >= 4) {
    ev.preventDefault()
    focusAndSelect(monthInputRef.value)
  }
}

const handleMonthKeydown = (ev) => {
  if (ev.key === 'Backspace' && month.value.length === 0) {
    ev.preventDefault()
    focusAndSelect(yearInputRef.value)
  }
  if (ev.key === 'Enter' && month.value.length >= 2) {
    ev.preventDefault()
    focusAndSelect(dayInputRef.value)
  }
}

const handleDayKeydown = (ev) => {
  if (ev.key === 'Backspace' && day.value.length === 0) {
    ev.preventDefault()
    focusAndSelect(monthInputRef.value)
  }
}

const submit = () => {
  const y = year.value.padStart(4, '0')
  const m = month.value.padStart(2, '0')
  const d = day.value.padStart(2, '0')
  const date = `${y}-${m}-${d}`
  const name = product.value.trim()
  const p = price.value

  if (y.length !== 4 || m.length !== 2 || d.length !== 2 || !name || p === '' || p === null) {
    alert('請填寫日期、商品名稱與價格')
    return
  }

  emit('add', { date, name, price: Number(p) })
  
  // Reset form except date maybe? Or reset all
  product.value = ''
  price.value = ''
}
</script>

<template>
  <div class="form-box">
    <div class="form-row">
      <label>日期：</label>
      <div class="date-group" aria-label="日期輸入欄位">
        <input
          v-model="year"
          ref="yearInputRef"
          type="text"
          inputmode="numeric"
          maxlength="4"
          placeholder="YYYY"
          aria-label="年份"
          @input="handleYearInput"
          @keydown="handleYearKeydown"
        />
        <span class="date-separator">-</span>
        <input
          v-model="month"
          ref="monthInputRef"
          type="text"
          inputmode="numeric"
          maxlength="2"
          placeholder="MM"
          aria-label="月份"
          @input="handleMonthInput"
          @keydown="handleMonthKeydown"
        />
        <span class="date-separator">-</span>
        <input
          v-model="day"
          ref="dayInputRef"
          type="text"
          inputmode="numeric"
          maxlength="2"
          placeholder="DD"
          aria-label="日期"
          @input="handleDayInput"
          @keydown="handleDayKeydown"
        />
      </div>
    </div>

    <div class="form-row">
      <label>商品名稱：</label>
      <input v-model="product" type="text" placeholder="例如：珍珠奶茶" />
    </div>

    <div class="form-row price-row">
      <label>價格：</label>
      <input v-model="price" type="number" placeholder="例如：65" />
    </div>

    <button @click="submit">新增資料</button>
  </div>
</template>
