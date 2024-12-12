[![en](https://img.shields.io/badge/lang-en-blue.svg)](https://github.com/RomaruDaze/fastAPI-Sqlite/blob/main/README.md) [![jp](https://img.shields.io/badge/lang-jp-red.svg)](https://github.com/RomaruDaze/fastAPI-Sqlite/blob/main/README.jp.md) [![cn](https://img.shields.io/badge/lang-cn-green.svg)](https://github.com/RomaruDaze/fastAPI-Sqlite/blob/main/README.cn.md) [![es](https://img.shields.io/badge/lang-es-yellow.svg)](https://github.com/RomaruDaze/fastAPI-Sqlite/blob/main/README.es.md)

# FastAPI-Sqlite

FastAPI-Sqlite を使用した REST API

> 🚀 **デモ : https://fastapi-sqlite-ejk7.onrender.com**
> 📖 **API ドキュメント : https://fastapi-sqlite-ejk7.onrender.com/docs**

# 作成方法

**ステップ 1：ローカルプロジェクトの作成**

```bash
# Create project directory and initialize
mkdir my-api
cd my-api
npm init -y

# Install necessary dependencies
npm install express pg dotenv cors
npm install nodemon --save-dev
```

**ステップ 2：基本的なファイル構造の作成**

```plaintext
my-api/
  ├── src/
  │   ├── config/
  │   │   └── database.js
  │   ├── routes/
  │   │   └── api.js
  │   └── index.js
  ├── .env
  ├── .gitignore
  └── package.json
```

**ステップ 3：.gitignore の設定**

```gitignore
node_modules
.env
```

**ステップ 4：データベース接続の設定 (src/config/database.js)**

```javascript:src/config/database.js
const { Pool } = require('pg');

const pool = new Pool({
  connectionString: process.env.DATABASE_URL,
  ssl: {
    rejectUnauthorized: false
  }
});

module.exports = pool;
```

**ステップ 5：メインサーバーファイルの作成 (src/index.js)**

```javascript:src/index.js
const express = require('express');
const cors = require('cors');
require('dotenv').config();

const apiRoutes = require('./routes/api');

const app = express();
const PORT = process.env.PORT || 3000;

app.use(cors());
app.use(express.json());

// Routes
app.use('/api', apiRoutes);

app.listen(PORT, () => {
  console.log(`Server running on port ${PORT}`);
});
```

**ステップ 6：API ルートの作成 (src/routes/api.js)**

```javascript:src/routes/api.js
const express = require('express');
const router = express.Router();
const db = require('../config/database');

// Get all items
router.get('/items', async (req, res) => {
  try {
    const result = await db.query('SELECT * FROM items');
    res.json(result.rows);
  } catch (err) {
    res.status(500).json({ error: err.message });
  }
});

// Add an item
router.post('/items', async (req, res) => {
  const { name, description } = req.body;
  try {
    const result = await db.query(
      'INSERT INTO items (name, description) VALUES ($1, $2) RETURNING *',
      [name, description]
    );
    res.json(result.rows[0]);
  } catch (err) {
    res.status(500).json({ error: err.message });
  }
});

module.exports = router;
```

**ステップ 7：package.json のスクリプトを更新**

```json:package.json
{
  "scripts": {
    "start": "node src/index.js",
    "dev": "nodemon src/index.js"
  }
}
```

**ステップ 8：Render でのデータベースのセットアップ**

1. render.com にアクセスしてアカウントを作成
2. 新しい PostgreSQL データベースを作成
3. 外部データベース URL をコピー
4. .env ファイルを作成：

```plaintext:.env
DATABASE_URL=your_postgres_database_url
PORT=3000
```

**ステップ 9：データベーステーブルの作成**
Render のデータベースダッシュボードまたは PostgreSQL クライアントを使用して実行：

```sql
CREATE TABLE items (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    description TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

**ステップ 10：Render へのデプロイ**

1. GitHub にコードをプッシュ
2. Render で：
   - 新しい"Web Service"を作成
   - あなたの GitHub リポジトリに接続
   - ビルド設定を構成：
     ```plaintext
     Build Command: npm install
     Start Command: npm start
     ```
   - .env から環境変数を追加
   - デプロイ！

**API のテスト：**

```bash
# Local testing
curl http://localhost:3000/api/items  # GET items
curl -X POST -H "Content-Type: application/json" -d '{"name":"test","description":"test desc"}' http://localhost:3000/api/items  # POST item
```

**追加のヒント：**

1. エラー処理の追加
2. 入力バリデーションの実装
3. 必要に応じて認証を追加
4. より複雑なアプリケーションの場合は、Sequelize や Prisma などの ORM を使用
5. 本番環境モニタリング用のログ記録を追加

これは基本的なセットアップで、これを基に構築することができます。手順について不明な点がある場合や、機能を追加したい場合はお知らせください！
