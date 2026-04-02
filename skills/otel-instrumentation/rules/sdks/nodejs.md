<!-- Copyright 2026 Dynatrace LLC

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    https://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License. -->

# Node.js OpenTelemetry Instrumentation

Production patterns for instrumenting Node.js applications with OpenTelemetry.

## Quick Start

### Installation

```bash
npm install @opentelemetry/auto-instrumentations-node
```

### Environment Setup

```bash
export OTEL_SERVICE_NAME="my-service"
export OTEL_TRACES_EXPORTER="otlp"
export OTEL_METRICS_EXPORTER="otlp" 
export OTEL_LOGS_EXPORTER="otlp"
export OTEL_EXPORTER_OTLP_ENDPOINT="https://your-collector-endpoint"
export OTEL_EXPORTER_OTLP_HEADERS="Authorization=Bearer YOUR_TOKEN"
export OTEL_EXPORTER_OTLP_PROTOCOL="grpc"  # Important for gRPC receivers
```

### Auto-Instrumentation Activation

**ESM Projects:**

```bash
export NODE_OPTIONS="--import @opentelemetry/auto-instrumentations-node/register"
node app.js
```

**CommonJS Projects:**

```bash
export NODE_OPTIONS="--require @opentelemetry/auto-instrumentations-node/register"
node app.js
```

## Manual SDK Configuration

### Basic Setup

```javascript
// tracing.js
const { NodeSDK } = require('@opentelemetry/sdk-node');
const { getNodeAutoInstrumentations } = require('@opentelemetry/auto-instrumentations-node');
const { OTLPTraceExporter } = require('@opentelemetry/exporter-otlp-grpc');
const { Resource } = require('@opentelemetry/resources');
const { SEMRESATTRS_SERVICE_NAME, SEMRESATTRS_SERVICE_VERSION } = require('@opentelemetry/semantic-conventions');

// Configure resource attributes
const resource = Resource.default().merge(
  new Resource({
    [SEMRESATTRS_SERVICE_NAME]: process.env.SERVICE_NAME || 'unknown',
    [SEMRESATTRS_SERVICE_VERSION]: process.env.SERVICE_VERSION || '0.0.0',
    'deployment.environment': process.env.NODE_ENV || 'development',
    'cloud.region': process.env.AWS_REGION || process.env.CLOUD_REGION,
  }),
);

// Configure exporters
const traceExporter = new OTLPTraceExporter({
  url: process.env.OTEL_EXPORTER_OTLP_TRACES_ENDPOINT || 'http://localhost:4317',
  headers: {
    Authorization: `Bearer ${process.env.OTEL_TOKEN}`
  }
});

// Initialize SDK
const sdk = new NodeSDK({
  resource,
  traceExporter,
  instrumentations: [getNodeAutoInstrumentations({
    // Disable problematic instrumentations if needed
    '@opentelemetry/instrumentation-fs': { enabled: false },
    
    // Configure HTTP instrumentation
    '@opentelemetry/instrumentation-http': {
      enabled: true,
      ignoreIncomingRequestHook: (req) => {
        // Ignore health checks and metrics endpoints
        return req.url?.includes('/health') || req.url?.includes('/metrics');
      },
      ignoreOutgoingRequestHook: (options) => {
        // Ignore requests to telemetry backends
        return options.hostname?.includes('otel-collector');
      }
    }
  })]
});

// Start instrumentation
sdk.start();

// Graceful shutdown
process.on('SIGTERM', () => {
  sdk.shutdown()
    .then(() => console.log('Telemetry terminated'))
    .catch((error) => console.log('Error terminating telemetry', error))
    .finally(() => process.exit(0));
});
```

## Express.js Integration

### Complete Setup

```javascript
// app.js
require('./tracing'); // Initialize before importing other modules

const express = require('express');
const { trace, context } = require('@opentelemetry/api');

const app = express();
const tracer = trace.getTracer('my-service');

// Middleware to add business context
app.use((req, res, next) => {
  const span = trace.getActiveSpan();
  if (span) {
    // Add safe request context
    span.setAttributes({
      'user.id': hashUserId(req.headers['x-user-id']),
      'tenant.id': req.headers['x-tenant-id'],
      'request.id': req.headers['x-request-id'],
      'http.route': req.route?.path || req.path
    });
  }
  next();
});

// Custom business span
app.post('/orders', async (req, res) => {
  return tracer.startActiveSpan('order.process', async (span) => {
    try {
      span.setAttributes({
        'order.id': req.body.orderId,
        'order.amount': req.body.amount,
        'order.currency': req.body.currency
      });
      
      const order = await processOrder(req.body);
      
      span.setStatus({ code: SpanStatusCode.OK });
      res.json({ success: true, orderId: order.id });
      
    } catch (error) {
      span.setStatus({
        code: SpanStatusCode.ERROR,
        message: error.message
      });
      span.recordException(error);
      
      res.status(500).json({ error: 'Order processing failed' });
    }
  });
});

// Error handling middleware
app.use((error, req, res, next) => {
  const span = trace.getActiveSpan();
  if (span) {
    span.setStatus({
      code: SpanStatusCode.ERROR,
      message: error.message
    });
    span.recordException(error);
    span.setAttributes({
      'error.type': error.constructor.name,
      'error.stack': error.stack
    });
  }
  
  res.status(500).json({ error: 'Internal Server Error' });
});
```

### Async Context Handling

```javascript
async function processOrderItems(items) {
  // Context is automatically propagated in async/await
  const promises = items.map(async (item) => {
    return tracer.startActiveSpan('order.item.process', async (span) => {
      span.setAttributes({
        'item.sku': item.sku,
        'item.quantity': item.quantity
      });
      
      // Nested async operations maintain context
      const inventory = await checkInventory(item.sku);
      const pricing = await calculatePrice(item);
      
      return { ...item, inventory, pricing };
    });
  });
  
  return Promise.all(promises);
}
```

## Database Integration

### MongoDB

```javascript
const { MongoClient } = require('mongodb');

// Auto-instrumentation covers basic operations
// Add custom spans for business logic
async function findUserOrders(userId) {
  return tracer.startActiveSpan('user.orders.fetch', async (span) => {
    span.setAttributes({
      'user.id': hashUserId(userId),
      'db.operation': 'find',
      'db.collection.name': 'orders'
    });
    
    try {
      const orders = await db.collection('orders')
        .find({ userId: hashUserId(userId) })
        .toArray();
      
      span.setAttributes({
        'db.result.count': orders.length
      });
      
      return orders;
    } catch (error) {
      span.recordException(error);
      throw error;
    }
  });
}
```

### PostgreSQL with pg

```javascript
const { Pool } = require('pg');

// Custom query wrapper with instrumentation
class InstrumentedPool extends Pool {
  async query(text, params, callback) {
    return tracer.startActiveSpan('db.query', async (span) => {
      span.setAttributes({
        'db.system': 'postgresql',
        'db.statement': sanitizeSqlQuery(text),
        'db.operation': extractSqlOperation(text)
      });
      
      try {
        const result = await super.query(text, params, callback);
        span.setAttributes({
          'db.result.count': result.rowCount
        });
        return result;
      } catch (error) {
        span.recordException(error);
        throw error;
      }
    });
  }
}

function sanitizeSqlQuery(query) {
  return query
    .replace(/\$\d+/g, '?')  // Replace parameter placeholders
    .replace(/'[^']*'/g, "'?'")  // Replace string literals
    .substring(0, 1000);  // Limit length
}
```

## HTTP Client Instrumentation

### Axios Configuration

```javascript
const axios = require('axios');
const { trace } = require('@opentelemetry/api');

// Create instrumented client
const apiClient = axios.create({
  timeout: 5000,
  headers: {
    'User-Agent': 'MyService/1.0'
  }
});

// Request interceptor
apiClient.interceptors.request.use((config) => {
  const span = trace.getActiveSpan();
  if (span) {
    // Add correlation headers
    config.headers['X-Trace-Id'] = span.spanContext().traceId;
    config.headers['X-Span-Id'] = span.spanContext().spanId;
  }
  return config;
});

// Response interceptor
apiClient.interceptors.response.use(
  (response) => {
    const span = trace.getActiveSpan();
    if (span) {
      span.setAttributes({
        'http.response.status_code': response.status,
        'http.response.body.size': JSON.stringify(response.data).length
      });
    }
    return response;
  },
  (error) => {
    const span = trace.getActiveSpan();
    if (span) {
      span.setStatus({
        code: SpanStatusCode.ERROR,
        message: error.message
      });
      span.recordException(error);
    }
    return Promise.reject(error);
  }
);
```

## Metrics Integration

### Custom Metrics

```javascript
const { metrics } = require('@opentelemetry/api');

// Get meter
const meter = metrics.getMeter('my-service', '1.0.0');

// Create instruments
const httpRequestsTotal = meter.createCounter('http_requests_total', {
  description: 'Total number of HTTP requests'
});

const httpRequestDuration = meter.createHistogram('http_request_duration_seconds', {
  description: 'Duration of HTTP requests in seconds'
});

// Use in middleware
app.use((req, res, next) => {
  const startTime = Date.now();
  
  res.on('finish', () => {
    const duration = (Date.now() - startTime) / 1000;
    
    const labels = {
      method: req.method,
      route: req.route?.path || 'unknown',
      status_code: res.statusCode.toString()
    };
    
    httpRequestsTotal.add(1, labels);
    httpRequestDuration.record(duration, labels);
  });
  
  next();
});
```

## Logging Integration

### Structured Logging with Winston

```javascript
const winston = require('winston');
const { trace } = require('@opentelemetry/api');

const logger = winston.createLogger({
  format: winston.format.combine(
    winston.format.timestamp(),
    winston.format.errors({ stack: true }),
    winston.format.json(),
    winston.format.printf((info) => {
      // Add trace context to logs
      const span = trace.getActiveSpan();
      if (span) {
        const spanContext = span.spanContext();
        info.trace_id = spanContext.traceId;
        info.span_id = spanContext.spanId;
      }
      
      // Sanitize sensitive data
      if (info.user) {
        info.user = {
          id: hashUserId(info.user.id),
          role: info.user.role
        };
      }
      
      return JSON.stringify(info);
    })
  ),
  transports: [
    new winston.transports.Console()
  ]
});

// Usage
logger.info('User order created', {
  user: { id: userId, role: userRole },
  order: { id: orderId, amount: amount }
});
```

## Testing Patterns

### Span Testing

```javascript
// test/instrumentation.test.js
const { trace } = require('@opentelemetry/api');
const { NodeTracerProvider } = require('@opentelemetry/sdk-trace-node');
const { InMemorySpanExporter, SimpleSpanProcessor } = require('@opentelemetry/sdk-trace-node');

describe('Instrumentation', () => {
  let spanExporter;
  let provider;
  
  beforeEach(() => {
    spanExporter = new InMemorySpanExporter();
    provider = new NodeTracerProvider();
    provider.addSpanProcessor(new SimpleSpanProcessor(spanExporter));
    trace.setGlobalTracerProvider(provider);
  });
  
  afterEach(() => {
    spanExporter.reset();
  });
  
  it('should create spans for order processing', async () => {
    const tracer = trace.getTracer('test');
    
    await tracer.startActiveSpan('test.operation', async (span) => {
      span.setAttributes({ 'test.value': 'example' });
    });
    
    const spans = spanExporter.getFinishedSpans();
    expect(spans).toHaveLength(1);
    expect(spans[0].name).toBe('test.operation');
    expect(spans[0].attributes['test.value']).toBe('example');
  });
});
```

### Integration Testing

```javascript
// test/integration.test.js
const request = require('supertest');
const app = require('../app');

describe('API Integration', () => {
  it('should create proper spans for API requests', async () => {
    const response = await request(app)
      .post('/orders')
      .send({ orderId: '123', amount: 100 })
      .expect(200);
    
    // Verify spans were created
    const spans = getRecordedSpans();
    const orderSpan = spans.find(s => s.name === 'order.process');
    
    expect(orderSpan).toBeDefined();
    expect(orderSpan.attributes['order.id']).toBe('123');
  });
});
```

## Production Considerations

### Performance

```javascript
// Use sampling to control overhead
const { TraceIdRatioBasedSampler } = require('@opentelemetry/sdk-trace-base');

const sdk = new NodeSDK({
  sampler: new TraceIdRatioBasedSampler(0.1), // 10% sampling
  // ... other config
});
```

### Error Handling

```javascript
// Prevent instrumentation from crashing the app
process.on('uncaughtException', (error) => {
  console.error('Uncaught exception:', error);
  // Don't exit for instrumentation errors
  if (!error.message.includes('telemetry')) {
    process.exit(1);
  }
});
```

### Health Checks

```javascript
app.get('/health', (req, res) => {
  // Don't create spans for health checks
  res.json({ status: 'healthy' });
});
```
