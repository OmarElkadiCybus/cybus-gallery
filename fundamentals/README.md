# 📚 Fundamentals

**Start your Cybus Connectware journey here!** These examples cover the core concepts and building blocks that every Cybus Connectware developer should master.

## 🎯 What You'll Learn

This section focuses on **foundational concepts** that form the basis of all Cybus Connectware applications:

- **Data Processing Rules** - The heart of message transformation and routing
- **Service Configuration** - Basic service structure and resource definitions
- **JSONata Expressions** - The expression language for data manipulation
- **Rule Chains** - Combining multiple rules for complex processing

## 📊 Content Overview

### 🔧 [Data Processing Rules](./data-processing-rules/)

Master the 8 essential rule types that power Cybus Connectware's data processing engine:

| Rule Type | Purpose | Difficulty | Key Concepts |
|-----------|---------|------------|--------------|
| **Transform** | Modify and reshape data | ⭐ Beginner | JSONata expressions, field mapping |
| **Filter** | Select data based on conditions | ⭐ Beginner | Conditional logic, boolean expressions |
| **Parse** | Extract structured data | ⭐⭐ Intermediate | JSON parsing, regex patterns |
| **Stash** | Store data for comparison | ⭐⭐ Intermediate | State management, temporal data |
| **SetContextVars** | Set dynamic variables | ⭐⭐ Intermediate | Context manipulation, variable scope |
| **COV** | Change of value detection | ⭐⭐ Intermediate | Event-driven processing, delta detection |
| **Collect** | Combine multiple sources | ⭐⭐⭐ Advanced | Data aggregation, multi-source correlation |
| **Burst** | Split arrays into messages | ⭐⭐⭐ Advanced | Array processing, message multiplication |

**🎯 Featured Examples:**
- **[Complete Rule Chain](./data-processing-rules/complete_rule_chain_example.scf.yaml)** - Simple 3-rule temperature processing
- **[Stash Patterns](./data-processing-rules/04_stash/)** - 5 different state management patterns
- **[Multi-Station Counter](./data-processing-rules/04_stash/service.scf.yaml#L214)** - Real-world event counting

## 🚀 Recommended Learning Path

### **Phase 1: Core Data Manipulation** ⭐ Beginner
1. **[Transform Rules](./data-processing-rules/01_transform/)** - Learn basic data reshaping
2. **[Filter Rules](./data-processing-rules/02_filter/)** - Understand conditional processing
3. **[Complete Rule Chain](./data-processing-rules/complete_rule_chain_example.scf.yaml)** - See rules working together

### **Phase 2: Advanced Processing** ⭐⭐ Intermediate  
4. **[Parse Rules](./data-processing-rules/03_parse/)** - Extract structured data from strings
5. **[Stash Rules](./data-processing-rules/04_stash/)** - Master state management and comparison
6. **[SetContextVars](./data-processing-rules/05_setContextVars/)** - Dynamic variable manipulation
7. **[COV Rules](./data-processing-rules/06_cov/)** - Event-driven change detection

### **Phase 3: Multi-Source Integration** ⭐⭐⭐ Advanced
8. **[Collect Rules](./data-processing-rules/07_collect/)** - Aggregate data from multiple sources
9. **[Burst Rules](./data-processing-rules/08_burst/)** - Handle array processing and message multiplication

## 🎓 Prerequisites

**Before Starting:**
- Basic understanding of JSON data structures
- Familiarity with MQTT topics and messaging concepts
- Access to a Cybus Connectware instance for testing

**Helpful Background:**
- JSONata expression language (learned as you go)
- Basic programming concepts (variables, conditions, loops)
- Industrial IoT communication patterns

## 🏭 Real-World Applications

These fundamentals enable you to build solutions for:

- **🌡️ Sensor Data Processing** - Temperature, pressure, flow monitoring
- **🏭 Production Line Monitoring** - Equipment status, cycle counting
- **📊 Data Validation** - Quality checks, range validation
- **🔄 Protocol Translation** - Convert between different data formats
- **📈 Trend Analysis** - Detect patterns and changes over time

## ➡️ What's Next?

Once you've mastered these fundamentals, you're ready for:

- **🚀 [Enterprise Patterns](../enterprise-patterns/)** - Complex multi-system integration
- **🔌 [Protocol Examples](../protocols/)** - Connect to specific industrial devices
- **🌐 [Cloud Integration](../cloud-integration/)** - Connect to cloud platforms

---

**💡 Tip**: Don't skip the fundamentals! Even experienced developers should review these examples to understand Cybus Connectware's specific approach to data processing.