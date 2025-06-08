# n8n 結構化數據存儲節點完整指南

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![n8n](https://img.shields.io/badge/n8n-Compatible-orange)](https://n8n.io)

這個倉庫整理了 n8n 工作流自動化平台中所有適合存儲結構化數據的節點選項，除了常見的 Google Sheets、Notion、Supabase、Airtable 之外的其他選擇。

## 📋 目錄

- [傳統關係型數據庫](#傳統關係型數據庫)
- [NoSQL 數據庫](#nosql-數據庫)
- [雲端數據庫服務](#雲端數據庫服務)
- [鍵值存儲](#鍵值存儲)
- [時序數據庫](#時序數據庫)
- [搜索引擎](#搜索引擎)
- [表格/電子表格替代方案](#表格電子表格替代方案)
- [對象存儲](#對象存儲)
- [圖數據庫](#圖數據庫)
- [數據倉庫](#數據倉庫)
- [使用建議](#使用建議)
- [性能對比](#性能對比)

## 🗄️ 傳統關係型數據庫

### PostgreSQL
- **節點名稱**: `Postgres`
- **適用場景**: 複雜查詢、事務處理、大型應用
- **特點**: 開源、高性能、支持 ACID、豐富的數據類型
- **操作**: Execute Query, Insert, Update, Delete
- **最佳實踐**: 適合需要複雜關聯查詢和數據完整性的場景

### Microsoft SQL Server  
- **節點名稱**: `Microsoft SQL`
- **適用場景**: 企業級應用、Windows 環境集成
- **特點**: 企業級功能、商業支持、與 Microsoft 生態系統集成
- **操作**: Execute Query, Insert, Update, Delete
- **最佳實踐**: 適合已有 Microsoft 技術棧的企業環境

### MySQL
- **節點名稱**: `MySQL`
- **適用場景**: Web 應用、中小型項目
- **特點**: 輕量級、易於使用、廣泛支持
- **操作**: Execute Query, Insert, Update, Delete
- **注意**: n8n v1.0 後不再支持 MySQL 作為 n8n 本身的數據庫，但仍可作為數據存儲節點使用

## 🍃 NoSQL 數據庫

### MongoDB
- **節點名稱**: `MongoDB`
- **適用場景**: 文檔型數據、靈活數據結構、快速原型開發
- **特點**: 文檔導向、無模式、水平擴展
- **操作**: Aggregate, Find, Insert, Update, Delete
- **最佳實踐**: 適合需要靈活數據結構和快速迭代的項目

### Google Cloud Firestore
- **節點名稱**: `Google Cloud Firestore`
- **適用場景**: 移動應用、實時同步、離線支持
- **特點**: 實時數據同步、自動擴展、離線支持
- **操作**: Create, Read, Update, Delete documents
- **最佳實踐**: 適合移動和 Web 應用的實時數據需求

## ☁️ 雲端數據庫服務

### AWS DynamoDB
- **節點名稱**: `AWS DynamoDB`
- **適用場景**: 高性能應用、無服務器架構、遊戲平台
- **特點**: 無服務器、單位數毫秒延遲、自動擴展
- **操作**: GetItem, PutItem, UpdateItem, DeleteItem, Query, Scan
- **最佳實踐**: 適合需要高性能和無服務器架構的應用

## 🔑 鍵值存儲

### Redis
- **節點名稱**: `Redis`
- **適用場景**: 緩存、會話存儲、實時分析、消息隊列
- **特點**: 內存存儲、高性能、豐富的數據結構
- **操作**: Get, Set, Delete, Incr, Keys, Publish
- **最佳實踐**: 適合需要高速讀寫和緩存的場景

## ⏰ 時序數據庫

### InfluxDB Cloud
- **節點名稱**: `InfluxDB Cloud` (透過 HTTP Request 節點)
- **適用場景**: IoT 數據、監控指標、時間序列分析
- **特點**: 專為時序數據優化、高效壓縮、實時查詢
- **操作**: 透過 REST API 進行讀寫操作
- **最佳實踐**: 適合 IoT 設備數據和系統監控指標存儲

## 🔍 搜索引擎

### Elasticsearch
- **節點名稱**: `Elasticsearch` (透過 HTTP Request 節點)
- **適用場景**: 全文搜索、日誌分析、數據探索
- **特點**: 分佈式搜索、實時分析、RESTful API
- **操作**: Index, Search, Update, Delete documents
- **最佳實踐**: 適合需要強大搜索功能和日誌分析的應用

## 📊 表格/電子表格替代方案

### Baserow
- **節點名稱**: `Baserow`
- **適用場景**: 無代碼數據庫、協作型項目管理
- **特點**: 開源、無代碼、電子表格界面
- **操作**: Create, Read, Update, Delete rows
- **最佳實踐**: 適合非技術團隊管理結構化數據

### SeaTable
- **節點名稱**: `SeaTable` (透過 HTTP Request 節點)
- **適用場景**: 協作數據庫、項目管理
- **特點**: 電子表格界面、數據庫功能、協作特性
- **操作**: 透過 API 進行數據操作
- **最佳實踐**: 適合需要團隊協作的數據管理

### Grist
- **節點名稱**: `Grist` (透過 HTTP Request 節點)
- **適用場景**: 智能電子表格、數據分析
- **特點**: 電子表格與數據庫的結合、Python 公式支持
- **操作**: 透過 API 進行數據操作
- **最佳實踐**: 適合需要複雜計算的數據分析場景

## 🗃️ 對象存儲

### AWS S3
- **節點名稱**: `AWS S3`
- **適用場景**: 文件存儲、數據備份、靜態網站託管
- **特點**: 可擴展、高可用性、成本效益
- **操作**: Upload, Download, Delete, List objects
- **最佳實踐**: 適合存儲文件和進行數據備份

## 🕸️ 圖數據庫

### GraphQL
- **節點名稱**: `GraphQL`
- **適用場景**: API 查詢、關聯數據獲取
- **特點**: 靈活查詢、單一端點、類型安全
- **操作**: Query, Mutation, Subscription
- **最佳實踐**: 適合複雜的關聯數據查詢和 API 整合

## 🏭 數據倉庫

### Snowflake
- **節點名稱**: `Snowflake` (透過 HTTP Request 節點)
- **適用場景**: 大數據分析、數據倉庫、商業智能
- **特點**: 雲原生、自動擴展、數據共享
- **操作**: 透過 SQL API 進行數據操作
- **最佳實踐**: 適合企業級數據分析和 BI 場景

## 💡 使用建議

### 選擇標準

1. **數據量大小**
   - 小型項目 (< 1GB): SQLite, Google Sheets, Baserow
   - 中型項目 (1GB - 100GB): PostgreSQL, MongoDB, Redis
   - 大型項目 (> 100GB): Snowflake, AWS DynamoDB, Elasticsearch

2. **查詢複雜度**
   - 簡單查詢: Redis, Google Sheets, Baserow
   - 中等複雜度: PostgreSQL, MongoDB
   - 複雜分析: Snowflake, Elasticsearch

3. **實時性要求**
   - 低延遲: Redis, AWS DynamoDB
   - 實時同步: Google Cloud Firestore, MongoDB
   - 批量處理: PostgreSQL, Snowflake

4. **團隊技能**
   - 非技術團隊: Baserow, SeaTable, Grist
   - 開發團隊: PostgreSQL, MongoDB, Redis
   - 數據團隊: Snowflake, Elasticsearch, InfluxDB

### 最佳實踐

#### 數據建模
```javascript
// PostgreSQL - 關係型數據建模
{
  "users": {
    "id": "primary_key",
    "email": "unique",
    "created_at": "timestamp"
  },
  "orders": {
    "id": "primary_key", 
    "user_id": "foreign_key",
    "total": "decimal"
  }
}

// MongoDB - 文檔型數據建模
{
  "_id": "ObjectId",
  "user": {
    "email": "user@example.com",
    "profile": {...}
  },
  "orders": [
    {
      "id": "order_1",
      "total": 99.99,
      "items": [...]
    }
  ]
}
```

#### 性能優化
- **索引策略**: 根據查詢模式創建適當索引
- **數據分區**: 大表按時間或其他維度分區
- **緩存層**: 使用 Redis 作為緩存提升性能
- **連接池**: 配置適當的數據庫連接池

## 📊 性能對比

| 節點類型 | 寫入性能 | 查詢性能 | 擴展性 | 複雜度 | 成本 |
|---------|---------|---------|--------|--------|------|
| Redis | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐ | ⭐⭐⭐ |
| MongoDB | ⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐ |
| PostgreSQL | ⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐ |
| AWS DynamoDB | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐ | ⭐⭐ |
| Elasticsearch | ⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐ |
| Baserow | ⭐⭐ | ⭐⭐ | ⭐⭐ | ⭐ | ⭐⭐⭐⭐⭐ |

## 🚀 快速開始示例

### PostgreSQL 基本操作
```javascript
// 插入數據
{
  "operation": "insert",
  "schema": "public",
  "table": "users",
  "columns": "name, email, created_at",
  "returnFields": "*"
}

// 查詢數據
{
  "operation": "executeQuery",
  "query": "SELECT * FROM users WHERE created_at > $1",
  "parameters": "2024-01-01"
}
```

### MongoDB 基本操作
```javascript
// 插入文檔
{
  "operation": "insert",
  "collection": "products",
  "fields": {
    "name": "iPhone 15",
    "price": 999,
    "category": "electronics"
  }
}

// 查詢文檔
{
  "operation": "find", 
  "collection": "products",
  "query": {
    "category": "electronics",
    "price": {"$lt": 1000}
  }
}
```

### Redis 基本操作
```javascript
// 設置鍵值
{
  "operation": "set",
  "key": "user:1001",
  "value": "{\"name\":\"John\",\"email\":\"john@example.com\"}"
}

// 獲取值
{
  "operation": "get",
  "key": "user:1001"
}
```

## 📚 相關資源

- [n8n 官方文檔](https://docs.n8n.io/)
- [n8n 社區節點](https://docs.n8n.io/integrations/community-nodes/)
- [數據庫設計最佳實踐](https://docs.n8n.io/data/)
- [n8n 工作流示例](https://n8n.io/workflows/)

## 🤝 貢獻

歡迎提交 PR 來補充更多節點信息或改進文檔！

## 📄 許可證

MIT License - 詳見 [LICENSE](LICENSE) 文件

---

**注意**: 這個列表會隨著 n8n 版本更新而變化，建議定期查看官方文檔獲取最新信息。
