<script setup>
import { ref, computed } from 'vue'

const questions = [
  '내 인생의 드라마나 영화는 무엇인가요? 어떤 점이 마음에 남았나요?',
  '좋아하는 책은 무엇인가요? 그 책을 좋아하는 이유도 들려주세요.',
  '요즘 가장 관심 있는 것은 무엇인가요? 최근의 고민을 나눠주셔도 좋아요.',
  '국내든 해외든, 꼭 가보고 싶은 곳은 어디인가요? 그곳에서 무엇을 하고 싶나요?',
  '즐겨 보는 유튜버나 인스타그래머가 있나요? 어떤 매력 때문에 보게 되나요?',
  '가장 좋아하는 음료와 음식은 무엇인가요? 나만의 추천 조합이 있나요?',
  '나를 한 단어로 표현한다면 무엇인가요? 그 단어를 고른 이유는요?',
  '지금 나 자신을 사랑하는 마음을 1~100점으로 표현한다면 몇 점인가요? 언제 그 마음이 커지나요?',
  '앞으로 꼭 이루고 싶은 삶의 목표는 무엇인가요?',
  '능력을 딱 하나 가질 수 있다면, 어떤 능력을 갖고 싶나요?',
  '심심하거나 혼자 시간이 생기면 주로 무엇을 하나요?',
  '나도 모르게 자주 하는 버릇이나 나만의 독특한 습관이 있나요?',
  '과거의 한 순간을 다시 경험할 수 있다면, 언제로 돌아가고 싶나요?',
  '내가 생각하는 나의 매력은 무엇인가요?',
  '주변 사람들은 나를 어떤 사람이라고 말하나요? 나도 그 말에 공감하나요?',
  '사람들에게 어떤 사람으로 기억되고 싶나요?',
]

const answers = ref(questions.map(() => ''))
const page = ref(0) // 0: 표지, 1~16: 질문, 17: 모아보기
const last = questions.length + 1
const coverVideo = `${import.meta.env.BASE_URL}cover.mp4`

const nextLabel = computed(() =>
  page.value === 0 ? '시작하기' : page.value === questions.length ? '모아보기' : '다음 질문'
)
</script>

<template>
  <section class="qna">
    <div class="viewport">
      <div class="track" :style="{ transform: `translateX(-${page * 100}%)` }">
        <!-- 표지 -->
        <div class="page cover" :inert="page !== 0">
          <video class="cover-video" :src="coverVideo" autoplay muted loop playsinline aria-hidden="true" />
          <h2 class="title">즉문즉답</h2>
          <p class="sub">{{ questions.length }}개의 질문, 떠오르는 대로 답해 주세요</p>
        </div>

        <!-- 질문 -->
        <div v-for="(q, i) in questions" :key="i" class="page" :inert="page !== i + 1">
          <span class="num">Q{{ i + 1 }} / {{ questions.length }}</span>
          <label :for="`a${i}`" class="question">{{ q }}</label>
          <textarea :id="`a${i}`" v-model="answers[i]" class="answer" placeholder="여기에 답을 적어 주세요" />
        </div>

        <!-- 모아보기 -->
        <div class="page summary" :inert="page !== last">
          <h2 class="title">나의 즉문즉답</h2>
          <details v-for="(q, i) in questions" :key="i" class="item">
            <summary>Q{{ i + 1 }}. {{ q }}</summary>
            <p class="answer-text">{{ answers[i] || '아직 답하지 않았어요' }}</p>
          </details>
        </div>
      </div>
    </div>

    <nav class="nav" :class="{ 'on-cover': page === 0 }">
      <button v-if="page > 0" class="btn ghost" @click="page--">이전 질문</button>
      <button v-if="page < last" class="btn" @click="page++">{{ nextLabel }}</button>
    </nav>
  </section>
</template>

<style>
:root {
  --green: #5F7652;
  --brown: #B58B68;
  --black: #303229;
  --ivory: #FFFDF7;
  --beige: #F1EBDD;
}
body { margin: 0; -webkit-text-size-adjust: 100%; background: var(--ivory); color: var(--black); }
</style>

<style scoped>
.qna {
  position: relative;
  width: 100%;
  background: var(--ivory);
  height: 100vh; /* dvh 미지원 브라우저용 */
  height: 100dvh;
  display: flex;
  flex-direction: column;
  overflow: hidden;
  font-family: 'Pretendard', sans-serif;
  font-weight: 500;
  -webkit-tap-highlight-color: transparent;
  touch-action: manipulation;
}
.viewport { flex: 1; min-height: 0; overflow: hidden; }
.track { display: flex; height: 100%; transition: transform 0.4s cubic-bezier(0.22, 1, 0.36, 1); }
@media (prefers-reduced-motion: reduce) { .track { transition: none; } }
.page {
  flex: 0 0 100%;
  box-sizing: border-box;
  height: 100%;
  /* 하단 여백 = 버튼 영역 높이 (버튼이 페이지 위에 떠 있음) */
  padding: 28px 20px calc(92px + env(safe-area-inset-bottom));
  display: flex;
  flex-direction: column;
  overflow-y: auto;
  -webkit-overflow-scrolling: touch;
  overscroll-behavior: contain;
}
.cover { position: relative; align-items: center; justify-content: flex-start; padding-top: 56px; text-align: center; background: var(--beige); }
.cover > :not(.cover-video) { position: relative; }
.cover-video { position: absolute; inset: 0; width: 100%; height: 100%; object-fit: cover; pointer-events: none; }
.title { margin: 0 0 20px; font-family: 'GumiRomance', sans-serif; font-weight: normal; font-size: 45px; color: var(--green); }
.sub { margin: 0; font-size: 20px; }
.num { margin-bottom:10px;color: var(--brown); font-size: 15px; }
.question { margin-bottom:20px;font-size: 20px; line-height: 1.5; word-break: keep-all; }
.answer {
  flex: 1;
  min-height: 160px;
  resize: none;
  padding: 16px;
  border: 2px solid var(--beige);
  border-radius: 16px;
  background: var(--beige);
  color: var(--black);
  font-family: 'OwnglyphYuntaeng', cursive;
  font-size: 22px; /* 16px 이상이어야 iOS 확대 안 됨 */
  line-height: 1.6;
  transition: border-color 0.2s;
}
.answer:focus { outline: none; border-color: var(--green); }
.answer::placeholder { color: var(--brown); }
.summary .title { margin-bottom:20px;font-size: 35px; text-align: center; }
.item {padding:5px 0; }
.item + .item{border-top: 1px solid var(--beige); }
.item summary {
  cursor: pointer;
  display: flex;
  gap: 12px;
  align-items: center;
  min-height: 50px;
  padding: 10px;
  border-radius: 12px;
  list-style: none;
  line-height: 1.5;
  word-break: keep-all;
  transition: background-color 0.2s, color 0.2s;
}
.item summary::-webkit-details-marker { display: none; }
.item summary::after { content: '+'; margin-left: auto; font-size: 20px; color: var(--brown); transition: transform 0.2s; }
.item[open] summary { color: var(--green); }
.item[open] summary::after { transform: rotate(45deg); }
.item summary:active { background: var(--beige); }
.answer-text {
  margin: 0 0 12px;
  padding: 12px 16px;
  border-radius: 12px;
  background: var(--beige);
  font-family: 'OwnglyphYuntaeng', cursive;
  font-size: 20px;
  white-space: pre-wrap;
}
.nav {
  position: absolute;
  left: 0;
  right: 0;
  bottom: 0;
  display: flex;
  gap: 10px;
  padding: 12px 20px calc(16px + env(safe-area-inset-bottom));
  background: var(--ivory);
  transition: background-color 0.4s;
}
.nav.on-cover { background: transparent; }
.btn {
  flex: 1;
  min-height: 52px;
  border: 2px solid var(--green);
  border-radius: 999px;
  background: var(--green);
  color: var(--ivory);
  font: inherit;
  font-size: 16px;
  cursor: pointer;
  transition: background-color 0.2s, border-color 0.2s, color 0.2s, transform 0.1s;
}
.btn.ghost { background: var(--ivory); color: var(--green); }
.btn:active { transform: scale(0.96); background: var(--black); border-color: var(--black); color: var(--ivory); }
.btn:focus-visible { outline: 2px solid var(--brown); outline-offset: 2px; }

/* 마우스 등 hover 가능한 기기에서만 — 터치에서 hover가 눌러붙는 것 방지 */
@media (hover: hover) {
  .btn:hover { background: var(--brown); border-color: var(--brown); color: var(--ivory); }
  .btn.ghost:hover { background: var(--beige); border-color: var(--green); color: var(--green); }
  .btn:active, .btn.ghost:active { background: var(--black); border-color: var(--black); color: var(--ivory); }
  .item summary:hover { background: var(--beige); }
  .answer:hover:not(:focus) { border-color: var(--brown); }
}
</style>
