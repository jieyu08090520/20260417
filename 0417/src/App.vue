<script setup>
import { ref, computed, onMounted, onUnmounted } from 'vue'

// === 狀態管理 ===
const isCountdownMode = ref(false) // 切換計時模式
const isZhuyinMode = ref(false)    // 切換注音模式
const isDarkMode = ref(false)      // 切換黑白主題

// === 時間與倒數計時相關 ===
const currentTime = ref('')
const countdownSeconds = ref(600)  // 預設倒數時間：10 分鐘 (600 秒)
const isCounting = ref(false)
let timerInterval = null

// === 表單輸入與顯示資料 ===
const examData = ref({
  time: '',
  subject: '',
  proctor: ''
})

// 取得並更新「現在時間」
const updateTime = () => {
  const now = new Date()
  currentTime.value = now.toLocaleTimeString('zh-TW', { hour12: false })
}

// 格式化「倒數時間」 (顯示為 MM:SS)
const formattedCountdown = computed(() => {
  const m = Math.floor(countdownSeconds.value / 60).toString().padStart(2, '0')
  const s = (countdownSeconds.value % 60).toString().padStart(2, '0')
  return `${m}:${s}`
})

// 倒數計時器操作
const toggleCountdown = () => {
  isCounting.value = !isCounting.value
}
const resetCountdown = () => {
  isCounting.value = false
  countdownSeconds.value = 600
}

// 生命週期掛載：啟動時鐘背景執行
onMounted(() => {
  updateTime()
  timerInterval = setInterval(() => {
    updateTime() // 保持現在時間持續更新
    
    // 如果正在倒數模式且啟動中，則每秒扣除
    if (isCountdownMode.value && isCounting.value && countdownSeconds.value > 0) {
      countdownSeconds.value--
    } else if (countdownSeconds.value === 0) {
      isCounting.value = false // 倒數結束自動停止
    }
  }, 1000)
})

// 元件卸載時清除計時器避免記憶體流失
onUnmounted(() => {
  clearInterval(timerInterval)
})
</script>

<template>
  <div class="app-wrapper" :class="{ 'dark-theme': isDarkMode, 'light-theme': !isDarkMode }">
    <div class="container">
      
      <!-- 頂部：標題與淡江教科系按鈕 -->
      <header class="header">
        <a href="https://www.et.tku.edu.tw/" target="_blank" class="tku-btn">
          <span v-if="!isZhuyinMode">淡江教科系</span>
          <span v-else>
            <ruby>淡<rt>ㄉㄢˋ</rt></ruby><ruby>江<rt>ㄐㄧㄤ</rt></ruby><ruby>教<rt>ㄐㄧㄠˋ</rt></ruby><ruby>科<rt>ㄎㄜ</rt></ruby><ruby>系<rt>ㄒㄧˋ</rt></ruby>
          </span>
        </a>
        <h1 class="title">
          <span v-if="!isZhuyinMode">互動程式設計_413730994</span>
          <span v-else>
            <ruby>互<rt>ㄏㄨˋ</rt></ruby><ruby>動<rt>ㄉㄨㄥˋ</rt></ruby><ruby>程<rt>ㄔㄥˊ</rt></ruby><ruby>式<rt>ㄕˋ</rt></ruby><ruby>設<rt>ㄕㄜˋ</rt></ruby><ruby>計<rt>ㄐㄧˋ</rt></ruby>_413730994
          </span>
        </h1>
      </header>

      <!-- 第二區：三個監考小工具按鈕 -->
      <div class="tools-section">
        <button @click="isCountdownMode = !isCountdownMode" class="tool-btn btn-blue">
          <span v-if="!isZhuyinMode">切換倒數/現在時間</span>
          <span v-else>
            <ruby>切<rt>ㄑㄧㄝ</rt></ruby><ruby>換<rt>ㄏㄨㄢˋ</rt></ruby><ruby>時<rt>ㄕˊ</rt></ruby><ruby>間<rt>ㄐㄧㄢ</rt></ruby>
          </span>
        </button>
        
        <button @click="isZhuyinMode = !isZhuyinMode" class="tool-btn btn-green">
          <span v-if="!isZhuyinMode">注音符號切換</span>
          <span v-else>
            <ruby>注<rt>ㄓㄨˋ</rt></ruby><ruby>音<rt>ㄧㄣ</rt></ruby><ruby>切<rt>ㄑㄧㄝ</rt></ruby><ruby>換<rt>ㄏㄨㄢˋ</rt></ruby>
          </span>
        </button>
        
        <button @click="isDarkMode = !isDarkMode" class="tool-btn btn-purple">
          <span v-if="!isZhuyinMode">黑白主題切換</span>
          <span v-else>
            <ruby>主<rt>ㄓㄨˇ</rt></ruby><ruby>題<rt>ㄊㄧˊ</rt></ruby><ruby>切<rt>ㄑㄧㄝ</rt></ruby><ruby>換<rt>ㄏㄨㄢˋ</rt></ruby>
          </span>
        </button>
      </div>

      <!-- 第三區：時間顯示畫面 -->
      <div class="time-display-section glass-card">
        <!-- 顯示現在時間 -->
        <div v-if="!isCountdownMode" class="time-box">
          <h2>
            <span v-if="!isZhuyinMode">現在時間</span>
            <span v-else><ruby>現<rt>ㄒㄧㄢˋ</rt></ruby><ruby>在<rt>ㄗㄞˋ</rt></ruby><ruby>時<rt>ㄕˊ</rt></ruby><ruby>間<rt>ㄐㄧㄢ</rt></ruby></span>
          </h2>
          <div class="time-text">{{ currentTime }}</div>
        </div>
        
        <!-- 顯示倒數計時 -->
        <div v-else class="time-box">
          <h2>
            <span v-if="!isZhuyinMode">倒數計時</span>
            <span v-else><ruby>倒<rt>ㄉㄠˋ</rt></ruby><ruby>數<rt>ㄕㄨˇ</rt></ruby><ruby>計<rt>ㄐㄧˋ</rt></ruby><ruby>時<rt>ㄕˊ</rt></ruby></span>
          </h2>
          <div class="time-text">{{ formattedCountdown }}</div>
          <div class="timer-controls">
            <button @click="toggleCountdown" class="control-btn">{{ isCounting ? '暫停' : '開始' }}</button>
            <button @click="resetCountdown" class="control-btn btn-reset">重設</button>
          </div>
        </div>
      </div>

      <!-- 第四區：資料輸入 -->
      <div class="form-section glass-card">
        <h3>
          <span v-if="!isZhuyinMode">輸入監考資料</span>
          <span v-else><ruby>輸<rt>ㄕㄨ</rt></ruby><ruby>入<rt>ㄖㄨˋ</rt></ruby><ruby>資<rt>ㄗ</rt></ruby><ruby>料<rt>ㄌㄧㄠˋ</rt></ruby></span>
        </h3>
        
        <div class="input-group">
          <label>時間 (Time):</label>
          <input v-model="examData.time" type="text" placeholder="例如: 10:00 - 12:00" />
        </div>
        <div class="input-group">
          <label>科目 (Subject):</label>
          <input v-model="examData.subject" type="text" placeholder="輸入考試科目" />
        </div>
        <div class="input-group">
          <label>監考老師 (Proctor):</label>
          <input v-model="examData.proctor" type="text" placeholder="輸入監考老師姓名" />
        </div>
      </div>

      <!-- 第五區：資料即時顯示 (雙向綁定) -->
      <div class="data-display-section glass-card" v-if="examData.time || examData.subject || examData.proctor">
        <h3>
          <span v-if="!isZhuyinMode">考試資訊顯示</span>
          <span v-else><ruby>考<rt>ㄎㄠˇ</rt></ruby><ruby>試<rt>ㄕˋ</rt></ruby><ruby>資<rt>ㄗ</rt></ruby><ruby>訊<rt>ㄒㄩㄣˋ</rt></ruby></span>
        </h3>
        <div class="info-card">
          <p><strong>時間:</strong> {{ examData.time || '（尚未輸入）' }}</p>
          <p><strong>科目:</strong> {{ examData.subject || '（尚未輸入）' }}</p>
          <p><strong>監考老師:</strong> {{ examData.proctor || '（尚未輸入）' }}</p>
        </div>
      </div>

    </div>
  </div>
</template>

<style scoped>
/* === iOS 27 風格核心設定 === */

/* 最外層包裹器 (動態漸層背景與過渡) */
.app-wrapper {
  min-height: 100vh;
  width: 100%;
  transition: background 0.8s cubic-bezier(0.22, 1, 0.36, 1), color 0.6s ease;
  display: flex;
  justify-content: center;
  /* 使用 Apple 原生字體堆疊 */
  font-family: -apple-system, BlinkMacSystemFont, "SF Pro Display", "SF Pro Text", "Helvetica Neue", sans-serif;
  padding: 2vh 0;
  box-sizing: border-box;
}

/* 輕快明亮的 iOS 淺色漸層 */
.light-theme { 
  background: linear-gradient(135deg, #e0c3fc 0%, #8ec5fc 100%); 
  color: #1d1d1f; 
}
/* 深邃質感的 iOS 深色太空漸層 */
.dark-theme { 
  background: linear-gradient(135deg, #0f2027 0%, #203a43 50%, #2c5364 100%); 
  color: #f5f5f7; 
}

/* 響應式容器 */
.container {
  width: 100%;
  max-width: 800px;
  padding: 20px;
  display: flex;
  flex-direction: column;
  gap: 24px;
  box-sizing: border-box;
}

/* --- 毛玻璃卡片特效 (Glassmorphism) --- */
.glass-card {
  background: rgba(255, 255, 255, 0.4);
  backdrop-filter: blur(24px);
  -webkit-backdrop-filter: blur(24px);
  border: 1px solid rgba(255, 255, 255, 0.4);
  border-radius: 28px; /* 大圓角 */
  padding: 32px;
  box-shadow: 0 10px 40px -10px rgba(0,0,0,0.1);
  transition: all 0.4s cubic-bezier(0.25, 0.8, 0.25, 1);
}
.dark-theme .glass-card {
  background: rgba(30, 30, 32, 0.45);
  border: 1px solid rgba(255, 255, 255, 0.1);
  box-shadow: 0 10px 40px -10px rgba(0,0,0,0.4);
}

/* 頂部標題與按鈕 */
.header { display: flex; align-items: center; gap: 16px; flex-wrap: wrap; margin-bottom: 8px; }
.title { font-size: 2rem; font-weight: 700; letter-spacing: -0.5px; margin: 0; flex-grow: 1; }
.tku-btn {
  text-decoration: none; background: rgba(255, 255, 255, 0.6); color: #1d1d1f;
  padding: 12px 20px; border-radius: 20px; font-weight: 600;
  box-shadow: 0 4px 12px rgba(0,0,0,0.05); transition: 0.3s cubic-bezier(0.2, 0, 0, 1);
  border: 1px solid rgba(255, 255, 255, 0.5);
  backdrop-filter: blur(10px);
}
.dark-theme .tku-btn { background: rgba(50, 50, 50, 0.6); color: #fff; border-color: rgba(255,255,255,0.1); }
.tku-btn:active { transform: scale(0.95); }

/* 工具按鈕區 (類 iOS 控制中心) */
.tools-section { display: flex; gap: 16px; flex-wrap: wrap; }
.tool-btn {
  flex: 1; min-width: 140px; padding: 16px; border: none; border-radius: 24px;
  color: white; font-size: 1.1rem; font-weight: 600; cursor: pointer; 
  transition: transform 0.2s cubic-bezier(0.2, 0, 0, 1), box-shadow 0.2s;
  box-shadow: 0 4px 15px rgba(0,0,0,0.1);
}
.tool-btn:active { transform: scale(0.94); }
.btn-blue { background: linear-gradient(135deg, #007aff, #0056b3); }
.btn-green { background: linear-gradient(135deg, #34c759, #248a3d); }
.btn-purple { background: linear-gradient(135deg, #af52de, #7b1fa2); }

/* 時間顯示區 */
.time-display-section { text-align: center; }
.time-box h2 { margin: 0 0 12px 0; font-size: 1.4rem; font-weight: 500; opacity: 0.8; }
.time-text { font-size: 5.5rem; font-weight: 700; font-variant-numeric: tabular-nums; letter-spacing: -2px; margin: 0; text-shadow: 0 2px 15px rgba(0,0,0,0.05); }
.timer-controls { display: flex; justify-content: center; gap: 16px; margin-top: 24px; }
.control-btn { padding: 14px 32px; font-size: 1.1rem; font-weight: 600; border: none; border-radius: 30px; cursor: pointer; background: #ff9500; color: white; transition: 0.2s cubic-bezier(0.2, 0, 0, 1); box-shadow: 0 4px 15px rgba(255, 149, 0, 0.3); }
.control-btn:active { transform: scale(0.92); }
.btn-reset { background: rgba(142, 142, 147, 0.8); box-shadow: none; color: white; backdrop-filter: blur(10px); }

/* 表單與資料區 */
.form-section h3, .data-display-section h3 { margin-top: 0; font-size: 1.4rem; font-weight: 600; margin-bottom: 20px; }
.input-group { display: flex; flex-direction: column; margin-bottom: 16px; }
.input-group label { font-weight: 500; margin-bottom: 8px; font-size: 0.95rem; opacity: 0.8; }
.input-group input { padding: 16px; font-size: 1.05rem; border: 1px solid rgba(0,0,0,0.1); border-radius: 16px; background: rgba(255, 255, 255, 0.7); color: inherit; transition: all 0.3s ease; font-family: inherit; }
.input-group input:focus { outline: none; border-color: #007aff; background: #fff; box-shadow: 0 0 0 4px rgba(0, 122, 255, 0.2); }
.dark-theme .input-group input { border-color: rgba(255,255,255,0.1); background: rgba(0, 0, 0, 0.2); color: #fff; }
.dark-theme .input-group input:focus { background: rgba(0, 0, 0, 0.4); border-color: #0a84ff; box-shadow: 0 0 0 4px rgba(10, 132, 255, 0.3); }
.dark-theme .input-group input::placeholder { color: #8e8e93; }
.info-card { padding: 20px; border-radius: 20px; font-size: 1.15rem; line-height: 1.8; background: rgba(255, 255, 255, 0.3); border: 1px solid rgba(255, 255, 255, 0.4); }
.dark-theme .info-card { background: rgba(0, 0, 0, 0.2); border-color: rgba(255, 255, 255, 0.1); }
.info-card p { margin: 8px 0; }

/* 注音標記美化 */
ruby { font-family: inherit; margin: 0 2px; }
rt { font-size: 0.55em; color: #ff3b30; font-weight: 600; }
.dark-theme rt { color: #ff453a; }

/* 手機版排版微調 */
@media (max-width: 600px) {
  .header { flex-direction: column; align-items: flex-start; gap: 12px; }
  .time-text { font-size: 4rem; }
  .glass-card { padding: 24px; border-radius: 24px; }
}
</style>
