[index.html](https://github.com/user-attachments/files/30030801/index.html)
<!DOCTYPE html>
<html lang="ko">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1">
<title>그 소문 들었어? — 소문 판별 대작전</title>
<style>
  @import url('https://fonts.googleapis.com/css2?family=Noto+Serif+KR:wght@500;700;900&family=Noto+Sans+KR:wght@400;500;700;900&display=swap');

  :root{
    --navy: #12162c;
    --navy2: #1c2140;
    --gold: #d9b44a;
    --gold-bright: #f2d17a;
    --silver: #b9c2cc;
    --danger: #d8574f;
    --safe: #4fae8a;
    --paper: #f4efe2;
  }
  *{ box-sizing: border-box; }
  html,body{
    margin:0; padding:0; height:100%;
    background: radial-gradient(circle at 50% -10%, var(--navy2), var(--navy) 60%);
    font-family: 'Noto Sans KR', sans-serif;
    color: var(--paper);
    overflow-x:hidden;
  }
  #app{
    min-height:100vh;
    display:flex; align-items:center; justify-content:center;
    padding: 24px;
    position:relative;
  }
  .screen{ width:100%; max-width:560px; display:none; }
  .screen.active{ display:block; }

  /* floating rumor bubbles ambient bg */
  .bubble{
    position:fixed; pointer-events:none;
    font-family:'Noto Serif KR', serif; font-weight:700;
    color: rgba(217,180,74,0.08);
    animation: rise linear infinite;
    white-space:nowrap;
  }
  @keyframes rise{
    0%{ transform: translateY(10vh) rotate(-3deg); opacity:0; }
    10%{ opacity:1; }
    90%{ opacity:1; }
    100%{ transform: translateY(-120vh) rotate(3deg); opacity:0; }
  }

  h1,h2,h3{ font-family:'Noto Serif KR', serif; margin:0; }

  /* ---- start screen ---- */
  .crest{
    text-align:center; margin-bottom: 28px;
  }
  .crest .lions{ font-size: 52px; letter-spacing: 14px; margin-bottom: 6px; }
  .eyebrow{
    text-align:center; letter-spacing:4px; font-size:12px; color: var(--silver);
    text-transform:uppercase; margin-bottom:10px;
  }
  .title-main{
    text-align:center; font-size: clamp(28px,7vw,40px); font-weight:900;
    color: var(--gold-bright); line-height:1.3;
    text-shadow: 0 0 24px rgba(242,209,122,0.35);
  }
  .subtitle{
    text-align:center; color: var(--silver); font-size:15px; margin-top:14px; line-height:1.6;
  }
  .rules-card{
    background: rgba(255,255,255,0.04); border:1px solid rgba(217,180,74,0.25);
    border-radius:18px; padding:20px 22px; margin-top:26px;
  }
  .rules-card div{ display:flex; gap:12px; font-size:14px; margin-bottom:10px; align-items:flex-start; }
  .rules-card div:last-child{ margin-bottom:0; }
  .rules-card .icon{ color: var(--gold); font-weight:900; flex-shrink:0; }

  .btn{
    display:block; width:100%; margin-top:28px;
    background: linear-gradient(180deg, var(--gold-bright), var(--gold));
    color: #241a05; font-weight:900; font-size:18px;
    border:none; border-radius:14px; padding:16px;
    cursor:pointer; letter-spacing:1px;
    box-shadow: 0 10px 30px rgba(217,180,74,0.25);
    transition: transform .15s ease;
  }
  .btn:active{ transform: scale(0.97); }
  .btn.secondary{
    background: rgba(255,255,255,0.06); color: var(--paper);
    border: 1px solid rgba(255,255,255,0.2); box-shadow:none;
  }

  /* ---- quiz screen ---- */
  .quiz-top{ display:flex; justify-content:space-between; align-items:center; margin-bottom:16px; }
  .q-count{ font-size:13px; color: var(--silver); letter-spacing:1px; }
  .q-count b{ color: var(--gold-bright); font-size:16px; }
  .score-chip{
    background: rgba(255,255,255,0.06); border-radius:20px; padding:6px 14px;
    font-size:13px; color: var(--gold-bright); font-weight:700;
  }

  .timer-wrap{
    height:10px; border-radius:6px; background: rgba(255,255,255,0.08);
    overflow:hidden; margin-bottom: 10px; position:relative;
  }
  .timer-bar{
    height:100%; width:100%; border-radius:6px;
    background: linear-gradient(90deg, var(--safe), var(--gold));
    transform-origin: left;
    transition: width 0.1s linear, background 0.3s ease;
  }
  .timer-num{
    text-align:right; font-size:12px; color: var(--silver); margin-bottom:22px;
  }
  .timer-num span{ color: var(--gold-bright); font-weight:900; font-size:14px; }

  .question-card{
    background: rgba(255,255,255,0.05); border:1px solid rgba(255,255,255,0.1);
    border-radius:20px; padding:34px 26px; min-height:150px;
    display:flex; align-items:center; justify-content:center; text-align:center;
    font-size: clamp(18px,4.6vw,22px); font-weight:700; line-height:1.6;
    box-shadow: 0 20px 40px rgba(0,0,0,0.25);
  }

  .ox-row{ display:flex; gap:16px; margin-top:26px; }
  .ox-btn{
    flex:1; padding: 28px 0; border-radius:18px; border:2px solid transparent;
    font-size: 40px; font-weight:900; cursor:pointer;
    transition: transform .12s ease, filter .12s ease;
  }
  .ox-btn.o{ background: rgba(79,174,138,0.15); border-color: var(--safe); color: var(--safe); }
  .ox-btn.x{ background: rgba(216,87,79,0.15); border-color: var(--danger); color: var(--danger); }
  .ox-btn:active{ transform: scale(0.95); }
  .ox-btn:disabled{ opacity:0.4; cursor:default; }

  /* ---- feedback overlay ---- */
  .feedback{
    text-align:center; margin-top:22px; font-size:15px; line-height:1.7;
    color: var(--silver); min-height: 24px;
  }
  .feedback b{ font-size:17px; }
  .feedback.correct b{ color: var(--safe); }
  .feedback.wrong b{ color: var(--danger); }

  /* ---- waiting screen ---- */
  .waiting-wrap{ text-align:center; }
  .waiting-badge{
    width:90px; height:90px; border-radius:50%; margin:0 auto 22px;
    display:flex; align-items:center; justify-content:center;
    font-size:38px; background: rgba(217,180,74,0.12); border:2px solid rgba(217,180,74,0.4);
  }
  .waiting-title{ font-size:22px; color: var(--gold-bright); font-weight:900; margin-bottom:8px; }
  .waiting-sub{ color: var(--silver); font-size:14px; margin-bottom:30px; line-height:1.6; }

  /* ---- end screen ---- */
  .end-score{ text-align:center; }
  .end-score .num{
    font-size: 64px; font-weight:900; color: var(--gold-bright);
    text-shadow: 0 0 30px rgba(242,209,122,0.4);
  }
  .end-score .den{ font-size:22px; color: var(--silver); }
  .end-msg{ text-align:center; margin-top:18px; font-size:15px; color: var(--paper); line-height:1.7; }

  @media (max-width: 400px){
    .ox-btn{ font-size:32px; padding:22px 0; }
    .question-card{ padding:26px 18px; }
  }
</style>
</head>
<body>

<div id="app">
  <!-- START SCREEN -->
  <div class="screen active" id="screen-start">
    <div class="crest">
      <div class="lions">🦁 　 🦁</div>
      <div class="eyebrow">동물 왕국 소문 판별 대작전</div>
      <div class="title-main">그 소문 들었어?</div>
      <div class="subtitle">금색 사자와 은색 사자, 그리고 왕국을 뒤흔든 소문 이야기.<br>내용을 얼마나 정확히 기억하고 있는지 확인해 보자!</div>
    </div>
    <div class="rules-card">
      <div><span class="icon">O·X</span> 문장을 읽고 맞으면 <b>O</b>, 틀리면 <b>X</b>를 눌러요.</div>
      <div><span class="icon">⏱</span> 한 문제당 생각할 시간은 <b>10초</b>! 시간이 지나면 자동으로 오답 처리돼요.</div>
      <div><span class="icon">🙋</span> 한 사람씩 순서대로 참여해요. 한 문제가 끝나면 다음 사람을 위한 <b>대기 화면</b>이 나와요.</div>
      <div><span class="icon">📜</span> 총 <b>25문제</b>, 소문이 얼마나 위험한지 함께 알아가요.</div>
    </div>
    <button class="btn" id="btn-start">도전 시작하기</button>
  </div>

  <!-- QUIZ SCREEN -->
  <div class="screen" id="screen-quiz">
    <div class="quiz-top">
      <div class="q-count">문제 <b id="q-index">1</b> / <span id="q-total">25</span></div>
      <div class="score-chip">맞힌 개수 <span id="q-score">0</span></div>
    </div>
    <div class="timer-wrap"><div class="timer-bar" id="timer-bar"></div></div>
    <div class="timer-num">남은 시간 <span id="timer-num">10</span>초</div>
    <div class="question-card" id="question-text">문제 내용</div>
    <div class="ox-row">
      <button class="ox-btn o" id="btn-o">O</button>
      <button class="ox-btn x" id="btn-x">X</button>
    </div>
    <div class="feedback" id="feedback"></div>
  </div>

  <!-- WAITING SCREEN -->
  <div class="screen" id="screen-waiting">
    <div class="waiting-wrap">
      <div class="waiting-badge">🕊️</div>
      <div class="waiting-title">다음 소문을 준비하고 있어요</div>
      <div class="waiting-sub">다음 참가자는 자리에 앉아 준비해 주세요.<br>준비가 되면 아래 버튼을 눌러 다음 문제를 시작하세요.</div>
      <button class="btn" id="btn-next">다음 문제 시작하기</button>
    </div>
  </div>

  <!-- END SCREEN -->
  <div class="screen" id="screen-end">
    <div class="eyebrow" style="text-align:center;">도전 결과</div>
    <div class="end-score">
      <span class="num" id="final-score">0</span><span class="den"> / 25</span>
    </div>
    <div class="end-msg" id="final-msg"></div>
    <button class="btn" id="btn-restart">처음부터 다시 도전하기</button>
  </div>
</div>

<script>
const questions = [
  {q:"금색 사자는 왕이 되고 싶어 했다.", a:"O"},
  {q:"은색 사자는 동물들에게 인기가 없었다.", a:"X"},
  {q:"금색 사자는 정정당당한 경쟁만으로 왕이 되려고 했다.", a:"X"},
  {q:"금색 사자는 은색 사자에 대한 소문을 퍼뜨렸다.", a:"O"},
  {q:"동물들은 소문의 진위를 꼼꼼히 확인한 뒤 다른 동물에게 전했다.", a:"X"},
  {q:"소문은 동물들 사이에서 빠르게 퍼져 나갔다.", a:"O"},
  {q:"은색 사자는 소문 때문에 피해를 입었다.", a:"O"},
  {q:"동물들은 소문을 전혀 믿지 않았다.", a:"X"},
  {q:"금색 사자는 소문 덕분에 왕이 되는 데 유리한 위치를 차지했다.", a:"O"},
  {q:"왕이 된 후 금색 사자는 훌륭한 통치로 나라를 더욱 발전시켰다.", a:"X"},
  {q:"이 이야기는 소문의 위험성을 보여 준다.", a:"O"},
  {q:"동물들은 처음부터 금색 사자의 거짓말을 알아차렸다.", a:"X"},
  {q:"소문은 사실 여부와 상관없이 사람들의 생각에 영향을 줄 수 있다.", a:"O"},
  {q:"은색 사자는 금색 사자에 대한 거짓 소문을 퍼뜨렸다.", a:"X"},
  {q:"이 책은 정보를 비판적으로 받아들이는 태도의 중요성을 이야기한다.", a:"O"},
  {q:"동물들이 사실을 확인하지 않은 것은 문제 해결에 도움이 되었다.", a:"X"},
  {q:"소문을 무심코 전달하는 행동도 문제를 키울 수 있다.", a:"O"},
  {q:"금색 사자의 행동은 정직한 리더의 모습이라고 볼 수 있다.", a:"X"},
  {q:"이 책은 SNS나 인터넷에서 정보를 접할 때에도 적용할 수 있는 교훈을 담고 있다.", a:"O"},
  {q:"『그 소문 들었어?』는 소문이 공동체에 어떤 영향을 미칠 수 있는지 보여 주는 이야기이다.", a:"O"},
  {q:"금색 사자가 퍼뜨린 소문은 모두 사실이었다.", a:"X"},
  {q:"소문은 처음 만들어질 때보다 전달되는 과정에서 더 큰 영향을 미칠 수 있다.", a:"O"},
  {q:"이 책의 교훈은 \"들은 이야기는 무조건 믿어야 한다\"이다.", a:"X"},
  {q:"거짓 소문 하나가 공동체 전체에 피해를 줄 수 있다.", a:"O"},
  {q:"사실 확인은 소문을 막는 데 도움이 된다.", a:"O"}
];

// shuffle helper - randomizes question order every time the game starts
let order = questions.map((_,i)=>i);
function shuffleOrder(){
  for(let i = order.length - 1; i > 0; i--){
    const j = Math.floor(Math.random() * (i + 1));
    [order[i], order[j]] = [order[j], order[i]];
  }
}

let current = 0;
let score = 0;
let timeLeft = 10;
let timerInterval = null;
const TOTAL_TIME = 10;

const screens = {
  start: document.getElementById('screen-start'),
  quiz: document.getElementById('screen-quiz'),
  waiting: document.getElementById('screen-waiting'),
  end: document.getElementById('screen-end'),
};
function showScreen(name){
  Object.values(screens).forEach(s=>s.classList.remove('active'));
  screens[name].classList.add('active');
}

const qIndexEl = document.getElementById('q-index');
const qTotalEl = document.getElementById('q-total');
const qScoreEl = document.getElementById('q-score');
const timerBar = document.getElementById('timer-bar');
const timerNum = document.getElementById('timer-num');
const questionText = document.getElementById('question-text');
const feedbackEl = document.getElementById('feedback');
const btnO = document.getElementById('btn-o');
const btnX = document.getElementById('btn-x');

qTotalEl.textContent = questions.length;

document.getElementById('btn-start').addEventListener('click', ()=>{
  current = 0; score = 0;
  shuffleOrder();
  showScreen('quiz');
  loadQuestion();
});

document.getElementById('btn-next').addEventListener('click', ()=>{
  current++;
  if(current >= questions.length){
    finishGame();
  } else {
    showScreen('quiz');
    loadQuestion();
  }
});

document.getElementById('btn-restart').addEventListener('click', ()=>{
  showScreen('start');
});

function loadQuestion(){
  const item = questions[order[current]];
  qIndexEl.textContent = current + 1;
  qScoreEl.textContent = score;
  questionText.textContent = item.q;
  feedbackEl.textContent = '';
  feedbackEl.className = 'feedback';
  btnO.disabled = false;
  btnX.disabled = false;
  timeLeft = TOTAL_TIME;
  updateTimerUI();
  clearInterval(timerInterval);
  timerInterval = setInterval(()=>{
    timeLeft -= 0.1;
    if(timeLeft <= 0){
      timeLeft = 0;
      updateTimerUI();
      clearInterval(timerInterval);
      handleAnswer(null, item.a);
    } else {
      updateTimerUI();
    }
  }, 100);
}

function updateTimerUI(){
  const pct = Math.max(0, (timeLeft / TOTAL_TIME) * 100);
  timerBar.style.width = pct + '%';
  timerNum.textContent = Math.ceil(timeLeft);
  if(pct < 30){
    timerBar.style.background = 'linear-gradient(90deg, var(--danger), #ff9d78)';
  } else if(pct < 60){
    timerBar.style.background = 'linear-gradient(90deg, var(--gold), var(--gold-bright))';
  } else {
    timerBar.style.background = 'linear-gradient(90deg, var(--safe), var(--gold))';
  }
}

btnO.addEventListener('click', ()=> handleAnswer('O', questions[order[current]].a));
btnX.addEventListener('click', ()=> handleAnswer('X', questions[order[current]].a));

function handleAnswer(selected, correctAnswer){
  clearInterval(timerInterval);
  btnO.disabled = true;
  btnX.disabled = true;

  const isCorrect = selected === correctAnswer;
  if(isCorrect){
    score++;
    qScoreEl.textContent = score;
    feedbackEl.className = 'feedback correct';
    feedbackEl.innerHTML = '<b>정답이에요!</b> 소문의 흐름을 정확히 짚어냈어요.';
  } else if(selected === null){
    feedbackEl.className = 'feedback wrong';
    feedbackEl.innerHTML = `<b>시간 초과!</b> 정답은 <b>${correctAnswer}</b>였어요.`;
  } else {
    feedbackEl.className = 'feedback wrong';
    feedbackEl.innerHTML = `<b>아쉬워요!</b> 정답은 <b>${correctAnswer}</b>였어요.`;
  }

  setTimeout(()=>{
    showScreen('waiting');
  }, 1400);
}

function finishGame(){
  document.getElementById('final-score').textContent = score;
  let msg = '';
  const total = questions.length;
  const ratio = score / total;
  if(ratio >= 0.9){
    msg = '완벽해요! 당신은 소문에 흔들리지 않고 이야기의 핵심을 정확히 꿰뚫어 보는 지혜로운 동물이에요.';
  } else if(ratio >= 0.7){
    msg = '훌륭해요! 소문의 위험성과 이야기의 교훈을 잘 이해하고 있어요.';
  } else if(ratio >= 0.5){
    msg = '절반 이상 맞혔어요. 다시 한번 이야기를 떠올리며 소문이 왜 위험한지 생각해 봐요.';
  } else {
    msg = '아직 소문에 쉽게 흔들릴 수 있어요. 이야기를 다시 읽고 도전해 보면 어떨까요?';
  }
  document.getElementById('final-msg').textContent = msg;
  showScreen('end');
}

// ambient floating rumor bubbles
const bubbleWords = ['소문', '진짜?', '들었어?', '???', '루머', '사실일까'];
function spawnBubble(){
  const el = document.createElement('div');
  el.className = 'bubble';
  el.textContent = bubbleWords[Math.floor(Math.random()*bubbleWords.length)];
  el.style.left = Math.random()*90 + 'vw';
  el.style.fontSize = (16 + Math.random()*22) + 'px';
  const dur = 14 + Math.random()*10;
  el.style.animationDuration = dur + 's';
  document.body.appendChild(el);
  setTimeout(()=> el.remove(), dur*1000);
}
setInterval(spawnBubble, 1800);
for(let i=0;i<4;i++) setTimeout(spawnBubble, i*500);
</script>
</body>
</html>
