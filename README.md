# Asset List API Integration Guide

## Overview

This API provides real-time and historical asset pricing data with comprehensive metadata. The service supports both REST endpoints for one-time queries and WebSocket connections for continuous data streaming.

## API Endpoints

### REST Interface

#### Get Asset List

Returns a snapshot of available assets with current pricing and metadata for a specific category. Optionally filter to specific assets.

**Endpoint:** `POST /api/assets`

**Base URL:** `https://api.example.com`

**Full URL:** `https://api.example.com/api/assets`

**Request Body:**

```json
{
  "category": "crypto",
  "assets": ["BTC-USD", "ETH-USD"],  // Optional
  "includePricing": true,            // Optional
  "verbose": true                     // Optional
}
```

**Request Body Parameters:**

| Parameter | Type | Description | Required |
|-----------|------|-------------|----------|
| `category` | string | Asset category (e.g., "crypto", "equity", "forex") | **Yes** |
| `assets` | string[] | Array of specific assets to retrieve | No |
| `includePricing` | boolean | Include API usage pricing information | No |
| `verbose` | boolean | Include trading hours and active status information | No |

**Example Requests:**

All crypto assets:
```json
POST https://api.example.com/api/assets
{
  "category": "crypto"
}
```

Specific crypto assets with pricing:
```json
POST https://api.example.com/api/assets
{
  "category": "crypto",
  "assets": ["BTC-USD", "ETH-USD", "SOL-USD"],
  "includePricing": true
}
```

#### Get Specific Asset

Returns pricing and metadata for a specific asset.

**Endpoint:** `POST /api/asset`

**Base URL:** `https://api.example.com`

**Full URL:** `https://api.example.com/api/asset`

**Request Body:**

```json
{
  "assetName": "BTC-USD",
  "category": "crypto",
  "includePricing": true,  // Optional
  "verbose": true          // Optional
}
```

**Request Body Parameters:**

| Parameter | Type | Description | Required |
|-----------|------|-------------|----------|
| `assetName` | string | Asset symbol or name (e.g., "BTC-USD", "ETH-USD") | **Yes** |
| `category` | string | Asset category (e.g., "crypto", "equity", "forex") | **Yes** |
| `includePricing` | boolean | Include API usage pricing information | No |
| `verbose` | boolean | Include trading hours and active status information | No |

**Example Requests:**

Bitcoin:
```json
POST https://api.example.com/api/asset
{
  "assetName": "BTC-USD",
  "category": "crypto"
}
```

Bitcoin with pricing:
```json
POST https://api.example.com/api/asset
{
  "assetName": "BTC-USD",
  "category": "crypto",
  "includePricing": true
}
```

**Response Format (List):**

```json
{
  "assets": [
    {
      "name": "BTC/USD",
      "category": "crypto",
      "priceMantissa": "6342000000",
      "decimals": 8,
      "timestampMs": 1719964800123
    },
    {
      "name": "ETH/USD",
      "category": "crypto",
      "priceMantissa": "342000000",
      "decimals": 8,
      "timestampMs": 1719964800456
    }
  ]
}
```

**Response Format (List with Pricing):**

```json
{
  "assets": [
    {
      "name": "BTC/USD",
      "category": "crypto",
      "priceMantissa": "6342000000",
      "decimals": 8,
      "timestampMs": 1719964800123,
      "apiPricing": {
        "perRequestUsd": "0.0001",
        "monthlySubscriptionUsd": "99.99"
      }
    },
    {
      "name": "ETH/USD",
      "category": "crypto",
      "priceMantissa": "342000000",
      "decimals": 8,
      "timestampMs": 1719964800456,
      "apiPricing": {
        "perRequestUsd": "0.0001",
        "monthlySubscriptionUsd": "99.99"
      }
    }
  ]
}
```

**Response Format (Single Asset):**

```json
{
  "name": "BTC/USD",
  "category": "crypto",
  "priceMantissa": "6342000000",
  "decimals": 8,
  "timestampMs": 1719964800123
}
```

**Response Format (Single Asset with Pricing):**

```json
{
  "name": "BTC/USD",
  "category": "crypto",
  "priceMantissa": "6342000000",
  "decimals": 8,
  "timestampMs": 1719964800123,
  "apiPricing": {
    "perRequestUsd": "0.0001",
    "monthlySubscriptionUsd": "99.99"
  }
}
```

**Response Format (Single Asset with Verbose Mode):**

```json
{
  "name": "BTC/USD",
  "category": "crypto",
  "priceMantissa": "6342000000",
  "decimals": 8,
  "timestampMs": 1719964800123,
  "activeHours": {
    "schedule": "* * * * *",
    "timezone": "UTC",
    "description": "24/7 trading"
  },
  "isActive": true,
  "afterHoursAvailable": false
}
```

**Response Format (Equity Asset with Verbose Mode):**

```json
{
  "name": "AAPL",
  "category": "equity",
  "priceMantissa": "17500",
  "decimals": 2,
  "timestampMs": 1719964800123,
  "activeHours": {
    "schedule": "30-59 9 * * 1-5, 0-59 10-15 * * 1-5, 0 16 * * 1-5",
    "timezone": "America/New_York",
    "description": "NYSE hours: 9:30 AM - 4:00 PM ET, Monday-Friday"
  },
  "isActive": false,
  "afterHoursAvailable": true
}
```

**Response Format (Forex Asset with Verbose Mode):**

```json
{
  "name": "EUR/USD",
  "category": "forex",
  "priceMantissa": "108753",
  "decimals": 5,
  "timestampMs": 1719964800123,
  "activeHours": {
    "schedule": "0 22 * * 0-4, * 0-21 * * 1-5, * 22-23 * * 1-4",
    "timezone": "UTC",
    "description": "Forex market: Sunday 10 PM - Friday 10 PM UTC"
  },
  "isActive": true,
  "afterHoursAvailable": false
}
```

### WebSocket Interface

For real-time updates, connect to the WebSocket endpoint and subscribe to the assets channel.

**WebSocket URL:** `wss://api.example.com/ws/assets`

#### Subscription Request

Subscribe to all assets in a category:

```json
{
  "action": "subscribe",
  "channel": "assets",
  "category": "crypto"
}
```

Subscribe with pricing information:

```json
{
  "action": "subscribe",
  "channel": "assets",
  "category": "crypto",
  "includePricing": true
}
```

Subscribe to specific assets in a category:

```json
{
  "action": "subscribe",
  "channel": "assets",
  "category": "crypto",
  "assets": ["BTC-USD", "ETH-USD", "SOL-USD"],
  "includePricing": true
}
```

Subscribe with verbose mode for trading hours:

```json
{
  "action": "subscribe",
  "channel": "assets",
  "category": "equity",
  "verbose": true
}
```

Subscribe to a single asset:

```json
{
  "action": "subscribe",
  "channel": "assets",
  "category": "crypto",
  "asset": "BTC-USD"
}
```

#### Server Push Updates

The server will push updates whenever asset prices change:

```json
{
  "assets": [
    {
      "name": "BTC/USD",
      "category": "crypto",
      "priceMantissa": "6342000000",
      "decimals": 8,
      "timestampMs": 1719964800123
    },
    {
      "name": "ETH/USD",
      "category": "crypto",
      "priceMantissa": "342000000",
      "decimals": 8,
      "timestampMs": 1719964800456
    }
  ]
}
```

## Data Schema

### Asset Object Fields

| Field | Type | Description |
|-------|------|-------------|
| `name` | string | Asset symbol or trading pair (e.g., "BTC/USD") |
| `category` | string | Asset category (e.g., "crypto", "equity", "forex") |
| `priceMantissa` | string | Integer representation of the price before decimal shifting |
| `decimals` | integer | Number of decimal places to apply to the mantissa |
| `timestampMs` | integer | Unix timestamp in milliseconds |
| `apiPricing` | object | Optional API usage pricing (when `includePricing=true`) |
| `activeHours` | object | Optional trading hours information (when `verbose=true`) |
| `isActive` | boolean | Optional current trading status (when `verbose=true`) |
| `afterHoursAvailable` | boolean | Optional after-hours trading availability (when `verbose=true`) |

### API Pricing Object Fields

| Field | Type | Description |
|-------|------|-------------|
| `perRequestUsd` | string | Cost per API request in USD |
| `monthlySubscriptionUsd` | string | Monthly subscription cost in USD for unlimited access to this asset |

### Active Hours Object Fields

| Field | Type | Description |
|-------|------|-------------|
| `schedule` | string | Cron expression for trading hours (minute hour day month weekday) |
| `timezone` | string | Timezone for the cron schedule (e.g., "UTC", "America/New_York") |
| `description` | string | Human-readable description of the schedule |

## Price Calculation

To convert the mantissa and decimals into a floating-point price:

```typescript
const floatPrice = parseInt(priceMantissa) / Math.pow(10, decimals);
```

### Example Calculation

For BTC/USD with:
- `priceMantissa`: "6342000000"
- `decimals`: 8

```typescript
const price = 6342000000 / Math.pow(10, 8);
// Result: 63.42
```

## Integration Examples

### REST API Example (TypeScript)

```typescript
// Type definitions
interface Asset {
  name: string;
  category: string;
  priceMantissa: string;
  decimals: number;
  timestampMs: number;
  apiPricing?: {
    perRequestUsd: string;
    monthlySubscriptionUsd: string;
  };
  activeHours?: {
    schedule: string;
    timezone: string;
    description: string;
  };
  isActive?: boolean;
  afterHoursAvailable?: boolean;
}

interface AssetsResponse {
  assets: Asset[];
}

interface AssetsRequest {
  category: string;
  assets?: string[];
  includePricing?: boolean;
  verbose?: boolean;
}

interface AssetRequest {
  assetName: string;
  category: string;
  includePricing?: boolean;
  verbose?: boolean;
}

// Get all crypto assets (category is required)
const response = await fetch('https://api.example.com/api/assets', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
  },
  body: JSON.stringify({
    category: 'crypto'
  } as AssetsRequest)
});
const data: AssetsResponse = await response.json();

data.assets.forEach((asset) => {
  const price = parseInt(asset.priceMantissa) / Math.pow(10, asset.decimals);
  console.log(`${asset.name}: $${price.toFixed(2)}`);
});

// Get crypto assets with pricing information
const pricingResponse = await fetch('https://api.example.com/api/assets', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
  },
  body: JSON.stringify({
    category: 'crypto',
    includePricing: true
  } as AssetsRequest)
});
const dataWithPricing: AssetsResponse = await pricingResponse.json();

dataWithPricing.assets.forEach((asset) => {
  const price = parseInt(asset.priceMantissa) / Math.pow(10, asset.decimals);
  if (asset.apiPricing) {
    const apiCost = asset.apiPricing.perRequestUsd;
    const monthly = asset.apiPricing.monthlySubscriptionUsd;
    console.log(`${asset.name}: $${price.toFixed(2)} ($${apiCost}/request, $${monthly}/month)`);
  }
});

// Get specific crypto assets
const selectedResponse = await fetch('https://api.example.com/api/assets', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
  },
  body: JSON.stringify({
    category: 'crypto',
    assets: ['BTC-USD', 'ETH-USD', 'SOL-USD']
  } as AssetsRequest)
});
const selectedCrypto: AssetsResponse = await selectedResponse.json();

selectedCrypto.assets.forEach((asset) => {
  const price = parseInt(asset.priceMantissa) / Math.pow(10, asset.decimals);
  console.log(`${asset.name}: $${price.toFixed(2)}`);
});

// Get a single asset with pricing
const btcResponse = await fetch('https://api.example.com/api/asset', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
  },
  body: JSON.stringify({
    assetName: 'BTC-USD',
    category: 'crypto',
    includePricing: true
  } as AssetRequest)
});
const btcData: Asset = await btcResponse.json();

const btcPrice = parseInt(btcData.priceMantissa) / Math.pow(10, btcData.decimals);
console.log(`Bitcoin: $${btcPrice.toFixed(2)}`);
if (btcData.apiPricing) {
  console.log(`  API Cost: $${btcData.apiPricing.perRequestUsd}/request`);
  console.log(`  Monthly: $${btcData.apiPricing.monthlySubscriptionUsd}`);
}

// Get specific stocks
const stocksResponse = await fetch('https://api.example.com/api/assets', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
  },
  body: JSON.stringify({
    category: 'equity',
    assets: ['AAPL', 'GOOGL', 'MSFT']
  } as AssetsRequest)
});
const techStocks: AssetsResponse = await stocksResponse.json();

techStocks.assets.forEach((asset) => {
  const price = parseInt(asset.priceMantissa) / Math.pow(10, asset.decimals);
  console.log(`${asset.name}: $${price.toFixed(2)}`);
});

// Get assets with verbose mode to see trading hours
const verboseResponse = await fetch('https://api.example.com/api/assets', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
  },
  body: JSON.stringify({
    category: 'equity',
    verbose: true
  } as AssetsRequest)
});
const verboseData: AssetsResponse = await verboseResponse.json();

verboseData.assets.forEach((asset) => {
  const price = parseInt(asset.priceMantissa) / Math.pow(10, asset.decimals);
  console.log(`${asset.name}: $${price.toFixed(2)}`);
  if (asset.activeHours) {
    console.log(`  Schedule: ${asset.activeHours.schedule}`);
    console.log(`  Timezone: ${asset.activeHours.timezone}`);
    console.log(`  Hours: ${asset.activeHours.description}`);
    console.log(`  Status: ${asset.isActive ? 'ACTIVE' : 'CLOSED'}`);
    console.log(`  After-hours: ${asset.afterHoursAvailable ? 'Available' : 'Not available'}`);
  }
});
```

### WebSocket Example (TypeScript)

```typescript
// Type definitions
interface Asset {
  name: string;
  category: string;
  priceMantissa: string;
  decimals: number;
  timestampMs: number;
  apiPricing?: {
    perRequestUsd: string;
    monthlySubscriptionUsd: string;
  };
  activeHours?: {
    schedule: string;
    timezone: string;
    description: string;
  };
  isActive?: boolean;
  afterHoursAvailable?: boolean;
}

interface AssetsMessage {
  assets?: Asset[];
  name?: string;
  category?: string;
  priceMantissa?: string;
  decimals?: number;
  timestampMs?: number;
  apiPricing?: {
    perRequestUsd: string;
    monthlySubscriptionUsd: string;
  };
}

interface SubscriptionRequest {
  action: 'subscribe';
  channel: 'assets';
  category: string;
  assets?: string[];
  asset?: string;
  includePricing?: boolean;
  verbose?: boolean;
}

const ws = new WebSocket('wss://api.example.com/ws/assets');

ws.onopen = () => {
  // Subscribe to all crypto assets
  const subscription1: SubscriptionRequest = {
    action: 'subscribe',
    channel: 'assets',
    category: 'crypto'
  };
  ws.send(JSON.stringify(subscription1));
  
  // Subscribe to crypto assets with pricing info
  const subscription2: SubscriptionRequest = {
    action: 'subscribe',
    channel: 'assets',
    category: 'crypto',
    includePricing: true
  };
  ws.send(JSON.stringify(subscription2));
  
  // Subscribe to specific crypto assets with pricing
  const subscription3: SubscriptionRequest = {
    action: 'subscribe',
    channel: 'assets',
    category: 'crypto',
    assets: ['BTC-USD', 'ETH-USD', 'SOL-USD'],
    includePricing: true
  };
  ws.send(JSON.stringify(subscription3));
  
  // Subscribe to specific forex pairs
  const subscription4: SubscriptionRequest = {
    action: 'subscribe',
    channel: 'assets',
    category: 'forex',
    assets: ['EUR-USD', 'GBP-USD', 'USD-JPY']
  };
  ws.send(JSON.stringify(subscription4));
};

ws.onmessage = (event: MessageEvent) => {
  const data: AssetsMessage = JSON.parse(event.data);
  
  // Handle array of assets (category or multiple asset subscription)
  if (data.assets) {
    data.assets.forEach((asset) => {
      const price = parseInt(asset.priceMantissa) / Math.pow(10, asset.decimals);
      let output = `${asset.name}: $${price.toFixed(2)}`;
      
      // Add pricing info if available
      if (asset.apiPricing) {
        output += ` (Cost: $${asset.apiPricing.perRequestUsd}/req, $${asset.apiPricing.monthlySubscriptionUsd}/mo)`;
      }
      
      console.log(output);
    });
  }
  
  // Handle single asset update
  if (data.name && !data.assets && data.priceMantissa && data.decimals !== undefined) {
    const price = parseInt(data.priceMantissa) / Math.pow(10, data.decimals);
    let output = `Update - ${data.name}: $${price.toFixed(2)}`;
    
    if (data.apiPricing) {
      output += ` (Cost: $${data.apiPricing.perRequestUsd}/req)`;
    }
    
    console.log(output);
  }
};

ws.onerror = (error: Event) => {
  console.error('WebSocket error:', error);
};

ws.onclose = (event: CloseEvent) => {
  console.log('WebSocket closed:', event.code, event.reason);
};
```

## Rate Limits and Best Practices

1. **REST API**: Limit requests to 10 per second per IP address
2. **WebSocket**: Maintain a single connection per client application
3. **Error Handling**: Implement exponential backoff for connection failures
4. **Data Validation**: Always validate decimal places before price calculation

## Error Responses

### HTTP Status Codes

- `200 OK`: Successful request
- `400 Bad Request`: Missing required parameters (e.g., category)
- `404 Not Found`: Asset not found
- `429 Too Many Requests`: Rate limit exceeded
- `500 Internal Server Error`: Server-side error
- `503 Service Unavailable`: Service temporarily unavailable

### Error Response Format

```json
{
  "error": {
    "code": "MISSING_REQUIRED_PARAMETER",
    "message": "The 'category' parameter is required."
  }
}
```

```json
{
  "error": {
    "code": "ASSET_NOT_FOUND",
    "message": "Asset 'BTC-USD' not found in category 'equity'."
  }
}
```

```json
{
  "error": {
    "code": "RATE_LIMIT_EXCEEDED",
    "message": "Too many requests. Please retry after 60 seconds."
  }
}
```