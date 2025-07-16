<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ドロップファイブ</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            margin: 0;
            padding: 20px;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            min-height: 100vh;
        }

        .container {
            max-width: 800px;
            margin: 0 auto;
        }

        .card {
            background: white;
            border-radius: 10px;
            padding: 20px;
            margin-bottom: 20px;
            box-shadow: 0 4px 6px rgba(0,0,0,0.1);
        }

        .start-screen {
            text-align: center;
            padding: 40px;
        }

        .game-title {
            font-size: 3em;
            color: #333;
            margin-bottom: 20px;
        }

        .rules {
            text-align: left;
            margin: 20px 0;
            padding: 20px;
            background: #f8f9fa;
            border-radius: 8px;
        }

        .rules ul {
            margin: 10px 0;
            padding-left: 20px;
        }

        .rules li {
            margin: 8px 0;
        }

        .btn {
            background: linear-gradient(135deg, #8b5cf6 0%, #ec4899 100%);
            color: white;
            border: none;
            padding: 15px 30px;
            border-radius: 8px;
            font-size: 18px;
            cursor: pointer;
            transition: transform 0.2s;
        }

        .btn:hover {
            transform: translateY(-2px);
        }

        .game-screen {
            display: none;
        }

        .game-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 20px;
        }

        .game-status {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 20px;
        }

        .players {
            display: flex;
            gap: 30px;
        }

        .player {
            display: flex;
            align-items: center;
            gap: 10px;
            font-size: 18px;
        }

        .player.active {
            font-weight: bold;
        }

        .player-color {
            width: 20px;
            height: 20px;
            border-radius: 50%;
        }

        .red { background: #ef4444; }
        .blue { background: #3b82f6; }

        .timer {
            font-size: 24px;
            font-weight: bold;
        }

        .timer.warning {
            color: #ef4444;
            animation: blink 1s infinite;
        }

        .game-info {
            display: flex;
            justify-content: space-between;
            font-size: 14px;
            color: #666;
        }

        .grid-container {
            text-align: center;
            padding: 20px;
            overflow-x: auto;
        }

        .grid {
            display: inline-grid;
            grid-template-columns: repeat(15, 32px);
            gap: 2px;
            background: #ddd;
            padding: 10px;
            border-radius: 8px;
            min-width: max-content;
        }

        .cell {
            width: 32px;
            height: 32px;
            background: white;
            border: 1px solid #ccc;
            cursor: pointer;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 18px;
            transition: all 0.3s;
        }

        .cell:hover {
            background: #f0f0f0;
        }

        .cell.ground {
            background: #fef3c7;
            border-bottom: 3px solid #d97706;
        }

        .cell.red {
            background: #ef4444;
            color: white;
        }

        .cell.blue {
            background: #3b82f6;
            color: white;
        }

        .cell.winning {
            border: 3px solid #fbbf24;
            animation: winningBlink 1s infinite;
            box-shadow: 0 0 10px #fbbf24;
        }

        @keyframes winningBlink {
            0%, 100% { 
                border-color: #fbbf24;
                box-shadow: 0 0 10px #fbbf24;
            }
            50% { 
                border-color: #f59e0b;
                box-shadow: 0 0 20px #f59e0b;
            }
        }

        .winning-line {
            position: absolute;
            background: #fbbf24;
            z-index: 5;
            animation: lineGlow 2s infinite;
        }

        .winning-line.horizontal {
            height: 4px;
            top: 50%;
            transform: translateY(-50%);
        }

        .winning-line.vertical {
            width: 4px;
            left: 50%;
            transform: translateX(-50%);
        }

        .winning-line.diagonal {
            height: 4px;
            top: 50%;
            transform-origin: left center;
        }

        .cell.dropping {
            animation: drop 0.5s ease-out;
        }

        .ground-label {
            margin-top: 15px;
            color: #d97706;
            font-weight: bold;
        }

        .modal {
            display: none;
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0,0,0,0.5);
            z-index: 1000;
        }

        .modal-content {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            background: white;
            padding: 40px;
            border-radius: 10px;
            text-align: center;
            max-width: 400px;
            width: 90%;
        }

        .winner-icon {
            width: 60px;
            height: 60px;
            border-radius: 50%;
            margin: 0 auto 20px;
        }

        @keyframes blink {
            0%, 50% { opacity: 1; }
            51%, 100% { opacity: 0.5; }
        }

        @keyframes pulse {
            0%, 100% { transform: scale(1); opacity: 1; }
            50% { transform: scale(1.1); opacity: 0.8; }
        }

        @keyframes drop {
            0% { transform: translateY(-20px); opacity: 0.7; }
            100% { transform: translateY(0); opacity: 1; }
        }

        .alert {
            position: fixed;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            background: #ef4444;
            color: white;
            padding: 15px 25px;
            border-radius: 8px;
            font-weight: bold;
            z-index: 1001;
            animation: alertShow 0.3s ease-out;
        }

        @keyframes alertShow {
            0% { opacity: 0; transform: translate(-50%, -50%) scale(0.8); }
            100% { opacity: 1; transform: translate(-50%, -50%) scale(1); }
        }

        @media (max-width: 600px) {
            .grid {
                grid-template-columns: repeat(15, 20px);
            }
            .cell {
                width: 20px;
                height: 20px;
                font-size: 12px;
            }
            .game-status {
                flex-direction: column;
                gap: 15px;
            }
            .players {
                gap: 20px;
            }
        }
    </style>
</head>
<body>
    <div class="container">
        <!-- スタート画面 -->
        <div id="startScreen" class="card start-screen">
            <h1 class="game-title">ドロップファイブ</h1>
            <p>2人対戦ブロック配置パズルゲーム</p>
            
            <div class="rules">
                <h3>📋 ゲームルール</h3>
                <ul>
                    <li>各プレイヤーは1ターンに2個のブロックを配置</li>
                    <li>縦・横・斜めのいずれかで5個連続に並べると勝利</li>
                    <li>1階（地面）では隣接するブロックが必要（最初の1個を除く）</li>
                    <li>2階以上では隣接制限なし</li>
                    <li>ブロックは重力で自動的に落下</li>
                    <li>制限時間内に配置しないと負け</li>
                </ul>
            </div>
            
            <button class="btn" onclick="startGame()">🎮 ゲーム開始</button>
        </div>

        <!-- ゲーム画面 -->
        <div id="gameScreen" class="game-screen">
            <!-- ヘッダー -->
            <div class="card">
                <div class="game-header">
                    <h2>ドロップファイブ</h2>
                    <button class="btn" onclick="showStart()">新しいゲーム</button>
                </div>
            </div>

            <!-- ゲーム状況 -->
            <div class="card">
                <div class="game-status">
                    <div class="players">
                        <div class="player" id="player1">
                            <div class="player-color red"></div>
                            <span>プレイヤー1</span>
                        </div>
                        <div class="player" id="player2">
                            <div class="player-color blue"></div>
                            <span>プレイヤー2</span>
                        </div>
                    </div>
                    <div class="timer" id="timer">5:00</div>
                </div>
                <div class="game-info">
                    <div>現在のターン: <span id="currentPlayer">プレイヤー1</span></div>
                    <div>残りブロック: <span id="remainingBlocks">1</span>/<span id="maxBlocks">1</span></div>
                </div>
            </div>

            <!-- ゲームボード -->
            <div class="card grid-container">
                <div class="grid" id="gameGrid"></div>
                <div class="ground-label">⬆️ 地面レベル（隣接ブロック必要）</div>
            </div>
        </div>

        <!-- ゲーム終了モーダル -->
        <div id="gameModal" class="modal">
            <div class="modal-content">
                <h2 id="modalTitle">ゲーム終了</h2>
                <div class="winner-icon" id="winnerIcon"></div>
                <p id="modalMessage">結果メッセージ</p>
                <button class="btn" onclick="startGame()">もう一度プレイ</button>
            </div>
        </div>
    </div>

    <script>
        // ゲーム設定
        const GRID_WIDTH = 15;
        const GRID_HEIGHT = 12;
        const WIN_COUNT = 5;
        const BLOCKS_PER_TURN = 2;
        const INITIAL_TIME = 300; // 5分

        // ゲーム状態
        var game = {
            grid: [],
            currentPlayer: 1,
            remainingBlocks: BLOCKS_PER_TURN,
            timeLeft: 30, // 初手は30秒
            gameActive: false,
            timer: null,
            animating: false,
            winningCells: [],
            showingWin: false
        };

        // グリッド初期化
        function initGrid() {
            game.grid = [];
            for (let row = 0; row < GRID_HEIGHT; row++) {
                game.grid[row] = [];
                for (let col = 0; col < GRID_WIDTH; col++) {
                    game.grid[row][col] = 0;
                }
            }
        }

        // ゲーム開始
        function startGame() {
            initGrid();
            game.currentPlayer = Math.random() > 0.5 ? 1 : 2;
            game.remainingBlocks = 1; // 先行は初手1個のみ
            game.timeLeft = 30; // 初手は30秒
            game.gameActive = true;
            game.animating = false;
            game.winningCells = [];
            game.showingWin = false;

            // 先行プレイヤーの色のブロックを真ん中の地面に設置
            var centerCol = Math.floor(GRID_WIDTH / 2);
            var groundRow = GRID_HEIGHT - 1;
            game.grid[groundRow][centerCol] = game.currentPlayer;

            document.getElementById('startScreen').style.display = 'none';
            document.getElementById('gameScreen').style.display = 'block';
            document.getElementById('gameModal').style.display = 'none';

            createGrid();
            updateDisplay();
            startTimer();
        }

        // スタート画面表示
        function showStart() {
            clearInterval(game.timer);
            document.getElementById('startScreen').style.display = 'block';
            document.getElementById('gameScreen').style.display = 'none';
            document.getElementById('gameModal').style.display = 'none';
        }

        // グリッド作成
        function createGrid() {
            const gridElement = document.getElementById('gameGrid');
            gridElement.innerHTML = '';

            for (let row = 0; row < GRID_HEIGHT; row++) {
                for (let col = 0; col < GRID_WIDTH; col++) {
                    const cell = document.createElement('div');
                    cell.className = 'cell';
                    cell.onclick = () => placePiece(row, col);
                    
                    if (row === GRID_HEIGHT - 1) {
                        cell.classList.add('ground');
                    }
                    
                    gridElement.appendChild(cell);
                }
            }
        }

        // 表示更新
        function updateDisplay() {
            // プレイヤー表示
            document.getElementById('player1').classList.toggle('active', game.currentPlayer === 1);
            document.getElementById('player2').classList.toggle('active', game.currentPlayer === 2);
            
            // 現在のプレイヤー
            document.getElementById('currentPlayer').textContent = `プレイヤー${game.currentPlayer}`;
            
            // 残りブロック
            document.getElementById('remainingBlocks').textContent = game.remainingBlocks;
            
            // タイマー
            const minutes = Math.floor(game.timeLeft / 60);
            const seconds = game.timeLeft % 60;
            const timerElement = document.getElementById('timer');
            timerElement.textContent = `${minutes}:${seconds.toString().padStart(2, '0')}`;
            timerElement.classList.toggle('warning', game.timeLeft <= 30);

            // グリッド更新
            updateGrid();
        }

        // グリッド表示更新
        function updateGrid() {
            const cells = document.querySelectorAll('.cell');
            let index = 0;
            
            for (let row = 0; row < GRID_HEIGHT; row++) {
                for (let col = 0; col < GRID_WIDTH; col++) {
                    const cell = cells[index];
                    const value = game.grid[row][col];
                    
                    cell.className = 'cell';
                    if (row === GRID_HEIGHT - 1) {
                        cell.classList.add('ground');
                    }
                    
                    if (value === 1) {
                        cell.classList.add('red');
                        cell.textContent = '●';
                    } else if (value === 2) {
                        cell.classList.add('blue');
                        cell.textContent = '●';
                    } else {
                        cell.textContent = '';
                    }
                    
                    index++;
                }
            }
        }

        // 勝利ライン描画を削除（枠の点滅のみ使用）
        function drawWinningLine() {
            // 枠の点滅のみ使用するため、ライン描画は無効化
            return;
        }
        function placePiece(row, col) {
            if (!game.gameActive || game.animating || game.remainingBlocks <= 0) {
                return;
            }

            // 落下先を計算
            var dropRow = calculateDrop(col);
            if (dropRow === -1) return; // 列が満杯

            // 地面レベルの隣接チェック
            if (dropRow === GRID_HEIGHT - 1) {
                if (!isValidGroundPlacement(col)) {
                    return;
                }
            }

            // アニメーション開始
            game.animating = true;
            var cells = document.querySelectorAll('.cell');
            var cellIndex = dropRow * GRID_WIDTH + col;
            var targetCell = cells[cellIndex];
            
            targetCell.classList.add('dropping');
            
            setTimeout(function() {
                // 現在のブロック数をカウント
        function countCurrentBlocks() {
            var count = 0;
            for (var row = 0; row < GRID_HEIGHT; row++) {
                for (var col = 0; col < GRID_WIDTH; col++) {
                    if (game.grid[row][col] !== 0) {
                        count++;
                    }
                }
            }
            return count;
        }

        // ターン開始時のタイマー設定
        function setTurnTimer() {
            var currentBlocks = countCurrentBlocks();
            if (currentBlocks === 1) {
                game.timeLeft = 30; // 先行の初手後（既に中央に1個ある）は30秒
            } else {
                game.timeLeft = currentBlocks * 10; // 既存ブロック数×10秒
            }
        }

        // アラート表示
        function showAlert(message) {
            var existingAlert = document.querySelector('.alert');
            if (existingAlert) {
                existingAlert.remove();
            }
            
            var alert = document.createElement('div');
            alert.className = 'alert';
            alert.textContent = message;
            document.body.appendChild(alert);
            
            setTimeout(function() {
                alert.remove();
            }, 2000);
        }

        // ピース配置の検証（修正版）
        function canPlacePiece(targetRow, targetCol) {
            // 列が満杯かチェック
            if (game.grid[0][targetCol] !== 0) {
                return { valid: false, message: "この列は満杯です" };
            }
            
            // 実際の落下先を計算
            var actualDropRow = calculateDrop(targetCol);
            if (actualDropRow === -1 || game.grid[actualDropRow][targetCol] !== 0) {
                return { valid: false, message: "その場所には既にブロックがあります" };
            }
            
            // 地面レベルの隣接チェック
            if (actualDropRow === GRID_HEIGHT - 1) {
                if (!isValidGroundPlacement(targetCol)) {
                    return { valid: false, message: "地面レベルでは隣接するブロックが必要です" };
                }
            }
            
            return { valid: true, dropRow: actualDropRow };
        }
                game.grid[dropRow][col] = game.currentPlayer;
                game.remainingBlocks--;
                
                // 勝利チェック
                if (checkWin(dropRow, col)) {
                    game.showingWin = true;
                    showWinAnimation(function() {
                        endGame(game.currentPlayer);
                    });
                    return;
                }
                
                // ターン終了チェック
                if (game.remainingBlocks <= 0) {
                    setTimeout(function() {
                        game.currentPlayer = game.currentPlayer === 1 ? 2 : 1;
                        game.remainingBlocks = BLOCKS_PER_TURN; // 通常は2個
                        setTurnTimer(); // 新しいターンのタイマー設定
                        startTimer(); // タイマー再開
                        updateDisplay();
                    }, 500); // ターン交代の演出ディレイ
                }
                
                game.animating = false;
                updateDisplay();
            }, 500);
        }

        // 落下位置計算
        function calculateDrop(col) {
            for (var row = GRID_HEIGHT - 1; row >= 0; row--) {
                if (game.grid[row][col] === 0) {
                    return row;
                }
            }
            return -1; // 列が満杯
        }

        // 地面レベルの配置チェック
        function isValidGroundPlacement(col) {
            // 初回は制限なし
            var hasAnyPiece = false;
            for (var r = 0; r < GRID_HEIGHT; r++) {
                for (var c = 0; c < GRID_WIDTH; c++) {
                    if (game.grid[r][c] !== 0) {
                        hasAnyPiece = true;
                        break;
                    }
                }
                if (hasAnyPiece) break;
            }
            
            if (!hasAnyPiece) return true;

            // 隣接チェック
            var groundRow = GRID_HEIGHT - 1;
            var leftCol = col - 1;
            var rightCol = col + 1;
            
            if (leftCol >= 0 && game.grid[groundRow][leftCol] !== 0) return true;
            if (rightCol < GRID_WIDTH && game.grid[groundRow][rightCol] !== 0) return true;
            
            return false;
        }

        // 勝利チェック（改良版 - 連続するすべてのセルを記録）
        function checkWin(row, col) {
            var player = game.grid[row][col];
            var directions = [
                [0, 1],  // 横
                [1, 0],  // 縦
                [1, 1],  // 斜め右下
                [1, -1]  // 斜め左下
            ];

            for (var d = 0; d < directions.length; d++) {
                var dr = directions[d][0];
                var dc = directions[d][1];
                var line = [[row, col]]; // 中心セルから始める
                
                // 左側（負方向）をチェック
                for (var i = 1; i < WIN_COUNT; i++) {
                    var r = row - dr * i;
                    var c = col - dc * i;
                    if (r >= 0 && r < GRID_HEIGHT && c >= 0 && c < GRID_WIDTH && 
                        game.grid[r][c] === player) {
                        line.unshift([r, c]); // 配列の前に追加
                    } else {
                        break;
                    }
                }
                
                // 右側（正方向）をチェック
                for (var i = 1; i < WIN_COUNT; i++) {
                    var r = row + dr * i;
                    var c = col + dc * i;
                    if (r >= 0 && r < GRID_HEIGHT && c >= 0 && c < GRID_WIDTH && 
                        game.grid[r][c] === player) {
                        line.push([r, c]); // 配列の後に追加
                    } else {
                        break;
                    }
                }
                
                if (line.length >= WIN_COUNT) {
                    game.winningCells = line;
                    return true;
                }
            }
            
            return false;
        }

        // 勝利アニメーション（15秒間）
        function showWinAnimation(callback) {
            updateDisplay(); // 点滅表示を反映
            
            // 15秒間アニメーションを見せた後に終了処理
            setTimeout(function() {
                callback(); // endGame()
            }, 15000);
        }

        // タイマー開始
        function startTimer() {
            clearInterval(game.timer);
            game.timer = setInterval(function() {
                if (!game.gameActive || game.showingWin) return;
                
                game.timeLeft--;
                updateDisplay();
                
                if (game.timeLeft <= 0) {
                    var winner = game.currentPlayer === 1 ? 2 : 1;
                    endGame(winner, '時間切れ');
                }
            }, 1000);
        }

        // ゲーム終了
        function endGame(winner, reason) {
            reason = reason || '';
            game.gameActive = false;
            clearInterval(game.timer);
            
            var modal = document.getElementById('gameModal');
            var title = document.getElementById('modalTitle');
            var icon = document.getElementById('winnerIcon');
            var message = document.getElementById('modalMessage');
            
            title.textContent = 'プレイヤー' + winner + 'の勝利！';
            icon.className = 'winner-icon ' + (winner === 1 ? 'red' : 'blue');
            message.textContent = reason || (winner === 1 ? '赤' : '青') + 'プレイヤーの勝利です！';
            
            modal.style.display = 'block';
        }

        // ゲーム強制終了
        function endGameNow() {
            if (!game.gameActive) return;
            
            if (confirm('ゲームを終了しますか？')) {
                game.gameActive = false;
                clearInterval(game.timer);
                showStart();
            }
        }
        document.addEventListener('DOMContentLoaded', () => {
            showStart();
        });
    </script>
</body>
</html>
