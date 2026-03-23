# Span Creation and Management

Spans represent units of work in distributed traces. Proper span creation is crucial for meaningful observability.

## Span Naming

### Naming Patterns

Follow semantic conventions for consistent naming:

```javascript
// HTTP requests
"GET /api/users/{id}"
"POST /api/orders"

// Database operations  
"db.query users"
"db.insert orders"

// External APIs
"http.client GET api.stripe.com"
"http.client POST api.sendgrid.com"

// Business operations
"order.process"
"payment.validate"
"user.authenticate"
```

### Anti-Patterns

```javascript
// ❌ Too generic
"request"
"database"
"function"

// ❌ Too specific (high cardinality)
"user-12345-order-67890"
"/api/users/12345"

// ❌ Including sensitive data
"authenticate-user@example.com"
"query-SELECT * FROM users WHERE ssn='123456789'"
```

## Span Kinds

Choose the appropriate span kind:

### SERVER
Handles incoming requests:

```python
with tracer.start_as_current_span(
    "POST /api/users",
    kind=trace.SpanKind.SERVER
) as span:
    span.set_attributes({
        "http.method": "POST",
        "http.route": "/api/users",
        "http.scheme": "https"
    })
```

### CLIENT  
Makes outgoing requests:

```go
ctx, span := tracer.Start(ctx, "http.client GET api.payment.com",
    trace.WithSpanKind(trace.SpanKindClient))
defer span.End()

span.SetAttributes(
    attribute.String("http.method", "GET"),
    attribute.String("http.url", "https://api.payment.com/validate"),
    attribute.String("server.address", "api.payment.com"),
)
```

### INTERNAL
Internal application operations:

```java
Span span = tracer.spanBuilder("order.validate")
    .setSpanKind(SpanKind.INTERNAL)
    .startSpan();
try (Scope scope = span.makeCurrent()) {
    // Business logic
    span.setAllAttributes(Attributes.of(
        AttributeKey.stringKey("order.id"), orderId,
        AttributeKey.doubleKey("order.amount"), amount
    ));
}
```

### PRODUCER/CONSUMER
Message queue operations:

```javascript
// Producer
const span = tracer.startSpan('order.events publish', {
  kind: SpanKind.PRODUCER,
  attributes: {
    'messaging.system': 'kafka',
    'messaging.destination': 'order-events',
    'messaging.operation': 'publish'
  }
});

// Consumer
const span = tracer.startSpan('order.events process', {
  kind: SpanKind.CONSUMER,
  attributes: {
    'messaging.system': 'kafka',
    'messaging.destination': 'order-events',
    'messaging.operation': 'receive'
  }
});
```

## Span Status

Set status based on operation outcomes:

### Success
```python
# Explicit success (optional, OK is default)
span.set_status(trace.Status(trace.StatusCode.OK))

# HTTP status codes
if status_code < 400:
    span.set_status(trace.Status(trace.StatusCode.OK))
```

### Errors
```javascript
try {
  await processOrder(order);
  // Success - no action needed
} catch (error) {
  // Set error status with description
  span.setStatus({
    code: SpanStatusCode.ERROR,
    message: error.message
  });
  
  // Record the exception
  span.recordException(error);
  
  // Add error attributes
  span.setAttributes({
    'error.type': error.constructor.name,
    'error.message': error.message
  });
}
```

### HTTP Status Mapping
```go
// Map HTTP status to span status
switch statusCode {
case 400, 401, 403, 404, 409, 422:
    // Client errors - usually not span errors
    span.SetStatus(codes.Unset, "")
case 500, 501, 502, 503, 504:
    // Server errors - mark span as error
    span.SetStatus(codes.Error, http.StatusText(statusCode))
default:
    if statusCode >= 400 {
        span.SetStatus(codes.Error, http.StatusText(statusCode))
    }
}
```

## Span Attributes

### Safe Attributes
Always safe to include:

```yaml
# Service identification
service.name: "user-api"
service.version: "1.2.0"

# Operation details
http.method: "POST"
http.route: "/api/users" 
http.status_code: 201

# Database operations
db.system: "postgresql"
db.operation: "INSERT"
db.collection.name: "users"

# Infrastructure
cloud.provider: "aws"
cloud.region: "us-west-2"
k8s.cluster.name: "prod"
```

### Conditional Attributes
Include only if safe:

```javascript
// Hash user IDs if correlation needed
if (userId && typeof userId === 'string' && userId.length < 50) {
  span.setAttribute('user.id', hashUserId(userId));
}

// Include request ID for debugging
if (requestId && /^[a-zA-Z0-9-]+$/.test(requestId)) {
  span.setAttribute('request.id', requestId);
}

// Sanitize URLs
if (url) {
  const sanitized = sanitizeUrl(url);
  span.setAttribute('http.url', sanitized);
}
```

### Never Include
```javascript
// ❌ Sensitive data
span.setAttribute('user.password', password);
span.setAttribute('api.key', apiKey);
span.setAttribute('credit.card', cardNumber);

// ❌ High cardinality
span.setAttribute('user.email', email);  // Use hash instead
span.setAttribute('sql.query', query);   // Use parameterized version

// ❌ Large payloads
span.setAttribute('request.body', JSON.stringify(body));
span.setAttribute('response.body', responseBody);
```

## Span Lifecycle

### Start Spans Correctly
```python
# ✅ Use context manager
with tracer.start_as_current_span("operation") as span:
    # Work happens here
    pass  # Span automatically ends

# ✅ Manual management
span = tracer.start_span("operation")
try:
    # Work happens here
    pass
finally:
    span.end()  # Always end spans
```

### End Spans Properly
```go
// ✅ Use defer
ctx, span := tracer.Start(ctx, "operation")
defer span.End()

// ❌ Don't forget to end
ctx, span := tracer.Start(ctx, "operation")
// Missing span.End() - memory leak!
```

### Context Propagation
```javascript
// ✅ Propagate context in async operations
async function processOrder(order) {
  return tracer.startActiveSpan('order.process', async (span) => {
    // Context is automatically propagated to child operations
    await validatePayment(order.payment);
    await updateInventory(order.items);
    return order;
  });
}

// ✅ Manual context passing
function processOrder(order, parentContext) {
  const span = tracer.startSpan('order.process', {}, parentContext);
  return context.with(trace.setSpan(parentContext, span), () => {
    // Work with proper context
    return doWork();
  });
}
```

## Advanced Patterns

### Span Links
Link related spans that aren't parent-child:

```java
// Link to spans from different traces
SpanBuilder spanBuilder = tracer.spanBuilder("batch.process")
    .addLink(relatedSpanContext1)
    .addLink(relatedSpanContext2);
```

### Span Events
Add timeline events within spans:

```python
span.add_event("validation.started")
# ... validation work ...
span.add_event("validation.completed", {
    "validation.result": "success",
    "validation.duration_ms": 150
})
```

### Sampling Decisions
Respect sampling decisions:

```javascript
// Check if span is being recorded
const span = trace.getActiveSpan();
if (span?.isRecording()) {
  // Only add expensive attributes if span is recorded
  span.setAttributes(computeExpensiveAttributes());
}
```