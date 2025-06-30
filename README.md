# Asset List API Integration Guide

## Overview

This API provides real-time and historical asset pricing data with comprehensive metadata. The service supports both REST endpoints for one-time queries and WebSocket connections for continuous data streaming.

## API Endpoints

### REST Interface

#### Get Asset List

Returns a snapshot of available assets with current pricing and metadata for a specific category.

**Endpoint:** `GET /api/assets`

**Base URL:** `https://api.example.com`

**Full URL:** `https://api.example.com/api/assets`

**Query Parameters:**

| Parameter | Type | Description | Required |
|-----------|------|-------------|----------|
| `category` | string | Asset category (e.g., "crypto", "equity", "forex") | **Yes** |

**Example Requests:**

- Crypto assets: `GET https://api.example.com/api/assets?category=crypto`
- Equity assets: `GET https://api.example.com/api/assets?category=equity`
- Forex assets: `GET https://api.example.com/api/assets?category=forex`

#### Get Specific Asset

Returns pricing and metadata for a specific asset.

**Endpoint:** `GET /api/assets/{asset_name}`

**Base URL:** `https://api.example.com`

**Path Parameters:**

| Parameter | Type | Description | Required |
|-----------|------|-------------|----------|
| `asset_name` | string | Asset symbol or name (e.g., "BTC-USD", "ETH-USD") | **Yes** |

**Query Parameters:**

| Parameter | Type | Description | Required |
|-----------|------|-------------|----------|
| `category` | string | Asset category (e.g., "crypto", "equity", "forex") | **Yes** |

**Example Requests:**

- Bitcoin: `GET https://api.example.com/api/assets/BTC-USD?category=crypto`
- Apple Stock: `GET https://api.example.com/api/assets/AAPL?category=equity`
- EUR/USD: `GET https://api.example.com/api/assets/EUR-USD?category=forex`

**Response Format (List):**

```json
{
  "assets": [
    {
      "name": "BTC/USD",
      "category": "crypto",
      "price_mantissa": "6342000000",
      "decimals": 8,
      "timestamp_ms": 1719964800123
    },
    {
      "name": "ETH/USD",
      "category": "crypto",
      "price_mantissa": "342000000",
      "decimals": 8,
      "timestamp_ms": 1719964800456
    }
  ]
}
```

**Response Format (Single Asset):**

```json
{
  "name": "BTC/USD",
  "category": "crypto",
  "price_mantissa": "6342000000",
  "decimals": 8,
  "timestamp_ms": 1719964800123
}
```

### WebSocket Interface

For real-time updates, connect to the WebSocket endpoint and subscribe to the assets channel.

**WebSocket URL:** `wss://api.example.com/ws/assets`

#### Subscription Request

Subscribe to assets by category (required):

```json
{
  "action": "subscribe",
  "channel": "assets",
  "category": "crypto"
}
```

Subscribe to a specific asset:

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
      "price_mantissa": "6342000000",
      "decimals": 8,
      "timestamp_ms": 1719964800123
    },
    {
      "name": "ETH/USD",
      "category": "crypto",
      "price_mantissa": "342000000",
      "decimals": 8,
      "timestamp_ms": 1719964800456
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
| `price_mantissa` | string | Integer representation of the price before decimal shifting |
| `decimals` | integer | Number of decimal places to apply to the mantissa |
| `timestamp_ms` | integer | Unix timestamp in milliseconds |

## Price Calculation

To convert the mantissa and decimals into a floating-point price:

```python
float_price = int(price_mantissa) / (10 ** decimals)
```

### Example Calculation

For BTC/USD with:
- `price_mantissa`: "6342000000"
- `decimals`: 8

```python
price = 6342000000 / (10 ** 8)
# Result: 63.42
```

## Integration Examples

### REST API Example (Python)

```python
import requests

# Get crypto assets (category is required)
response = requests.get('https://api.example.com/api/assets?category=crypto')
data = response.json()

for asset in data['assets']:
    price = int(asset['price_mantissa']) / (10 ** asset['decimals'])
    print(f"{asset['name']}: ${price:.2f}")

# Get a specific asset
response = requests.get('https://api.example.com/api/assets/BTC-USD?category=crypto')
btc_data = response.json()

btc_price = int(btc_data['price_mantissa']) / (10 ** btc_data['decimals'])
print(f"Bitcoin: ${btc_price:.2f}")

# Get equity assets
response = requests.get('https://api.example.com/api/assets?category=equity')
equity_data = response.json()

for asset in equity_data['assets']:
    price = int(asset['price_mantissa']) / (10 ** asset['decimals'])
    print(f"Stock: {asset['name']}: ${price:.2f}")
```

### WebSocket Example (JavaScript)

```javascript
const ws = new WebSocket('wss://api.example.com/ws/assets');

ws.onopen = () => {
  // Subscribe to crypto category (category is required)
  ws.send(JSON.stringify({
    action: 'subscribe',
    channel: 'assets',
    category: 'crypto'
  }));
  
  // Subscribe to a specific asset
  ws.send(JSON.stringify({
    action: 'subscribe',
    channel: 'assets',
    category: 'crypto',
    asset: 'ETH-USD'
  }));
};

ws.onmessage = (event) => {
  const data = JSON.parse(event.data);
  
  // Handle array of assets (category subscription)
  if (data.assets) {
    data.assets.forEach(asset => {
      const price = parseInt(asset.price_mantissa) / Math.pow(10, asset.decimals);
      console.log(`${asset.name}: $${price.toFixed(2)}`);
    });
  }
  
  // Handle single asset update
  if (data.name && !data.assets) {
    const price = parseInt(data.price_mantissa) / Math.pow(10, data.decimals);
    console.log(`Update - ${data.name}: $${price.toFixed(2)}`);
  }
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