# MedChain Backend Setup Guide

## Overview
This frontend expects a REST API backend. You can use **Node.js/Express** or **Python/FastAPI**.

---

## API Endpoints Required

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/api/verify-code` | Verify medicine by code |
| POST | `/api/scan-qr` | Verify medicine by QR data |
| GET | `/api/batches` | List all batches |
| GET | `/api/batches/:id` | Get batch details |
| GET | `/api/journey/:batchId` | Get supply chain journey |
| GET | `/api/alerts/:batchId` | Get batch alerts |
| POST | `/api/blockchain/verify` | Verify blockchain hash |

---

## Node.js/Express Backend Example

### 1. Initialize Project
```bash
mkdir medchain-backend && cd medchain-backend
npm init -y
npm install express cors dotenv mongoose
```

### 2. Create `server.js`
```javascript
const express = require('express');
const cors = require('cors');
require('dotenv').config();

const app = express();
app.use(cors());
app.use(express.json());

// Medicine/Batch Schema (MongoDB example)
const medicineSchema = {
  id: String,
  name: String,
  batchNumber: String,
  manufacturer: String,
  expiryDate: String,
  status: String, // 'verified' | 'warning' | 'expired' | 'recalled' | 'tampered' | 'duplicate'
  supplyChain: Array,
  qaStatus: Object,
  alerts: Array
};

// Routes
app.post('/api/verify-code', async (req, res) => {
  const { code } = req.body;
  // Query database for medicine by code
  // Return: { success: true, data: medicine }
  res.json({ success: true, data: {} });
});

app.post('/api/scan-qr', async (req, res) => {
  const { qrData } = req.body;
  // Parse QR and fetch medicine
  res.json({ success: true, data: {} });
});

app.get('/api/batches', async (req, res) => {
  // Return all batches
  res.json({ success: true, data: [] });
});

app.get('/api/batches/:id', async (req, res) => {
  const { id } = req.params;
  // Return batch by ID
  res.json({ success: true, data: {} });
});

app.get('/api/journey/:batchId', async (req, res) => {
  const { batchId } = req.params;
  // Return supply chain journey
  res.json({ success: true, data: [] });
});

app.get('/api/alerts/:batchId', async (req, res) => {
  const { batchId } = req.params;
  // Return alerts for batch
  res.json({ success: true, data: [] });
});

app.post('/api/blockchain/verify', async (req, res) => {
  const { hash } = req.body;
  // Verify blockchain hash
  res.json({ success: true, data: {} });
});

const PORT = process.env.PORT || 3001;
app.listen(PORT, () => console.log(`Server running on port ${PORT}`));
```

---

## Python/FastAPI Backend Example

### 1. Initialize Project
```bash
mkdir medchain-backend && cd medchain-backend
pip install fastapi uvicorn pymongo python-dotenv
```

### 2. Create `main.py`
```python
from fastapi import FastAPI
from fastapi.middleware.cors import CORSMiddleware
from pydantic import BaseModel
from typing import Optional, List

app = FastAPI()

app.add_middleware(
    CORSMiddleware,
    allow_origins=["*"],
    allow_methods=["*"],
    allow_headers=["*"],
)

class VerifyRequest(BaseModel):
    code: str

class ScanRequest(BaseModel):
    qrData: str

class BlockchainRequest(BaseModel):
    hash: str

@app.post("/api/verify-code")
async def verify_code(request: VerifyRequest):
    # Query database for medicine
    return {"success": True, "data": {}}

@app.post("/api/scan-qr")
async def scan_qr(request: ScanRequest):
    # Parse QR and fetch medicine
    return {"success": True, "data": {}}

@app.get("/api/batches")
async def get_batches():
    return {"success": True, "data": []}

@app.get("/api/batches/{batch_id}")
async def get_batch(batch_id: str):
    return {"success": True, "data": {}}

@app.get("/api/journey/{batch_id}")
async def get_journey(batch_id: str):
    return {"success": True, "data": []}

@app.get("/api/alerts/{batch_id}")
async def get_alerts(batch_id: str):
    return {"success": True, "data": []}

@app.post("/api/blockchain/verify")
async def verify_blockchain(request: BlockchainRequest):
    return {"success": True, "data": {}}

# Run: uvicorn main:app --reload --port 3001
```

---

## Connect Frontend to Backend

1. Create `.env` file in frontend root:
```
VITE_API_URL=http://localhost:3001/api
```

2. In `src/hooks/useMedicine.ts`, set:
```typescript
const USE_MOCK_DATA = false;
```

---

## Database Schema (MongoDB/PostgreSQL)

### Medicines Table
| Field | Type | Description |
|-------|------|-------------|
| id | string | Unique ID |
| name | string | Medicine name |
| batchNumber | string | Batch identifier |
| manufacturer | string | Manufacturer name |
| expiryDate | string | Expiry date (ISO) |
| status | enum | verified/warning/expired/recalled/tampered/duplicate |
| supplyChain | array | Array of SupplyChainNode |
| qaStatus | object | QA test results |
| alerts | array | Active alerts |

### SupplyChainNode
| Field | Type |
|-------|------|
| stage | enum (manufacturer/distributor/wholesaler/pharmacy/customer) |
| name | string |
| location | string |
| timestamp | string |
| isCompleted | boolean |
| isActive | boolean |
| block | BlockData |

### BlockData
| Field | Type |
|-------|------|
| hash | string |
| previousHash | string |
| timestamp | string |
| nonce | number |
| data | object |

---

## Next Steps
1. Set up your database (MongoDB/PostgreSQL)
2. Implement the API endpoints
3. Add blockchain integration (e.g., Hyperledger, Ethereum)
4. Set `VITE_API_URL` and `USE_MOCK_DATA = false`
