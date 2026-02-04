<template>
  <div class="app">
    <h1>🎵 楽器診断アプリ</h1>

    <!-- 質問画面 -->
    <div v-if="step < questions.length" class="card">
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

    <!-- 結果画面 -->
    <div v-else class="card result">
      <h2>診断結果</h2>

      <p class="instrument">
        あなたにおすすめの楽器は  
        <span>{{ result.label }}</span>
      </p>

      <p class="description">
        {{ result.description }}
      </p>

      <button class="reset" @click="reset">
        もう一度診断する
      </button>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from "vue"

const step = ref(0)

const scores = ref({
  guitar: 0,
  bass: 0,
  drums: 0,
  keyboard: 0
})

const questions = [
  {
    text: "バンドでの理想の立ち位置は？",
    options: [
      { label: "目立ちたい", type: "guitar" },
      { label: "支える役が好き", type: "bass" },
      { label: "全体を引っ張りたい", type: "drums" },
      { label: "世界観を作りたい", type: "keyboard" }
    ]
  },
  {
    text: "リズム感には自信ある？",
    options: [
      { label: "かなりある", type: "drums" },
      { label: "安定している", type: "bass" },
      { label: "普通かな", type: "guitar" },
      { label: "正直あまり…", type: "keyboard" }
    ]
  },
  {
    text: "音楽で一番大事だと思うのは？",
    options: [
      { label: "メロディ", type: "guitar" },
      { label: "グルーヴ", type: "bass" },
      { label: "ノリと勢い", type: "drums" },
      { label: "雰囲気・空気感", type: "keyboard" }
    ]
  }
]

const instruments = {
  guitar: {
    label: "ギター 🎸",
    description:
      "表現力が高く、感情を音に乗せるのが得意。バンドの顔になりやすいタイプ。"
  },
  bass: {
    label: "ベース 🎸",
    description:
      "縁の下の力持ち。安定感があり、全体を支えることに喜びを感じるタイプ。"
  },
  drums: {
    label: "ドラム 🥁",
    description:
      "エネルギッシュでリーダー気質。リズムでバンドを引っ張る存在。"
  },
  keyboard: {
    label: "キーボード 🎹",
    description:
      "感性派でクリエイティブ。音楽に色や広がりを与えるタイプ。"
  }
}

const answer = (type) => {
  scores.value[type]++
  step.value++
}

const result = computed(() => {
  const sorted = Object.entries(scores.value).sort(
    (a, b) => b[1] - a[1]
  )
  return instruments[sorted[0][0]]
})

const reset = () => {
  step.value = 0
  for (const key in scores.value) {
    scores.value[key] = 0
  }
}
</script>

<style scoped>
.app {
  min-height: 100vh;
  background: #f4f6f8;
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 32px 16px;
  font-family: "Helvetica Neue", Arial, sans-serif;
}

h1 {
  margin-bottom: 24px;
}

.card {
  width: 100%;
  max-width: 420px;
  background: #fff;
  padding: 24px;
  border-radius: 16px;
  box-shadow: 0 10px 30px rgba(0, 0, 0, 0.08);
}

.question {
  font-size: 18px;
  margin-bottom: 16px;
}

.option {
  width: 100%;
  margin-bottom: 12px;
  padding: 12px;
  border-radius: 10px;
  border: none;
  font-size: 16px;
  cursor: pointer;
  background: #4f46e5;
  color: #fff;
}

.option:hover {
  opacity: 0.9;
}

.result {
  text-align: center;
}

.instrument {
  font-size: 20px;
  margin: 16px 0;
}

.instrument span {
  font-weight: bold;
  color: #4f46e5;
}

.description {
  color: #555;
  margin-bottom: 24px;
}

.reset {
  background: #9ca3af;
  border: none;
  padding: 10px 16px;
  border-radius: 8px;
  cursor: pointer;
  color: #fff;
}
</style>
