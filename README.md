[ロゴ当てゲーム改良版１index.html](https://github.com/user-attachments/files/32339132/index.html)
<!DOCTYPE html>
<html lang="ja">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>飲食チェーン店 タイピングロゴクイズ</title>
  <style>
    body {
      font-family: 'Helvetica Neue', Arial, sans-serif;
      background-color: #f4f7f6;
      display: flex;
      justify-content: center;
      align-items: center;
      min-height: 100vh;
      margin: 0;
    }

    .quiz-container {
      background: white;
      padding: 30px;
      border-radius: 12px;
      box-shadow: 0 4px 15px rgba(0,0,0,0.1);
      text-align: center;
      max-width: 450px;
      width: 90%;
    }

    .header-bar {
      display: flex;
      justify-content: space-between;
      align-items: center;
      margin-bottom: 10px;
    }

    h1 {
      font-size: 20px;
      color: #333;
      margin: 0;
    }

    .timer {
      font-size: 18px;
      font-weight: bold;
      color: #d32f2f;
      background-color: #ffebee;
      padding: 4px 10px;
      border-radius: 6px;
    }

    .logo-img {
      width: 200px;
      height: 200px;
      object-fit: contain;
      border: 2px solid #eee;
      border-radius: 8px;
      padding: 10px;
      margin-bottom: 15px;
      background-color: #fff;
      /* ぼかし機能を完全に削除しました */
    }

    .input-group {
      display: flex;
      gap: 10px;
      margin-bottom: 10px;
    }

    input[type="text"] {
      flex: 1;
      padding: 12px;
      font-size: 16px;
      border: 2px solid #ccc;
      border-radius: 6px;
      outline: none;
    }

    input[type="text"]:focus {
      border-color: #4CAF50;
    }

    .btn {
      background-color: #4CAF50;
      color: white;
      border: none;
      padding: 12px 20px;
      font-size: 16px;
      font-weight: bold;
      border-radius: 6px;
      cursor: pointer;
    }

    .btn:hover {
      background-color: #45a049;
    }

    .giveup-btn {
      background-color: #757575;
      color: white;
      border: none;
      padding: 10px;
      font-size: 14px;
      border-radius: 6px;
      cursor: pointer;
      width: 100%;
      margin-bottom: 15px;
    }

    .giveup-btn:hover {
      background-color: #616161;
    }

    #result-message {
      font-size: 16px;
      font-weight: bold;
      min-height: 40px;
      margin-bottom: 15px;
      line-height: 1.4;
      white-space: pre-wrap;
    }

    .correct { color: #e91e63; }
    .wrong { color: #2196F3; }

    #next-btn, #restart-btn {
      background-color: #ff9800;
      width: 100%;
    }

    #next-btn:hover, #restart-btn:hover {
      background-color: #e68a00;
    }

    .hidden {
      display: none;
    }
  </style>
</head>
<body>

  <div class="quiz-container">
    <!-- クイズ画面 -->
    <div id="quiz-screen">
      <div class="header-bar">
        <h1 id="question-number">第 1 問</h1>
        <div id="timer" class="timer">05:00</div>
      </div>
      
      <img id="logo-image" class="logo-img" src="" alt="ロゴ画像">
      
      <form id="answer-form" class="input-group" onsubmit="submitAnswer(event)">
        <input type="text" id="user-input" placeholder="店名を入力（例: ガスト）" autocomplete="off" autofocus>
        <button type="submit" id="submit-btn" class="btn">回答</button>
      </form>

      <button id="giveup-btn" class="giveup-btn" onclick="giveUpAnswer()">パス（分からない）</button>

      <div id="result-message"></div>
      <button id="next-btn" class="btn hidden" onclick="nextQuestion()">次の問題へ</button>
    </div>

    <!-- 結果画面 -->
    <div id="result-screen" class="hidden">
      <h1 id="result-title">結果発表！</h1>
      <p id="score-text" style="font-size: 24px; font-weight: bold;"></p>
      <button id="restart-btn" class="btn" onclick="restartQuiz()">もう一度あそぶ</button>
    </div>
  </div>

  <script>
    // サイゼリヤとすき家のみ .jpeg に変更
    const masterQuizData = [
      { name: "ガスト", answers: ["ガスト", "がすと", "gusto"], image: "images/ガスト.webp" },
      { name: "すき家", answers: ["すき家", "すきや", "sukiya"], image: "images/すき家.jpeg" },
      { name: "サイゼリヤ", answers: ["サイゼリヤ", "さいぜりや", "サイゼ", "さいぜ"], image: "images/サイゼリヤ.jpeg" },
      { name: "ケンタッキーフライドチキン", answers: ["ケンタッキーフライドチキン", "ケンタッキー", "けんたっきー", "kfc"], image: "images/ケンタッキーフライドチキン.webp" },
      { name: "五右衛門パスタ", answers: ["五右衛門パスタ", "五右衛門", "ごえもん", "洋麺屋五右衛門"], image: "images/五右衛門パスタ.webp" },
      { name: "デニーズ", answers: ["デニーズ", "でにーず", "dennys", "denny's"], image: "images/デニーズ.webp" },
      { name: "しゃぶ葉", answers: ["しゃぶ葉", "しゃぶよう", "syabuyo"], image: "images/しゃぶ葉.webp" },
      { name: "餃子の王将", answers: ["餃子の王将", "ぎょうざのおうしょう", "王将", "おうしょう"], image: "images/餃子の王将.webp" },
      { name: "ロッテリア", answers: ["ロッテリア", "ろってりあ", "lotteria"], image: "images/ロッテリア.webp" },
      { name: "バーミヤン", answers: ["バーミヤン", "ばーみやん", "bamiyan"], image: "images/バーミヤン.webp" },
      { name: "リンガーハット", answers: ["リンガーハット", "りんがーはっと", "ringerhut"], image: "images/リンガーハット.webp" },
      { name: "モスバーガー", answers: ["モスバーガー", "もすばーがー", "モス", "もす"], image: "images/モスバーガー.webp" },
      { name: "松屋", answers: ["松屋", "まつや", "matsuya"], image: "images/松屋.webp" },
      { name: "ビッグボーイ", answers: ["ビッグボーイ", "びっぐぼーい", "bigboy"], image: "images/ビッグボーイ.webp" },
      { name: "ジョイフル", answers: ["ジョイフル", "じょいふる", "joyfull"], image: "images/ジョイフル.webp" },
      { name: "バーガーキング", answers: ["バーガーキング", "ばーがーきんぐ", "バガキング", "burgerking"], image: "images/バーガーキング.webp" },
      { name: "吉野家", answers: ["吉野家", "よしのや", "yoshinoya"], image: "images/吉野家.webp" },
      { name: "かっぱ寿司", answers: ["かっぱ寿司", "かっぱずし", "カッパ寿司"], image: "images/かっぱ寿司.webp" },
      { name: "びっくりドンキー", answers: ["びっくりドンキー", "びっくりどんきー", "ビクドン", "びくどん"], image: "images/びっくりドンキー.webp" },
      { name: "フレッシュネスバーガー", answers: ["フレッシュネスバーガー", "ふれっしゅねすばーがー", "フレッシュネス", "freshnessburger"], image: "images/フレッシュネスバーガー.webp" },
      { name: "はなまるうどん", answers: ["はなまるうどん", "はなまる", "ハナマルウドン"], image: "images/はなまるうどん.webp" },
      { name: "資さんうどん", answers: ["資さんうどん", "すけさんうどん", "資さん", "すけさん"], image: "images/資さんうどん.webp" }
    ];

    const TOTAL_QUESTIONS = 15;
    const TIME_LIMIT_SECONDS = 300; // 制限時間：5分（300秒）

    let currentQuizSet = [];
    let currentQuestionIndex = 0;
    let score = 0;
    let timerInterval = null;
    let timeRemaining = TIME_LIMIT_SECONDS;

    const questionNumberEl = document.getElementById("question-number");
    const timerEl = document.getElementById("timer");
    const logoImageEl = document.getElementById("logo-image");
    const userInputEl = document.getElementById("user-input");
    const submitBtn = document.getElementById("submit-btn");
    const giveupBtn = document.getElementById("giveup-btn");
    const resultMessageEl = document.getElementById("result-message");
    const nextBtn = document.getElementById("next-btn");
    const quizScreen = document.getElementById("quiz-screen");
    const resultScreen = document.getElementById("result-screen");
    const resultTitleEl = document.getElementById("result-title");
    const scoreTextEl = document.getElementById("score-text");

    function initGame() {
      currentQuestionIndex = 0;
      score = 0;
      timeRemaining = TIME_LIMIT_SECONDS;

      const shuffled = [...masterQuizData].sort(() => 0.5 - Math.random());
      currentQuizSet = shuffled.slice(0, TOTAL_QUESTIONS);

      startTimer();
      loadQuestion();
    }

    // 制限時間タイマーの設定
    function startTimer() {
      clearInterval(timerInterval);
      updateTimerDisplay();

      timerInterval = setInterval(() => {
        timeRemaining--;
        updateTimerDisplay();

        if (timeRemaining <= 0) {
          clearInterval(timerInterval);
          finishGameByTimeout();
        }
      }, 1000);
    }

    function updateTimerDisplay() {
      const minutes = Math.floor(timeRemaining / 60);
      const seconds = timeRemaining % 60;
      timerEl.textContent = `${String(minutes).padStart(2, '0')}:${String(seconds).padStart(2, '0')}`;
    }

    function loadQuestion() {
      resultMessageEl.textContent = "";
      resultMessageEl.className = "";
      nextBtn.classList.add("hidden");
      submitBtn.disabled = false;
      giveupBtn.disabled = false;
      userInputEl.disabled = false;
      userInputEl.value = "";
      userInputEl.focus();

      const currentQuiz = currentQuizSet[currentQuestionIndex];
      questionNumberEl.textContent = `第 ${currentQuestionIndex + 1} 問 / 全 ${TOTAL_QUESTIONS} 問`;
      logoImageEl.src = currentQuiz.image;
    }

    function submitAnswer(event) {
      event.preventDefault();

      const inputVal = userInputEl.value.trim().toLowerCase();
      if (!inputVal) return;

      const currentQuiz = currentQuizSet[currentQuestionIndex];

      submitBtn.disabled = true;
      giveupBtn.disabled = true;
      userInputEl.disabled = true;

      const isCorrect = currentQuiz.answers.some(ans => 
        ans.toLowerCase().replace(/\s+/g, '') === inputVal.replace(/\s+/g, '')
      );

      if (isCorrect) {
        resultMessageEl.textContent = "⭕ 正解！";
        resultMessageEl.className = "correct";
        score++;
      } else {
        resultMessageEl.textContent = `❌ 不正解...\n正解は「${currentQuiz.name}」でした`;
        resultMessageEl.className = "wrong";
      }

      nextBtn.classList.remove("hidden");
      nextBtn.focus();
    }

    // 「パス（分からない）」ボタンを押した処理
    function giveUpAnswer() {
      const currentQuiz = currentQuizSet[currentQuestionIndex];

      submitBtn.disabled = true;
      giveupBtn.disabled = true;
      userInputEl.disabled = true;

      resultMessageEl.textContent = `❌ 不正解（パス）\n正解は「${currentQuiz.name}」でした`;
      resultMessageEl.className = "wrong";

      nextBtn.classList.remove("hidden");
      nextBtn.focus();
    }

    function nextQuestion() {
      currentQuestionIndex++;
      if (currentQuestionIndex < TOTAL_QUESTIONS) {
        loadQuestion();
      } else {
        clearInterval(timerInterval);
        showResults("結果発表！");
      }
    }

    // タイムオーバー時の強制終了
    function finishGameByTimeout() {
      submitBtn.disabled = true;
      giveupBtn.disabled = true;
      userInputEl.disabled = true;
      showResults("⏰ タイムアップ！");
    }

    function showResults(title) {
      quizScreen.classList.add("hidden");
      resultScreen.classList.remove("hidden");
      resultTitleEl.textContent = title;
      scoreTextEl.textContent = `${TOTAL_QUESTIONS} 問中 ${score} 問 正解！`;
    }

    function restartQuiz() {
      resultScreen.classList.add("hidden");
      quizScreen.classList.remove("hidden");
      initGame();
    }

    initGame();
  </script>
</body>
</html>
