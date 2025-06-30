# Asset List API Specification

This API allows clients to request a list of assets with pricing and metadata. Supports both REST and WebSocket interfaces.

---

## Endpoint (REST)

**URL:**  

---
## GET /api/assets

**Response:**  
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

WebSocket

URL: wss://example.com/ws/assets

```json
{
  "action": "subscribe",
  "channel": "assets"
}
```

Servert push example
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

Field descriptions:
```
Field
Type
Description
name
string
Asset symbol or name (e.g., “BTC/USD”)
category
string
Category (e.g., “crypto”, “equity”, “forex”)
price_mantissa
string
Integer representing the price before decimal shifting
decimals
integer
Number of decimals to left-shift the mantissa
timestamp_ms
integer
Milliseconds since UNIX epoch
```

Price Conversion Example

float_price = int(price_mantissa) / (10 ** decimals)
