# 貪吃蛇遊戲資料模型

## 概述
本文件定義了貪吃蛇遊戲的資料模型，包括實體結構、關係和驗證規則。

## 核心實體

### 1. Snake (蛇)

**描述**: 代表遊戲中的蛇，包含頭部和身體節點

**屬性**:
```typescript
interface Snake {
  head: Position;           // 蛇頭位置
  body: Position[];         // 身體節點位置陣列
  direction: Direction;     // 當前移動方向
  length: number;          // 蛇的長度
}
```

**驗證規則**:
- `head` 不能為空
- `body` 陣列不能包含重複位置
- `direction` 必須是有效的方向值
- `length` 必須等於 `body.length + 1`

**狀態轉換**:
- 移動: 頭部移動到新位置，身體跟隨
- 成長: 身體增加一個節點
- 碰撞: 轉換到遊戲結束狀態

### 2. Food (食物)

**描述**: 代表遊戲中的食物，蛇的目標

**屬性**:
```typescript
interface Food {
  position: Position;       // 食物位置
  value: number;           // 食物分數值
  type: FoodType;          // 食物類型
}
```

**驗證規則**:
- `position` 必須在遊戲區域內
- `position` 不能與蛇身重疊
- `value` 必須大於 0
- `type` 必須是有效的食物類型

**狀態轉換**:
- 生成: 在隨機位置創建新食物
- 被吃: 食物消失，觸發分數增加

### 3. Game (遊戲)

**描述**: 代表遊戲的整體狀態

**屬性**:
```typescript
interface Game {
  score: number;           // 當前分數
  highScore: number;       // 最高分數
  level: number;           // 當前等級
  speed: number;           // 遊戲速度 (ms)
  state: GameState;        // 遊戲狀態
  gridSize: GridSize;      // 網格大小
  startTime: Date;         // 遊戲開始時間
  endTime?: Date;          // 遊戲結束時間
}
```

**驗證規則**:
- `score` 必須 ≥ 0
- `highScore` 必須 ≥ `score`
- `level` 必須 ≥ 1
- `speed` 必須在有效範圍內 (50-500ms)
- `state` 必須是有效的遊戲狀態
- `gridSize` 必須是有效的網格大小

**狀態轉換**:
- 開始: `state` → `RUNNING`
- 暫停: `state` → `PAUSED`
- 結束: `state` → `GAME_OVER`
- 重置: 所有屬性回到初始值

### 4. Position (位置)

**描述**: 代表二維座標位置

**屬性**:
```typescript
interface Position {
  x: number;              // X 座標
  y: number;              // Y 座標
}
```

**驗證規則**:
- `x` 必須在 0 到 `gridSize.width - 1` 範圍內
- `y` 必須在 0 到 `gridSize.height - 1` 範圍內
- 座標必須是整數

### 5. GridSize (網格大小)

**描述**: 定義遊戲區域的大小

**屬性**:
```typescript
interface GridSize {
  width: number;          // 網格寬度
  height: number;         // 網格高度
}
```

**驗證規則**:
- `width` 必須 ≥ 10 且 ≤ 50
- `height` 必須 ≥ 10 且 ≤ 50
- 預設值: 20x20

## 枚舉類型

### Direction (方向)
```typescript
enum Direction {
  UP = 'UP',
  DOWN = 'DOWN',
  LEFT = 'LEFT',
  RIGHT = 'RIGHT'
}
```

### GameState (遊戲狀態)
```typescript
enum GameState {
  READY = 'READY',         // 準備開始
  RUNNING = 'RUNNING',     // 運行中
  PAUSED = 'PAUSED',       // 暫停
  GAME_OVER = 'GAME_OVER', // 遊戲結束
  ERROR = 'ERROR'          // 錯誤狀態
}
```

### FoodType (食物類型)
```typescript
enum FoodType {
  NORMAL = 'NORMAL',       // 普通食物
  SPECIAL = 'SPECIAL'      // 特殊食物 (未來擴展)
}
```

## 資料關係

### 一對一關係
- `Game` ↔ `Snake`: 一個遊戲有一個蛇
- `Game` ↔ `Food`: 一個遊戲有一個食物

### 一對多關係
- `Snake` → `Position[]`: 一個蛇有多個身體節點位置

## 業務規則

### 蛇的移動規則
1. 蛇頭根據當前方向移動一格
2. 身體節點跟隨前一個節點的位置
3. 蛇不能反向移動（避免立即死亡）
4. 蛇的移動是連續的，不能停止

### 碰撞檢測規則
1. 蛇頭與身體節點碰撞：座標完全重疊
2. 蛇頭與邊界碰撞：座標超出遊戲區域
3. 蛇頭與食物碰撞：座標完全重疊

### 分數計算規則
1. 每吃一個普通食物得 10 分
2. 分數累積，不重置
3. 遊戲結束時更新最高分數

### 速度調整規則
1. 初始速度：200ms 移動一格
2. 每吃 5 個食物，速度增加 20ms
3. 最小速度：50ms 移動一格
4. 速度變化平滑，避免突然跳躍

## 資料驗證

### 輸入驗證
- 所有數值必須在有效範圍內
- 字串必須符合預定義的枚舉值
- 陣列不能包含無效元素

### 狀態一致性
- 蛇的長度必須與身體節點數量一致
- 食物位置不能與蛇身重疊
- 遊戲狀態轉換必須符合預定義規則

### 邊界檢查
- 所有位置座標必須在網格範圍內
- 分數不能為負數
- 速度值必須在合理範圍內

## 資料持久化

### 本地儲存
- 最高分數儲存在 localStorage
- 遊戲設定儲存在 localStorage
- 不儲存遊戲進度（每次重新開始）

### 資料格式
```json
{
  "highScore": 150,
  "settings": {
    "gridSize": { "width": 20, "height": 20 },
    "initialSpeed": 200,
    "soundEnabled": true
  }
}
```

## 錯誤處理

### 資料錯誤
- 無效的座標值：重置到預設位置
- 無效的遊戲狀態：轉換到錯誤狀態
- 資料不一致：重新初始化相關實體

### 恢復機制
- 提供資料重置功能
- 記錄錯誤日誌供除錯
- 顯示友善的錯誤訊息

---

*資料模型版本: 1.0.0*  
*建立日期: 2025-01-17*  
*最後更新: 2025-01-17*
