<template>
  <!-- ヘッダー -->
  <header class="app-header">
    <h1 class="app-title">🎵 楽器診断アプリ</h1>
  </header>

  <!-- ログイン画面 -->
  <div v-if="!isLoggedIn" class="login-page">
    <div class="login-card">
      <h2>ログイン</h2>

      <input
        v-model="name"
        type="text"
        placeholder="名前を入力"
        class="login-input"
      />

      <button class="login-button" @click="login">
        はじめる
      </button>
    </div>
  </div>

  <!-- 診断画面 -->
  <div v-else class="app">
    <!-- 進捗バー -->
    <div class="progress">
      <div class="progress-bar">
        <div class="progress-fill" :style="{ width: progress + '%' }"></div>
      </div>
      <p class="progress-text">{{ step }} / {{ questions.length }}</p>
    </div>

    <Transition name="question" mode="out-in">
      <!-- 質問 -->
      <div
        v-if="step < questions.length"
        :key="step"
        class="card"
      >
        <p class="question">
          Q{{ step + 1 }}. {{ questions[step].text }}
        </p>

        <button
          v-for="option in questions[step].options"
          :key="option.label"
          @click="answer(option.type)"
          class="option"
        >
          {{ option.label }}
        </button>
      </div>

      <!-- 結果 -->
      <div
        v-else
        :key="'result'"
        class="card result"
        id="result-card"
      >
        <p class="result-name">
          {{ name }} さんの診断結果
        </p>

        <p class="instrument">
          あなたに合っている楽器は
          <span>{{ result.label }}</span>
        </p>

        <p class="description">
          {{ result.description }}
        </p>

<button
    type="button"
    class="share"
    @click="shareResult"
  >
    📸 Instagramでシェア
  </button>

        <button class="reset" @click="reset">
          もう一度診断する
        </button>
      </div>
    </Transition>
  </div>
</template>

<script setup>
import html2canvas from "html2canvas";

import { ref, computed } from "vue";
import { questions } from "./data/questions";

const isLoggedIn = ref(false);
const name = ref("");

const step = ref(0);

const scores = ref({
  guitar: 0,
  bass: 0,
  drums: 0,
  keyboard: 0,
});

const instruments = {
  guitar: {
    label: "ギター 🎸",
    description: "表現力が高く、感情を音に乗せるのが得意。",
  },
  bass: {
    label: "ベース 🎸",
    description: "縁の下の力持ち。全体を支えるタイプ。",
  },
  drums: {
    label: "ドラム 🥁",
    description: "エネルギッシュでリーダー気質。",
  },
  keyboard: {
    label: "キーボード 🎹",
    description: "感性派で世界観を作るタイプ。",
  },
};

const login = () => {
  if (!name.value) return;
  isLoggedIn.value = true;
};

const answer = (type) => {
  scores.value[type]++;
  step.value++;
};

const result = computed(() => {
  const max = Math.max(...Object.values(scores.value));
  const keys = Object.keys(scores.value).filter(
    (k) => scores.value[k] === max
  );
  return instruments[keys[Math.floor(Math.random() * keys.length)]];
});

const reset = () => {
  step.value = 0;
  Object.keys(scores.value).forEach((k) => (scores.value[k] = 0));
};

const progress = computed(() =>
  Math.round((step.value / questions.length) * 100)
);

const shareResult = async () => {
  const element = document.getElementById("result-card");
  const canvas = await html2canvas(element);

  const link = document.createElement("a");
  link.download = "instrument-result.png";
  link.href = canvas.toDataURL("image/png");
  link.click();

  alert("画像を保存しました！/nInstagramのストーリーに追加してね 📸");
};


</script>

<style>
/* ログイン */
.login-page {
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
}

.login-card {
  width: 320px;
  background: white;
  padding: 32px;
  border-radius: 16px;
  text-align: center;
}

/* 診断 */
.app {
  padding-top: 96px;
  display: flex;
  flex-direction: column;
  align-items: center;
}

.card {
  width: 100%;
  max-width: 420px;
  background: white;
  padding: 24px;
  border-radius: 16px;
}

.option {
  width: 100%;
  margin-top: 12px;
}

/* アニメーション */
.question-enter-active,
.question-leave-active {
  transition: opacity 0.3s ease;
}
.question-enter-from,
.question-leave-to {
  opacity: 0;
}
.share {
  margin-top: 20px;
  width: 100%;
  padding: 14px;
  border-radius: 14px;
  border: none;
  font-size: 16px;
  font-weight: bold;
  color: white;
  background: linear-gradient(
    135deg,
    #f58529,
    #dd2a7b,
    #8134af
  );
  cursor: pointer;
}

.share:hover {
  opacity: 0.9;
}

</style>
