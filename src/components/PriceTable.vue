<script setup>
defineProps({
  prices: {
    type: Array,
    required: true
  }
})

defineEmits(['delete'])
</script>

<template>
  <table>
    <thead>
      <tr>
        <th>日期</th>
        <th>商品名稱</th>
        <th>價格</th>
        <th>操作</th>
      </tr>
    </thead>
    <tbody>
      <tr v-for="item in prices" :key="item.id">
        <td>{{ item.date }}</td>
        <td>{{ item.name }}</td>
        <td>{{ item.price }}</td>
        <td>
          <button class="delete-btn" @click="$emit('delete', item.id)">刪除</button>
        </td>
      </tr>
      <tr v-if="prices.length === 0">
        <td colspan="4">目前沒有任何紀錄</td>
      </tr>
    </tbody>
  </table>
</template>
