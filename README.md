# Cybus Gallery

A curated collection of **Cybus Connectware** examples, tutorials, and best practices.

## 🎯 What is this?

This repository contains **real-world examples** and **learning resources** for Cybus Connectware, the Industrial IoT platform. Whether you're a beginner or an expert, you'll find practical examples to help you build better Industrial IoT solutions.

## 📚 What's Inside

### � [Fundamentals](./fundamentals/) - **Start Here!**

Master the core building blocks of Cybus Connectware development:

| Category | Focus | Skill Level | What You'll Learn |
|----------|-------|-------------| ------------------|
| **[Data Processing Rules](./fundamentals/data-processing-rules/)** | Message transformation & routing | ⭐-⭐⭐⭐ | 8 essential rule types with hands-on examples |
| **Service Basics** | Configuration fundamentals | ⭐ | Resource definitions, connections, endpoints |

### 🚀 [Enterprise Patterns](./enterprise-patterns/) - **Advanced Integration**

Enterprise-grade patterns for production-scale Industrial IoT deployments:

| Category | Focus | Skill Level | What You'll Master |
|----------|-------|-------------|-------------------|
| **[Multi-Topic Mapping](./enterprise-patterns/multi-topic-mapping/)** | Complex data integration | ⭐⭐-⭐⭐⭐⭐ | Arrays, wildcards, UNS, dynamic routing |
| **HTTP Integration** | REST API connectivity | ⭐⭐⭐ | Request/response patterns, webhook handling |
| **Industrial Protocols** | Specialized device connectivity | ⭐⭐⭐ | Heidenhain DNC, custom protocols |

### 🔌 [Protocols](./protocols/) - **Device Connectivity** *(Coming Soon)*

Connect to industrial devices and systems:
- **OPC UA** - Industrial automation standard
- **Modbus TCP** - Serial communication over Ethernet  
- **Siemens S7** - PLC communication protocol
- **SOPAS** - SICK sensor protocol
- **Custom TCP** - Build your own connectors

### 🌐 [Cloud Integration](./cloud-integration/) - **Platform Connectivity** *(Coming Soon)*

Integrate with major cloud platforms:
- **AWS IoT Core & Greengrass** - Amazon cloud services
- **Azure IoT Hub & Edge** - Microsoft cloud platform
- **Monitoring & Analytics** - Cloud-based insights

### 🔐 [Security](./security/) - **Production Security** *(Coming Soon)*

Secure your Industrial IoT deployments:
- **TLS Certificates** - Encrypted communication
- **User Management** - Access control and authentication
- **Network Security** - VPN and firewall integration

### 🛠️ [Operations](./operations/) - **Production Deployment** *(Coming Soon)*

Deploy and monitor Cybus Connectware:
- **Logging & Monitoring** - ELK stack integration
- **High Availability** - Clustering and failover
- **Performance Tuning** - Optimization strategies

**🎯 Featured Examples:**
- **[Complete Rule Chain](./fundamentals/data-processing-rules/complete_rule_chain_example.scf.yaml)** - Simple 3-rule temperature processing
- **[Stash Patterns](./fundamentals/data-processing-rules/04_stash/)** - 5 different state management patterns
- **[Multi-Station Counter](./fundamentals/data-processing-rules/04_stash/service.scf.yaml#L214)** - Real-world event counting
- **[Factory Line Analytics](./enterprise-patterns/multi-topic-mapping/03_wildcards_with_collect/02_array_of_topics_patterns/)** - Cross-domain correlation
- **[UNS Implementation](./enterprise-patterns/multi-topic-mapping/04_dynamic_publish_topic/)** - Enterprise namespace transformation

## 🚀 Quick Start

### 1. **Browse Examples**
Each folder contains:
- 📖 **README.md** - Detailed explanations and concepts
- 🔧 **service.scf.yaml** - Ready-to-use Cybus service configurations
- 💡 **Input/Output examples** - See exactly how data flows

### 2. **Try an Example**
```bash
# Clone the repository
git clone https://github.com/cybusio/cybus-gallery.git
cd cybus-gallery

# Start with simple transform example  
cd fundamentals/data-processing-rules/01_transform
```

### 3. **Deploy to Connectware**
```bash
# Upload to your Cybus Connectware instance
cybus-tool upload service.scf.yaml
```

## 🎓 Learning Path

### 👶 **New to Cybus Connectware?** - Start with [Fundamentals](./fundamentals/)
1. **[Transform Rules](./fundamentals/data-processing-rules/01_transform/)** - Learn basic data manipulation
2. **[Filter Rules](./fundamentals/data-processing-rules/02_filter/)** - Understand conditional processing  
3. **[Complete Rule Chain](./fundamentals/data-processing-rules/complete_rule_chain_example.scf.yaml)** - See rules working together

### 🔧 **Ready for Advanced Patterns?** - Continue with [Fundamentals](./fundamentals/)
4. **[Stash Rules](./fundamentals/data-processing-rules/04_stash/)** - Master stateful processing
5. **[Collect Rules](./fundamentals/data-processing-rules/07_collect/)** - Handle multiple data sources
6. **[Multi-Station Counter](./fundamentals/data-processing-rules/04_stash/service.scf.yaml#L214)** - Real-world state management

### 🚀 **Enterprise Integration Expert?** - Advance to [Enterprise Patterns](./enterprise-patterns/)
7. **[Array Mapping](./enterprise-patterns/multi-topic-mapping/01_array/)** - Multi-system integration foundations
8. **[Wildcard Patterns](./enterprise-patterns/multi-topic-mapping/02_wildcards/)** - Scalable topic discovery
9. **[Enterprise Analytics](./enterprise-patterns/multi-topic-mapping/03_wildcards_with_collect/)** - Cross-domain correlation
10. **[UNS Implementation](./enterprise-patterns/multi-topic-mapping/04_dynamic_publish_topic/)** - Unified Namespace transformation

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
