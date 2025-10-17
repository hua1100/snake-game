# 貪吃蛇遊戲 API 合約

## 概述
本文件定義了貪吃蛇遊戲的 API 合約，包括類別介面、方法和事件。

## 核心類別介面

### GameEngine (遊戲引擎)

**描述**: 遊戲的核心引擎，負責遊戲邏輯和狀態管理

```typescript
interface GameEngine {
  // 遊戲控制
  start(): void;
  pause(): void;
  resume(): void;
  reset(): void;
  stop(): void;
  
  // 狀態查詢
  getState(): GameState;
  getScore(): number;
  getHighScore(): number;
  getLevel(): number;
  getSpeed(): number;
  
  // 事件監聽
  on(event: GameEvent, callback: EventCallback): void;
  off(event: GameEvent, callback: EventCallback): void;
  
  // 設定
  setSpeed(speed: number): void;
  setGridSize(size: GridSize): void;
}
```

### Snake (蛇)

**描述**: 代表遊戲中的蛇

```typescript
interface Snake {
  // 屬性
  readonly head: Position;
  readonly body: Position[];
  readonly direction: Direction;
  readonly length: number;
  
  // 方法
  move(): void;
  changeDirection(newDirection: Direction): boolean;
  grow(): void;
  checkCollision(): CollisionType;
  getNextPosition(): Position;
  
  // 狀態查詢
  isAt(position: Position): boolean;
  contains(position: Position): boolean;
}
```

### Food (食物)

**描述**: 代表遊戲中的食物

```typescript
interface Food {
  // 屬性
  readonly position: Position;
  readonly value: number;
  readonly type: FoodType;
  
  // 方法
  generateNewPosition(snake: Snake, gridSize: GridSize): void;
  isEatenBy(snake: Snake): boolean;
  
  // 狀態查詢
  isAt(position: Position): boolean;
}
```

### GameRenderer (遊戲渲染器)

**描述**: 負責遊戲畫面的渲染

```typescript
interface GameRenderer {
  // 渲染控制
  render(gameState: GameState): void;
  clear(): void;
  update(): void;
  
  // 繪製方法
  drawSnake(snake: Snake): void;
  drawFood(food: Food): void;
  drawGrid(): void;
  drawUI(game: Game): void;
  drawGameOver(score: number, highScore: number): void;
  
  // 設定
  setCanvas(canvas: HTMLCanvasElement): void;
  setGridSize(size: GridSize): void;
}
```

### InputHandler (輸入處理器)

**描述**: 處理使用者輸入

```typescript
interface InputHandler {
  // 事件監聽
  startListening(): void;
  stopListening(): void;
  
  // 輸入處理
  handleKeyPress(key: string): void;
  handleDirectionInput(direction: Direction): void;
  handleGameControlInput(action: GameAction): void;
  
  // 設定
  setKeyBindings(bindings: KeyBindings): void;
}
```

## 事件系統

### GameEvent (遊戲事件)

```typescript
enum GameEvent {
  GAME_START = 'gameStart',
  GAME_PAUSE = 'gamePause',
  GAME_RESUME = 'gameResume',
  GAME_OVER = 'gameOver',
  GAME_RESET = 'gameReset',
  SCORE_UPDATE = 'scoreUpdate',
  LEVEL_UP = 'levelUp',
  SNAKE_MOVE = 'snakeMove',
  FOOD_EATEN = 'foodEaten',
  COLLISION = 'collision',
  ERROR = 'error'
}
```

### EventCallback (事件回調)

```typescript
type EventCallback = (data?: any) => void;
```

### 事件資料結構

```typescript
interface GameStartEvent {
  timestamp: Date;
  initialScore: number;
}

interface ScoreUpdateEvent {
  newScore: number;
  previousScore: number;
  pointsGained: number;
}

interface CollisionEvent {
  type: CollisionType;
  position: Position;
  timestamp: Date;
}

interface FoodEatenEvent {
  foodPosition: Position;
  foodValue: number;
  newScore: number;
}
```

## 工具類別

### Position (位置)

```typescript
class Position {
  constructor(public x: number, public y: number) {}
  
  // 方法
  equals(other: Position): boolean;
  add(other: Position): Position;
  subtract(other: Position): Position;
  distance(other: Position): number;
  isValid(gridSize: GridSize): boolean;
  
  // 靜態方法
  static random(gridSize: GridSize): Position;
  static fromDirection(direction: Direction): Position;
}
```

### GridSize (網格大小)

```typescript
class GridSize {
  constructor(public width: number, public height: number) {}
  
  // 方法
  contains(position: Position): boolean;
  getRandomPosition(): Position;
  getCenterPosition(): Position;
  
  // 驗證
  isValid(): boolean;
}
```

### GameConfig (遊戲配置)

```typescript
interface GameConfig {
  gridSize: GridSize;
  initialSpeed: number;
  speedIncrement: number;
  minSpeed: number;
  foodValue: number;
  keyBindings: KeyBindings;
  renderSettings: RenderSettings;
}
```

### KeyBindings (按鍵綁定)

```typescript
interface KeyBindings {
  up: string;
  down: string;
  left: string;
  right: string;
  pause: string;
  restart: string;
}
```

### RenderSettings (渲染設定)

```typescript
interface RenderSettings {
  snakeColor: string;
  foodColor: string;
  gridColor: string;
  backgroundColor: string;
  cellSize: number;
  showGrid: boolean;
}
```

## 錯誤處理

### GameError (遊戲錯誤)

```typescript
class GameError extends Error {
  constructor(
    message: string,
    public code: ErrorCode,
    public context?: any
  ) {
    super(message);
    this.name = 'GameError';
  }
}
```

### ErrorCode (錯誤代碼)

```typescript
enum ErrorCode {
  INVALID_POSITION = 'INVALID_POSITION',
  INVALID_DIRECTION = 'INVALID_DIRECTION',
  INVALID_STATE = 'INVALID_STATE',
  RENDER_ERROR = 'RENDER_ERROR',
  INPUT_ERROR = 'INPUT_ERROR',
  CONFIG_ERROR = 'CONFIG_ERROR'
}
```

## 工廠模式

### GameFactory (遊戲工廠)

```typescript
class GameFactory {
  static createGame(config: GameConfig): GameEngine;
  static createSnake(startPosition: Position): Snake;
  static createFood(position: Position): Food;
  static createRenderer(canvas: HTMLCanvasElement): GameRenderer;
  static createInputHandler(): InputHandler;
}
```

## 使用範例

### 基本遊戲初始化

```typescript
// 建立遊戲配置
const config: GameConfig = {
  gridSize: new GridSize(20, 20),
  initialSpeed: 200,
  speedIncrement: 20,
  minSpeed: 50,
  foodValue: 10,
  keyBindings: {
    up: 'ArrowUp',
    down: 'ArrowDown',
    left: 'ArrowLeft',
    right: 'ArrowRight',
    pause: 'Space',
    restart: 'r'
  },
  renderSettings: {
    snakeColor: '#00ff00',
    foodColor: '#ff0000',
    gridColor: '#333333',
    backgroundColor: '#000000',
    cellSize: 20,
    showGrid: true
  }
};

// 建立遊戲實例
const game = GameFactory.createGame(config);
const canvas = document.getElementById('gameCanvas') as HTMLCanvasElement;
const renderer = GameFactory.createRenderer(canvas);

// 設定事件監聽
game.on(GameEvent.SCORE_UPDATE, (data: ScoreUpdateEvent) => {
  console.log(`分數更新: ${data.newScore}`);
});

game.on(GameEvent.GAME_OVER, () => {
  console.log('遊戲結束');
});

// 開始遊戲
game.start();
```

### 自定義輸入處理

```typescript
const inputHandler = GameFactory.createInputHandler();

inputHandler.on('direction', (direction: Direction) => {
  game.changeDirection(direction);
});

inputHandler.on('pause', () => {
  if (game.getState() === GameState.RUNNING) {
    game.pause();
  } else if (game.getState() === GameState.PAUSED) {
    game.resume();
  }
});
```

---

*API 合約版本: 1.0.0*  
*建立日期: 2025-01-17*  
*最後更新: 2025-01-17*
