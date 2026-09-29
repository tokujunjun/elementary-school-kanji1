# elementary-school-kanji1
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>しょうがく1ねんせいの かん字れんしゅう</title>
    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Google Fonts & Font Awesome -->
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Klee+One:wght@400;600;700&family=M+PLUS+Rounded+1c:wght@400;700;800;900&display=swap" rel="stylesheet">
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">

    <style>
        body {
            font-family: 'M PLUS Rounded 1c', sans-serif;
            background-color: #f0fdf4;
            user-select: none;
            -webkit-user-select: none;
            touch-action: manipulation;
        }

        /* 教科書体（学校で習う漢字フォント）設定 */
        .font-kyokasho {
            font-family: "UD Digital Kyokasho N-R", "UD デジタル 教科書体 N-R", "YuKyokasho", "游教科書体", "HGKyokashotai", "HG教科書体", "Klee One", serif;
        }

        /* 漢字用マス目デザイン */
        .kanji-grid-bg {
            background-color: #ffffff;
            background-image: 
                linear-gradient(to right, #fecdd3 2px, transparent 2px),
                linear-gradient(to bottom, #fecdd3 2px, transparent 2px);
            background-size: 50% 50%;
            background-position: center center;
            border: 4px solid #f43f5e;
            box-shadow: inset 0 0 0 2px #ffe4e6;
        }

        /* タッチスクロール防止用キャンバス設定 */
        canvas {
            touch-action: none;
        }

        /* ポップなボタンデザイン */
        .btn-pop {
            transition: all 0.15s ease;
            box-shadow: 0 6px 0 0 rgba(0,0,0,0.2);
        }
        .btn-pop:active {
            transform: translateY(4px);
            box-shadow: 0 2px 0 0 rgba(0,0,0,0.2);
        }

        /* 花マルアニメーション */
        @keyframes hanamaru-pop {
            0% { transform: scale(0) rotate(-45deg); opacity: 0; }
            70% { transform: scale(1.15) rotate(10deg); opacity: 1; }
            100% { transform: scale(1) rotate(0deg); opacity: 1; }
        }

        .animate-hanamaru {
            animation: hanamaru-pop 0.6s cubic-bezier(0.34, 1.56, 0.64, 1) forwards;
        }

        /* キラキラ・紙吹雪アニメーション */
        @keyframes float-up {
            0% { transform: translateY(0) rotate(0deg); opacity: 1; }
            100% { transform: translateY(-100px) rotate(360deg); opacity: 0; }
        }
        .confetti {
            position: absolute;
            pointer-events: none;
            animation: float-up 1.2s ease-out forwards;
        }
    </style>
</head>
<body class="min-h-screen flex flex-col justify-between text-slate-800 pb-6">

    <!-- リセット確認ダイアログ（モーダル） -->
    <div id="reset-modal" class="fixed inset-0 z-50 bg-black/50 hidden flex items-center justify-center p-4">
        <div class="bg-white rounded-3xl p-6 max-w-sm w-full text-center space-y-4 border-4 border-amber-300 shadow-xl">
            <div class="text-5xl">⚠️</div>
            <h3 class="text-xl font-black text-slate-800">ほんとうに けしますか？</h3>
            <p class="text-xs font-bold text-slate-500">これまでの おぼえたスタンプや きろくが ぜんぶ きえてしまいます。</p>
            <div class="flex gap-3 pt-2">
                <button onclick="closeResetModal()" class="flex-1 py-2.5 rounded-2xl bg-slate-200 text-slate-700 font-extrabold text-sm border-2 border-slate-300 active:scale-95">キャンセル</button>
                <button onclick="confirmResetProgress()" class="flex-1 py-2.5 rounded-2xl bg-rose-500 text-white font-extrabold text-sm border-2 border-rose-600 shadow active:scale-95">けす！</button>
            </div>
        </div>
    </div>

    <!-- おしらせポップアップ（メッセージモーダル） -->
    <div id="msg-modal" class="fixed inset-0 z-50 bg-black/40 hidden flex items-center justify-center p-4">
        <div class="bg-white rounded-3xl p-5 max-w-xs w-full text-center space-y-3 border-4 border-sky-300 shadow-xl">
            <div class="text-4xl">✏️</div>
            <p id="msg-modal-text" class="text-base font-extrabold text-slate-700">もう少し 大きく しっかり かいてみよう！</p>
            <button onclick="closeMsgModal()" class="w-full py-2 rounded-xl bg-sky-500 text-white font-extrabold text-sm border-2 border-sky-600 shadow active:scale-95">はーい！</button>
        </div>
    </div>

    <!-- ヘッダー -->
    <header class="bg-amber-400 border-b-4 border-amber-500 px-3 py-2.5 shadow-md sticky top-0 z-30">
        <div class="max-w-4xl mx-auto flex justify-between items-center">
            <div class="flex items-center gap-1.5 sm:gap-2">
                <button onclick="showPage('list')" class="flex items-center gap-1 bg-white text-amber-900 px-2.5 py-1.5 rounded-full font-extrabold text-xs sm:text-sm border-2 border-amber-300 shadow active:scale-95">
                    <i class="fa-solid fa-book"></i>
                    <span>れんしゅう</span>
                </button>
                <button onclick="showTestSetup()" class="flex items-center gap-1 bg-violet-600 text-white px-2.5 py-1.5 rounded-full font-extrabold text-xs sm:text-sm border-2 border-violet-400 shadow active:scale-95">
                    <i class="fa-solid fa-pen-clip"></i>
                    <span>テスト</span>
                </button>
            </div>
            <h1 class="text-base sm:text-xl font-black text-amber-950 tracking-wider text-center hidden md:block">
                ✏️ 1ねんせい かん字
            </h1>
            <button onclick="showPage('rewards')" class="flex items-center gap-1.5 bg-rose-500 text-white px-3 py-1.5 rounded-full font-bold text-xs sm:text-sm border-2 border-rose-300 shadow active:scale-95">
                <i class="fa-solid fa-award text-yellow-300"></i>
                <span id="star-count-display">★ 0</span>
            </button>
        </div>
    </header>

    <main class="max-w-4xl mx-auto w-full px-3 pt-4 flex-grow">

        <!-- ページ1: 漢字一覧選択（練習モード） -->
        <section id="page-list" class="space-y-4">
            <!-- カテゴリタブ -->
            <div class="flex flex-wrap gap-2 justify-center" id="category-tabs">
                <!-- JavaScriptで動的生成 -->
            </div>

            <!-- 進捗状況バー -->
            <div class="bg-white p-3 rounded-2xl border-2 border-emerald-200 shadow-sm flex items-center justify-between">
                <div class="flex items-center gap-2">
                    <span class="text-2xl">💮</span>
                    <span class="font-bold text-emerald-800 text-sm md:text-base">おぼえたかん字: <span id="cleared-count" class="text-xl text-rose-500">0</span> / 80</span>
                </div>
                <button onclick="resetProgress()" class="text-xs text-slate-400 underline hover:text-rose-500">りせっと</button>
            </div>

            <!-- 漢字グリッド -->
            <div id="kanji-grid" class="grid grid-cols-4 sm:grid-cols-6 md:grid-cols-8 gap-2.5 sm:gap-3">
                <!-- JavaScriptで漢字カードを動的生成 -->
            </div>
        </section>

        <!-- ページ2: 手書き練習ボード -->
        <section id="page-practice" class="hidden w-full max-w-4xl mx-auto space-y-4">
            
            <!-- 上部ヘッダー（戻るボタンなど） -->
            <div class="flex items-center justify-between bg-white px-4 py-2 rounded-2xl border-2 border-sky-200 shadow-sm">
                <button onclick="showPage('list')" class="text-sky-600 font-bold text-sm flex items-center gap-1 bg-sky-50 px-3 py-1.5 rounded-xl border border-sky-200 active:scale-95">
                    <i class="fa-solid fa-arrow-left"></i> もどる
                </button>
                <div class="text-center">
                    <span id="practice-category" class="text-xs font-bold bg-amber-100 text-amber-800 px-3 py-1 rounded-full">カテゴリ</span>
                </div>
                <div class="w-16"></div> <!-- レイアウト均等保持用スペース -->
            </div>

            <!-- メインコンテンツ（左: 手書きキャンバス, 右: 操作ボタン & 情報） -->
            <div class="grid grid-cols-1 md:grid-cols-2 gap-6 items-start justify-items-center">
                
                <!-- 左側：手書きキャンバス エリア -->
                <div class="flex flex-col items-center space-y-3 w-full">
                    <div id="canvas-container" class="relative kanji-grid-bg rounded-3xl overflow-hidden w-[280px] h-[280px] sm:w-[320px] sm:h-[320px] shadow-sm">
                        <!-- お手本ガイド表示層（教科書体） -->
                        <div id="guide-text" class="font-kyokasho absolute inset-0 flex items-center justify-center font-bold text-slate-300 pointer-events-none select-none transition-opacity duration-200 text-[190px] sm:text-[220px]">
                            木
                        </div>

                        <!-- 手書き用キャンバス -->
                        <canvas id="paint-canvas" width="320" height="320" class="absolute inset-0 z-10 cursor-crosshair"></canvas>

                        <!-- 花マルオーバーレイ -->
                        <div id="hanamaru-overlay" class="absolute inset-0 z-20 hidden flex-col items-center justify-center bg-white/60 backdrop-blur-[2px] animate-hanamaru pointer-events-none">
                            <div class="text-8xl md:text-9xl text-rose-500 font-black drop-shadow-md">💮</div>
                            <p id="result-message" class="text-xl font-black text-rose-600 bg-white/90 px-4 py-1 rounded-full shadow border-2 border-rose-300 mt-2">たいへんよくできました！</p>
                        </div>
                    </div>

                    <!-- キャンバスツールバー -->
                    <div class="flex items-center gap-2 bg-white p-2 rounded-2xl border-2 border-slate-200 shadow-sm w-[280px] sm:w-[320px] justify-between">
                        <div class="flex items-center gap-1">
                            <button onclick="setGuideOpacity(true)" id="btn-guide-on" class="px-2.5 py-1.5 rounded-xl font-bold text-xs bg-sky-500 text-white shadow">ガイド ON</button>
                            <button onclick="setGuideOpacity(false)" id="btn-guide-off" class="px-2.5 py-1.5 rounded-xl font-bold text-xs bg-slate-100 text-slate-600">ガイド OFF</button>
                        </div>
                        <button onclick="clearCanvas()" class="px-3 py-1.5 rounded-xl font-bold text-xs bg-rose-100 text-rose-700 hover:bg-rose-200 active:scale-95 flex items-center gap-1">
                            <i class="fa-solid fa-rotate-left"></i> けす
                        </button>
                    </div>
                </div>

                <!-- 右側：漢字情報 & 操作パネル -->
                <div class="flex flex-col space-y-3.5 w-full max-w-[320px] md:max-w-none">
                    
                    <!-- 漢字・読み & 音声読み上げボタン -->
                    <div class="bg-white p-3.5 rounded-2xl border-2 border-amber-200 shadow-sm flex items-center justify-between">
                        <div>
                            <p class="text-xs font-bold text-amber-700">かん字の よみ:</p>
                            <h2 id="practice-reading" class="text-xl md:text-2xl font-extrabold text-slate-700 mt-0.5 flex items-center gap-2">
                                <span class="font-kyokasho text-3xl text-amber-900">木</span>（き）
                            </h2>
                        </div>
                        <button id="btn-speak" onclick="speakCurrentKanji()" class="bg-amber-400 hover:bg-amber-500 text-amber-950 w-12 h-12 rounded-2xl font-bold shadow-md flex items-center justify-center text-xl active:scale-95 transition">
                            <i class="fa-solid fa-volume-high"></i>
                        </button>
                    </div>

                    <!-- 例文カード -->
                    <div class="bg-amber-50 p-3.5 rounded-2xl border-2 border-amber-200 text-left">
                        <span class="text-xs font-bold text-amber-800">つかいかたの れい:</span>
                        <p id="practice-example" class="text-base sm:text-lg font-bold text-amber-950 mt-0.5">「木（き）が たっている。」</p>
                    </div>

                    <!-- 判定ボタン -->
                    <button onclick="checkDrawing()" class="btn-pop bg-emerald-500 hover:bg-emerald-600 border-2 border-emerald-600 text-white font-black py-3.5 rounded-2xl text-lg sm:text-xl shadow-lg flex items-center justify-center gap-2 w-full">
                        <i class="fa-solid fa-check text-2xl"></i> はんてい！
                    </button>

                    <!-- 前後漢字移動ボタン -->
                    <div class="flex gap-2.5 w-full pt-1">
                        <button onclick="prevKanji()" class="btn-pop bg-slate-100 hover:bg-slate-200 border-2 border-slate-300 text-slate-700 font-extrabold py-3 rounded-2xl flex-1 text-xs sm:text-sm flex items-center justify-center gap-1">
                            <i class="fa-solid fa-chevron-left"></i> まえの文字
                        </button>
                        <button onclick="nextKanji()" class="btn-pop bg-slate-100 hover:bg-slate-200 border-2 border-slate-300 text-slate-700 font-extrabold py-3 rounded-2xl flex-1 text-xs sm:text-sm flex items-center justify-center gap-1">
                            つぎの文字 <i class="fa-solid fa-chevron-right"></i>
                        </button>
                    </div>

                </div>

            </div>
        </section>

        <!-- ページ3: テスト準備 & テスト画面 -->
        <section id="page-test" class="hidden w-full max-w-4xl mx-auto space-y-4">
            
            <!-- テストスタート選択画面 -->
            <div id="test-setup" class="bg-white p-6 rounded-3xl border-4 border-violet-200 shadow-sm text-center space-y-5">
                <div class="inline-block bg-violet-100 p-4 rounded-full text-5xl mb-1">📝</div>
                <h2 class="text-2xl sm:text-3xl font-black text-violet-900">かん字 かきかたテスト</h2>
                <p class="text-sm font-bold text-slate-600 max-w-md mx-auto">
                    おてほんガイドなしで かん字を かいてみよう！<br>
                    全10もんの テストに ちょうせんだ！
                </p>

                <div class="pt-2">
                    <button onclick="startTest(10)" class="btn-pop bg-violet-600 hover:bg-violet-700 border-2 border-violet-800 text-white font-black py-4 px-8 rounded-2xl text-xl shadow-lg inline-flex items-center gap-3">
                        <i class="fa-solid fa-play text-2xl"></i> テストを はじめる！ (10もん)
                    </button>
                </div>
            </div>

            <!-- テスト実行画面 -->
            <div id="test-runner" class="hidden space-y-4">
                <!-- 上部情報バー -->
                <div class="flex items-center justify-between bg-white px-4 py-2.5 rounded-2xl border-2 border-violet-200 shadow-sm">
                    <button onclick="cancelTest()" class="text-xs font-bold text-slate-500 bg-slate-100 px-3 py-1.5 rounded-xl active:scale-95">
                        やめる
                    </button>
                    <div class="text-center font-black text-violet-900 text-base sm:text-lg">
                        もんだい <span id="test-current-num" class="text-2xl text-rose-500">1</span> / <span id="test-total-num">10</span>
                    </div>
                    <div class="text-xs font-bold bg-violet-100 text-violet-800 px-3 py-1 rounded-full">
                        てんすう: <span id="test-score-live">0</span>
                    </div>
                </div>

                <!-- メイン対話エリア (左: 手書きマス, 右: 問題とお題) -->
                <div class="grid grid-cols-1 md:grid-cols-2 gap-6 items-start justify-items-center">
                    
                    <!-- キャンバスエリア -->
                    <div class="flex flex-col items-center space-y-3 w-full">
                        <div id="test-canvas-container" class="relative kanji-grid-bg rounded-3xl overflow-hidden w-[280px] h-[280px] sm:w-[320px] sm:h-[320px] shadow-sm">
                            
                            <!-- 答え合わせ時に表示される正解お手本 -->
                            <div id="test-answer-guide" class="font-kyokasho absolute inset-0 flex items-center justify-center font-bold text-rose-400 pointer-events-none select-none text-[190px] sm:text-[220px] opacity-0 transition-opacity duration-300">
                                木
                            </div>

                            <!-- テスト用キャンバス -->
                            <canvas id="test-paint-canvas" width="320" height="320" class="absolute inset-0 z-10 cursor-crosshair"></canvas>

                            <!-- 正解・不正解オーバーレイ -->
                            <div id="test-result-overlay" class="absolute inset-0 z-20 hidden flex-col items-center justify-center bg-white/70 backdrop-blur-[2px] animate-hanamaru pointer-events-none">
                                <div id="test-result-icon" class="text-8xl md:text-9xl font-black drop-shadow-md">💮</div>
                                <p id="test-result-text" class="text-xl font-black bg-white/90 px-4 py-1 rounded-full shadow border-2 border-rose-300 mt-2">せいかい！</p>
                            </div>
                        </div>

                        <!-- リセットボタン -->
                        <div class="flex justify-end w-[280px] sm:w-[320px]">
                            <button onclick="clearTestCanvas()" class="px-3 py-1.5 rounded-xl font-bold text-xs bg-rose-100 text-rose-700 hover:bg-rose-200 active:scale-95 flex items-center gap-1">
                                <i class="fa-solid fa-rotate-left"></i> けす
                            </button>
                        </div>
                    </div>

                    <!-- 右側: お題カードと回答ボタン -->
                    <div class="flex flex-col space-y-4 w-full max-w-[320px] md:max-w-none">
                        
                        <div class="bg-violet-50 p-4 rounded-2xl border-2 border-violet-200 text-center space-y-2">
                            <span class="text-xs font-bold text-violet-700 bg-white px-3 py-1 rounded-full border border-violet-200">あかい ひらがなを かん字に しよう！</span>
                            <h3 id="test-prompt-sentence" class="text-xl sm:text-2xl font-black text-slate-800 pt-1">
                                大（おお）きな <span class="text-rose-600 underline font-extrabold decoration-2">き</span>
                            </h3>
                            <p class="text-xs text-slate-500 font-bold">□の マスに かん字を かいてね</p>
                        </div>

                        <!-- 判定 / つぎへ ボタン -->
                        <button id="btn-test-check" onclick="submitTestAnswer()" class="btn-pop bg-emerald-500 hover:bg-emerald-600 border-2 border-emerald-600 text-white font-black py-4 rounded-2xl text-xl shadow-lg flex items-center justify-center gap-2 w-full">
                            <i class="fa-solid fa-check text-2xl"></i> こたえあわせ！
                        </button>

                        <button id="btn-test-next" onclick="nextTestQuestion()" class="hidden btn-pop bg-violet-600 hover:bg-violet-700 border-2 border-violet-800 text-white font-black py-4 rounded-2xl text-xl shadow-lg flex items-center justify-center gap-2 w-full">
                            つぎの もんだいへ <i class="fa-solid fa-arrow-right"></i>
                        </button>

                    </div>

                </div>
            </div>

            <!-- テスト結果画面 -->
            <div id="test-summary" class="hidden bg-white p-6 rounded-3xl border-4 border-amber-300 shadow-xl text-center space-y-5">
                <div class="text-6xl animate-bounce">🏆</div>
                <h2 class="text-2xl sm:text-3xl font-black text-slate-800">テスト けっか はっぴょう！</h2>
                
                <div class="bg-amber-50 p-4 rounded-2xl border-2 border-amber-200 max-w-xs mx-auto">
                    <p class="text-sm font-bold text-amber-800">あなたの とくてんは...</p>
                    <div class="text-4xl sm:text-5xl font-black text-rose-500 my-2">
                        <span id="test-final-score">100</span> <span class="text-xl">てん！</span>
                    </div>
                    <p id="test-eval-msg" class="text-xs font-bold text-slate-600">素晴らしい！かん字マスターだね！</p>
                </div>

                <div class="flex gap-3 justify-center pt-2">
                    <button onclick="showTestSetup()" class="btn-pop bg-violet-600 text-white font-black py-3 px-6 rounded-2xl text-base shadow">
                        もういちど テスト
                    </button>
                    <button onclick="showPage('list')" class="btn-pop bg-slate-200 text-slate-700 font-black py-3 px-6 rounded-2xl text-base border-2 border-slate-300">
                        トップへ もどる
                    </button>
                </div>
            </div>

        </section>

        <!-- ページ4: ごほうび・スタンプ帳 -->
        <section id="page-rewards" class="hidden space-y-4">
            <div class="bg-white p-4 rounded-2xl border-2 border-rose-200 shadow-sm text-center">
                <h2 class="text-2xl font-black text-rose-600 flex items-center justify-center gap-2">
                    <i class="fa-solid fa-trophy text-amber-400"></i> ごほうび スタンプちょう
                </h2>
                <p class="text-xs sm:text-sm font-bold text-slate-500 mt-1">漢字をれんしゅうして、ほし（★）とメダルをあつめよう！</p>
            </div>

            <!-- 獲得メダル・称号カード -->
            <div id="badges-container" class="grid grid-cols-2 sm:grid-cols-3 gap-3">
                <!-- JavaScriptで動的生成 -->
            </div>
        </section>

    </main>

    <script>
        /* 小学1年生 漢字データ (全80字) */
        const KANJI_DATA = [
            // 数・時間 (16)
            { char: "一", reading: "いち", onyomi: "イチ・イツ", kunyomi: "ひと", category: "かず・じかん", example: "一（いち）ねんせい" },
            { char: "二", reading: "に", onyomi: "ニ", kunyomi: "ふた", category: "かず・じかん", example: "二（に）ねんせい" },
            { char: "三", reading: "さん", onyomi: "サン", kunyomi: "み", category: "かず・じかん", example: "三（さん）ねんせい" },
            { char: "四", reading: "よん", onyomi: "シ", kunyomi: "よ・よん", category: "かず・じかん", example: "四（よん）ほんの えんぴつ" },
            { char: "五", reading: "ご", onyomi: "ゴ", kunyomi: "いつ", category: "かず・じかん", example: "五（ご）ねんせい" },
            { char: "六", reading: "ろく", onyomi: "ロク", kunyomi: "む", category: "かず・じかん", example: "六（ろく）ねんせい" },
            { char: "七", reading: "なな", onyomi: "シチ", kunyomi: "なな", category: "かず・じかん", example: "七（なな）つの ほし" },
            { char: "八", reading: "はち", onyomi: "ハチ", kunyomi: "や", category: "かず・じかん", example: "八（はち）がつに あう" },
            { char: "九", reading: "きゅう", onyomi: "キュウ・ク", kunyomi: "ここの", category: "かず・じかん", example: "九（きゅう）じに ねる" },
            { char: "十", reading: "じゅう", onyomi: "ジュウ", kunyomi: "とお", category: "かず・じかん", example: "十（じゅう）えんの おかし" },
            { char: "百", reading: "ひゃく", onyomi: "ヒャク", kunyomi: "-", category: "かず・じかん", example: "百（ひゃく）てんを とる" },
            { char: "千", reading: "せん", onyomi: "セン", kunyomi: "ち", category: "かず・じかん", example: "千（せん）えんさつ" },
            { char: "日", reading: "ひ", onyomi: "ニチ・ジツ", kunyomi: "ひ・か", category: "かず・じかん", example: "日（ひ）が のぼる" },
            { char: "月", reading: "つき", onyomi: "ゲツ・ガツ", kunyomi: "つき", category: "かず・じかん", example: "月（つき）が でる" },
            { char: "年", reading: "ねん", onyomi: "ネン", kunyomi: "とし", category: "かず・じかん", example: "一（いち）年（ねん）せい" },
            { char: "早", reading: "はやい", onyomi: "ソウ", kunyomi: "はや", category: "かず・じかん", example: "早（はや）おきを する" },

            // 自然・いきもの (19)
            { char: "雨", reading: "あめ", onyomi: "ウ", kunyomi: "あめ", category: "しぜん・いきもの", example: "雨（あめ）が ふる" },
            { char: "花", reading: "はな", onyomi: "カ", kunyomi: "はな", category: "しぜん・いきもの", example: "花（はな）が さく" },
            { char: "貝", reading: "かい", onyomi: "-", kunyomi: "かい", category: "しぜん・いきもの", example: "貝（かい）を ひろう" },
            { char: "空", reading: "そら", onyomi: "クウ", kunyomi: "そら・あ", category: "しぜん・いきもの", example: "青（あおい）空（そら）" },
            { char: "犬", reading: "いぬ", onyomi: "ケン", kunyomi: "いぬ", category: "しぜん・いきもの", example: "犬（いぬ）と さんぽ" },
            { char: "山", reading: "やま", onyomi: "サン", kunyomi: "やま", category: "しぜん・いきもの", example: "高（たか）い山（やま）" },
            { char: "川", reading: "かわ", onyomi: "セン", kunyomi: "かわ", category: "しぜん・いきもの", example: "川（かわ）で あそぶ" },
            { char: "水", reading: "みず", onyomi: "スイ", kunyomi: "みず", category: "しぜん・いきもの", example: "冷（つめ）たい水（みず）" },
            { char: "青", reading: "あお", onyomi: "セイ", kunyomi: "あお", category: "しぜん・いきもの", example: "青（あお）い かさ" },
            { char: "夕", reading: "ゆう", onyomi: "セキ", kunyomi: "ゆう", category: "しぜん・いきもの", example: "夕（ゆう）やけが きれい" },
            { char: "石", reading: "いし", onyomi: "セキ", kunyomi: "いし", category: "しぜん・いきもの", example: "丸（まる）い石（いし）" },
            { char: "草", reading: "くさ", onyomi: "ソウ", kunyomi: "くさ", category: "しぜん・いきもの", example: "草（くさ）むらを はしる" },
            { char: "竹", reading: "たけ", onyomi: "チク", kunyomi: "たけ", category: "しぜん・いきもの", example: "竹（たけ）のこを たべる" },
            { char: "虫", reading: "むし", onyomi: "チュウ", kunyomi: "むし", category: "しぜん・いきもの", example: "虫（むし）とりを する" },
            { char: "田", reading: "た", onyomi: "デン", kunyomi: "た", category: "しぜん・いきもの", example: "田（た）んぼに いこう" },
            { char: "土", reading: "つち", onyomi: "ド・ト", kunyomi: "つち", category: "しぜん・いきもの", example: "土（つち）を いじる" },
            { char: "木", reading: "き", onyomi: "モク・ボク", kunyomi: "き", category: "しぜん・いきもの", example: "大（おお）きな木（き）" },
            { char: "林", reading: "はやし", onyomi: "リン", kunyomi: "はやし", category: "しぜん・いきもの", example: "雑木林（ぞうきばやし）" },
            { char: "森", reading: "もり", onyomi: "シン", kunyomi: "もり", category: "しぜん・いきもの", example: "緑（みどり）の森（もり）" },

            // がっこう・生活 (17)
            { char: "音", reading: "おと", onyomi: "オン・イン", kunyomi: "おと・ね", category: "がっこう・せいかつ", example: "すずの音（おと）" },
            { char: "円", reading: "えん", onyomi: "エン", kunyomi: "まる", category: "がっこう・せいかつ", example: "百円（ひゃくえん）玉（だま）" },
            { char: "学", reading: "がく", onyomi: "ガク", kunyomi: "まな", category: "がっこう・せいかつ", example: "学（がく）しゅう ノート" },
            { char: "校", reading: "こう", onyomi: "コウ", kunyomi: "-", category: "がっこう・せいかつ", example: "学（がっ）校（こう）の 校（こう）しゃ" },
            { char: "字", reading: "じ", onyomi: "ジ", kunyomi: "-", category: "がっこう・せいかつ", example: "かん字（じ）を かく" },
            { char: "文", reading: "ぶん", onyomi: "ブン・モン", kunyomi: "ふみ", category: "がっこう・せいかつ", example: "文（ぶん）しょうを よむ" },
            { char: "本", reading: "ほん", onyomi: "ホン", kunyomi: "もと", category: "がっこう・せいかつ", example: "本（ほん）を よむ" },
            { char: "名", reading: "な", onyomi: "メイ・ミョウ", kunyomi: "な", category: "がっこう・せいかつ", example: "名（な）まえを かく" },
            { char: "王", reading: "おう", onyomi: "オウ", kunyomi: "-", category: "がっこう・せいかつ", example: "王（おう）さまの かんむり" },
            { char: "金", reading: "きん", onyomi: "キン・コン", kunyomi: "かね", category: "がっこう・せいかつ", example: "金（きん）メダル" },
            { char: "赤", reading: "あか", onyomi: "セキ", kunyomi: "あか", category: "がっこう・せいかつ", example: "赤（あか）い えんぴつ" },
            { char: "白", reading: "しろ", onyomi: "ハク", kunyomi: "しろ", category: "がっこう・せいかつ", example: "白（しろ）い くも" },
            { char: "玉", reading: "たま", onyomi: "ギョク", kunyomi: "たま", category: "がっこう・せいかつ", example: "丸（まる）い 玉（たま）" },
            { char: "車", reading: "くるま", onyomi: "シャ", kunyomi: "くるま", category: "がっこう・せいかつ", example: "赤（あか）い車（くるま）" },
            { char: "糸", reading: "いと", onyomi: "シ", kunyomi: "いと", category: "がっこう・せいかつ", example: "ながい糸（いと）" },
            { char: "町", reading: "まち", onyomi: "チョウ", kunyomi: "まち", category: "がっこう・せいかつ", example: "にぎやかな町（まち）" },
            { char: "村", reading: "むら", onyomi: "ソン", kunyomi: "むら", category: "がっこう・せいかつ", example: "しずかな村（むら）" },

            // からだ・ひと (13)
            { char: "手", reading: "て", onyomi: "シュ", kunyomi: "て", category: "からだ・ひと", example: "手（て）を あらう" },
            { char: "足", reading: "あし", onyomi: "ソク", kunyomi: "あし・た", category: "からだ・ひと", example: "足（あし）が はやい" },
            { char: "耳", reading: "みみ", onyomi: "ジ", kunyomi: "みみ", category: "からだ・ひと", example: "耳（みみ）を すます" },
            { char: "目", reading: "め", onyomi: "モク", kunyomi: "め", category: "からだ・ひと", example: "目（め）を つぶる" },
            { char: "口", reading: "くち", onyomi: "コウ", kunyomi: "くち", category: "からだ・ひと", example: "大（おお）きな口（くち）" },
            { char: "子", reading: "こ", onyomi: "シ・ス", kunyomi: "こ", category: "からだ・ひと", example: "男（おとこ）の 子（こ）" },
            { char: "女", reading: "おんな", onyomi: "ジョ", kunyomi: "おんな", category: "からだ・ひと", example: "女（おんな）の ひと" },
            { char: "男", reading: "おとこ", onyomi: "ダン・ナン", kunyomi: "おとこ", category: "からだ・ひと", example: "男（おとこ）の ひと" },
            { char: "人", reading: "ひと", onyomi: "ジン・ニン", kunyomi: "ひと", category: "からだ・ひと", example: "たくさんの人（ひと）" },
            { char: "生", reading: "せい", onyomi: "セイ・ショウ", kunyomi: "い・う・なま", category: "からだ・ひと", example: "一（いち）年（ねん）生（せい）" },
            { char: "力", reading: "ちから", onyomi: "リョク・リキ", kunyomi: "ちから", category: "からだ・ひと", example: "力（ちから）もち" },
            { char: "休", reading: "やすむ", onyomi: "キュウ", kunyomi: "やす", category: "からだ・ひと", example: "お休（やす）みなさい" },
            { char: "見", reading: "みる", onyomi: "ケン", kunyomi: "み", category: "からだ・ひと", example: "テレビを 見（み）る" },

            // うごき・むき・大きさ (14)
            { char: "上", reading: "うえ", onyomi: "ジョウ", kunyomi: "うえ・あ", category: "むき・大きさ", example: "上（うえ）を むく" },
            { char: "下", reading: "した", onyomi: "カ・ゲ", kunyomi: "した・さ", category: "むき・大きさ", example: "坂（さか）を 下（くだ）る" },
            { char: "左", reading: "ひだり", onyomi: "サ", kunyomi: "ひだり", category: "むき・大きさ", example: "左（ひだり）がわ" },
            { char: "右", reading: "みぎ", onyomi: "ウ・ユウ", kunyomi: "みぎ", category: "むき・大きさ", example: "右（みぎ）てを あげる" },
            { char: "大", reading: "おおきい", onyomi: "ダイ・タイ", kunyomi: "おお", category: "むき・大きさ", example: "大（おお）きな いぬ" },
            { char: "小", reading: "ちいさい", onyomi: "ショウ", kunyomi: "ちい・こ", category: "むき・大きさ", example: "小（ちい）さな はな" },
            { char: "中", reading: "なか", onyomi: "チュウ", kunyomi: "なか", category: "むき・大きさ", example: "はこの 中（なか）" },
            { char: "正", reading: "ただしい", onyomi: "セイ・ショウ", kunyomi: "ただ・まさ", category: "むき・大きさ", example: "正（ただ）しい こたえ" },
            { char: "先", reading: "せん", onyomi: "セン", kunyomi: "さき", category: "むき・大きさ", example: "先（せん）せい" },
            { char: "立", reading: "たつ", onyomi: "リツ", kunyomi: "た", category: "むき・大きさ", example: "きりりと 立（た）つ" },
            { char: "入", reading: "はいる", onyomi: "ニュウ", kunyomi: "はい・い", category: "むき・大きさ", example: "へやに 入（はい）る" },
            { char: "出", reading: "でる", onyomi: "シュツ", kunyomi: "で・だ", category: "むき・大きさ", example: "外（そと）に 出（で）る" },
            { char: "天", reading: "てん", onyomi: "テン", kunyomi: "あまつ", category: "むき・大きさ", example: "天（てん）きが いい" },
            { char: "気", reading: "き", onyomi: "キ・ケ", kunyomi: "-", category: "むき・大きさ", example: "元（げん）気（き）な 子" }
        ];

        /* カテゴリリスト定義 */
        const CATEGORIES = [
            { id: "all", label: "ぜんぶ (80)" },
            { id: "かず・じかん", label: "🔢 かず・じかん" },
            { id: "しぜん・いきもの", label: "🌿 しぜん・いきもの" },
            { id: "がっこう・せいかつ", label: "🏫 がっこう・せいかつ" },
            { id: "からだ・ひと", label: "🙋 からだ・ひと" },
            { id: "むき・大きさ", label: "↕️ むき・大きさ" }
        ];

        /* アプリの状態 */
        let state = {
            currentCategory: "all",
            currentIndex: 0,
            clearedKanji: new Set(),
            showGuide: true,
            isDrawing: false
        };

        /* テストの状態 */
        let testState = {
            questions: [],
            currentIndex: 0,
            score: 0,
            isAnswered: false,
            isDrawing: false
        };

        /* Canvas設定 */
        let canvas, ctx;
        let testCanvas, testCtx;

        // 効果音再生用 (Web Audio API)
        const audioCtx = new (window.AudioContext || window.webkitAudioContext)();

        function playSound(type) {
            if (audioCtx.state === 'suspended') {
                audioCtx.resume();
            }
            const osc = audioCtx.createOscillator();
            const gain = audioCtx.createGain();
            osc.connect(gain);
            gain.connect(audioCtx.destination);

            const now = audioCtx.currentTime;

            if (type === 'stroke') {
                osc.type = 'sine';
                osc.frequency.setValueAtTime(400, now);
                osc.frequency.exponentialRampToValueAtTime(800, now + 0.05);
                gain.gain.setValueAtTime(0.08, now);
                gain.gain.linearRampToValueAtTime(0.01, now + 0.05);
                osc.start(now);
                osc.stop(now + 0.05);
            } else if (type === 'clear') {
                osc.type = 'triangle';
                osc.frequency.setValueAtTime(300, now);
                osc.frequency.exponentialRampToValueAtTime(150, now + 0.15);
                gain.gain.setValueAtTime(0.15, now);
                gain.gain.linearRampToValueAtTime(0.01, now + 0.15);
                osc.start(now);
                osc.stop(now + 0.15);
            } else if (type === 'success') {
                const notes = [523.25, 659.25, 783.99, 1046.50]; // C5, E5, G5, C6
                notes.forEach((freq, idx) => {
                    const o = audioCtx.createOscillator();
                    const g = audioCtx.createGain();
                    o.type = 'sine';
                    o.frequency.setValueAtTime(freq, now + idx * 0.08);
                    g.connect(audioCtx.destination);
                    g.gain.setValueAtTime(0.2, now + idx * 0.08);
                    g.gain.exponentialRampToValueAtTime(0.001, now + idx * 0.08 + 0.3);
                    o.start(now + idx * 0.08);
                    o.stop(now + idx * 0.08 + 0.3);
                });
            } else if (type === 'retry') {
                osc.type = 'sawtooth';
                osc.frequency.setValueAtTime(220, now);
                osc.frequency.setValueAtTime(180, now + 0.1);
                gain.gain.setValueAtTime(0.1, now);
                gain.gain.linearRampToValueAtTime(0.01, now + 0.25);
                osc.start(now);
                osc.stop(now + 0.25);
            }
        }

        window.addEventListener('DOMContentLoaded', () => {
            loadProgress();
            initCategoryTabs();
            renderKanjiGrid();
            initCanvas();
            initTestCanvas();
            updateHeaderStars();
            renderBadges();

            window.addEventListener('resize', () => {
                adjustCanvasSize();
                adjustTestCanvasSize();
            });
        });

        /* ローカルストレージ進捗管理 */
        function loadProgress() {
            const saved = localStorage.getItem('kanji_cleared_v1');
            if (saved) {
                state.clearedKanji = new Set(JSON.parse(saved));
            }
        }

        function saveProgress() {
            localStorage.setItem('kanji_cleared_v1', JSON.stringify(Array.from(state.clearedKanji)));
            updateHeaderStars();
            renderBadges();
        }

        function resetProgress() {
            document.getElementById('reset-modal').classList.remove('hidden');
        }

        function closeResetModal() {
            document.getElementById('reset-modal').classList.add('hidden');
        }

        function confirmResetProgress() {
            state.clearedKanji.clear();
            saveProgress();
            renderKanjiGrid();
            closeResetModal();
        }

        function showMsgModal(text) {
            document.getElementById('msg-modal-text').innerText = text;
            document.getElementById('msg-modal').classList.remove('hidden');
        }

        function closeMsgModal() {
            document.getElementById('msg-modal').classList.add('hidden');
        }

        function updateHeaderStars() {
            const count = state.clearedKanji.size;
            document.getElementById('star-count-display').innerText = `★ ${count}`;
            document.getElementById('cleared-count').innerText = count;
        }

        function showPage(pageId) {
            document.getElementById('page-list').classList.add('hidden');
            document.getElementById('page-practice').classList.add('hidden');
            document.getElementById('page-test').classList.add('hidden');
            document.getElementById('page-rewards').classList.add('hidden');

            document.getElementById(`page-${pageId}`).classList.remove('hidden');
            window.scrollTo({ top: 0, behavior: 'smooth' });
        }

        /* カテゴリタブ描画 */
        function initCategoryTabs() {
            const container = document.getElementById('category-tabs');
            container.innerHTML = CATEGORIES.map(cat => `
                <button onclick="setCategory('${cat.id}')" 
                    id="tab-${cat.id}"
                    class="px-3 py-1.5 rounded-xl font-bold text-xs sm:text-sm border-2 transition-all duration-150 ${
                        state.currentCategory === cat.id 
                            ? 'bg-amber-500 text-white border-amber-600 shadow' 
                            : 'bg-white text-amber-900 border-amber-200 hover:bg-amber-50'
                    }">
                    ${cat.label}
                </button>
            `).join('');
        }

        function setCategory(catId) {
            state.currentCategory = catId;
            initCategoryTabs();
            renderKanjiGrid();
        }

        /* 漢字グリッド描画 */
        function renderKanjiGrid() {
            const grid = document.getElementById('kanji-grid');
            const filtered = KANJI_DATA.filter(k => state.currentCategory === 'all' || k.category === state.currentCategory);

            grid.innerHTML = filtered.map((k) => {
                const globalIndex = KANJI_DATA.findIndex(item => item.char === k.char);
                const isCleared = state.clearedKanji.has(k.char);

                return `
                    <button onclick="startPractice(${globalIndex})" 
                        class="relative bg-white rounded-2xl p-2 border-2 ${isCleared ? 'border-emerald-400 bg-emerald-50' : 'border-slate-200'} 
                        shadow hover:shadow-md active:scale-95 transition flex flex-col items-center justify-between aspect-square">
                        ${isCleared ? '<span class="absolute top-1 right-1 text-xs sm:text-sm">💮</span>' : ''}
                        <span class="font-kyokasho text-2xl sm:text-3xl font-extrabold text-slate-800 my-auto">${k.char}</span>
                        <span class="text-[10px] sm:text-xs font-bold text-slate-500 truncate w-full text-center">${k.reading}</span>
                    </button>
                `;
            }).join('');
        }

        /* 練習用キャンバス初期化 */
        function initCanvas() {
            canvas = document.getElementById('paint-canvas');
            ctx = canvas.getContext('2d');

            adjustCanvasSize();

            canvas.addEventListener('mousedown', startDrawing);
            canvas.addEventListener('mousemove', draw);
            canvas.addEventListener('mouseup', stopDrawing);
            canvas.addEventListener('mouseleave', stopDrawing);

            canvas.addEventListener('touchstart', (e) => {
                e.preventDefault();
                startDrawing(e.touches[0]);
            }, { passive: false });

            canvas.addEventListener('touchmove', (e) => {
                e.preventDefault();
                draw(e.touches[0]);
            }, { passive: false });

            canvas.addEventListener('touchend', stopDrawing);
        }

        function adjustCanvasSize() {
            const container = document.getElementById('canvas-container');
            if (!container || !canvas) return;
            
            const rect = container.getBoundingClientRect();
            canvas.width = rect.width;
            canvas.height = rect.height;

            resetCanvasStyle(ctx, canvas);
        }

        function resetCanvasStyle(cCtx, cCanvas) {
            cCtx.lineWidth = Math.max(12, cCanvas.width / 20);
            cCtx.lineCap = 'round';
            cCtx.lineJoin = 'round';
            cCtx.strokeStyle = '#1e293b';
        }

        function getCanvasCoordinates(cCanvas, e) {
            const rect = cCanvas.getBoundingClientRect();
            return {
                x: (e.clientX - rect.left) * (cCanvas.width / rect.width),
                y: (e.clientY - rect.top) * (cCanvas.height / rect.height)
            };
        }

        function startDrawing(e) {
            state.isDrawing = true;
            const pos = getCanvasCoordinates(canvas, e);
            ctx.beginPath();
            ctx.moveTo(pos.x, pos.y);
            playSound('stroke');
        }

        function draw(e) {
            if (!state.isDrawing) return;
            const pos = getCanvasCoordinates(canvas, e);
            ctx.lineTo(pos.x, pos.y);
            ctx.stroke();
        }

        function stopDrawing() {
            if (state.isDrawing) {
                state.isDrawing = false;
                ctx.closePath();
            }
        }

        function clearCanvas() {
            ctx.clearRect(0, 0, canvas.width, canvas.height);
            document.getElementById('hanamaru-overlay').classList.add('hidden');
            playSound('clear');
        }

        function setGuideOpacity(show) {
            state.showGuide = show;
            const guide = document.getElementById('guide-text');
            const btnOn = document.getElementById('btn-guide-on');
            const btnOff = document.getElementById('btn-guide-off');

            if (show) {
                guide.style.opacity = '1';
                btnOn.className = "px-2.5 py-1.5 rounded-xl font-bold text-xs bg-sky-500 text-white shadow";
                btnOff.className = "px-2.5 py-1.5 rounded-xl font-bold text-xs bg-slate-100 text-slate-600";
            } else {
                guide.style.opacity = '0';
                btnOff.className = "px-2.5 py-1.5 rounded-xl font-bold text-xs bg-sky-500 text-white shadow";
                btnOn.className = "px-2.5 py-1.5 rounded-xl font-bold text-xs bg-slate-100 text-slate-600";
            }
        }

        function startPractice(index) {
            state.currentIndex = index;
            const k = KANJI_DATA[index];

            document.getElementById('guide-text').innerText = k.char;
            document.getElementById('practice-reading').innerHTML = `<span class="font-kyokasho text-2xl text-amber-900">${k.char}</span>（${k.reading}）`;
            document.getElementById('practice-category').innerText = k.category;
            document.getElementById('practice-example').innerText = k.example;

            clearCanvas();
            showPage('practice');
            adjustCanvasSize();

            speakCurrentKanji();
        }

        function prevKanji() {
            state.currentIndex = (state.currentIndex - 1 + KANJI_DATA.length) % KANJI_DATA.length;
            startPractice(state.currentIndex);
        }

        function nextKanji() {
            state.currentIndex = (state.currentIndex + 1) % KANJI_DATA.length;
            startPractice(state.currentIndex);
        }

        /* 音声読み上げ */
        function speakCurrentKanji() {
            const k = KANJI_DATA[state.currentIndex];
            if ('speechSynthesis' in window) {
                window.speechSynthesis.cancel();
                const uttr = new SpeechSynthesisUtterance(k.char);
                uttr.lang = 'ja-JP';
                uttr.rate = 0.85;
                window.speechSynthesis.speak(uttr);
            }
        }

        function checkDrawing() {
            const imgData = ctx.getImageData(0, 0, canvas.width, canvas.height);
            let drawnPixels = 0;

            for (let i = 3; i < imgData.data.length; i += 4) {
                if (imgData.data[i] > 20) drawnPixels++;
            }

            const minRequiredPixels = (canvas.width * canvas.height) * 0.012;

            if (drawnPixels > minRequiredPixels) {
                const currentKanji = KANJI_DATA[state.currentIndex].char;
                state.clearedKanji.add(currentKanji);
                saveProgress();

                const overlay = document.getElementById('hanamaru-overlay');
                document.getElementById('result-message').innerText = "たいへんよくできました！";
                overlay.classList.remove('hidden');

                playSound('success');
                triggerConfetti(document.getElementById('canvas-container'));

                setTimeout(() => {
                    renderKanjiGrid();
                }, 1500);

            } else {
                playSound('retry');
                showMsgModal('もう少し 大きく しっかり かいてみよう！');
            }
        }

        /* ----------------------------------------------------
           テストモード機能
        ---------------------------------------------------- */
        function showTestSetup() {
            showPage('test');
            document.getElementById('test-setup').classList.remove('hidden');
            document.getElementById('test-runner').classList.add('hidden');
            document.getElementById('test-summary').classList.add('hidden');
        }

        function initTestCanvas() {
            testCanvas = document.getElementById('test-paint-canvas');
            testCtx = testCanvas.getContext('2d');

            adjustTestCanvasSize();

            testCanvas.addEventListener('mousedown', startTestDrawing);
            testCanvas.addEventListener('mousemove', drawTest);
            testCanvas.addEventListener('mouseup', stopTestDrawing);
            testCanvas.addEventListener('mouseleave', stopTestDrawing);

            testCanvas.addEventListener('touchstart', (e) => {
                e.preventDefault();
                startTestDrawing(e.touches[0]);
            }, { passive: false });

            testCanvas.addEventListener('touchmove', (e) => {
                e.preventDefault();
                drawTest(e.touches[0]);
            }, { passive: false });

            testCanvas.addEventListener('touchend', stopTestDrawing);
        }

        function adjustTestCanvasSize() {
            const container = document.getElementById('test-canvas-container');
            if (!container || !testCanvas) return;
            
            const rect = container.getBoundingClientRect();
            testCanvas.width = rect.width;
            testCanvas.height = rect.height;

            resetCanvasStyle(testCtx, testCanvas);
        }

        function startTestDrawing(e) {
            if (testState.isAnswered) return;
            testState.isDrawing = true;
            const pos = getCanvasCoordinates(testCanvas, e);
            testCtx.beginPath();
            testCtx.moveTo(pos.x, pos.y);
            playSound('stroke');
        }

        function drawTest(e) {
            if (!testState.isDrawing || testState.isAnswered) return;
            const pos = getCanvasCoordinates(testCanvas, e);
            testCtx.lineTo(pos.x, pos.y);
            testCtx.stroke();
        }

        function stopTestDrawing() {
            if (testState.isDrawing) {
                testState.isDrawing = false;
                testCtx.closePath();
            }
        }

        function clearTestCanvas() {
            if (testState.isAnswered) return;
            testCtx.clearRect(0, 0, testCanvas.width, testCanvas.height);
            playSound('clear');
        }

        function startTest(numQuestions = 10) {
            // ランダムに漢字を選出
            const shuffled = [...KANJI_DATA].sort(() => 0.5 - Math.random());
            testState.questions = shuffled.slice(0, numQuestions);
            testState.currentIndex = 0;
            testState.score = 0;

            document.getElementById('test-setup').classList.add('hidden');
            document.getElementById('test-runner').classList.remove('hidden');
            document.getElementById('test-summary').classList.add('hidden');

            document.getElementById('test-total-num').innerText = testState.questions.length;
            document.getElementById('test-score-live').innerText = 0;

            renderTestQuestion();
        }

        /* テスト用問題文整形関数 (出題対象の漢字をひらがな赤字・下線に置換) */
        function buildTestQuestionHTML(q) {
            // 例: "大（おお）きな木（き）" -> 対象の "木（き）" を赤字・下線の "き" に置換
            const targetRegex = new RegExp(`${q.char}（[^）]+）`, 'g');
            let formatted = q.example.replace(targetRegex, `<span class="text-rose-600 underline font-extrabold decoration-2">${q.reading}</span>`);
            
            // 括弧書きパターンが無い場合へのフォールバック
            if (!formatted.includes('text-rose-600')) {
                const singleRegex = new RegExp(`${q.char}`, 'g');
                formatted = formatted.replace(singleRegex, `<span class="text-rose-600 underline font-extrabold decoration-2">${q.reading}</span>`);
            }

            // 問題文の中にあるその他のふりがなカッコ（例: 大（おお）きな）を取り除いて読みやすくする
            formatted = formatted.replace(/（[^）]+）/g, '');

            return `「${formatted}」`;
        }

        function renderTestQuestion() {
            const q = testState.questions[testState.currentIndex];
            testState.isAnswered = false;

            document.getElementById('test-current-num').innerText = testState.currentIndex + 1;
            
            // お題文（出題対象の漢字のみ赤字・下線のひらがなに変換して表示）
            document.getElementById('test-prompt-sentence').innerHTML = buildTestQuestionHTML(q);
            document.getElementById('test-answer-guide').innerText = q.char;
            document.getElementById('test-answer-guide').style.opacity = '0';

            // オーバーレイ初期化
            document.getElementById('test-result-overlay').classList.add('hidden');

            // ボタン表示初期化
            document.getElementById('btn-test-check').classList.remove('hidden');
            document.getElementById('btn-test-next').classList.add('hidden');

            testCtx.clearRect(0, 0, testCanvas.width, testCanvas.height);
            adjustTestCanvasSize();
        }

        function submitTestAnswer() {
            if (testState.isAnswered) return;

            const imgData = testCtx.getImageData(0, 0, testCanvas.width, testCanvas.height);
            let drawnPixels = 0;

            for (let i = 3; i < imgData.data.length; i += 4) {
                if (imgData.data[i] > 20) drawnPixels++;
            }

            const minRequired = (testCanvas.width * testCanvas.height) * 0.012;

            if (drawnPixels < minRequired) {
                playSound('retry');
                showMsgModal('文字が かかれていないよ！ かいてから 押してね。');
                return;
            }

            testState.isAnswered = true;
            const currentKanji = testState.questions[testState.currentIndex];

            // 正解ガイドを表示
            document.getElementById('test-answer-guide').style.opacity = '0.7';

            // 合格処理 (10点追加 & 学習済みに登録)
            testState.score += 10;
            state.clearedKanji.add(currentKanji.char);
            saveProgress();

            document.getElementById('test-score-live').innerText = testState.score;

            // 結果オーバーレイ
            const overlay = document.getElementById('test-result-overlay');
            document.getElementById('test-result-icon').innerText = '💮';
            document.getElementById('test-result-text').innerText = 'せいかい！';
            overlay.classList.remove('hidden');

            playSound('success');
            triggerConfetti(document.getElementById('test-canvas-container'));

            document.getElementById('btn-test-check').classList.add('hidden');
            document.getElementById('btn-test-next').classList.remove('hidden');
        }

        function nextTestQuestion() {
            testState.currentIndex++;
            if (testState.currentIndex < testState.questions.length) {
                renderTestQuestion();
            } else {
                finishTest();
            }
        }

        function cancelTest() {
            showPage('list');
        }

        function finishTest() {
            document.getElementById('test-runner').classList.add('hidden');
            document.getElementById('test-summary').classList.remove('hidden');

            const score = testState.score;
            document.getElementById('test-final-score').innerText = score;

            let evalMsg = "素晴らしい！かん字マスターだね！";
            if (score < 60) {
                evalMsg = "よく がんばったね！もういちど れんしゅうしてみよう！";
            } else if (score < 100) {
                evalMsg = "すごい！あとすこしで 満点だよ！";
            }
            document.getElementById('test-eval-msg').innerText = evalMsg;

            playSound('success');
            triggerConfetti(document.getElementById('test-summary'));
        }

        /* キラキラ演出 */
        function triggerConfetti(targetContainer) {
            const colors = ['#f43f5e', '#10b981', '#3b82f6', '#f59e0b', '#8b5cf6'];

            for (let i = 0; i < 20; i++) {
                const confetti = document.createElement('div');
                confetti.className = 'confetti text-lg';
                confetti.style.left = `${Math.random() * 80 + 10}%`;
                confetti.style.top = `${Math.random() * 80 + 10}%`;
                confetti.style.color = colors[Math.floor(Math.random() * colors.length)];
                confetti.innerHTML = ['★', '💮', '✨', '🎵'][Math.floor(Math.random() * 4)];
                targetContainer.appendChild(confetti);

                setTimeout(() => confetti.remove(), 1200);
            }
        }

        function renderBadges() {
            const container = document.getElementById('badges-container');
            const clearedCount = state.clearedKanji.size;

            const BADGES = [
                { count: 1, title: "はじめの一歩", desc: "1つの 漢字を おぼえた！", icon: "🌱" },
                { count: 10, title: "かん字 見習い", desc: "10個の 漢字を おぼえた！", icon: "⭐" },
                { count: 25, title: "かん字 物知り", desc: "25個の 漢字を おぼえた！", icon: "🥉" },
                { count: 50, title: "かん字 達人", desc: "50個の 漢字を おぼえた！", icon: "🥈" },
                { count: 80, title: "かん字 マスター", desc: "1ねんせいの 漢字を コンプリート！", icon: "👑" }
            ];

            container.innerHTML = BADGES.map(b => {
                const unlocked = clearedCount >= b.count;
                return `
                    <div class="p-4 rounded-2xl border-2 text-center transition-all ${
                        unlocked 
                            ? 'bg-amber-50 border-amber-300 shadow-md' 
                            : 'bg-slate-100 border-slate-200 opacity-60 grayscale'
                    }">
                        <div class="text-4xl mb-2">${unlocked ? b.icon : '🔒'}</div>
                        <h3 class="font-extrabold text-slate-800 text-sm sm:text-base">${b.title}</h3>
                        <p class="text-xs text-slate-500 font-bold mt-1">${b.desc}</p>
                        <span class="inline-block mt-2 text-[10px] font-bold px-2 py-0.5 rounded-full ${
                            unlocked ? 'bg-amber-400 text-amber-950' : 'bg-slate-300 text-slate-600'
                        }">
                            ${unlocked ? 'ゲット！' : `あと ${b.count - clearedCount}こ`}
                        </span>
                    </div>
                `;
            }).join('');
        }
    </script>
</body>
</html>
