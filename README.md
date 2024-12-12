[![en](https://img.shields.io/badge/lang-en-blue.svg)](https://github.com/RomaruDaze/fastAPI-Sqlite/blob/main/README.md) [![jp](https://img.shields.io/badge/lang-jp-red.svg)](https://github.com/RomaruDaze/fastAPI-Sqlite/blob/main/README.jp.md) [![cn](https://img.shields.io/badge/lang-cn-green.svg)](https://github.com/RomaruDaze/fastAPI-Sqlite/blob/main/README.cn.md) [![es](https://img.shields.io/badge/lang-es-yellow.svg)](https://github.com/RomaruDaze/fastAPI-Sqlite/blob/main/README.es.md)

# FastAPI-Sqlite

REST API with FastAPI-Sqlite

> 🚀 **Online Demo : https://fastapi-sqlite-ejk7.onrender.com**

> 📖 **API Docs : https://fastapi-sqlite-ejk7.onrender.com/docs**

# 如何创建

**Step 1: Create Local Project**

```bash
# Create project directory and initialize
mkdir my-api
cd my-api
npm init -y

# Install necessary dependencies
npm install express pg dotenv cors
npm install nodemon --save-dev
```

**Step 2: Create Basic File Structure**

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

**Step 3: Set Up .gitignore**

```gitignore
node_modules
.env
```

**Step 4: Configure Database Connection (src/config/database.js)**

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

**Step 5: Create Main Server File (src/index.js)**

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

**Step 6: Create API Routes (src/routes/api.js)**

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

**Step 7: Update package.json Scripts**

```json:package.json
{
  "scripts": {
    "start": "node src/index.js",
    "dev": "nodemon src/index.js"
  }
}
```

**Step 8: Set Up Database on Render**

1. Go to render.com and create account
2. Create new PostgreSQL database
3. Copy the external database URL
4. Create `.env` file:

```plaintext:.env
DATABASE_URL=your_postgres_database_url
PORT=3000
```

**Step 9: Create Database Table**
Use Render's database dashboard or a PostgreSQL client to run:

```sql
CREATE TABLE items (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    description TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

**Step 10: Deploy to Render**

1. Push code to GitHub
2. On Render:
   - Create new "Web Service"
   - Connect to your GitHub repo
   - Configure build settings:
     ```plaintext
     Build Command: npm install
     Start Command: npm start
     ```
   - Add environment variables from .env
   - Deploy!

**Testing Your API:**

```bash
# Local testing
curl http://localhost:3000/api/items  # GET items
curl -X POST -H "Content-Type: application/json" -d '{"name":"test","description":"test desc"}' http://localhost:3000/api/items  # POST item
```

**Additional Tips:**

1. Add error handling
2. Implement input validation
3. Add authentication if needed
4. Use an ORM like Sequelize or Prisma for more complex applications
5. Add logging for production monitoring

This is a basic setup that you can build upon. Let me know if you need clarification on any step or want to add more features!
