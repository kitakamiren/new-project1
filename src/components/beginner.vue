<template>
  <div class="beginner-page">
    <h2>🌱 楽器別：最初の一歩ガイド</h2>
    <p class="intro">気になる楽器をタップして、選び方のコツをチェック！</p>

    <div class="tab-menu">
      <button 
        v-for="(data, key) in guideData" 
        :key="key"
        :class="{ active: selectedTab === key }"
        @click="selectedTab = key"
      >
        {{ data.icon }} {{ data.shortLabel }}
      </button>
    </div>

    <Transition name="fade" mode="out-in">
      <div :key="selectedTab" class="content-body card">
        <div class="header-group">
          <span class="instrument-tag">{{ guideData[selectedTab].shortLabel }}</span>
          <h3>{{ guideData[selectedTab].title }}</h3>
        </div>
        
        <p class="advice-text">{{ guideData[selectedTab].advice }}</p>

        <div class="recommend-section">
          <h4>🛒 おすすめの最初の一本</h4>
          <div v-for="model in guideData[selectedTab].models" :key="model.name" class="model-item">
            <div class="model-info">
              <span class="model-name">{{ model.name }}</span>
              <span class="model-price">予算目安：{{ model.price }}</span>
            </div>
            <a :href="model.link" target="_blank" class="affiliate-btn">商品をチェック</a>
          </div>
        </div>

        <div class="tips-box">
          <h5>💡 選ぶ時のポイント</h5>
          <ul>
            <li v-for="tip in guideData[selectedTab].tips" :key="tip">{{ tip }}</li>
          </ul>
        </div>
      </div>
    </Transition>
  </div>
</template>

<script setup>
import { ref } from 'vue';

const selectedTab = ref('guitar');

const guideData = {
  guitar: {
    shortLabel: "ギター",
    icon: "🎸",
    title: "表現力無限大！一番人気の相棒",
    advice: "最初は手が痛くなりやすいので、弦高が低めに調整されたモデルや、手が小さくても握りやすいネックのものを選びましょう。",
    tips: ["好きな見た目で選ぶのが一番（モチベ維持）", "アンプとシールドも忘れずに", "最初は『チューナー』が必須アイテム"],
    models: [
      { name: "YAMAHA Pacifica", price: "3万円台", link: "https://jp.yamaha.com/products/musical_instruments/guitars_basses/el_guitars/pacifica/pac_100.html" },
    ]
  },
  bass: {
    shortLabel: "ベース",
    icon: "🎸",
    title: "バンドの心臓。重低音の魅力",
    advice: "まずは『ジャズベース』タイプを選んでおけば間違いありません。手が小さい人は『ミディアムスケール』も検討を。",
    tips: ["アンプを通した音を聴いてみる", "重すぎないものを選ぶ（肩が凝らないため）", "指弾きかピック弾きか試してみる"],
    models: [
      { name: "Bacchus BJB-1R", price: "2万円台", link: "https://bacchusdo.com/bjb1rsm.htm?srsltid=AfmBOoqHJcPt8B80bS_P-OpOcNlq5-o2Dkqv0g2hJ9wss43JSXoj6s1F" },
    ]
  },
  drums: {
    shortLabel: "ドラム",
    icon: "🥁",
    title: "全身でリズムを刻む楽しさ",
    advice: "いきなり生ドラムを買うのは難しいので、まずは『練習パッド』や『電子ドラム』、そして自分専用の『スティック』から！",
    tips: ["スティックの太さと重さを手に馴染ませる", "電子ドラムはメッシュパッドが静かで人気", "近所迷惑にならない防音対策を考える"],
    models: [
      { name: "Roland V-Drums TD-02K", price: "5万円台", link: "https://www.roland.com/jp/products/td-02k/" },
      { name: "PEARL ( パール ) / 106HC ドラムスティック ヒッコリー クリアラッカー", price: "2千円程度", link: "https://www.soundhouse.co.jp/products/detail/item/201027/" }
    ]
  },
  keyboard: {
    shortLabel: "キーボード",
    icon: "🎹",
    title: "無限の音色で世界を作る",
    advice: "ピアノに近い感触が欲しいなら『88鍵・重めの鍵盤』、持ち運びや作曲なら『61鍵・シンセタイプ』がおすすめ。",
    tips: ["鍵盤の『タッチ感』を実機で確認", "内蔵音色の種類をチェック", "サスティンペダルをセットで買う"],
    models: [
      { name: "Casio Privia PX-S1100", price: "6万円台", link: "https://www.casio.com/jp/electronic-musical-instruments/product.PX-S1100BK/" },
    ]
  }
};
</script>

<style scoped>
.beginner-page { max-width: 600px; margin: 0 auto; padding: 20px; }
.tab-menu { display: flex; gap: 8px; margin-bottom: 20px; overflow-x: auto; padding-bottom: 10px; }
.tab-menu button { 
  flex: 1; padding: 10px; border: 1px solid #ddd; border-radius: 8px; background: white; cursor: pointer; white-space: nowrap;
}
.tab-menu button.active { background: #42b983; color: white; border-color: #42b983; font-weight: bold; }

.header-group { display: flex; align-items: center; gap: 10px; margin-bottom: 15px; }
.instrument-tag { background: #e8f5e9; color: #2e7d32; padding: 4px 12px; border-radius: 4px; font-weight: bold; font-size: 0.8rem; }

.advice-text { line-height: 1.6; color: #444; margin-bottom: 20px; }

.recommend-section { background: #fdfdfd; border: 1px solid #eee; border-radius: 12px; padding: 15px; }
.model-item { 
  display: flex; justify-content: space-between; align-items: center; 
  padding: 12px 0; border-bottom: 1px dashed #eee;
}
.model-item:last-child { border-bottom: none; }
.model-name { font-weight: bold; display: block; }
.model-price { font-size: 0.8rem; color: #888; }

.affiliate-btn { 
  background: #ff9900; color: white; text-decoration: none; padding: 8px 12px; border-radius: 6px; font-size: 0.85rem; font-weight: bold;
}

.tips-box { margin-top: 20px; background: #fff9c4; padding: 15px; border-radius: 8px; }
.tips-box h5 { margin: 0 0 10px 0; color: #f57f17; }
.tips-box ul { margin: 0; padding-left: 20px; font-size: 0.9rem; }

/* アニメーション */
.fade-enter-active, .fade-leave-active { transition: opacity 0.2s ease; }
.fade-enter-from, .fade-leave-to { opacity: 0; }
</style>
