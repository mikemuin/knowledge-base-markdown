# Complete Guide: Building ElectronJS Desktop Apps with SQLite

## Table of Contents

1. [Prerequisites & Environment Setup](https://claude.ai/chat/c33e140c-f281-4268-9f22-a5891d03f5d8#prerequisites--environment-setup)
2. [Project Initialization](https://claude.ai/chat/c33e140c-f281-4268-9f22-a5891d03f5d8#project-initialization)
3. [Basic Electron Application Structure](https://claude.ai/chat/c33e140c-f281-4268-9f22-a5891d03f5d8#basic-electron-application-structure)
4. [SQLite Database Integration](https://claude.ai/chat/c33e140c-f281-4268-9f22-a5891d03f5d8#sqlite-database-integration)
5. [Inter-Process Communication (IPC)](https://claude.ai/chat/c33e140c-f281-4268-9f22-a5891d03f5d8#inter-process-communication-ipc)
6. [Building the User Interface](https://claude.ai/chat/c33e140c-f281-4268-9f22-a5891d03f5d8#building-the-user-interface)
7. [Security Best Practices](https://claude.ai/chat/c33e140c-f281-4268-9f22-a5891d03f5d8#security-best-practices)
8. [Testing Your Application](https://claude.ai/chat/c33e140c-f281-4268-9f22-a5891d03f5d8#testing-your-application)
9. [Building & Packaging](https://claude.ai/chat/c33e140c-f281-4268-9f22-a5891d03f5d8#building--packaging)
10. [Deployment & Distribution](https://claude.ai/chat/c33e140c-f281-4268-9f22-a5891d03f5d8#deployment--distribution)

------

## Prerequisites & Environment Setup

### Required Tools

- **Node.js** (LTS version 18.x or higher)
- **npm** or **yarn** package manager
- **Git** for version control
- **Code Editor** (VS Code recommended)

### Development Environment Setup

```bash
# Check Node.js version
node --version

# Check npm version
npm --version

# Install useful global packages
npm install -g electron-builder
```

### Recommended VS Code Extensions

- **Electron** - IntelliSense and debugging support
- **SQLite Viewer** - Database inspection
- **ESLint** - Code quality
- **Prettier** - Code formatting
- **Thunder Client** - API testing (if needed)

------

## Project Initialization

### Step 1: Create Project Directory

```bash
mkdir my-electron-app
cd my-electron-app
```

### Step 2: Initialize Package.json

```bash
npm init -y
```

### Step 3: Install Core Dependencies

```bash
# Electron framework
npm install --save-dev electron

# SQLite database
npm install sqlite3

# Additional utilities
npm install --save-dev concurrently nodemon
```

### Step 4: Create Basic Project Structure

```
my-electron-app/
├── src/
│   ├── main/           # Main process files
│   │   ├── main.js     # Main process entry point
│   │   └── database.js # Database operations
│   ├── renderer/       # Renderer process files
│   │   ├── index.html  # UI HTML
│   │   ├── renderer.js # Renderer process logic
│   │   └── styles.css  # Styling
│   └── preload/        # Preload scripts
│       └── preload.js  # Secure IPC bridge
├── assets/             # Images, icons, etc.
├── build/              # Build configuration
├── dist/               # Distribution files
└── package.json
```

### Step 5: Update Package.json Scripts

```json
{
  "main": "src/main/main.js",
  "scripts": {
    "start": "electron .",
    "dev": "concurrently \"npm run start\" \"nodemon --watch src --exec npm run start\"",
    "build": "electron-builder",
    "build:win": "electron-builder --win",
    "build:mac": "electron-builder --mac",
    "build:linux": "electron-builder --linux"
  }
}
```

------

## Basic Electron Application Structure

### Step 1: Create Main Process (src/main/main.js)

```javascript
const { app, BrowserWindow, ipcMain } = require('electron');
const path = require('path');
const isDev = process.env.NODE_ENV === 'development';

let mainWindow;

function createWindow() {
  // Create the browser window
  mainWindow = new BrowserWindow({
    width: 1200,
    height: 800,
    minWidth: 800,
    minHeight: 600,
    webPreferences: {
      nodeIntegration: false,        // Security best practice
      contextIsolation: true,        // Security best practice
      enableRemoteModule: false,     // Security best practice
      preload: path.join(__dirname, '../preload/preload.js')
    },
    icon: path.join(__dirname, '../../assets/icon.png'), // Optional
    show: false // Don't show until ready
  });

  // Load the app
  mainWindow.loadFile(path.join(__dirname, '../renderer/index.html'));

  // Show window when ready to prevent visual flash
  mainWindow.once('ready-to-show', () => {
    mainWindow.show();
  });

  // Open DevTools in development
  if (isDev) {
    mainWindow.webContents.openDevTools();
  }

  // Handle window closed
  mainWindow.on('closed', () => {
    mainWindow = null;
  });
}

// App event listeners
app.whenReady().then(createWindow);

app.on('window-all-closed', () => {
  if (process.platform !== 'darwin') {
    app.quit();
  }
});

app.on('activate', () => {
  if (BrowserWindow.getAllWindows().length === 0) {
    createWindow();
  }
});

// Security: Prevent new window creation
app.on('web-contents-created', (event, contents) => {
  contents.on('new-window', (event, navigationUrl) => {
    event.preventDefault();
  });
});
```

### Step 2: Create Preload Script (src/preload/preload.js)

```javascript
const { contextBridge, ipcRenderer } = require('electron');

// Expose protected methods that allow the renderer process to use
// the ipcRenderer without exposing the entire object
contextBridge.exposeInMainWorld('electronAPI', {
  // Database operations
  createRecord: (data) => ipcRenderer.invoke('db:create', data),
  readRecords: () => ipcRenderer.invoke('db:read'),
  updateRecord: (id, data) => ipcRenderer.invoke('db:update', id, data),
  deleteRecord: (id) => ipcRenderer.invoke('db:delete', id),

  // Utility functions
  showMessage: (message) => ipcRenderer.invoke('show-message', message),

  // Event listeners
  onDatabaseChange: (callback) => ipcRenderer.on('db:changed', callback),
  removeDatabaseListener: () => ipcRenderer.removeAllListeners('db:changed')
});
```

### Step 3: Create Basic HTML (src/renderer/index.html)

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>My Electron App</title>
    <link rel="stylesheet" href="styles.css">
</head>
<body>
    <div class="container">
        <header>
            <h1>My Electron SQLite App</h1>
        </header>

        <main>
            <section class="form-section">
                <h2>Add New Record</h2>
                <form id="record-form">
                    <input type="text" id="name" placeholder="Name" required>
                    <input type="email" id="email" placeholder="Email" required>
                    <button type="submit">Add Record</button>
                </form>
            </section>

            <section class="records-section">
                <h2>Records</h2>
                <div id="records-list"></div>
            </section>
        </main>
    </div>

    <script src="renderer.js"></script>
</body>
</html>
```

------

## SQLite Database Integration

### Step 1: Create Database Module (src/main/database.js)

```javascript
const sqlite3 = require('sqlite3').verbose();
const path = require('path');
const { app } = require('electron');

class Database {
  constructor() {
    this.db = null;
    this.init();
  }

  init() {
    // Create database in user data directory
    const dbPath = path.join(app.getPath('userData'), 'app.db');

    this.db = new sqlite3.Database(dbPath, (err) => {
      if (err) {
        console.error('Database opening error: ', err);
        return;
      }
      console.log('Connected to SQLite database.');
    });

    // Create tables if they don't exist
    this.createTables();
  }

  createTables() {
    const createUsersTable = `
      CREATE TABLE IF NOT EXISTS users (
        id INTEGER PRIMARY KEY AUTOINCREMENT,
        name TEXT NOT NULL,
        email TEXT UNIQUE NOT NULL,
        created_at DATETIME DEFAULT CURRENT_TIMESTAMP,
        updated_at DATETIME DEFAULT CURRENT_TIMESTAMP
      )
    `;

    this.db.run(createUsersTable, (err) => {
      if (err) {
        console.error('Error creating users table:', err);
      } else {
        console.log('Users table created or already exists.');
      }
    });
  }

  // Create a new record
  create(data) {
    return new Promise((resolve, reject) => {
      const { name, email } = data;
      const sql = `INSERT INTO users (name, email) VALUES (?, ?)`;

      this.db.run(sql, [name, email], function(err) {
        if (err) {
          reject(err);
        } else {
          resolve({ id: this.lastID, name, email });
        }
      });
    });
  }

  // Read all records
  readAll() {
    return new Promise((resolve, reject) => {
      const sql = `SELECT * FROM users ORDER BY created_at DESC`;

      this.db.all(sql, [], (err, rows) => {
        if (err) {
          reject(err);
        } else {
          resolve(rows);
        }
      });
    });
  }

  // Read a single record by ID
  readById(id) {
    return new Promise((resolve, reject) => {
      const sql = `SELECT * FROM users WHERE id = ?`;

      this.db.get(sql, [id], (err, row) => {
        if (err) {
          reject(err);
        } else {
          resolve(row);
        }
      });
    });
  }

  // Update a record
  update(id, data) {
    return new Promise((resolve, reject) => {
      const { name, email } = data;
      const sql = `UPDATE users SET name = ?, email = ?, updated_at = CURRENT_TIMESTAMP WHERE id = ?`;

      this.db.run(sql, [name, email, id], function(err) {
        if (err) {
          reject(err);
        } else {
          resolve({ id, name, email, changes: this.changes });
        }
      });
    });
  }

  // Delete a record
  delete(id) {
    return new Promise((resolve, reject) => {
      const sql = `DELETE FROM users WHERE id = ?`;

      this.db.run(sql, [id], function(err) {
        if (err) {
          reject(err);
        } else {
          resolve({ id, changes: this.changes });
        }
      });
    });
  }

  // Search records
  search(query) {
    return new Promise((resolve, reject) => {
      const sql = `SELECT * FROM users WHERE name LIKE ? OR email LIKE ? ORDER BY created_at DESC`;
      const searchTerm = `%${query}%`;

      this.db.all(sql, [searchTerm, searchTerm], (err, rows) => {
        if (err) {
          reject(err);
        } else {
          resolve(rows);
        }
      });
    });
  }

  // Close database connection
  close() {
    if (this.db) {
      this.db.close((err) => {
        if (err) {
          console.error('Error closing database:', err);
        } else {
          console.log('Database connection closed.');
        }
      });
    }
  }
}

module.exports = Database;
```

### Step 2: Integrate Database with Main Process

Update `src/main/main.js` to include database operations:

```javascript
// Add at the top of main.js
const Database = require('./database');

// Add after createWindow function
let db;

app.whenReady().then(() => {
  createWindow();

  // Initialize database
  db = new Database();

  // Set up IPC handlers
  setupIpcHandlers();
});

// Add IPC handlers
function setupIpcHandlers() {
  // Create record
  ipcMain.handle('db:create', async (event, data) => {
    try {
      const result = await db.create(data);
      // Notify renderer of database change
      mainWindow.webContents.send('db:changed', 'create', result);
      return { success: true, data: result };
    } catch (error) {
      console.error('Database create error:', error);
      return { success: false, error: error.message };
    }
  });

  // Read records
  ipcMain.handle('db:read', async (event) => {
    try {
      const results = await db.readAll();
      return { success: true, data: results };
    } catch (error) {
      console.error('Database read error:', error);
      return { success: false, error: error.message };
    }
  });

  // Update record
  ipcMain.handle('db:update', async (event, id, data) => {
    try {
      const result = await db.update(id, data);
      mainWindow.webContents.send('db:changed', 'update', result);
      return { success: true, data: result };
    } catch (error) {
      console.error('Database update error:', error);
      return { success: false, error: error.message };
    }
  });

  // Delete record
  ipcMain.handle('db:delete', async (event, id) => {
    try {
      const result = await db.delete(id);
      mainWindow.webContents.send('db:changed', 'delete', result);
      return { success: true, data: result };
    } catch (error) {
      console.error('Database delete error:', error);
      return { success: false, error: error.message };
    }
  });

  // Search records
  ipcMain.handle('db:search', async (event, query) => {
    try {
      const results = await db.search(query);
      return { success: true, data: results };
    } catch (error) {
      console.error('Database search error:', error);
      return { success: false, error: error.message };
    }
  });
}

// Clean up database on app quit
app.on('before-quit', () => {
  if (db) {
    db.close();
  }
});
```

------

## Inter-Process Communication (IPC)

### Understanding IPC in Electron

IPC allows secure communication between the main process and renderer processes. We use:

- **`ipcMain.handle()`** - Handle async requests from renderer
- **`ipcRenderer.invoke()`** - Send async requests to main process
- **`contextBridge`** - Safely expose APIs to renderer

### Step 1: Enhance Preload Script

Update `src/preload/preload.js`:

```javascript
const { contextBridge, ipcRenderer } = require('electron');

contextBridge.exposeInMainWorld('electronAPI', {
  // Database CRUD operations
  createRecord: (data) => ipcRenderer.invoke('db:create', data),
  readRecords: () => ipcRenderer.invoke('db:read'),
  updateRecord: (id, data) => ipcRenderer.invoke('db:update', id, data),
  deleteRecord: (id) => ipcRenderer.invoke('db:delete', id),
  searchRecords: (query) => ipcRenderer.invoke('db:search', query),

  // Event listeners for real-time updates
  onDatabaseChange: (callback) => {
    ipcRenderer.on('db:changed', (event, action, data) => {
      callback(action, data);
    });
  },

  // Cleanup
  removeAllListeners: () => {
    ipcRenderer.removeAllListeners('db:changed');
  }
});
```

### Step 2: Create Renderer Process Logic

Create `src/renderer/renderer.js`:

```javascript
class AppController {
  constructor() {
    this.records = [];
    this.editingId = null;
    this.init();
  }

  async init() {
    // Set up event listeners
    this.setupEventListeners();

    // Load initial data
    await this.loadRecords();

    // Listen for database changes
    window.electronAPI.onDatabaseChange((action, data) => {
      console.log('Database changed:', action, data);
      this.loadRecords(); // Refresh the list
    });
  }

  setupEventListeners() {
    // Form submission
    document.getElementById('record-form').addEventListener('submit', (e) => {
      e.preventDefault();
      this.handleFormSubmit();
    });

    // Search functionality
    const searchInput = document.getElementById('search');
    if (searchInput) {
      searchInput.addEventListener('input', (e) => {
        this.handleSearch(e.target.value);
      });
    }
  }

  async handleFormSubmit() {
    const name = document.getElementById('name').value.trim();
    const email = document.getElementById('email').value.trim();

    if (!name || !email) {
      this.showMessage('Please fill in all fields', 'error');
      return;
    }

    try {
      let result;
      if (this.editingId) {
        // Update existing record
        result = await window.electronAPI.updateRecord(this.editingId, { name, email });
        this.editingId = null;
        document.querySelector('#record-form button').textContent = 'Add Record';
      } else {
        // Create new record
        result = await window.electronAPI.createRecord({ name, email });
      }

      if (result.success) {
        this.clearForm();
        this.showMessage('Record saved successfully!', 'success');
      } else {
        this.showMessage(result.error, 'error');
      }
    } catch (error) {
      console.error('Error saving record:', error);
      this.showMessage('Error saving record', 'error');
    }
  }

  async loadRecords() {
    try {
      const result = await window.electronAPI.readRecords();
      if (result.success) {
        this.records = result.data;
        this.renderRecords();
      } else {
        this.showMessage(result.error, 'error');
      }
    } catch (error) {
      console.error('Error loading records:', error);
      this.showMessage('Error loading records', 'error');
    }
  }

  renderRecords() {
    const recordsList = document.getElementById('records-list');
    recordsList.innerHTML = '';

    if (this.records.length === 0) {
      recordsList.innerHTML = '<p class="no-records">No records found</p>';
      return;
    }

    this.records.forEach(record => {
      const recordElement = document.createElement('div');
      recordElement.className = 'record-item';
      recordElement.innerHTML = `
        <div class="record-info">
          <h3>${this.escapeHtml(record.name)}</h3>
          <p>${this.escapeHtml(record.email)}</p>
          <small>Created: ${new Date(record.created_at).toLocaleString()}</small>
        </div>
        <div class="record-actions">
          <button onclick="app.editRecord(${record.id})" class="btn-edit">Edit</button>
          <button onclick="app.deleteRecord(${record.id})" class="btn-delete">Delete</button>
        </div>
      `;
      recordsList.appendChild(recordElement);
    });
  }

  async editRecord(id) {
    const record = this.records.find(r => r.id === id);
    if (record) {
      document.getElementById('name').value = record.name;
      document.getElementById('email').value = record.email;
      this.editingId = id;
      document.querySelector('#record-form button').textContent = 'Update Record';
    }
  }

  async deleteRecord(id) {
    if (confirm('Are you sure you want to delete this record?')) {
      try {
        const result = await window.electronAPI.deleteRecord(id);
        if (result.success) {
          this.showMessage('Record deleted successfully!', 'success');
        } else {
          this.showMessage(result.error, 'error');
        }
      } catch (error) {
        console.error('Error deleting record:', error);
        this.showMessage('Error deleting record', 'error');
      }
    }
  }

  async handleSearch(query) {
    try {
      if (query.trim() === '') {
        await this.loadRecords();
      } else {
        const result = await window.electronAPI.searchRecords(query);
        if (result.success) {
          this.records = result.data;
          this.renderRecords();
        }
      }
    } catch (error) {
      console.error('Error searching records:', error);
    }
  }

  clearForm() {
    document.getElementById('name').value = '';
    document.getElementById('email').value = '';
    this.editingId = null;
    document.querySelector('#record-form button').textContent = 'Add Record';
  }

  showMessage(message, type) {
    // Create a simple notification system
    const messageElement = document.createElement('div');
    messageElement.className = `message message-${type}`;
    messageElement.textContent = message;

    document.body.appendChild(messageElement);

    setTimeout(() => {
      messageElement.remove();
    }, 3000);
  }

  escapeHtml(text) {
    const div = document.createElement('div');
    div.textContent = text;
    return div.innerHTML;
  }
}

// Initialize the app when DOM is loaded
document.addEventListener('DOMContentLoaded', () => {
  window.app = new AppController();
});
```

------

## Building the User Interface

### Step 1: Enhanced HTML Structure

Update `src/renderer/index.html`:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>My Electron SQLite App</title>
    <link rel="stylesheet" href="styles.css">
</head>
<body>
    <div class="container">
        <header class="app-header">
            <h1>📊 My Electron SQLite App</h1>
            <div class="header-controls">
                <input type="text" id="search" placeholder="Search records..." class="search-input">
            </div>
        </header>

        <main class="app-main">
            <section class="form-section">
                <h2>Add New Record</h2>
                <form id="record-form" class="record-form">
                    <div class="form-group">
                        <label for="name">Name:</label>
                        <input type="text" id="name" placeholder="Enter name" required>
                    </div>
                    <div class="form-group">
                        <label for="email">Email:</label>
                        <input type="email" id="email" placeholder="Enter email" required>
                    </div>
                    <button type="submit" class="btn-primary">Add Record</button>
                </form>
            </section>

            <section class="records-section">
                <h2>Records</h2>
                <div id="records-list" class="records-list"></div>
            </section>
        </main>
    </div>

    <script src="renderer.js"></script>
</body>
</html>
```

### Step 2: Create Stylish CSS

Create `src/renderer/styles.css`:

```css
/* Reset and base styles */
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  min-height: 100vh;
  color: #333;
}

.container {
  max-width: 1200px;
  margin: 0 auto;
  padding: 20px;
}

/* Header styles */
.app-header {
  background: rgba(255, 255, 255, 0.95);
  padding: 20px;
  border-radius: 10px;
  margin-bottom: 20px;
  display: flex;
  justify-content: space-between;
  align-items: center;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
}

.app-header h1 {
  color: #4a5568;
  font-size: 2rem;
}

.search-input {
  padding: 10px 15px;
  border: 2px solid #e2e8f0;
  border-radius: 5px;
  font-size: 14px;
  width: 250px;
  transition: border-color 0.3s;
}

.search-input:focus {
  outline: none;
  border-color: #667eea;
}

/* Main content */
.app-main {
  display: grid;
  grid-template-columns: 1fr 2fr;
  gap: 20px;
  align-items: start;
}

/* Form section */
.form-section {
  background: rgba(255, 255, 255, 0.95);
  padding: 25px;
  border-radius: 10px;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
}

.form-section h2 {
  color: #4a5568;
  margin-bottom: 20px;
}

.record-form {
  display: flex;
  flex-direction: column;
  gap: 15px;
}

.form-group {
  display: flex;
  flex-direction: column;
}

.form-group label {
  font-weight: 600;
  margin-bottom: 5px;
  color: #2d3748;
}

.form-group input {
  padding: 12px;
  border: 2px solid #e2e8f0;
  border-radius: 5px;
  font-size: 14px;
  transition: border-color 0.3s;
}

.form-group input:focus {
  outline: none;
  border-color: #667eea;
}

/* Button styles */
.btn-primary {
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  color: white;
  padding: 12px 24px;
  border: none;
  border-radius: 5px;
  font-size: 16px;
  font-weight: 600;
  cursor: pointer;
  transition: transform 0.2s, box-shadow 0.2s;
}

.btn-primary:hover {
  transform: translateY(-2px);
  box-shadow: 0 6px 12px rgba(102, 126, 234, 0.4);
}

.btn-edit {
  background: #48bb78;
  color: white;
  padding: 8px 16px;
  border: none;
  border-radius: 4px;
  font-size: 14px;
  cursor: pointer;
  margin-right: 8px;
  transition: background 0.2s;
}

.btn-edit:hover {
  background: #38a169;
}

.btn-delete {
  background: #f56565;
  color: white;
  padding: 8px 16px;
  border: none;
  border-radius: 4px;
  font-size: 14px;
  cursor: pointer;
  transition: background 0.2s;
}

.btn-delete:hover {
  background: #e53e3e;
}

/* Records section */
.records-section {
  background: rgba(255, 255, 255, 0.95);
  padding: 25px;
  border-radius: 10px;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
}

.records-section h2 {
  color: #4a5568;
  margin-bottom: 20px;
}

.records-list {
  display: flex;
  flex-direction: column;
  gap: 15px;
}

.record-item {
  background: #f7fafc;
  padding: 20px;
  border-radius: 8px;
  border-left: 4px solid #667eea;
  display: flex;
  justify-content: space-between;
  align-items: center;
  transition: transform 0.2s;
}

.record-item:hover {
  transform: translateX(5px);
}

.record-info h3 {
  color: #2d3748;
  margin-bottom: 5px;
}

.record-info p {
  color: #4a5568;
  margin-bottom: 5px;
}

.record-info small {
  color: #718096;
  font-size: 12px;
}

.record-actions {
  display: flex;
  gap: 8px;
}

.no-records {
  text-align: center;
  color: #718096;
  font-style: italic;
  padding: 40px;
}

/* Message notifications */
.message {
  position: fixed;
  top: 20px;
  right: 20px;
  padding: 15px 20px;
  border-radius: 5px;
  color: white;
  font-weight: 600;
  z-index: 1000;
  animation: slideIn 0.3s ease-out;
}

.message-success {
  background: #48bb78;
}

.message-error {
  background: #f56565;
}

@keyframes slideIn {
  from {
    transform: translateX(100%);
    opacity: 0;
  }
  to {
    transform: translateX(0);
    opacity: 1;
  }
}

/* Responsive design */
@media (max-width: 768px) {
  .app-main {
    grid-template-columns: 1fr;
  }

  .app-header {
    flex-direction: column;
    gap: 15px;
  }

  .search-input {
    width: 100%;
  }
}
```

------

## Security Best Practices

### Critical Security Considerations

1. **Never Enable Node Integration in Renderer**

```javascript
// ❌ WRONG - Security vulnerability
webPreferences: {
  nodeIntegration: true,
  contextIsolation: false
}

// ✅ CORRECT - Secure configuration
webPreferences: {
  nodeIntegration: false,
  contextIsolation: true,
  preload: path.join(__dirname, 'preload.js')
}
```

1. **Use Context Bridge for IPC**

```javascript
// In preload.js - Use contextBridge
const { contextBridge, ipcRenderer } = require('electron');

contextBridge.exposeInMainWorld('electronAPI', {
  // Only expose what's needed
  createRecord: (data) => ipcRenderer.invoke('db:create', data)
});
```

1. **Validate All Input Data**

```javascript
// In main process
ipcMain.handle('db:create', async (event, data) => {
  // Validate input
  if (!data || typeof data !== 'object') {
    throw new Error('Invalid data format');
  }

  const { name, email } = data;
  if (!name || !email) {
    throw new Error('Missing required fields');
  }

  // Proceed with database operation
});
```

1. **Prevent Navigation and New Windows**

```javascript
// In main.js
app.on('web-contents-created', (event, contents) => {
  contents.on('new-window', (event, navigationUrl) => {
    event.preventDefault();
  });

  contents.on('will-navigate', (event, navigationUrl) => {
    event.preventDefault();
  });
});
```

1. **Use Parameterized Queries**

```javascript
// ✅ CORRECT - Prevents SQL injection
const sql = `INSERT INTO users (name, email) VALUES (?, ?)`;
db.run(sql, [name, email], callback);

// ❌ WRONG - Vulnerable to SQL injection
const sql = `INSERT INTO users (name, email) VALUES ('${name}', '${email}')`;
```

------

## Testing Your Application

### Step 1: Install Testing Dependencies

```bash
npm install --save-dev jest spectron
```

### Step 2: Create Test Configuration

Create `jest.config.js`:

```javascript
module.exports = {
  testEnvironment: 'node',
  testMatch: ['**/tests/**/*.test.js'],
  collectCoverage: true,
  coverageDirectory: 'coverage',
  coverageReporters: ['text', 'html']
};
```

### Step 3: Create Test Files

Create `tests/database.test.js`:

```javascript
const Database = require('../src/main/database');
const path = require('path');
const fs = require('fs');

describe('Database', () => {
  let db;
  const testDbPath = path.join(__dirname, 'test.db');

  beforeEach(() => {
    // Create a test database
    db = new Database();
    // Override the database path for testing
    db.dbPath = testDbPath;
  });

  afterEach(async () => {
    // Clean up
    if (db) {
      await db.close();
    }
    if (fs.existsSync(testDbPath)) {
      fs.unlinkSync(testDbPath);
    }
  });

  test('should create a new record', async () => {
    const testData = { name: 'John Doe', email: 'john@example.com' };
    const result = await db.create(testData);

    expect(result).toHaveProperty('id');
    expect(result.name).toBe(testData.name);
    expect(result.email).toBe(testData.email);
  });

  test('should read all records', async () => {
    // Create test data
    await db.create({ name: 'John Doe', email: 'john@example.com' });
    await db.create({ name: 'Jane Smith', email: 'jane@example.com' });

    const results = await db.readAll();
    expect(results).toHaveLength(2);
  });

  test('should update a record', async () => {
    const created = await db.create({ name: 'John Doe', email: 'john@example.com' });
    const updated = await db.update(created.id, { name: 'John Updated', email: 'john@example.com' });

    expect(updated.name).toBe('John Updated');
  });

  test('should delete a record', async () => {
    const created = await db.create({ name: 'John Doe', email: 'john@example.com' });
    const deleted = await db.delete(created.id);

    expect(deleted.changes).toBe(1);
  });
});
```

### Step 4: Add Test Scripts to Package.json

```json
{
  "scripts": {
    "test": "jest",
    "test:watch": "jest --watch",
    "test:coverage": "jest --coverage"
  }
}
```

------

## Building & Packaging

### Step 1: Install electron-builder

```bash
npm install --save-dev electron-builder
```

### Step 2: Configure Build Settings

Add to `package.json`:

```json
{
  "build": {
    "appId": "com.yourcompany.yourapp",
    "productName": "My Electron App",
    "directories": {
      "output": "dist"
    },
    "files": [
      "src/**/*",
      "node_modules/**/*",
      "package.json"
    ],
    "extraResources": [
      "assets/**/*"
    ],
    "mac": {
      "category": "public.app-category.productivity",
      "icon": "assets/icon.icns"
    },
    "win": {
      "target": "nsis",
      "icon": "assets/icon.ico"
    },
    "linux": {
      "target": "AppImage",
      "icon": "assets/icon.png"
    }
  }
}
```

### Step 3: Create Build Scripts

```bash
# Build for current platform
npm run build

# Build for specific platforms
npm run build:win
npm run build:mac
npm run build:linux
```

### Step 4: Optimize for Production

Create `src/main/config.js`:

```javascript
const isDev = process.env.NODE_ENV === 'development';

module.exports = {
  isDev,
  // Database configuration
  database: {
    path: isDev ? './dev.db' : './prod.db',
    debug: isDev
  },
  // Window configuration
  window: {
    width: isDev ? 1400 : 1200,
    height: isDev ? 900 : 800,
    devTools: isDev
  }
};
```

------

## Deployment & Distribution

### Step 1: Code Signing (Optional but Recommended)

```bash
# For macOS
export CSC_LINK="path/to/certificate.p12"
export CSC_KEY_PASSWORD="certificate-password"

# For Windows
export CSC_LINK="path/to/certificate.p12"
export CSC_KEY_PASSWORD="certificate-password"
```

### Step 2: Auto-updater Setup

```bash
npm install electron-updater
```

Add to main process:

```javascript
const { autoUpdater } = require('electron-updater');

app.whenReady().then(() => {
  // Check for updates
  autoUpdater.checkForUpdatesAndNotify();
});
```

### Step 3: Distribution Options

**Option 1: Direct Distribution**

- Build installers for each platform
- Host on your website/server
- Provide download links

**Option 2: App Stores**

- Mac App Store (requires Apple Developer account)
- Microsoft Store
- Snap Store (Linux)

**Option 3: Auto-update Server**

- Set up update server
- Configure auto-updater
- Automatic updates for users

### Step 4: Final Checklist

- [ ] All features tested
- [ ] Database migrations work
- [ ] Cross-platform compatibility verified
- [ ] Security review completed
- [ ] Performance optimized
- [ ] Build process documented
- [ ] User documentation created
- [ ] Installation tested on clean systems

------

## Troubleshooting Common Issues

### Database Connection Issues

```javascript
// Add better error handling
this.db = new sqlite3.Database(dbPath, (err) => {
  if (err) {
    console.error('Database connection failed:', err);
    // Fallback to in-memory database
    this.db = new sqlite3.Database(':memory:');
  }
});
```

### IPC Communication Problems

```javascript
// Add timeout handling
const timeout = setTimeout(() => {
  reject(new Error('IPC timeout'));
}, 5000);

ipcRenderer.invoke('db:create', data).then(result => {
  clearTimeout(timeout);
  resolve(result);
});
```

### Build Issues

```bash
# Clear cache and rebuild
rm -rf node_modules
rm package-lock.json
npm install
npm run build
```

------

## Next Steps & Advanced Features

### Phase 1 Enhancements

- Add data validation
- Implement user authentication
- Add file attachments
- Create backup/restore functionality

### Phase 2 Features

- Multi-window support
- Plugin system
- Custom themes
- Advanced search

### Phase 3 Scaling

- Database optimization
- Performance monitoring
- Analytics integration
- Cloud synchronization

------

## Resources & References

### Documentation

- [Electron Documentation](https://www.electronjs.org/docs)
- [SQLite Documentation](https://sqlite.org/docs.html)
- [Node.js Documentation](https://nodejs.org/docs)

### Tools

- [Electron Builder](https://www.electron.build/)
- [Electron Forge](https://www.electronforge.io/)
- [Spectron](https://github.com/electron-userland/spectron)

### Best Practices

- [Electron Security Guidelines](https://www.electronjs.org/docs/tutorial/security)
- [SQLite Best Practices](https://sqlite.org/pragma.html)
- [Node.js Best Practices](https://github.com/goldbergyoni/nodebestpractices)

------

*This guide provides a comprehensive foundation for building production-ready Electron applications with SQLite. Remember to always prioritize security, performance, and user experience in your development process.*