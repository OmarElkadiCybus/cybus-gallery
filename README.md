# Cybus Gallery

A curated collection of **Cybus Connectware** examples, tutorials, and best practices.

## 🎯 What is this?

This repository contains **real-world examples** and **learning resources** for Cybus Connectware, the Industrial IoT platform. Whether you're a beginner or an expert, you'll find practical examples to help you build better Industrial IoT solutions.

## 📚 What's Inside

### 📊 [Data Processing Rules Examples](./data_processing_rules_examples/)

Complete guide to Cybus Connectware's data processing rules with hands-on examples:

| Rule Type | Purpose | Difficulty | Examples |
|-----------|---------|------------|----------|
| **Transform** | Modify and reshape data | ⭐ Beginner | JSONata expressions, field mapping |
| **Filter** | Select data based on conditions | ⭐ Beginner | Threshold filtering, conditional logic |
| **Parse** | Extract structured data | ⭐⭐ Intermediate | JSON parsing, regex extraction |
| **Stash** | Store data for comparison | ⭐⭐ Intermediate | Current vs previous, trend detection |
| **SetContextVars** | Set dynamic variables | ⭐⭐ Intermediate | Dynamic routing, stateful processing |
| **COV** | Change of value detection | ⭐⭐ Intermediate | Event-driven processing |
| **Collect** | Combine multiple sources | ⭐⭐⭐ Advanced | Multi-sensor aggregation |
| **Burst** | Split arrays into messages | ⭐⭐⭐ Advanced | Batch processing, array handling |

### 🚀 [Advanced Data Mapping](./advanced_data_mapping/)

Master enterprise-grade multi-topic data integration and UNS (Unified Namespace) patterns:

| Approach | Complexity | Purpose | Best For |
|----------|------------|---------|----------|
| **[Arrays](./advanced_data_mapping/01_array/)** | ⭐ Basic | Different topic roots, mixed endpoints | Different systems, precise control |
| **[Wildcards](./advanced_data_mapping/02_wildcards/)** | ⭐⭐ Intermediate | Pattern matching, auto-discovery | Hierarchical data, device fleets |
| **[Wildcards + Collect](./advanced_data_mapping/03_wildcards_with_collect/)** | ⭐⭐⭐ Advanced | Cross-correlation, analytics | Enterprise integration, MES systems |
| **[Dynamic Publish](./advanced_data_mapping/04_dynamic_publish_topic/)** | ⭐⭐⭐⭐ Expert | Content-based routing, UNS | Multi-tenant, business logic routing |

**🎯 Featured Examples:**
- **[Complete Rule Chain](./data_processing_rules_examples/complete_rule_chain_example.scf.yaml)** - Simple 3-rule temperature processing
- **[Stash Patterns](./data_processing_rules_examples/04_stash/)** - 5 different stash usage patterns
- **[Multi-Station Counter](./data_processing_rules_examples/04_stash/service.scf.yaml#L214)** - Real-world per-station event counting
- **[Factory Line Analytics](./advanced_data_mapping/03_wildcards_with_collect/02_array_of_topics_patterns/)** - Cross-domain correlation
- **[UNS Implementation](./advanced_data_mapping/04_dynamic_publish_topic/)** - Enterprise namespace transformation

## 🚀 Quick Start

### 1. **Browse Examples**
Each folder contains:
- 📖 **README.md** - Detailed explanations and concepts
- 🔧 **service.scf.yaml** - Ready-to-use Cybus service configurations
- 💡 **Input/Output examples** - See exactly how data flows

### 2. **Try an Example**
```bash
# Clone the repository
git clone https://github.com/OmarElkadiCybus/cybus-gallery.git
cd cybus-gallery

# Start with simple transform example
cd data_processing_rules_examples/01_transform
```

### 3. **Deploy to Connectware**
```bash
# Upload to your Cybus Connectware instance
cybus-tool upload service.scf.yaml
```

## 🎓 Learning Path

**👶 New to Cybus Connectware?**
1. Start with **[Transform Rules](./data_processing_rules_examples/01_transform/)** - Learn basic data manipulation
2. Try **[Filter Rules](./data_processing_rules_examples/02_filter/)** - Understand conditional processing
3. Explore **[Complete Rule Chain](./data_processing_rules_examples/complete_rule_chain_example.scf.yaml)** - See rules working together

**🔧 Ready for Advanced Patterns?**
1. **[Stash Rules](./data_processing_rules_examples/04_stash/)** - Master stateful processing
2. **[Collect Rules](./data_processing_rules_examples/07_collect/)** - Handle multiple data sources
3. **[Multi-Station Counter Pattern](./data_processing_rules_examples/04_stash/service.scf.yaml#L214)** - Real-world state management

**🚀 Enterprise Integration Expert?**
1. **[Array Mapping](./advanced_data_mapping/01_array/)** - Multi-system integration foundations
2. **[Wildcard Patterns](./advanced_data_mapping/02_wildcards/)** - Scalable topic discovery
3. **[Enterprise Analytics](./advanced_data_mapping/03_wildcards_with_collect/)** - Cross-domain correlation
4. **[UNS Implementation](./advanced_data_mapping/04_dynamic_publish_topic/)** - Unified Namespace transformation

## 🏭 Real-World Use Cases

These examples are based on **actual Industrial IoT scenarios**:

### 🔧 **Data Processing Scenarios**
- **🌡️ Temperature Monitoring** - Sensor data processing with alerts
- **🏭 Production Line Tracking** - Multi-station event counting
- **📊 Data Validation** - Compare current vs historical values
- **🔄 Protocol Translation** - Transform between different data formats
- **📈 Trend Detection** - Identify changes and patterns in sensor data

### 🏢 **Enterprise Integration Scenarios**
- **🏗️ Manufacturing Execution Systems (MES)** - Cross-domain production analytics
- **🌐 Unified Namespace (UNS)** - Enterprise-wide data standardization
- **🏢 Multi-Tenant SaaS** - Content-based routing and isolation
- **📱 Building Automation** - Hierarchical sensor data aggregation
- **🚗 Fleet Management** - Device discovery and health monitoring
- **⚡ Legacy System Modernization** - Transform to ISA-95 compliance

## 🤝 Contributing

Found a bug? Have a great example? Want to improve documentation?

1. **Fork the repository**
2. **Create your example** following the existing structure
3. **Submit a pull request**

### 📝 Example Structure
```
your_example/
├── README.md           # Clear explanation and concepts
├── service.scf.yaml    # Working Cybus service configuration  
└── examples/           # Input/output samples (optional)
```

## 📖 Documentation

- **[Cybus Connectware Documentation](https://docs.cybus.io/)**
- **[JSONata Expression Language](https://jsonata.org/)**
- **[MQTT Protocol Guide](https://mqtt.org/)**

## 📞 Support

- **Documentation**: [docs.cybus.io](https://docs.cybus.io/)
- **Issues**: [GitHub Issues](https://github.com/OmarElkadiCybus/cybus-gallery/issues)

## 📜 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

---

**Made with ❤️ by the Cybus Community**

*Industrial IoT made simple with Cybus Connectware*
