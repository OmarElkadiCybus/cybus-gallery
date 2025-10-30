# Filter Rule: Progressive Examples from Basic to Advanced

The filter rule conditionally passes messages based on JSONata expressions that evaluate to true/false. This guide builds from simple field comparisons to complex data analysis, matching the progressive examples in `service.scf.yaml`.

## Core Concept: Conditional Message Passing

Filter rules act as **gatekeepers** - they evaluate each incoming message against a JSONata expression:
- **Expression returns `true`**: Message passes through to the output topic
- **Expression returns `false`**: Message is discarded (filtered out)
- **No modification**: Messages pass through unchanged (unlike transform rules)

## Progressive Learning Path

### Level 1: Basic Field Filtering

**Goal**: Filter messages based on simple field value comparisons

```yaml
rules:
- filter:
    expression: level = "ERROR"    # Simple equality check
```

**Use Cases**:
- Log level filtering (ERROR, WARN, INFO)
- Status filtering (active, inactive, error)
- Type-based routing (alarm, event, data)

**Key Point**: Single field comparison - most common filtering pattern

### Level 2: Numeric Range Filtering

**Goal**: Filter based on numeric thresholds and ranges

```yaml
rules:
- filter:
    expression: temperature > 85    # Numeric comparison
```

**Common Patterns**:
```yaml
# Single threshold
temperature > 85
pressure < 30
speed >= 100

# Range filtering  
temperature > 20 and temperature < 80
diameter >= 24.95 and diameter <= 25.05

# Multiple thresholds
temperature > 85 or pressure < 30 or voltage < 11.5
```

**Use Cases**:
- Temperature alarm thresholds
- Quality control tolerances  
- Performance monitoring limits
- Safety parameter checks

### Level 3: Complex Multi-Field Logic

**Goal**: Combine multiple conditions with AND/OR logic

```yaml
rules:
- filter:
    expression: |
      (diameter < 24.95 or diameter > 25.05) and 
      status = "completed"
```

**Logic Operators**:
- `and` - Both conditions must be true
- `or` - Either condition can be true  
- `not` - Inverts the condition
- Parentheses for grouping: `(A or B) and C`

**Use Cases**:
- Quality control with status checks
- Multi-parameter equipment monitoring
- Business rule enforcement
- Complex alarm conditions

### Level 4: Array and Object Filtering

**Goal**: Filter based on nested data structures

```yaml
rules:
- filter:
    expression: |
      $count(issues[severity = "critical"]) > 0 and
      equipment.operational = false
```

**Common Patterns**:
```yaml
# Array filtering
$count(alarms[priority = "high"]) > 0
$exists(sensors[value > 100])
"error" in status_list

# Object property access
equipment.status = "maintenance"
config.settings.enabled = true
metadata.tags.priority = "urgent"

# Nested combinations
$count(readings[value > threshold]) >= 3
```

**Use Cases**:
- Maintenance report filtering
- Multi-sensor alarm conditions
- Configuration-based routing
- Complex data validation

### Level 5: Wildcard Context Filtering

**Goal**: Use wildcard subscriptions with context variables

```yaml
rules:
- filter:
    expression: |
      alarm_type = "production_stop" and
      $contains($context.topic, "line-a")
```

**Context Variables**:
- `$context.topic` - Full topic path
- `$context.label` - Dynamic label from wildcards
- Topic path analysis with `$contains()`, `$split()`, etc.

**Use Cases**:
- Location-specific filtering
- Department-based routing
- Hierarchical data organization
- Multi-tenant filtering

### Level 6: Filter + Transform Combinations

**Goal**: Filter messages then add intelligence

```yaml
rules:
- filter:
    expression: |
      engine_temp > 95 or oil_pressure < 30
- transform:
    expression: |
      {
        "alert_type": "maintenance_required",
        "severity": engine_temp > 100 ? "critical" : "warning"
      }
```

**Benefits**:
- **Efficiency**: Filter first to reduce transform workload
- **Intelligence**: Add context and analysis to filtered results
- **Enrichment**: Combine original data with computed fields

**Use Cases**:
- Maintenance alert generation
- Quality control with analysis
- Performance monitoring with recommendations
- Alarm enrichment with context

### Level 7: Advanced JSONata Functions

**Goal**: Complex data analysis within filter expressions

```yaml
rules:
- filter:
    expression: |
      (
        $avg := $average(readings.value);
        $std := $sqrt($sum($map(readings.value, function($v) { 
          ($v - $avg) * ($v - $avg) 
        })) / $count(readings.value));
        $count($filter(readings, function($r) { 
          $abs($r.value - $avg) > (2 * $std) 
        })) > 0
      )
```

**Advanced Functions**:
- Statistical: `$average()`, `$sum()`, `$min()`, `$max()`
- Array processing: `$map()`, `$filter()`, `$reduce()`
- Mathematical: `$sqrt()`, `$abs()`, `$round()`
- String processing: `$contains()`, `$split()`, `$join()`

**Use Cases**:
- Statistical anomaly detection
- Complex business rule evaluation
- Data quality validation
- Predictive condition monitoring

## When to Use Each Level

### **Level 1 (Basic)**: Start Here
- ✅ **Log filtering** by severity
- ✅ **Status-based routing** 
- ✅ **Simple categorization**
- ❌ Numeric threshold monitoring

### **Level 2 (Numeric)**: Add Thresholds
- ✅ **Temperature monitoring**
- ✅ **Pressure/flow limits**
- ✅ **Performance thresholds**
- ❌ Complex multi-parameter conditions

### **Level 3 (Complex Logic)**: Multiple Conditions
- ✅ **Quality control** with multiple parameters
- ✅ **Equipment monitoring** with status checks
- ✅ **Business rule** enforcement
- ❌ Simple single-field filtering

### **Level 4 (Arrays/Objects)**: Structured Data
- ✅ **Maintenance reports** with issue arrays
- ✅ **Multi-sensor** alarm conditions
- ✅ **Configuration-driven** filtering
- ❌ Flat data structures

### **Level 5 (Wildcard Context)**: Scale with Wildcards
- ✅ **Multi-location** monitoring
- ✅ **Hierarchical** data organization
- ✅ **Department/line-specific** filtering
- ❌ Fixed, known topic structures

### **Level 6 (Filter + Transform)**: Add Intelligence
- ✅ **Alert generation** with analysis
- ✅ **Data enrichment** after filtering
- ✅ **Maintenance recommendations**
- ❌ Simple passthrough filtering

### **Level 7 (Advanced JSONata)**: Complex Analysis
- ✅ **Statistical analysis**
- ✅ **Anomaly detection**
- ✅ **Complex business rules**
- ❌ Simple conditional filtering

## Best Practices by Level

### **Performance Optimization**
- **Level 1-2**: Fastest execution, use for high-frequency data
- **Level 3-4**: Moderate complexity, good for most use cases
- **Level 5-6**: Higher complexity, optimize expressions
- **Level 7**: Most complex, use sparingly for critical analysis

### **Expression Design**
- **Start simple**: Begin with basic comparisons
- **Add complexity gradually**: Build up from working expressions
- **Test thoroughly**: Verify filter logic with sample data
- **Document complex expressions**: Add comments for maintenance

### **Rule Chaining**
- **Filter first**: Always filter before transform for efficiency
- **Logical grouping**: Group related filters together
- **Performance order**: Put fastest filters first

## Configuration Reference

See `service.scf.yaml` for complete implementation examples:
- `basicFieldFilter` (Level 1)
- `numericRangeFilter` (Level 2)
- `complexConditionFilter` (Level 3)
- `arrayObjectFilter` (Level 4)
- `wildcardContextFilter` (Level 5)
- `filterTransformCombination` (Level 6)
- `advancedJsonataFilter` (Level 7)
