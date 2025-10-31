# Dynamic Publish Topics

**Purpose**: Learn how to create dynamic publish topics using data from input topic names and payloads  
**Complexity**: ⭐⭐⭐ Advanced | **Focus**: Context-based routing and topic construction  
**Prerequisites**: [Basic Wildcards](../01_basic_wildcards/), [Array Mapping](../02_array/), and JSONata expressions

## What You'll Learn

By the end of this section, you'll master:
- ✅ **Wildcard-based routing** using topic segments for output construction  
- ✅ **Payload-based routing** using setContextVars for content-driven destinations
- ✅ **Multi-tenant architectures** with dynamic topic separation
- ✅ **UNS implementation** for enterprise namespace standardization

## The Challenge: Smart Message Routing

Static publish topics work for simple cases:
```yaml
# ❌ Static routing - not flexible
publish:
  topic: alerts/temperature/high    # Always goes to the same place
```

But what about dynamic scenarios where routing depends on:
- **Topic content**: Route `sensors/kitchen/temp` → `alerts/kitchen/temperature`  
- **Payload data**: Route based on `{"tenant": "acme", "priority": "high"}`
- **Business logic**: Different destinations based on data values or topic structure

**Solution**: Dynamic publish topics that construct destinations from context and data.

## When to Use Dynamic Publish Topics

**Perfect Use Cases:**
- **Multi-tenant systems**: Route messages to tenant-specific topics
- **Device-specific routing**: Send data to device-specific endpoints
- **Hierarchical data organization**: Build topic trees based on data content
- **Dynamic system integration**: Route to different systems based on payload content
- **UNS (Unified Namespace)**: Transform legacy systems into standardized enterprise hierarchies

## Folder Structure

### [wildcards/](wildcards/)
Learn how to use wildcard segments from input topics to construct dynamic publish topics:
- Extract location, device, or system identifiers from topic paths
- Build hierarchical output topics using wildcard values
- Route messages based on topic structure

### [set_context_vars/](set_context_vars/)
Learn how to use payload data to create dynamic publish topics:
- Extract routing information from message payloads
- Use setContextVars rule to prepare dynamic values
- Build complex topic paths from payload content

## Comparison: Wildcards vs SetContextVars

| Approach | Data Source | Use Case | Example |
|----------|-------------|----------|---------|
| **Wildcards** | Topic segments | Route by topic structure | `input: sensors/{room}/temp` → `output: alerts/{room}/high` |
| **SetContextVars** | Payload content | Route by data content | `payload: {"priority": "high"}` → `output: alerts/high/notification` |
| **Combined** | Both sources | Complex routing logic | Topic + payload → `output: {tenant}/{priority}/{device}` |
| **UNS Implementation** | Topic structure | Legacy to standardized hierarchy | `legacy/{site}/{line}/data` → `UNS/Enterprise/{site}/Line/{line}` |

## Key Benefits

✅ **Flexible Routing**: Send messages to different destinations based on content
✅ **System Integration**: Route to different systems or services dynamically  
✅ **Multi-tenancy**: Separate data streams by tenant, user, or organization
✅ **Conditional Logic**: Apply business rules to determine routing
✅ **Scalability**: Handle dynamic systems without hardcoded topic mappings
✅ **UNS Compliance**: Transform legacy topic structures into ISA-95 compliant hierarchies

## Getting Started

1. **Start with [wildcards/](wildcards/)** if you need to route based on topic structure
2. **Try [set_context_vars/](set_context_vars/)** if you need to route based on payload content
3. **Combine both** for advanced routing scenarios

Each folder contains working examples with detailed explanations and real-world use cases.

## 💡 UNS (Unified Namespace) Implementation Hint

**UNS transforms disparate enterprise systems into a single, standardized topic hierarchy.** Dynamic publish topics are **essential** for UNS because:

### Why UNS Needs Dynamic Publishing
- **Legacy Integration**: Transform `legacy/plant-01/line-a/data` → `UNS/Enterprise/plant-01/Area/production/Line/line-a`
- **ISA-95 Compliance**: Automatically structure topics according to enterprise hierarchy standards
- **Semantic Consistency**: Extract business meaning from legacy topics and map to standardized paths

### UNS Pattern Examples
```yaml
# Legacy System → UNS Transformation
Input:  legacy/+site/+area/+line/+asset/data
Output: UNS/Enterprise/{site}/Area/{area}/Line/{line}/Asset/{asset}

# Multi-Protocol UNS
Input:  protocols/+protocol/+device/+datapoint
Output: UNS/Systems/{protocol}/Devices/{device}/DataPoints/{datapoint}
```

**👉 See [wildcards/wildcard-routing.scf.yaml](wildcards/wildcard-routing.scf.yaml) for complete UNS implementation example with ISA-95 compliance.**