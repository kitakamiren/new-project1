<template>
  <div class="app">
    <div class="card">
      <h2>🎵 かんたん楽器診断</h2>

      <p v-if="step < questions.length">
        Q{{ step + 1 }}. {{ questions[step].text }}
      </p>

      <button
        v-for="option in questions[step]?.options"
        :key="option.label"
        class="option"
        @click="answer(option.type)"
      >
        {{ option.label }}
      </button>

      <!-- 結果 -->
      <div v-if="step === questions.length" class="result">
        <p>あなたに向いている楽器は…</p>
        <h3>{{ result.label }}</h3>

        <p class="desc">{{ result.description }}</p>

        <button class="login-guide" @click="$emit('go-login')">
          ログインして結果を保存する
        </button>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from "vue";

const questions = [
  {
    text: "音楽は？",
    options: [
      { label: "1人で楽しみたい", type: "guitar" },
      { label: "みんなでやりたい", type: "bass" },
    ],
  },
  {
    text: "性格は？",
    options: [
      { label: "目立ちたい", type: "guitar" },
      { label: "支えたい", type: "bass" },
    ],
  },
  {
    text: "集中力は？",
    options: [
      { label: "長時間OK", type: "keyboard" },
      { label: "短時間派", type: "drums" },
    ],
  },
  {
    text: "体を動かすのは？",
    options: [
      { label: "好き", type: "drums" },
      { label: "あまり", type: "keyboard" },
    ],
  },
  {
    text: "メロディーは？",
    options: [
      { label: "弾きたい", type: "guitar" },
      { label: "作りたい", type: "keyboard" },
    ],
  },
];

const step = ref(0);
const scores = ref({
  guitar: 0,
  bass: 0,
  drums: 0,
  keyboard: 0,
});

const instruments = {
  guitar: { label: "ギター 🎸", description: "自由度が高く始めやすい" },
  bass: { label: "ベース 🎸", description: "縁の下の力持ちタイプ" },
  drums: { label: "ドラム 🥁", description: "エネルギッシュで爽快" },
  keyboard: { label: "キーボード 🎹", description: "音楽全体を操れる" },
};

const answer = (type) => {
  scores.value[type]++;
  step.value++;
};

const result = computed(() => {
  const max = Math.max(...Object.values(scores.value));
  const key = Object.keys(scores.value).find(
    (k) => scores.value[k] === max
  );
  return instruments[key];
});
const logout = () => {
  isLoggedIn.value = false;
  name.value = "";
  email.value = "";

  step.value = 0;
  Object.keys(scores.value).forEach((k) => (scores.value[k] = 0));

  currentPage.value = "top";
};
</script>
