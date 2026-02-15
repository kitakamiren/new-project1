<template>
  <div class="board-page">
    <h2>🎸 楽器仲間掲示板</h2>
    
    <div class="post-form card">
      <textarea 
        v-model="newMessage" 
        placeholder="今の練習状況や、診断結果について呟こう！"
        rows="3"
      ></textarea>
      <button @click="submitPost" :disabled="!newMessage.trim()">投稿する</button>
    </div>

    <div class="posts-list">
      <div v-for="post in posts" :key="post.id" class="post-card card">
        <div class="post-header">
          <span class="post-user">👤 {{ post.userName }}</span>
          <span class="post-date">{{ post.date }}</span>
        </div>
        <p class="post-content">{{ post.content }}</p>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';

const props = defineProps(['userName']);
const newMessage = ref("");
const posts = ref([]);

// 投稿を読み込む
onMounted(() => {
  const savedPosts = localStorage.getItem('app_posts');
  if (savedPosts) posts.value = JSON.parse(savedPosts);
});

// 投稿する
const submitPost = () => {
  const newPost = {
    id: Date.now(),
    userName: props.userName || "名無しさん",
    content: newMessage.value,
    date: new Date().toLocaleString()
  };
  
  posts.value.unshift(newPost); // 先頭に追加
  localStorage.setItem('app_posts', JSON.stringify(posts.value));
  newMessage.value = ""; // 入力欄を空に
};
</script>

<style scoped>
.board-page { padding: 20px; max-width: 500px; margin: 0 auto; }
.post-form { margin-bottom: 20px; display: flex; flex-direction: column; gap: 10px; }
textarea { width: 100%; border-radius: 8px; padding: 10px; border: 1px solid #ddd; }
.post-card { margin-bottom: 15px; text-align: left; }
.post-header { display: flex; justify-content: space-between; font-size: 0.8rem; color: #666; margin-bottom: 8px; }
.post-user { font-weight: bold; color: #444; }
</style>
