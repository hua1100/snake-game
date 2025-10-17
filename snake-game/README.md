# 貪吃蛇遊戲

一個使用 HTML5 Canvas 和 JavaScript 開發的經典貪吃蛇遊戲，採用模組化架構設計，遵循 TDD 和 BDD 開發原則。

## 🎮 遊戲特色

- **經典玩法**: 重現經典貪吃蛇遊戲體驗
- **流暢動畫**: 60 FPS 流暢遊戲體驗
- **響應式設計**: 支援各種螢幕尺寸
- **現代化 UI**: 美觀的使用者介面
- **跨瀏覽器**: 支援所有現代瀏覽器
- **無需安裝**: 直接在瀏覽器中遊玩

## 🚀 快速開始

### 線上遊玩

直接點擊 [這裡](https://your-domain.com) 開始遊玩！

### 本地運行

1. **克隆專案**
   ```bash
   git clone https://github.com/your-username/snake-game.git
   cd snake-game
   ```

2. **安裝依賴**
   ```bash
   npm install
   ```

3. **啟動開發伺服器**
   ```bash
   npm run dev
   ```

4. **開啟瀏覽器**
   訪問 `http://localhost:3000` 開始遊玩

## 🎯 遊戲目標

控制蛇移動並吃食物來成長，避免撞到牆壁或自己的身體，挑戰最高分數！

## 🕹️ 操作說明

| 按鍵 | 功能 |
|------|------|
| ↑ 方向鍵 | 向上移動 |
| ↓ 方向鍵 | 向下移動 |
| ← 方向鍵 | 向左移動 |
| → 方向鍵 | 向右移動 |
| 空白鍵 | 暫停/繼續遊戲 |
| R 鍵 | 重新開始遊戲 |

## 🏗️ 技術架構

### 技術堆疊

- **前端**: HTML5 Canvas + JavaScript (ES6+)
- **測試框架**: Jest + Testing Library
- **建置工具**: Webpack 5
- **程式碼品質**: ESLint + Prettier
- **版本控制**: Git

### 專案結構

```
snake-game/
├── src/                    # 原始碼
│   ├── core/              # 核心遊戲邏輯
│   ├── render/            # 渲染系統
│   ├── input/             # 輸入處理
│   ├── utils/             # 工具類別
│   ├── Game.js            # 主遊戲類別
│   └── index.js           # 入口檔案
├── tests/                 # 測試檔案
├── public/                # 靜態資源
├── docs/                  # 文檔
└── dist/                  # 建置輸出
```

## 🧪 測試

### 執行測試

```bash
# 執行所有測試
npm test

# 執行測試並生成覆蓋率報告
npm run test:coverage

# 監聽模式執行測試
npm run test:watch
```

### 測試覆蓋率

- 程式碼覆蓋率: ≥ 80%
- 分支覆蓋率: ≥ 70%
- 功能覆蓋率: 100%

## 🔧 開發

### 開發指令

```bash
# 啟動開發伺服器
npm run dev

# 建置生產版本
npm run build

# 程式碼檢查
npm run lint

# 程式碼格式化
npm run format
```

### 程式碼規範

- 使用 ESLint 進行程式碼檢查
- 使用 Prettier 進行程式碼格式化
- 遵循 ES6+ 語法規範
- 撰寫清晰的註解

## 📚 文檔

- [使用者手冊](docs/user-manual.md) - 遊戲操作和技巧指南
- [開發者指南](docs/developer-guide.md) - 開發環境設定和架構說明
- [API 參考](docs/api-reference.md) - 完整的 API 文檔
- [部署指南](docs/deployment-guide.md) - 部署到各種平台的說明
- [維護手冊](docs/maintenance-guide.md) - 系統維護和故障排除

## 🌟 功能特色

### 核心功能

- ✅ 蛇的移動和方向控制
- ✅ 食物生成和碰撞檢測
- ✅ 分數計算和等級系統
- ✅ 遊戲狀態管理
- ✅ 暫停和重新開始功能

### 進階功能

- ✅ 響應式使用者介面
- ✅ 本地最高分數儲存
- ✅ 跨瀏覽器相容性
- ✅ 效能優化
- ✅ 錯誤處理機制

### 技術特色

- ✅ 模組化架構設計
- ✅ 事件驅動系統
- ✅ 物件池模式
- ✅ 效能監控
- ✅ 完整的測試覆蓋

## 🎨 自定義

### 遊戲配置

```javascript
const config = new GameConfig({
  gridSize: new GridSize(20, 20),        // 遊戲區域大小
  initialSpeed: 200,                     // 初始速度 (ms)
  speedIncrement: 20,                    // 速度增量
  minSpeed: 50,                          // 最小速度
  foodValue: 10,                         // 食物分數
  renderSettings: {
    snakeColor: '#00ff00',               // 蛇的顏色
    foodColor: '#ff0000',                // 食物的顏色
    gridColor: '#333333',                // 網格顏色
    backgroundColor: '#000000',          // 背景顏色
    cellSize: 20,                        // 格子大小
    showGrid: true                       // 是否顯示網格
  }
});
```

### 按鍵綁定

```javascript
const keyBindings = {
  up: 'ArrowUp',      // 向上
  down: 'ArrowDown',  // 向下
  left: 'ArrowLeft',  // 向左
  right: 'ArrowRight', // 向右
  pause: 'Space',     // 暫停
  restart: 'r'        // 重新開始
};
```

## 🚀 部署

### 靜態託管

```bash
# 建置專案
npm run build

# 將 dist/ 目錄內容上傳到靜態主機
```

### 支援的平台

- GitHub Pages
- Netlify
- Vercel
- AWS S3 + CloudFront
- 傳統 Web 伺服器

## 🤝 貢獻

歡迎貢獻程式碼！請遵循以下步驟：

1. Fork 專案
2. 建立功能分支 (`git checkout -b feature/AmazingFeature`)
3. 提交變更 (`git commit -m 'Add some AmazingFeature'`)
4. 推送到分支 (`git push origin feature/AmazingFeature`)
5. 開啟 Pull Request

### 貢獻指南

- 遵循現有的程式碼風格
- 撰寫清晰的提交訊息
- 添加適當的測試
- 更新相關文檔

## 📄 授權

本專案採用 MIT 授權條款 - 查看 [LICENSE](LICENSE) 檔案了解詳情。

## 🙏 致謝

- 感謝所有貢獻者的努力
- 感謝開源社群的支援
- 感謝使用者的回饋和建議

## 📞 聯絡

- 問題回報: [GitHub Issues](https://github.com/your-username/snake-game/issues)
- 功能建議: [GitHub Discussions](https://github.com/your-username/snake-game/discussions)
- 電子郵件: your-email@example.com

## 📈 專案統計

![GitHub stars](https://img.shields.io/github/stars/your-username/snake-game?style=social)
![GitHub forks](https://img.shields.io/github/forks/your-username/snake-game?style=social)
![GitHub issues](https://img.shields.io/github/issues/your-username/snake-game)
![GitHub license](https://img.shields.io/github/license/your-username/snake-game)

---

**享受遊戲！** 🐍✨

*最後更新: 2025-01-17*
