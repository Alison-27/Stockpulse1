# StockPulse Pro 🚀

台股智慧追蹤系統 — 完整 Node.js 網站

## 功能

- 📊 資產配置圓餅圖（可自訂持倉）
- 🏦 三大法人買賣超（TWSE + FinMind）
- 📉 融資融券變化
- 🔥 強勢股排行
- 🐋 大戶持股追蹤
- 📦 主動ETF追蹤
- 📈 主動買賣統計
- ⏱ 自動更新（30秒 / 1分 / 5分 / 15分）
- 🌐 伺服器端 API Proxy（繞過 CORS）

---

## 快速啟動

### 1. 安裝相依套件

```bash
npm install
```

### 2. 設定環境變數

```bash
cp .env.example .env
```

編輯 `.env`，填入以下設定：

```env
PORT=3000
FINMIND_TOKEN=你的FinMind_Token   # https://finmindtrade.com 免費申請
```

### 3. 啟動伺服器

```bash
# 正式模式
npm start

# 開發模式（自動重啟）
npm run dev
```

開啟瀏覽器：[http://localhost:3000](http://localhost:3000)

---

## API 端點

| 方法 | 路徑 | 說明 |
|------|------|------|
| GET  | `/api/health`           | 伺服器健康檢查 |
| GET  | `/api/prices`           | 全市場台股行情（TWSE）|
| GET  | `/api/quotes?s=AAPL,NVDA` | 美股即時報價（Yahoo）|
| GET  | `/api/chart/:symbol`    | 個股歷史K線 |
| GET  | `/api/search?q=台積電`  | 股票搜尋 |
| GET  | `/api/institution`      | 三大法人彙總 |
| GET  | `/api/institution/:id`  | 個股三大法人（需FinMind Token）|
| GET  | `/api/margin`           | 融資融券彙總 |
| GET  | `/api/margin/:id`       | 個股資券（需FinMind Token）|
| GET  | `/api/holding/:id`      | 大戶持股分級（需FinMind Token）|
| GET  | `/api/etf`              | ETF 資訊 |
| POST | `/api/finmind/test`     | 測試 FinMind 連線 |
| POST | `/api/twse/test`        | 測試 TWSE 連線 |
| POST | `/api/yahoo/test`       | 測試 Yahoo 連線 |

---

## 部署

### Render.com（免費）

1. Push 到 GitHub
2. 至 [render.com](https://render.com) 新增 Web Service
3. Build Command: `npm install`
4. Start Command: `npm start`
5. 環境變數：加入 `FINMIND_TOKEN`

### Railway.app

```bash
railway init
railway up
railway variables set FINMIND_TOKEN=你的token
```

### VPS / 自架（Ubuntu）

```bash
# 安裝 Node.js 20
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
sudo apt-get install -y nodejs

# 安裝 PM2 守護程序
npm install -g pm2

# 啟動
pm2 start src/server.js --name stockpulse
pm2 save
pm2 startup
```

### Nginx 反向代理

```nginx
server {
    listen 80;
    server_name yourdomain.com;

    location / {
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}
```

---

## 資料來源

| 來源 | 資料類型 | 費用 |
|------|----------|------|
| [TWSE 公開資料](https://openapi.twse.com.tw) | 台股行情、三大法人、資券 | 免費 |
| [FinMind](https://finmindtrade.com) | 台股完整歷史、籌碼、大戶 | 免費方案可用 |
| [Yahoo Finance](https://finance.yahoo.com) | 美股即時報價、K線 | 免費 |

---

## 專案結構

```
stockpulse/
├── src/
│   ├── server.js          # Express 主程式
│   ├── routes/
│   │   └── api.js         # 所有 API 路由
│   ├── api/
│   │   ├── finmind.js     # FinMind 客戶端
│   │   ├── twse.js        # TWSE 客戶端
│   │   └── yahoo.js       # Yahoo Finance 客戶端
│   └── cache/
│       └── index.js       # TTL 快取
├── public/
│   ├── index.html         # 前端 SPA
│   └── js/
│       └── app.js         # 前端邏輯
├── .env.example           # 環境變數範本
├── package.json
└── README.md
```

---

> 本系統資料僅供參考，不構成投資建議。
