<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>サークルカウントゲーム 3D - 塚田淳専用5000チャレンジ究極版</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=M+PLUS+Rounded+1c:wght@400;900&display=swap');
        
        :root, [data-theme="standard"] {
            --bg-main: #0f172a;
            --bg-screen: #020617;
            --primary: #2563eb;
            --primary-light: #3b82f6;
            --accent: #818cf8;
            --text-main: #ffffff;
            --text-muted: #94a3b8;
            --panel-bg: rgba(30, 41, 59, 0.88);
            --numpad-bg: rgba(15, 23, 42, 0.82);
            --border-color: rgba(255, 255, 255, 0.15);
        }
        [data-theme="cyber"] {
            --bg-main: #1a0b2e;
            --bg-screen: #0f0518;
            --primary: #c026d3;
            --primary-light: #d946ef;
            --accent: #22d3ee;
            --text-main: #ffffff;
            --text-muted: #a78bfa;
            --panel-bg: rgba(46, 16, 101, 0.88);
            --numpad-bg: rgba(15, 5, 24, 0.82);
            --border-color: rgba(34, 211, 238, 0.28);
        }
        [data-theme="magma"] {
            --bg-main: #2a0a0a;
            --bg-screen: #1a0505;
            --primary: #dc2626;
            --primary-light: #ef4444;
            --accent: #fb923c;
            --text-main: #ffffff;
            --text-muted: #fca5a5;
            --panel-bg: rgba(69, 10, 10, 0.88);
            --numpad-bg: rgba(26, 5, 5, 0.85);
            --border-color: rgba(251, 146, 60, 0.25);
        }
        [data-theme="light"] {
            --bg-main: #f8fafc;
            --bg-screen: #e2e8f0;
            --primary: #0ea5e9;
            --primary-light: #38bdf8;
            --accent: #6366f1;
            --text-main: #0f172a;
            --text-muted: #64748b;
            --panel-bg: rgba(255, 255, 255, 0.92);
            --numpad-bg: rgba(226, 232, 240, 0.9);
            --border-color: rgba(15, 23, 42, 0.12);
        }

        body {
            font-family: 'M PLUS Rounded 1c', sans-serif;
            background-color: var(--bg-main);
            color: var(--text-main);
            margin: 0;
            padding: 0;
            height: 100dvh;
            display: flex;
            flex-direction: column;
            overflow: hidden;
            touch-action: manipulation;
            transition: background-color 0.5s ease, color 0.5s ease;
        }

        .screen {
            position: fixed;
            inset: 0;
            display: none;
            flex-direction: column;
            z-index: 10;
        }

        .screen.active {
            display: flex;
        }

        #start-bg {
            position: absolute;
            inset: 0;
            z-index: -1;
            background-color: var(--bg-screen);
            transition: background-color 0.5s ease;
            overflow: hidden;
        }

        .status-section {
            position: relative;
            padding: 0.45rem 0.75rem;
            background: var(--panel-bg);
            backdrop-filter: blur(12px);
            -webkit-backdrop-filter: blur(12px);
            border-bottom: 1px solid var(--border-color);
            z-index: 25;
            box-shadow: 0 6px 24px rgba(0, 0, 0, 0.4);
            flex-shrink: 0;
        }

        .canvas-container {
            position: relative;
            flex-grow: 1;
            width: 100%;
            overflow: hidden;
            background-color: transparent;
            touch-action: none;
            cursor: grab;
            z-index: 10;
        }

        .canvas-container:active {
            cursor: grabbing;
        }

        canvas.webgl-canvas {
            position: fixed;
            inset: 0;
            display: block;
            width: 100vw !important;
            height: 100dvh !important;
            outline: none;
            z-index: 0;
            pointer-events: none;
        }

        .numpad {
            position: relative;
            display: grid;
            grid-template-columns: repeat(5, 1fr);
            gap: 6px;
            padding: 8px 10px;
            background: var(--numpad-bg);
            backdrop-filter: blur(10px);
            -webkit-backdrop-filter: blur(10px);
            border-top: 1px solid var(--border-color);
            padding-bottom: max(env(safe-area-inset-bottom, 10px), 10px);
            z-index: 25;
            perspective: 800px;
            box-shadow: 0 -8px 28px rgba(0, 0, 0, 0.5);
            flex-shrink: 0;
        }

        .num-btn {
            height: 44px;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 1.3rem;
            font-weight: 900;
            border-radius: 12px;
            background: var(--primary-light);
            color: white;
            border: 1px solid rgba(255, 255, 255, 0.35);
            box-shadow: 0 4px 0 var(--primary), 0 6px 12px rgba(0,0,0,0.4), inset 0 2px 3px rgba(255,255,255,0.5);
            transition: transform 0.08s cubic-bezier(0.4, 0, 0.2, 1), background-color 0.2s ease, box-shadow 0.08s ease;
            user-select: none;
            touch-action: manipulation;
            -webkit-tap-highlight-color: transparent;
            cursor: pointer;
            transform: rotateX(10deg) translateZ(2px);
            text-shadow: 0 2px 4px rgba(0,0,0,0.4);
        }

        @media (min-height: 760px) {
            .num-btn {
                height: 52px;
                font-size: 1.45rem;
            }
        }

        .num-btn:active {
            transform: rotateX(4deg) translateY(3px) scale(0.95);
            box-shadow: 0 1px 0 var(--primary), 0 2px 4px rgba(0,0,0,0.3), inset 0 1px 2px rgba(255,255,255,0.3) !important;
        }

        .combo-count {
            font-weight: 900;
            font-size: 3.2rem;
            opacity: 0;
            transform: scale(0.5) rotate(-15deg);
            transition: all 0.12s cubic-bezier(0.175, 0.885, 0.32, 1.275);
            line-height: 1;
            text-shadow: 0 4px 12px rgba(0,0,0,0.45);
        }

        .combo-count.pop {
            opacity: 1;
            transform: scale(1.1) rotate(5deg);
        }

        .flash-overlay {
            position: absolute;
            inset: 0;
            background: white;
            opacity: 0;
            pointer-events: none;
            z-index: 35;
        }

        .animate-flash {
            animation: flash-anim 0.2s ease-out;
        }

        @keyframes flash-anim {
            0% { opacity: 0.35; }
            100% { opacity: 0; }
        }

        .feedback-pop {
            position: absolute;
            inset: 0;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 8rem;
            font-weight: 900;
            pointer-events: none;
            opacity: 0;
            z-index: 30;
            text-align: center;
            text-shadow: 0 6px 20px rgba(0,0,0,0.35);
        }

        @keyframes pop-in {
            0% { transform: scale(0.4) rotate(-8deg); opacity: 0; }
            50% { transform: scale(1.15) rotate(4deg); opacity: 0.75; }
            100% { transform: scale(1) rotate(0deg); opacity: 0; }
        }

        .animate-feedback { animation: pop-in 0.4s ease-out forwards; }

        .btn-shine { position: relative; overflow: hidden; }
        .btn-shine::after {
            content: "";
            position: absolute;
            top: -50%; left: -50%; width: 200%; height: 200%;
            background: linear-gradient(45deg, transparent, rgba(255,255,255,0.35), transparent);
            transform: rotate(45deg);
            animation: shine 3s infinite;
        }
        @keyframes shine { 0% { left: -100%; } 20% { left: 100%; } 100% { left: 100%; } }

        .theme-btn {
            position: relative;
            overflow: hidden;
            cursor: pointer;
        }
        .theme-btn::after {
            content: '';
            position: absolute;
            inset: 0;
            border-radius: 50%;
            box-shadow: inset 0 2px 4px rgba(255,255,255,0.35);
        }

        .chest {
            cursor: pointer;
            transition: all 0.3s cubic-bezier(0.175, 0.885, 0.32, 1.275);
            touch-action: manipulation;
        }
        .chest:hover { transform: scale(1.1) translateY(-4px); }
        .chest:active { transform: scale(0.92); }
        .chest.opened {
            transform: scale(1.22);
            filter: drop-shadow(0 0 24px rgba(250, 204, 21, 0.9));
        }
        .chest.dimmed {
            opacity: 0.3;
            transform: scale(0.9);
            filter: grayscale(100%);
        }
        .chest-svg { width: 100px; height: 100px; filter: drop-shadow(0 6px 10px rgba(0,0,0,0.35)); transition: all 0.3s ease; }

        .shake {
            animation: shake-anim 0.3s cubic-bezier(.36,.07,.19,.97) both;
        }
        @keyframes shake-anim {
            0%, 100% { transform: translateX(0); }
            20% { transform: translateX(-10px) rotate(-1deg); }
            40% { transform: translateX(10px) rotate(1deg); }
            60% { transform: translateX(-10px) rotate(-1deg); }
            80% { transform: translateX(10px) rotate(1deg); }
        }

        .log-container {
            max-height: 60dvh;
            overflow-y: auto;
            width: 100%;
            padding-right: 8px;
            -webkit-overflow-scrolling: touch;
        }
        .log-item {
            background: var(--panel-bg);
            border: 1px solid var(--border-color);
            margin-bottom: 8px;
            padding: 12px 16px;
            border-radius: 16px;
            display: flex;
            justify-content: space-between;
            align-items: center;
            backdrop-filter: blur(8px);
        }

        .admin-hitbox {
            position: absolute;
            top: 0; width: 80px; height: 80px;
            z-index: 100;
        }
        .admin-hitbox-left { left: 0; }
        .admin-hitbox-right { right: 0; }
        
        .presentation-area {
            min-height: 200px;
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
        }

        .loader {
            width: 20px; height: 20px;
            border: 3px solid var(--text-muted); border-bottom-color: transparent; border-radius: 50%;
            display: inline-block; animation: rotation 1s linear infinite;
        }
        @keyframes rotation { 0% { transform: rotate(0deg); } 100% { transform: rotate(360deg); } }

        .text-rainbow {
            background: linear-gradient(to right, #ff0000, #ff7f00, #eab308, #22c55e, #3b82f6, #8b5cf6, #ec4899);
            -webkit-background-clip: text;
            background-clip: text;
            color: transparent;
            animation: rainbow-anim 3s linear infinite;
            background-size: 200% auto;
        }
        .shadow-rainbow {
            filter: drop-shadow(0 0 10px rgba(255,255,255,0.8));
        }
        @keyframes rainbow-anim {
            0% { background-position: 0% center; }
            100% { background-position: -200% center; }
        }

        .cam-badge {
            background: var(--panel-bg);
            border: 1px solid var(--border-color);
            backdrop-filter: blur(10px);
            color: var(--text-muted);
            font-size: 0.62rem;
            font-weight: 800;
            padding: 0.22rem 0.5rem;
            border-radius: 9999px;
            display: inline-flex;
            align-items: center;
            gap: 0.2rem;
            cursor: pointer;
            user-select: none;
            transition: all 0.2s ease;
            white-space: nowrap;
        }
        .cam-badge:active { transform: scale(0.95); }

        .stage-badge {
            background: var(--panel-bg);
            border: 1px solid var(--border-color);
            backdrop-filter: blur(10px);
            color: var(--text-main);
            font-size: 0.64rem;
            font-weight: 900;
            padding: 0.22rem 0.55rem;
            border-radius: 9999px;
            display: inline-flex;
            align-items: center;
            gap: 0.25rem;
            box-shadow: 0 4px 12px rgba(0,0,0,0.25);
            transition: all 0.3s ease;
            white-space: nowrap;
        }

        .stage-cutin {
            position: absolute;
            top: 18%;
            left: 50%;
            transform: translate(-50%, -20px) scale(0.85);
            background: rgba(15, 23, 42, 0.9);
            border: 2px solid rgba(255, 255, 255, 0.35);
            backdrop-filter: blur(12px);
            padding: 0.6rem 1.6rem;
            border-radius: 9999px;
            pointer-events: none;
            opacity: 0;
            z-index: 45;
            text-align: center;
            box-shadow: 0 10px 30px rgba(0,0,0,0.5);
            transition: all 0.35s cubic-bezier(0.175, 0.885, 0.32, 1.275);
            white-space: nowrap;
        }
        .stage-cutin.show {
            opacity: 1;
            transform: translate(-50%, 0) scale(1);
        }

        .hype-ticker {
            background: rgba(2, 6, 23, 0.85);
            border: 1px solid var(--border-color);
            border-radius: 10px;
            padding: 5px 8px;
            display: flex;
            align-items: center;
            gap: 6px;
            margin-top: 4px;
            box-shadow: inset 0 2px 6px rgba(0,0,0,0.4);
            min-height: 2.1rem;
        }

        .fever-bar-bg {
            background: linear-gradient(90deg, #ef4444, #f59e0b, #eab308, #22c55e, #3b82f6, #a855f7, #ec4899);
            background-size: 200% 100%;
            animation: rainbow-anim 1.2s linear infinite;
        }

        .fever-screen-glow {
            box-shadow: inset 0 0 45px rgba(250, 204, 21, 0.75), inset 0 0 90px rgba(236, 72, 153, 0.45);
            animation: fever-pulse 0.5s ease-in-out infinite alternate;
        }

        @keyframes fever-pulse {
            0% { box-shadow: inset 0 0 25px rgba(250, 204, 21, 0.6), inset 0 0 60px rgba(59, 130, 246, 0.4); }
            100% { box-shadow: inset 0 0 55px rgba(236, 72, 153, 0.85), inset 0 0 100px rgba(250, 204, 21, 0.65); }
        }

        .bonus-banner {
            position: absolute;
            top: 32%;
            left: 50%;
            transform: translate(-50%, -50%) scale(0.3);
            pointer-events: none;
            opacity: 0;
            z-index: 48;
            text-align: center;
            transition: all 0.28s cubic-bezier(0.175, 0.885, 0.32, 1.275);
            white-space: nowrap;
        }
        .bonus-banner.active {
            opacity: 1;
            transform: translate(-50%, -50%) scale(1);
        }

        .confetti-piece {
            position: fixed;
            width: 10px;
            height: 16px;
            pointer-events: none;
            z-index: 150;
            animation: confetti-fall 1.8s cubic-bezier(0.25, 0.46, 0.45, 0.94) forwards;
        }

        @keyframes confetti-fall {
            0% { transform: translateY(0) rotate(0deg) scale(1); opacity: 1; }
            100% { transform: translateY(85vh) rotate(720deg) scale(0.6); opacity: 0; }
        }

        .cockpit-hud {
            position: fixed;
            inset: 0;
            pointer-events: none;
            z-index: 15;
            box-shadow: inset 0 0 85px rgba(0, 0, 0, 0.7);
            background:
                radial-gradient(circle at 50% 50%, transparent 50%, rgba(56, 189, 248, 0.14) 100%),
                linear-gradient(90deg, rgba(56, 189, 248, 0.06) 0%, transparent 8%, transparent 92%, rgba(56, 189, 248, 0.06) 100%);
            transition: box-shadow 0.25s ease;
        }
        .cockpit-hud.warp-boost {
            box-shadow: inset 0 0 120px rgba(56, 189, 248, 0.85), inset 0 0 50px rgba(255, 255, 255, 0.6);
        }
        .cockpit-hud.front-hit {
            box-shadow: inset 0 0 100px rgba(250, 204, 21, 0.8), inset 0 0 40px rgba(255, 255, 255, 0.7);
        }

        .cockpit-reticle {
            position: absolute;
            top: 50%;
            left: 50%;
            width: 170px;
            height: 170px;
            transform: translate(-50%, -50%);
            border: 1px dashed rgba(255, 255, 255, 0.12);
            border-radius: 50%;
            pointer-events: none;
            z-index: 12;
        }
        .cockpit-reticle::before, .cockpit-reticle::after {
            content: '';
            position: absolute;
            background: rgba(56, 189, 248, 0.22);
        }
        .cockpit-reticle::before { top: 50%; left: -16px; right: -16px; height: 1px; }
        .cockpit-reticle::after { left: 50%; top: -16px; bottom: -16px; width: 1px; }
    </style>
</head>
<body>

    <div id="start-screen" class="screen active items-center justify-center p-8">
        <div id="start-bg"></div>
        <div class="z-20 text-center flex flex-col items-center max-w-sm w-full bg-[var(--panel-bg)] backdrop-blur-md p-7 rounded-[2.2rem] border border-[var(--border-color)] shadow-2xl">
            <div class="inline-flex items-center gap-1.5 px-3 py-1 rounded-full bg-[var(--primary)]/20 border border-[var(--primary-light)]/40 text-[var(--accent)] text-xs font-black tracking-widest mb-3">
                <span>CUBE ARENA 3D EDITION</span>
            </div>
            <p class="text-red-500 font-black text-2xl sm:text-3xl mb-3 animate-bounce tracking-tighter drop-shadow-sm">
                準備はいいか！塚田淳！
            </p>
            <h1 class="text-3xl sm:text-4xl font-black mb-7 tracking-tighter text-[var(--text-main)] text-center leading-tight drop-shadow-md">
                サークルカウント<br><span class="text-[var(--accent)] text-4xl sm:text-5xl">3Dゲーム</span>
            </h1>

            <div class="mb-7 w-full">
                <p class="text-[11px] text-[var(--text-muted)] font-bold mb-3 uppercase tracking-widest text-center">3D空間テーマを選ぼう！</p>
                <div class="flex justify-center gap-3">
                    <button id="theme-standard" onclick="setTheme('standard')" class="theme-btn w-11 h-11 rounded-full bg-blue-500 border-4 border-white scale-110 shadow-[0_0_15px_rgba(59,130,246,0.6)] transition-all opacity-100" title="スタンダード"></button>
                    <button id="theme-cyber" onclick="setTheme('cyber')" class="theme-btn w-11 h-11 rounded-full bg-fuchsia-500 border-4 border-transparent scale-100 opacity-50 transition-all" title="サイバー"></button>
                    <button id="theme-magma" onclick="setTheme('magma')" class="theme-btn w-11 h-11 rounded-full bg-red-500 border-4 border-transparent scale-100 opacity-50 transition-all" title="マグマ"></button>
                    <button id="theme-light" onclick="setTheme('light')" class="theme-btn w-11 h-11 rounded-full bg-slate-100 border-4 border-transparent scale-100 opacity-50 transition-all" title="ライト"></button>
                </div>
            </div>
            
            <button id="start-btn" onclick="startGame()" class="btn-shine w-full bg-[var(--primary)] text-white py-4 sm:py-5 rounded-3xl font-black text-xl sm:text-2xl shadow-xl active:scale-95 transition-transform mb-4">
                チャレンジスタート
            </button>

            <button onclick="showLog()" class="w-full bg-[var(--panel-bg)] text-[var(--text-muted)] py-3 rounded-2xl font-bold text-base sm:text-lg border border-[var(--border-color)] shadow-sm active:scale-95 transition">
                <span>ログを見る（共有）</span>
            </button>
        </div>
    </div>

    <div id="game-screen" class="screen">
        <div class="status-section">
            <div class="flex justify-between items-end mb-1">
                <div>
                    <div class="flex items-center gap-1.5">
                        <span class="text-[10px] text-[var(--text-muted)] font-bold uppercase tracking-wider">スコア</span>
                        <span id="fever-badge" class="hidden px-2 py-0.5 text-[9px] font-black rounded-full fever-bar-bg text-white shadow-md animate-bounce">🌟 無敵STAR FEVER x3!!</span>
                    </div>
                    <div id="score-text" class="text-2xl sm:text-3xl font-black text-[var(--text-main)] leading-none">0</div>
                </div>
                <div class="text-right">
                    <div class="text-[10px] text-[var(--text-muted)] font-bold uppercase tracking-wider">最高コンボ</div>
                    <div id="best-combo" class="text-base sm:text-lg font-bold text-amber-500 leading-none drop-shadow-sm">0</div>
                </div>
            </div>
            <div class="space-y-1">
                <div class="flex justify-between text-[10px] font-black text-[var(--text-muted)]">
                    <span id="percent">達成率 0%</span>
                    <span id="remaining">クリアーまで残り 5,000</span>
                </div>
                <div class="w-full bg-slate-300/30 h-1.5 rounded-full overflow-hidden">
                    <div id="progress-bar" class="h-full bg-[var(--primary-light)] transition-all duration-300" style="width: 0%"></div>
                </div>
                <div class="flex items-center gap-2 pt-0.5">
                    <span class="text-[9px] font-black text-yellow-400 tracking-wider shrink-0">⭐ STAR</span>
                    <div class="w-full bg-slate-800/80 h-1.5 rounded-full overflow-hidden border border-yellow-400/30">
                        <div id="fever-bar" class="h-full bg-yellow-400 transition-all duration-200" style="width: 0%"></div>
                    </div>
                    <span id="fever-status-text" class="text-[9px] font-black text-yellow-300 shrink-0">0/5</span>
                </div>
            </div>
            <div class="hype-ticker">
                <span id="hype-icon" class="text-sm sm:text-base shrink-0 animate-bounce">🎙️</span>
                <p id="hype-text" class="text-[11px] sm:text-xs font-black text-[var(--text-main)] leading-tight whitespace-normal break-words">さあ始まった！塚田淳、伝説の5000チャレンジへ挑め！！</p>
            </div>
        </div>

        <div class="canvas-container" id="canvas">
            <div id="flash" class="flash-overlay"></div>
            <div id="cockpit-hud" class="cockpit-hud"></div>
            <div class="cockpit-reticle"></div>
            
            <div class="absolute top-2 left-2 right-16 flex flex-wrap gap-1 z-40">
                <button id="immersive-btn" onclick="toggleImmersiveMode()" class="cam-badge !text-cyan-300 !border-cyan-400/50" title="3D没入ダイブ視点の切替">
                    <span>🚀 没入: ON</span>
                </button>
                <button onclick="resetCameraView()" class="cam-badge" title="カメラ正面に戻す">
                    <span>🎥 正面</span>
                </button>
                <button id="auto-orbit-btn" onclick="toggleAutoOrbit()" class="cam-badge" title="自動カメラ回転">
                    <span>🔄 遊泳: ON</span>
                </button>
                <button id="gyro-btn" onclick="toggleGyroMode()" class="cam-badge" title="スマホの傾き視点">
                    <span>📱 傾き: OFF</span>
                </button>
                <button id="bgm-btn" onclick="toggleBGM()" class="cam-badge" title="8bit BGM">
                    <span>🎵 BGM: OFF</span>
                </button>
                <button id="stage-mode-btn" onclick="cycleStageOverride()" class="stage-badge cursor-pointer active:scale-95" title="タップでステージ切替">
                    <span id="stage-indicator">🏠 ベース空間 (連動)</span>
                </button>
            </div>
            
            <div class="absolute bottom-1.5 left-0 right-0 text-center pointer-events-none z-30 opacity-75 text-[8.5px] font-bold text-[var(--text-muted)] px-2">
                1本指ドラッグ:360°回転 ／ 2本指ピンチ:ズーム ／ ステージ名タップ:世界切替
            </div>

            <div id="stage-cutin" class="stage-cutin">
                <div class="text-[10px] font-black tracking-widest text-cyan-300 uppercase">SPACE STAGE CHANGE!!</div>
                <div id="stage-cutin-title" class="text-xl sm:text-2xl font-black text-white drop-shadow-md">🫧 深海アクア</div>
            </div>

            <div id="bonus-banner" class="bonus-banner">
                <div id="bonus-sub" class="text-xs font-black tracking-widest text-yellow-300 uppercase drop-shadow">SUPER FEVER BONUS!!</div>
                <div id="bonus-main" class="text-2xl sm:text-3xl font-black text-rainbow shadow-rainbow">💎 +3 SCORE!!</div>
            </div>

            <div class="absolute top-4 right-5 text-right pointer-events-none z-40">
                <div id="combo-count" class="combo-count">0</div>
                <div id="combo-label" class="text-base sm:text-lg font-black hidden drop-shadow-sm">COMBO</div>
            </div>
            <div id="feedback" class="feedback-pop"></div>
        </div>

        <div class="numpad" id="numpad"></div>
    </div>

    <div id="treasure-screen" class="screen items-center justify-center p-8 bg-[var(--bg-main)] bg-opacity-95">
        <div class="text-center w-full">
            <h2 class="text-3xl font-black text-yellow-500 mb-2 drop-shadow-md">チャレンジ達成!!</h2>
            <p id="treasure-msg" class="text-[var(--text-main)] text-lg mb-12 font-bold italic transition-opacity duration-300">運命の報酬を選べ、塚田淳！</p>
            <div class="flex justify-around items-center w-full max-w-md gap-4 mx-auto" id="chest-container">
                <div class="chest" id="chest-0" onclick="openChest(0)">
                    <svg class="chest-svg" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
                        <rect x="2" y="10" width="20" height="10" rx="2" fill="#8B4513" stroke="#FBBF24" stroke-width="1.5"/>
                        <path d="M2 10C2 7 5 5 12 5C19 5 22 7 22 10" fill="#A0522D" stroke="#FBBF24" stroke-width="1.5"/>
                        <rect x="10" y="9" width="4" height="3" rx="1" fill="#FBBF24"/>
                    </svg>
                    <p class="mt-2 text-[10px] font-bold text-yellow-500 uppercase tracking-widest drop-shadow-sm chest-label">宝箱 A</p>
                </div>
                <div class="chest" id="chest-1" onclick="openChest(1)">
                    <svg class="chest-svg" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
                        <rect x="2" y="10" width="20" height="10" rx="2" fill="#8B4513" stroke="#FBBF24" stroke-width="1.5"/>
                        <path d="M2 10C2 7 5 5 12 5C19 5 22 7 22 10" fill="#A0522D" stroke="#FBBF24" stroke-width="1.5"/>
                        <rect x="10" y="9" width="4" height="3" rx="1" fill="#FBBF24"/>
                    </svg>
                    <p class="mt-2 text-[10px] font-bold text-yellow-500 uppercase tracking-widest drop-shadow-sm chest-label">宝箱 B</p>
                </div>
                <div class="chest" id="chest-2" onclick="openChest(2)">
                    <svg class="chest-svg" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
                        <rect x="2" y="10" width="20" height="10" rx="2" fill="#8B4513" stroke="#FBBF24" stroke-width="1.5"/>
                        <path d="M2 10C2 7 5 5 12 5C19 5 22 7 22 10" fill="#A0522D" stroke="#FBBF24" stroke-width="1.5"/>
                        <rect x="10" y="9" width="4" height="3" rx="1" fill="#FBBF24"/>
                    </svg>
                    <p class="mt-2 text-[10px] font-bold text-yellow-500 uppercase tracking-widest drop-shadow-sm chest-label">宝箱 C</p>
                </div>
            </div>
        </div>
    </div>

    <div id="double-up-screen" class="screen items-center justify-center p-8 bg-[var(--bg-main)] bg-opacity-95">
        <div class="text-center w-full">
            <p class="text-red-600 font-black text-2xl sm:text-3xl mb-4 animate-bounce tracking-tighter">準備はいいか！塚田淳！</p>
            
            <h2 id="double-up-title" class="font-black text-rose-500 mb-6 drop-shadow-md animate-pulse tracking-tighter flex flex-col items-center gap-1">
                <span class="text-3xl sm:text-4xl w-full">ダブルアップチャレンジ</span>
                <span class="text-xl sm:text-2xl text-rose-400 drop-shadow-sm">(1回目)</span>
            </h2>
            
            <p class="text-[var(--text-muted)] text-lg mb-8 font-bold italic">現在の報酬: <span id="current-reward-text" class="text-3xl text-blue-500 drop-shadow-md">0円</span></p>

            <div id="double-up-actions" class="flex flex-col gap-4 w-full max-w-sm mx-auto">
                <div class="bg-[var(--panel-bg)] border border-[var(--border-color)] rounded-xl p-3 mb-2 shadow-lg">
                    <p class="text-cyan-500 font-black text-xl mb-1 tracking-wider drop-shadow-sm">勝率 50%</p>
                    <p class="text-[var(--text-muted)] text-sm">成功すれば報酬<span class="text-yellow-500 font-bold text-xl ml-1">2倍</span>！<br>失敗すれば<span class="text-rose-500 font-bold">すべて没収</span>。</p>
                </div>
                
                <button onclick="playDoubleUp()" class="btn-shine w-full bg-rose-600 text-white py-4 rounded-3xl font-black text-2xl shadow-[0_4px_15px_rgba(225,29,72,0.4)] active:scale-95 transition-transform">挑戦する</button>
                <button onclick="declineDoubleUp()" class="w-full bg-[var(--panel-bg)] border border-[var(--border-color)] text-[var(--text-muted)] py-3 rounded-2xl font-bold text-lg shadow-sm active:scale-95 transition">やめておく (確定)</button>
            </div>

            <div id="double-up-presentation" class="hidden w-full max-w-sm mx-auto presentation-area mt-4">
                <div id="double-up-roulette" class="hidden w-full flex-col items-center justify-center">
                    <p id="roulette-status-text" class="text-[var(--text-muted)] font-bold mb-4 animate-pulse text-lg">抽選中...</p>
                    <div id="roulette-box" class="w-full bg-[var(--panel-bg)] border-4 border-[var(--border-color)] rounded-2xl p-8 text-center transition-colors duration-75 shadow-2xl">
                        <span id="roulette-text" class="text-5xl font-black text-[var(--text-main)] drop-shadow-md">運命</span>
                    </div>
                </div>
                <div id="double-up-result" class="hidden w-full flex-col items-center justify-center opacity-0 transition-opacity duration-300">
                    <div id="double-up-result-text" class="text-6xl sm:text-7xl font-black transform scale-50 transition-transform duration-500"></div>
                </div>
            </div>
        </div>
    </div>

    <div id="clear-screen" class="screen items-center justify-center p-6 bg-[var(--bg-main)]">
        <div class="text-7xl mb-4 animate-pulse drop-shadow-md">👑</div>
        <h2 class="text-2xl font-black mb-6 text-[var(--accent)] uppercase tracking-tighter text-center drop-shadow-sm">伝説達成</h2>
        <div class="bg-[var(--panel-bg)] border border-[var(--border-color)] rounded-[2.5rem] p-8 w-full max-w-xs mb-8 text-center relative overflow-hidden shadow-2xl">
            <div id="timestamp" class="text-[10px] font-bold text-[var(--text-muted)] mb-4 uppercase tracking-widest"></div>
            <p class="text-[var(--text-muted)] text-[10px] font-bold uppercase tracking-[0.2em] mb-2 italic">獲得報酬</p>
            <p id="bonus-value-text" class="text-5xl font-black italic tracking-tighter mb-4 text-[var(--text-main)] drop-shadow-md">0円</p>
            <div class="border-t border-[var(--border-color)] pt-4">
                <p class="text-[10px] text-[var(--text-muted)] font-bold tracking-wider italic">認定プレイヤー: <span class="text-[var(--text-main)] font-black">塚田淳</span></p>
            </div>
        </div>
        <p class="text-yellow-500 font-black mb-8 text-center text-xl animate-bounce drop-shadow-sm">最高の記録だ、塚田淳！！</p>
        <button onclick="backToStart()" class="w-full max-w-xs bg-[var(--primary)] py-5 rounded-3xl font-black text-xl text-white shadow-xl active:scale-95 transition">タイトルに戻る</button>
    </div>

    <div id="log-screen" class="screen items-center justify-center p-6 bg-[var(--bg-main)]">
        <div class="admin-hitbox admin-hitbox-left" onclick="handleAdminTap('left')"></div>
        <div class="admin-hitbox admin-hitbox-right" onclick="handleAdminTap('right')"></div>
        
        <h2 class="text-2xl font-black mb-6 text-[var(--accent)] uppercase tracking-tighter text-center italic drop-shadow-sm">共有：栄光の記録</h2>
        <div id="log-list" class="log-container relative z-10">
            <div class="flex justify-center py-10"><span class="loader"></span></div>
        </div>
        <button onclick="backToStartFromLog()" class="mt-8 w-full max-w-xs bg-[var(--panel-bg)] border border-[var(--border-color)] text-[var(--text-main)] py-4 rounded-3xl font-black text-xl shadow-lg active:scale-95 transition relative z-10">戻る</button>
    </div>

    <div id="admin-confirm-modal" style="display: none;" class="fixed inset-0 bg-black/80 z-[200] items-center justify-center p-4">
        <div class="bg-slate-800 p-6 rounded-2xl w-full max-w-sm text-center border border-rose-500/50 shadow-2xl">
            <h3 class="text-xl font-black text-rose-500 mb-4">【管理者認証】</h3>
            <p class="mb-6 font-bold text-white text-sm">すべての共有ログを消去しますか？<br>この操作は取り消せません。</p>
            <div class="flex gap-4">
                <button onclick="cancelAdminClear()" class="flex-1 py-3 bg-slate-600 text-white rounded-xl font-bold active:scale-95 transition">キャンセル</button>
                <button onclick="executeAdminClear()" class="flex-1 py-3 bg-rose-600 text-white rounded-xl font-bold shadow-lg shadow-rose-600/20 active:scale-95 transition">消去する</button>
            </div>
        </div>
    </div>

    <div id="load-confirm-modal" style="display: none;" class="fixed inset-0 bg-black/80 z-[200] items-center justify-center p-4">
        <div class="bg-slate-800 p-6 rounded-2xl w-full max-w-sm text-center border border-blue-500/50 shadow-2xl">
            <h3 class="text-xl font-black text-blue-500 mb-4">セーブデータ発見</h3>
            <p class="mb-6 font-bold text-white text-sm">中断されたデータがあります。<br>続きから再開しますか？<br><span class="text-slate-400 text-xs mt-2 block">スコア: <span id="load-score-text" class="text-xl text-white"></span></span></p>
            <div class="flex gap-4">
                <button onclick="startNewGame()" class="flex-1 py-3 bg-slate-600 text-white rounded-xl font-bold active:scale-95 transition">最初から</button>
                <button onclick="resumeGame()" class="flex-1 py-3 bg-blue-600 text-white rounded-xl font-bold shadow-lg shadow-blue-600/20 active:scale-95 transition">続きから</button>
            </div>
        </div>
    </div>

    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-app.js";
        import { getAuth, signInAnonymously, signInWithCustomToken, onAuthStateChanged } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-auth.js";
        import { getFirestore, collection, addDoc, onSnapshot, serverTimestamp, getDocs, deleteDoc, doc, setDoc, getDoc } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-firestore.js";

        const defaultFirebaseConfig = {
          apiKey: "AIzaSyD9TtJvfUILwBzsDjXAlZBH5R5Khp1pRzA",
          authDomain: "jungame.firebaseapp.com",
          projectId: "jungame",
          storageBucket: "jungame.firebasestorage.app",
          messagingSenderId: "410177738553",
          appId: "1:410177738553:web:56d7e876c1c7b4333a7ae8",
          measurementId: "G-QGL9HRKVHX"
        };

        const firebaseConfig = typeof __firebase_config !== 'undefined' ? JSON.parse(__firebase_config) : defaultFirebaseConfig;
        
        let db, auth;
        const appId = typeof __app_id !== 'undefined' ? __app_id : 'atsushi-5000-challenge-final'; 
        let currentUser = null;
        let savedData = null;
        let saveTimeout = null;
        
        function enableStartButton() {
            const btn = document.getElementById('start-btn');
            if (btn) {
                btn.disabled = false;
                btn.innerText = "チャレンジスタート";
                btn.classList.remove('opacity-50', 'cursor-not-allowed');
            }
        }

        async function initFirebase() {
            try {
                const app = initializeApp(firebaseConfig);
                auth = getAuth(app);
                db = getFirestore(app);
                
                if (typeof __initial_auth_token !== 'undefined' && __initial_auth_token) {
                    await signInWithCustomToken(auth, __initial_auth_token);
                } else {
                    await signInAnonymously(auth);
                }

                onAuthStateChanged(auth, async (user) => { 
                    currentUser = user; 
                    if (user) {
                        try {
                            const saveDoc = doc(db, 'artifacts', appId, 'users', user.uid, 'saveData', 'current');
                            const snap = await getDoc(saveDoc);
                            if (snap.exists()) {
                                savedData = snap.data();
                            }
                        } catch(e) { console.error("Load save data failed.", e); }
                    }
                    enableStartButton();
                });
            } catch (err) { 
                console.error("Firebase init failed.", err); 
                enableStartButton();
            }
        }
        initFirebase();

        function saveProgress() {
            if (!db || !currentUser) return;
            if (saveTimeout) clearTimeout(saveTimeout);
            saveTimeout = setTimeout(async () => {
                try {
                    const saveDoc = doc(db, 'artifacts', appId, 'users', currentUser.uid, 'saveData', 'current');
                    await setDoc(saveDoc, {
                        score: score,
                        combo: combo,
                        maxCombo: maxCombo,
                        feverCharge: feverCharge,
                        isFeverMode: isFeverMode,
                        feverTurnsLeft: feverTurnsLeft,
                        timestamp: serverTimestamp()
                    });
                } catch(e) { console.error("Save failed", e); }
            }, 1000); 
        }

        function clearSaveData() {
            savedData = null;
            if (saveTimeout) {
                clearTimeout(saveTimeout);
                saveTimeout = null;
            }
            if (db && currentUser) {
                const saveDoc = doc(db, 'artifacts', appId, 'users', currentUser.uid, 'saveData', 'current');
                deleteDoc(saveDoc).catch(e => console.error(e));
            }
        }

        const THEMES = {
            standard: {
                bgHex: 0x020617,
                gridMain: 0x2563eb,
                gridSub: 0x1e293b,
                cageColor: 0x3b82f6,
                ambientHex: 0x94a3b8,
                colors: ['#FF6B6B', '#4D96FF', '#6BCB77', '#FFD93D', '#917FB3', '#FF87CA', '#00D7FF'],
                baseCombo: { c: '#3b82f6', s: '#1d4ed8' },
                comboStages: [
                    { limit: 40, c: '#ef4444', s: '#b91c1c' },
                    { limit: 30, c: '#f97316', s: '#c2410c' },
                    { limit: 20, c: '#eab308', s: '#a16207' },
                    { limit: 10, c: '#60a5fa', s: '#2563eb' }
                ]
            },
            cyber: {
                bgHex: 0x0f0518,
                gridMain: 0x22d3ee,
                gridSub: 0x3b0764,
                cageColor: 0xd946ef,
                ambientHex: 0xc084fc,
                colors: ['#FF00FF', '#00FFFF', '#39FF14', '#9D00FF', '#FF007F', '#00FF9D'],
                baseCombo: { c: '#d946ef', s: '#a21caf' },
                comboStages: [
                    { limit: 40, c: '#22d3ee', s: '#0e7490' },
                    { limit: 30, c: '#3b82f6', s: '#1d4ed8' },
                    { limit: 20, c: '#8b5cf6', s: '#5b21b6' },
                    { limit: 10, c: '#e879f9', s: '#c026d3' }
                ]
            },
            magma: {
                bgHex: 0x1a0505,
                gridMain: 0xf97316,
                gridSub: 0x450a0a,
                cageColor: 0xef4444,
                ambientHex: 0xfca5a5,
                colors: ['#FF0000', '#FF4500', '#FF8C00', '#FFD700', '#FF2400', '#DC143C'],
                baseCombo: { c: '#ef4444', s: '#b91c1c' },
                comboStages: [
                    { limit: 40, c: '#fef08a', s: '#ca8a04' },
                    { limit: 30, c: '#fde047', s: '#a16207' },
                    { limit: 20, c: '#fb923c', s: '#c2410c' },
                    { limit: 10, c: '#f87171', s: '#dc2626' }
                ]
            },
            light: {
                bgHex: 0xe2e8f0,
                gridMain: 0x0ea5e9,
                gridSub: 0xcbd5e1,
                cageColor: 0x0284c7,
                ambientHex: 0xffffff,
                colors: ['#FF6B8B', '#FF9F43', '#FECA57', '#1DD1A1', '#54A0FF', '#5F27CD', '#00D2D3'],
                baseCombo: { c: '#0ea5e9', s: '#0369a1' },
                comboStages: [
                    { limit: 40, c: '#e11d48', s: '#9f1239' },
                    { limit: 30, c: '#8b5cf6', s: '#5b21b6' },
                    { limit: 20, c: '#10b981', s: '#047857' },
                    { limit: 10, c: '#3b82f6', s: '#1d4ed8' }
                ]
            }
        };

        let currentTheme = 'standard';
        let currentComboColor = THEMES[currentTheme].baseCombo.c;
        let currentShadowColor = THEMES[currentTheme].baseCombo.s;

        const TARGET = 5000;
        let score = 0, combo = 0, maxCombo = 0, count = 0, anim, isStarted = false, chestValues = [];
        let isChestOpened = false;
        let isDoubleUpRunning = false;
        let currentReward = 0; 
        let doubleUpCount = 0;

        let feverCharge = 0;
        const FEVER_MAX_CHARGE = 5;
        let isFeverMode = false;
        let feverTurnsLeft = 0;
        let bonusPopupTimeout = null;
        let isBgmEnabled = false;
        let bgmInterval = null;
        let bgmStep = 0;

        let scene, camera, renderer;
        let arenaGroup, cageLine, gridHelper, ceilingGridHelper, floorPlane;
        let leftGridHelper, rightGridHelper, backGridHelper;
        let tunnelFramesGroup, spaceDustSystem;
        let ambientLight, dirLight, pointLight1, pointLight2;
        let spheres3D = [];
        let particles3D = [];
        let flyingCoins3D = [];
        let shockwaves3D = [];
        let wallRipples3D = [];
        let stageEnvGroup;
        let stageObjects = [];
        let currentStageKey = 'base';
        let manualStageIndex = -1;
        let cutinTimeout = null;

        let isImmersiveDive = true;
        let camWarpBoost = 0;
        let baseCameraFov = 68;
        let userZoomOffset = 0;
        let isGyroEnabled = false;
        let gyroTheta = 0;
        let gyroPhi = 0;
        let pinchStartDist = 0;
        let lastFrontHitTime = 0;

        const STAGES = [
            {
                key: 'base',
                minCombo: 0,
                name: '🏠 ベース空間',
                sub: 'STARTING CUBE',
                bgHex: null,
                fogDensity: 0.015,
                cageHex: null,
                gridMain: null,
                gridSub: null,
                ambientHex: null
            },
            {
                key: 'ocean',
                minCombo: 5,
                name: '🫧 深海・アクアオーシャン',
                sub: '5+ COMBO STAGE',
                bgHex: 0x031b33,
                fogDensity: 0.022,
                cageHex: 0x00e5ff,
                gridMain: 0x00b4d8,
                gridSub: 0x023e8a,
                ambientHex: 0x48cae4
            },
            {
                key: 'sakura',
                minCombo: 10,
                name: '🌸 幻想・桜吹雪の庭',
                sub: '10+ COMBO STAGE',
                bgHex: 0x1f0d29,
                fogDensity: 0.016,
                cageHex: 0xff85c0,
                gridMain: 0xf759ab,
                gridSub: 0x4a154b,
                ambientHex: 0xffadd2
            },
            {
                key: 'sky',
                minCombo: 15,
                name: '☁️ 蒼穹・スカイアイランド',
                sub: '15+ COMBO STAGE',
                bgHex: 0x1e6091,
                fogDensity: 0.012,
                cageHex: 0xffffff,
                gridMain: 0x90e0ef,
                gridSub: 0x168aad,
                ambientHex: 0xffffff
            },
            {
                key: 'magma',
                minCombo: 25,
                name: '🌋 灼熱・マグマコア',
                sub: '25+ COMBO STAGE',
                bgHex: 0x1c0404,
                fogDensity: 0.018,
                cageHex: 0xff4500,
                gridMain: 0xff6d00,
                gridSub: 0x4a0e0e,
                ambientHex: 0xff9e00
            },
            {
                key: 'aurora',
                minCombo: 35,
                name: '❄️ 極光・オーロラ氷河',
                sub: '35+ COMBO STAGE',
                bgHex: 0x061a23,
                fogDensity: 0.015,
                cageHex: 0x2ec4b6,
                gridMain: 0x64dfdf,
                gridSub: 0x0b3948,
                ambientHex: 0x80ffdb
            },
            {
                key: 'space',
                minCombo: 45,
                name: '🪐 ギャラクシー・宇宙',
                sub: '45+ COMBO STAGE',
                bgHex: 0x03020d,
                fogDensity: 0.008,
                cageHex: 0xa855f7,
                gridMain: 0x7209b7,
                gridSub: 0x1b0633,
                ambientHex: 0xc77dff
            },
            {
                key: 'prism',
                minCombo: 55,
                name: '🌈 虹彩・プリズムディメンション',
                sub: '55+ COMBO STAGE',
                bgHex: 0x070318,
                fogDensity: 0.009,
                cageHex: 0xffea00,
                gridMain: 0xff007f,
                gridSub: 0x00f5d4,
                ambientHex: 0xffffff
            },
            {
                key: 'warp',
                minCombo: 65,
                name: '🕳️ 超時空・ワープホール',
                sub: '65+ COMBO STAGE',
                bgHex: 0x050014,
                fogDensity: 0.01,
                cageHex: 0x00f5d4,
                gridMain: 0xf15bb5,
                gridSub: 0x2b0938,
                ambientHex: 0x00bbf9
            },
            {
                key: 'god',
                minCombo: 80,
                name: '👑 究極・黄金の神域',
                sub: '80+ COMBO MASTER STAGE',
                bgHex: 0x1c1403,
                fogDensity: 0.012,
                cageHex: 0xffd700,
                gridMain: 0xffbe0b,
                gridSub: 0x5c4308,
                ambientHex: 0xfff3b0
            }
        ];

        const ARENA = { halfX: 11, halfY: 8.5, halfZ: 8.5 };

        let camTheta = 0, camPhi = 0.12, camRadius = 18.5;
        let autoOrbit = true, arenaShakeIntensity = 0;
        let isDraggingCamera = false;
        let prevPointer = { x: 0, y: 0 };

        const sharedSphereGeo = new THREE.SphereGeometry(1, 32, 32);
        const sharedBoxGeo = new THREE.BoxGeometry(1, 1, 1);
        const sharedCoreGeo = new THREE.OctahedronGeometry(0.48, 1);
        const sharedRingGeo = new THREE.TorusGeometry(1.22, 0.045, 12, 48);
        const sharedShadowGeo = new THREE.CircleGeometry(1, 24);
        sharedShadowGeo.rotateX(-Math.PI / 2);
        const sharedWallRippleGeo = new THREE.RingGeometry(0.25, 0.42, 28);
        const sharedTrailGeo = new THREE.SphereGeometry(0.28, 10, 10);
        const sharedLaserGeo = new THREE.CylinderGeometry(0.025, 0.025, ARENA.halfY * 2, 6);
        const sharedParticleGeo = new THREE.OctahedronGeometry(0.28, 0);

        let sphereGlossTex = null;

        function getSphereGlossTexture() {
            if (!sphereGlossTex) {
                const c = document.createElement('canvas');
                c.width = 256; c.height = 256;
                const ctx = c.getContext('2d');
                const grad = ctx.createRadialGradient(85, 75, 10, 128, 128, 128);
                grad.addColorStop(0, 'rgba(255, 255, 255, 0.95)');
                grad.addColorStop(0.22, 'rgba(255, 255, 255, 0.45)');
                grad.addColorStop(0.65, 'rgba(180, 220, 255, 0.12)');
                grad.addColorStop(1, 'rgba(0, 0, 0, 0.45)');
                ctx.fillStyle = grad;
                ctx.fillRect(0, 0, 256, 256);
                sphereGlossTex = new THREE.CanvasTexture(c);
            }
            return sphereGlossTex;
        }

        function buildStageEnvironment(stageKey) {
            while (stageEnvGroup.children.length > 0) {
                const obj = stageEnvGroup.children[0];
                stageEnvGroup.remove(obj);
                if (obj.geometry) obj.geometry.dispose();
                if (obj.material) {
                    if (Array.isArray(obj.material)) obj.material.forEach(m => m.dispose());
                    else obj.material.dispose();
                }
            }
            stageObjects = [];
            currentStageKey = stageKey;

            if (stageKey === 'base') {
                const cubeGeo = new THREE.BoxGeometry(0.9, 0.9, 0.9);
                for (let i = 0; i < 18; i++) {
                    const cMat = new THREE.MeshBasicMaterial({ color: THEMES[currentTheme].cageColor, wireframe: true, transparent: true, opacity: 0.25 });
                    const cMesh = new THREE.Mesh(cubeGeo, cMat);
                    cMesh.position.set((Math.random() - 0.5) * 24, (Math.random() - 0.5) * 18, (Math.random() - 0.5) * 16);
                    stageEnvGroup.add(cMesh);
                    stageObjects.push({ type: 'floatCube', mesh: cMesh, speedY: 0.015 + Math.random() * 0.02, rotSpeed: 0.01 + Math.random() * 0.015 });
                }
            } else if (stageKey === 'ocean') {
                const bubbleGeo = new THREE.SphereGeometry(0.35, 12, 12);
                for (let i = 0; i < 45; i++) {
                    const bMat = new THREE.MeshBasicMaterial({ color: 0x00ffff, transparent: true, opacity: 0.35 });
                    const bMesh = new THREE.Mesh(bubbleGeo, bMat);
                    bMesh.position.set((Math.random() - 0.5) * ARENA.halfX * 1.9, (Math.random() - 0.5) * ARENA.halfY * 1.9, (Math.random() - 0.5) * ARENA.halfZ * 1.8);
                    stageEnvGroup.add(bMesh);
                    stageObjects.push({ type: 'bubble', mesh: bMesh, speedY: 0.04 + Math.random() * 0.05, wobbleOffset: Math.random() * Math.PI });
                }
            } else if (stageKey === 'sakura') {
                const petalMat = new THREE.MeshBasicMaterial({ color: 0xffb5e8, transparent: true, opacity: 0.8, side: THREE.DoubleSide });
                for (let i = 0; i < 55; i++) {
                    const pMesh = new THREE.Mesh(new THREE.PlaneGeometry(0.35, 0.25), petalMat);
                    pMesh.position.set((Math.random() - 0.5) * 22, (Math.random() - 0.5) * 16, (Math.random() - 0.5) * 16);
                    stageEnvGroup.add(pMesh);
                    stageObjects.push({ type: 'petal', mesh: pMesh, speedY: -0.03 - Math.random() * 0.03, speedX: 0.02 + Math.random() * 0.02, rotSpeed: 0.02 + Math.random() * 0.03, phase: Math.random() * Math.PI });
                }
            } else if (stageKey === 'sky') {
                const cloudGeo = new THREE.DodecahedronGeometry(1.8, 1);
                const cloudMat = new THREE.MeshBasicMaterial({ color: 0xffffff, transparent: true, opacity: 0.55 });
                for (let i = 0; i < 10; i++) {
                    const cMesh = new THREE.Mesh(cloudGeo, cloudMat);
                    cMesh.position.set((Math.random() - 0.5) * 24, 5 + Math.random() * 3, -4 + (Math.random() - 0.5) * 10);
                    cMesh.scale.set(2.5 + Math.random() * 1.5, 1, 1.5);
                    stageEnvGroup.add(cMesh);
                    stageObjects.push({ type: 'cloud', mesh: cMesh, speedX: 0.015 + Math.random() * 0.015 });
                }
            } else if (stageKey === 'magma') {
                const emberGeo = new THREE.OctahedronGeometry(0.32, 0);
                for (let i = 0; i < 35; i++) {
                    const eMat = new THREE.MeshBasicMaterial({ color: i % 2 === 0 ? 0xff4500 : 0xffaa00, transparent: true, opacity: 0.8 });
                    const eMesh = new THREE.Mesh(emberGeo, eMat);
                    eMesh.position.set((Math.random() - 0.5) * 20, -ARENA.halfY + Math.random() * 16, (Math.random() - 0.5) * 14);
                    stageEnvGroup.add(eMesh);
                    stageObjects.push({ type: 'ember', mesh: eMesh, speedY: 0.05 + Math.random() * 0.06, wobble: Math.random() * Math.PI, rotSpeed: 0.05 });
                }
            } else if (stageKey === 'aurora') {
                const crystalGeo = new THREE.OctahedronGeometry(0.6, 0);
                const crystalMat = new THREE.MeshStandardMaterial({ color: 0x64dfdf, emissive: 0x2ec4b6, emissiveIntensity: 0.6, roughness: 0.1, metalness: 0.8 });
                for (let i = 0; i < 18; i++) {
                    const cMesh = new THREE.Mesh(crystalGeo, crystalMat);
                    cMesh.position.set((Math.random() - 0.5) * 20, (Math.random() - 0.5) * 14, (Math.random() - 0.5) * 14);
                    stageEnvGroup.add(cMesh);
                    stageObjects.push({ type: 'iceCrystal', mesh: cMesh, speedY: -0.015 - Math.random() * 0.02, rotSpeed: 0.02 });
                }
            } else if (stageKey === 'space') {
                const planetGeo = new THREE.SphereGeometry(3.5, 32, 32);
                const planetMat = new THREE.MeshStandardMaterial({ color: 0x8b5cf6, roughness: 0.6, metalness: 0.2 });
                const planet = new THREE.Mesh(planetGeo, planetMat);
                planet.position.set(12, 8, -25);
                stageEnvGroup.add(planet);
                stageObjects.push({ type: 'planet', mesh: planet, rotSpeed: 0.005 });
            } else if (stageKey === 'prism') {
                const starGeo = new THREE.OctahedronGeometry(0.8, 1);
                const starMat = new THREE.MeshBasicMaterial({ color: 0xffea00 });
                for (let i = 0; i < 20; i++) {
                    const sMesh = new THREE.Mesh(starGeo, starMat);
                    sMesh.position.set((Math.random() - 0.5) * 22, (Math.random() - 0.5) * 15, (Math.random() - 0.5) * 16);
                    stageEnvGroup.add(sMesh);
                    stageObjects.push({ type: 'star', mesh: sMesh, phase: Math.random() * Math.PI });
                }
            } else if (stageKey === 'warp') {
                const ringGeo = new THREE.TorusGeometry(5, 0.15, 16, 64);
                for (let i = 0; i < 8; i++) {
                    const rMat = new THREE.MeshBasicMaterial({ color: 0x00f5d4, transparent: true, opacity: 0.4 });
                    const rMesh = new THREE.Mesh(ringGeo, rMat);
                    rMesh.position.z = -30 + i * 5;
                    stageEnvGroup.add(rMesh);
                    stageObjects.push({ type: 'warpRing', mesh: rMesh, speedZ: 0.25 });
                }
            } else if (stageKey === 'god') {
                const goldGeo = new THREE.TorusGeometry(4.5, 0.1, 16, 64);
                const goldMat = new THREE.MeshStandardMaterial({ color: 0xffd700, emissive: 0xb45309, emissiveIntensity: 0.5, metalness: 0.9, roughness: 0.1 });
                for (let i = 0; i < 3; i++) {
                    const gMesh = new THREE.Mesh(goldGeo, goldMat);
                    gMesh.rotation.set(i * 0.8, i * 1.2, 0);
                    stageEnvGroup.add(gMesh);
                    stageObjects.push({ type: 'godRing', mesh: gMesh, axis: i });
                }
            }
        }

        function checkStageProgression(showCutin = true) {
            if (manualStageIndex !== -1) return;
            let targetStage = STAGES[0];
            for (let i = STAGES.length - 1; i >= 0; i--) {
                if (combo >= STAGES[i].minCombo) {
                    targetStage = STAGES[i];
                    break;
                }
            }
            if (targetStage.key !== currentStageKey) {
                applyStageVisuals(targetStage, showCutin);
            }
        }

        window.cycleStageOverride = () => {
            if (manualStageIndex === -1) {
                manualStageIndex = 0;
            } else {
                manualStageIndex++;
                if (manualStageIndex >= STAGES.length) {
                    manualStageIndex = -1; // AUTOに戻す
                }
            }
            const stageObj = manualStageIndex === -1 ? (STAGES.slice().reverse().find(s => combo >= s.minCombo) || STAGES[0]) : STAGES[manualStageIndex];
            applyStageVisuals(stageObj, true);
        };

        function setupCameraInteraction() {
            const container = document.getElementById('canvas');
            if (!container) return;

            container.addEventListener('pointerdown', (e) => {
                if (isGyroEnabled) return;
                isDraggingCamera = true;
                prevPointer = { x: e.clientX, y: e.clientY };
            });

            window.addEventListener('pointermove', (e) => {
                if (!isDraggingCamera || isGyroEnabled) return;
                const dx = e.clientX - prevPointer.x;
                const dy = e.clientY - prevPointer.y;
                prevPointer = { x: e.clientX, y: e.clientY };

                camTheta -= dx * 0.007;
                camPhi = Math.max(-1.1, Math.min(1.1, camPhi + dy * 0.007));
                autoOrbit = false;
                const orbitBtn = document.getElementById('auto-orbit-btn');
                if (orbitBtn) orbitBtn.innerHTML = '<span>🔄 遊泳: OFF</span>';
            });

            window.addEventListener('pointerup', () => { isDraggingCamera = false; });

            // 2本指ピンチズーム対応
            container.addEventListener('touchstart', (e) => {
                if (e.touches.length === 2) {
                    pinchStartDist = Math.hypot(
                        e.touches[0].clientX - e.touches[1].clientX,
                        e.touches[0].clientY - e.touches[1].clientY
                    );
                }
            }, { passive: true });

            container.addEventListener('touchmove', (e) => {
                if (e.touches.length === 2 && pinchStartDist > 0) {
                    const dist = Math.hypot(
                        e.touches[0].clientX - e.touches[1].clientX,
                        e.touches[0].clientY - e.touches[1].clientY
                    );
                    const diff = dist - pinchStartDist;
                    userZoomOffset = Math.max(-8, Math.min(6, userZoomOffset - diff * 0.03));
                }
            }, { passive: true });

            container.addEventListener('wheel', (e) => {
                userZoomOffset = Math.max(-8, Math.min(6, userZoomOffset + e.deltaY * 0.015));
            }, { passive: true });
        }

        window.toggleAutoOrbit = () => {
            autoOrbit = !autoOrbit;
            const btn = document.getElementById('auto-orbit-btn');
            if (btn) btn.innerHTML = `<span>🔄 遊泳: ${autoOrbit ? 'ON' : 'OFF'}</span>`;
        };

        window.toggleImmersiveMode = () => {
            isImmersiveDive = !isImmersiveDive;
            const btn = document.getElementById('immersive-btn');
            if (btn) btn.innerHTML = `<span>🚀 没入: ${isImmersiveDive ? 'ON' : 'OFF'}</span>`;
            handleResize();
        };

        window.toggleGyroMode = async () => {
            if (!isGyroEnabled) {
                if (typeof DeviceOrientationEvent !== 'undefined' && typeof DeviceOrientationEvent.requestPermission === 'function') {
                    try {
                        const response = await DeviceOrientationEvent.requestPermission();
                        if (response === 'granted') {
                            activateGyro();
                        } else {
                            const textEl = document.getElementById('hype-text');
                            if (textEl) textEl.innerText = 'ジャイロセンサーの許可が得られませんでした。ブラウザ設定をご確認ください。';
                        }
                    } catch (e) { activateGyro(); }
                } else {
                    activateGyro();
                }
            } else {
                deactivateGyro();
            }
        };

        function activateGyro() {
            isGyroEnabled = true;
            window.addEventListener('deviceorientation', handleDeviceOrientation, true);
            const btn = document.getElementById('gyro-btn');
            if (btn) btn.innerHTML = '<span>📱 傾き: ON</span>';
        }

        function deactivateGyro() {
            isGyroEnabled = false;
            window.removeEventListener('deviceorientation', handleDeviceOrientation, true);
            const btn = document.getElementById('gyro-btn');
            if (btn) btn.innerHTML = '<span>📱 傾き: OFF</span>';
        }

        function handleDeviceOrientation(e) {
            if (e.gamma !== null && e.beta !== null) {
                gyroTheta = (e.gamma / 45) * 1.2;
                gyroPhi = ((e.beta - 45) / 45) * 0.8;
            }
        }

        window.resetCameraView = () => {
            camTheta = 0;
            camPhi = 0.12;
            userZoomOffset = 0;
            autoOrbit = true;
            const orbitBtn = document.getElementById('auto-orbit-btn');
            if (orbitBtn) orbitBtn.innerHTML = '<span>🔄 遊泳: ON</span>';
        };

        function initThreeJS() {
            scene = new THREE.Scene();
            const themeData = THEMES[currentTheme];
            scene.background = new THREE.Color(themeData.bgHex);
            scene.fog = new THREE.FogExp2(themeData.bgHex, 0.012);

            camera = new THREE.PerspectiveCamera(68, window.innerWidth / window.innerHeight, 0.1, 220);
            updateCameraPosition();

            renderer = new THREE.WebGLRenderer({ antialias: true, powerPreference: "high-performance" });
            renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
            renderer.domElement.className = 'webgl-canvas';

            ambientLight = new THREE.AmbientLight(themeData.ambientHex, 0.8);
            scene.add(ambientLight);

            dirLight = new THREE.DirectionalLight(0xffffff, 1.2);
            dirLight.position.set(15, 25, 20);
            scene.add(dirLight);

            pointLight1 = new THREE.PointLight(themeData.cageColor, 1.5, 65);
            pointLight1.position.set(-12, 10, 12);
            scene.add(pointLight1);

            pointLight2 = new THREE.PointLight(0xffffff, 1.0, 65);
            pointLight2.position.set(12, -10, 10);
            scene.add(pointLight2);

            stageEnvGroup = new THREE.Group();
            scene.add(stageEnvGroup);

            arenaGroup = new THREE.Group();
            scene.add(arenaGroup);

            buildArenaGeometry();
            buildImmersiveTunnelAndDust();
            buildStageEnvironment('base');
            setupCameraInteraction();

            attachRendererToContainer('start-bg');
        }

        function buildImmersiveTunnelAndDust() {
            const themeData = THEMES[currentTheme];
            tunnelFramesGroup = new THREE.Group();
            const frameGeo = new THREE.EdgesGeometry(new THREE.BoxGeometry(ARENA.halfX * 2.35, ARENA.halfY * 2.35, 0.2));
            for (let i = 0; i < 7; i++) {
                const mat = new THREE.LineBasicMaterial({ color: themeData.cageColor, transparent: true, opacity: 0.22 });
                const frame = new THREE.LineSegments(frameGeo, mat);
                frame.position.z = -35 + i * 9;
                tunnelFramesGroup.add(frame);
            }
            scene.add(tunnelFramesGroup);

            const dustCount = 220;
            const dustGeo = new THREE.BufferGeometry();
            const positions = new Float32Array(dustCount * 3);
            for (let i = 0; i < dustCount; i++) {
                positions[i * 3] = (Math.random() - 0.5) * 46;
                positions[i * 3 + 1] = (Math.random() - 0.5) * 36;
                positions[i * 3 + 2] = (Math.random() - 0.5) * 70;
            }
            dustGeo.setAttribute('position', new THREE.BufferAttribute(positions, 3));
            const dustMat = new THREE.PointsMaterial({ color: 0xffffff, size: 0.38, transparent: true, opacity: 0.75 });
            spaceDustSystem = new THREE.Points(dustGeo, dustMat);
            scene.add(spaceDustSystem);
        }

        function updateImmersiveTunnelAndDust() {
            const speedMultiplier = (isFeverMode ? 3.2 : 1.0) + camWarpBoost * 6.0;

            if (tunnelFramesGroup) {
                tunnelFramesGroup.children.forEach((frame) => {
                    frame.position.z += 0.16 * speedMultiplier;
                    if (frame.position.z > 24) frame.position.z = -39;
                    const distFactor = 1 - Math.abs(frame.position.z) / 40;
                    frame.material.opacity = Math.max(0.04, distFactor * 0.32);
                });
            }

            if (spaceDustSystem) {
                const posAttr = spaceDustSystem.geometry.attributes.position;
                for (let i = 0; i < posAttr.count; i++) {
                    let z = posAttr.getZ(i);
                    z += 0.24 * speedMultiplier;
                    if (z > 28) {
                        z = -42;
                        posAttr.setX(i, (Math.random() - 0.5) * 46);
                        posAttr.setY(i, (Math.random() - 0.5) * 36);
                    }
                    posAttr.setZ(i, z);
                }
                posAttr.needsUpdate = true;
            }
        }

        function spawnShockwave3D(colorHex = 0x38bdf8) {
            if (!arenaGroup) return;
            const mat = new THREE.MeshBasicMaterial({ color: colorHex, transparent: true, opacity: 0.9, side: THREE.DoubleSide });
            const mesh = new THREE.Mesh(sharedRingGeo, mat);
            mesh.position.set(0, 0, 2);
            mesh.scale.setScalar(1.2);
            arenaGroup.add(mesh);
            shockwaves3D.push({ mesh, mat, scale: 1.2, opacity: 0.9 });

            camWarpBoost = 1.15;
            const hud = document.getElementById('cockpit-hud');
            if (hud) {
                hud.classList.add('warp-boost');
                setTimeout(() => hud.classList.remove('warp-boost'), 260);
            }
        }

        function spawnWallRipple3D(x, y, z, wallType, colorHex) {
            if (!arenaGroup) return;
            const isFront = wallType === 'front';
            const mat = new THREE.MeshBasicMaterial({
                color: isFront ? 0xffffff : colorHex,
                transparent: true,
                opacity: isFront ? 0.95 : 0.65,
                side: THREE.DoubleSide,
                depthWrite: false
            });
            const mesh = new THREE.Mesh(sharedWallRippleGeo, mat);
            mesh.position.set(x, y, z);

            if (wallType === 'left' || wallType === 'right') mesh.rotation.y = Math.PI / 2;
            else if (wallType === 'top' || wallType === 'bottom') mesh.rotation.x = Math.PI / 2;

            arenaGroup.add(mesh);
            wallRipples3D.push({ mesh, mat, scale: isFront ? 1.2 : 0.8, growSpeed: isFront ? 0.42 : 0.25, opacity: isFront ? 0.95 : 0.65 });

            if (isFront && isStarted) {
                const now = performance.now();
                if (now - lastFrontHitTime > 220) {
                    lastFrontHitTime = now;
                    const hud = document.getElementById('cockpit-hud');
                    if (hud) {
                        hud.classList.add('front-hit');
                        setTimeout(() => hud.classList.remove('front-hit'), 130);
                    }
                    playSound('bounce');
                }
            }
        }

        function updateStageEnvironment() {
            const t = performance.now() * 0.001;
            for (let i = 0; i < stageObjects.length; i++) {
                const obj = stageObjects[i];
                if (obj.type === 'floatCube') {
                    obj.mesh.position.y += obj.speedY;
                    obj.mesh.rotation.x += obj.rotSpeed;
                    obj.mesh.rotation.y += obj.rotSpeed;
                    if (obj.mesh.position.y > 16) obj.mesh.position.y = -16;
                } else if (obj.type === 'bubble') {
                    obj.mesh.position.y += obj.speedY;
                    obj.mesh.position.x += Math.sin(t * 2 + obj.wobbleOffset) * 0.03;
                    if (obj.mesh.position.y > 16) {
                        obj.mesh.position.y = -16;
                        obj.mesh.position.x = (Math.random() - 0.5) * 38;
                    }
                } else if (obj.type === 'waterRing') {
                    const s = obj.baseScale + Math.sin(t * 1.5 + obj.phase) * 1.5;
                    obj.mesh.scale.setScalar(s);
                } else if (obj.type === 'petal') {
                    obj.mesh.position.y += obj.speedY;
                    obj.mesh.position.x += Math.sin(t + obj.phase) * 0.03 + obj.speedX;
                    obj.mesh.rotation.x += obj.rotSpeed;
                    obj.mesh.rotation.y += obj.rotSpeed;
                    if (obj.mesh.position.y < -16) obj.mesh.position.y = 16;
                    if (obj.mesh.position.x > 22) obj.mesh.position.x = -22;
                } else if (obj.type === 'cloud') {
                    obj.mesh.position.x += obj.speedX;
                    if (obj.mesh.position.x > 28) obj.mesh.position.x = -28;
                } else if (obj.type === 'ember') {
                    obj.mesh.position.y += obj.speedY;
                    obj.mesh.position.x += Math.sin(t * 3 + obj.wobble) * 0.025;
                    obj.mesh.rotation.x += obj.rotSpeed;
                    obj.mesh.rotation.y += obj.rotSpeed;
                    if (obj.mesh.position.y > 16) obj.mesh.position.y = -16;
                } else if (obj.type === 'iceCrystal') {
                    obj.mesh.position.y += obj.speedY;
                    obj.mesh.rotation.y += obj.rotSpeed;
                    if (obj.mesh.position.y < -16) obj.mesh.position.y = 16;
                } else if (obj.type === 'planet') {
                    obj.mesh.rotation.y += obj.rotSpeed;
                } else if (obj.type === 'star') {
                    const pulse = 0.25 + (Math.sin(t * 3 + obj.phase) + 1) * 0.18;
                    obj.mesh.scale.setScalar(pulse);
                } else if (obj.type === 'warpRing') {
                    obj.mesh.position.z += obj.speedZ;
                    obj.mesh.rotation.z += 0.015;
                    if (obj.mesh.position.z > 18) obj.mesh.position.z = -45;
                } else if (obj.type === 'godRing') {
                    if (obj.axis === 0) {
                        obj.mesh.rotation.x += 0.012;
                        obj.mesh.rotation.y += 0.008;
                    } else if (obj.axis === 1) {
                        obj.mesh.rotation.y -= 0.015;
                        obj.mesh.rotation.z += 0.01;
                    } else {
                        obj.mesh.rotation.z -= 0.012;
                        obj.mesh.rotation.x -= 0.008;
                    }
                }
            }
        }

        function applyStageVisuals(stageObj, showCutin = true) {
            if (!scene) return;
            const themeData = THEMES[currentTheme];
            const bgHex = stageObj.bgHex !== null ? stageObj.bgHex : themeData.bgHex;
            const cageHex = stageObj.cageHex !== null ? stageObj.cageHex : themeData.cageColor;
            const gMain = stageObj.gridMain !== null ? stageObj.gridMain : themeData.gridMain;
            const gSub = stageObj.gridSub !== null ? stageObj.gridSub : themeData.gridSub;
            const ambHex = stageObj.ambientHex !== null ? stageObj.ambientHex : themeData.ambientHex;

            scene.background = new THREE.Color(bgHex);
            scene.fog.color.setHex(bgHex);
            scene.fog.density = stageObj.fogDensity || 0.015;

            ambientLight.color.setHex(ambHex);
            pointLight1.color.setHex(cageHex);
            cageLine.material.color.setHex(cageHex);
            floorPlane.material.color.setHex(cageHex);

            if (tunnelFramesGroup) {
                tunnelFramesGroup.children.forEach(f => f.material.color.setHex(cageHex));
            }
            if (spaceDustSystem) {
                spaceDustSystem.material.color.setHex(ambHex);
            }

            arenaGroup.remove(gridHelper);
            gridHelper.geometry.dispose();
            gridHelper.material.dispose();
            gridHelper = new THREE.GridHelper(ARENA.halfX * 2, 16, gMain, gSub);
            gridHelper.position.y = -ARENA.halfY;
            gridHelper.scale.z = ARENA.halfZ / ARENA.halfX;
            gridHelper.material.transparent = true;
            gridHelper.material.opacity = 0.65;
            arenaGroup.add(gridHelper);

            if (ceilingGridHelper) {
                arenaGroup.remove(ceilingGridHelper);
                ceilingGridHelper.geometry.dispose();
                ceilingGridHelper.material.dispose();
            }
            ceilingGridHelper = new THREE.GridHelper(ARENA.halfX * 2, 16, gMain, gSub);
            ceilingGridHelper.position.y = ARENA.halfY;
            ceilingGridHelper.scale.z = ARENA.halfZ / ARENA.halfX;
            ceilingGridHelper.material.transparent = true;
            ceilingGridHelper.material.opacity = 0.35;
            arenaGroup.add(ceilingGridHelper);

            rebuildSideAndBackGrids(gMain, gSub);
            buildStageEnvironment(stageObj.key);
            spawnShockwave3D(cageHex);

            const ind = document.getElementById('stage-indicator');
            if (ind) {
                const modeLabel = manualStageIndex === -1 ? '連動' : '固定';
                ind.innerText = `${stageObj.name} (${modeLabel})`;
            }

            if (showCutin && isStarted && stageObj.key !== 'base') {
                const cutin = document.getElementById('stage-cutin');
                const title = document.getElementById('stage-cutin-title');
                if (cutin && title) {
                    title.innerText = stageObj.name;
                    cutin.classList.add('show');
                    if (cutinTimeout) clearTimeout(cutinTimeout);
                    cutinTimeout = setTimeout(() => {
                        cutin.classList.remove('show');
                    }, 2400);
                }
            }
        }

        function rebuildSideAndBackGrids(gMain, gSub) {
            [leftGridHelper, rightGridHelper, backGridHelper].forEach(h => {
                if (h) {
                    arenaGroup.remove(h);
                    h.geometry.dispose();
                    h.material.dispose();
                }
            });

            leftGridHelper = new THREE.GridHelper(ARENA.halfY * 2, 12, gMain, gSub);
            leftGridHelper.rotation.z = Math.PI / 2;
            leftGridHelper.position.x = -ARENA.halfX;
            leftGridHelper.scale.z = ARENA.halfZ / ARENA.halfY;
            leftGridHelper.material.transparent = true;
            leftGridHelper.material.opacity = 0.28;
            arenaGroup.add(leftGridHelper);

            rightGridHelper = new THREE.GridHelper(ARENA.halfY * 2, 12, gMain, gSub);
            rightGridHelper.rotation.z = Math.PI / 2;
            rightGridHelper.position.x = ARENA.halfX;
            rightGridHelper.scale.z = ARENA.halfZ / ARENA.halfY;
            rightGridHelper.material.transparent = true;
            rightGridHelper.material.opacity = 0.28;
            arenaGroup.add(rightGridHelper);

            backGridHelper = new THREE.GridHelper(ARENA.halfX * 2, 14, gMain, gSub);
            backGridHelper.rotation.x = Math.PI / 2;
            backGridHelper.position.z = -ARENA.halfZ;
            backGridHelper.scale.z = ARENA.halfY / ARENA.halfX;
            backGridHelper.material.transparent = true;
            backGridHelper.material.opacity = 0.32;
            arenaGroup.add(backGridHelper);
        }

        function buildArenaGeometry() {
            const themeData = THEMES[currentTheme];
            const boxGeo = new THREE.BoxGeometry(ARENA.halfX * 2, ARENA.halfY * 2, ARENA.halfZ * 2);
            const edgesGeo = new THREE.EdgesGeometry(boxGeo);
            const lineMat = new THREE.LineBasicMaterial({ color: themeData.cageColor, transparent: true, opacity: 0.72 });
            cageLine = new THREE.LineSegments(edgesGeo, lineMat);
            arenaGroup.add(cageLine);

            gridHelper = new THREE.GridHelper(ARENA.halfX * 2, 16, themeData.gridMain, themeData.gridSub);
            gridHelper.position.y = -ARENA.halfY;
            gridHelper.scale.z = ARENA.halfZ / ARENA.halfX;
            gridHelper.material.transparent = true;
            gridHelper.material.opacity = 0.65;
            arenaGroup.add(gridHelper);

            ceilingGridHelper = new THREE.GridHelper(ARENA.halfX * 2, 16, themeData.gridMain, themeData.gridSub);
            ceilingGridHelper.position.y = ARENA.halfY;
            ceilingGridHelper.scale.z = ARENA.halfZ / ARENA.halfX;
            ceilingGridHelper.material.transparent = true;
            ceilingGridHelper.material.opacity = 0.35;
            arenaGroup.add(ceilingGridHelper);

            rebuildSideAndBackGrids(themeData.gridMain, themeData.gridSub);

            const planeGeo = new THREE.PlaneGeometry(ARENA.halfX * 2, ARENA.halfZ * 2);
            planeGeo.rotateX(-Math.PI / 2);
            const planeMat = new THREE.MeshBasicMaterial({ color: themeData.cageColor, transparent: true, opacity: 0.08, side: THREE.DoubleSide });
            floorPlane = new THREE.Mesh(planeGeo, planeMat);
            floorPlane.position.y = -ARENA.halfY - 0.02;
            arenaGroup.add(floorPlane);
        }

        function updateThreeTheme() {
            if (!scene) return;
            const currentStageObj = STAGES.find(s => s.key === currentStageKey) || STAGES[0];
            applyStageVisuals(currentStageObj, false);

            const palette = THEMES[currentTheme].colors;
            spheres3D.forEach((s, idx) => {
                const newCol = palette[idx % palette.length];
                s.setColor(newCol);
            });
        }

        function attachRendererToContainer(containerId) {
            const container = document.getElementById(containerId);
            if (!container || !renderer) return;
            if (renderer.domElement.parentElement !== container) {
                container.insertBefore(renderer.domElement, container.firstChild);
            }
            handleResize();
        }

        function handleResize() {
            if (!renderer || !camera) return;
            const w = window.innerWidth;
            const h = window.innerHeight;
            if (w === 0 || h === 0) return;

            camera.aspect = w / h;
            const aspectFactor = w / h;

            if (isImmersiveDive) {
                baseCameraFov = aspectFactor < 0.75 ? 76 : 66;
                camera.fov = baseCameraFov;
                camRadius = aspectFactor < 0.75 ? 18.2 / Math.max(0.64, aspectFactor * 0.98) : 16.5;
            } else {
                baseCameraFov = 50;
                camera.fov = baseCameraFov;
                camRadius = aspectFactor < 0.75 ? 26 / Math.max(0.54, aspectFactor * 0.95) : 25;
            }

            camera.updateProjectionMatrix();
            renderer.setSize(w, h);
        }

        function updateCameraPosition() {
            const t = performance.now() * 0.0004;
            if (autoOrbit && !isDraggingCamera) {
                camTheta = t * 0.55;
            }

            if (isGyroEnabled) {
                camTheta += gyroTheta * 0.05;
                camPhi = Math.max(-1.1, Math.min(1.1, camPhi + gyroPhi * 0.05));
            }

            if (camWarpBoost > 0) {
                camWarpBoost = Math.max(0, camWarpBoost - 0.035);
            }

            const effectiveRadius = Math.max(8.5, camRadius + userZoomOffset - camWarpBoost * 5.2);

            if (camera.fov !== undefined) {
                const targetFov = baseCameraFov + camWarpBoost * 14;
                if (Math.abs(camera.fov - targetFov) > 0.05) {
                    camera.fov += (targetFov - camera.fov) * 0.25;
                    camera.updateProjectionMatrix();
                }
            }

            camera.position.x = effectiveRadius * Math.sin(camTheta) * Math.cos(camPhi);
            camera.position.y = effectiveRadius * Math.sin(camPhi);
            camera.position.z = effectiveRadius * Math.cos(camTheta) * Math.cos(camPhi);

            const lookX = isImmersiveDive ? Math.sin(t * 0.9) * 1.35 : 0;
            const lookY = isImmersiveDive ? Math.cos(t * 0.7) * 0.9 - 0.35 : -0.35;
            camera.lookAt(lookX, lookY, -2.0);

            if (isImmersiveDive && isStarted) {
                camera.rotation.z += (-camTheta * 0.22 - camera.rotation.z) * 0.14;
            }
        }

        class Sphere3D {
            constructor(colorHex, existingSpheres) {
                this.radius = 1.12 + Math.random() * 0.65;
                this.group = new THREE.Group();

                this.mat = new THREE.MeshStandardMaterial({
                    color: new THREE.Color(colorHex),
                    map: getSphereGlossTexture(),
                    emissive: new THREE.Color(colorHex),
                    emissiveIntensity: 0.42,
                    roughness: 0.08,
                    metalness: 0.28
                });
                this.mesh = new THREE.Mesh(sharedSphereGeo, this.mat);
                this.mesh.scale.setScalar(this.radius);
                this.group.add(this.mesh);

                this.specMat = new THREE.MeshBasicMaterial({ color: 0xffffff, transparent: true, opacity: 0.75 });
                this.specMesh = new THREE.Mesh(sharedTrailGeo, this.specMat);
                this.specMesh.scale.setScalar(this.radius * 0.95);
                this.specMesh.position.set(-this.radius * 0.35, this.radius * 0.38, this.radius * 0.42);
                this.group.add(this.specMesh);

                this.coreMat = new THREE.MeshBasicMaterial({ color: 0xffffff, wireframe: true, transparent: true, opacity: 0.75 });
                this.coreMesh = new THREE.Mesh(sharedCoreGeo, this.coreMat);
                this.coreMesh.scale.setScalar(this.radius);
                this.group.add(this.coreMesh);

                this.ringMat = new THREE.MeshBasicMaterial({ color: new THREE.Color(colorHex), transparent: true, opacity: 0.85 });
                this.ringMesh = new THREE.Mesh(sharedRingGeo, this.ringMat);
                this.ringMesh.scale.setScalar(this.radius);
                this.ringMesh.rotation.set(Math.random() * Math.PI, Math.random() * Math.PI, 0);
                this.group.add(this.ringMesh);

                this.trailNodes = [];
                this.trailPositions = [];
                this.trailMat = new THREE.MeshBasicMaterial({ color: new THREE.Color(colorHex), transparent: true, opacity: 0.42, depthWrite: false });
                for (let t = 0; t < 5; t++) {
                    const tMesh = new THREE.Mesh(sharedTrailGeo, this.trailMat);
                    arenaGroup.add(tMesh);
                    this.trailNodes.push(tMesh);
                }

                this.shadowMat = new THREE.MeshBasicMaterial({ color: new THREE.Color(colorHex), transparent: true, opacity: 0.42, depthWrite: false });
                this.shadowMesh = new THREE.Mesh(sharedShadowGeo, this.shadowMat);
                arenaGroup.add(this.shadowMesh);

                this.laserMat = new THREE.MeshBasicMaterial({ color: new THREE.Color(colorHex), transparent: true, opacity: 0.22, depthWrite: false });
                this.laserMesh = new THREE.Mesh(sharedLaserGeo, this.laserMat);
                arenaGroup.add(this.laserMesh);

                const maxX = ARENA.halfX - this.radius - 1.1;
                const maxY = ARENA.halfY - this.radius - 0.9;
                const maxZ = ARENA.halfZ - this.radius - 0.6;

                let pos = new THREE.Vector3();
                for (let attempt = 0; attempt < 30; attempt++) {
                    pos.set(
                        (Math.random() * 2 - 1) * maxX,
                        (Math.random() * 2 - 1) * maxY,
                        (Math.random() * 2 - 1) * maxZ
                    );
                    let overlap = false;
                    for (const other of existingSpheres) {
                        if (pos.distanceTo(other.group.position) < this.radius + other.radius + 0.6) {
                            overlap = true;
                            break;
                        }
                    }
                    if (!overlap) break;
                }
                this.group.position.copy(pos);
                for (let t = 0; t < 5; t++) {
                    this.trailPositions.push(pos.clone());
                    this.trailNodes[t].position.copy(pos);
                }

                const baseSpeed = isStarted ? 0.165 : 0.085;
                const dir = new THREE.Vector3(Math.random() - 0.5, Math.random() - 0.5, (Math.random() - 0.5) * 1.65).normalize();
                this.velocity = dir.multiplyScalar(baseSpeed * (0.85 + Math.random() * 0.45));

                this.spawnScale = 0.01;
                this.group.scale.setScalar(this.spawnScale);
                arenaGroup.add(this.group);
            }

            setColor(colorHex) {
                const c = new THREE.Color(colorHex);
                this.mat.color.copy(c);
                this.mat.emissive.copy(c);
                this.ringMat.color.copy(c);
                this.shadowMat.color.copy(c);
                this.laserMat.color.copy(c);
                this.trailMat.color.copy(c);
            }

            update() {
                if (this.spawnScale < 1) {
                    this.spawnScale = Math.min(1, this.spawnScale + 0.12);
                    this.group.scale.setScalar(this.spawnScale);
                }

                this.group.position.add(this.velocity);

                this.trailPositions.unshift(this.group.position.clone());
                if (this.trailPositions.length > 5) this.trailPositions.pop();
                for (let t = 0; t < this.trailNodes.length; t++) {
                    const p = this.trailPositions[t] || this.group.position;
                    this.trailNodes[t].position.copy(p);
                    const s = this.radius * (0.65 - t * 0.11) * this.spawnScale;
                    this.trailNodes[t].scale.setScalar(Math.max(0.05, s));
                }

                this.ringMesh.rotation.x += 0.035;
                this.ringMesh.rotation.y += 0.04;
                this.coreMesh.rotation.y -= 0.05;

                const zFactor = (this.group.position.z + ARENA.halfZ) / (ARENA.halfZ * 2);
                this.mat.emissiveIntensity = 0.25 + zFactor * 0.45;
                this.laserMat.opacity = 0.1 + zFactor * 0.22;

                const maxX = ARENA.halfX - this.radius - 0.5;
                const maxY = ARENA.halfY - this.radius - 0.4;
                const maxZ = ARENA.halfZ - this.radius - 0.3;
                const colHex = this.mat.color.getHex();

                if (this.group.position.x <= -maxX) {
                    this.group.position.x = -maxX;
                    this.velocity.x = Math.abs(this.velocity.x);
                    spawnWallRipple3D(-ARENA.halfX + 0.05, this.group.position.y, this.group.position.z, 'left', colHex);
                } else if (this.group.position.x >= maxX) {
                    this.group.position.x = maxX;
                    this.velocity.x = -Math.abs(this.velocity.x);
                    spawnWallRipple3D(ARENA.halfX - 0.05, this.group.position.y, this.group.position.z, 'right', colHex);
                }

                if (this.group.position.y <= -maxY) {
                    this.group.position.y = -maxY;
                    this.velocity.y = Math.abs(this.velocity.y);
                    spawnWallRipple3D(this.group.position.x, -ARENA.halfY + 0.05, this.group.position.z, 'bottom', colHex);
                } else if (this.group.position.y >= maxY) {
                    this.group.position.y = maxY;
                    this.velocity.y = -Math.abs(this.velocity.y);
                    spawnWallRipple3D(this.group.position.x, ARENA.halfY - 0.05, this.group.position.z, 'top', colHex);
                }

                if (this.group.position.z <= -maxZ) {
                    this.group.position.z = -maxZ;
                    this.velocity.z = Math.abs(this.velocity.z);
                    spawnWallRipple3D(this.group.position.x, this.group.position.y, -ARENA.halfZ + 0.05, 'back', colHex);
                } else if (this.group.position.z >= maxZ) {
                    this.group.position.z = maxZ;
                    this.velocity.z = -Math.abs(this.velocity.z);
                    spawnWallRipple3D(this.group.position.x, this.group.position.y, ARENA.halfZ - 0.05, 'front', colHex);
                }

                const heightRatio = (this.group.position.y + ARENA.halfY) / (ARENA.halfY * 2);
                const shadowScale = this.radius * (1.25 - heightRatio * 0.5);
                this.shadowMesh.position.set(this.group.position.x, -ARENA.halfY + 0.02, this.group.position.z);
                this.shadowMesh.scale.setScalar(Math.max(0.25, shadowScale));
                this.shadowMat.opacity = Math.max(0.14, 0.45 * (1 - heightRatio * 0.5));

                this.laserMesh.position.set(this.group.position.x, 0, this.group.position.z);
            }

            destroy() {
                arenaGroup.remove(this.group);
                arenaGroup.remove(this.shadowMesh);
                arenaGroup.remove(this.laserMesh);
                this.trailNodes.forEach(n => arenaGroup.remove(n));
                this.mat.dispose();
                this.specMat.dispose();
                this.coreMat.dispose();
                this.ringMat.dispose();
                this.shadowMat.dispose();
                this.laserMat.dispose();
                this.trailMat.dispose();
            }
        }

        function handleSphereCollisions() {
            const len = spheres3D.length;
            for (let i = 0; i < len; i++) {
                for (let j = i + 1; j < len; j++) {
                    const a = spheres3D[i];
                    const b = spheres3D[j];
                    const diff = new THREE.Vector3().subVectors(a.group.position, b.group.position);
                    const dist = diff.length();
                    const minDist = a.radius + b.radius;

                    if (dist < minDist && dist > 0.0001) {
                        const normal = diff.clone().divideScalar(dist);
                        const overlap = minDist - dist;
                        a.group.position.addScaledVector(normal, overlap * 0.5);
                        b.group.position.addScaledVector(normal, -overlap * 0.5);

                        const relVel = new THREE.Vector3().subVectors(a.velocity, b.velocity);
                        const sepVel = relVel.dot(normal);
                        if (sepVel < 0) {
                            const impulse = normal.multiplyScalar(sepVel);
                            a.velocity.sub(impulse);
                            b.velocity.add(impulse);
                        }
                    }
                }
            }
        }

        function spawnBurstParticles(position, colorHex) {
            for (let i = 0; i < 10; i++) {
                const mat = new THREE.MeshBasicMaterial({ color: colorHex, transparent: true, opacity: 1 });
                const mesh = new THREE.Mesh(sharedParticleGeo, mat);
                mesh.position.copy(position);
                const vel = new THREE.Vector3((Math.random() - 0.5) * 0.55, (Math.random() - 0.5) * 0.55, (Math.random() - 0.5) * 0.55);
                arenaGroup.add(mesh);
                particles3D.push({ mesh, mat, vel, life: 1.0 });
            }
        }

        function updateParticles() {
            for (let i = particles3D.length - 1; i >= 0; i--) {
                const p = particles3D[i];
                p.mesh.position.add(p.vel);
                p.mesh.rotation.x += 0.1;
                p.mesh.rotation.y += 0.1;
                p.life -= 0.045;
                p.mat.opacity = Math.max(0, p.life);
                p.mesh.scale.setScalar(Math.max(0.01, p.life));
                if (p.life <= 0) {
                    arenaGroup.remove(p.mesh);
                    p.mat.dispose();
                    particles3D.splice(i, 1);
                }
            }

            for (let i = flyingCoins3D.length - 1; i >= 0; i--) {
                const fc = flyingCoins3D[i];
                fc.mesh.position.add(fc.vel);
                fc.vel.y -= 0.018;
                fc.mesh.rotation.y += 0.28;
                fc.life -= 0.032;
                fc.mat.opacity = Math.max(0, fc.life);
                if (fc.life <= 0) {
                    arenaGroup.remove(fc.mesh);
                    fc.mat.dispose();
                    flyingCoins3D.splice(i, 1);
                }
            }

            for (let i = shockwaves3D.length - 1; i >= 0; i--) {
                const sw = shockwaves3D[i];
                sw.scale += 0.9;
                sw.opacity -= 0.04;
                sw.mesh.scale.setScalar(sw.scale);
                sw.mesh.position.z += 0.35;
                sw.mat.opacity = Math.max(0, sw.opacity);
                if (sw.opacity <= 0) {
                    arenaGroup.remove(sw.mesh);
                    sw.mat.dispose();
                    shockwaves3D.splice(i, 1);
                }
            }

            for (let i = wallRipples3D.length - 1; i >= 0; i--) {
                const wr = wallRipples3D[i];
                wr.scale += wr.growSpeed;
                wr.opacity -= 0.045;
                wr.mesh.scale.setScalar(wr.scale);
                wr.mat.opacity = Math.max(0, wr.opacity);
                if (wr.opacity <= 0) {
                    arenaGroup.remove(wr.mesh);
                    wr.mat.dispose();
                    wallRipples3D.splice(i, 1);
                }
            }
        }

        function spawnFlyingCoins3D(amount = 3) {
            if (!arenaGroup) return;
            for (let i = 0; i < amount; i++) {
                const mat = new THREE.MeshStandardMaterial({
                    color: isFeverMode ? 0xff00ff : 0x38bdf8,
                    emissive: isFeverMode ? 0x00ffff : 0x0284c7,
                    emissiveIntensity: 0.7,
                    roughness: 0.1,
                    metalness: 0.9,
                    transparent: true,
                    opacity: 1
                });
                const mesh = new THREE.Mesh(sharedCoreGeo, mat);
                mesh.scale.setScalar(1.4);
                mesh.position.set((Math.random() - 0.5) * 8, -2, (Math.random() - 0.5) * 4);
                const vel = new THREE.Vector3((Math.random() - 0.5) * 0.35, 0.38 + Math.random() * 0.22, (Math.random() - 0.5) * 0.25);
                arenaGroup.add(mesh);
                flyingCoins3D.push({ mesh, mat, vel, life: 1.0 });
            }
        }

        function showBonusBanner(subText, mainText) {
            const banner = document.getElementById('bonus-banner');
            const subEl = document.getElementById('bonus-sub');
            const mainEl = document.getElementById('bonus-main');
            if (!banner || !subEl || !mainEl) return;
            subEl.innerText = subText;
            mainEl.innerText = mainText;
            banner.classList.remove('active');
            void banner.offsetWidth;
            banner.classList.add('active');
            if (bonusPopupTimeout) clearTimeout(bonusPopupTimeout);
            bonusPopupTimeout = setTimeout(() => { banner.classList.remove('active'); }, 1300);
        }

        function launchConfetti(countPieces = 26) {
            const colors = ['#facc15', '#ef4444', '#3b82f6', '#22c55e', '#ec4899', '#a855f7', '#00f5d4'];
            for (let i = 0; i < countPieces; i++) {
                const el = document.createElement('div');
                el.className = 'confetti-piece';
                el.style.left = `${10 + Math.random() * 80}vw`;
                el.style.top = `-20px`;
                el.style.backgroundColor = colors[i % colors.length];
                el.style.borderRadius = i % 2 === 0 ? '50%' : '2px';
                el.style.animationDuration = `${1.2 + Math.random() * 1.1}s`;
                document.body.appendChild(el);
                setTimeout(() => el.remove(), 2400);
            }
        }

        function updateHypeComment(eventType, extraVal = 0) {
            const iconEl = document.getElementById('hype-icon');
            const textEl = document.getElementById('hype-text');
            if (!iconEl || !textEl) return;

            if (eventType === 'fever_start') {
                iconEl.innerText = '🌟';
                textEl.innerText = '無敵スターフィーバー突入！！塚田淳、5ターン獲得スコア3倍だ！！';
                return;
            }
            if (eventType === 'lucky_block') {
                iconEl.innerText = '💎';
                textEl.innerText = `ラッキークリスタルボーナス発動！！淳に +${extraVal} スコアの特別ボーナス！！`;
                return;
            }
            if (eventType === 'milestone') {
                iconEl.innerText = '🎉';
                textEl.innerText = `${combo}コンボ突破ボーナス！！淳、キレッキレじゃないか！！(+${extraVal})`;
                return;
            }
            if (eventType === 'wrong') {
                const missMsgs = [
                    'おっと塚田淳！3Dの奥行きに惑わされたか！？切り替えろ！',
                    'ドンマイ淳！！深呼吸してもう一度数え直せ！！',
                    '惜しい！！3D視点ドラッグで重なりを確認するんだ、淳！',
                    '痛恨のミス！だが塚田淳の伝説はここでは終わらない！！'
                ];
                iconEl.innerText = '💥';
                textEl.innerText = missMsgs[Math.floor(Math.random() * missMsgs.length)];
                return;
            }

            if (isFeverMode) {
                iconEl.innerText = '🌈';
                textEl.innerText = `STAR FEVER中（残り${feverTurnsLeft}回）！今の淳は無敵だ！スコア3倍獲得！！`;
            } else if (combo >= 50) {
                iconEl.innerText = '👑';
                textEl.innerText = `${combo}連撃！！神の領域に到達した男・塚田淳！！誰も止められない！`;
            } else if (combo >= 25) {
                iconEl.innerText = '🔥';
                textEl.innerText = `${combo}コンボ！！完全にゾーンに入った塚田淳！動体視力がバケモノ級だ！`;
            } else if (combo >= 10) {
                iconEl.innerText = '⚡';
                textEl.innerText = `${combo}コンボ乗ってきた！！塚田淳の集中力が研ぎ澄まされているぞ！`;
            } else if (combo >= 3) {
                iconEl.innerText = '✨';
                textEl.innerText = `いいリズムだ塚田淳！現在${combo}コンボ！スターゲージを貯めろ！`;
            } else {
                const normalMsgs = [
                    'ナイス正解、淳！あと少しでスターフィーバー突入だ！',
                    '冷静なカウントだ塚田淳！このペースで5,000を目指せ！',
                    'バッチリ見えてるぞ淳！連続正解で空間ステージを進化させろ！'
                ];
                iconEl.innerText = '🎙️';
                textEl.innerText = normalMsgs[score % normalMsgs.length];
            }
        }

        let leftTap = 0, lastTap = 0;
        window.handleAdminTap = (side) => {
            const now = Date.now();
            if (now - lastTap > 2000) leftTap = 0;
            lastTap = now;
            if (side === 'left') leftTap++;
            else if (side === 'right' && leftTap >= 3) {
                leftTap = 0;
                document.getElementById('admin-confirm-modal').style.display = 'flex';
            }
        };

        window.cancelAdminClear = () => { document.getElementById('admin-confirm-modal').style.display = 'none'; };
        window.executeAdminClear = async () => {
            document.getElementById('admin-confirm-modal').style.display = 'none';
            if (db && currentUser) {
                const logCol = collection(db, 'artifacts', appId, 'public', 'data', 'logs');
                const snap = await getDocs(logCol);
                for (const d of snap.docs) { await deleteDoc(doc(db, 'artifacts', appId, 'public', 'data', 'logs', d.id)); }
            }
        };

        function getRewardColorClass(reward) {
            if (reward === 0) return { class: 'text-slate-500', shadow: 'none' };
            if (reward === 1500) return { class: 'text-sky-400', shadow: 'drop-shadow-[0_0_8px_rgba(56,189,248,0.6)]' }; 
            if (reward === 2500) return { class: 'text-blue-500', shadow: 'drop-shadow-[0_0_10px_rgba(59,130,246,0.6)]' }; 
            if (reward === 3000) return { class: 'text-indigo-500 font-black', shadow: 'drop-shadow-[0_0_12px_rgba(99,102,241,0.6)]' }; 
            if (reward === 3500) return { class: 'text-purple-500 font-black', shadow: 'drop-shadow-[0_0_12px_rgba(168,85,247,0.6)]' }; 
            if (reward === 5000) return { class: 'text-fuchsia-500 font-black', shadow: 'drop-shadow-[0_0_15px_rgba(217,70,239,0.7)]' }; 
            if (reward === 6000) return { class: 'text-orange-500 font-black', shadow: 'drop-shadow-[0_0_15px_rgba(249,115,22,0.8)]' }; 
            if (reward === 7000) return { class: 'text-red-500 font-black animate-pulse', shadow: 'drop-shadow-[0_0_20px_rgba(239,68,68,0.8)]' }; 
            if (reward === 10000) return { class: 'text-rose-600 font-black animate-pulse', shadow: 'drop-shadow-[0_0_20px_rgba(225,29,72,0.9)]' }; 
            if (reward === 14000) return { class: 'text-rainbow font-black', shadow: 'shadow-rainbow' }; 
            return { class: 'text-[var(--text-main)]', shadow: 'none' };
        }

        let audioCtx;
        function initAudio() { 
            if (!audioCtx) audioCtx = new (window.AudioContext || window.webkitAudioContext)(); 
            if (audioCtx.state === 'suspended') audioCtx.resume();
        }

        function playSound(type) {
            if (!audioCtx) return;
            const now = audioCtx.currentTime, osc = audioCtx.createOscillator(), gain = audioCtx.createGain();
            osc.connect(gain); gain.connect(audioCtx.destination);
            
            if (type === 'correct') {
                osc.type = 'sine'; osc.frequency.setValueAtTime(880, now);
                osc.frequency.exponentialRampToValueAtTime(1320, now + 0.1);
                gain.gain.setValueAtTime(0.1, now); gain.gain.exponentialRampToValueAtTime(0.01, now + 0.1);
                osc.start(now); osc.stop(now + 0.1);
            } else if (type === 'combo') {
                osc.type = 'sawtooth'; osc.frequency.setValueAtTime(220, now);
                osc.frequency.exponentialRampToValueAtTime(880, now + 0.05);
                gain.gain.setValueAtTime(0.15, now); gain.gain.exponentialRampToValueAtTime(0.01, now + 0.4);
                osc.start(now); osc.stop(now + 0.4);
            } else if (type === 'wrong') {
                const playB = (t) => {
                    const o = audioCtx.createOscillator(), g = audioCtx.createGain(); o.connect(g); g.connect(audioCtx.destination); o.type = 'square';
                    o.frequency.setValueAtTime(120, t); g.gain.setValueAtTime(0.1, t);
                    g.gain.linearRampToValueAtTime(0, t + 0.1); o.start(t); o.stop(t + 0.1);
                };
                playB(now); playB(now + 0.15); 
            } else if (type === 'tick') {
                osc.type = 'triangle';
                osc.frequency.setValueAtTime(800, now);
                osc.frequency.exponentialRampToValueAtTime(1000, now + 0.05);
                gain.gain.setValueAtTime(0.05, now);
                gain.gain.exponentialRampToValueAtTime(0.01, now + 0.05);
                osc.start(now); osc.stop(now + 0.05);
            } else if (type === 'bounce') {
                osc.type = 'sine';
                osc.frequency.setValueAtTime(190, now);
                osc.frequency.exponentialRampToValueAtTime(65, now + 0.08);
                gain.gain.setValueAtTime(0.055, now);
                gain.gain.exponentialRampToValueAtTime(0.005, now + 0.08);
                osc.start(now); osc.stop(now + 0.08);
            } else if (type === 'treasure') {
                osc.type = 'sine';
                osc.frequency.setValueAtTime(440, now);
                osc.frequency.exponentialRampToValueAtTime(880, now + 0.2);
                osc.frequency.exponentialRampToValueAtTime(1760, now + 0.4);
                gain.gain.setValueAtTime(0.1, now);
                gain.gain.linearRampToValueAtTime(0, now + 0.6);
                osc.start(now); osc.stop(now + 0.6);
            } else if (type === 'fever') {
                const notes = [523.25, 659.25, 783.99, 1046.5, 1318.5, 1567.98];
                notes.forEach((freq, idx) => {
                    const o = audioCtx.createOscillator(), g = audioCtx.createGain();
                    o.type = 'sawtooth';
                    o.connect(g); g.connect(audioCtx.destination);
                    const st = now + idx * 0.055;
                    o.frequency.setValueAtTime(freq, st);
                    g.gain.setValueAtTime(0.08, st);
                    g.gain.exponentialRampToValueAtTime(0.008, st + 0.12);
                    o.start(st); o.stop(st + 0.12);
                });
            }
        }

        const BGM_NORMAL_NOTES = [261.63, 329.63, 392.00, 523.25, 392.00, 329.63, 293.66, 392.00];
        const BGM_FEVER_NOTES = [523.25, 659.25, 783.99, 1046.5, 987.77, 783.99, 659.25, 880.00];

        function startBgmLoop() {
            if (bgmInterval) clearInterval(bgmInterval);
            bgmInterval = setInterval(() => {
                if (!isBgmEnabled || !isStarted || !audioCtx) return;
                const now = audioCtx.currentTime;
                const o = audioCtx.createOscillator();
                const g = audioCtx.createGain();
                o.type = isFeverMode ? 'sawtooth' : 'square';
                const notes = isFeverMode ? BGM_FEVER_NOTES : BGM_NORMAL_NOTES;
                const freq = notes[bgmStep % notes.length];
                bgmStep++;
                o.frequency.setValueAtTime(freq, now);
                g.gain.setValueAtTime(isFeverMode ? 0.035 : 0.022, now);
                g.gain.exponentialRampToValueAtTime(0.002, now + 0.14);
                o.connect(g); g.connect(audioCtx.destination);
                o.start(now); o.stop(now + 0.15);
            }, 180);
        }

        window.toggleBGM = () => {
            initAudio();
            isBgmEnabled = !isBgmEnabled;
            const btn = document.getElementById('bgm-btn');
            if (btn) btn.innerHTML = `<span>🎵 BGM: ${isBgmEnabled ? 'ON' : 'OFF'}</span>`;
            if (isBgmEnabled) startBgmLoop();
            else if (bgmInterval) { clearInterval(bgmInterval); bgmInterval = null; }
        };

        window.setTheme = (themeName) => {
            currentTheme = themeName;
            document.documentElement.setAttribute('data-theme', themeName);
            
            const themesInfo = {
                standard: { shadow: 'rgba(59,130,246,0.6)', border: 'white' },
                cyber: { shadow: 'rgba(217,70,239,0.6)', border: 'white' },
                magma: { shadow: 'rgba(239,68,68,0.6)', border: 'white' },
                light: { shadow: 'rgba(14,165,233,0.5)', border: '#0f172a' }
            };
            
            document.querySelectorAll('.theme-btn').forEach(btn => {
                const tName = btn.id.replace('theme-', '');
                if (tName === themeName) {
                    btn.classList.remove('border-transparent', 'scale-100', 'opacity-50');
                    btn.classList.add('scale-110', 'opacity-100');
                    btn.style.borderColor = themesInfo[tName].border;
                    btn.style.boxShadow = `0 0 15px ${themesInfo[tName].shadow}`;
                } else {
                    btn.classList.add('border-transparent', 'scale-100', 'opacity-50');
                    btn.classList.remove('scale-110', 'opacity-100');
                    btn.style.borderColor = 'transparent';
                    btn.style.boxShadow = 'none';
                }
            });

            resetComboEffect();
            updateThreeTheme();
        };

        function generateNumpad() {
            const pad = document.getElementById('numpad');
            if (!pad) return;
            if (pad.children.length === 10) {
                document.querySelectorAll('.num-btn').forEach(btn => {
                    btn.style.backgroundColor = currentComboColor;
                    btn.style.boxShadow = `0 4px 0 ${currentShadowColor}, 0 6px 12px rgba(0,0,0,0.4), inset 0 2px 3px rgba(255,255,255,0.5)`;
                });
                return;
            }
            pad.innerHTML = '';
            const nums = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10];
            for (let i = 0; i < nums.length; i++) {
                const num = nums[i];
                const btn = document.createElement('div');
                btn.className = 'num-btn';
                btn.innerText = num;
                btn.style.backgroundColor = currentComboColor;
                btn.style.boxShadow = `0 4px 0 ${currentShadowColor}, 0 6px 12px rgba(0,0,0,0.4), inset 0 2px 3px rgba(255,255,255,0.5)`;
                btn.onclick = () => window.check(num);
                pad.appendChild(btn);
            }
        }

        function realStartGame(isResume) {
            document.getElementById('load-confirm-modal').style.display = 'none';
            initAudio();
            if (!scene || !renderer || !arenaGroup) {
                initThreeJS();
            }
            isStarted = true;
            document.getElementById('start-screen').classList.remove('active');
            document.getElementById('game-screen').classList.add('active');
            
            generateNumpad();
            attachRendererToContainer('canvas');
            window.resetCameraView();

            if (isResume && savedData) {
                score = Math.min(TARGET - 1, savedData.score || 0);
                combo = savedData.combo || 0;
                maxCombo = savedData.maxCombo || 0;
                feverCharge = savedData.feverCharge || 0;
                isFeverMode = !!savedData.isFeverMode;
                feverTurnsLeft = savedData.feverTurnsLeft || 0;
                updateFeverUI();
                updateUI();
                updateComboEffect();
                checkStageProgression(false);
            } else {
                resetGameData();
            }
            
            initCircles(false);
        }

        window.startGame = () => {
            if (savedData && savedData.score > 0) {
                document.getElementById('load-score-text').innerText = savedData.score.toLocaleString();
                document.getElementById('load-confirm-modal').style.display = 'flex';
            } else {
                realStartGame(false);
            }
        };

        window.startNewGame = () => { clearSaveData(); realStartGame(false); };
        window.resumeGame = () => { realStartGame(true); };

        let unsubscribeLogs = null;
        window.showLog = async () => {
            document.getElementById('start-screen').classList.remove('active');
            document.getElementById('log-screen').classList.add('active');
            
            const listEl = document.getElementById('log-list');
            if (!db) { listEl.innerHTML = '<p class="text-[var(--text-muted)] text-center py-8">接続エラー</p>'; return; }

            if (!currentUser && auth) {
                try {
                    const userCred = await signInAnonymously(auth);
                    currentUser = userCred.user;
                } catch(e) {
                    listEl.innerHTML = '<p class="text-[var(--text-muted)] text-center py-8">認証エラー</p>';
                    return;
                }
            }

            if (unsubscribeLogs) unsubscribeLogs();
            const logCol = collection(db, 'artifacts', appId, 'public', 'data', 'logs');
            unsubscribeLogs = onSnapshot(logCol, (snap) => {
                const logs = snap.docs.map(d => d.data()).sort((a, b) => {
                    const timeA = a.localTime || (a.timestamp?.seconds * 1000) || 0;
                    const timeB = b.localTime || (b.timestamp?.seconds * 1000) || 0;
                    return timeB - timeA;
                });

                if (logs.length === 0) { listEl.innerHTML = '<p class="text-[var(--text-muted)] text-center py-8">記録なし</p>'; return; }
                
                listEl.innerHTML = logs.map(l => {
                    const styleInfo = getRewardColorClass(l.reward || 0);
                    const formattedReward = `${(l.reward || 0).toLocaleString()}円`;
                    return `
                    <div class="log-item">
                        <div>
                            <div class="text-[10px] text-[var(--text-muted)]">${l.date} ${l.time}</div>
                            <div class="text-xs text-[var(--accent)] font-black drop-shadow-sm">CLEARED</div>
                        </div>
                        <div class="text-2xl font-black italic ${styleInfo.class} ${styleInfo.shadow}">${formattedReward}</div>
                    </div>`;
                }).join('');
            }, (err) => {
                console.error("Log fetch error:", err);
                listEl.innerHTML = '<p class="text-[var(--text-muted)] text-center py-8">読み込みエラー</p>';
            });
        };

        window.backToStartFromLog = () => {
            document.getElementById('log-screen').classList.remove('active');
            document.getElementById('start-screen').classList.add('active');
            if (unsubscribeLogs) unsubscribeLogs();
            attachRendererToContainer('start-bg');
        };

        window.backToStart = () => {
            isStarted = false;
            document.getElementById('clear-screen').classList.remove('active');
            document.getElementById('start-screen').classList.add('active');
            
            document.getElementById('double-up-actions').classList.remove('hidden');
            document.getElementById('double-up-presentation').classList.add('hidden');
            document.getElementById('double-up-roulette').classList.add('hidden');
            document.getElementById('double-up-result').classList.add('hidden');
            document.getElementById('double-up-result').classList.add('opacity-0');
            
            attachRendererToContainer('start-bg');
            initStartBg();
        };

        function initCircles(withBurst = false) {
            generateNumpad();
            if (!arenaGroup) return;

            spheres3D.forEach(s => {
                if (withBurst) spawnBurstParticles(s.group.position, s.mat.color.getHex());
                s.destroy();
            });
            spheres3D = [];

            count = Math.floor(Math.random() * 10) + 1;
            const colors = THEMES[currentTheme].colors;
            for (let i = 0; i < count; i++) {
                const col = colors[Math.floor(Math.random() * colors.length)];
                spheres3D.push(new Sphere3D(col, spheres3D));
            }
        }

        function initStartBg() {
            if (!arenaGroup) return;
            spheres3D.forEach(s => s.destroy());
            spheres3D = [];
            const colors = THEMES[currentTheme].colors;
            for (let i = 0; i < 8; i++) {
                const col = colors[i % colors.length];
                spheres3D.push(new Sphere3D(col, spheres3D));
            }
        }

        function animateLoop() {
            anim = requestAnimationFrame(animateLoop);
            if (!scene || !renderer || !camera) return;

            spheres3D.forEach(s => s.update());
            handleSphereCollisions();
            updateParticles();
            updateImmersiveTunnelAndDust();
            updateStageEnvironment();
            updateCameraPosition();

            if (arenaShakeIntensity > 0.01) {
                arenaGroup.position.set(
                    (Math.random() - 0.5) * arenaShakeIntensity,
                    (Math.random() - 0.5) * arenaShakeIntensity,
                    (Math.random() - 0.5) * arenaShakeIntensity
                );
                arenaShakeIntensity *= 0.85;
            } else {
                arenaGroup.position.set(0, 0, 0);
            }

            renderer.render(scene, camera);
        }

        function updateFeverUI() {
            const bar = document.getElementById('fever-bar');
            const statusText = document.getElementById('fever-status-text');
            const badge = document.getElementById('fever-badge');
            const canvasContainer = document.getElementById('canvas');
            if (!bar || !statusText) return;

            if (isFeverMode) {
                const pct = (feverTurnsLeft / 5) * 100;
                bar.style.width = `${pct}%`;
                bar.className = 'h-full fever-bar-bg transition-all duration-200';
                statusText.innerText = `FEVER残り${feverTurnsLeft}`;
                if (badge) badge.classList.remove('hidden');
                if (canvasContainer) canvasContainer.classList.add('fever-screen-glow');
            } else {
                const pct = (feverCharge / FEVER_MAX_CHARGE) * 100;
                bar.style.width = `${pct}%`;
                bar.className = 'h-full bg-yellow-400 transition-all duration-200';
                statusText.innerText = `${feverCharge}/${FEVER_MAX_CHARGE}`;
                if (badge) badge.classList.add('hidden');
                if (canvasContainer) canvasContainer.classList.remove('fever-screen-glow');
            }
        }

        window.check = (num) => {
            if (!isStarted) return; 
            initAudio(); 
            
            if (num === count) {
                combo++; if (combo > maxCombo) maxCombo = combo;

                let gainedScore = isFeverMode ? 3 : 1;
                let eventType = 'correct';
                let bonusVal = 0;

                if (isFeverMode) {
                    feverTurnsLeft--;
                    showBonusBanner('🌟 STAR FEVER x3!!', `💎 +${gainedScore} SCORE!!`);
                    if (feverTurnsLeft <= 0) {
                        isFeverMode = false;
                        feverCharge = 0;
                    }
                } else {
                    feverCharge++;
                    if (feverCharge >= FEVER_MAX_CHARGE) {
                        isFeverMode = true;
                        feverTurnsLeft = 5;
                        feverCharge = 0;
                        eventType = 'fever_start';
                        playSound('fever');
                        launchConfetti(28);
                        showBonusBanner('🌈 HYPER STAR FEVER!!', '🌟 無敵フィーバー突入 (スコア3倍)!!');
                    }
                }

                if (combo > 0 && combo % 10 === 0) {
                    const milestoneBonus = 5;
                    gainedScore += milestoneBonus;
                    bonusVal = milestoneBonus;
                    eventType = 'milestone';
                    playSound('combo');
                    launchConfetti(22);
                    showBonusBanner(`🎉 ${combo} COMBO BREAK BONUS!!`, `👑 +${gainedScore} SCORE!!`);
                } else if (!isFeverMode && eventType !== 'fever_start' && Math.random() < 0.15) {
                    const luckyBonus = 3;
                    gainedScore += luckyBonus;
                    bonusVal = luckyBonus;
                    eventType = 'lucky_block';
                    playSound('combo');
                    showBonusBanner('💎 LUCKY CRYSTAL BONUS!!', `✨ +${gainedScore} SCORE!!`);
                } else if (eventType === 'correct') {
                    playSound('correct');
                }

                score = Math.min(TARGET, score + gainedScore);
                spawnFlyingCoins3D(isFeverMode ? 6 : 2);
                spawnShockwave3D(isFeverMode ? 0xfacc15 : 0x38bdf8);
                updateFeverUI();
                updateHypeComment(eventType, bonusVal);
                updateComboEffect();
                showFeedback(gainedScore > 1 ? `+${gainedScore}` : '○', isFeverMode ? 'text-yellow-400' : 'text-[var(--accent)]');
                updateUI();
                saveProgress();

                if (score >= TARGET) { 
                    isStarted = false; 
                    clearSaveData();
                    launchConfetti(50);
                    initCircles(true);
                    setTimeout(prepareTreasure, 500); 
                    return; 
                }
                initCircles(true);
            } else {
                playSound('wrong');
                score = Math.max(0, score - 1); combo = 0;
                isFeverMode = false;
                feverCharge = 0;
                feverTurnsLeft = 0;
                updateFeverUI();
                updateHypeComment('wrong');
                resetComboEffect();
                saveProgress();
                if (navigator.vibrate) navigator.vibrate([100, 50, 100]); 
                showFeedback('×', 'text-rose-500');
                updateUI();

                arenaShakeIntensity = 1.6;
                document.body.classList.remove('shake');
                void document.body.offsetWidth;
                document.body.classList.add('shake');
                initCircles(false);
            }
        };

        function updateComboEffect() {
            checkStageProgression(false);
            if (combo >= 2) {
                const countEl = document.getElementById('combo-count');
                const labelEl = document.getElementById('combo-label');
                countEl.innerText = combo; labelEl.classList.remove('hidden');
                
                const theme = THEMES[currentTheme];
                let nc = theme.baseCombo.c, ns = theme.baseCombo.s;
                
                for (let stage of theme.comboStages) {
                    if (combo > stage.limit) {
                        nc = stage.c; ns = stage.s;
                        break;
                    }
                }

                if (nc !== currentComboColor) {
                    playSound('combo'); if (navigator.vibrate) navigator.vibrate(300);
                    currentComboColor = nc; currentShadowColor = ns;
                    document.querySelectorAll('.num-btn').forEach(btn => {
                        btn.style.backgroundColor = nc;
                        btn.style.boxShadow = `0 5px 0 ${ns}, 0 8px 15px rgba(0,0,0,0.3), inset 0 2px 2px rgba(255,255,255,0.4)`;
                    });
                    if (pointLight1) pointLight1.color.set(nc);
                    if (cageLine) cageLine.material.color.set(nc);
                }
                countEl.style.color = nc; labelEl.style.color = nc;
                countEl.classList.remove('pop'); void countEl.offsetWidth; countEl.classList.add('pop');
                document.getElementById('flash').classList.remove('animate-flash');
                void document.getElementById('flash').offsetWidth; document.getElementById('flash').classList.add('animate-flash');
            }
        }

        function resetComboEffect() {
            const countEl = document.getElementById('combo-count');
            const labelEl = document.getElementById('combo-label');
            countEl.classList.remove('pop'); labelEl.classList.add('hidden');
            
            const theme = THEMES[currentTheme];
            currentComboColor = theme.baseCombo.c; 
            currentShadowColor = theme.baseCombo.s;
            
            document.querySelectorAll('.num-btn').forEach(btn => {
                btn.style.backgroundColor = currentComboColor; 
                btn.style.boxShadow = `0 5px 0 ${currentShadowColor}, 0 8px 15px rgba(0,0,0,0.3), inset 0 2px 2px rgba(255,255,255,0.4)`;
            });

            checkStageProgression(false);
            if (currentStageKey === 'base') {
                if (pointLight1) pointLight1.color.setHex(theme.cageColor);
                if (cageLine) cageLine.material.color.setHex(theme.cageColor);
            }
        }

        function showFeedback(text, colorClass) {
            const f = document.getElementById('feedback');
            f.innerText = text; f.className = `feedback-pop ${colorClass} animate-feedback`;
            setTimeout(() => f.classList.remove('animate-feedback'), 420);
        }

        function updateUI() {
            document.getElementById('score-text').innerText = score.toLocaleString();
            document.getElementById('best-combo').innerText = maxCombo;
            const p = (score / TARGET) * 100;
            document.getElementById('progress-bar').style.width = `${Math.min(p, 100)}%`;
            document.getElementById('percent').innerText = `達成率 ${Math.floor(Math.min(p, 100))}%`;
            document.getElementById('remaining').innerText = `残り ${Math.max(0, TARGET - score).toLocaleString()}`;
        }

        function updateRewardDisplay(elId, amount) {
            const el = document.getElementById(elId);
            const styleInfo = getRewardColorClass(amount);
            el.innerText = `${amount.toLocaleString()}円`;
            el.className = `text-3xl transition-all duration-500 ${styleInfo.class} ${styleInfo.shadow}`;
        }

        function prepareTreasure() {
            isStarted = false; isChestOpened = false;
            document.getElementById('game-screen').classList.remove('active');
            
            const chests = document.querySelectorAll('.chest');
            const labels = ['宝箱 A', '宝箱 B', '宝箱 C'];
            chests.forEach((chest, index) => {
                chest.classList.remove('opened', 'dimmed');
                const label = chest.querySelector('.chest-label');
                if (label) label.innerText = labels[index];
            });
            document.getElementById('treasure-msg').innerText = "運命の報酬を選べ、塚田淳！";
            document.getElementById('treasure-screen').classList.add('active');
            
            let array = [1500, 2500, 3500];
            for (let i = array.length - 1; i > 0; i--) {
                const j = Math.floor(Math.random() * (i + 1));
                [array[i], array[j]] = [array[j], array[i]];
            }
            chestValues = array;
        }

        window.openChest = (index) => {
            if (isChestOpened) return;
            isChestOpened = true; 
            playSound('treasure');
            currentReward = chestValues[index];
            
            const chests = document.querySelectorAll('.chest');
            chests.forEach((chest, i) => {
                if (i === index) {
                    chest.classList.add('opened');
                    const label = chest.querySelector('.chest-label');
                    if(label) label.innerText = `${currentReward}円`;
                } else {
                    chest.classList.add('dimmed');
                }
            });

            document.getElementById('treasure-msg').innerText = "運命の扉が開かれた...！";

            setTimeout(() => {
                doubleUpCount = 0;
                isDoubleUpRunning = false;
                document.getElementById('double-up-title').innerHTML = `
                    <span class="text-3xl sm:text-4xl w-full">ダブルアップチャレンジ</span>
                    <span class="text-xl sm:text-2xl text-rose-400 drop-shadow-sm">(1回目)</span>
                `;
                updateRewardDisplay('current-reward-text', currentReward);
                
                document.getElementById('double-up-actions').classList.remove('hidden');
                document.getElementById('double-up-presentation').classList.add('hidden');
                document.getElementById('double-up-roulette').classList.add('hidden');
                document.getElementById('double-up-roulette').classList.remove('flex');
                document.getElementById('double-up-result').classList.add('hidden');
                document.getElementById('double-up-result').classList.remove('flex');
                document.getElementById('double-up-result').classList.add('opacity-0');
                
                const statusText = document.getElementById('roulette-status-text');
                if(statusText) {
                    statusText.innerText = "抽選中...";
                    statusText.className = "text-[var(--text-muted)] font-bold mb-4 animate-pulse text-lg";
                }
                
                document.getElementById('treasure-screen').classList.remove('active');
                document.getElementById('double-up-screen').classList.add('active');
            }, 1500);
        };

        window.playDoubleUp = () => {
            if (isDoubleUpRunning) return;
            isDoubleUpRunning = true;
            playSound('tick');
            document.getElementById('double-up-actions').classList.add('hidden');
            document.getElementById('double-up-presentation').classList.remove('hidden');
            document.getElementById('double-up-roulette').classList.remove('hidden');
            document.getElementById('double-up-roulette').classList.add('flex');
            
            const rBox = document.getElementById('roulette-box');
            const rText = document.getElementById('roulette-text');
            const statusText = document.getElementById('roulette-status-text');
            
            statusText.innerText = "抽選中...";
            statusText.className = "text-[var(--text-muted)] font-bold mb-4 animate-pulse text-lg";
            
            const duration = 8000; 
            const startTime = Date.now();
            let ticks = 0;
            const isWin = Math.random() < 0.5;

            function runRoulette() {
                const elapsed = Date.now() - startTime;
                ticks++;
                playSound('tick');

                if (ticks % 2 === 0) {
                    rBox.className = "w-full bg-rose-50 border-4 border-rose-400 rounded-2xl p-8 text-center shadow-[0_4px_15px_rgba(244,63,94,0.3)]";
                    rText.innerText = "成功！？";
                    rText.className = "text-5xl font-black text-rose-500 drop-shadow-md";
                } else {
                    rBox.className = "w-full bg-blue-50 border-4 border-blue-400 rounded-2xl p-8 text-center shadow-[0_4px_15px_rgba(59,130,246,0.3)]";
                    rText.innerText = "失敗！？";
                    rText.className = "text-5xl font-black text-blue-500 drop-shadow-md";
                }

                if (elapsed > 2000 && elapsed <= 4000) {
                    statusText.innerText = "まだまだ！";
                    statusText.className = "text-amber-500 font-bold mb-4 animate-pulse text-xl drop-shadow-sm";
                } else if (elapsed > 4000 && elapsed <= 5500) {
                    statusText.innerText = "どっちだ！？";
                    statusText.className = "text-orange-500 font-black mb-4 animate-bounce text-2xl drop-shadow-sm";
                } else if (elapsed > 5500 && elapsed <= 7000) {
                    statusText.innerText = "決まるぞ！ジュン！";
                    statusText.className = "text-rose-500 font-black mb-4 animate-ping text-3xl drop-shadow-sm";
                } else if (elapsed > 7000) {
                    statusText.innerText = "どっちだ！？淳！！！！";
                    statusText.className = "text-red-600 font-black mb-4 animate-ping text-4xl drop-shadow-lg";
                }

                if (elapsed >= duration) {
                    showDoubleUpResult(isWin);
                } else {
                    const progress = elapsed / duration;
                    const delay = 50 + (progress * progress) * 350; 
                    setTimeout(runRoulette, delay);
                }
            }
            setTimeout(runRoulette, 50);
        };

        function showDoubleUpResult(isWin) {
            const resEl = document.getElementById('double-up-result');
            const resText = document.getElementById('double-up-result-text');
            
            document.getElementById('double-up-roulette').classList.add('hidden');
            document.getElementById('double-up-roulette').classList.remove('flex');
            resEl.classList.remove('hidden');
            resEl.classList.add('flex');
            
            setTimeout(() => resEl.classList.remove('opacity-0'), 50);
            
            if (isWin) {
                playSound('correct');
                launchConfetti(40);
                currentReward *= 2;
                doubleUpCount++;
                
                resText.innerText = "成功！！";
                resText.className = "text-6xl sm:text-7xl font-black text-yellow-500 drop-shadow-[0_0_20px_rgba(250,204,21,0.8)] transform scale-110 transition-transform duration-500";
                
                setTimeout(() => {
                    updateRewardDisplay('current-reward-text', currentReward);
                    setTimeout(() => {
                        resEl.classList.add('opacity-0');
                        setTimeout(() => {
                            if (doubleUpCount < 2) {
                                isDoubleUpRunning = false;
                                document.getElementById('double-up-title').innerHTML = `
                                    <span class="text-3xl sm:text-4xl w-full">ダブルアップチャレンジ</span>
                                    <span class="text-xl sm:text-2xl text-rose-400 drop-shadow-sm">(2回目)</span>
                                `;
                                document.getElementById('double-up-actions').classList.remove('hidden');
                                resEl.classList.add('hidden');
                                resEl.classList.remove('flex');
                                document.getElementById('double-up-presentation').classList.add('hidden');
                            } else {
                                finishGame();
                            }
                        }, 300);
                    }, 1200); 
                }, 1000); 
            } else {
                playSound('wrong');
                currentReward = 0;
                resText.innerText = "失敗...";
                resText.className = "text-6xl sm:text-7xl font-black text-[var(--text-muted)] drop-shadow-md transform scale-100 transition-transform duration-500";
                
                document.body.classList.remove('shake');
                void document.body.offsetWidth;
                document.body.classList.add('shake');

                setTimeout(() => {
                    updateRewardDisplay('current-reward-text', currentReward);
                    setTimeout(() => {
                        resEl.classList.add('opacity-0');
                        setTimeout(() => finishGame(), 300);
                    }, 1200); 
                }, 1000); 
            }
        }

        window.declineDoubleUp = () => finishGame();

        function finishGame() {
            const bonusTextEl = document.getElementById('bonus-value-text');
            const styleInfo = getRewardColorClass(currentReward);
            
            bonusTextEl.innerText = `${currentReward.toLocaleString()}円`;
            bonusTextEl.className = `text-6xl italic tracking-tighter mb-4 transition-all duration-1000 ${styleInfo.class} ${styleInfo.shadow}`;

            const now = new Date();
            const dateStr = now.getFullYear() + '/' + ('0' + (now.getMonth() + 1)).slice(-2) + '/' + ('0' + now.getDate()).slice(-2);
            const timeStr = ('0' + now.getHours()).slice(-2) + ':' + ('0' + now.getMinutes()).slice(-2) + ':' + ('0' + now.getSeconds()).slice(-2);
            document.getElementById('timestamp').innerText = `${dateStr} ${timeStr}`;
            
            document.getElementById('double-up-screen').classList.remove('active');
            document.getElementById('clear-screen').classList.add('active');

            if (db && currentUser) {
                const logCol = collection(db, 'artifacts', appId, 'public', 'data', 'logs');
                addDoc(logCol, { 
                    date: dateStr, time: timeStr, reward: currentReward,
                    timestamp: serverTimestamp(), localTime: Date.now()
                }).catch(e => console.error(e));
            }
        }

        function resetGameData() {
            score = 0; combo = 0; maxCombo = 0;
            feverCharge = 0; isFeverMode = false; feverTurnsLeft = 0;
            updateFeverUI();
            updateUI();
            resetComboEffect();
        }

        window.addEventListener('resize', handleResize);

        window.onload = function () {
            generateNumpad();
            initThreeJS();
            initStartBg();
            animateLoop();
        };
    </script>
</body>
</html>
