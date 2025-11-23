<!DOCTYPE html>
< lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>319分の1 抽選ゲーム</title>
    <style>
        /* --- 全体デザイン --- */
        body {
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            min-height: 100vh;
            margin: 0;
            background-color: #f0f0f0;
            font-family: 'Arial', sans-serif;
        }
        /* ゲーム画面とスタート画面のコンテナ */
        #start-screen,
        #game-container {
            background-color: #ffffff;
            padding: 40px;
            border-radius: 15px;
            box-shadow: 0 4px 15px rgba(0, 0, 0, 0.2);
            text-align: center;
            width: 300px;
            box-sizing: border-box; /* paddingを含めた幅にする */
        }

        /* --- スタート画面のデザイン --- */
        #start-screen {
            background-color: #333333; /* 暗い背景色 */
            color: white;
            border: 5px solid #ffcc00; /* 金色の枠 */
            display: flex;
            flex-direction: column;
            gap: 20px;
        }
        #start-screen h1 {
            color: #ffcc00;
            font-size: 1.8em; /* サイズを調整 */
            margin-bottom: 5px;
        }
        #start-screen p.subtitle {
            font-size: 1.2em;
            font-weight: bold;
            color: #ffcc00; /* 強調色 */
            margin-top: 0;
            margin-bottom: 25px;
        }
        #start-button {
            padding: 15px 30px;
            font-size: 1.2em;
            color: #333;
            background-color: #ffcc00;
            border: none;
            border-radius: 8px;
            cursor: pointer;
            font-weight: bold;
            transition: background-color 0.1s ease;
        }
        #start-button:hover {
            background-color: #e6b800;
        }

        /* --- 抽選ゲーム画面のデザイン（既存） --- */
        #game-container {
            display: none; /* 初期状態では非表示 */
        }
        #counter-display {
            margin-bottom: 20px;
            padding: 10px;
            border: 1px solid #ccc;
            border-radius: 5px;
            background-color: #f9f9f9;
            font-size: 1.1em;
            text-align: left;
        }
        .count-item {
            margin: 5px 0;
        }
        #result {
            font-size: 2em;
            font-weight: bold;
            height: 50px;
            margin-top: 20px;
            color: #333;
        }
        #firework-button {
            padding: 15px 30px;
            font-size: 1.5em;
            color: white;
            background-color: #ff4500;
            border: none;
            border-radius: 8px;
            cursor: pointer;
            transition: background-color 0.1s ease, opacity 0.3s ease;
        }
        #firework-button:hover {
            background-color: #cc3700;
        }
        /* ボタン無効時のスタイル */
        #firework-button:disabled {
            background-color: #cccccc;
            cursor: not-allowed;
            opacity: 0.6;
        }
        .success {
            color: #008000;
            animation: fireworks-effect 0.1s ease-in-out 5 alternate;
        }
        @keyframes fireworks-effect {
            from { transform: scale(1.0); }
            to { transform: scale(1.1); color: #ff00ff;}
        }
    </style>
</head>
<body>
    <div id="start-screen">
        <h1>準備はいいか塚田淳！！</h1>
        <p class="subtitle">319分の1 抽選ゲーム</p>
        <p>大当たり確率 $1/319$ の運命を、今、決める時！</p>
        <button id="start-button">
            ゲームを始める
        </button>
    </div>
    <div id="game-container">
        <div id="counter-display">
            <div class="count-item">試行回数: <span id="attempt-count">0</span>回</div>
            <div class="count-item">当たり回数: <span id="win-count">0</span>回</div>
        </div>

        <h1>319分の1 抽選ゲーム</h1>
        <p>ボタンを押して、花火を打ち上げよう！</p>
        <button id="firework-button">
            🚀 ボタンを押す
        </button>
        <div id="result">
            結果待ち...
        </div>
    </div>
    <script>
        // --- ロジック（JavaScript） ---
        // 画面要素の取得
        const startScreen = document.getElementById('start-screen');
        const gameContainer = document.getElementById('game-container');
        const startButton = document.getElementById('start-button');
        const button = document.getElementById('firework-button');
        const resultDisplay = document.getElementById('result');
        const attemptCountDisplay = document.getElementById('attempt-count');
        const winCountDisplay = document.getElementById('win-count');

        const WIN_NUMBER = 1;    
        const MAX_NUMBER = 319;  
        const WAIT_TIME = 4100;  
       
        // カウンター変数の初期化
        let attemptCount = 0;
        let winCount = 0;

        // カウンター表示を更新する関数
        function updateCounters() {
            attemptCountDisplay.textContent = attemptCount;
            winCountDisplay.textContent = winCount;
        }
       
        // 初回表示の更新
        updateCounters();
       
        // 🌟 スタートボタンのイベントリスナー（画面切り替え処理） 🌟
        startButton.addEventListener('click', () => {
            // スタート画面を非表示にし、ゲーム画面を表示する
            startScreen.style.display = 'none';
            gameContainer.style.display = 'block';
            // game-containerがflexboxで中央揃えされているため、blockでも問題ありません。
        });

        button.addEventListener('click', () => {
            // **試行回数をインクリメント**
            attemptCount++;
            updateCounters();

            // 1. ボタンを無効化
            button.disabled = true;
           
            // 2. ウェイト中であることを表示
            resultDisplay.textContent = '⏱️ 抽選中...しばらくお待ちください。';
            resultDisplay.classList.remove('success');

            // 3. 1から319までのランダムな整数を生成
            const randomNumber = Math.floor(Math.random() * MAX_NUMBER) + 1;
           
            console.log(`生成された数字: ${randomNumber}`);

            // 4. 4.1秒後に結果を表示し、ボタンを有効化する
            setTimeout(() => {
                // 4-1. 当たり判定（条件分岐）
                if (randomNumber === WIN_NUMBER) {
                    // 当たりの場合
                   
                    // **当たり回数をインクリメント**
                    winCount++;
                    updateCounters();

                    resultDisplay.textContent = '🎉 大当たり！花火が打ち上がった！ 🎉';
                    resultDisplay.classList.add('success');
                    // アラートで特別感
                    alert("✨ 祝！花火大当たり！ ✨");
                } else {
                    // ハズレの場合
                    resultDisplay.textContent = `ハズレ... (出目: ${randomNumber}) また挑戦しよう。`;
                    resultDisplay.classList.remove('success');
                }
               
                // 4-2. ボタンを有効化
                button.disabled = false;

            }, WAIT_TIME);
        });
    </script>
</body>
</html>
