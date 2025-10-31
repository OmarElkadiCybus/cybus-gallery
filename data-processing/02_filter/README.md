# Filter Rules: Conditional Message Processing

Filter rules act as **gatekeepers** - they conditionally pass messages based on JSONata expressions that evaluate to true/false.

- **Expression returns `true`**: Message passes through unchanged
- **Expression returns `false`**: Message is discarded
- **No data modification**: Unlike transform rules, filters don't change message content

## Three Essential Patterns

### 1. Basic Filtering (Start Here)

Filter by simple field values - the most common pattern:

```yaml
rules:
- filter:
    expression: level = "ERROR"    # Only pass ERROR level messages
```

**Common Examples**:
```yaml
temperature > 85                  # Temperature alarms
status = "active"                # Active devices only  
priority in ["high", "critical"] # Important messages
```

**Use Cases**: Log filtering, status routing, simple categorization

### 2. Multi-Condition Logic

Combine multiple conditions for complex business rules:

```yaml
rules:
- filter:
    expression: |
      (temperature > 85 or pressure < 30) and 
      status = "operational"
```

**Logic Operators**:
- `and` - Both conditions must be true
- `or` - Either condition can be true
- `not` - Inverts the condition
- Parentheses for grouping: `(A or B) and C`

**Use Cases**: Quality control, equipment monitoring, safety checks

### 3. Advanced Scenarios

Handle complex data structures and real-world requirements:

```yaml
rules:
- filter:
    expression: |
      $count(alarms[severity = "critical"]) > 0 and
      equipment.maintenance_due = false
```

**Advanced Capabilities**:
- **Array processing**: `$count()`, `$exists()`, `$filter()`
- **Object navigation**: `equipment.status`, `config.settings.enabled`
- **Context variables**: `$context.topic` for wildcard subscriptions
- **Mathematical functions**: `$average()`, `$sum()`, `$abs()`

**Use Cases**: Maintenance alerts, statistical analysis, multi-sensor correlation

## Filter + Transform Pattern

Combine filtering with data enrichment for intelligent processing:

```yaml
rules:
- filter:
    expression: temperature > 95 or oil_pressure < 30
- transform:
    expression: |
      {
        "alert_type": "maintenance_required",
        "severity": temperature > 100 ? "critical" : "warning",
        "original_data": $
      }
```

**Benefits**: Filter first for efficiency, then add intelligence and context

## Performance Tips

- **Start simple**: Use basic patterns for high-frequency data
- **Filter early**: Place filters before transforms in rule chains
- **Test expressions**: Verify logic with sample data before deployment
- **Optimize order**: Put fastest filters first in complex chains

## Examples in This Folder

See `service.scf.yaml` for working implementations:
- Basic field filtering
- Numeric range checks  
- Complex multi-condition logic
- Array and object processing
- Filter + transform combinations
