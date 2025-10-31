# COV Rule: Filter on Value Changes

## What This Does
**COV (Change-of-Value)** only passes messages when specific fields actually change. Reduces data traffic by 60-80% while preserving important changes.

**Quick Example:**
```yaml
# Only pass temperature changes ≥ 0.5°C
- cov:
    key: temperature
    deadband: 0.5          # ±0.5 threshold
    deadbandMode: absolute # Fixed threshold
```

## Quick Setup
1. **Deploy the service**: `docker exec -it connectware-container cybus-ctl commissioning-file apply service.scf.yaml`
2. **Send test data**: Publish temperature readings to input topics
3. **Check filtering**: Subscribe to output topics to see only significant changes

## Key Examples

### **1. State Changes** (`machine/status` → `machine/status-changes`)
```yaml
# Input: {"state": "running", "speed": 1800} then {"state": "running", "speed": 1820}
- cov:
    key: state    # Only passes when state changes
# Output: Only {"state": "running", "speed": 1800} (second message filtered - same state)
```

### **2. Temperature Filtering** (`sensors/temperature/raw` → `sensors/temperature/filtered`)
```yaml
# Input: 22.0°C → 22.1°C → 22.3°C → 22.6°C
- cov:
    key: temperature
    deadband: 0.5          # ±0.5°C threshold
    deadbandMode: absolute # Fixed threshold
# Output: 22.0°C (first) → 22.6°C (≥0.5°C change)
```

### **3. Pressure Monitoring** (`equipment/press/pressure` → `equipment/press/pressure/filtered`)
```yaml
# Input: 100 bar → 102 bar → 106 bar → 108 bar
- cov:
    key: pressure_bar
    deadband: 5            # 5% change required
    deadbandMode: relative # Percentage-based
# Output: 100 bar (first) → 106 bar (6% change) → filters 108 bar (1.9% change)
```

### **4. Multiple Field Monitoring** (`equipment/motor/all-sensors` → `equipment/motor/significant-changes`)
```yaml
# Monitor ANY of these fields changing
- cov:
    key: mode             # String - exact match
- cov:
    key: temperature      # Numeric - ±2°C threshold
    deadband: 2.0
    deadbandMode: absolute
- cov:
    key: alarm_active     # Boolean - exact match
```

### **5. Quality Control** (`production/quality/measurements` → `production/quality/alerts`)
```yaml
# COV + Transform combination
- cov:
    key: diameter_mm      # ±0.01mm precision
    deadband: 0.01
    deadbandMode: absolute
- transform:
    expression: |
      {
        "part_id": $.part_id,
        "measurement": $.diameter_mm,
        "status": ($.diameter_mm >= 25.00 and $.diameter_mm <= 25.10) ? "PASS" : "FAIL"
      }
```

## Parameter Reference

| Parameter | Required | Purpose | Example |
|-----------|----------|---------|---------|
| **key** | ✅ | Field to monitor | `temperature`, `state`, `pressure_bar` |
| **deadband** | ⬜ | Change threshold | `0.5`, `5` (omit for exact matching) |
| **deadbandMode** | ⬜ | Threshold type | `absolute` (fixed) or `relative` (%) |

**When to omit deadband:** Strings, booleans, objects need exact matching

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