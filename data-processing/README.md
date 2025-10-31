# Data Processing Rules

**Purpose**: Master essential data processing rules for transforming, filtering, and routing Industrial IoT data  
**Complexity**: ⭐ to ⭐⭐⭐ | **Focus**: Progressive rule learning from basic to advanced patterns  
**Prerequisites**: Basic understanding of MQTT topics and JSON data structures

## What You'll Learn

Master the complete data processing pipeline with Cybus Connectware rules:
- ✅ **Data transformation** and format conversion techniques
- ✅ **Message filtering** and conditional processing  
- ✅ **State management** and data persistence patterns
- ✅ **Advanced aggregation** and change detection methods
- ✅ **Rule chaining** for complex data processing workflows

## Learning Path: From Simple to Advanced

Follow this progression to build your data processing expertise:

### 🎯 **Foundation Rules (Start Here)**
Master these essential rules first - they form the core of most data processing workflows:

#### 📁 [01_transform/](./01_transform/) - Data Transformation  
**Complexity**: ⭐ Beginner | **Focus**: Convert and manipulate data formats
- **Essential skill**: JSONata expressions for data conversion
- **Common use**: Format standardization, field mapping, calculations  
- **Best for**: Every project needs transforms - start here!

#### 📁 [02_filter/](./02_filter/) - Conditional Processing
**Complexity**: ⭐ Beginner | **Focus**: Conditional message routing
- **Essential skill**: Boolean logic and filtering conditions
- **Common use**: Quality control, alert filtering, data validation
- **Best for**: Removing unwanted data and routing by conditions

### 🔧 **Intermediate Rules**
Add these capabilities as your processing needs grow:

#### 📁 [03_parse/](./03_parse/) - String and Format Parsing  
**Complexity**: ⭐⭐ Intermediate | **Focus**: Extract data from complex formats
- **Essential skill**: Regular expressions and format parsing
- **Common use**: Legacy system integration, log processing
- **Best for**: Dealing with non-JSON data formats

#### 📁 [05_setContextVars/](./05_setContextVars/) - Dynamic Configuration
**Complexity**: ⭐⭐ Intermediate | **Focus**: Manage runtime variables
- **Essential skill**: Dynamic variable extraction and usage
- **Common use**: Multi-tenant systems, dynamic routing
- **Best for**: Systems requiring runtime configuration changes

### 🚀 **Advanced Rules**  
Master these for enterprise-grade data processing:

#### 📁 [04_stash/](./04_stash/) - State Management
**Complexity**: ⭐⭐⭐ Advanced | **Focus**: Store and retrieve data between messages
- **Essential skill**: Stateful processing and data correlation
- **Common use**: Equipment tracking, process monitoring
- **Best for**: When processing depends on previous messages

#### 📁 [06_cov/](./06_cov/) - Change of Value Detection
**Complexity**: ⭐⭐⭐ Advanced | **Focus**: Detect and process data changes  
- **Essential skill**: Change detection and threshold monitoring
- **Common use**: Alarm systems, efficiency monitoring
- **Best for**: Reducing data volume while catching important changes

#### 📁 [07_collect/](./07_collect/) - Data Aggregation
**Complexity**: ⭐⭐⭐ Advanced | **Focus**: Aggregate data from multiple sources
- **Essential skill**: Multi-source data correlation and caching
- **Common use**: System-wide monitoring, cross-correlation analytics
- **Best for**: Building comprehensive dashboards and analytics

#### 📁 [08_burst/](./08_burst/) - Message Splitting  
**Complexity**: ⭐⭐⭐ Advanced | **Focus**: Split messages into multiple outputs
- **Essential skill**: Array processing and multi-destination routing
- **Common use**: Fan-out patterns, parallel processing
- **Best for**: Distributing data to multiple downstream systems

## Complete Integration Example

### 📁 [Complete Rule Chain](./complete_rule_chain_example.scf.yaml) - All Rules Working Together
See how all data processing rules work together in a real-world industrial monitoring system.

## Beyond Basic Processing

Once you've mastered these foundational rules, advance to:
- **[Advanced Mapping Patterns](../advanced-mapping/)** - Multi-topic patterns, wildcards, and dynamic routing
- **[Connecting Systems](../connecting-systems/)** - Protocol integration and endpoint management

## Quick Reference

| Rule Type | Complexity | Primary Use | When to Use |
|-----------|------------|-------------|-------------|
| **Transform** | ⭐ | Data conversion | Every project - format standardization |
| **Filter** | ⭐ | Conditional routing | Remove unwanted data, apply conditions |
| **Parse** | ⭐⭐ | Format extraction | Legacy systems, non-JSON data |
| **SetContextVars** | ⭐⭐ | Dynamic config | Multi-tenant, runtime variables |
| **Stash** | ⭐⭐⭐ | State management | Cross-message correlation |
| **COV** | ⭐⭐⭐ | Change detection | Efficiency, alarm systems |
| **Collect** | ⭐⭐⭐ | Data aggregation | Multi-source analytics |
| **Burst** | ⭐⭐⭐ | Message splitting | Fan-out, parallel processing |

## Resources

- **[JSONata Documentation](https://jsonata.org/)** - Master the expression language used in all rules
- **[Cybus Services Documentation](https://docs.cybus.io/documentation/services)** - Complete service configuration reference