# ДИАГРАММЫ АРХИТЕКТУРЫ EMERGENCY GRANT MANAGEMENT SYSTEM

## 1. HIGH-LEVEL GRANT CYCLE FLOW

```mermaid
graph TB
    Start([Начало: Crisis/Gap Detected]) --> GapID[ЭТАП 1: Gap Identification]
    
    GapID --> |DHIS2 Data + AI Analysis| Priority[Gap Prioritization]
    Priority --> Fund[ЭТАП 2: Funding Request]
    
    Fund --> |AI Screening| Review[ЭТАП 3: Grant Review]
    Review --> |AI Scoring + Human Expert| Decision{Approval Decision}
    
    Decision -->|Approved| Award[ЭТАП 4: Grant Award]
    Decision -->|Rejected| Feedback[Feedback to Applicant]
    Feedback --> Fund
    
    Award --> |Blockchain Registration| Disbursement[ЭТАП 5: Fund Disbursement]
    Disbursement --> |Smart Contracts| Implementation[ЭТАП 6: Implementation]
    
    Implementation --> |Real-time Monitoring| Reporting[ЭТАП 7: Reporting & Compliance]
    Reporting --> |AI Validation| Impact[ЭТАП 8: Impact Evaluation]
    
    Impact --> |Proof-of-Impact NFT| Closure[Grant Closure]
    Closure --> |Lessons Learned| Knowledge[(Knowledge Base)]
    
    Knowledge -.-> GapID
    
    style GapID fill:#e1f5ff
    style Review fill:#fff4e1
    style Disbursement fill:#e8f5e9
    style Impact fill:#f3e5f5
    style Closure fill:#e0e0e0
```

---

## 2. DETAILED GRANT CYCLE WITH AI & BLOCKCHAIN TOUCHPOINTS

```mermaid
graph TB
    subgraph "ЭТАП 1: GAP IDENTIFICATION"
        G1[DHIS2 Real-time Data] --> G2[AI Gap Detection]
        G2 --> G3[Statistical Analysis]
        G3 --> G4[Risk Assessment ML]
        G4 --> G5[Prioritized Gap List]
    end
    
    subgraph "ЭТАП 2: FUNDING REQUEST"
        F1[Application Submission] --> F2[AI Eligibility Check]
        F2 --> F3{Eligible?}
        F3 -->|Yes| F4[NLP Document Analysis]
        F3 -->|No| F5[Auto-Rejection + Feedback]
        F4 --> F6[Smart Reviewer Assignment]
    end
    
    subgraph "ЭТАП 3: REVIEW & EVALUATION"
        R1[Technical Review] --> R2[AI Scoring Assistant]
        R2 --> R3[Financial ML Analysis]
        R3 --> R4[Risk Prediction Model]
        R4 --> R5[Human Expert Review]
        R5 --> R6[Consensus & Ranking]
    end
    
    subgraph "ЭТАП 4: APPROVAL & AWARD"
        A1[Grant Committee] --> A2[Decision]
        A2 --> A3[Smart Contract Creation]
        A3 --> A4[Blockchain Registration]
        A4 --> A5[Grant Agreement NFT]
    end
    
    subgraph "ЭТАП 5: DISBURSEMENT"
        D1[Milestone Definition] --> D2[Smart Contract Logic]
        D2 --> D3[AML/Fraud AI Check]
        D3 --> D4{Clear?}
        D4 -->|Yes| D5[Blockchain Transaction]
        D4 -->|No| D6[Investigation]
        D5 --> D7[Funds Released]
    end
    
    subgraph "ЭТАП 6: IMPLEMENTATION MONITORING"
        I1[Activity Data Collection] --> I2[AI Anomaly Detection]
        I2 --> I3[Real-time Risk Alerts]
        I3 --> I4[DHIS2 Data Validation]
        I4 --> I5[Blockchain Evidence Logging]
    end
    
    subgraph "ЭТАП 7: REPORTING"
        RP1[Report Submission] --> RP2[AI Completeness Check]
        RP2 --> RP3[NLP Quality Assessment]
        RP3 --> RP4[Compliance Validation]
        RP4 --> RP5[Blockchain Record]
    end
    
    subgraph "ЭТАП 8: IMPACT EVALUATION"
        E1[Impact Measurement] --> E2[AI Multi-source Validation]
        E2 --> E3[Computer Vision Verification]
        E3 --> E4[Statistical Validation]
        E4 --> E5[Proof-of-Impact NFT Mint]
        E5 --> E6[Blockchain Certificate]
    end
    
    G5 --> F1
    F6 --> R1
    R6 --> A1
    A5 --> D1
    D7 --> I1
    I5 --> RP1
    RP5 --> E1
    E6 --> End([Grant Closure])
    
    style G2 fill:#ffeb3b
    style F2 fill:#ffeb3b
    style F4 fill:#ffeb3b
    style R2 fill:#ffeb3b
    style R3 fill:#ffeb3b
    style D3 fill:#ffeb3b
    style I2 fill:#ffeb3b
    style RP2 fill:#ffeb3b
    style E2 fill:#ffeb3b
    
    style A3 fill:#4caf50
    style A4 fill:#4caf50
    style D2 fill:#4caf50
    style D5 fill:#4caf50
    style I5 fill:#4caf50
    style RP5 fill:#4caf50
    style E6 fill:#4caf50
```

**Легенда:**
- 🟡 Желтый = AI/ML Processing
- 🟢 Зеленый = Blockchain Operations

---

## 3. SYSTEM ARCHITECTURE - LAYERED VIEW

```mermaid
graph TB
    subgraph "PRESENTATION LAYER"
        UI1[Web Portal - React]
        UI2[Mobile Apps - React Native]
        UI3[Admin Dashboard]
        UI4[API Gateway]
    end
    
    subgraph "APPLICATION LAYER - Microservices"
        MS1[Application Service]
        MS2[Review Service]
        MS3[Financial Service]
        MS4[Monitoring Service]
        MS5[Reporting Service]
        MS6[Notification Service]
    end
    
    subgraph "AI/ML LAYER"
        AI1[NLP Service<br/>GPT-4/Claude]
        AI2[ML Scoring Engine<br/>TensorFlow/PyTorch]
        AI3[Computer Vision<br/>OpenCV/Detectron2]
        AI4[Fraud Detection<br/>Anomaly ML]
        AI5[Predictive Analytics<br/>Time-series ML]
    end
    
    subgraph "BLOCKCHAIN LAYER"
        BC1[Smart Contract Engine<br/>Solidity/Web3]
        BC2[Transaction Manager]
        BC3[Ethereum/Polygon Node]
        BC4[IPFS Storage]
    end
    
    subgraph "INTEGRATION LAYER"
        INT1[DHIS2 Connector]
        INT2[Payment Gateway]
        INT3[Email/SMS Service]
        INT4[External APIs]
    end
    
    subgraph "DATA LAYER"
        DB1[(PostgreSQL<br/>Relational Data)]
        DB2[(MongoDB<br/>Documents)]
        DB3[(Redis<br/>Cache)]
        DB4[(Elasticsearch<br/>Search/Analytics)]
    end
    
    subgraph "INFRASTRUCTURE LAYER"
        INF1[Kubernetes Cluster]
        INF2[Load Balancer]
        INF3[Message Queue<br/>RabbitMQ/Kafka]
        INF4[Monitoring<br/>Prometheus/Grafana]
    end
    
    UI1 & UI2 & UI3 --> UI4
    UI4 --> MS1 & MS2 & MS3 & MS4 & MS5 & MS6
    
    MS1 --> AI1
    MS2 --> AI2
    MS3 --> AI4
    MS4 --> AI3 & AI5
    
    MS1 & MS2 & MS3 --> BC1
    BC1 --> BC2
    BC2 --> BC3
    BC3 --> BC4
    
    MS4 --> INT1
    MS3 --> INT2
    MS6 --> INT3
    MS1 & MS2 & MS4 --> INT4
    
    MS1 & MS2 & MS3 & MS4 & MS5 --> DB1
    MS1 & MS5 --> DB2
    MS1 & MS2 & MS3 --> DB3
    MS4 & MS5 --> DB4
    
    AI1 & AI2 & AI3 & AI4 & AI5 -.-> DB3
    
    MS1 & MS2 & MS3 & MS4 & MS5 & MS6 --> INF3
    
    INF1 -.-> INF2
    INF1 -.-> MS1 & MS2 & MS3 & MS4 & MS5 & MS6
    INF4 -.-> INF1
    
    style AI1 fill:#fff9c4
    style AI2 fill:#fff9c4
    style AI3 fill:#fff9c4
    style AI4 fill:#fff9c4
    style AI5 fill:#fff9c4
    
    style BC1 fill:#c8e6c9
    style BC2 fill:#c8e6c9
    style BC3 fill:#c8e6c9
    style BC4 fill:#c8e6c9
```

---

## 4. DATA FLOW ARCHITECTURE

```mermaid
graph LR
    subgraph "EXTERNAL DATA SOURCES"
        EXT1[DHIS2<br/>Health Data]
        EXT2[Applicant<br/>Submissions]
        EXT3[Bank<br/>Systems]
        EXT4[Field<br/>Reports]
    end
    
    subgraph "DATA INGESTION"
        ING1[API Gateway]
        ING2[ETL Pipeline]
        ING3[Data Validation]
        ING4[Streaming Processor]
    end
    
    subgraph "DATA PROCESSING"
        PROC1[AI/ML<br/>Processing]
        PROC2[Business<br/>Logic]
        PROC3[Blockchain<br/>Validation]
        PROC4[Aggregation<br/>& Analytics]
    end
    
    subgraph "DATA STORAGE"
        STORE1[(Operational<br/>Database)]
        STORE2[(Document<br/>Store)]
        STORE3[(Blockchain<br/>Ledger)]
        STORE4[(Data<br/>Warehouse)]
    end
    
    subgraph "DATA CONSUMPTION"
        CONS1[Dashboards]
        CONS2[Reports]
        CONS3[Alerts]
        CONS4[APIs]
        CONS5[Analytics]
    end
    
    EXT1 --> ING1
    EXT2 --> ING1
    EXT3 --> ING2
    EXT4 --> ING4
    
    ING1 --> ING3
    ING2 --> ING3
    ING4 --> ING3
    
    ING3 --> PROC1
    ING3 --> PROC2
    ING3 --> PROC3
    
    PROC1 --> STORE1
    PROC2 --> STORE1
    PROC3 --> STORE3
    
    STORE1 --> PROC4
    STORE2 --> PROC4
    STORE3 --> PROC4
    
    PROC4 --> STORE4
    
    STORE1 --> CONS1
    STORE4 --> CONS2
    PROC1 --> CONS3
    STORE1 --> CONS4
    STORE4 --> CONS5
    
    style PROC1 fill:#ffe082
    style PROC3 fill:#a5d6a7
    style STORE3 fill:#a5d6a7
```

---

## 5. BLOCKCHAIN INTEGRATION DETAILED FLOW

```mermaid
sequenceDiagram
    participant App as Applicant
    participant Portal as Web Portal
    participant API as API Gateway
    participant Grant as Grant Service
    participant BC as Blockchain Service
    participant SC as Smart Contract
    participant IPFS as IPFS Storage
    participant Chain as Ethereum/Polygon
    
    App->>Portal: Submit Grant Application
    Portal->>API: POST /applications
    API->>Grant: Create Application
    
    Note over Grant: AI Eligibility Check
    
    Grant->>IPFS: Store Application Documents
    IPFS-->>Grant: Return IPFS Hash
    
    Grant->>BC: Register Application
    BC->>SC: createApplication(hash, metadata)
    SC->>Chain: Transaction
    Chain-->>SC: Transaction Hash
    SC-->>BC: Application ID + Tx Hash
    BC-->>Grant: Blockchain Record
    
    Note over Grant: Review Process (AI + Human)
    
    Grant->>BC: Update Status: Approved
    BC->>SC: approveGrant(grantId)
    SC->>Chain: State Change
    
    Note over SC: Smart Contract:<br/>Grant Agreement Created
    
    Grant->>BC: Create Milestones
    BC->>SC: defineMilestones(grantId, milestones[])
    SC->>Chain: Milestone Data
    
    Note over App: Grant Implementation
    
    App->>Portal: Submit Milestone Evidence
    Portal->>IPFS: Store Evidence Files
    IPFS-->>Portal: Evidence Hash
    
    Portal->>Grant: Complete Milestone + Evidence
    Grant->>BC: Validate & Disburse
    
    Note over BC: AI Validation:<br/>- Evidence Check<br/>- Fraud Detection<br/>- DHIS2 Cross-check
    
    BC->>SC: completeMilestone(grantId, milestoneId, evidenceHash)
    SC->>Chain: Update State
    SC->>Chain: Transfer Funds
    Chain-->>App: Funds Released
    
    Note over App: All Milestones Complete
    
    Grant->>BC: Request Impact Validation
    
    Note over BC: Multi-layer AI Validation:<br/>1. Data Validation<br/>2. Evidence Assessment<br/>3. Impact Scoring
    
    BC->>SC: mintProofOfImpact(grantId, impactData)
    SC->>Chain: Create NFT
    Chain-->>BC: NFT Token ID
    BC-->>Grant: Proof-of-Impact Certificate
    
    Grant->>App: Grant Closure + NFT Certificate
```

---

## 6. PROOF-OF-IMPACT VALIDATION FLOW

```mermaid
graph TB
    Start([Impact Measurement Triggered]) --> Collect[Collect Impact Data]
    
    Collect --> Source1[DHIS2 Data]
    Collect --> Source2[Field Reports]
    Collect --> Source3[Photos/Videos]
    Collect --> Source4[Financial Records]
    Collect --> Source5[Beneficiary Surveys]
    
    Source1 & Source2 & Source3 & Source4 & Source5 --> Layer1[LAYER 1: Automated Data Validation]
    
    subgraph "LAYER 1: Automated Validation"
        L1A[Cross-check vs DHIS2]
        L1B[Statistical Plausibility]
        L1C[Time-series Consistency]
        L1D[Peer Benchmarking]
    end
    
    Layer1 --> L1A & L1B & L1C & L1D
    L1A & L1B & L1C & L1D --> Score1{Score >= 70%?}
    
    Score1 -->|No| Flag1[Flag for Manual Review]
    Score1 -->|Yes| Layer2[LAYER 2: AI Evidence Assessment]
    
    subgraph "LAYER 2: AI Evidence"
        L2A[Computer Vision:<br/>Photo Analysis]
        L2B[NLP:<br/>Report Analysis]
        L2C[Geolocation<br/>Verification]
        L2D[Timestamp<br/>Verification]
    end
    
    Layer2 --> L2A & L2B & L2C & L2D
    L2A & L2B & L2C & L2D --> Score2{Score >= 75%?}
    
    Score2 -->|No| Flag2[Flag for Manual Review]
    Score2 -->|Yes| Layer3[LAYER 3: Smart Contract Verification]
    
    subgraph "LAYER 3: Blockchain"
        L3A[Multi-validator<br/>Consensus]
        L3B[Weighted Scoring<br/>by Reputation]
        L3C[On-chain<br/>Record]
    end
    
    Layer3 --> L3A
    L3A --> L3B
    L3B --> L3C
    L3C --> Score3{Consensus >= 80%?}
    
    Score3 -->|No| Flag3[Flag for Manual Review]
    Score3 -->|Yes| Sample{High-value<br/>Grant?}
    
    Sample -->|Yes >$500K| Layer4[LAYER 4: Human Expert Review]
    Sample -->|No| Approve[Generate PoI Certificate]
    
    subgraph "LAYER 4: Human Expert"
        L4A[Spot Check<br/>Sample]
        L4B[Independent<br/>Evaluation]
        L4C[Beneficiary<br/>Verification]
    end
    
    Layer4 --> L4A & L4B & L4C
    L4A & L4B & L4C --> Final{Expert<br/>Approval?}
    
    Final -->|Yes| Approve
    Final -->|No| Reject[Reject PoI]
    
    Flag1 & Flag2 & Flag3 --> Manual[Manual Investigation]
    Manual --> ManualDecision{Decision}
    ManualDecision -->|Approve| Approve
    ManualDecision -->|Reject| Reject
    
    Approve --> NFT[Mint PoI NFT]
    NFT --> Blockchain[Record on Blockchain]
    Blockchain --> Certificate[Issue Certificate]
    
    Reject --> Feedback[Detailed Feedback]
    Feedback --> Remediation[Remediation Plan]
    
    style L1A fill:#e3f2fd
    style L1B fill:#e3f2fd
    style L1C fill:#e3f2fd
    style L1D fill:#e3f2fd
    
    style L2A fill:#fff9c4
    style L2B fill:#fff9c4
    style L2C fill:#fff9c4
    style L2D fill:#fff9c4
    
    style L3A fill:#c8e6c9
    style L3B fill:#c8e6c9
    style L3C fill:#c8e6c9
    
    style L4A fill:#f8bbd0
    style L4B fill:#f8bbd0
    style L4C fill:#f8bbd0
```

---

## 7. AI AUTOMATION DECISION MATRIX

```mermaid
graph TB
    Start([Grant Process Step]) --> Q1{Data<br/>Quality?}
    
    Q1 -->|Poor| Manual1[Manual Process]
    Q1 -->|Good| Q2{Risk<br/>Level?}
    
    Q2 -->|Critical| Manual2[Human Decision<br/>+ AI Support]
    Q2 -->|Medium/Low| Q3{Proven<br/>Tech?}
    
    Q3 -->|No| Manual3[Phased Rollout<br/>+ Testing]
    Q3 -->|Yes| Q4{Volume?}
    
    Q4 -->|Low <100/day| Manual4[Optional<br/>Automation]
    Q4 -->|High >100/day| Auto[Full<br/>Automation ✅]
    
    Manual2 --> HumanLoop[Human-in-the-Loop<br/>AI Assistant]
    
    Manual3 --> Pilot[Pilot Program]
    Pilot --> Test{Success?}
    Test -->|Yes| Scale[Scale Up 🔨]
    Test -->|No| Iterate[Iterate]
    
    Auto --> Monitor[Continuous<br/>Monitoring]
    Monitor --> Audit[Regular<br/>Audits]
    
    HumanLoop --> Examples1["Examples:<br/>- Grant Approval<br/>- High-value disbursement<br/>- Complex evaluations"]
    
    Auto --> Examples2["Examples:<br/>- Eligibility screening<br/>- Document classification<br/>- Completeness checks<br/>- Fraud detection alerts"]
    
    Scale --> Examples3["Examples:<br/>- Application scoring<br/>- Risk assessment<br/>- Budget analysis<br/>- Impact validation"]
    
    style Auto fill:#4caf50,color:#fff
    style HumanLoop fill:#ff9800,color:#fff
    style Scale fill:#2196f3,color:#fff
    style Manual1 fill:#f44336,color:#fff
    style Manual2 fill:#ff9800,color:#fff
    style Manual3 fill:#9c27b0,color:#fff
    style Manual4 fill:#607d8b,color:#fff
```

---

## 8. EMERGENCY FUNDING FAST-TRACK PROCESS

```mermaid
graph LR
    subgraph "DAY 0-3: Crisis Detection"
        C1[Gap Detected] --> C2[AI Rapid<br/>Assessment]
        C2 --> C3[Priority<br/>Score]
        C3 --> C4{Emergency?}
        C4 -->|No| Standard[Standard<br/>Process<br/>6-9 months]
        C4 -->|Yes| Fast[FAST-TRACK<br/>30-45 days]
    end
    
    subgraph "DAY 3-10: Application"
        F1[Simplified<br/>Application]
        F2[AI Pre-screening<br/>2-3 days]
        F3[Auto-eligibility<br/>< 24h]
        
        F1 --> F3
        F3 --> F2
    end
    
    subgraph "DAY 10-20: Rapid Review"
        R1[Parallel Review<br/>Technical + Financial]
        R2[AI Risk Score]
        R3[Expert Panel<br/>3-5 days]
        
        R1 --> R2
        R2 --> R3
    end
    
    subgraph "DAY 20-25: Approval"
        A1[Expedited<br/>Committee]
        A2[Conditional<br/>Approval]
        A3[Smart Contract<br/>Setup]
    end
    
    subgraph "DAY 25-30: Disbursement"
        D1[Rapid<br/>Compliance]
        D2[Blockchain<br/>Transaction]
        D3[Funds<br/>Released]
    end
    
    subgraph "ONGOING: Enhanced Monitoring"
        M1[Real-time<br/>Tracking]
        M2[AI Anomaly<br/>Detection]
        M3[Weekly<br/>Check-ins]
    end
    
    Fast --> F1
    F2 --> R1
    R3 --> A1
    A1 --> A2
    A2 --> A3
    A3 --> D1
    D1 --> D2
    D2 --> D3
    D3 --> M1
    M1 --> M2
    M2 --> M3
    
    style C2 fill:#ffeb3b
    style C3 fill:#ffeb3b
    style F2 fill:#ffeb3b
    style F3 fill:#ffeb3b
    style R2 fill:#ffeb3b
    style M2 fill:#ffeb3b
    
    style A3 fill:#4caf50
    style D2 fill:#4caf50
    
    style Fast fill:#ff5722,color:#fff
```

---

## 9. INTEGRATION ARCHITECTURE - DHIS2 FOCUS

```mermaid
graph TB
    subgraph "DHIS2 ECOSYSTEM"
        DHIS1[DHIS2 Core<br/>Health Data]
        DHIS2[Facility Data]
        DHIS3[Commodity Data]
        DHIS4[Indicators DB]
    end
    
    subgraph "INTEGRATION LAYER"
        INT1[REST API<br/>Connector]
        INT2[OAuth2<br/>Authentication]
        INT3[Data Mapping<br/>Engine]
        INT4[ETL Pipeline]
        INT5[Real-time<br/>Webhooks]
    end
    
    subgraph "GRANT MANAGEMENT SYSTEM"
        GMS1[Gap Detection<br/>Service]
        GMS2[Monitoring<br/>Service]
        GMS3[Impact<br/>Validation]
        GMS4[Reporting<br/>Engine]
    end
    
    subgraph "AI/ML PROCESSING"
        AI1[Gap Analysis<br/>ML Model]
        AI2[Anomaly<br/>Detection]
        AI3[Impact<br/>Measurement]
    end
    
    subgraph "DATA STORE"
        DS1[(Time-series<br/>DB)]
        DS2[(Analytics<br/>DB)]
        DS3[(Blockchain<br/>Evidence)]
    end
    
    DHIS1 & DHIS2 & DHIS3 & DHIS4 --> INT1
    INT1 --> INT2
    INT2 --> INT3
    INT3 --> INT4
    INT4 --> INT5
    
    INT5 --> GMS1
    INT5 --> GMS2
    
    GMS1 --> AI1
    GMS2 --> AI2
    GMS3 --> AI3
    
    AI1 --> DS1
    AI2 --> DS1
    AI3 --> DS2
    
    DS1 --> GMS4
    DS2 --> GMS4
    
    GMS3 --> DS3
    
    INT5 -.->|Real-time Sync| GMS2
    
    style INT1 fill:#90caf9
    style INT5 fill:#90caf9
    
    style AI1 fill:#fff176
    style AI2 fill:#fff176
    style AI3 fill:#fff176
    
    style DS3 fill:#a5d6a7
```

---

## 10. SECURITY & COMPLIANCE LAYERS

```mermaid
graph TB
    subgraph "ENTRY POINTS"
        E1[Web Portal]
        E2[Mobile Apps]
        E3[APIs]
        E4[Admin Access]
    end
    
    subgraph "SECURITY LAYER 1: Authentication"
        S1[Multi-Factor Auth<br/>MFA]
        S2[OAuth2/OIDC]
        S3[Role-Based Access<br/>RBAC]
        S4[Session Management]
    end
    
    subgraph "SECURITY LAYER 2: Network"
        N1[WAF<br/>Web Application<br/>Firewall]
        N2[DDoS<br/>Protection]
        N3[SSL/TLS<br/>Encryption]
        N4[API Rate<br/>Limiting]
    end
    
    subgraph "SECURITY LAYER 3: Application"
        A1[Input Validation]
        A2[SQL Injection<br/>Prevention]
        A3[XSS Protection]
        A4[CSRF Tokens]
    end
    
    subgraph "SECURITY LAYER 4: Data"
        D1[Encryption<br/>at Rest]
        D2[Encryption<br/>in Transit]
        D3[Data<br/>Anonymization]
        D4[Audit Logs]
    end
    
    subgraph "COMPLIANCE LAYER"
        C1[GDPR<br/>Compliance]
        C2[AML/KYC<br/>Checks]
        C3[Financial<br/>Regulations]
        C4[Humanitarian<br/>Standards]
    end
    
    subgraph "MONITORING LAYER"
        M1[SIEM<br/>Security Info]
        M2[Intrusion<br/>Detection]
        M3[Anomaly<br/>Detection AI]
        M4[Incident<br/>Response]
    end
    
    E1 & E2 & E3 & E4 --> S1 & S2 & S3 & S4
    S1 & S2 & S3 & S4 --> N1 & N2 & N3 & N4
    N1 & N2 & N3 & N4 --> A1 & A2 & A3 & A4
    A1 & A2 & A3 & A4 --> D1 & D2 & D3 & D4
    
    D1 & D2 & D3 & D4 --> C1 & C2 & C3 & C4
    
    S1 & S2 & S3 & S4 -.-> M1
    N1 & N2 -.-> M2
    A1 & A2 & A3 & A4 -.-> M3
    D4 -.-> M1
    
    M1 & M2 & M3 --> M4
    
    style M3 fill:#ffeb3b
    style D1 fill:#4caf50
    style D2 fill:#4caf50
    style C2 fill:#ff9800
```

---

## 11. DEPLOYMENT ARCHITECTURE - KUBERNETES

```mermaid
graph TB
    subgraph "INTERNET"
        USER[Users]
        CDN[CloudFlare CDN]
    end
    
    subgraph "LOAD BALANCING"
        LB[AWS/Azure<br/>Load Balancer]
        IG[Ingress<br/>Controller]
    end
    
    subgraph "KUBERNETES CLUSTER"
        subgraph "Frontend Namespace"
            POD1[Web Portal<br/>Pods x3]
            POD2[Admin UI<br/>Pods x2]
        end
        
        subgraph "Application Namespace"
            POD3[Grant Service<br/>Pods x5]
            POD4[Review Service<br/>Pods x3]
            POD5[Financial Service<br/>Pods x3]
            POD6[Monitoring Service<br/>Pods x4]
        end
        
        subgraph "AI/ML Namespace"
            POD7[NLP Service<br/>GPU Pods x2]
            POD8[ML Scoring<br/>GPU Pods x2]
            POD9[CV Service<br/>GPU Pods x2]
        end
        
        subgraph "Blockchain Namespace"
            POD10[Blockchain Service<br/>Pods x3]
            POD11[IPFS Node<br/>Pods x2]
        end
        
        subgraph "Data Namespace"
            POD12[PostgreSQL<br/>StatefulSet]
            POD13[MongoDB<br/>StatefulSet]
            POD14[Redis Cluster]
            POD15[Elasticsearch<br/>Cluster]
        end
    end
    
    subgraph "EXTERNAL SERVICES"
        EXT1[Ethereum/Polygon<br/>Node]
        EXT2[DHIS2<br/>API]
        EXT3[Payment<br/>Gateway]
    end
    
    subgraph "MONITORING"
        MON1[Prometheus]
        MON2[Grafana]
        MON3[ELK Stack]
    end
    
    USER --> CDN
    CDN --> LB
    LB --> IG
    
    IG --> POD1
    IG --> POD2
    
    POD1 --> POD3
    POD2 --> POD3 & POD4 & POD5 & POD6
    
    POD3 & POD4 --> POD7 & POD8
    POD6 --> POD9
    POD3 & POD4 & POD5 --> POD10
    
    POD3 & POD4 & POD5 & POD6 --> POD12 & POD13 & POD14 & POD15
    
    POD10 --> EXT1
    POD10 --> POD11
    POD6 --> EXT2
    POD5 --> EXT3
    
    POD3 & POD4 & POD5 & POD6 & POD7 & POD8 & POD9 & POD10 -.-> MON1
    MON1 --> MON2
    POD3 & POD4 & POD5 & POD6 -.-> MON3
    
    style POD7 fill:#fff9c4
    style POD8 fill:#fff9c4
    style POD9 fill:#fff9c4
    
    style POD10 fill:#c8e6c9
    style POD11 fill:#c8e6c9
    
    style POD12 fill:#e1bee7
    style POD13 fill:#e1bee7
    style POD14 fill:#e1bee7
    style POD15 fill:#e1bee7
```

---

## ЛЕГЕНДА ЦВЕТОВ

- 🟡 **Желтый** = AI/ML Processing Components
- 🟢 **Зеленый** = Blockchain Components
- 🔵 **Синий** = Integration/API Components
- 🟣 **Фиолетовый** = Data Storage Components
- 🟠 **Оранжевый** = Human Decision Points
- 🔴 **Красный** = Critical/High-Risk Components

---

## КЛЮЧЕВЫЕ INSIGHTS ИЗ ДИАГРАММ

### 1. AUTOMATION POINTS
- **Входящий поток**: 80% screening automated (AI)
- **Review процесс**: 60% scoring automated + human final decision
- **Disbursement**: 90% automated via smart contracts
- **Monitoring**: 70% real-time automated alerts

### 2. BLOCKCHAIN TOUCHPOINTS
- Grant Registration (immutable record)
- Milestone-based disbursement (smart contracts)
- Evidence logging (IPFS + blockchain)
- Proof-of-Impact NFT (final validation)

### 3. DATA FLOWS
- **Real-time**: DHIS2 → Gap Detection (streaming)
- **Batch**: Reports → Impact Validation (daily)
- **Event-driven**: Milestone completion → Disbursement trigger
- **On-demand**: User queries → Analytics engine

### 4. CRITICAL PATHS
- **Fast-track**: 30-45 days (emergency funding)
- **Standard**: 6-9 months (regular grants)
- **Proof-of-Impact**: 2-4 weeks post-implementation

### 5. SCALING CONSIDERATIONS
- Kubernetes auto-scaling на базе load
- AI/ML services требуют GPU pods (costly)
- Blockchain nodes можно использовать managed services
- Data storage растёт линейно с grant volume

