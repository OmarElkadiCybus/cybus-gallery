# Cybus Gallery

**Production-ready examples for Industrial IoT with Cybus Connectware**

Connect industrial devices, transform data, and monitor your systems with proven service configuration templates and processing rules.

## 🔌 Connecting Systems

**Industrial Protocols** (12 supported):
- **[OPC UA](./connecting-systems/opcua/)** - Industry 4.0 standard protocol
- **[Modbus TCP](./connecting-systems/modbus/)** - Read/write Modbus devices  
- **[Siemens S7](./connecting-systems/siemens-s7/)** - Connect to Siemens PLCs
- **[EtherNet/IP](./connecting-systems/ethernetip/)** - Allen-Bradley PLCs and devices
- **[ADS](./connecting-systems/ads/)** - Beckhoff TwinCAT automation
- **[BACnet](./connecting-systems/bacnet/)** - Building automation protocol
- **[FOCAS](./connecting-systems/focas/)** - FANUC CNC machine integration
- **[OpenProtocol](./connecting-systems/openprotocol/)** - Atlas Copco tools
- **[SOPAS](./connecting-systems/sopas/)** - SICK sensor integration
- **[Heidenhain DNC](./connecting-systems/heidenhain-dnc/)** - CNC data collection

**Cloud & Web Integration**:
- **[AWS IoT](./connecting-systems/aws-iot/)** - Amazon IoT Core & Greengrass
- **[Azure IoT](./connecting-systems/azure-iot/)** - Microsoft IoT Hub & Edge
- **[HTTP Integration](./connecting-systems/http-post-mqtt/)** - RESTful API bridging
- **[Custom TCP](./connecting-systems/custom-tcp/)** - Build custom connectors

**Security & Management**:
- **[TLS Certificates](./connecting-systems/mqtt-tls-certificates/)** - Secure MQTT connections
- **[User Management](./connecting-systems/user-management/)** - Access control setup

**[→ All connection examples](./connecting-systems/)**

## 🔄 Data Processing

**Core Processing Rules**:
- **[Transform](./data-processing/01_transform/)** - Convert JSON data with JSONata
- **[Filter](./data-processing/02_filter/)** - Conditional data processing
- **[COV (Change of Value)](./data-processing/06_cov/)** - Publish only on changes
- **[Collect](./data-processing/07_collect/)** - Aggregate multiple data sources

**Advanced Patterns**:
- **[Array Processing](./data-processing/01_array/)** - Handle multi-device data
- **[Wildcard Topics](./data-processing/02_wildcards/)** - Dynamic topic matching  
- **[Stash & Context](./data-processing/04_stash/)** - State management
- **[Dynamic Publishing](./data-processing/04_dynamic_publish_topic/)** - Content-based routing
- **[Burst Detection](./data-processing/08_burst/)** - High-frequency event handling

**[→ All data processing examples](./data-processing/)**

## 📊 Monitoring

Monitor and visualize your Industrial IoT data:

- **[Elasticsearch & Kibana](./monitoring/elasticsearch/)** - Search and visualize data
- **[Grafana & Loki](./monitoring/grafana-loki/)** - Log aggregation and monitoring
- **[Azure Monitoring](./monitoring/azure-monitoring/)** - Cloud-based monitoring

**[→ All monitoring examples](./monitoring/)**

## 🚀 Quick Start

1. **Install Cybus Connectware** (requires license):
   ```bash
   docker run --rm cybus/connectware-cli --version
   ```

2. **Deploy an example**:
   ```bash
   cd connecting-systems/opcua
   docker run --rm -v ${PWD}:/workspace cybus/connectware-cli deploy /workspace/opcua-example-commissioning-file.yml
   ```

3. **Process data**:
   ```bash
   cd data-processing/01_transform  
   docker run --rm -v ${PWD}:/workspace cybus/connectware-cli deploy /workspace/machine-monitoring.scf.yaml
   ```

## 📚 Documentation

- **[Cybus Connectware Docs](https://docs.cybus.io/)** - Complete platform documentation
- **[Protocol Details](https://docs.cybus.io/documentation/industry-protocol-details)** - Industrial protocol guides
- **[Service Configuration](https://docs.cybus.io/documentation/services)** - SCF file reference
- **[Data Processing](https://docs.cybus.io/documentation/services/data-processing-rules)** - Processing rules guide

## 🎯 Use Cases

**Manufacturing**: Connect PLCs, robots, and machines for real-time monitoring  
**Energy**: Integrate SCADA systems with cloud analytics  
**Building Automation**: BACnet integration with modern dashboards  
**Automotive**: CNC machine data collection and quality control

## 🤝 Contributing

1. Fork this repository
2. Create examples following our standardized template structure
3. Test with real devices where possible
4. Submit pull request with clear documentation

## 📞 Support

- **Documentation**: [docs.cybus.io](https://docs.cybus.io/)
- **Support Portal**: [support.cybus.io](https://support.cybus.io/)
- **Professional Services**: Contact [Cybus](https://www.cybus.io/) for implementation support

## 📜 License

MIT License - see [LICENSE](LICENSE) file for details.

---

**🏭 Industrial IoT Integration Made Simple**  
*Powered by [Cybus Connectware](https://www.cybus.io/)*