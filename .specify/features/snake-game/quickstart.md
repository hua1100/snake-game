# 貪吃蛇遊戲快速開始指南

## 概述
本指南將幫助您快速開始貪吃蛇遊戲的開發，包括環境設定、專案結構和基本實作。

## 環境需求

### 必要工具
- Node.js 16+ 
- npm 或 yarn
- 現代瀏覽器 (Chrome 60+, Firefox 55+, Safari 12+, Edge 79+)
- Git

### 開發工具
- Visual Studio Code (推薦)
- ESLint 擴展
- Prettier 擴展
- Jest 擴展

## 專案設定

### 1. 建立專案目錄
```bash
mkdir snake-game
cd snake-game
```

### 2. 初始化 npm 專案
```bash
npm init -y
```

### 3. 安裝依賴套件
```bash
# 開發依賴
npm install --save-dev jest @types/jest eslint prettier webpack webpack-cli webpack-dev-server

# 生產依賴 (如果需要)
npm install --save-dev typescript @types/node
```

### 4. 建立專案結構
```
snake-game/
├── src/
│   ├── core/
│   │   ├── GameEngine.js
│   │   ├── Snake.js
│   │   ├── Food.js
│   │   └── Position.js
│   ├── render/
│   │   └── GameRenderer.js
│   ├── input/
│   │   └── InputHandler.js
│   ├── utils/
│   │   └── GameConfig.js
│   └── index.js
├── tests/
│   ├── core/
│   ├── render/
│   └── input/
├── public/
│   ├── index.html
│   └── style.css
├── dist/
├── package.json
├── webpack.config.js
├── jest.config.js
├── .eslintrc.js
└── .prettierrc
```

## 基本實作

### 1. HTML 結構 (public/index.html)
```html
<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>貪吃蛇遊戲</title>
    <link rel="stylesheet" href="style.css">
</head>
<body>
    <div class="game-container">
        <h1>貪吃蛇遊戲</h1>
        <div class="game-info">
            <div class="score">分數: <span id="score">0</span></div>
            <div class="high-score">最高分: <span id="highScore">0</span></div>
            <div class="level">等級: <span id="level">1</span></div>
        </div>
        <canvas id="gameCanvas" width="400" height="400"></canvas>
        <div class="controls">
            <button id="startBtn">開始遊戲</button>
            <button id="pauseBtn">暫停</button>
            <button id="resetBtn">重新開始</button>
        </div>
        <div class="instructions">
            <p>使用方向鍵控制蛇的移動</p>
            <p>按空白鍵暫停/繼續遊戲</p>
            <p>按 R 鍵重新開始</p>
        </div>
    </div>
    <script src="../dist/bundle.js"></script>
</body>
</html>
```

### 2. CSS 樣式 (public/style.css)
```css
body {
    font-family: Arial, sans-serif;
    background-color: #1a1a1a;
    color: #ffffff;
    margin: 0;
    padding: 20px;
    display: flex;
    justify-content: center;
    align-items: center;
    min-height: 100vh;
}

.game-container {
    text-align: center;
    background-color: #2a2a2a;
    padding: 20px;
    border-radius: 10px;
    box-shadow: 0 4px 6px rgba(0, 0, 0, 0.3);
}

h1 {
    color: #00ff00;
    margin-bottom: 20px;
}

.game-info {
    display: flex;
    justify-content: space-around;
    margin-bottom: 20px;
    font-size: 18px;
}

#gameCanvas {
    border: 2px solid #00ff00;
    background-color: #000000;
    margin-bottom: 20px;
}

.controls {
    margin-bottom: 20px;
}

button {
    background-color: #00ff00;
    color: #000000;
    border: none;
    padding: 10px 20px;
    margin: 0 5px;
    border-radius: 5px;
    cursor: pointer;
    font-size: 16px;
    font-weight: bold;
}

button:hover {
    background-color: #00cc00;
}

button:disabled {
    background-color: #666666;
    cursor: not-allowed;
}

.instructions {
    font-size: 14px;
    color: #cccccc;
}

.instructions p {
    margin: 5px 0;
}
```

### 3. 核心類別實作 (src/core/Position.js)
```javascript
export class Position {
    constructor(x, y) {
        this.x = x;
        this.y = y;
    }

    equals(other) {
        return this.x === other.x && this.y === other.y;
    }

    add(other) {
        return new Position(this.x + other.x, this.y + other.y);
    }

    isValid(gridSize) {
        return this.x >= 0 && this.x < gridSize.width &&
               this.y >= 0 && this.y < gridSize.height;
    }

    static random(gridSize) {
        return new Position(
            Math.floor(Math.random() * gridSize.width),
            Math.floor(Math.random() * gridSize.height)
        );
    }

    static fromDirection(direction) {
        const directions = {
            UP: new Position(0, -1),
            DOWN: new Position(0, 1),
            LEFT: new Position(-1, 0),
            RIGHT: new Position(1, 0)
        };
        return directions[direction];
    }
}
```

### 4. 遊戲引擎 (src/core/GameEngine.js)
```javascript
import { Position } from './Position.js';
import { Snake } from './Snake.js';
import { Food } from './Food.js';

export class GameEngine {
    constructor(config) {
        this.config = config;
        this.score = 0;
        this.highScore = this.loadHighScore();
        this.level = 1;
        this.speed = config.initialSpeed;
        this.state = 'READY';
        this.snake = null;
        this.food = null;
        this.gameLoop = null;
        this.lastMoveTime = 0;
        this.eventListeners = {};
    }

    start() {
        if (this.state !== 'READY' && this.state !== 'GAME_OVER') {
            return;
        }

        this.initializeGame();
        this.state = 'RUNNING';
        this.lastMoveTime = Date.now();
        this.gameLoop = setInterval(() => this.update(), 16); // 60 FPS
        this.emit('gameStart', { score: this.score });
    }

    pause() {
        if (this.state !== 'RUNNING') {
            return;
        }

        this.state = 'PAUSED';
        clearInterval(this.gameLoop);
        this.emit('gamePause');
    }

    resume() {
        if (this.state !== 'PAUSED') {
            return;
        }

        this.state = 'RUNNING';
        this.lastMoveTime = Date.now();
        this.gameLoop = setInterval(() => this.update(), 16);
        this.emit('gameResume');
    }

    reset() {
        this.state = 'READY';
        this.score = 0;
        this.level = 1;
        this.speed = this.config.initialSpeed;
        clearInterval(this.gameLoop);
        this.emit('gameReset');
    }

    changeDirection(newDirection) {
        if (this.snake && this.state === 'RUNNING') {
            this.snake.changeDirection(newDirection);
        }
    }

    initializeGame() {
        const centerX = Math.floor(this.config.gridSize.width / 2);
        const centerY = Math.floor(this.config.gridSize.height / 2);
        const startPosition = new Position(centerX, centerY);
        
        this.snake = new Snake(startPosition, this.config.gridSize);
        this.food = new Food(this.config.gridSize);
        this.generateNewFood();
    }

    update() {
        if (this.state !== 'RUNNING') {
            return;
        }

        const currentTime = Date.now();
        if (currentTime - this.lastMoveTime >= this.speed) {
            this.moveSnake();
            this.lastMoveTime = currentTime;
        }
    }

    moveSnake() {
        this.snake.move();
        this.emit('snakeMove', { position: this.snake.head });

        // 檢查碰撞
        const collision = this.snake.checkCollision(this.config.gridSize);
        if (collision) {
            this.gameOver();
            return;
        }

        // 檢查是否吃到食物
        if (this.snake.head.equals(this.food.position)) {
            this.eatFood();
        }
    }

    eatFood() {
        this.snake.grow();
        this.score += this.food.value;
        this.updateLevel();
        this.generateNewFood();
        this.emit('foodEaten', { 
            position: this.food.position, 
            value: this.food.value,
            newScore: this.score 
        });
    }

    generateNewFood() {
        let newPosition;
        do {
            newPosition = Position.random(this.config.gridSize);
        } while (this.snake.contains(newPosition));
        
        this.food.position = newPosition;
    }

    updateLevel() {
        const newLevel = Math.floor(this.score / 50) + 1;
        if (newLevel > this.level) {
            this.level = newLevel;
            this.speed = Math.max(
                this.config.minSpeed,
                this.config.initialSpeed - (this.level - 1) * this.config.speedIncrement
            );
            this.emit('levelUp', { level: this.level, speed: this.speed });
        }
    }

    gameOver() {
        this.state = 'GAME_OVER';
        clearInterval(this.gameLoop);
        
        if (this.score > this.highScore) {
            this.highScore = this.score;
            this.saveHighScore();
        }
        
        this.emit('gameOver', { score: this.score, highScore: this.highScore });
    }

    // 事件系統
    on(event, callback) {
        if (!this.eventListeners[event]) {
            this.eventListeners[event] = [];
        }
        this.eventListeners[event].push(callback);
    }

    off(event, callback) {
        if (this.eventListeners[event]) {
            this.eventListeners[event] = this.eventListeners[event].filter(cb => cb !== callback);
        }
    }

    emit(event, data) {
        if (this.eventListeners[event]) {
            this.eventListeners[event].forEach(callback => callback(data));
        }
    }

    // 本地儲存
    loadHighScore() {
        return parseInt(localStorage.getItem('snakeGameHighScore') || '0');
    }

    saveHighScore() {
        localStorage.setItem('snakeGameHighScore', this.highScore.toString());
    }

    // Getters
    getState() { return this.state; }
    getScore() { return this.score; }
    getHighScore() { return this.highScore; }
    getLevel() { return this.level; }
    getSpeed() { return this.speed; }
}
```

## 測試設定

### Jest 配置 (jest.config.js)
```javascript
module.exports = {
  testEnvironment: 'jsdom',
  setupFilesAfterEnv: ['<rootDir>/tests/setup.js'],
  moduleNameMapping: {
    '^@/(.*)$': '<rootDir>/src/$1'
  },
  collectCoverageFrom: [
    'src/**/*.js',
    '!src/index.js'
  ],
  coverageThreshold: {
    global: {
      branches: 70,
      functions: 80,
      lines: 80,
      statements: 80
    }
  }
};
```

### 測試範例 (tests/core/Position.test.js)
```javascript
import { Position } from '../../src/core/Position.js';

describe('Position', () => {
  test('應該正確建立位置', () => {
    const pos = new Position(5, 10);
    expect(pos.x).toBe(5);
    expect(pos.y).toBe(10);
  });

  test('應該正確比較位置', () => {
    const pos1 = new Position(5, 10);
    const pos2 = new Position(5, 10);
    const pos3 = new Position(5, 11);
    
    expect(pos1.equals(pos2)).toBe(true);
    expect(pos1.equals(pos3)).toBe(false);
  });

  test('應該正確驗證位置有效性', () => {
    const gridSize = { width: 20, height: 20 };
    const validPos = new Position(10, 10);
    const invalidPos = new Position(25, 10);
    
    expect(validPos.isValid(gridSize)).toBe(true);
    expect(invalidPos.isValid(gridSize)).toBe(false);
  });
});
```

## 建置設定

### Webpack 配置 (webpack.config.js)
```javascript
const path = require('path');

module.exports = {
  entry: './src/index.js',
  output: {
    filename: 'bundle.js',
    path: path.resolve(__dirname, 'dist'),
    clean: true
  },
  devServer: {
    static: {
      directory: path.join(__dirname, 'public')
    },
    compress: true,
    port: 3000
  },
  module: {
    rules: [
      {
        test: /\.js$/,
        exclude: /node_modules/,
        use: {
          loader: 'babel-loader',
          options: {
            presets: ['@babel/preset-env']
          }
        }
      }
    ]
  }
};
```

## 執行指令

### 開發模式
```bash
# 啟動開發伺服器
npm run dev

# 執行測試
npm test

# 執行測試並生成覆蓋率報告
npm run test:coverage

# 程式碼檢查
npm run lint

# 程式碼格式化
npm run format
```

### 建置
```bash
# 建置生產版本
npm run build

# 預覽生產版本
npm run preview
```

## 下一步

1. **實作剩餘類別**: Snake, Food, GameRenderer, InputHandler
2. **撰寫測試**: 為所有類別撰寫單元測試
3. **整合測試**: 測試完整的遊戲流程
4. **效能優化**: 優化渲染和遊戲循環
5. **功能擴展**: 添加音效、動畫等增強功能

---

*快速開始指南版本: 1.0.0*  
*建立日期: 2025-01-17*  
*最後更新: 2025-01-17*
