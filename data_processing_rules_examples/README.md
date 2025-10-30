# Data Processing Rules: Complete Learning Guide

Data processing rules are the **heart of Cybus Connectware** - they transform, filter, and process messages as they flow through your system. This guide provides a complete learning path from basic concepts to advanced rule combinations.

## What Are Data Processing Rules?

**Simple Concept**: Rules are like **assembly line workers** - each one does a specific job on your message before passing it to the next worker.

```yaml
# Message flows through rules (Rule Chain) in order:
rules:
- transform: {...}    # Worker 1: Reshape the data
- filter: {...}       # Worker 2: Decide if it should continue
- stash: {...}        # Worker 3: Remember this message
```

## Where Can Rules Be Used?

Data processing rules can be used in **two main places** in Cybus Connectware:

### **1. Mappings** (Most Common)
Rules in mappings process messages flowing between endpoints and topics:
```yaml
mapping:
  type: Cybus::Mapping
  properties:
    mappings:
    - subscribe: {endpoint: !ref sensorEndpoint}
      publish: {topic: processed/data}
      rules:
      - transform: {...}
      - filter: {...}
```

### **2. Endpoints** (Direct Processing)
Rules can also be applied directly in endpoints to process messages at the edge:
```yaml
sensorEndpoint:
  type: Cybus::Endpoint
  properties:
    protocol: Mqtt
    connection: !ref connection
    subscribe: {topic: sensors/raw}
    rules:
    - transform: {...}
    - filter: {...}
```

**Key Difference**: 
- **Mapping rules**: Process specific message flows between sources and destinations
- **Endpoint rules**: Process messages directly at the endpoint level (edge processing)

## How Rules Work as a Chain

### **The Processing Pipeline**

```
┌─────────────┐    ┌─────────┐    ┌─────────┐    ┌─────────┐    ┌─────────────┐
│   Trigger   │───▶│ Rule 1  │───▶│ Rule 2  │───▶│ Rule 3  │───▶│   Publish   │
│  (Endpoint  │    │Transform│    │ Filter  │    │  Stash  │    │   Topic     │
│  or Topic)  │    │         │    │         │    │         │    │             │
└─────────────┘    └─────────┘    └─────────┘    └─────────┘    └─────────────┘
```

**Flow Explanation:**
1. **Trigger**: Message arrives from endpoint or topic
2. **Rule Chain**: Each rule processes the message in order
3. **Pass Through**: Message moves to next rule (unless filtered out)
4. **Publish**: Final message goes to output topic

### **Rule Chain Behavior**

- ✅ **Sequential Processing**: Rules execute in the order you write them
- ✅ **Pass Through**: Each rule receives the output from the previous rule
- ✅ **Short Circuit**: If a filter rule blocks a message, chain stops
- ✅ **Transform Changes**: Transform rules change the message structure
- ✅ **Memory Rules**: Some rules (stash, collect) remember data between messages

## What is JSONata?

**JSONata** is the expression language used in data processing rules. Think of it as **JavaScript for JSON data**.

### **Basic JSONata Concepts**

```javascript
// Input message: {"temperature": 25, "sensor": {"id": "temp-01", "location": "warehouse"}}

$.temperature           // Gets: 25
$.sensor.id            // Gets: "temp-01"  
$.sensor.location      // Gets: "warehouse"
$                      // Gets: whole entire message
$now()                 // Gets: current timestamp
$.temperature > 20     // Gets: true (boolean)
$.temperature * 1.8 + 32  // Gets: 77 (calculation)
```

### **JSONata in Different Rules**

```yaml
# TRANSFORM: Create new JSON structure
- transform:
    expression: |
      {
        "device_name": $.sensor.id,           # Extract nested field
        "temp_celsius": $.temperature,        # Keep original
        "temp_fahrenheit": $.temperature * 1.8 + 32,  # Calculate
        "is_hot": $.temperature > 30          # Boolean logic
      }

# FILTER: Boolean expression (true/false)
- filter:
    expression: $.temperature > 20 and $.sensor.location = "warehouse"

## Simple Rule Chain Example

### **Basic Temperature Alert System**

This shows how messages flow through a simple 3-rule chain.

**Service File**: `complete_rule_chain_example.scf.yaml`

```yaml
resources:
  mapping:
    type: Cybus::Mapping
    properties:
      mappings:
      - subscribe: {endpoint: !ref sensorEndpoint}
        publish: {topic: alerts/temp}
        rules:
        - transform: {...}  # Clean data
        - filter: {...}     # Only high temps  
        - transform: {...}  # Format alert
```

## Message Flow from Trigger to Rules

### **Step-by-Step Process**

1. **Message Arrives** 📨
   ```json
   {"temp": 25, "sensor": "room-01", "battery": 85}
   ```

2. **Rule 1: Transform** 🔄
   ```yaml
   - transform:
       expression: |
         {
           "temperature": $.temp,
           "device": $.sensor,
           "timestamp": $now()
         }
   ```
   **Result**: `{"temperature": 25, "device": "room-01", "timestamp": "2024-10-29T10:30:00Z"}`

3. **Rule 2: Filter** 🚪
   ```yaml
   - filter:
       expression: $.temperature > 20
   ```
   **Result**: Message passes through (25 > 20 is true)

4. **Rule 3: Stash** 💾
   ```yaml
   - stash:
       label: tempHistory
   ```
   **Result**: Message stored for next time, passes through unchanged

5. **Published** 📡
   Final message goes to output topic


## Rule Folders (Ordered for Learning)

📁 **Follow these folders in numerical order:**

1. **`01_transform/`** - Learn data reshaping and JSONata basics
2. **`02_filter/`** - Learn conditional message processing  
3. **`03_parse/`** - Learn data format conversion
4. **`04_stash/`** - Learn stateful processing and memory
5. **`05_setContextVars/`** - Learn variable extraction and reuse
6. **`06_cov/`** - Learn change detection and deadband filtering
7. **`07_collect/`** - Learn data aggregation from multiple sources
8. **`08_burst/`** - Learn batch processing and timing controls

## Common Patterns

### **Data Cleaning Pipeline**
```yaml
rules:
- parse: {format: json}           # Convert string to JSON
- filter: {expression: $.valid}   # Only valid data
- transform: {expression: ...}    # Clean and restructure
```

### **Trend Analysis**
```yaml
rules:
- transform: {expression: ...}    # Process current data
- stash: {label: history}         # Store for comparison
- transform: {expression: ...}    # Add trend analysis
```

### **Multi-Source Aggregation**
```yaml
rules:
- collect: {label: sensors}       # Combine multiple sources
- transform: {expression: ...}    # Process combined data
```

## Key Concepts to Remember

- ✅ **Order Matters**: Rules execute sequentially
- ✅ **Message Changes**: Each rule can modify the message
- ✅ **JSONata Everywhere**: All expressions use JSONata syntax
- ✅ **State Management**: Some rules remember data between messages
- ✅ **Testing**: Always test rules with sample data first
- ✅ **Documentation**: Each folder has complete examples with input/output

Start with **transform** to learn the basics, then progress through the other rules as your understanding grows!