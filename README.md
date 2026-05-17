# Forgotzhz.github.io
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no">
    <title>将军之战 · 完整版</title>
    <style>
        :root {
            --bg: #0f0f1a;
            --panel: #1a1a2e;
            --text: #e0e0e0;
            --gold: #f0c040;
            --player: #4a90d9;
            --ai: #d94a4a;
        }
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }
        body {
            background: var(--bg);
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            font-family: 'Segoe UI', 'PingFang SC', 'Microsoft YaHei', sans-serif;
            padding: 10px;
            user-select: none;
            -webkit-user-select: none;
        }
        .game-container {
            background: var(--panel);
            border-radius: 18px;
            padding: 16px 20px 20px;
            box-shadow: 0 20px 50px rgba(0, 0, 0, 0.6), 0 0 0 1px rgba(255, 255, 255, 0.08);
            display: flex;
            flex-direction: column;
            align-items: center;
            gap: 12px;
            max-width: 100%;
            position: relative;
        }
        .header {
            display: flex;
            align-items: center;
            justify-content: space-between;
            width: 100%;
            flex-wrap: wrap;
            gap: 10px;
        }
        .player-badge {
            display: flex;
            align-items: center;
            gap: 8px;
            padding: 8px 16px;
            border-radius: 10px;
            font-weight: 700;
            font-size: 14px;
            letter-spacing: 0.5px;
        }
        .player-badge.you {
            background: rgba(74, 144, 217, 0.2);
            color: #7ab8f5;
            border: 1px solid rgba(74, 144, 217, 0.4);
        }
        .player-badge.ai {
            background: rgba(217, 74, 74, 0.2);
            color: #f57a7a;
            border: 1px solid rgba(217, 74, 74, 0.4);
        }
        .dot {
            width: 12px;
            height: 12px;
            border-radius: 50%;
            display: inline-block;
        }
        .dot.you {
            background: #5ba0f0;
            box-shadow: 0 0 8px rgba(74, 144, 217, 0.7);
        }
        .dot.ai {
            background: #e85555;
            box-shadow: 0 0 8px rgba(217, 74, 74, 0.7);
        }
        .turn-info {
            background: rgba(255, 255, 255, 0.06);
            padding: 6px 14px;
            border-radius: 20px;
            font-size: 14px;
            font-weight: 600;
            color: #ccc;
        }
        .status-text {
            font-size: 14px;
            font-weight: 600;
            color: #aaa;
            min-height: 18px;
            text-align: center;
            transition: color 0.3s;
        }
        .status-text.victory {
            color: #5bf07a;
            text-shadow: 0 0 8px rgba(91, 240, 122, 0.5);
        }
        .status-text.defeat {
            color: #f05b5b;
            text-shadow: 0 0 8px rgba(240, 91, 91, 0.5);
        }

        /* 画布容器 */
        .canvas-wrapper {
            position: relative;
            display: inline-block;
            line-height: 0;
        }

        canvas {
            border-radius: 10px;
            cursor: pointer;
            box-shadow: 0 4px 20px rgba(0, 0, 0, 0.5);
            max-width: 100%;
            height: auto;
            display: block;
        }

        /* 结束画面覆盖层 */
        .end-screen {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            border-radius: 10px;
            pointer-events: none;
            opacity: 0;
            transition: opacity 0.4s ease;
            z-index: 10;
        }
        .end-screen.show {
            opacity: 1;
            pointer-events: auto;
        }
        .end-screen.victory-bg {
            background: rgba(10, 30, 20, 0.85);
        }
        .end-screen.defeat-bg {
            background: rgba(30, 10, 10, 0.85);
        }

        .end-title {
            font-size: 42px;
            font-weight: 900;
            letter-spacing: 2px;
            margin-bottom: 8px;
            text-shadow: 0 0 30px currentColor;
            animation: popIn 0.5s ease-out;
        }
        .end-title.victory-text {
            color: #5bf07a;
        }
        .end-title.defeat-text {
            color: #f05b5b;
        }

        .end-subtitle {
            font-size: 16px;
            color: #ccc;
            margin-bottom: 20px;
            letter-spacing: 1px;
        }

        .btn-center {
            padding: 14px 32px;
            border-radius: 12px;
            border: none;
            font-weight: 700;
            font-size: 16px;
            cursor: pointer;
            letter-spacing: 0.5px;
            transition: all 0.25s;
            font-family: inherit;
            outline: none;
            background: #f0c040;
            color: #1a1a2e;
            box-shadow: 0 6px 20px rgba(240, 192, 64, 0.4);
            animation: popIn 0.6s ease-out;
        }
        .btn-center:hover {
            background: #f5d060;
            transform: translateY(-2px);
            box-shadow: 0 8px 25px rgba(240, 192, 64, 0.55);
        }
        .btn-center:active {
            transform: scale(0.96);
        }

        @keyframes popIn {
            0% {
                transform: scale(0.5);
                opacity: 0;
            }
            70% {
                transform: scale(1.08);
                opacity: 1;
            }
            100% {
                transform: scale(1);
                opacity: 1;
            }
        }

        .row-buttons {
            display: flex;
            gap: 10px;
            flex-wrap: wrap;
            justify-content: center;
        }
        .btn {
            padding: 9px 18px;
            border-radius: 10px;
            border: none;
            font-weight: 700;
            font-size: 13px;
            cursor: pointer;
            letter-spacing: 0.4px;
            transition: all 0.2s;
            font-family: inherit;
            outline: none;
            background: rgba(255, 255, 255, 0.08);
            color: #ccc;
            border: 1px solid rgba(255, 255, 255, 0.15);
        }
        .btn:hover {
            background: rgba(255, 255, 255, 0.15);
            color: #fff;
        }
        .btn.active {
            background: rgba(255, 255, 255, 0.18);
            color: #fff;
            border-color: #f0c040;
        }
        .btn.restart {
            background: #f0c040;
            color: #1a1a2e;
            border: none;
            box-shadow: 0 4px 14px rgba(240, 192, 64, 0.3);
        }
        .btn.restart:hover {
            background: #f5d060;
            transform: translateY(-1px);
        }
        .btn.replay {
            background: #4a6fa5;
            color: white;
            border: none;
        }
        .legend {
            display: flex;
            gap: 12px;
            flex-wrap: wrap;
            justify-content: center;
            font-size: 11px;
            color: #999;
        }
        .legend span {
            display: flex;
            align-items: center;
            gap: 4px;
        }
        .legend-dot {
            width: 12px;
            height: 12px;
            border-radius: 3px;
            display: inline-block;
        }
        .legend-dot.player {
            background: #5ba0f0;
        }
        .legend-dot.ai {
            background: #e85555;
        }
        .legend-dot.neutral {
            background: #777;
        }
        .legend-dot.mountain {
            background: #5a5a6a;
            border: 1px solid #888;
        }
        .legend-dot.barrack {
            background: #b8860b;
            border-radius: 2px;
            border: 1px solid #f0c040;
        }
        .legend-dot.general {
            background: #f0c040;
            border-radius: 50%;
        }

        @media (max-width: 780px) {
            .game-container {
                padding: 8px 6px 12px;
            }
            .btn {
                padding: 6px 12px;
                font-size: 11px;
            }
            .player-badge {
                padding: 6px 10px;
                font-size: 12px;
            }
            .end-title {
                font-size: 30px;
            }
            .btn-center {
                padding: 10px 24px;
                font-size: 14px;
            }
        }
    </style>
</head>
<body>
    <div class="game-container">
        <div class="header">
            <div class="player-badge you"><span class="dot you"></span> 你 <span id="playerScore">0</span></div>
            <div class="turn-info">⏳ 回合 <span id="turnDisplay">0</span></div>
            <div class="player-badge ai"><span class="dot ai"></span> AI <span id="aiScore">0</span></div>
        </div>
        <div class="status-text" id="gameStatus">点击领地或WASD移动 | 每回合一次行动</div>
        <!-- 画布容器 + 结束画面 -->
        <div class="canvas-wrapper" id="canvasWrapper">
            <canvas id="gameCanvas"></canvas>
            <div class="end-screen" id="endScreen">
                <div class="end-title" id="endTitle"></div>
                <div class="end-subtitle" id="endSubtitle"></div>
                <button class="btn-center" id="btnEndRestart">🔄 再来一局</button>
            </div>
        </div>
        <div class="row-buttons">
            <button class="btn restart" id="btnRestart">🔄 新游戏</button>
            <button class="btn" id="btnReplay" style="display:none;">▶️ 观看回放</button>
            <button class="btn" id="btnMoveMode">⚔️ 移动模式: 全部</button>
            <button class="btn" id="btnSpeed1" data-speed="500">🐢 标准</button>
            <button class="btn active" id="btnSpeed2" data-speed="350">⚡ 快速</button>
            <button class="btn" id="btnSpeed3" data-speed="200">🚀 极速</button>
        </div>
        <div class="legend">
            <span><span class="legend-dot player"></span> 你</span>
            <span><span class="legend-dot ai"></span> AI</span>
            <span><span class="legend-dot neutral"></span> 中立</span>
            <span><span class="legend-dot mountain"></span> 山脉</span>
            <span><span class="legend-dot barrack"></span> 兵营</span>
            <span><span class="legend-dot general"></span> 总部</span>
        </div>
    </div>

    <script>
        (function() {
            // ---------- 地图配置 ----------
            const COLS = 25;
            const ROWS = 20;
            const CELL_SIZE = 30;
            const CANVAS_WIDTH = COLS * CELL_SIZE;
            const CANVAS_HEIGHT = ROWS * CELL_SIZE;

            let PLAYER_GENERAL = null;
            let AI_GENERAL = null;

            // DOM
            const canvas = document.getElementById('gameCanvas');
            const ctx = canvas.getContext('2d');
            const canvasWrapper = document.getElementById('canvasWrapper');
            const endScreen = document.getElementById('endScreen');
            const endTitle = document.getElementById('endTitle');
            const endSubtitle = document.getElementById('endSubtitle');
            const btnEndRestart = document.getElementById('btnEndRestart');
            const playerScoreEl = document.getElementById('playerScore');
            const aiScoreEl = document.getElementById('aiScore');
            const turnDisplay = document.getElementById('turnDisplay');
            const gameStatusEl = document.getElementById('gameStatus');
            const btnRestart = document.getElementById('btnRestart');
            const btnReplay = document.getElementById('btnReplay');
            const btnMoveMode = document.getElementById('btnMoveMode');
            const speedBtns = [
                document.getElementById('btnSpeed1'),
                document.getElementById('btnSpeed2'),
                document.getElementById('btnSpeed3')
            ];

            canvas.width = CANVAS_WIDTH;
            canvas.height = CANVAS_HEIGHT;
            canvas.style.width = Math.min(CANVAS_WIDTH, 750) + 'px';
            canvas.style.height = 'auto';

            // 状态
            let grid = [];
            let turnNumber = 0;
            let selectedCell = null;
            let gameOver = false;
            let gameResult = null;
            let playerMoveThisTurn = false;
            let aiMoveThisTurn = false;
            let moveMode = 'all';
            let turnIntervalId = null;
            let animationFrameId = null;
            let particles = [];
            let playerVisibility = [];
            let actionLog = [];
            let replayMode = false;
            let replayIndex = 0;
            let replayIntervalId = null;
            let initialGridState = null;
            let TURN_INTERVAL = 350;

            // ---------- 地图生成 ----------
            function createGrid() {
                grid = [];
                for (let r = 0; r < ROWS; r++) {
                    grid[r] = [];
                    for (let c = 0; c < COLS; c++) {
                        grid[r][c] = { owner: 'neutral', army: 0, type: 'normal' };
                    }
                }

                const mountainCount = 20 + Math.floor(Math.random() * 12);
                let placed = 0;
                while (placed < mountainCount) {
                    const r = Math.floor(Math.random() * ROWS);
                    const c = Math.floor(Math.random() * COLS);
                    if (grid[r][c].owner === 'neutral' && grid[r][c].army === 0) {
                        grid[r][c].owner = 'mountain';
                        placed++;
                    }
                }

                let pRow, pCol;
                do {
                    pRow = 2 + Math.floor(Math.random() * (ROWS - 4));
                    pCol = 2 + Math.floor(Math.random() * (COLS - 4));
                } while (grid[pRow][pCol].owner === 'mountain');
                PLAYER_GENERAL = { row: pRow, col: pCol };
                grid[pRow][pCol] = { owner: 'player', army: 10, type: 'general' };

                let aRow, aCol;
                do {
                    aRow = 2 + Math.floor(Math.random() * (ROWS - 4));
                    aCol = 2 + Math.floor(Math.random() * (COLS - 4));
                } while (grid[aRow][aCol].owner === 'mountain' ||
                    (Math.abs(aRow - pRow) + Math.abs(aCol - pCol)) < 10);
                AI_GENERAL = { row: aRow, col: aCol };
                grid[aRow][aCol] = { owner: 'ai', army: 10, type: 'general' };

                const barrackCount = 10 + Math.floor(Math.random() * 6);
                placed = 0;
                while (placed < barrackCount) {
                    const r = Math.floor(Math.random() * ROWS);
                    const c = Math.floor(Math.random() * COLS);
                    if (grid[r][c].owner === 'neutral' && grid[r][c].army === 0 &&
                        (Math.abs(r - pRow) + Math.abs(c - pCol) > 2) &&
                        (Math.abs(r - aRow) + Math.abs(c - aCol) > 2)) {
                        grid[r][c].type = 'barrack';
                        grid[r][c].army = 40 + Math.floor(Math.random() * 11);
                        placed++;
                    }
                }
            }

            function getNeighbors(row, col) {
                const dirs = [
                    [-1, 0],
                    [1, 0],
                    [0, -1],
                    [0, 1]
                ];
                return dirs.map(([dr, dc]) => ({ row: row + dr, col: col + dc }))
                    .filter(p => p.row >= 0 && p.row < ROWS && p.col >= 0 && p.col < COLS);
            }

            function calculatePlayerVisibility() {
                playerVisibility = Array.from({ length: ROWS }, () => Array(COLS).fill(false));
                for (let r = 0; r < ROWS; r++) {
                    for (let c = 0; c < COLS; c++) {
                        if (grid[r][c].owner === 'player') {
                            playerVisibility[r][c] = true;
                            for (const n of getNeighbors(r, c)) {
                                playerVisibility[n.row][n.col] = true;
                            }
                        }
                    }
                }
            }

            // 回合增长
            function executeTurnGrowth() {
                turnNumber++;
                for (let r = 0; r < ROWS; r++) {
                    for (let c = 0; c < COLS; c++) {
                        const cell = grid[r][c];
                        if (cell.owner === 'mountain') continue;
                        if (cell.owner === 'neutral' && cell.type === 'barrack') continue;
                        if ((cell.owner === 'player' || cell.owner === 'ai') && cell.type === 'barrack') {
                            cell.army += 1;
                            if (cell.army > 999) cell.army = 999;
                            continue;
                        }
                        if (cell.type === 'general' && (cell.owner === 'player' || cell.owner === 'ai')) {
                            cell.army += 1;
                            if (cell.army > 999) cell.army = 999;
                            continue;
                        }
                        if ((cell.owner === 'player' || cell.owner === 'ai') && cell.type === 'normal') {
                            if (turnNumber % 25 === 0) cell.army += 1;
                            if (cell.army > 999) cell.army = 999;
                        }
                    }
                }
                playerMoveThisTurn = false;
                aiMoveThisTurn = false;
                calculatePlayerVisibility();
                updateScoreDisplay();
            }

            // 移动逻辑 - 只有玩家操作才产生粒子
            function moveArmy(fromRow, fromCol, toRow, toCol, forceAmount = null, showParticles = false) {
                if (gameOver) return null;
                const src = grid[fromRow][fromCol];
                const dst = grid[toRow][toCol];
                if (src.owner !== 'player' && src.owner !== 'ai') return null;
                if (dst.owner === 'mountain') return null;
                if (src.army <= 0) return null;

                let moveAmount;
                if (forceAmount !== null) {
                    moveAmount = Math.min(forceAmount, src.army);
                    if (moveAmount <= 0) return null;
                } else {
                    const isAttack = (dst.owner !== src.owner && dst.owner !== 'neutral') || (dst.owner ===
                        'neutral' && dst.army > 0);
                    if (isAttack) moveAmount = src.army > 1 ? src.army - 1 : 1;
                    else if (dst.owner === src.owner) {
                        moveAmount = Math.floor(src.army / 2);
                        if (moveAmount < 1 && src.army >= 1) moveAmount = 1;
                    } else moveAmount = src.army > 1 ? src.army - 1 : 1;
                }

                src.army -= moveAmount;
                let result = {
                    moved: moveAmount,
                    fromRow,
                    fromCol,
                    toRow,
                    toCol,
                    combat: false,
                    captured: false,
                    generalCaptured: false,
                    winner: null
                };

                if (dst.owner === src.owner) {
                    dst.army += moveAmount;
                } else if (dst.owner === 'neutral') {
                    if (moveAmount > dst.army) {
                        dst.owner = src.owner;
                        dst.army = moveAmount - dst.army;
                        result.captured = true;
                        result.combat = dst.army > 0;
                    } else {
                        dst.army -= moveAmount;
                        result.combat = true;
                    }
                } else {
                    result.combat = true;
                    if (moveAmount > dst.army) {
                        const wasGeneral = (dst.type === 'general');
                        dst.owner = src.owner;
                        dst.army = moveAmount - dst.army;
                        if (wasGeneral) {
                            dst.type = 'normal';
                            result.generalCaptured = true;
                            result.winner = src.owner;
                        }
                        result.captured = true;
                    } else {
                        dst.army -= moveAmount;
                        if (dst.army <= 0) { dst.army = 0;
                            dst.owner = 'neutral'; }
                    }
                }

                if (src.army <= 0 && src.type !== 'general') src.army = 0;

                // 只有玩家相关的操作才显示粒子
                if (showParticles) {
                    if (result.captured) spawnParticles(toCol, toRow, 'player');
                    else if (result.combat) spawnParticles(toCol, toRow, 'combat');
                }

                if (result.generalCaptured) endGame(result.winner);
                checkGeneralsAlive();
                calculatePlayerVisibility();
                updateScoreDisplay();
                return result;
            }

            function checkGeneralsAlive() {
                let pAlive = false,
                    aAlive = false;
                for (let r = 0; r < ROWS; r++)
                    for (let c = 0; c < COLS; c++)
                        if (grid[r][c].type === 'general') {
                            if (grid[r][c].owner === 'player') pAlive = true;
                            if (grid[r][c].owner === 'ai') aAlive = true;
                        }
                if (!pAlive) endGame('ai');
                if (!aAlive) endGame('player');
            }

            function endGame(winner) {
                if (gameOver) return;
                gameOver = true;
                gameResult = winner === 'player' ? 'victory' : 'defeat';
                if (turnIntervalId) clearInterval(turnIntervalId);
                turnIntervalId = null;
                gameStatusEl.textContent = gameResult === 'victory' ? '🎉 胜利！摧毁敌方总部！' : '💀 失败！总部被占领...';
                gameStatusEl.className = 'status-text ' + gameResult;
                btnReplay.style.display = 'inline-block';
                updateScoreDisplay();
                draw();
                // 显示结束画面
                showEndScreen();
            }

            function showEndScreen() {
                if (gameResult === 'victory') {
                    endTitle.textContent = '🎉 胜 利 🎉';
                    endTitle.className = 'end-title victory-text';
                    endSubtitle.textContent = '你摧毁了敌方总部，赢得了战争！';
                    endScreen.className = 'end-screen victory-bg show';
                } else {
                    endTitle.textContent = '💀 失 败 💀';
                    endTitle.className = 'end-title defeat-text';
                    endSubtitle.textContent = '你的总部被敌方占领...';
                    endScreen.className = 'end-screen defeat-bg show';
                }
            }

            function hideEndScreen() {
                endScreen.className = 'end-screen';
            }

            function resetGame() {
                if (turnIntervalId) clearInterval(turnIntervalId);
                if (replayIntervalId) clearInterval(replayIntervalId);
                turnIntervalId = null;
                replayIntervalId = null;
                gameOver = false;
                gameResult = null;
                turnNumber = 0;
                selectedCell = null;
                particles = [];
                playerMoveThisTurn = false;
                aiMoveThisTurn = false;
                actionLog = [];
                replayMode = false;
                replayIndex = 0;
                btnReplay.style.display = 'none';
                hideEndScreen();
                createGrid();
                initialGridState = JSON.parse(JSON.stringify(grid));
                calculatePlayerVisibility();
                updateScoreDisplay();
                gameStatusEl.textContent = '点击领地或WASD移动 | 每回合一次行动';
                gameStatusEl.className = 'status-text';
                turnIntervalId = setInterval(gameTurn, TURN_INTERVAL);
                draw();
            }

            function gameTurn() {
                if (gameOver || replayMode) return;
                executeTurnGrowth();
                if (!aiMoveThisTurn && !gameOver) {
                    aiAct();
                    aiMoveThisTurn = true;
                }
                draw();
            }

            function aiAct() {
                const cells = [];
                for (let r = 0; r < ROWS; r++)
                    for (let c = 0; c < COLS; c++)
                        if (grid[r][c].owner === 'ai' && grid[r][c].army > 1)
                            cells.push({ r, c, army: grid[r][c].army, isGeneral: grid[r][c].type === 'general' });
                cells.sort((a, b) => b.army - a.army);

                for (const cell of cells) {
                    if (cell.isGeneral && cell.army < 6) continue;
                    const nbrs = shuffleArr(getNeighbors(cell.r, cell.c));
                    for (const n of nbrs) {
                        const dst = grid[n.row][n.col];
                        if (dst.owner === 'player' && !dst.isGeneral && cell.army - 1 > dst.army) {
                            const res = moveArmy(cell.r, cell.c, n.row, n.col, null, false);
                            if (res) { logAction('ai', cell.r, cell.c, n.row, n.col, res.moved); return; }
                        }
                    }
                }
                for (const cell of cells) {
                    if (cell.isGeneral && cell.army < 15) continue;
                    const nbrs = shuffleArr(getNeighbors(cell.r, cell.c));
                    for (const n of nbrs) {
                        const dst = grid[n.row][n.col];
                        if (dst.type === 'barrack' && dst.owner === 'neutral' && cell.army - 1 > dst.army) {
                            const res = moveArmy(cell.r, cell.c, n.row, n.col, null, false);
                            if (res) { logAction('ai', cell.r, cell.c, n.row, n.col, res.moved); return; }
                        }
                    }
                }
                for (const cell of cells) {
                    if (cell.army < 4) continue;
                    const nbrs = shuffleArr(getNeighbors(cell.r, cell.c));
                    for (const n of nbrs) {
                        if (grid[n.row][n.col].owner === 'neutral' && grid[n.row][n.col].army === 0) {
                            const res = moveArmy(cell.r, cell.c, n.row, n.col, null, false);
                            if (res) { logAction('ai', cell.r, cell.c, n.row, n.col, res.moved); return; }
                        }
                    }
                }
                for (const cell of cells) {
                    if (cell.army < 6) continue;
                    const nbrs = getNeighbors(cell.r, cell.c);
                    const weakOwn = nbrs.filter(n => grid[n.row][n.col].owner === 'ai' && grid[n.row][n.col].army < cell
                        .army - 3);
                    if (weakOwn.length > 0) {
                        weakOwn.sort((a, b) => grid[a.row][a.col].army - grid[b.row][b.col].army);
                        const target = weakOwn[0];
                        const res = moveArmy(cell.r, cell.c, target.row, target.col, null, false);
                        if (res) { logAction('ai', cell.r, cell.c, target.row, target.col, res.moved); return; }
                    }
                }
            }

            function shuffleArr(arr) {
                for (let i = arr.length - 1; i > 0; i--) {
                    const j = Math.floor(Math.random() * (i + 1));
                    [arr[i], arr[j]] = [arr[j], arr[i]];
                }
                return arr;
            }

            function logAction(player, fr, fc, tr, tc, amount) {
                actionLog.push({ turn: turnNumber, player, fromRow: fr, fromCol: fc, toRow: tr, toCol: tc, amount });
            }

            function playerMove(fromRow, fromCol, toRow, toCol, forceAmount = null) {
                if (gameOver || replayMode) return false;
                if (playerMoveThisTurn) {
                    gameStatusEl.textContent = '⏳ 本回合已行动';
                    return false;
                }
                const src = grid[fromRow][fromCol];
                if (src.owner !== 'player' || src.army <= 0) return false;
                let amount = forceAmount;
                if (amount === null) {
                    amount = moveMode === 'all' ? (src.army > 1 ? src.army - 1 : 1) : Math.floor(src.army / 2);
                    if (amount < 1 && src.army >= 1) amount = 1;
                }
                const res = moveArmy(fromRow, fromCol, toRow, toCol, amount, true);
                if (res) {
                    playerMoveThisTurn = true;
                    logAction('player', fromRow, fromCol, toRow, toCol, res.moved);
                    gameStatusEl.textContent = '✅ 兵力已移动';
                    draw();
                    return true;
                }
                return false;
            }

            function handleWASD(direction) {
                if (gameOver || replayMode || !selectedCell) return;
                if (playerMoveThisTurn) {
                    gameStatusEl.textContent = '⏳ 本回合已行动';
                    return;
                }
                const { row, col } = selectedCell;
                let dr = 0,
                    dc = 0;
                if (direction === 'w') dr = -1;
                else if (direction === 's') dr = 1;
                else if (direction === 'a') dc = -1;
                else if (direction === 'd') dc = 1;
                const nr = row + dr,
                    nc = col + dc;
                if (nr < 0 || nr >= ROWS || nc < 0 || nc >= COLS) return;
                if (grid[nr][nc].owner === 'mountain') return;
                const moved = playerMove(row, col, nr, nc);
                if (moved) {
                    const targetCell = grid[nr][nc];
                    if (targetCell.owner === 'player' && targetCell.army > 0) {
                        selectedCell = { row: nr, col: nc };
                    } else {
                        selectedCell = null;
                    }
                }
            }

            function spawnParticles(col, row, type) {
                const cx = col * CELL_SIZE + CELL_SIZE / 2;
                const cy = row * CELL_SIZE + CELL_SIZE / 2;
                const color = type === 'player' ? '#5ba0f0' : '#f0c040';
                for (let i = 0; i < 12; i++) {
                    particles.push({
                        x: cx,
                        y: cy,
                        vx: (Math.random() - 0.5) * 3.5,
                        vy: (Math.random() - 0.5) * 3.5 - 1,
                        life: 1.0,
                        decay: 0.02 + Math.random() * 0.03,
                        color: color
                    });
                }
            }

            function updateParticles() {
                for (let i = particles.length - 1; i >= 0; i--) {
                    const p = particles[i];
                    p.x += p.vx;
                    p.y += p.vy;
                    p.life -= p.decay;
                    if (p.life <= 0) particles.splice(i, 1);
                }
            }

            function startReplay() {
                if (!actionLog.length || gameOver === false) return;
                replayMode = true;
                replayIndex = 0;
                if (turnIntervalId) clearInterval(turnIntervalId);
                if (replayIntervalId) clearInterval(replayIntervalId);
                grid = JSON.parse(JSON.stringify(initialGridState));
                turnNumber = 0;
                playerMoveThisTurn = false;
                aiMoveThisTurn = false;
                calculatePlayerVisibility();
                updateScoreDisplay();
                gameStatusEl.textContent = '⏪ 回放中...';
                btnReplay.textContent = '⏸️ 停止回放';
                hideEndScreen();
                replayIntervalId = setInterval(replayStep, 400);
            }

            function stopReplay() {
                replayMode = false;
                if (replayIntervalId) clearInterval(replayIntervalId);
                replayIntervalId = null;
                btnReplay.textContent = '▶️ 观看回放';
                gameStatusEl.textContent = gameOver ? (gameResult === 'victory' ? '胜利！' : '失败。') : '回放已停止';
                if (!gameOver) turnIntervalId = setInterval(gameTurn, TURN_INTERVAL);
                if (gameOver) showEndScreen();
            }

            function replayStep() {
                if (replayIndex >= actionLog.length) { stopReplay(); return; }
                const act = actionLog[replayIndex];
                while (turnNumber < act.turn) executeTurnGrowth();
                const src = grid[act.fromRow][act.fromCol];
                if (src && src.army >= act.amount) moveArmy(act.fromRow, act.fromCol, act.toRow, act.toCol, act.amount,
                    false);
                replayIndex++;
                draw();
            }

            function updateScoreDisplay() {
                playerScoreEl.textContent = getTotal('player') + '兵/' + getTerritory('player') + '地';
                aiScoreEl.textContent = getTotal('ai') + '兵/' + getTerritory('ai') + '地';
                turnDisplay.textContent = turnNumber;
            }

            function getTotal(owner) { let t = 0; for (let r = 0; r < ROWS; r++)
                    for (let c = 0; c < COLS; c++) if (grid[r][c].owner === owner) t += grid[r][c].army; return t; }

            function getTerritory(owner) { let t = 0; for (let r = 0; r < ROWS; r++)
                    for (let c = 0; c < COLS; c++) if (grid[r][c].owner === owner) t++; return t; }

            function draw() {
                ctx.clearRect(0, 0, CANVAS_WIDTH, CANVAS_HEIGHT);
                for (let r = 0; r < ROWS; r++) {
                    for (let c = 0; c < COLS; c++) {
                        const x = c * CELL_SIZE,
                            y = r * CELL_SIZE;
                        const cell = grid[r][c];
                        const visible = playerVisibility[r]?.[c] || replayMode;
                        if (!visible) {
                            ctx.fillStyle = '#111122';
                            ctx.fillRect(x + 1, y + 1, CELL_SIZE - 2, CELL_SIZE - 2);
                            continue;
                        }
                        let bg = '#2a2a38';
                        if (cell.owner === 'player') bg = lerpColor('#3a6fb5', '#1e3d6b', Math.min(cell.army / 30, 0.8));
                        else if (cell.owner === 'ai') bg = lerpColor('#c94040', '#6b1e1e', Math.min(cell.army / 30, 0.8));
                        else if (cell.owner === 'mountain') bg = '#3a3a4a';
                        else if (cell.type === 'barrack') bg = '#8b6914';
                        ctx.fillStyle = bg;
                        ctx.fillRect(x + 1, y + 1, CELL_SIZE - 2, CELL_SIZE - 2);

                        if (selectedCell && selectedCell.row === r && selectedCell.col === c) {
                            ctx.strokeStyle = '#f0c040';
                            ctx.lineWidth = 2.5;
                            ctx.strokeRect(x + 1, y + 1, CELL_SIZE - 2, CELL_SIZE - 2);
                        }

                        if (cell.owner === 'mountain') {
                            ctx.fillStyle = '#5a5a6a';
                            ctx.beginPath();
                            ctx.moveTo(x + 6, y + CELL_SIZE - 6);
                            ctx.lineTo(x + CELL_SIZE / 2, y + 4);
                            ctx.lineTo(x + CELL_SIZE - 6, y + CELL_SIZE - 6);
                            ctx.closePath();
                            ctx.fill();
                            ctx.fillStyle = '#fff';
                            ctx.font = 'bold 11px sans-serif';
                            ctx.textAlign = 'center';
                            ctx.textBaseline = 'middle';
                            ctx.fillText('⛰️', x + CELL_SIZE / 2, y + CELL_SIZE / 2);
                        } else if (cell.type === 'general') {
                            ctx.fillStyle = '#f0c040';
                            ctx.beginPath();
                            ctx.arc(x + CELL_SIZE / 2, y + CELL_SIZE / 2, 6, 0, 2 * Math.PI);
                            ctx.fill();
                            ctx.fillStyle = '#fff';
                            ctx.font = 'bold 10px sans-serif';
                            ctx.textAlign = 'center';
                            ctx.textBaseline = 'middle';
                            ctx.fillText('👑', x + CELL_SIZE / 2, y + CELL_SIZE / 2 - 1);
                        } else if (cell.type === 'barrack') {
                            ctx.fillStyle = '#cd9b1d';
                            ctx.fillRect(x + 6, y + 10, CELL_SIZE - 12, CELL_SIZE - 16);
                            ctx.fillStyle = '#f0c040';
                            ctx.fillRect(x + 8, y + 8, CELL_SIZE - 16, 4);
                            ctx.fillStyle = '#fff';
                            ctx.font = 'bold 11px sans-serif';
                            ctx.textAlign = 'center';
                            ctx.textBaseline = 'middle';
                            ctx.fillText('🏰', x + CELL_SIZE / 2, y + CELL_SIZE / 2);
                        }

                        if (cell.army > 0 && cell.owner !== 'mountain') {
                            ctx.fillStyle = '#fff';
                            ctx.font = 'bold 12px sans-serif';
                            ctx.textAlign = 'center';
                            ctx.textBaseline = 'middle';
                            ctx.fillText(cell.army, x + CELL_SIZE / 2, y + CELL_SIZE / 2 + (cell.type === 'general' ||
                                cell.type === 'barrack' ? 9 : 0));
                        }
                    }
                }

                for (const p of particles) {
                    ctx.globalAlpha = p.life;
                    ctx.fillStyle = p.color;
                    ctx.beginPath();
                    ctx.arc(p.x, p.y, 2.5, 0, 2 * Math.PI);
                    ctx.fill();
                }
                ctx.globalAlpha = 1;
            }

            function lerpColor(c1, c2, t) {
                const a = parseInt(c1.slice(1), 16),
                    b = parseInt(c2.slice(1), 16);
                const r = Math.round(((a >> 16) & 0xff) * (1 - t) + ((b >> 16) & 0xff) * t);
                const g = Math.round(((a >> 8) & 0xff) * (1 - t) + ((b >> 8) & 0xff) * t);
                const bl = Math.round((a & 0xff) * (1 - t) + (b & 0xff) * t);
                return `rgb(${r},${g},${bl})`;
            }

            function gameLoop() {
                updateParticles();
                draw();
                animationFrameId = requestAnimationFrame(gameLoop);
            }

            // 事件
            canvas.addEventListener('click', e => {
                if (gameOver || replayMode) return;
                const rect = canvas.getBoundingClientRect();
                const scaleX = CANVAS_WIDTH / rect.width,
                    scaleY = CANVAS_HEIGHT / rect.height;
                const mx = (e.clientX - rect.left) * scaleX,
                    my = (e.clientY - rect.top) * scaleY;
                const col = Math.floor(mx / CELL_SIZE),
                    row = Math.floor(my / CELL_SIZE);
                if (row < 0 || row >= ROWS || col < 0 || col >= COLS) return;
                if (!playerVisibility[row]?.[col]) return;
                const cell = grid[row][col];
                if (!selectedCell) {
                    if (cell.owner === 'player' && cell.army > 0) selectedCell = { row, col };
                } else if (selectedCell.row === row && selectedCell.col === col) {
                    selectedCell = null;
                } else {
                    const dist = Math.abs(row - selectedCell.row) + Math.abs(col - selectedCell.col);
                    if (dist === 1) {
                        playerMove(selectedCell.row, selectedCell.col, row, col);
                        selectedCell = null;
                    } else if (cell.owner === 'player' && cell.army > 0) {
                        selectedCell = { row, col };
                    }
                }
            });

            window.addEventListener('keydown', e => {
                if (replayMode) return;
                const key = e.key.toLowerCase();
                if (key === 'r') { resetGame(); return; }
                if (key === 'escape') { selectedCell = null; return; }
                if (['w', 'a', 's', 'd'].includes(key)) {
                    e.preventDefault();
                    handleWASD(key);
                }
                if (key === 'tab') {
                    e.preventDefault();
                    moveMode = moveMode === 'all' ? 'half' : 'all';
                    btnMoveMode.textContent = `⚔️ 移动模式: ${moveMode === 'all' ? '全部' : '一半'}`;
                }
            });

            btnRestart.addEventListener('click', resetGame);
            btnEndRestart.addEventListener('click', resetGame);
            btnReplay.addEventListener('click', () => { if (replayMode) stopReplay();
                else startReplay(); });
            btnMoveMode.addEventListener('click', () => {
                moveMode = moveMode === 'all' ? 'half' : 'all';
                btnMoveMode.textContent = `⚔️ 移动模式: ${moveMode === 'all' ? '全部' : '一半'}`;
            });
            speedBtns.forEach(btn => {
                btn.addEventListener('click', () => {
                    speedBtns.forEach(b => b.classList.remove('active'));
                    btn.classList.add('active');
                    TURN_INTERVAL = parseInt(btn.dataset.speed);
                    if (turnIntervalId) clearInterval(turnIntervalId);
                    if (!gameOver && !replayMode) turnIntervalId = setInterval(gameTurn, TURN_INTERVAL);
                });
            });

            function boot() {
                createGrid();
                initialGridState = JSON.parse(JSON.stringify(grid));
                calculatePlayerVisibility();
                updateScoreDisplay();
                hideEndScreen();
                gameLoop();
                turnIntervalId = setInterval(gameTurn, TURN_INTERVAL);
                draw();
            }
            boot();
        })();
    </script>
</body>
</html>
