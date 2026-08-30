<!DOCTYPE html>
<html lang="ja">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>TOKIO 100問サバイバルクイズ</title>
  <script src="https://cdn.tailwindcss.com"></script>
  <style>
    body { font-family: 'Helvetica Neue', Arial, 'Hiragino Kaku Gothic ProN', 'Hiragino Sans', Meiryo, sans-serif; }
    .flash-red { animation: flashR 0.5s; }
    @keyframes flashR { 0% { background-color: #ef4444; } 100% { background-color: transparent; } }
  </style>
</head>
<body class="bg-gray-900 text-gray-100 min-h-screen flex flex-col items-center justify-center p-4">

  <div id="app" class="w-full max-w-lg bg-gray-800 rounded-2xl shadow-2xl p-6 border border-gray-700 relative overflow-hidden">
    
    <!-- Start Screen -->
    <div id="start-screen" class="text-center space-y-6 py-8">
      <h1 class="text-3xl font-extrabold text-transparent bg-clip-text bg-gradient-to-r from-red-500 to-yellow-500">
        TOKIO 100問<br>サバイバルクイズ
      </h1>
      <p class="text-gray-400 text-sm">
        パチンコ「TOKIO」シリーズの歴史・物理法則・運用データから全100問を出題。<br><br>
        <span class="text-red-400 font-bold">1問でも間違えれば即ゲームオーバー。</span><br>
        順番は毎回シャッフルされます。完全制覇を目指せ！
      </p>
      <button onclick="startGame()" class="w-full bg-red-600 hover:bg-red-500 text-white font-bold py-4 rounded-xl transition shadow-[0_0_15px_rgba(220,38,38,0.5)] active:scale-95">
        サバイバル開始
      </button>
    </div>

    <!-- Quiz Screen -->
    <div id="quiz-screen" class="hidden flex flex-col space-y-6">
      <div class="flex justify-between items-end border-b border-gray-700 pb-2">
        <span class="text-sm text-gray-400">SURVIVAL CHALLENGE</span>
        <span id="progress" class="text-xl font-bold text-yellow-400">1 / 100</span>
      </div>
      <div id="question-text" class="text-lg font-medium leading-relaxed min-h-[5rem]">
        <!-- Question goes here -->
      </div>
      <div id="options" class="flex flex-col space-y-3">
        <!-- Options go here -->
      </div>
    </div>

    <!-- Game Over Screen -->
    <div id="game-over-screen" class="hidden text-center space-y-6 py-8">
      <h2 class="text-4xl font-black text-red-500 tracking-wider">GAME OVER</h2>
      <p class="text-xl">記録: <span id="final-score" class="font-bold text-yellow-400 text-3xl">0</span> 問連続正解</p>
      <p id="mistake-reason" class="text-sm text-gray-400 p-4 bg-gray-900 rounded-lg border border-gray-700">
        <!-- Mistake explanation -->
      </p>
      <button onclick="startGame()" class="w-full bg-gray-700 hover:bg-gray-600 text-white font-bold py-4 rounded-xl transition active:scale-95">
        最初からやり直す
      </button>
    </div>

    <!-- Clear Screen -->
    <div id="clear-screen" class="hidden text-center space-y-6 py-8">
      <h2 class="text-4xl font-black text-transparent bg-clip-text bg-gradient-to-r from-yellow-300 to-yellow-600">
        CONGRATULATIONS!
      </h2>
      <p class="text-xl font-bold">100問連続正解達成！</p>
      <p class="text-gray-300">あなたは完璧なTOKIOマスターです。ホールの釘とネカセを見極める力が完全に備わっています。</p>
      <button onclick="startGame()" class="w-full bg-blue-600 hover:bg-blue-500 text-white font-bold py-4 rounded-xl transition active:scale-95">
        もう一度遊ぶ
      </button>
    </div>

  </div>

  <script>
    // 100 Questions generated from the document
    const qData = [
      {q:"TOKIOシリーズのカテゴリーはどれ？", o:["羽根物", "デジパチ", "一発台", "権利物"], a:0},
      {q:"羽根物は1980年代〜1990年代にかけてパチンコホールの主役だった。", o:["○", "×"], a:0},
      {q:"デジパチのスペック進化に伴い、羽根物の設置比率はどうなった？", o:["増加した", "減衰した", "変わらなかった", "独占状態になった"], a:1},
      {q:"初代『CRA TOKIO DELUXE』の導入年月日は？", o:["2010年5月5日", "2012年7月2日", "2015年1月1日", "2018年8月8日"], a:1},
      {q:"TOKIOシリーズを開発した「株式会社アムテックス」の親会社はどこ？", o:["サミー", "三洋物産", "平和", "京楽"], a:2},
      {q:"TOKIOシリーズの役物のモチーフとなっている建造物は？", o:["東京タワー", "スカイツリー", "通天閣", "京都タワー"], a:0},
      {q:"羽根の開放契機となるのは何への入賞？", o:["V穴", "スルーチャッカー", "1・2チャッカー", "アタッカー"], a:2},
      {q:"デジパチの内部抽選は「確率論的なブラックボックス」である。", o:["○", "×"], a:0},
      {q:"TOKIOのV入賞率に反映されないものはどれ？", o:["役物の製造精度", "設置角度(ネカセ)", "釘調整", "液晶のリーチ演出"], a:3},
      {q:"役物構造の上部に位置する領域の名称は？", o:["ミニタワー", "中央タワー", "クルーン", "リフト"], a:0},
      {q:"役物構造の下部に位置する領域の名称は？", o:["ミニタワー", "中央タワー", "バンパー", "ハカマ"], a:1},
      {q:"ミニタワーの中央を正面から突破するルートの名称は？", o:["ノーマルルート", "直Vルート", "スペシャル(SP)ルート", "プレミアムルート"], a:2},
      {q:"SPルートの玉が経由するギミックは？", o:["可変スロープ", "バンパー", "1穴クルーン", "エレベーター"], a:2},
      {q:"1穴クルーンを経由した玉はどこへ落下する？", o:["回転体の真上", "中央タワーの側面", "下部ステージ", "リフトの中段"], a:0},
      {q:"SPルートからのV入賞確率はどのくらい？", o:["約1/10", "約1/2〜1/3", "ほぼ100%", "約1%"], a:1},
      {q:"ミニタワーで弾かれた玉などが辿る難所のルートは？", o:["ノーマルルート", "SPルート", "こぼしルート", "リフトルート"], a:0},
      {q:"ノーマルルートに進入した玉が接触しないものはどれ？", o:["可変式スロープ", "タワー壁面", "バンパー", "1穴クルーン"], a:3},
      {q:"可変スロープに乗って一直線にV穴へ向かう挙動の名称は？", o:["波乗り挙動", "中ゆら挙動", "直V挙動", "落下挙動"], a:2},
      {q:"障害物を回避し後ろから波に乗るようにV穴に滑り込む挙動は？", o:["直V挙動", "波乗り挙動", "中ゆら挙動", "クルーン挙動"], a:1},
      {q:"くぼみで滞留し左右に揺れてV穴へ落ちる挙動(粘り入賞)は？", o:["直V挙動", "波乗り挙動", "中ゆら挙動", "弾かれ挙動"], a:2},
      {q:"台の設置角度(傾斜)を現場用語で何と呼ぶ？", o:["ブッコミ", "ハカマ", "ネカセ", "ヨリ"], a:2},
      {q:"台が垂直に近い状態のネカセを何と呼ぶ？", o:["寝かせ", "起こし", "平行", "逆ネカセ"], a:1},
      {q:"ネカセが「起こし」の状態の時、SPルート通過率はどうなる傾向がある？", o:["低下する", "変化しない", "上昇する", "0になる"], a:2},
      {q:"「起こし」状態の時、下部ステージに落下した玉はどうなりやすい？", o:["滞留しやすい", "回転体を飛び越えやすい", "確実にV穴に入る", "リフトへ直行する"], a:1},
      {q:"台が手前側に傾いている状態のネカセを何と呼ぶ？", o:["起こし", "寝かせ", "スロープ", "ブッコミ"], a:1},
      {q:"ネカセが「寝かせ」の状態の時、玉の移動速度はどうなる？", o:["全体的に上昇する", "全体的に低下する", "完全に停止する", "変則的になる"], a:1},
      {q:"「寝かせ」状態の時、ノーマルルートの玉はどうなりやすい？", o:["滞留しやすくなる", "回転体を飛び越えやすくなる", "バンパーに強く弾かれる", "V穴を完全に避ける"], a:0},
      {q:"V入賞した玉が運ばれる、役物右側に配置された機構は？", o:["アタッカー", "1穴クルーン", "エレベーターリフト", "ハカマ"], a:2},
      {q:"エレベーターリフトの役割は？", o:["羽根を開放する", "ラウンド数を物理的に決定する", "出玉を払い出す", "V入賞の再抽選を行う"], a:1},
      {q:"リフト中段ステージにある穴の文字・数字は？", o:["UP!と3", "UP!と7", "5と10", "7と16"], a:0},
      {q:"中段の「3」の穴に入ると実質何R大当りとなる？", o:["1R", "2R", "3R", "16R"], a:1},
      {q:"玉がさらに上部へ引き揚げられるのはどの穴に入った時？", o:["3", "7", "16", "UP!"], a:3},
      {q:"上段ステージに到達した玉が向かう穴の組み合わせとして正しいのは？", o:["3と7", "7と16 または 5と10", "15と16", "UP!とV"], a:1},
      {q:"TOKIOのラウンド決定機構は単なるデジタル演出である。", o:["○", "×"], a:1},
      {q:"初代『TOKIO DELUXE』の大当り消化アタッカーは盤面のどこにあった？", o:["盤面上部", "盤面下部", "盤面右側", "盤面左側"], a:0},
      {q:"初代の盤面上部にあったアタッカー構造を何と呼ぶ？", o:["下アタッカー", "羽根アタッカー", "スルーアタッカー", "Vアタッカー"], a:1},
      {q:"『CRA TOKIO PREMIUM』の導入年は？", o:["2012年", "2015年", "2018年", "2021年"], a:1},
      {q:"『トキオプレミアム』で採用された独自の振り分け機構は？", o:["エレベーター機構", "ブンブン丸機構", "クルーン機構", "パトランプ機構"], a:1},
      {q:"『トキオプレミアム』の1チャッカー開放時間は何秒だった？", o:["0.160秒", "0.364秒", "0.524秒", "1.000秒"], a:1},
      {q:"『CR TOKIO Special』の導入年は？", o:["2015年", "2018年", "2019年", "2021年"], a:1},
      {q:"『トキオスペシャル』で変更されたアタッカーの位置は？", o:["盤面上部", "盤面下部", "役物内部", "右打ち専用口"], a:1},
      {q:"アタッカーを盤面下部へ移設した目的の一つは「大当り消化時間の短縮」である。", o:["○", "×"], a:0},
      {q:"アタッカー下部移設により、技術介入(止め打ち等)による獲得出玉の上乗せ効果はどうなった？", o:["大幅に拡大した", "大幅に制限された", "変わらなかった", "必須になった"], a:1},
      {q:"『トキオスペシャル』の1チャッカー開放時間は何秒になった？", o:["0.364秒", "0.524秒", "0.800秒", "1.000秒"], a:1},
      {q:"『トキオスペシャル』で1チャッカー開放時間が延長された理由は？", o:["拾い率の極端な低下を防ぐため", "V入賞率を2倍にするため", "音楽を長く聞かせるため", "出玉を減らすため"], a:0},
      {q:"『Pトキオブラック4500』の導入年月日は？", o:["2015年1月1日", "2018年5月5日", "2019年10月7日", "2021年6月21日"], a:2},
      {q:"『Pトキオブラック4500』のスペックの特徴は？", o:["遊べる甘デジ", "一撃出玉特化型", "ラウンド振り分け均等", "完全デジタル抽選"], a:1},
      {q:"『Pトキオブラック4500』の初回V入賞で獲得できる出玉は約何個？", o:["約1000個", "約2000個", "約4500個", "約9000個"], a:2},
      {q:"『Pトキオブラック4500』の大当りセット数は？", o:["3R×10セット", "5R×5セット", "10R×4セット", "16R×2セット"], a:2},
      {q:"『Pトキオブラック4500』の初回V入賞後の打ち方は？", o:["左打ち", "右打ち", "止め打ち", "ハンドル固定"], a:1},
      {q:"『Pトキオブラック4500』の右打ち中、役物内のタワーはどうなる？", o:["高速回転する", "逆回転する", "上下に動く", "回転が停止する"], a:3},
      {q:"『Pニュートキオ』が対応している遊技機規格は？", o:["4号機", "5号機", "6号機P機", "スマートパチンコ"], a:2},
      {q:"『Pニュートキオ』の導入年月日は？", o:["2019年10月7日", "2021年6月21日", "2023年1月1日", "2024年2月5日"], a:1},
      {q:"『Pニュートキオ V1』は何タイプと呼ばれる？", o:["ヘソタイプ", "ハカマタイプ", "電チュータイプ", "直撃タイプ"], a:1},
      {q:"『Pニュートキオ H1』は何タイプと呼ばれる？", o:["ヘソタイプ", "ハカマタイプ", "スルータイプ", "右打ちタイプ"], a:0},
      {q:"『Pニュートキオ』の最大ラウンド数は？", o:["7R", "10R", "15R", "16R"], a:1},
      {q:"『Pニュートキオ』のラウンド振り分け（3R:5R:10R）の比率は？", o:["均等に各1/3", "5:3:2", "2:3:5", "10Rが50%"], a:0},
      {q:"『Pニュートキオ V1』の賞球数は？", o:["3 & 10", "4 & 14", "5 & 14", "7 & 15"], a:2},
      {q:"『Pニュートキオ』の10R大当りの出玉設計値は約何個？", o:["504個", "1134個", "2000個", "4500個"], a:1},
      {q:"『Pニュートキオ』の3R大当りの出玉設計値は約何個？", o:["252個", "504個", "1134個", "2000個"], a:0},
      {q:"『PニュートキオGREEN』の導入年月日は？", o:["2021年6月21日", "2022年8月8日", "2023年12月1日", "2024年2月5日"], a:3},
      {q:"『PニュートキオGREEN』で採用された新筐体枠の名称は？", o:["トマト枠", "ライム枠", "パプリカ枠", "ピーマン枠"], a:2},
      {q:"パチンコ羽根物の勝率・出玉期待値は回転率(スタート回数)のみで決まる。", o:["○", "×"], a:1},
      {q:"羽根物の期待値は「拾い率」と何の掛け合わせで決まる？", o:["確変突入率", "継続率", "役物確率", "リーチ信頼度"], a:2},
      {q:"盤面左上の玉を打ち出す目標となる釘領域を何と呼ぶ？", o:["ハカマ", "ブッコミ", "ヨリ", "道釘"], a:1},
      {q:"ニュートキオ以降、ブッコミ領域には何製のパーツが採用されている？", o:["ガラス製", "木製", "プラスチック製", "金属製のみ"], a:2},
      {q:"ブッコミにプラスチックパーツが採用されたことで、ストローク微調整による強引な挙動変化はどうなった？", o:["非常に容易になった", "困難になった", "完全に不可能になった", "変わらない"], a:1},
      {q:"羽根横の急所釘がマイナス調整（外向き）に叩かれていると、玉はどうなりやすい？", o:["羽根に吸い込まれる", "V穴へ直行する", "こぼしルートへ逃げる", "役物内で滞留する"], a:2},
      {q:"1チャッカーの上部に位置する、複数本の釘で構成された構造の名称は？", o:["ブッコミ", "クルーン", "ハカマ", "ワープ"], a:2},
      {q:"ハカマの中心軸と1チャッカーの開口位置の関係は？", o:["完全に一致している", "やや左側へオフセット", "やや右側へオフセット", "毎回変動する"], a:2},
      {q:"見栄えを良くするため、ハカマの左側の釘だけを大きく開けるホールの罠調整を何と呼ぶ？", o:["サービス調整", "ダマシ調整", "起こし調整", "平行調整"], a:1},
      {q:"ハカマのダマシ調整が施されている場合、左から流れてきた玉はどうなりやすい？", o:["1チャッカーに確実に入る", "3本目の釘に弾かれて落下する", "2チャッカーへ向かう", "羽根に直接入る"], a:1},
      {q:"ハカマ周辺の評価において絶対的な条件となるのは？", o:["左の釘が大きく開いているか", "右側の釘が開いているか(または均等か)", "釘が全て上を向いているか", "釘が全くないか"], a:1},
      {q:"羽根に拾われた玉が最終的にV入賞に達する確率を何と呼ぶ？", o:["大当り確率", "確変確率", "役物確率", "突破率"], a:2},
      {q:"TOKIOシリーズにおける役物確率の理論的な優秀値はどの程度？", o:["1/2〜1/3", "1/10〜1/13", "1/18〜1/20", "1/50"], a:1},
      {q:"アクリルパーツの磨耗やネカセの悪さにより、癖の悪い台の役物確率はどの程度まで低下する？", o:["1/5", "1/10", "1/18〜1/20", "1/100"], a:2},
      {q:"役物確率の良し悪しを判定する際、プロ的な分析手法として観察すべきはどこからのV決定率？", o:["SPルート", "ノーマルルート", "1チャッカー", "リフト"], a:1},
      {q:"標準的な台において、ノーマルルートからのV入賞率は拾い数の約何%程度？", o:["0.1%", "3%", "10%", "30%"], a:1},
      {q:"役物癖やネカセが絶妙な台では、ノーマルルート単体で何%のV決定率を記録することがある？", o:["1%", "3%", "6%〜7%", "20%"], a:2},
      {q:"ノーマルルートからの加算確率は、全体のV入賞率を押し上げる最大の要因とはならない。", o:["○", "×"], a:1},
      {q:"羽根物において、大当り乱数をストックする「保留」機能は基本的にどうなっている？", o:["8個まで貯まる", "4個まで貯まる", "存在しない(または極めて少ない)", "無限に貯まる"], a:2},
      {q:"保留が存在しない仕様を突いた、無駄玉を排除する技術介入を何と呼ぶ？", o:["捻り打ち", "止め打ち", "ぶっこみ狙い", "チョロ打ち"], a:1},
      {q:"止め打ちを行う際、打ち出しを停止する合図となる効果音はどれ？", o:["キュイン！", "ピロリロ！", "ピン！", "ブブー！"], a:2},
      {q:"通常時の打ち球に対する払い出し・維持効率のことを何と呼ぶ？", o:["ボーダー", "ベース", "出玉率", "機械割"], a:1},
      {q:"止め打ちによって通常時のベースは飛躍的に高めることが可能である。", o:["○", "×"], a:0},
      {q:"大当り中のラウンド間のインターバルに合わせて打ち出しを止める目的は？", o:["演出を飛ばすため", "獲得出玉の減少を抑止するため", "音楽を変えるため", "V入賞をキャンセルするため"], a:1},
      {q:"多数あった羽根物市場でTOKIOが生き残った理由の一つは、物理的挙動の「透明性」と何？", o:["爆発力", "複雑さ", "再現性", "デジタル演出"], a:2},
      {q:"多くの競合羽根物機が遊技者からの信頼を失った要因は？", o:["玉が大きすぎた", "役物がなかった", "複雑なギミックや不透明な乱数制御", "音楽が古かった"], a:2},
      {q:"TOKIOで敗北した際、打ち手は「内部の乱数抽選に外れたからだ」と納得する。", o:["○", "×"], a:1},
      {q:"現代のデジパチ（ハイミドル機やスマパチ）は、店舗の日次粗利にどんな影響をもたらす？", o:["全く変動させない", "常にマイナスにする", "激しい振れ幅をもたらす", "常に一定のプラスにする"], a:2},
      {q:"ホールマネジメントの観点において、TOKIOはどのような装置として機能している？", o:["利益の緩衝装置(バッファー)", "赤字製造機", "客飛ばし装置", "節税対策"], a:0},
      {q:"TOKIOのような羽根物は、デジパチと比べて大当り確率の収束がどうなっている？", o:["極めて遅い", "極めて早い", "全く収束しない", "デジパチと同じ"], a:1},
      {q:"技術派遊技客に「知識と観察力で勝率を高められる選択肢」を残すことは、ホールの健全性アピールに不可欠である。", o:["○", "×"], a:0},
      {q:"スマートパチンコ(スマパチ)の普及に伴い、パチンコホールから薄れつつあるものは何？", o:["液晶画面", "デジタル音", "椅子", "物理的な「玉」の存在"], a:3},
      {q:"将来的な封入式枠の導入によって解消されるリスクは、遊技球の磨耗と何？", o:["電気代の高騰", "不正ゴトのリスク", "従業員の退職", "台の落下"], a:1},
      {q:"画面上のCG演出は、重力や摩擦力が織りなす不可逆な玉のドラマを完全に代替できる。", o:["○", "×"], a:1},
      {q:"『PニュートキオGREEN』の全国導入台数は約何台規模？", o:["約100台", "約1,000台", "約5,000台", "約10,000台"], a:1},
      {q:"デジタル全盛の時代であっても、物理役物機への需要は絶滅することはないと確実視されている。", o:["○", "×"], a:0},
      {q:"TOKIOシリーズは、現代の電子制御と精巧な何役物を融合させている？", o:["木製役物", "鉄製役物", "アクリル役物", "ゴム製役物"], a:2}
    ];

    let currentPool = [];
    let score = 0;

    const screens = {
      start: document.getElementById('start-screen'),
      quiz: document.getElementById('quiz-screen'),
      over: document.getElementById('game-over-screen'),
      clear: document.getElementById('clear-screen')
    };

    function hideAll() {
      Object.values(screens).forEach(s => s.classList.add('hidden'));
      document.body.classList.remove('flash-red');
    }

    // Fisher-Yates Shuffle
    function shuffle(array) {
      const arr = [...array];
      for (let i = arr.length - 1; i > 0; i--) {
        const j = Math.floor(Math.random() * (i + 1));
        [arr[i], arr[j]] = [arr[j], arr[i]];
      }
      return arr;
    }

    function startGame() {
      currentPool = shuffle(qData);
      score = 0;
      hideAll();
      screens.quiz.classList.remove('hidden');
      showQuestion();
    }

    function showQuestion() {
      if (score >= currentPool.length) {
        showClear();
        return;
      }
      
      const q = currentPool[score];
      document.getElementById('progress').innerText = `${score + 1} / 100`;
      document.getElementById('question-text').innerText = q.q;
      
      const optionsDiv = document.getElementById('options');
      optionsDiv.innerHTML = '';
      
      q.o.forEach((optText, idx) => {
        const btn = document.createElement('button');
        btn.className = 'w-full bg-gray-700 hover:bg-gray-600 text-white font-bold py-4 px-6 rounded-xl transition-colors text-left border border-gray-600 active:scale-95 shadow-sm';
        btn.innerText = optText;
        btn.onclick = () => handleAnswer(idx, q.a, q);
        optionsDiv.appendChild(btn);
      });
    }

    function handleAnswer(selectedIdx, correctIdx, qObj) {
      if (selectedIdx === correctIdx) {
        score++;
        showQuestion();
      } else {
        showGameOver(qObj, correctIdx);
      }
    }

    function showGameOver(qObj, correctIdx) {
      hideAll();
      // Flash effect
      document.body.classList.add('flash-red');
      setTimeout(() => document.body.classList.remove('flash-red'), 500);

      document.getElementById('final-score').innerText = score;
      const reasonDiv = document.getElementById('mistake-reason');
      reasonDiv.innerHTML = `
        <span class="text-red-400 font-bold block mb-2">【間違えた問題】</span>
        ${qObj.q}<br><br>
        正解は <span class="text-white font-bold bg-green-600 px-2 py-1 rounded mx-1">${qObj.o[correctIdx]}</span> でした。
      `;
      screens.over.classList.remove('hidden');
    }

    function showClear() {
      hideAll();
      screens.clear.classList.remove('hidden');
    }
  </script>
</body>
</html>
