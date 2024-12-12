[![en](https://img.shields.io/badge/lang-en-blue.svg)](https://github.com/RomaruDaze/fastAPI-Sqlite/blob/main/README.md) [![jp](https://img.shields.io/badge/lang-jp-red.svg)](https://github.com/RomaruDaze/fastAPI-Sqlite/blob/main/README.jp.md) [![cn](https://img.shields.io/badge/lang-cn-green.svg)](https://github.com/RomaruDaze/fastAPI-Sqlite/blob/main/README.cn.md) [![es](https://img.shields.io/badge/lang-es-yellow.svg)](https://github.com/RomaruDaze/fastAPI-Sqlite/blob/main/README.es.md)

# FastAPI-Sqlite

API REST con FastAPI-Sqlite

> 🚀 **Demostración en línea : https://fastapi-sqlite-ejk7.onrender.com**
> 📖 **Documentación API : https://fastapi-sqlite-ejk7.onrender.com/docs**

# Cómo crear

**Paso 1: Crear Proyecto Local**

```bash
# Create project directory and initialize
mkdir my-api
cd my-api
npm init -y

# Install necessary dependencies
npm install express pg dotenv cors
npm install nodemon --save-dev
```

**Paso 2: Crear Estructura Básica de Archivos**

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

**Paso 3: Set Up .gitignore**

```gitignore
node_modules
.env
```

**Paso 4: Configure Database Connection (src/config/database.js)**

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

**Paso 5: Create Main Server File (src/index.js)**

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

**Paso 6: Create API Routes (src/routes/api.js)**

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

**Paso 7: Update package.json Scripts**

```json:package.json
{
  "scripts": {
    "start": "node src/index.js",
    "dev": "nodemon src/index.js"
  }
}
```

**Paso 8: Set Up Database on Render**

1. Go to render.com and create account
2. Create new PostgreSQL database
3. Copy the external database URL
4. Create `.env` file:

```plaintext:.env
DATABASE_URL=your_postgres_database_url
PORT=3000
```

**Paso 9: Create Database Table**
Use Render's database dashboard or a PostgreSQL client to run:

```sql
CREATE TABLE items (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    description TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

**Paso 10: Deploy to Render**

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

**Consejos Adicionales:**

1. Agregar manejo de errores
2. Implementar validación de entrada
3. Agregar autenticación si es necesario
4. Usar un ORM como Sequelize o Prisma para aplicaciones más complejas
5. Agregar registro para monitoreo de producción

¡Esta es una configuración básica sobre la cual puede construir. Háganme saber si necesitan aclaración sobre algún paso o desean agregar más funciones!
