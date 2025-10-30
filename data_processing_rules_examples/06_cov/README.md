# COV Rule: Progressive Examples from Basic to Advanced

The COV (Change-of-Value) rule filters messages based on changes in specific data values. This guide builds from the simplest usage to advanced industrial scenarios, matching the progressive examples in `service.scf.yaml`.

## Core Concept: Change Detection

COV rules monitor specific JSON fields and only pass messages when those fields change:
- **Without deadband**: Exact value matching (strings, booleans, objects)
- **With deadband**: Threshold-based filtering for numeric values (absolute or relative)

## Progressive Learning Path

## Level 1: Basic COV - Simplest Usage

**Goal**: Monitor exact field changes without any thresholds

```yaml
rules:
- cov:
    key: state    # Only required parameter
```

**Use Cases**: 
- Machine state changes (running → stopped → error)
- Boolean alarm states (false → true)  
- Configuration updates

**Key Point**: No deadband parameters needed - monitors exact value changes

## Level 2: COV with Absolute Deadband  

**Goal**: Add numeric threshold filtering to reduce noise

```yaml
rules:
- cov:
    key: temperature
    deadband: 0.5          # ±0.5 degree threshold
    deadbandMode: absolute # Fixed threshold
```

**Use Cases**:
- Temperature sensor noise filtering
- Pressure readings with measurement fluctuations
- Any numeric value with consistent scale

**Key Point**: Fixed threshold value - same sensitivity across all value ranges

## Level 3: COV with Relative Deadband

**Goal**: Percentage-based filtering for wide-range values

```yaml
rules:
- cov:
    key: pressure_bar
    deadband: 5            # 5% change required  
    deadbandMode: relative # Percentage-based
```

**Use Cases**:
- Hydraulic pressure (100 bar vs 1000 bar systems)
- Motor speeds (100 RPM vs 3000 RPM)
- Power consumption monitoring

**Key Point**: Threshold scales with value magnitude - 5% of 100 = 5, 5% of 1000 = 50

## Level 4: Multiple COV Rules

**Goal**: Monitor different fields with different strategies (OR logic)

```yaml
rules:
- cov:
    key: mode             # String - exact match
- cov:
    key: temperature      # Numeric - with threshold  
    deadband: 2.0
    deadbandMode: absolute
- cov:
    key: alarm_active     # Boolean - exact match
```

**Use Cases**:
- Equipment monitoring (state + health parameters)
- Multi-sensor data filtering
- Mixed data type scenarios

**Key Point**: Message passes if ANY monitored field meets its criteria

## Level 5: COV + Transform Combination

**Goal**: Add data processing to filtered results

```yaml
rules:
- cov:
    key: diameter_mm
    deadband: 0.01
    deadbandMode: absolute
- transform:
    expression: |
      {
        "measurement": $.diameter_mm,
        "status": ($.diameter_mm >= 25.00 and $.diameter_mm <= 25.10) ? "PASS" : "FAIL"
      }
```

**Use Cases**:
- Quality control with pass/fail analysis
- Alarm generation with processed data
- Data enrichment after filtering

**Key Point**: Rules chain together - filter first, then transform

## Level 6: Endpoint Usage (Production Pattern)

**Goal**: Apply COV at data source for maximum efficiency

```yaml
# Most common production usage
endpoints:
  temperatureSensor:
    type: Cybus::Endpoint
    properties:
      protocol: Opcua
      rules:
      - cov:
          key: value
          deadband: 0.5
          deadbandMode: absolute
```

**Benefits**:
- **75% traffic reduction** typical
- **Early filtering** at source
- **Network efficiency** 
- **Edge computing** advantages

## Level 7: Complex Object Monitoring

**Goal**: Monitor entire objects or configurations for structural changes

```yaml
rules:
- cov:
    key: settings  # Monitor entire object
    # No deadband - detects any structural change
```

**Use Cases**:
- Configuration change detection
- Complex nested object monitoring
- System settings updates

**Key Point**: Object comparison detects any structural differences

## When to Use Each Level

### **Level 1 (Basic)**: Start Here
- ✅ **Machine state monitoring**
- ✅ **Boolean alarm detection** 
- ✅ **Simple change detection**
- ❌ Noisy numeric sensor data

### **Level 2 (Absolute Deadband)**: Add Noise Filtering
- ✅ **Temperature sensors** with consistent ranges
- ✅ **Pressure measurements** with known scale
- ✅ **Position/distance** measurements
- ❌ Wide-range values (1-1000+ scale differences)

### **Level 3 (Relative Deadband)**: Handle Wide Ranges  
- ✅ **Hydraulic pressure** (100-1000+ bar)
- ✅ **Motor speeds** (100-3000+ RPM)
- ✅ **Power consumption** (watts to kilowatts)
- ❌ Values near zero (division issues)

### **Level 4 (Multiple Rules)**: Complex Monitoring
- ✅ **Equipment health** (state + temperature + alarms)
- ✅ **Multi-sensor** filtering
- ✅ **Mixed data types** 
- ❌ Simple single-field scenarios

### **Level 5 (COV + Transform)**: Add Intelligence
- ✅ **Quality control** with pass/fail
- ✅ **Alarm generation** with context
- ✅ **Data enrichment** after filtering
- ❌ Simple passthrough filtering

### **Level 6 (Endpoints)**: Production Deployment
- ✅ **OPC-UA servers** with high-frequency data
- ✅ **Modbus devices** with polling noise
- ✅ **MQTT gateways** with multiple sensors
- ✅ **Network optimization** critical

### **Level 7 (Objects)**: Advanced Scenarios
- ✅ **Configuration management**
- ✅ **Complex nested data**
- ✅ **Structural change detection**
- ❌ Simple value monitoring

## Parameter Reference

### **Required:**
- `key` - JSON field name to monitor

### **Optional:**  
- `deadband` - Change threshold (omit for exact matching)
- `deadbandMode` - "absolute" or "relative" (omit for non-numeric)

### **When to Omit Deadband:**
- **Strings** (exact value changes)
- **Booleans** (true/false transitions)  
- **Objects** (structural changes)
- **Arrays** (content changes)

## Quick Reference: Deadband Modes

| Mode | Level | Best For | Example | When to Use |
|------|-------|----------|---------|-------------|
| **None** | 1, 4, 7 | Exact matching | Strings, booleans, objects | State changes, alarms |
| **Absolute** | 2, 4, 5 | Consistent scale values | ±0.5°C temperature | Known measurement ranges |
| **Relative** | 3, 4 | Wide-range values | ±5% pressure | Values spanning orders of magnitude |

## Example Progression

Following the `service.scf.yaml` examples:

### Level 1: Basic String Monitoring
```yaml
# machine/status → machine/status-changes
rules:
- cov:
    key: state
```

### Level 2: Temperature with Absolute Deadband  
```yaml
# sensors/temperature/raw → sensors/temperature/filtered
rules:
- cov:
    key: temperature
    deadband: 0.5
    deadbandMode: absolute
```

### Level 3: Pressure with Relative Deadband
```yaml
# equipment/press/pressure → equipment/press/pressure/filtered  
rules:
- cov:
    key: pressure_bar
    deadband: 5
    deadbandMode: relative
```

### Pressure Monitoring with Percentage
```yaml
rules:
- cov:
    key: pressure_bar
    deadband: 5            # ±5% change required
    deadbandMode: relative # Percentage-based
```

### Multiple Field Monitoring
```yaml
rules:
- cov:
    key: temperature_c     # Monitor temperature
    deadband: 2.0
    deadbandMode: absolute
- cov:
    key: vibration_mm_s    # Also monitor vibration
    deadband: 0.5
    deadbandMode: absolute
```

## Real-World Application Examples

Matching the scenarios in `service.scf.yaml`:

### **Machine State Monitoring** (Level 1)
- **Input**: `{"state": "running", "speed": 1800}`
- **COV**: Monitor `state` field exactly
- **Output**: Only when state changes (running → stopped → error)
- **Benefit**: Focus on operational state changes, ignore speed variations

### **Temperature Sensor Filtering** (Level 2)  
- **Input**: `{"temperature": 22.1, "humidity": 60}` 
- **COV**: ±0.5°C absolute deadband on `temperature`
- **Output**: Only when temperature changes by >0.5°C
- **Benefit**: Filter sensor noise, preserve meaningful temperature trends

### **Hydraulic Pressure Monitoring** (Level 3)
- **Input**: `{"pressure_bar": 102.0, "cycle_count": 1451}`
- **COV**: 5% relative deadband on `pressure_bar`
- **Output**: Only when pressure changes by >5% 
- **Benefit**: Adapt to different pressure ranges (100 bar vs 1000 bar systems)

### **Equipment Health Dashboard** (Level 4)
- **Input**: `{"mode": "auto", "temperature": 65.5, "alarm_active": false}`
- **COV**: Monitor mode (exact), temperature (±2°C), alarm (exact)
- **Output**: When ANY monitored field meets criteria
- **Benefit**: Comprehensive equipment monitoring with appropriate sensitivity

### **Quality Control** (Level 5)
- **Input**: `{"part_id": "P003", "diameter_mm": 25.062}`
- **COV + Transform**: ±0.01mm deadband + pass/fail analysis
- **Output**: Quality events with processing status
- **Benefit**: Precise measurement monitoring with automated quality assessment

## Best Practices by Level

### **Start Simple (Level 1)**
- Begin with basic field monitoring for state changes
- No deadband needed for strings, booleans, objects
- Perfect for understanding COV behavior

### **Add Filtering (Level 2-3)**  
- Use absolute deadband for consistent-scale values
- Use relative deadband for wide-range values
- Balance sensitivity vs. noise reduction

### **Scale Up (Level 4)**
- Combine multiple monitoring strategies
- Remember OR logic - ANY field change triggers output
- Group related monitoring fields

### **Add Intelligence (Level 5)**
- Chain COV with transform for data enrichment
- Generate alerts, quality status, processed data
- Maintain filtering efficiency while adding value

### **Deploy to Production (Level 6)**
- Move COV to endpoints for maximum efficiency
- 60-80% typical traffic reduction
- Essential for high-frequency data sources

### **Handle Complexity (Level 7)**
- Monitor configuration and complex objects
- Detect structural changes automatically
- Essential for configuration management

## Performance Impact

Following the progressive examples shows typical improvements:

| Level | Scenario | Traffic Reduction | Use Case |
|-------|----------|------------------|----------|
| 1 | State monitoring | 60-80% | Machine state changes |
| 2 | Temperature filtering | 70-85% | Environmental sensors |
| 3 | Pressure monitoring | 60-75% | Hydraulic systems |
| 4 | Multi-field monitoring | 50-70% | Equipment health |
| 5 | Quality control | 40-60% | Manufacturing precision |
| 6 | Endpoint filtering | 75%+ | Production deployment |
| 7 | Object monitoring | Variable | Configuration management |

## Troubleshooting by Level

### **Level 1 Issues**
- **No output**: Check field name spelling
- **Too much output**: Verify you want exact matching

### **Level 2-3 Issues**  
- **Too many messages**: Increase deadband size
- **Missing changes**: Decrease deadband size
- **Wrong mode**: Switch absolute ↔ relative

### **Level 4 Issues**
- **Unexpected triggers**: Remember OR logic between rules
- **Performance**: Consider if all fields need monitoring

### **Level 5 Issues**
- **Transform errors**: Ensure COV passes before transform
- **Complex expressions**: Test JSONata expressions separately

## Complete Examples

See `service.scf.yaml` for the full progression:
- `basicStringMonitoring` (Level 1)
- `basicNumericFiltering` (Level 2)  
- `relativeDeadbandFiltering` (Level 3)
- `multipleFieldMonitoring` (Level 4)
- `covWithTransform` (Level 5)
- `opcuaEndpointExample` (Level 6)
- `configurationMonitoring` (Level 7)
