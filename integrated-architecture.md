# INTEGRATED ARCHITECTURE: QF + Emergency Grants + DID/Reputation

## SYSTEM OVERVIEW

Интеграция трех систем для создания complete ecosystem:

```mermaid
graph TB
    subgraph "LAYER 1: IDENTITY & REPUTATION (PGPX)"
        DID[Decentralized Identity<br/>DID Standard]
        SBT[Soulbound Tokens<br/>Non-transferable Credentials]
        REP[Reputation System<br/>On-chain History]
    end

    subgraph "LAYER 2: FUNDING MECHANISM"
        QF[Quadratic Funding<br/>Community Fundraising]
        Match[Matching Pool<br/>Distribution]
        Verify[Multi-layer<br/>Verification]
    end

    subgraph "LAYER 3: GRANT MANAGEMENT"
        Award[Grant Award<br/>Smart Contracts]
        Milestone[Milestone-based<br/>Disbursement]
        Monitor[AI + DHIS2<br/>Monitoring]
        Impact[Impact Validation<br/>Multi-layer]
    end

    subgraph "LAYER 4: PROOF & REWARDS"
        NFT[Proof-of-Impact<br/>NFT]
        Badge[Achievement<br/>Badges SBT]
        Trust[Trust Score<br/>Update]
    end

    DID --> QF
    SBT --> Verify
    REP --> Match

    QF --> Award
    Match --> Award
    Verify --> Award

    Award --> Milestone
    Milestone --> Monitor
    Monitor --> Impact

    Impact --> NFT
    Impact --> Badge
    Impact --> Trust

    Trust -.-> REP
    Badge -.-> SBT

    style DID fill:#9c27b0
    style SBT fill:#9c27b0
    style REP fill:#9c27b0

    style QF fill:#2196f3
    style Match fill:#2196f3

    style Award fill:#4caf50
    style Milestone fill:#4caf50
    style Monitor fill:#4caf50

    style NFT fill:#ff9800
    style Badge fill:#ff9800
    style Trust fill:#ff9800
```

---

## DETAILED INTEGRATION POINTS

### 1. IDENTITY LAYER (DID + SBT)

**Problem Solved:**
- Sybil resistance в QF
- Stakeholder verification (healthcare workers, communities, etc.)
- Persistent reputation across rounds
- Credential verification for organizations

**Implementation:**

```mermaid
graph LR
    subgraph "USER ONBOARDING"
        Register[User Registration]
        Create[Create DID]
        Issue[Issue SBT<br/>Credentials]
    end

    subgraph "CREDENTIAL TYPES"
        C1[Healthcare Worker<br/>SBT]
        C2[Affected Population<br/>SBT]
        C3[Health Authority<br/>SBT]
        C4[Technical Expert<br/>SBT]
        C5[Organization<br/>Verification SBT]
    end

    subgraph "VERIFICATION SOURCES"
        V1[Professional<br/>Registry]
        V2[Government<br/>Database]
        V3[NGO<br/>Verification]
        V4[Community<br/>Attestation]
    end

    Register --> Create
    Create --> Issue

    Issue --> C1 & C2 & C3 & C4 & C5

    V1 --> C1
    V2 --> C2 & C3
    V3 --> C5
    V4 --> C2

    C1 & C2 & C3 & C4 --> Weight[Stakeholder<br/>Weighting in QF]
    C5 --> Eligibility[Project<br/>Eligibility]

    style Create fill:#9c27b0
    style Issue fill:#9c27b0
    style Weight fill:#2196f3
```

**Key Innovations:**
- **Healthcare Worker SBT**: Verified через professional registries → 1.3x QF multiplier
- **Affected Population SBT**: Verified через geographic + community attestation → 1.25x multiplier
- **Organization SBT**: Track record, financial capacity, compliance → Project eligibility
- **Non-transferable**: Cannot game system by selling credentials

---

### 2. REPUTATION SYSTEM

**On-chain Reputation Score Components:**

```python
def calculate_reputation_score(user_did):
    """
    Multi-dimensional reputation score для QF participants
    """
    score_components = {
        'participation_history': {
            'qf_rounds_participated': weight_0_20,
            'grants_completed': weight_0_30,
            'consistency': weight_0_10  # Regular participation
        },

        'impact_delivered': {
            'poi_nfts_earned': weight_0_25,
            'validation_scores': weight_0_30,
            'beneficiaries_reached': weight_0_20
        },

        'community_trust': {
            'peer_attestations': weight_0_15,
            'dispute_history': weight_minus_50,  # Negative if disputes
            'transparency_score': weight_0_20
        },

        'financial_integrity': {
            'on_time_reporting': weight_0_25,
            'budget_adherence': weight_0_20,
            'fraud_flags': weight_minus_100  # Major penalty
        },

        'collaboration': {
            'gov_partnerships': weight_0_15,
            'knowledge_sharing': weight_0_10,
            'mentorship': weight_0_10
        }
    }

    # Weighted calculation
    total_score = calculate_weighted_sum(score_components)

    # Normalize to 0-100
    reputation_score = normalize(total_score, 0, 100)

    return {
        'overall_score': reputation_score,
        'breakdown': score_components,
        'tier': get_reputation_tier(reputation_score),
        'multiplier': get_qf_multiplier(reputation_score)
    }


def get_reputation_tier(score):
    """
    Reputation tiers with benefits
    """
    if score >= 90:
        return {
            'tier': 'Platinum',
            'qf_multiplier': 1.5,
            'fast_track_eligibility': True,
            'max_grant_size': 'unlimited',
            'auto_approve_milestones': True
        }
    elif score >= 75:
        return {
            'tier': 'Gold',
            'qf_multiplier': 1.3,
            'fast_track_eligibility': True,
            'max_grant_size': 500000,
            'auto_approve_milestones': False
        }
    elif score >= 60:
        return {
            'tier': 'Silver',
            'qf_multiplier': 1.15,
            'fast_track_eligibility': False,
            'max_grant_size': 200000,
            'auto_approve_milestones': False
        }
    else:
        return {
            'tier': 'Bronze',
            'qf_multiplier': 1.0,
            'fast_track_eligibility': False,
            'max_grant_size': 50000,
            'auto_approve_milestones': False
        }
```

**Reputation Flow:**

```mermaid
graph TB
    Start([New Organization]) --> Initial[Initial Score: 50<br/>Bronze Tier]

    Initial --> FirstGrant[Apply to QF Round]

    FirstGrant --> Community[Community Validation<br/>+5 score]

    Community --> Funding{Receive<br/>Funding?}

    Funding -->|Yes| Implement[Implementation<br/>Phase]
    Funding -->|No| Feedback[Improve + Reapply<br/>No score change]

    Feedback -.-> FirstGrant

    Implement --> M1[Milestone 1 Complete<br/>+3 score]
    M1 --> M2[Milestone 2 Complete<br/>+3 score]
    M2 --> M3[Milestone 3 Complete<br/>+3 score]
    M3 --> Final[Impact Validation]

    Final --> Excellent{Score?}

    Excellent -->|>= 90| Platinum[Platinum Badge SBT<br/>+15 score]
    Excellent -->|>= 75| Gold[Gold Badge SBT<br/>+10 score]
    Excellent -->|>= 60| Silver[Silver Badge SBT<br/>+5 score]
    Excellent -->|< 60| Bronze[Bronze Badge SBT<br/>+2 score]

    Platinum & Gold & Silver & Bronze --> NextRound[Next QF Round<br/>Higher Multiplier]

    NextRound --> Advantage[Tier Advantages:<br/>- Higher matching<br/>- Fast-track<br/>- Auto-approvals]

    style Initial fill:#cd7f32
    style Platinum fill:#e5e4e2
    style Gold fill:#ffd700
    style Silver fill:#c0c0c0
    style Advantage fill:#4caf50
```

---

### 3. ENHANCED QF MECHANISM

**Integration с DID/Reputation:**

```mermaid
graph TB
    subgraph "DONOR SIDE"
        Donor[Donor] --> CheckDID{Has DID?}

        CheckDID -->|No| CreateDID[Create DID<br/>Basic Verification]
        CheckDID -->|Yes| LoadRep[Load Reputation<br/>Score]

        CreateDID --> BasicMulti[1.0x Base<br/>Multiplier]

        LoadRep --> RepCheck{Reputation<br/>Tier?}

        RepCheck -->|Platinum| Multi15[1.5x Multiplier]
        RepCheck -->|Gold| Multi13[1.3x Multiplier]
        RepCheck -->|Silver| Multi115[1.15x Multiplier]
        RepCheck -->|Bronze| Multi10[1.0x Multiplier]

        BasicMulti & Multi15 & Multi13 & Multi115 & Multi10 --> AddStake[Add Stakeholder<br/>Type Multiplier]

        AddStake --> Healthcare{SBT Type?}

        Healthcare -->|Healthcare Worker| Extra13[+0.3x]
        Healthcare -->|Affected Pop| Extra25[+0.25x]
        Healthcare -->|Health Auth| Extra20[+0.2x]
        Healthcare -->|Tech Expert| Extra15[+0.15x]
        Healthcare -->|None| Extra0[+0.0x]

        Extra13 & Extra25 & Extra20 & Extra15 & Extra0 --> FinalMulti[Final QF<br/>Multiplier]
    end

    subgraph "PROJECT SIDE"
        Project[Project] --> OrgDID{Has Org<br/>DID + SBT?}

        OrgDID -->|No| Reject[Rejected:<br/>Must verify]
        OrgDID -->|Yes| OrgRep[Check Org<br/>Reputation]

        OrgRep --> OrgTier{Tier?}

        OrgTier -->|Platinum| FastTrack[Fast-track<br/>Verification]
        OrgTier -->|Gold/Silver| Standard[Standard<br/>Review]
        OrgTier -->|Bronze| Enhanced[Enhanced<br/>Due Diligence]

        FastTrack & Standard & Enhanced --> Eligible[Eligible for<br/>QF Round]
    end

    FinalMulti --> Donate[Donate to<br/>Project]
    Eligible --> Donate

    Donate --> QFCalc[QF Algorithm<br/>with Multipliers]

    QFCalc --> MatchDist[Matching<br/>Distribution]

    style CreateDID fill:#9c27b0
    style LoadRep fill:#9c27b0
    style Multi15 fill:#e5e4e2
    style QFCalc fill:#2196f3
    style MatchDist fill:#4caf50
```

**Modified QF Formula:**

```
Match для проекта P = (Σ(√ci × Mi × Si))²

где:
- ci = contribution i для проекта P
- Mi = Reputation multiplier донора (1.0 - 1.5)
- Si = Stakeholder type multiplier донора (1.0 - 1.3)

Итоговый multiplier: Mi × Si
Max possible: 1.5 × 1.3 = 1.95x
```

---

### 4. GRANT LIFECYCLE WITH REPUTATION UPDATES

```mermaid
sequenceDiagram
    participant Org as Organization<br/>(with DID)
    participant QF as QF Platform
    participant Grant as Grant Mgmt
    participant Monitor as Monitoring
    participant Rep as Reputation<br/>System
    participant SBT as SBT Issuer

    Org->>QF: Apply to QF Round
    QF->>Rep: Check Reputation Score
    Rep-->>QF: Score: 65 (Silver Tier)

    Note over QF: Multiplier = 1.15x

    QF->>Org: Project Listed

    Note over Org,QF: Community Voting Period

    QF->>QF: Calculate QF Matching<br/>with Reputation Multiplier
    QF->>Org: Funding Awarded: $75K

    Org->>Grant: Accept Grant
    Grant->>Rep: +5 points (Grant Awarded)

    Org->>Grant: Complete Milestone 1
    Grant->>Monitor: AI + DHIS2 Validation
    Monitor-->>Grant: Validated
    Grant->>Org: Disburse 30%
    Grant->>Rep: +3 points (Milestone 1)

    Note over Rep: Score now 73 (approaching Gold)

    Org->>Grant: Complete Milestone 2
    Grant->>Monitor: Validation
    Monitor-->>Grant: Validated
    Grant->>Org: Disburse 30%
    Grant->>Rep: +3 points (Milestone 2)

    Org->>Grant: Complete Final Milestone
    Grant->>Monitor: Impact Validation
    Monitor-->>Grant: Score: 92/100

    Grant->>SBT: Mint Proof-of-Impact NFT
    SBT-->>Org: NFT Issued

    Grant->>SBT: Mint Platinum Badge SBT
    SBT-->>Org: Badge Issued

    Grant->>Rep: +15 points (Platinum Impact)
    Rep-->>Org: New Score: 91 (Platinum Tier!)

    Note over Org: Next round: 1.5x multiplier<br/>Fast-track eligibility<br/>Auto-approvals
```

---

### 5. PROOF-OF-IMPACT NFT + REPUTATION

**Enhanced NFT Metadata:**

```json
{
  "type": "ProofOfImpact",
  "standard": "ERC-721",
  "grantInfo": {
    "grantId": "QF-R1-2024-12345",
    "projectName": "Community Health Worker Training",
    "organization": {
      "did": "did:near:health-ngo-kenya.near",
      "name": "LocalHealthNGO",
      "reputationScore": 91,
      "tier": "Platinum"
    },
    "funding": {
      "crowdfunding": 15000,
      "qfMatching": 45000,
      "matchingMultiplier": 1.15,
      "totalAmount": 60000
    }
  },
  "impact": {
    "beneficiaries": {
      "direct": 250,
      "indirect": 1500
    },
    "outcomes": {
      "healthWorkersTrained": 50,
      "communitiesReached": 15,
      "servicesImproved": ["antenatal", "immunization", "malaria"],
      "dhis2Integration": true,
      "governmentPartnership": true
    },
    "validationScore": 92,
    "validationLayers": {
      "automated": "passed",
      "ai": "passed",
      "blockchain": "passed",
      "expert": "passed"
    }
  },
  "reputation": {
    "previousScore": 73,
    "scoreGain": 18,
    "newScore": 91,
    "tierUpgrade": "Silver → Platinum",
    "achievements": [
      "First Platinum Impact",
      "Government Partnership",
      "100% Milestone Success",
      "DHIS2 Integration Complete"
    ]
  },
  "badges": [
    {
      "type": "SoulboundToken",
      "name": "Platinum Impact Badge",
      "issuer": "Health QF Platform",
      "permanent": true,
      "benefits": [
        "1.5x QF multiplier next round",
        "Fast-track verification",
        "Auto-approval milestones <$50K",
        "Mentor program access"
      ]
    }
  ],
  "evidenceHash": "QmX...",
  "mintDate": "2024-12-01",
  "validators": ["0xABC...", "0xDEF...", "0x123..."]
}
```

---

### 6. DONOR INCENTIVES + REPUTATION

**GIVbacks-style System с Reputation:**

```mermaid
graph TB
    Donate[Donor Donates<br/>$100 to Project] --> Track[Track Donation<br/>On-chain]

    Track --> Check{Donor Has<br/>DID?}

    Check -->|No| Basic[Basic Reward:<br/>100 tokens]
    Check -->|Yes| Tier{Donor<br/>Tier?}

    Tier -->|Platinum| Reward150[150 tokens<br/>+ NFT badge]
    Tier -->|Gold| Reward130[130 tokens]
    Tier -->|Silver| Reward115[115 tokens]
    Tier -->|Bronze| Reward100[100 tokens]

    Basic & Reward150 & Reward130 & Reward115 & Reward100 --> Outcome{Project<br/>Succeeds?}

    Outcome -->|Yes<br/>High Impact| Bonus[Bonus Tokens<br/>+ Rep Points]
    Outcome -->|Yes<br/>Medium Impact| Small[Small Bonus]
    Outcome -->|No<br/>Failed| NoBonus[No Bonus]

    Bonus --> RepUp[Donor Rep +5]
    Small --> RepUp2[Donor Rep +2]

    RepUp & RepUp2 --> NextDonation[Next Donation:<br/>Higher Multiplier]

    style Donate fill:#2196f3
    style Reward150 fill:#e5e4e2
    style Bonus fill:#4caf50
    style RepUp fill:#9c27b0
```

**Donor Reputation Benefits:**
- Platinum donors: 1.5x matching power в QF
- Early access to новым QF rounds
- Exclusive impact reports
- NFT donor badges
- Community governance права

---

### 7. ANTI-SYBIL ENHANCED

**Multi-layer Sybil Detection:**

```mermaid
graph TB
    subgraph "TRADITIONAL CHECKS"
        T1[Email/Phone<br/>Verification]
        T2[Address Clustering]
        T3[Pairwise Analysis]
    end

    subgraph "DID-BASED CHECKS"
        D1[DID Uniqueness]
        D2[Credential Verification]
        D3[SBT Possession]
        D4[Reputation History]
    end

    subgraph "BEHAVIORAL ANALYSIS"
        B1[Donation Patterns]
        B2[Time-series Analysis]
        B3[Social Graph]
        B4[Device Fingerprinting]
    end

    subgraph "AI DETECTION"
        AI1[Anomaly Detection<br/>ML]
        AI2[Clustering<br/>Algorithms]
        AI3[Natural Behavior<br/>Classification]
    end

    T1 & T2 & T3 --> Score1[Traditional<br/>Score]
    D1 & D2 & D3 & D4 --> Score2[DID<br/>Score]
    B1 & B2 & B3 & B4 --> Score3[Behavioral<br/>Score]

    Score1 & Score2 & Score3 --> AI1 & AI2 & AI3

    AI1 & AI2 & AI3 --> Final{Final<br/>Assessment}

    Final -->|High Confidence<br/>Genuine| Accept[Accept Donation<br/>Full Multiplier]
    Final -->|Medium<br/>Uncertain| Review[Human Review<br/>Reduced Multiplier]
    Final -->|High Risk<br/>Sybil| Reject[Reject<br/>+ Flag Account]

    style D1 fill:#9c27b0
    style D2 fill:#9c27b0
    style D3 fill:#9c27b0
    style D4 fill:#9c27b0
    style AI1 fill:#fff9c4
    style AI2 fill:#fff9c4
    style AI3 fill:#fff9c4
    style Accept fill:#4caf50
    style Reject fill:#ff5722
```

**DID-based Advantages:**
- **Unique DID required**: Can't create 100 accounts
- **SBT verification**: Must prove real identity
- **Reputation stake**: Sybil attack risks reputation
- **Cross-round tracking**: Can't reset identity
- **Social graph**: Real networks look different

---

## KEY INNOVATIONS

### 1. Persistent Identity Across Funding Cycles
- Traditional: New application каждый раз
- **Our system**: DID + reputation carries over
- Platinum organizations fast-tracked
- Trust builds over time

### 2. Stakeholder-Weighted QF
- Traditional QF: 1 dollar = 1 sqrt vote
- **Our system**: Multipliers based on verified stakeholder type
- Healthcare workers get higher weight
- Affected populations prioritized

### 3. Reputation-Based Benefits
- Traditional: Same rules for everyone
- **Our system**:
  - Platinum tier → auto-approvals
  - Gold tier → fast-track verification
  - Bronze tier → enhanced due diligence

### 4. Composable Credentials
- **SBT Stack**:
  - Organization verification SBT
  - Platinum impact badge SBT
  - Healthcare worker credential SBT
  - Community attestation SBT
  - All non-transferable, verifiable

### 5. Impact → Reputation Loop
- Good impact → Higher reputation
- Higher reputation → Better funding terms
- Better funding → More capacity
- More capacity → Bigger impact
- **Virtuous cycle**

---

## TECHNICAL ARCHITECTURE

### Smart Contract Stack:

```
LAYER 1: Identity & Credentials
├── DIDRegistry.sol
├── SBTIssuer.sol
├── CredentialVerifier.sol
└── ReputationOracle.sol

LAYER 2: QF Mechanism
├── QFMatchingPool.sol
├── DonationRouter.sol (with DID verification)
├── MultiStakeholderQF.sol (weighted calculation)
└── AntiSybil.sol

LAYER 3: Grant Management
├── GrantAgreement.sol
├── MilestoneManager.sol
├── EvidenceStorage.sol (IPFS)
└── DisbursementEngine.sol

LAYER 4: Proof & Rewards
├── ProofOfImpactNFT.sol
├── BadgeIssuer.sol (SBTs)
├── ReputationUpdater.sol
└── DonorRewards.sol
```

### Integration Points:

```mermaid
graph LR
    subgraph "EXISTING SYSTEMS"
        DHIS2[DHIS2<br/>Health Data]
        Gov[Government<br/>Systems]
        Bank[Banking<br/>Rails]
    end

    subgraph "NEW COMPONENTS"
        DID[DID<br/>Registry]
        SBT[SBT<br/>Issuer]
        Rep[Reputation<br/>Oracle]
    end

    subgraph "QF + GRANT PLATFORM"
        QF[QF Engine]
        Grant[Grant Mgmt]
        Monitor[Monitoring]
    end

    DID --> QF
    SBT --> QF
    Rep --> QF

    QF --> Grant
    Grant --> Monitor

    DHIS2 --> Monitor
    Gov --> SBT
    Bank --> Grant

    Monitor --> Rep
    Grant --> SBT

    style DID fill:#9c27b0
    style SBT fill:#9c27b0
    style Rep fill:#9c27b0
    style QF fill:#2196f3
    style Grant fill:#4caf50
```

---

## ROADMAP INTEGRATION

### Phase 1: MVP (Current Emergency Grant + Basic QF)
- ✅ Emergency grant architecture (existing)
- ✅ QF concept design (new doc)
- 🔨 Basic DID integration (next)

### Phase 2: DID + Reputation Launch
- DID registry deployment
- Basic SBT issuance (org verification)
- Reputation system v1
- Enhanced QF with multipliers

### Phase 3: Full Stack
- Complete SBT credential types
- Advanced reputation tiers
- Auto-approval systems
- Cross-platform identity

### Phase 4: Ecosystem
- Open DID/SBT standards
- Multi-platform reputation
- Industry adoption
- Government integration

---

## NEXT STEPS

### Immediate:
1. **Copy QF concept** → emergency-grant-docs
2. **Create integration diagrams** (this document)
3. **Design DID/SBT schema** for health sector
4. **Prototype reputation algorithm**

### Short-term:
1. Deploy test DID registry
2. Issue test SBTs
3. Integrate with QF prototype
4. Run pilot with 10 organizations

### Medium-term:
1. Full reputation system
2. Multi-tier benefits
3. Government credential integration
4. Scale to production

Что думаешь? Нужно ли добавить эту интеграцию в основную документацию?
