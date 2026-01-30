# [貪食蛇遊戲 Web版] PRD

## 一、專案概述
- 專案名稱：經典貪食蛇遊戲
- 一句話描述：一個跨平台、響應式的網頁版貪食蛇遊戲，支援鍵盤 WASD/方向鍵操作，並針對手機用戶提供虛擬觸控按鍵。
- 版本：v1.0.0 (最終修復版)

## 二、問題與目標
### 問題陳述
現有的簡單貪食蛇程式碼在手機瀏覽器上經常出現按鈕無法點擊、觸控反應遲緩，以及遊戲在不同螢幕尺寸下顯示異常（如 Canvas 模糊或超出螢幕）的問題。此外，鍵盤操作時常因快速按鍵導致蛇「自殺」（反向撞擊自身）。

### 目標
1. 提供一個穩定、單一檔案的 HTML5 遊戲，確保在桌面與移動設備上均可順暢運行。
2. 解決移動端瀏覽器對點擊事件的延遲與攔截問題，實現 0 延遲觸控響應。
3. 優化遊戲邏輯，防止快速輸入造成的 Bug。

## 三、目標用戶
### 用戶特徵
- 擁有智慧型手機、平板或桌機的使用者。
- 習慣使用 WASD 或方向鍵的 PC 玩家。
- 偏好簡單、懷舊風格的休閒遊戲玩家。

### 用戶需求
- **手機用戶**：需要直觀的虛擬按鍵，且按鍵反應必須即時，不能有明顯延遲。
- **PC 用戶**：支援傳統方向鍵與左手習慣的 WASD 鍵位。
- **通用需求**：遊戲結束後能快速重開，且能記住最高分紀錄。

## 四、用戶故事
- **用戶故事1**：作為一名**手機用戶**，我想要**看到清晰的上下左右按鍵**，以便在通勤或休息時單手操作遊戲。
- **用戶故事2**：作為一名**PC 用戶**，我想要**使用 WASD 鍵控制方向**，以便符合我的打字習慣。
- **用戶故事3**：作為一名**玩家**，我想要**看到最高分紀錄**，以便挑戰自己的最佳成績，即使關閉瀏覽器後再打開也能看到。

## 五、功能需求與驗收標準
### 功能1：響應式遊戲畫布
- 描述：遊戲畫布需根據設備寬度自動調整大小，最大不超過 400px，並保持正方形比例。
- 驗收標準：
    - Given：用戶使用手機（寬度 < 400px）開啟網頁。
    - When：頁面載入完成。
    - Then：Canvas 寬度應等於螢幕寬度減去邊距，且邊界清晰可見。

### 功能2：雙重輸入支援
- 描述：同時支援鍵盤（WASD/方向鍵）與螢幕虛擬按鍵。
- 驗收標準：
    - Given：遊戲進行中。
    - When：用戶按下鍵盤 'W' 或點擊畫面「上」按鈕。
    - Then：蛇頭應立即轉向上方（若當前未向下移動）。
    - And：快速按鍵不應導致蛇在同一幀內反向移動而撞死。

### 功能3：觸控事件修復
- 描述：確保「開始遊戲」按鈕在 iOS/Android 瀏覽器上 100% 可用。
- 驗收標準：
    - Given：遊戲處於結束或準備狀態。
    - When：用戶點擊或觸摸「開始遊戲」按鈕。
    - Then：遮罩層必須消失，遊戲迴圈必須立即啟動。

### 功能4：分數系統
- 描述：即時顯示當前分數與歷史最高分，並將最高分儲存於瀏覽器。
- 驗收標準：
    - Given：用戶吃到一顆蘋果。
    - When：蛇身增長。
    - Then：分數數字 +1。
    - Given：用戶打破舊紀錄後重新整理頁面。
    - Then：最高分欄位應顯示新的分數。

## 六、技術約束
### 必須遵守
- 單一 HTML 檔案架構，不依賴外部圖片或腳本庫。
- JavaScript 必須置於 `<body>` 結束標籤之前，以確保 DOM 元素載入完成後再執行綁定。
- 使用 `pointerdown` 或同時綁定 `touchstart` 與 `click` 事件來處理移動端交互。

### 兼容性要求
- 支援 iOS Safari, Android Chrome, 桌面 Chrome/Edge/Firefox。
- 禁用使用者選取文字與雙擊縮放，提升遊戲體驗。

### 不要做
- 不要使用複雜的 CSS Framework（如 Bootstrap），保持輕量化。
- 不要使用 `alert()` 彈窗，使用 HTML Overlay 顯示訊息。

## 七、現有代碼

```html
<!DOCTYPE html>
<html lang="zh-Hant">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>貪食蛇修復版</title>
    <style>
        :root {
            --bg-color: #121212;
            --snake-color: #00ff00;
            --apple-color: #ff4444;
            --text-color: #ffffff;
            --accent-color: #4CAF50;
            --panel-bg: #1e1e1e;
        }

        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
            user-select: none;
            -webkit-user-select: none;
            touch-action: manipulation;
        }

        body {
            background-color: var(--bg-color);
            color: var(--text-color);
            font-family: 'Courier New', Courier, monospace;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            height: 100vh;
            overflow: hidden;
        }

        header {
            text-align: center;
            margin-bottom: 15px;
            width: 100%;
            max-width: 400px;
        }

        h1 {
            font-size: 2rem;
            margin-bottom: 10px;
            text-shadow: 0 0 10px var(--snake-color);
        }

        .scores {
            display: flex;
            justify-content: space-between;
            background-color: var(--panel-bg);
            padding: 10px 20px;
            border-radius: 8px;
            border: 1px solid #333;
            font-size: 1.2rem;
            font-weight: bold;
        }

        #game-container {
            position: relative;
            box-shadow: 0 0 20px rgba(0, 255, 0, 0.2);
            border: 2px solid #444;
            background-color: #000;
        }

        canvas {
            display: block;
            background-color: #000;
        }

        /* 遊戲狀態覆蓋層 */
        #overlay {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0, 0, 0, 0.9);
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            z-index: 100;
        }

        #overlay.hidden {
            display: none !important;
        }

        #overlay h2 {
            font-size: 2.5rem;
            margin-bottom: 15px;
            color: var(--snake-color);
        }

        #overlay p {
            font-size: 1.1rem;
            margin-bottom: 25px;
            color: #ccc;
            text-align: center;
            padding: 0 20px;
        }

        button.btn-start {
            padding: 15px 40px;
            font-size: 1.5rem;
            background-color: var(--accent-color);
            color: white;
            border: 2px solid #fff;
            border-radius: 50px;
            cursor: pointer;
            font-family: inherit;
            font-weight: bold;
            box-shadow: 0 0 15px var(--accent-color);
            outline: none;
        }

        button.btn-start:hover {
            background-color: #45a049;
            transform: scale(1.05);
        }

        button.btn-start:active {
            transform: scale(0.95);
        }

        /* 虛擬控制鍵 */
        #controls {
            margin-top: 20px;
            display: grid;
            grid-template-columns: 70px 70px 70px;
            grid-template-rows: 70px 70px;
            gap: 15px;
            justify-content: center;
        }

        .d-pad-btn {
            background: #333;
            border: 1px solid #555;
            border-radius: 12px;
            color: white;
            font-size: 24px;
            display: flex;
            align-items: center;
            justify-content: center;
            box-shadow: 0 4px 0 #111;
            cursor: pointer;
        }

        .d-pad-btn:active {
            box-shadow: 0 0 0 #111;
            transform: translateY(4px);
            background: var(--snake-color);
            color: #000;
        }

        .up { grid-column: 2; grid-row: 1; }
        .left { grid-column: 1; grid-row: 2; }
        .down { grid-column: 2; grid-row: 2; }
        .right { grid-column: 3; grid-row: 2; }

        @media (min-width: 768px) {
            #controls { display: none; }
        }
    </style>
</head>
<body>

    <header>
        <h1>貪食蛇</h1>
        <div class="scores">
            <span>分數: <span id="score" style="color: var(--snake-color)">0</span></span>
            <span>最高分: <span id="high-score">0</span></span>
        </div>
    </header>

    <div id="game-container">
        <canvas id="gameCanvas" width="400" height="400"></canvas>
        
        <!-- 遊戲狀態覆蓋層 -->
        <div id="overlay">
            <h2 id="overlay-title">準備開始</h2>
            <p id="overlay-msg">
                WASD 或 方向鍵 控制移動<br>
                手機請使用下方按鈕
            </p>
            <button class="btn-start" id="start-btn">開始遊戲</button>
        </div>
    </div>

    <!-- 虛擬方向鍵 -->
    <div id="controls">
        <button class="d-pad-btn up" id="btn-up">▲</button>
        <button class="d-pad-btn left" id="btn-left">◀</button>
        <button class="d-pad-btn down" id="btn-down">▼</button>
        <button class="d-pad-btn right" id="btn-right">▶</button>
    </div>

    <!-- 
      JavaScript 放在 body 結束標籤之前 
      這是確保所有元素都已載入且能被存取的最佳位置 
    -->
    <script>
        // 1. 獲取 DOM 元素
        const canvas = document.getElementById('gameCanvas');
        const ctx = canvas.getContext('2d');
        const scoreElement = document.getElementById('score');
        const highScoreElement = document.getElementById('high-score');
        const overlay = document.getElementById('overlay');
        const overlayTitle = document.getElementById('overlay-title');
        const overlayMsg = document.getElementById('overlay-msg');
        const startBtn = document.getElementById('start-btn');
        
        // 獲取方向鍵按鈕
        const btnUp = document.getElementById('btn-up');
        const btnDown = document.getElementById('btn-down');
        const btnLeft = document.getElementById('btn-left');
        const btnRight = document.getElementById('btn-right');

        // 2. 遊戲變數
        const tileCount = 20;
        let tileSize = canvas.width / tileCount; // 預設為 20
        
        let score = 0;
        let highScore = localStorage.getItem('snakeHighScore') || 0;
        let gameLoopId;
        const gameSpeed = 100; // 速度
        let isGameRunning = false;
        let canChangeDirection = true;

        let snake = [];
        let headX = 10;
        let headY = 10;
        let velocityX = 0;
        let velocityY = 0;
        
        let appleX = 5;
        let appleY = 5;

        // 顯示最高分
        highScoreElement.innerText = highScore;

        // 3. 響應式處理 (適應手機螢幕)
        function resizeGame() {
            // 限制最大寬度 400，或是螢幕寬度減去一點邊距
            const maxWidth = Math.min(window.innerWidth - 30, 400);
            canvas.width = maxWidth;
            canvas.height = maxWidth;
            tileSize = canvas.width / tileCount;
            
            // 如果不在遊戲中，重繪背景
            if (!isGameRunning) {
                drawBoard(); 
            }
        }
        window.addEventListener('resize', resizeGame);
        resizeGame(); // 初始化執行一次

        // 4. 遊戲核心功能

        function initGame() {
            console.log("初始化遊戲..."); // 除錯用
            
            // 重置狀態
            snake = [];
            headX = 10;
            headY = 10;
            score = 0;
            scoreElement.innerText = score;
            
            // 初始蛇身
            snake.push({x: 10, y: 10});
            snake.push({x: 10, y: 11});
            snake.push({x: 10, y: 12});

            // 初始方向 (向上)
            velocityX = 0;
            velocityY = -1;
            
            // 生成蘋果
            placeApple();

            // 隱藏選單
            overlay.classList.add('hidden');
            
            isGameRunning = true;
            canChangeDirection = true;

            // 確保舊的迴圈被清除
            if (gameLoopId) clearInterval(gameLoopId);
            
            // 立即繪製第一幀
            draw();
            
            // 啟動計時器
            gameLoopId = setInterval(gameLoop, gameSpeed);
        }

        function gameLoop() {
            update();
            draw();
            canChangeDirection = true;
        }

        function update() {
            // 移動頭部
            headX += velocityX;
            headY += velocityY;

            // 撞牆檢測
            if (headX < 0 || headX >= tileCount || headY < 0 || headY >= tileCount) {
                handleGameOver();
                return;
            }

            // 撞自己檢測
            for (let i = 0; i < snake.length; i++) {
                let part = snake[i];
                if (part.x === headX && part.y === headY) {
                    handleGameOver();
                    return;
                }
            }

            // 加入新頭部
            snake.unshift({x: headX, y: headY});

            // 吃蘋果
            if (headX === appleX && headY === appleY) {
                score++;
                scoreElement.innerText = score;
                placeApple();
            } else {
                // 沒吃到，移除尾巴
                snake.pop();
            }
        }

        function draw() {
            // 清空畫布
            ctx.fillStyle = 'black';
            ctx.fillRect(0, 0, canvas.width, canvas.height);

            // 畫蘋果
            ctx.fillStyle = 'red';
            ctx.beginPath();
            ctx.arc(appleX * tileSize + tileSize/2, appleY * tileSize + tileSize/2, tileSize/2 - 2, 0, Math.PI * 2);
            ctx.fill();

            // 畫蛇
            ctx.fillStyle = '#00ff00';
            for (let i = 0; i < snake.length; i++) {
                let part = snake[i];
                ctx.fillRect(part.x * tileSize + 1, part.y * tileSize + 1, tileSize - 2, tileSize - 2);
            }
        }
        
        // 單純繪製背景的函式
        function drawBoard() {
            ctx.fillStyle = 'black';
            ctx.fillRect(0, 0, canvas.width, canvas.height);
        }

        function placeApple() {
            let valid = false;
            while (!valid) {
                appleX = Math.floor(Math.random() * tileCount);
                appleY = Math.floor(Math.random() * tileCount);
                
                valid = true;
                for (let part of snake) {
                    if (part.x === appleX && part.y === appleY) {
                        valid = false;
                        break;
                    }
                }
            }
        }

        function handleGameOver() {
            isGameRunning = false;
            clearInterval(gameLoopId);
            
            if (score > highScore) {
                highScore = score;
                localStorage.setItem('snakeHighScore', highScore);
                highScoreElement.innerText = highScore;
                overlayMsg.innerText = `新紀錄！得分: ${score}`;
            } else {
                overlayMsg.innerText = `遊戲結束，得分: ${score}`;
            }

            overlayTitle.innerText = "再試一次";
            startBtn.innerText = "重新開始";
            overlay.classList.remove('hidden');
        }

        // 5. 輸入處理
        
        function changeDirection(x, y) {
            if (!isGameRunning) return;
            if (!canChangeDirection) return;

            // 防止反向
            if (x !== 0 && velocityX !== 0) return; 
            if (y !== 0 && velocityY !== 0) return;

            velocityX = x;
            velocityY = y;
            canChangeDirection = false;
        }

        // 鍵盤監聽
        document.addEventListener('keydown', (e) => {
            // 防止方向鍵滾動頁面
            if(["ArrowUp","ArrowDown","ArrowLeft","ArrowRight"].indexOf(e.code) > -1) {
                e.preventDefault();
            }

            if (e.key === 'ArrowUp' || e.key === 'w' || e.key === 'W') changeDirection(0, -1);
            else if (e.key === 'ArrowDown' || e.key === 's' || e.key === 'S') changeDirection(0, 1);
            else if (e.key === 'ArrowLeft' || e.key === 'a' || e.key === 'A') changeDirection(-1, 0);
            else if (e.key === 'ArrowRight' || e.key === 'd' || e.key === 'D') changeDirection(1, 0);
        });

        // 虛擬按鍵監聽 (使用 Pointer Events 同時支援滑鼠與觸控)
        const addBtnControl = (btn, x, y) => {
            if (!btn) return;
            // 使用 pointerdown 確保反應最快
            btn.addEventListener('pointerdown', (e) => {
                e.preventDefault(); // 防止滑鼠雙擊選取等行為
                changeDirection(x, y);
                // 讓按鈕失去焦點，避免按空白鍵再次觸發按鈕
                btn.blur(); 
            });
        };

        addBtnControl(btnUp, 0, -1);
        addBtnControl(btnDown, 0, 1);
        addBtnControl(btnLeft, -1, 0);
        addBtnControl(btnRight, 1, 0);

        // 6. 啟動按鈕事件 (關鍵修復點)
        
        // 使用 onclick 屬性作為最後一道防線
        startBtn.onclick = function() {
            console.log("按鈕被點擊了"); // 確認是否有進入函式
            initGame();
        };

        // 額外綁定 touchstart 確保手機上絕對能觸發
        startBtn.addEventListener('touchstart', (e) => {
            e.preventDefault(); // 防止觸發點擊兩次
            console.log("觸控事件觸發");
            initGame();
        }, { passive: false });

        // 初始化畫面
        drawBoard();

    </script>
</body>
</html>
```