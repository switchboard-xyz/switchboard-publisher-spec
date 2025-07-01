# Asset List API Integration Guide

## Overview

This API provides real-time and historical asset pricing data with comprehensive metadata. The service supports both REST endpoints for one-time queries and WebSocket connections for continuous data streaming.

## API Endpoints

### REST Interface

#### Get All Assets

Returns a snapshot of all available assets across all categories with current pricing and metadata.

**Endpoint:** `GET /api/assets/all`

**Base URL:** `https://api.example.com`

**Full URL:** `https://api.example.com/api/assets/all`

**Query Parameters:**

| Parameter | Type | Description | Required |
|-----------|------|-------------|----------|
| `includePricing` | boolean | Include API usage pricing information | No |
| `verbose` | boolean | Include trading hours and active status information | No |

**Example Requests:**

All assets across all categories:
```
GET https://api.example.com/api/assets/all
```

All assets with pricing information:
```
GET https://api.example.com/api/assets/all?includePricing=true
```

All assets with verbose information:
```
GET https://api.example.com/api/assets/all?verbose=true
```

**Response Format (Basic):**

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
    },
    {
      "name": "AAPL",
      "category": "equity",
      "priceMantissa": "17500",
      "decimals": 2,
      "timestampMs": 1719964800789
    },
    {
      "name": "GOOGL",
      "category": "equity",
      "priceMantissa": "14200",
      "decimals": 2,
      "timestampMs": 1719964800890
    },
    {
      "name": "EUR/USD",
      "category": "forex",
      "priceMantissa": "108753",
      "decimals": 5,
      "timestampMs": 1719964800234
    },
    {
      "name": "GBP/USD",
      "category": "forex",
      "priceMantissa": "127615",
      "decimals": 5,
      "timestampMs": 1719964800345
    }
  ]
}
```

**Response Format (With Pricing):**

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
    },
    {
      "name": "AAPL",
      "category": "equity",
      "priceMantissa": "17500",
      "decimals": 2,
      "timestampMs": 1719964800789,
      "apiPricing": {
        "perRequestUsd": "0.00005",
        "monthlySubscriptionUsd": "49.99"
      }
    },
    {
      "name": "EUR/USD",
      "category": "forex",
      "priceMantissa": "108753",
      "decimals": 5,
      "timestampMs": 1719964800234,
      "apiPricing": {
        "perRequestUsd": "0.00002",
        "monthlySubscriptionUsd": "29.99"
      }
    }
  ]
}
```

**Response Format (With Verbose):**

```json
{
  "assets": [
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
    },
    {
      "name": "AAPL",
      "category": "equity",
      "priceMantissa": "17500",
      "decimals": 2,
      "timestampMs": 1719964800789,
      "activeHours": {
        "schedule": "30-59 9 * * 1-5, 0-59 10-15 * * 1-5, 0 16 * * 1-5",
        "timezone": "America/New_York",
        "description": "NYSE hours: 9:30 AM - 4:00 PM ET, Monday-Friday"
      },
      "isActive": false,
      "afterHoursAvailable": true
    },
    {
      "name": "EUR/USD",
      "category": "forex",
      "priceMantissa": "108753",
      "decimals": 5,
      "timestampMs": 1719964800234,
      "activeHours": {
        "schedule": "0 22 * * 0-4, * 0-21 * * 1-5, * 22-23 * * 1-4",
        "timezone": "UTC",
        "description": "Forex market: Sunday 10 PM - Friday 10 PM UTC"
      },
      "isActive": true,
      "afterHoursAvailable": false
    }
  ]
}
```

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

#### Connection Management

**Keepalive Mechanism:**

The WebSocket connection uses ping/pong frames for keepalive:

- **Server → Client**: Server sends ping frame every 30 seconds
- **Client → Server**: Client must respond with pong frame
- **Timeout**: Connection closed if no pong received within 10 seconds
- **Client Ping**: Clients may also send ping frames (server will respond with pong)

**Reconnection Strategy:**

```json
{
  "action": "ping"
}
```

Server response:
```json
{
  "action": "pong",
  "timestampMs": 1719964800123
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

## Type Definitions

### Rust Trait

```rust
use serde::{Deserialize, Serialize};

#[derive(Debug, Clone, Serialize, Deserialize)]
#[serde(rename_all = "camelCase")]
pub struct Asset {
    pub name: String,
    pub category: String,
    pub price_mantissa: String,
    pub decimals: u8,
    pub timestamp_ms: u64,
    #[serde(skip_serializing_if = "Option::is_none")]
    pub api_pricing: Option<ApiPricing>,
    #[serde(skip_serializing_if = "Option::is_none")]
    pub active_hours: Option<ActiveHours>,
    #[serde(skip_serializing_if = "Option::is_none")]
    pub is_active: Option<bool>,
    #[serde(skip_serializing_if = "Option::is_none")]
    pub after_hours_available: Option<bool>,
}

#[derive(Debug, Clone, Serialize, Deserialize)]
#[serde(rename_all = "camelCase")]
pub struct ApiPricing {
    pub per_request_usd: String,
    pub monthly_subscription_usd: String,
}

#[derive(Debug, Clone, Serialize, Deserialize)]
#[serde(rename_all = "camelCase")]
pub struct ActiveHours {
    pub schedule: String,
    pub timezone: String,
    pub description: String,
}

#[derive(Debug, Clone, Serialize, Deserialize)]
pub struct AllAssetsResponse {
    pub assets: Vec<Asset>,
}

#[derive(Debug, Clone, Serialize, Deserialize)]
pub struct AssetsResponse {
    pub assets: Vec<Asset>,
}

#[derive(Debug, Clone, Serialize, Deserialize)]
#[serde(rename_all = "camelCase")]
pub struct AssetsRequest {
    pub category: String,
    #[serde(skip_serializing_if = "Option::is_none")]
    pub assets: Option<Vec<String>>,
    #[serde(skip_serializing_if = "Option::is_none")]
    pub include_pricing: Option<bool>,
    #[serde(skip_serializing_if = "Option::is_none")]
    pub verbose: Option<bool>,
}

#[derive(Debug, Clone, Serialize, Deserialize)]
#[serde(rename_all = "camelCase")]
pub struct AssetRequest {
    pub asset_name: String,
    pub category: String,
    #[serde(skip_serializing_if = "Option::is_none")]
    pub include_pricing: Option<bool>,
    #[serde(skip_serializing_if = "Option::is_none")]
    pub verbose: Option<bool>,
}

pub trait AssetProvider {
    /// Get all assets across all categories
    async fn get_all_assets(&self, include_pricing: bool, verbose: bool) -> Result<AllAssetsResponse, Box<dyn std::error::Error>>;
    
    /// Get assets for a specific category
    async fn get_assets(&self, request: AssetsRequest) -> Result<AssetsResponse, Box<dyn std::error::Error>>;
    
    /// Get a specific asset
    async fn get_asset(&self, request: AssetRequest) -> Result<Asset, Box<dyn std::error::Error>>;
    
    /// Calculate the float price from mantissa and decimals
    fn calculate_price(asset: &Asset) -> f64 {
        let mantissa: u64 = asset.price_mantissa.parse().unwrap_or(0);
        mantissa as f64 / 10_f64.powi(asset.decimals as i32)
    }
}
```

### TypeScript Interface

```typescript
export interface Asset {
  name: string;
  category: string;
  priceMantissa: string;
  decimals: number;
  timestampMs: number;
  apiPricing?: ApiPricing;
  activeHours?: ActiveHours;
  isActive?: boolean;
  afterHoursAvailable?: boolean;
}

export interface ApiPricing {
  perRequestUsd: string;
  monthlySubscriptionUsd: string;
}

export interface ActiveHours {
  schedule: string;
  timezone: string;
  description: string;
}

export interface AllAssetsResponse {
  assets: Asset[];
}

export interface AssetsResponse {
  assets: Asset[];
}

export interface AssetsRequest {
  category: string;
  assets?: string[];
  includePricing?: boolean;
  verbose?: boolean;
}

export interface AssetRequest {
  assetName: string;
  category: string;
  includePricing?: boolean;
  verbose?: boolean;
}

export interface AssetProvider {
  /**
   * Get all assets across all categories
   */
  getAllAssets(includePricing?: boolean, verbose?: boolean): Promise<AllAssetsResponse>;
  
  /**
   * Get assets for a specific category
   */
  getAssets(request: AssetsRequest): Promise<AssetsResponse>;
  
  /**
   * Get a specific asset
   */
  getAsset(request: AssetRequest): Promise<Asset>;
}

/**
 * Calculate the float price from mantissa and decimals
 */
export function calculatePrice(asset: Asset): number {
  const mantissa = parseInt(asset.priceMantissa);
  return mantissa / Math.pow(10, asset.decimals);
}
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

interface AllAssetsResponse {
  assets: Asset[];
}

// Get all assets across all categories
const allAssetsResponse = await fetch('https://api.example.com/api/assets/all', {
  method: 'GET',
  headers: {
    'Content-Type': 'application/json',
  }
});
const allAssets: AllAssetsResponse = await allAssetsResponse.json();

// Process all assets
allAssets.assets.forEach((asset) => {
  const price = parseInt(asset.priceMantissa) / Math.pow(10, asset.decimals);
  console.log(`${asset.name} (${asset.category}): $${price.toFixed(2)}`);
});
console.log(`\nTotal assets: ${allAssets.assets.length}`);

// Get all assets with pricing information
const allAssetsWithPricingResponse = await fetch('https://api.example.com/api/assets/all?includePricing=true', {
  method: 'GET',
  headers: {
    'Content-Type': 'application/json',
  }
});
const allAssetsWithPricing: AllAssetsResponse = await allAssetsWithPricingResponse.json();

// Process assets with pricing info
allAssetsWithPricing.assets.forEach((asset) => {
  const price = parseInt(asset.priceMantissa) / Math.pow(10, asset.decimals);
  if (asset.apiPricing) {
    console.log(`${asset.name} (${asset.category}): $${price.toFixed(2)} - API: $${asset.apiPricing.perRequestUsd}/req, $${asset.apiPricing.monthlySubscriptionUsd}/month`);
  }
});

// Get all assets with verbose information
const allAssetsVerboseResponse = await fetch('https://api.example.com/api/assets/all?verbose=true', {
  method: 'GET',
  headers: {
    'Content-Type': 'application/json',
  }
});
const allAssetsVerbose: AllAssetsResponse = await allAssetsVerboseResponse.json();

// Process assets with trading hours
allAssetsVerbose.assets.forEach((asset) => {
  const price = parseInt(asset.priceMantissa) / Math.pow(10, asset.decimals);
  const status = asset.isActive ? 'OPEN' : 'CLOSED';
  console.log(`${asset.name} (${asset.category}): $${price.toFixed(2)} - Market: ${status}`);
  if (asset.activeHours) {
    console.log(`  Hours: ${asset.activeHours.description}`);
  }
});

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
  action?: string;
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

// Ping interval for keepalive
let pingInterval: NodeJS.Timeout;

ws.onopen = () => {
  // Set up ping interval (every 25 seconds to stay under 30s server timeout)
  pingInterval = setInterval(() => {
    if (ws.readyState === WebSocket.OPEN) {
      ws.send(JSON.stringify({ action: 'ping' }));
    }
  }, 25000);
  
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
  
  // Handle pong response
  if (data.action === 'pong') {
    console.log('Pong received, connection alive');
    return;
  }
  
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
  // Clean up ping interval
  if (pingInterval) {
    clearInterval(pingInterval);
  }
  
  // Implement reconnection logic here
  if (event.code !== 1000) { // 1000 = normal closure
    console.log('Unexpected disconnect, reconnecting in 5 seconds...');
    setTimeout(() => {
      // Reconnect logic
    }, 5000);
  }
};
```

## Rate Limits and Best Practices

1. **REST API**: Limit requests to 10 per second per IP address
2. **WebSocket**: 
   - Maintain a single connection per client application
   - Implement ping/pong keepalive every 25-30 seconds
   - Handle automatic reconnection on unexpected disconnects
3. **Error Handling**: Implement exponential backoff for connection failures
4. **Data Validation**: Always validate decimal places before price calculation
5. **Connection Management**: 
   - Monitor WebSocket connection state
   - Clean up resources (intervals, listeners) on disconnect
   - Queue messages during reconnection attempts

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