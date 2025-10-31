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

## Tutorial Structure

Learn dynamic publishing through two progressive approaches:

### 📁 [using_wildcards/](using_wildcards/) - Topic-Based Routing (Start Here)
**Complexity**: ⭐⭐ Intermediate | **Focus**: Using wildcard values for output topics

**What You'll Learn:**
- Extract segments from input topics: `sensors/{room}/temp` → `alerts/{room}/high`
- Build hierarchical output topics using `$context.vars.{name}` 
- Route messages based on topic structure patterns
- Implement UNS (Unified Namespace) transformations

**Perfect For:**
- **Building automation**: Route by room, floor, or zone from topic structure
- **Device management**: Route by location and device ID extracted from topics  
- **Legacy system modernization**: Transform to standardized enterprise hierarchies
- **Simple dynamic routing**: When routing logic is embedded in topic names

---

### 📁 [using_set_context_vars/](using_set_context_vars/) - Content-Based Routing (Advanced)
**Complexity**: ⭐⭐⭐ Advanced | **Focus**: Using payload data for routing decisions

**What You'll Learn:**
- Extract routing info from message content: `{"tenant": "acme", "priority": "high"}`
- Use setContextVars rule to prepare dynamic routing variables
- Apply business logic and conditional routing based on data values
- Build multi-tenant and enterprise-grade routing systems

**Perfect For:**
- **Multi-tenant SaaS**: Route by organization, service, or user from payload
- **Alert systems**: Route by severity, destination, or escalation rules
- **Business process routing**: Apply complex conditional logic
- **Content-driven systems**: When routing depends on message data, not topic structure

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

## Learning Path

### 🎯 **Recommended Sequence**

1. **Start with [Topic-Based Routing](using_wildcards/)** - Master the fundamentals
   - Learn wildcard value extraction and usage
   - Understand `$context.vars.{name}` patterns  
   - Practice with building automation and UNS examples

2. **Advance to [Content-Based Routing](using_set_context_vars/)** - Add business logic
   - Master setContextVars rule for payload-driven routing
   - Implement multi-tenant and conditional routing patterns
   - Build enterprise-grade routing systems with complex logic

3. **Combine Both Approaches** - Handle complex enterprise scenarios
   - Use topic structure AND payload content for routing decisions
   - Implement hybrid routing patterns for maximum flexibility

### 💡 **Quick Decision Guide**

**Choose Topic-Based Routing When:**
- Routing information is **embedded in topic structure**
- Need **simple, predictable routing** patterns
- **Legacy system integration** where topic hierarchy matters
- **Performance is critical** (no payload processing overhead)

**Choose Content-Based Routing When:**
- Routing depends on **message content** or business rules
- Need **complex conditional logic** based on data values
- **Multi-tenant systems** with tenant info in payload
- **Business process automation** with dynamic routing rules

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

**👉 See [using_wildcards/wildcard-routing.scf.yaml](using_wildcards/wildcard-routing.scf.yaml) for complete UNS implementation example with ISA-95 compliance.**