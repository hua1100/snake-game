# 貪吃蛇遊戲速度說明

## 速度等級的意義

遊戲中顯示的**速度等級**是一個直觀的數字，代表遊戲的速度快慢。

### 重要概念
- **數字越大 = 蛇移動越快**（更直觀！）
- **數字越小 = 蛇移動越慢**

### 範例
- `速度等級 1` - 最慢（初始速度，300ms 間隔）
- `速度等級 5` - 中等速度（200ms 間隔）
- `速度等級 10` - 非常快（75ms 間隔，最快速度）

### 技術細節
速度等級背後對應的是蛇每次移動的間隔時間（毫秒）：
- 速度等級 1 = 300ms（蛇每 0.3 秒移動一格）
- 速度等級 5 = 200ms（蛇每 0.2 秒移動一格）
- 速度等級 10 = 75ms（蛇每 0.075 秒移動一格）

## 當前遊戲設定

在 `src/index.js` 中的配置：

```javascript
const config = new GameConfig({
  initialSpeed: 300,      // 初始速度：300ms（起始速度，較慢更容易上手）
  speedIncrement: 25,     // 速度增量：每升一級減少 25ms
  minSpeed: 75,          // 最小速度：75ms（最快速度的上限）
  // ...
});
```

## 速度變化規則

### 升級機制
- 每吃 **5 個食物**升一級
- 公式：`等級 = Math.floor(吃掉的食物數 / 5) + 1`

### 速度計算公式
```javascript
移動間隔(ms) = Math.max(
  minSpeed,  // 最小速度限制（75ms）
  initialSpeed - (等級 - 1) × speedIncrement
)

速度等級 = Math.floor((initialSpeed - 移動間隔) / speedIncrement) + 1
```

### 各等級速度對照表

| 遊戲等級 | 食物數 | 移動間隔 | 速度等級 | 說明 |
|---------|--------|----------|----------|------|
| 1 | 0-4 | 300ms | 1 | 初始速度（慢） |
| 2 | 5-9 | 275ms | 2 | 稍快 |
| 3 | 10-14 | 250ms | 3 | 中等偏慢 |
| 4 | 15-19 | 225ms | 4 | 中等 |
| 5 | 20-24 | 200ms | 5 | 中等偏快 |
| 6 | 25-29 | 175ms | 6 | 快 |
| 7 | 30-34 | 150ms | 7 | 很快 |
| 8 | 35-39 | 125ms | 8 | 非常快 |
| 9 | 40-44 | 100ms | 9 | 極快 |
| 10+ | 45+ | 75ms | 10 | 最快速度 |

## 如何調整遊戲難度

### 1. 調整初始速度
```javascript
initialSpeed: 200,  // 改大 = 起始更慢，改小 = 起始更快
```

### 2. 調整速度增量
```javascript
speedIncrement: 20,  // 改大 = 升級時加速更明顯，改小 = 加速更平緩
```

### 3. 調整最快速度
```javascript
minSpeed: 50,  // 改大 = 最快速度變慢，改小 = 可以更快
```

### 4. 調整升級頻率
在 `GameEngine.js` 的 `updateLevel()` 方法中：
```javascript
const newLevel = Math.floor(this.foodEatenCount / 5) + 1;
//                                              ↑
//                                    改這個數字調整升級頻率
//                                    改大 = 升級更慢，改小 = 升級更快
```

## 難度預設方案

### 簡單模式
```javascript
initialSpeed: 350,
speedIncrement: 20,
minSpeed: 100,
升級頻率: 每 7 個食物
```

### 普通模式（當前）
```javascript
initialSpeed: 300,
speedIncrement: 25,
minSpeed: 75,
升級頻率: 每 5 個食物
```

### 困難模式
```javascript
initialSpeed: 250,
speedIncrement: 30,
minSpeed: 50,
升級頻率: 每 3 個食物
```

## 技術細節

遊戲循環使用 `requestAnimationFrame`，在每一幀檢查是否達到移動間隔：

```javascript
if (currentTime - this.lastMoveTime >= this.speed) {
  this.update();  // 移動蛇
  this.lastMoveTime = currentTime;
}
```

這確保了遊戲速度的精確控制和流暢的動畫效果。
