<template>
  <header class="app-header">
    <h1 class="app-title">🎵 楽器診断アプリ</h1>

    <!-- 未ログイン用ナビ -->
    <nav class="app-nav" v-if="!isLoggedIn">
      <button class="login-nav" @click="currentPage = 'login'">ログイン</button>
    </nav>

    <!-- ログイン後ナビ -->
    <nav class="app-nav" v-else>
      <button
        :class="{ active: currentPage === 'diagnosis' }"
        @click="currentPage = 'diagnosis'"
      >
        🎵 診断
      </button>
      <button
        :class="{ active: currentPage === 'about' }"
        @click="currentPage = 'about'"
      >
        ℹ️ About
      </button>

      <button
        :class="{ active: currentPage === 'beginner' }"
        @click="currentPage = 'beginner'"
      >
        🌱 初心者向け
      </button>
      <button
        :class="{ active: currentPage === 'board' }"
        @click="currentPage = 'board'"
      >
        💬 掲示板
      </button>
      <button
        :class="{ active: currentPage === 'profile' }"
        @click="currentPage = 'profile'"
      >
        👤 Profile
      </button>
    </nav>
  </header>

  <!-- トップ：ログイン不要 -->
  <QuickDiagnosis
    v-if="!isLoggedIn && currentPage === 'top'"
    @go-login="currentPage = 'login'"
  />

  <!-- ログイン画面 -->
  <div v-if="!isLoggedIn && currentPage === 'login'" class="login-page">
    <div class="login-card">
      <h2>ログイン</h2>

      <input
        v-model="name"
        type="text"
        placeholder="名前を入力"
        class="login-input"
      />
      <input
        v-model="email"
        type="email"
        placeholder="メールアドレス"
        class="login-input"
      />

      <button class="login-button" @click="login">はじめる</button>
    </div>
  </div>

  <!-- ログイン後 -->
  <div v-else>
    <!-- 診断ページ -->
    <div v-if="currentPage === 'diagnosis'" class="app">
      <!-- 進捗バー -->
      <div class="progress">
        <div class="progress-bar">
          <div class="progress-fill" :style="{ width: progress + '%' }"></div>
        </div>
        <p class="progress-text">{{ step }} / {{ questions.length }}</p>
      </div>

      <Transition name="question" mode="out-in">
        <!-- 質問 -->
        <div v-if="step < questions.length" :key="step" class="card">
          <p class="question">Q{{ step + 1 }}. {{ questions[step].text }}</p>

          <button
            v-for="option in questions[step].options"
            :key="option.label"
            class="option"
            @click="answer(option.type)"
          >
            {{ option.label }}
          </button>
        </div>

        <!-- 結果 -->
        <div
          v-else
          key="result"
          class="card result"
          id="result-card"
          :class="result.type"
        >
          <div class="result-header">
            <p class="result-name">{{ name }} さんの診断結果</p>
            <h2 class="instrument-label">{{ result.label }}</h2>
          </div>

          <div class="status-container">
            <div
              v-for="(val, label) in result.status"
              :key="label"
              class="status-bar-item"
            >
              <span class="status-label">{{ label }}</span>
              <div class="bar-bg">
                <div class="bar-fill" :style="{ width: val + '%' }"></div>
              </div>
            </div>
          </div>

          <p class="description">{{ result.description }}</p>

          <div class="result-footer">
            <p>
              #楽器診断アプリ #{{
                result.label
                  .replace(" 🎸", "")
                  .replace(" 🥁", "")
                  .replace(" 🎹", "")
              }}
            </p>
          </div>

          <div class="action-buttons">
            <button class="share" @click="shareResult">
              📸 画像を保存してシェア
            </button>
            <button class="reset" @click="reset">もう一度診断する</button>
          </div>
        </div>
      </Transition>
    </div>

    <!-- About -->
    <AboutPage v-else-if="currentPage === 'about'" />

    <!-- Profile -->
    <ProfilePage
      v-else-if="currentPage === 'profile'"
      :name="name"
      @logout="logout"
    />
    <BeginnerPage v-else-if="currentPage === 'beginner'" />
    <BoardPage v-else-if="currentPage === 'board'" :userName="name" />
  </div>
</template>

<script setup>
import { ref, computed, watch } from "vue";

import html2canvas from "html2canvas";

import { questions } from "./data/questions";
import AboutPage from "./components/About.vue";
import ProfilePage from "./components/Profile.vue";
import BeginnerPage from "./components/Beginner.vue";
import QuickDiagnosis from "./components/QuickDiagnosis.vue";
import BoardPage from "./components/Board.vue"; // 追加
// ログアウト処理

const logout = () => {
  // 1. ログイン状態を解除
  isLoggedIn.value = false;

  // 2. ユーザー情報をクリア（必要に応じて）
  name.value = "";
  email.value = "";

  // 3. 画面をトップに戻す
  currentPage.value = "top";

  // 4. 診断状況をリセット
  reset();

  // 任意：ログアウトしたことを通知
  alert("ログアウトしました");
};
const isLoggedIn = ref(false);
const name = ref("");
const email = ref("");
const currentPage = ref("top");

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
  status: { "目立ち度": 95, "難易度": 70, "モテ度": 90, "運搬": 60 },
  youtubeId: "Wp0v_U_T1vE", // 例：初心者向けギターの凄さがわかる動画
    recommendArtist: "布袋寅泰, ジョン・メイヤー"
  },
  bass: {
    label: "ベース 🎸",
    description: "縁の下の力持ち。全体を支えるタイプ。",
    status: { "目立ち度": 40, "難易度": 60, "モテ度": 75, "運搬": 50 },
    youtubeId: "v_fIAtS6X30", // 例：ベースのカッコよさがわかる動画
    recommendArtist: "亀田誠治, フリー(Flea)"
  },
  drums: {
    label: "ドラム 🥁",
    description: "エネルギッシュでリーダー気質。",
    status: { "目立ち度": 80, "難易度": 85, "モテ度": 70, "運搬": 10 },
    youtubeId: "jWp5T_P2Tmg",
    recommendArtist: "神保彰, デイヴ・グロール"
  },
  keyboard: {
    label: "キーボード 🎹",
    description: "感性派で世界観を作るタイプ。",
    status: { "目立ち度": 60, "難易度": 90, "モテ度": 65, "運搬": 40 },
    youtubeId: "5mIn-P6F6I8",
    recommendArtist: "坂本龍一, 小室哲哉"
  },
};

const login = () => {
  if (!name.value) return;
  isLoggedIn.value = true;
  currentPage.value = "diagnosis";
};

const answer = (type) => {
  scores.value[type]++;
  step.value++;
};

const result = computed(() => {
  const max = Math.max(...Object.values(scores.value));
  const keys = Object.keys(scores.value).filter((k) => scores.value[k] === max);
  return instruments[keys[Math.floor(Math.random() * keys.length)]];
});
const lastResult = ref(null);

watch(result, (newResult) => {
  if (!email.value) return;

  const userKey = `user_${email.value}`;

  const data = {
    name: name.value,
    result: newResult,
    date: new Date().toISOString(),
  };

  localStorage.setItem(userKey, JSON.stringify(data));
  lastResult.value = newResult;
});

const reset = () => {
  step.value = 0;
  Object.keys(scores.value).forEach((k) => (scores.value[k] = 0));
};

const progress = computed(() =>
  Math.round((step.value / questions.length) * 100),
);

const shareResult = async () => {
  const element = document.getElementById("result-card");
  const canvas = await html2canvas(element);

  const link = document.createElement("a");
  link.download = "instrument-result.png";
  link.href = canvas.toDataURL("image/png");
  link.click();

  alert("画像を保存しました！\nInstagramのストーリーに追加してね 📸");
};

const saveResult = () => {
  const data = {
    name: name.value,
    result: result.value,
    date: new Date().toLocaleString(),
  };

  localStorage.setItem("instrumentResult_" + name.value, JSON.stringify(data));
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
  font-weight: bold;
  color: white;
  background: linear-gradient(135deg, #f58529, #dd2a7b, #8134af);
}
</style>
