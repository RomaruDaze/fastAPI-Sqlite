[![en](https://img.shields.io/badge/lang-en-blue.svg)](https://github.com/RomaruDaze/fastAPI-Sqlite/blob/main/README.md) [![jp](https://img.shields.io/badge/lang-jp-red.svg)](https://github.com/RomaruDaze/fastAPI-Sqlite/blob/main/README.jp.md) [![cn](https://img.shields.io/badge/lang-cn-green.svg)](https://github.com/RomaruDaze/fastAPI-Sqlite/blob/main/README.cn.md) [![es](https://img.shields.io/badge/lang-es-yellow.svg)](https://github.com/RomaruDaze/fastAPI-Sqlite/blob/main/README.es.md)

# fastAPI-Sqlite

使用 FastAPI-Sqlite 的 REST API

# 如何创建

**步骤 1：创建本地项目**

```bash
# Create project directory and initialize
mkdir my-api
cd my-api
npm init -y

# Install necessary dependencies
npm install express pg dotenv cors
npm install nodemon --save-dev
```

**步骤 2：创建基本文件结构**

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

**步骤 3：设置 .gitignore**

```gitignore
node_modules
.env
```

**步骤 4：配置数据库连接 (src/config/database.js)**

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

**步骤 5：创建主服务器文件 (src/index.js)**

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

**步骤 6：创建 API 路由 (src/routes/api.js)**

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

**步骤 7：更新 package.json 脚本**

```json:package.json
{
  "scripts": {
    "start": "node src/index.js",
    "dev": "nodemon src/index.js"
  }
}
```

**步骤 8：在 Render 上设置数据库**

1. 前往 render.com 并创建帐户
2. 创建新的 PostgreSQL 数据库
3. 复制外部数据库 URL
4. 创建 .env 文件：

```plaintext:.env
DATABASE_URL=your_postgres_database_url
PORT=3000
```

**步骤 9：创建数据库表**
使用 Render 的数据库仪表板或 PostgreSQL 客户端运行：

```sql
CREATE TABLE items (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    description TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

**步骤 10：部署到 Render**

1. 将代码推送到 GitHub
2. 在 Render：
   - 创建新的 "Web Service"
   - 连接到您的 GitHub 仓库
   - 配置构建设置：
     ```plaintext
     Build Command: npm install
     Start Command: npm start
     ```
   - 从 .env 添加环境变量
   - 部署！

**测试您的 API：**

```bash
# Local testing
curl http://localhost:3000/api/items  # GET items
curl -X POST -H "Content-Type: application/json" -d '{"name":"test","description":"test desc"}' http://localhost:3000/api/items  # POST item
```

**附加提示：**

1. 添加错误处理
2. 实现输入验证
3. 根据需要添加身份验证
4. 对于更复杂的应用程序，使用像 Sequelize 或 Prisma 这样的 ORM
5. 添加日志记录以进行生产监控

这是一个基本设置，您可以在此基础上进行构建。如果您需要对任何步骤进行说明或想要添加更多功能，请告诉我！
