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

# Sensitive Data Protection

Prevent PII, credentials, and other sensitive information from entering telemetry data. Security must be built-in from the start.

## Never Instrument (Zero Tolerance)

These categories must NEVER appear in any telemetry:

### Authentication Data

```javascript
// ❌ NEVER do this
span.setAttribute('password', user.password);
span.setAttribute('api_key', process.env.API_KEY);
span.setAttribute('jwt_token', token);
span.setAttribute('session_cookie', req.cookies.session);

// ✅ Instead
span.setAttribute('auth.method', 'bearer_token');
span.setAttribute('auth.provider', 'oauth2');
```

### Financial Information

```python
# ❌ NEVER do this
span.set_attribute('credit_card', payment.card_number)
span.set_attribute('cvv', payment.cvv)
span.set_attribute('bank_account', payment.account)

# ✅ Instead  
span.set_attribute('payment.method', 'credit_card')
span.set_attribute('payment.provider', 'stripe')
span.set_attribute('payment.amount_cents', payment.amount)
```

### Personal Identifiers

```go
// ❌ NEVER do this
span.SetAttributes(
    attribute.String("user.email", user.Email),
    attribute.String("user.phone", user.Phone),
    attribute.String("user.ssn", user.SSN),
)

// ✅ Instead
span.SetAttributes(
    attribute.String("user.id", hashUserId(user.ID)),
    attribute.String("user.role", user.Role),
    attribute.String("user.tier", user.Tier),
)
```

## Safe Hashing Patterns

### User ID Hashing

```javascript
const crypto = require('crypto');

function hashUserId(userId) {
  if (!userId || typeof userId !== 'string') return 'anonymous';
  
  // Use HMAC with secret key for consistent hashing
  const hmac = crypto.createHmac('sha256', process.env.USER_ID_SALT);
  return hmac.update(userId).digest('hex').substring(0, 16);
}

// Usage
span.setAttribute('user.id', hashUserId(req.user?.id));
```

### Email Hashing for Correlation

```python
import hashlib
import hmac

def hash_email(email: str) -> str:
    if not email or '@' not in email:
        return 'invalid'
    
    # Use HMAC for secure hashing
    key = os.getenv('EMAIL_HASH_KEY', '').encode()
    return hmac.new(key, email.encode(), hashlib.sha256).hexdigest()[:16]

# Usage
span.set_attribute('user.email_hash', hash_email(user.email))
```

## URL Sanitization

### Query Parameter Removal

```javascript
function sanitizeUrl(url) {
  try {
    const parsed = new URL(url);
    
    // Remove sensitive parameters
    const sensitiveParams = [
      'token', 'api_key', 'session_id', 'auth', 'password',
      'secret', 'key', 'authorization', 'bearer', 'oauth'
    ];
    
    sensitiveParams.forEach(param => {
      if (parsed.searchParams.has(param)) {
        parsed.searchParams.delete(param);
      }
    });
    
    return parsed.toString();
  } catch (error) {
    // Return generic URL if parsing fails
    return 'invalid_url';
  }
}

// Usage
span.setAttribute('http.url', sanitizeUrl(request.url));
```

### Path Parameterization

```go
func sanitizeHttpRoute(path string) string {
    // Replace numeric IDs with placeholders
    reID := regexp.MustCompile(`/\d+`)
    path = reID.ReplaceAllString(path, "/{id}")
    
    // Replace UUID patterns
    reUUID := regexp.MustCompile(`/[a-f0-9]{8}-[a-f0-9]{4}-[a-f0-9]{4}-[a-f0-9]{4}-[a-f0-9]{12}`)
    path = reUUID.ReplaceAllString(path, "/{uuid}")
    
    return path
}

// Usage
span.SetAttribute("http.route", sanitizeHttpRoute(request.URL.Path))
```

## Database Query Sanitization

### Parameter Replacement

```java
public String sanitizeSqlQuery(String query) {
    if (query == null) return "unknown";
    
    // Replace string literals
    query = query.replaceAll("'[^']*'", "'?'");
    
    // Replace numeric literals
    query = query.replaceAll("\\b\\d+\\b", "?");
    
    // Limit length
    if (query.length() > 1000) {
        query = query.substring(0, 1000) + "...";
    }
    
    return query;
}

// Usage
span.setAttribut("db.statement", sanitizeSqlQuery(sql));
```

### Query Templates

```python
def create_query_template(operation: str, table: str) -> str:
    """Create safe query template without literal values."""
    templates = {
        'SELECT': f'SELECT * FROM {table} WHERE ...',
        'INSERT': f'INSERT INTO {table} VALUES (...)',
        'UPDATE': f'UPDATE {table} SET ... WHERE ...',
        'DELETE': f'DELETE FROM {table} WHERE ...'
    }
    return templates.get(operation, f'{operation} {table}')

# Usage
span.set_attribute('db.statement', create_query_template(operation, table))
```

## HTTP Header Protection

### Authorization Header Redaction

```javascript
function sanitizeHeaders(headers) {
  const sensitiveHeaders = [
    'authorization', 'cookie', 'set-cookie', 'x-api-key',
    'x-auth-token', 'proxy-authorization'
  ];
  
  const safe = {};
  for (const [key, value] of Object.entries(headers)) {
    const lowerKey = key.toLowerCase();
    if (sensitiveHeaders.includes(lowerKey)) {
      safe[key] = 'REDACTED';
    } else {
      safe[key] = Array.isArray(value) ? value.join(', ') : value;
    }
  }
  return safe;
}

// Usage - don't include headers by default
// Only add specific safe headers when needed
span.setAttribute('http.request.header.content_type', req.get('content-type'));
```

## Request/Response Body Handling

### Never Log Full Bodies

```python
# ❌ NEVER do this
span.set_attribute('request.body', json.dumps(request.json))
span.set_attribute('response.body', response.text)

# ✅ Instead - log metadata only
span.set_attribute('request.content_length', len(request.data))
span.set_attribute('request.content_type', request.content_type)
span.set_attribute('response.status', response.status_code)
span.set_attribute('response.content_length', len(response.content))
```

### Safe Body Attributes

```go
// Extract safe metadata from request bodies
func extractSafeBodyAttributes(body []byte, contentType string) map[string]interface{} {
    attrs := make(map[string]interface{})
    
    attrs["request.body.size"] = len(body)
    attrs["request.body.content_type"] = contentType
    
    // Only for JSON - extract safe structural info
    if strings.Contains(contentType, "application/json") {
        var data map[string]interface{}
        if err := json.Unmarshal(body, &data); err == nil {
            attrs["request.body.field_count"] = len(data)
            // Could add safe field names (not values)
        }
    }
    
    return attrs
}
```

## Log Message Sanitization

### Pattern-Based Redaction

```javascript
function sanitizeLogMessage(message) {
  const patterns = [
    // Credit card numbers
    { pattern: /\b\d{4}[- ]?\d{4}[- ]?\d{4}[- ]?\d{4}\b/g, replacement: '****-****-****-****' },
    
    // Social Security Numbers
    { pattern: /\b\d{3}-\d{2}-\d{4}\b/g, replacement: '***-**-****' },
    
    // Email addresses
    { pattern: /\b[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Z|a-z]{2,}\b/g, replacement: '****@****.***' },
    
    // JWT tokens
    { pattern: /\beyJ[A-Za-z0-9_-]*\.[A-Za-z0-9_-]*\.[A-Za-z0-9_-]*/g, replacement: 'JWT_TOKEN_REDACTED' },
    
    // API keys (common patterns)
    { pattern: /\b(sk_|pk_|key_)[a-zA-Z0-9_-]{20,}/g, replacement: 'API_KEY_REDACTED' }
  ];
  
  let sanitized = message;
  patterns.forEach(({ pattern, replacement }) => {
    sanitized = sanitized.replace(pattern, replacement);
  });
  
  return sanitized;
}

// Usage in structured logging
logger.info(sanitizeLogMessage(message), {
  trace_id: span.spanContext().traceId,
  span_id: span.spanContext().spanId,
  user_id: hashUserId(userId)
});
```

## Framework-Specific Patterns

### Express.js Middleware

```javascript
function sensitiveDataMiddleware(req, res, next) {
  // Sanitize req.user for span context
  if (req.user) {
    res.locals.safeUser = {
      id: hashUserId(req.user.id),
      role: req.user.role,
      tier: req.user.tier
    };
  }
  
  // Sanitize URL
  res.locals.safeUrl = sanitizeUrl(req.originalUrl);
  
  next();
}

// Use in span creation
app.use(sensitiveDataMiddleware);
app.use((req, res, next) => {
  const span = trace.getActiveSpan();
  if (span && res.locals.safeUser) {
    span.setAttributes({
      'user.id': res.locals.safeUser.id,
      'user.role': res.locals.safeUser.role,
      'http.url': res.locals.safeUrl
    });
  }
  next();
});
```

### Django Integration

```python
class SensitiveDataMiddleware:
    def __init__(self, get_response):
        self.get_response = get_response
    
    def __call__(self, request):
        # Add safe user context
        if hasattr(request, 'user') and request.user.is_authenticated:
            request.safe_user = {
                'id': hash_user_id(str(request.user.id)),
                'role': getattr(request.user, 'role', 'user'),
                'is_staff': request.user.is_staff
            }
        
        response = self.get_response(request)
        return response

# Use in view instrumentation
def add_user_context_to_span(request, span):
    if hasattr(request, 'safe_user'):
        for key, value in request.safe_user.items():
            span.set_attribute(f'user.{key}', value)
```

## Validation and Testing

### Attribute Validation

```go
// Validate attributes before setting
func setAttributeSafely(span trace.Span, key string, value interface{}) {
    // Check for sensitive patterns in keys
    sensitiveKeyPatterns := []string{
        "password", "secret", "key", "token", "auth", "credential",
        "ssn", "credit", "card", "cvv", "pin",
    }
    
    keyLower := strings.ToLower(key)
    for _, pattern := range sensitiveKeyPatterns {
        if strings.Contains(keyLower, pattern) {
            // Log warning but don't set attribute
            log.Printf("Blocked sensitive attribute key: %s", key)
            return
        }
    }
    
    // Check value patterns
    if strValue, ok := value.(string); ok {
        if containsSensitiveData(strValue) {
            // Hash or redact the value
            value = "REDACTED"
        }
    }
    
    span.SetAttributes(attribute.Key(key).String(fmt.Sprint(value)))
}
```

### Testing Patterns

```javascript
// Unit tests for sensitive data protection
describe('Sensitive Data Protection', () => {
  it('should redact authorization headers', () => {
    const span = createTestSpan();
    const headers = { authorization: 'Bearer secret123' };
    
    addSafeHeaders(span, headers);
    
    expect(span.attributes['http.header.authorization']).toBe('REDACTED');
  });
  
  it('should hash user IDs', () => {
    const span = createTestSpan();
    const userId = 'user123@example.com';
    
    addUserContext(span, { id: userId });
    
    expect(span.attributes['user.id']).not.toBe(userId);
    expect(span.attributes['user.id']).toMatch(/^[a-f0-9]{16}$/);
  });
});
```
