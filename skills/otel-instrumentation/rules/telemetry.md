# Telemetry Signal Overview

OpenTelemetry defines three signal types that work together to provide complete observability.

## Signal Types

### Traces
Represent requests flowing through distributed systems.

**When to use:**
- Request/response cycles
- Cross-service communication
- Error debugging and troubleshooting
- Understanding system flow

**Structure:**
```
Trace (overall request)
├── Span (service A)
│   ├── Span (database query)
│   └── Span (external API call)
└── Span (service B)
    └── Span (cache lookup)
```

### Metrics  
Numerical measurements aggregated over time.

**When to use:**
- System health monitoring
- Performance trends
- Alerting and SLOs
- Capacity planning

**Types:**
- **Counter**: Monotonic increasing (requests, errors)
- **Gauge**: Point-in-time values (CPU, memory)
- **Histogram**: Distribution of values (latency, size)

### Logs
Discrete events with timestamps and context.

**When to use:**
- Debugging specific events
- Audit trails
- Business events
- Detailed error information

## Signal Correlation

### Trace-Log Correlation
Link logs to traces using trace and span IDs:

```javascript
logger.info('User logged in', {
  user_id: hashUserId(userId),
  trace_id: trace.getActiveSpan()?.spanContext().traceId,
  span_id: trace.getActiveSpan()?.spanContext().spanId
});
```

### Span-Metric Correlation
Create metrics from span data:

```python
# Record metric when span ends
with tracer.start_as_current_span("api.request") as span:
    start_time = time.time()
    try:
        # Business logic
        result = process_request()
        span.set_status(trace.Status(trace.StatusCode.OK))
    except Exception as e:
        span.set_status(trace.Status(trace.StatusCode.ERROR, str(e)))
        span.record_exception(e)
        error_counter.add(1, {"service": "api", "error": type(e).__name__})
    finally:
        duration = time.time() - start_time
        latency_histogram.record(duration, {"service": "api"})
```

### Resource Consistency
Use identical resource attributes across all signals:

```yaml
resource:
  service.name: "user-api"
  service.version: "1.2.0"
  deployment.environment: "production"
  k8s.cluster.name: "prod-us-west-2"
  k8s.namespace.name: "services"
```

## Data Relationships

### Hierarchical Structure
```
Service (Resource) 
├── Request (Trace)
│   ├── Database Call (Span) → Query Metrics
│   ├── Cache Call (Span) → Cache Metrics  
│   └── External API (Span) → HTTP Metrics
└── Error Event (Log) → Error Metrics
```

### Temporal Alignment
Ensure timestamps are synchronized:
- Use consistent time sources
- Account for clock skew in distributed systems
- Align metric collection intervals with trace durations

## Best Practices

### Signal Selection
- **High-frequency operations**: Metrics + sampling
- **Critical business flows**: Full traces
- **Error scenarios**: Traces + logs
- **Performance analysis**: Metrics + exemplars

### Context Propagation
Always propagate context between:
- HTTP requests
- Message queue operations  
- Database transactions
- Async operations

### Attribute Consistency
Use semantic conventions for common attributes:
- `http.method`, `http.status_code` for HTTP
- `db.system`, `db.operation` for databases
- `messaging.system`, `messaging.operation` for queues