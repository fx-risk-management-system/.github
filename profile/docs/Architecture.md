## 🧭 System Design

This system follows a modular, event-driven architecture centered on real-time FX data processing, volatility analysis, and risk management. Below is a high-level design diagram showcasing how data flows through the various layers:

### 📈 Architecture Diagram

<details>
<summary>Click to expand Mermaid diagram</summary>

```mermaid
flowchart TD
    %% External Data Sources
    subgraph ext_sources["External Data Sources"]
        FXDataAPI["FX Market Data APIs"]
        CoreBanking["Core Banking System"]
        ClientTxn["Client Transactions"]
        MarketAnalytics["Market Analytics"]
    end

    %% Data Ingestion Layer - Kafka
    subgraph kafka["Data Ingestion Layer (Kafka)"]
        fx_market_topic["fx-market-data Topic"]
        client_pos_topic["client-fx-positions Topic"]
        fx_alerts_topic["fx-alerts Topic"]
        fx_volatility_topic["fx-volatility Topic"]
    end

    %% Stream Processing Layer - Flink
    subgraph flink["Stream Processing Layer (Apache Flink)"]
        fx_rate_processor["FX Rate Processor"]
        volatility_calc["Volatility Calculator"]
        position_risk_detector["Position Risk Detector"]
        alert_generator["Alert Generator"]
    end

    %% Business Logic Layer - Spring Boot Services
    subgraph spring_services["Business Logic Layer (Spring Boot)"]
        fx_market_service["FX Market Data Service"]
        risk_analytics_service["Risk Analytics Service"]
        hedging_service["Hedging Recommendation Service"]
        position_service["Position Tracking Service"]
        notification_service["Notification Service"]
    end

    %% Data Storage Layer
    subgraph storage["Data Storage Layer"]
        postgres["PostgreSQL\nClient Data & Positions"]
        redis["Redis Cache\nReal-time Rates"]
        timescaledb["TimescaleDB\nTime-series Data"]
        elastic["Elasticsearch\nLogs & Analytics"]
    end

    %% API & UI Layer
    subgraph api_ui["API & UI Layer"]
        rest_api["REST APIs"]
        client_dashboard["Client Dashboard"]
        admin_console["Admin Console"]
        notification_ui["Notification System"]
    end

    %% Data Flows
    FXDataAPI -->|"Real-time rates"| fx_market_topic
    CoreBanking -->|"Account data"| fx_market_topic
    ClientTxn -->|"FX transactions"| client_pos_topic
    MarketAnalytics -->|"Market data"| fx_market_topic

    %% Kafka to Flink
    fx_market_topic -->|"Stream processing"| fx_rate_processor
    fx_market_topic -->|"Calculate volatility"| volatility_calc
    client_pos_topic -->|"Analyze risk"| position_risk_detector
    
    volatility_calc -->|"Publish metrics"| fx_volatility_topic
    position_risk_detector -->|"Generate alerts"| fx_alerts_topic
    
    %% Flink to Services
    fx_rate_processor -->|"Processed rates"| fx_market_service
    volatility_calc -->|"Volatility metrics"| risk_analytics_service
    position_risk_detector -->|"Risk positions"| position_service
    alert_generator -->|"Risk alerts"| notification_service
    
    %% Service interconnections
    fx_market_service <-->|"Exchange rates"| risk_analytics_service
    position_service -->|"Client positions"| risk_analytics_service
    risk_analytics_service -->|"Risk profiles"| hedging_service
    risk_analytics_service -->|"Risk thresholds"| notification_service
    
    %% Services to Storage
    fx_market_service -->|"Store rates"| timescaledb
    fx_market_service -->|"Cache rates"| redis
    risk_analytics_service -->|"Store risk metrics"| timescaledb
    position_service -->|"Store positions"| postgres
    notification_service -->|"Store alerts"| elastic
    hedging_service -->|"Store recommendations"| postgres
    
    %% Storage to API/UI
    postgres --> rest_api
    redis --> rest_api
    timescaledb --> rest_api
    elastic --> rest_api
    
    %% API to UI
    rest_api --> client_dashboard
    rest_api --> admin_console
    rest_api --> notification_ui
    
    %% Styling
    classDef external fill:#D4F1F9,stroke:#0078D7,stroke-width:2px
    classDef kafka fill:#E8F6EF,stroke:#1B9C85,stroke-width:2px
    classDef flink fill:#FFF6DC,stroke:#F2BE22,stroke-width:2px
    classDef spring fill:#E3DFFD,stroke:#7071E8,stroke-width:2px
    classDef storage fill:#FFDBAA,stroke:#FF8551,stroke-width:2px
    classDef api fill:#D2E9E9,stroke:#628E90,stroke-width:2px
    
    class ext_sources external
    class kafka kafka
    class flink flink
    class spring_services spring
    class storage storage
    class api_ui api

📌 For better readability, [click here to view the full zoomable diagram](designs/fx-risk-mermaid-architecture.mermaid).

