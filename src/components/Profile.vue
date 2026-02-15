<template>
  <div class="card">
    <h2>👤 Profile</h2>

    <p>名前：{{ name }}</p>

    <div v-if="savedResult">
      <h3>🎵 前回の診断結果</h3>
      <p><strong>{{ savedResult.result.label }}</strong></p>
      <p>{{ savedResult.result.description }}</p>
      <p class="date">診断日：{{ savedResult.date }}</p>
    </div>

    <p v-else>
      まだ診断結果がありません
    </p>
     <button class="logout" @click="$emit('logout')">
        ログアウト
      </button>
  </div>
</template>

<script setup>
import { ref, onMounted } from "vue";

const props = defineProps({
  name: String,
});

const savedResult = ref(null);

onMounted(() => {
  const data = localStorage.getItem(
    "instrumentResult_" + props.name
  );

  if (data) {
    savedResult.value = JSON.parse(data);
  }
});
</script>

<style scoped>
.logout {
  margin-top: 24px;
  width: 100%;
  padding: 12px;
  border-radius: 12px;
  border: none;
  background: #eee;
  font-weight: bold;
  cursor: pointer;
}
</style>
