# n8n 結構化數據存儲節點配置示例

這個文件提供了各種 n8n 結構化數據存儲節點的詳細配置示例和最佳實踐。

## 📋 目錄

- [PostgreSQL 配置與示例](#postgresql-配置與示例)
- [MongoDB 配置與示例](#mongodb-配置與示例)
- [Redis 配置與示例](#redis-配置與示例)
- [AWS DynamoDB 配置與示例](#aws-dynamodb-配置與示例)
- [Google Cloud Firestore 配置與示例](#google-cloud-firestore-配置與示例)
- [常見工作流模式](#常見工作流模式)
- [錯誤處理和重試策略](#錯誤處理和重試策略)
- [性能調優建議](#性能調優建議)

## 🐘 PostgreSQL 配置與示例

### 基本連接配置
```json
{
  "host": "localhost",
  "port": 5432,
  "database": "myapp",
  "username": "postgres",
  "password": "your_password",
  "ssl": {
    "enabled": false
  },
  "connectionTimeout": 60000,
  "idleTimeout": 30000
}
```

### 常用操作示例

#### 1. 插入用戶數據
```json
{
  "operation": "insert",
  "schema": "public",
  "table": "users",
  "columns": "name, email, created_at, metadata",
  "returnFields": "id, name, email",
  "onConflict": "doNothing"
}
```

#### 2. 更新用戶資料
```json
{
  "operation": "update",
  "schema": "public", 
  "table": "users",
  "updateKey": "email",
  "columns": "name, updated_at",
  "returnFields": "*"
}
```

#### 3. 複雜查詢
```sql
-- 在 Execute Query 操作中使用
SELECT 
  u.id,
  u.name,
  u.email,
  COUNT(o.id) as order_count,
  SUM(o.total) as total_spent
FROM users u
LEFT JOIN orders o ON u.id = o.user_id
WHERE u.created_at >= $1
  AND u.status = 'active'
GROUP BY u.id, u.name, u.email
HAVING COUNT(o.id) > 0
ORDER BY total_spent DESC
LIMIT 100;
```

#### 4. 事務處理
```json
{
  "operation": "executeQuery",
  "queryBatching": "transaction",
  "query": [
    "INSERT INTO orders (user_id, total) VALUES ($1, $2)",
    "UPDATE users SET last_order_date = NOW() WHERE id = $1",
    "INSERT INTO audit_log (action, user_id) VALUES ('order_created', $1)"
  ]
}
```

## 🍃 MongoDB 配置與示例

### 基本連接配置
```json
{
  "connectionString": "mongodb://username:password@localhost:27017/myapp",
  "options": {
    "useNewUrlParser": true,
    "useUnifiedTopology": true,
    "maxPoolSize": 10,
    "serverSelectionTimeoutMS": 5000,
    "socketTimeoutMS": 45000
  }
}
```

### 常用操作示例

#### 1. 插入文檔
```json
{
  "operation": "insert",
  "collection": "products",
  "fields": {
    "name": "{{ $json.productName }}",
    "price": "{{ $json.price }}",
    "category": "{{ $json.category }}",
    "tags": "{{ $json.tags.split(',') }}",
    "metadata": {
      "createdAt": "{{ new Date().toISOString() }}",
      "source": "n8n_workflow"
    },
    "inventory": {
      "quantity": "{{ parseInt($json.quantity) }}",
      "location": "{{ $json.warehouse }}"
    }
  }
}
```

#### 2. 複雜查詢
```json
{
  "operation": "find",
  "collection": "orders",
  "query": {
    "status": "completed",
    "createdAt": {
      "$gte": "{{ $json.startDate }}",
      "$lte": "{{ $json.endDate }}"
    },
    "total": { "$gt": 100 },
    "$or": [
      { "category": "electronics" },
      { "priority": "high" }
    ]
  },
  "options": {
    "projection": {
      "customerEmail": 1,
      "total": 1,
      "items": 1,
      "createdAt": 1
    },
    "sort": { "createdAt": -1 },
    "limit": 50
  }
}
```

#### 3. 聚合管道
```json
{
  "operation": "aggregate",
  "collection": "sales",
  "pipeline": [
    {
      "$match": {
        "date": {
          "$gte": "{{ $json.startDate }}",
          "$lte": "{{ $json.endDate }}"
        }
      }
    },
    {
      "$group": {
        "_id": {
          "category": "$category",
          "month": { "$month": "$date" }
        },
        "totalSales": { "$sum": "$amount" },
        "averageOrder": { "$avg": "$amount" },
        "orderCount": { "$sum": 1 }
      }
    },
    {
      "$sort": { "totalSales": -1 }
    }
  ]
}
```

#### 4. 更新多個文檔
```json
{
  "operation": "update",
  "collection": "users",
  "updateKey": "email",
  "fields": {
    "$set": {
      "lastLogin": "{{ new Date().toISOString() }}",
      "loginCount": "{{ $json.loginCount + 1 }}"
    },
    "$push": {
      "loginHistory": {
        "timestamp": "{{ new Date().toISOString() }}",
        "ip": "{{ $json.clientIP }}"
      }
    }
  },
  "upsert": true,
  "multi": false
}
```

## 🔑 Redis 配置與示例

### 基本連接配置
```json
{
  "host": "localhost",
  "port": 6379,
  "password": "your_redis_password",
  "database": 0,
  "connectionTimeout": 5000,
  "lazyConnect": true
}
```

### 常用操作示例

#### 1. 設置緩存數據
```json
{
  "operation": "set",
  "key": "user:{{ $json.userId }}:profile",
  "value": "{{ JSON.stringify($json.userProfile) }}",
  "expire": true,
  "expireAfter": 3600
}
```

#### 2. 批量設置
```json
{
  "operation": "set",
  "key": "session:{{ $json.sessionId }}",
  "value": "{{ JSON.stringify({
    userId: $json.userId,
    loginTime: new Date().toISOString(),
    permissions: $json.permissions
  }) }}",
  "expire": true,
  "expireAfter": 1800
}
```

#### 3. 計數器操作
```json
{
  "operation": "incr",
  "key": "page_views:{{ $json.pageId }}:{{ new Date().toISOString().split('T')[0] }}"
}
```

#### 4. 列表操作
```json
{
  "operation": "set",
  "key": "recent_activities:{{ $json.userId }}",
  "value": "{{ JSON.stringify($json.activity) }}"
}
```

#### 5. 發布訂閱
```json
{
  "operation": "publish",
  "channel": "notifications",
  "message": "{{ JSON.stringify({
    type: 'user_action',
    userId: $json.userId,
    action: $json.action,
    timestamp: new Date().toISOString()
  }) }}"
}
```

## ☁️ AWS DynamoDB 配置與示例

### 基本配置
```json
{
  "accessKeyId": "YOUR_ACCESS_KEY",
  "secretAccessKey": "YOUR_SECRET_KEY", 
  "region": "us-east-1",
  "endpoint": "https://dynamodb.us-east-1.amazonaws.com"
}
```

### 常用操作示例

#### 1. 插入項目
```json
{
  "operation": "putItem",
  "tableName": "Users",
  "item": {
    "userId": {
      "S": "{{ $json.userId }}"
    },
    "email": {
      "S": "{{ $json.email }}"
    },
    "profile": {
      "M": {
        "name": { "S": "{{ $json.name }}" },
        "age": { "N": "{{ $json.age.toString() }}" },
        "preferences": {
          "SS": "{{ $json.preferences }}"
        }
      }
    },
    "createdAt": {
      "S": "{{ new Date().toISOString() }}"
    }
  }
}
```

#### 2. 查詢項目
```json
{
  "operation": "query",
  "tableName": "Orders",
  "keyConditionExpression": "userId = :userId AND orderDate BETWEEN :startDate AND :endDate",
  "expressionAttributeValues": {
    ":userId": { "S": "{{ $json.userId }}" },
    ":startDate": { "S": "{{ $json.startDate }}" },
    ":endDate": { "S": "{{ $json.endDate }}" }
  },
  "projectionExpression": "orderId, total, #status",
  "expressionAttributeNames": {
    "#status": "status"
  }
}
```

## 🔥 Google Cloud Firestore 配置與示例

### 基本配置
```json
{
  "serviceAccountKey": {
    "type": "service_account",
    "project_id": "your-project-id",
    "private_key_id": "key-id",
    "private_key": "-----BEGIN PRIVATE KEY-----\n...\n-----END PRIVATE KEY-----\n",
    "client_email": "your-service@your-project.iam.gserviceaccount.com",
    "client_id": "your-client-id"
  },
  "databaseId": "(default)"
}
```

### 常用操作示例

#### 1. 創建文檔
```json
{
  "operation": "create",
  "collection": "users",
  "documentId": "{{ $json.userId }}",
  "fields": {
    "email": "{{ $json.email }}",
    "profile": {
      "displayName": "{{ $json.name }}",
      "avatar": "{{ $json.avatarUrl }}",
      "preferences": {
        "theme": "dark",
        "notifications": true
      }
    },
    "metadata": {
      "createdAt": "{{ new Date().toISOString() }}",
      "source": "n8n_automation"
    }
  }
}
```

#### 2. 查詢集合
```json
{
  "operation": "getAll",
  "collection": "products",
  "filters": [
    {
      "field": "category",
      "operation": "==",
      "value": "{{ $json.category }}"
    },
    {
      "field": "price",
      "operation": "<=",
      "value": "{{ parseFloat($json.maxPrice) }}"
    }
  ],
  "orderBy": [
    {
      "field": "rating",
      "direction": "desc"
    }
  ],
  "limit": 20
}
```

## 🔄 常見工作流模式

### 1. 數據同步模式
```yaml
Trigger (Webhook/Schedule) 
  → Source System (API/Database)
  → Transform Data (Code Node)
  → Target System (Database/Storage)
  → Notification (Email/Slack)
```

### 2. 實時處理模式
```yaml
Trigger (Webhook)
  → Validate Data (IF Node)
  → Cache Check (Redis Get)
  → Database Operation (PostgreSQL/MongoDB)
  → Update Cache (Redis Set)
  → Response (HTTP Response)
```

### 3. 批量處理模式
```yaml
Schedule Trigger
  → Source Data (Database Query)
  → Split in Batches (Split in Batches Node)
  → Process Each Batch (Loop)
  → Aggregate Results (Merge Node)
  → Generate Report (Email/File)
```

## ⚠️ 錯誤處理和重試策略

### 1. 數據庫連接錯誤
```json
{
  "continueOnFail": true,
  "retryOnFail": true,
  "maxRetries": 3,
  "retryInterval": 1000,
  "onError": "continueRegularOutput"
}
```

### 2. 數據驗證錯誤
```javascript
// 在 Code Node 中使用
if (!$input.first().json.email || !$input.first().json.email.includes('@')) {
  throw new Error('Invalid email format');
}

// 或使用 IF Node 進行條件判斷
// Expression: {{ $json.email && $json.email.includes('@') }}
```

### 3. 事務回滾
```sql
-- PostgreSQL 事務示例
BEGIN;
  INSERT INTO orders (user_id, total) VALUES ($1, $2);
  UPDATE inventory SET quantity = quantity - $3 WHERE product_id = $4;
  INSERT INTO audit_log (action, details) VALUES ('order_created', $5);
COMMIT;
```

## 🚀 性能調優建議

### 1. 數據庫連接優化
```json
{
  "connectionTimeout": 30000,
  "queryTimeout": 60000,
  "maxConnections": 10,
  "idleTimeout": 300000
}
```

### 2. 批量操作
```javascript
// 使用批量插入而不是逐條插入
// Code Node 示例
const batchSize = 100;
const batches = [];
for (let i = 0; i < items.length; i += batchSize) {
  batches.push(items.slice(i, i + batchSize));
}

return batches.map(batch => ({
  json: { 
    operation: 'batchInsert',
    data: batch 
  }
}));
```

### 3. 索引策略
```sql
-- PostgreSQL 索引建議
CREATE INDEX idx_users_email ON users(email);
CREATE INDEX idx_orders_user_date ON orders(user_id, created_at);
CREATE INDEX idx_products_category_price ON products(category, price);
```

### 4. 緩存策略
```javascript
// Redis 緩存模式
// 1. 先檢查緩存
// 2. 如果缺失，查詢數據庫
// 3. 將結果存入緩存
const cacheKey = `user:${userId}:profile`;
const cachedData = await redis.get(cacheKey);

if (cachedData) {
  return JSON.parse(cachedData);
} else {
  const userData = await database.query('SELECT * FROM users WHERE id = $1', [userId]);
  await redis.setex(cacheKey, 3600, JSON.stringify(userData));
  return userData;
}
```

## 📊 監控和日誌

### 1. 性能監控
```javascript
// Code Node 性能監控
const startTime = Date.now();

// 執行數據庫操作
const result = await performDatabaseOperation();

const duration = Date.now() - startTime;
console.log(`Database operation took ${duration}ms`);

// 記錄到監控系統
if (duration > 5000) {
  console.warn(`Slow query detected: ${duration}ms`);
}
```

### 2. 錯誤日誌
```javascript
// 結構化錯誤日誌
try {
  await databaseOperation();
} catch (error) {
  const errorLog = {
    timestamp: new Date().toISOString(),
    operation: 'database_insert',
    error: error.message,
    stack: error.stack,
    input: $input.all(),
    workflowId: $workflow.id
  };
  
  console.error('Database operation failed:', JSON.stringify(errorLog));
  throw error;
}
```

這些示例和配置可以幫助你更有效地使用 n8n 的各種數據存儲節點。記住始終根據你的具體用例調整配置，並在生產環境中實施適當的錯誤處理和監控。
