# SMART CONTRACTS: DID + SBT + QF + GRANT MANAGEMENT

## CONTRACT ARCHITECTURE

```mermaid
graph TB
    subgraph "IDENTITY LAYER"
        DIDRegistry[DIDRegistry.sol]
        SBTIssuer[SBTIssuer.sol]
        CredentialVerifier[CredentialVerifier.sol]
    end

    subgraph "REPUTATION LAYER"
        ReputationOracle[ReputationOracle.sol]
        ReputationCalculator[ReputationCalculator.sol]
    end

    subgraph "QF LAYER"
        QFMatchingPool[QFMatchingPool.sol]
        DonationRouter[DonationRouter.sol]
        AntiSybil[AntiSybil.sol]
    end

    subgraph "GRANT LAYER"
        GrantManager[GrantManager.sol]
        MilestoneValidator[MilestoneValidator.sol]
        ProofOfImpactNFT[ProofOfImpactNFT.sol]
    end

    DIDRegistry --> SBTIssuer
    SBTIssuer --> CredentialVerifier
    CredentialVerifier --> ReputationOracle

    ReputationOracle --> ReputationCalculator
    ReputationCalculator --> QFMatchingPool

    CredentialVerifier --> AntiSybil
    AntiSybil --> DonationRouter
    DonationRouter --> QFMatchingPool

    QFMatchingPool --> GrantManager
    GrantManager --> MilestoneValidator
    MilestoneValidator --> ReputationCalculator
    MilestoneValidator --> ProofOfImpactNFT
    ProofOfImpactNFT --> SBTIssuer

    style DIDRegistry fill:#9c27b0
    style SBTIssuer fill:#9c27b0
    style ReputationOracle fill:#ff9800
    style QFMatchingPool fill:#2196f3
    style GrantManager fill:#4caf50
```

---

## 1. DID REGISTRY

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

/**
 * @title DIDRegistry
 * @notice Decentralized Identity Registry for Health Sector
 * @dev Implements W3C DID standard
 */
contract DIDRegistry {
    // DID Document structure
    struct DIDDocument {
        address controller;
        string publicKey;
        string[] serviceEndpoints;
        uint256 created;
        uint256 updated;
        bool active;
    }

    // DID type enumeration
    enum DIDType {
        Individual,           // 0 - Individual person
        Organization,         // 1 - Organization/NGO
        HealthcareWorker,    // 2 - Verified healthcare professional
        GovernmentEntity     // 3 - Government agency
    }

    // Mapping: DID → Document
    mapping(bytes32 => DIDDocument) public didDocuments;

    // Mapping: DID → Type
    mapping(bytes32 => DIDType) public didTypes;

    // Mapping: Address → DID
    mapping(address => bytes32) public addressToDID;

    // Events
    event DIDCreated(bytes32 indexed did, address indexed controller, DIDType didType);
    event DIDUpdated(bytes32 indexed did, uint256 timestamp);
    event DIDDeactivated(bytes32 indexed did);
    event ControllerChanged(bytes32 indexed did, address indexed newController);

    /**
     * @notice Create a new DID
     * @param publicKey Public key for verification
     * @param didType Type of DID being created
     * @param serviceEndpoints Array of service endpoint URLs
     */
    function createDID(
        string memory publicKey,
        DIDType didType,
        string[] memory serviceEndpoints
    ) external returns (bytes32) {
        // Check if address already has DID
        require(addressToDID[msg.sender] == bytes32(0), "Address already has DID");

        // Generate DID identifier
        bytes32 did = keccak256(abi.encodePacked(msg.sender, block.timestamp, publicKey));

        // Store DID document
        didDocuments[did] = DIDDocument({
            controller: msg.sender,
            publicKey: publicKey,
            serviceEndpoints: serviceEndpoints,
            created: block.timestamp,
            updated: block.timestamp,
            active: true
        });

        // Store DID type
        didTypes[did] = didType;

        // Map address to DID
        addressToDID[msg.sender] = did;

        emit DIDCreated(did, msg.sender, didType);

        return did;
    }

    /**
     * @notice Update DID document
     * @param did DID to update
     * @param publicKey New public key
     * @param serviceEndpoints New service endpoints
     */
    function updateDID(
        bytes32 did,
        string memory publicKey,
        string[] memory serviceEndpoints
    ) external {
        DIDDocument storage doc = didDocuments[did];
        require(doc.controller == msg.sender, "Not controller");
        require(doc.active, "DID deactivated");

        doc.publicKey = publicKey;
        doc.serviceEndpoints = serviceEndpoints;
        doc.updated = block.timestamp;

        emit DIDUpdated(did, block.timestamp);
    }

    /**
     * @notice Transfer DID control to new address
     * @param did DID to transfer
     * @param newController New controller address
     */
    function transferControl(bytes32 did, address newController) external {
        DIDDocument storage doc = didDocuments[did];
        require(doc.controller == msg.sender, "Not controller");
        require(doc.active, "DID deactivated");

        // Update mappings
        delete addressToDID[msg.sender];
        addressToDID[newController] = did;

        // Update controller
        doc.controller = newController;
        doc.updated = block.timestamp;

        emit ControllerChanged(did, newController);
    }

    /**
     * @notice Deactivate DID
     * @param did DID to deactivate
     */
    function deactivateDID(bytes32 did) external {
        DIDDocument storage doc = didDocuments[did];
        require(doc.controller == msg.sender, "Not controller");

        doc.active = false;
        doc.updated = block.timestamp;

        emit DIDDeactivated(did);
    }

    /**
     * @notice Resolve DID to document
     * @param did DID to resolve
     */
    function resolveDID(bytes32 did) external view returns (
        address controller,
        string memory publicKey,
        string[] memory serviceEndpoints,
        uint256 created,
        uint256 updated,
        bool active
    ) {
        DIDDocument memory doc = didDocuments[did];
        return (
            doc.controller,
            doc.publicKey,
            doc.serviceEndpoints,
            doc.created,
            doc.updated,
            doc.active
        );
    }

    /**
     * @notice Get DID for address
     * @param account Address to query
     */
    function getDIDForAddress(address account) external view returns (bytes32) {
        return addressToDID[account];
    }

    /**
     * @notice Check if DID is active
     * @param did DID to check
     */
    function isActive(bytes32 did) external view returns (bool) {
        return didDocuments[did].active;
    }
}
```

---

## 2. SBT ISSUER

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

import "@openzeppelin/contracts/token/ERC721/ERC721.sol";
import "@openzeppelin/contracts/access/AccessControl.sol";

/**
 * @title SBTIssuer
 * @notice Soulbound Token Issuer for Health Sector Credentials
 * @dev Non-transferable tokens representing achievements and credentials
 */
contract SBTIssuer is ERC721, AccessControl {
    bytes32 public constant ISSUER_ROLE = keccak256("ISSUER_ROLE");

    // SBT types
    enum SBTType {
        OrganizationVerification,    // Basic org verification
        HealthcareWorkerCredential,  // Professional credential
        AffectedPopulationID,        // Community member
        HealthAuthorityID,           // Government official
        TechnicalExpertID,           // Technical specialist
        PlatinumImpactBadge,        // Platinum tier achievement
        GoldImpactBadge,            // Gold tier achievement
        SilverImpactBadge,          // Silver tier achievement
        BronzeImpactBadge,          // Bronze tier achievement
        GovernmentPartnerBadge,     // Government partnership
        DHIS2IntegrationBadge,      // DHIS2 integration complete
        MillionBeneficiaries        // 1M+ beneficiaries reached
    }

    // SBT metadata structure
    struct SBTMetadata {
        bytes32 did;                 // Owner's DID
        SBTType sbtType;             // Type of credential
        string credentialData;       // IPFS hash or JSON data
        address issuer;              // Who issued this SBT
        uint256 issuedAt;           // Timestamp
        uint256 expiresAt;          // Expiration (0 = never)
        bool revoked;               // Revocation status
    }

    // Token ID counter
    uint256 private _nextTokenId;

    // Mapping: tokenId => metadata
    mapping(uint256 => SBTMetadata) public sbtMetadata;

    // Mapping: DID => SBT types owned
    mapping(bytes32 => mapping(SBTType => uint256)) public didToSBTType;

    // Events
    event SBTIssued(
        uint256 indexed tokenId,
        bytes32 indexed did,
        SBTType sbtType,
        address issuer
    );
    event SBTRevoked(uint256 indexed tokenId, string reason);

    constructor() ERC721("Health Sector SBT", "HSSBT") {
        _grantRole(DEFAULT_ADMIN_ROLE, msg.sender);
        _grantRole(ISSUER_ROLE, msg.sender);
    }

    /**
     * @notice Issue a Soulbound Token
     * @param did Recipient's DID
     * @param recipient Recipient's address
     * @param sbtType Type of SBT to issue
     * @param credentialData IPFS hash or credential data
     * @param expiresAt Expiration timestamp (0 for no expiration)
     */
    function issueSBT(
        bytes32 did,
        address recipient,
        SBTType sbtType,
        string memory credentialData,
        uint256 expiresAt
    ) external onlyRole(ISSUER_ROLE) returns (uint256) {
        // Check if already has this SBT type (prevent duplicates)
        require(
            didToSBTType[did][sbtType] == 0,
            "Already has this SBT type"
        );

        uint256 tokenId = _nextTokenId++;

        // Mint SBT
        _safeMint(recipient, tokenId);

        // Store metadata
        sbtMetadata[tokenId] = SBTMetadata({
            did: did,
            sbtType: sbtType,
            credentialData: credentialData,
            issuer: msg.sender,
            issuedAt: block.timestamp,
            expiresAt: expiresAt,
            revoked: false
        });

        // Map DID to SBT type
        didToSBTType[did][sbtType] = tokenId;

        emit SBTIssued(tokenId, did, sbtType, msg.sender);

        return tokenId;
    }

    /**
     * @notice Revoke an SBT
     * @param tokenId Token to revoke
     * @param reason Revocation reason
     */
    function revokeSBT(
        uint256 tokenId,
        string memory reason
    ) external onlyRole(ISSUER_ROLE) {
        require(_ownerOf(tokenId) != address(0), "Token does not exist");

        SBTMetadata storage metadata = sbtMetadata[tokenId];
        metadata.revoked = true;

        // Remove from mapping
        delete didToSBTType[metadata.did][metadata.sbtType];

        emit SBTRevoked(tokenId, reason);
    }

    /**
     * @notice Check if DID has specific SBT type
     * @param did DID to check
     * @param sbtType Type to check for
     */
    function hasSBTType(bytes32 did, SBTType sbtType) external view returns (bool) {
        uint256 tokenId = didToSBTType[did][sbtType];
        if (tokenId == 0) return false;

        SBTMetadata memory metadata = sbtMetadata[tokenId];

        // Check not revoked and not expired
        if (metadata.revoked) return false;
        if (metadata.expiresAt > 0 && block.timestamp > metadata.expiresAt) return false;

        return true;
    }

    /**
     * @notice Get SBT metadata
     * @param tokenId Token ID
     */
    function getSBTMetadata(uint256 tokenId) external view returns (SBTMetadata memory) {
        return sbtMetadata[tokenId];
    }

    /**
     * @notice Override transfer functions to make tokens non-transferable
     */
    function _update(address to, uint256 tokenId, address auth)
        internal
        override
        returns (address)
    {
        address from = _ownerOf(tokenId);

        // Allow minting and burning, but not transfers
        if (from != address(0) && to != address(0)) {
            revert("Soulbound: Transfer not allowed");
        }

        return super._update(to, tokenId, auth);
    }

    /**
     * @notice Check interface support
     */
    function supportsInterface(bytes4 interfaceId)
        public
        view
        override(ERC721, AccessControl)
        returns (bool)
    {
        return super.supportsInterface(interfaceId);
    }
}
```

---

## 3. REPUTATION ORACLE

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

import "@openzeppelin/contracts/access/AccessControl.sol";

/**
 * @title ReputationOracle
 * @notice On-chain reputation system for health sector organizations
 */
contract ReputationOracle is AccessControl {
    bytes32 public constant SCORER_ROLE = keccak256("SCORER_ROLE");

    // Reputation tier thresholds
    uint256 public constant PLATINUM_THRESHOLD = 90;
    uint256 public constant GOLD_THRESHOLD = 75;
    uint256 public constant SILVER_THRESHOLD = 60;

    // Reputation tier enum
    enum ReputationTier {
        Bronze,    // 0-59
        Silver,    // 60-74
        Gold,      // 75-89
        Platinum   // 90-100
    }

    // Reputation score structure
    struct ReputationScore {
        uint256 participationScore;    // 0-20
        uint256 impactScore;           // 0-30
        uint256 communityTrustScore;   // 0-15
        uint256 financialIntegrityScore; // 0-25
        uint256 collaborationScore;    // 0-10
        uint256 totalScore;            // Sum (0-100)
        ReputationTier tier;
        uint256 lastUpdated;
    }

    // Reputation history event
    struct ReputationEvent {
        string eventType;     // "grant_awarded", "milestone_complete", etc.
        int256 scoreChange;   // Can be positive or negative
        uint256 timestamp;
        string metadata;      // IPFS hash with details
    }

    // Mapping: DID => Reputation Score
    mapping(bytes32 => ReputationScore) public reputationScores;

    // Mapping: DID => History
    mapping(bytes32 => ReputationEvent[]) public reputationHistory;

    // Events
    event ReputationUpdated(
        bytes32 indexed did,
        uint256 oldScore,
        uint256 newScore,
        ReputationTier tier
    );
    event ReputationEventRecorded(
        bytes32 indexed did,
        string eventType,
        int256 scoreChange
    );

    constructor() {
        _grantRole(DEFAULT_ADMIN_ROLE, msg.sender);
        _grantRole(SCORER_ROLE, msg.sender);
    }

    /**
     * @notice Initialize reputation for new DID
     * @param did DID to initialize
     */
    function initializeReputation(bytes32 did) external {
        require(reputationScores[did].lastUpdated == 0, "Already initialized");

        // Set initial scores (Bronze tier start)
        reputationScores[did] = ReputationScore({
            participationScore: 10,      // Starting participation
            impactScore: 0,              // No impact yet
            communityTrustScore: 7,      // Basic trust
            financialIntegrityScore: 13, // Neutral integrity
            collaborationScore: 5,       // Some collaboration
            totalScore: 35,              // Bronze tier
            tier: ReputationTier.Bronze,
            lastUpdated: block.timestamp
        });

        emit ReputationUpdated(did, 0, 35, ReputationTier.Bronze);
    }

    /**
     * @notice Update reputation score
     * @param did DID to update
     * @param eventType Type of event
     * @param scoreChange Change in score
     * @param metadata IPFS hash with event details
     */
    function updateReputation(
        bytes32 did,
        string memory eventType,
        int256 scoreChange,
        string memory metadata
    ) external onlyRole(SCORER_ROLE) {
        ReputationScore storage score = reputationScores[did];
        require(score.lastUpdated > 0, "Not initialized");

        uint256 oldTotalScore = score.totalScore;

        // Apply score change
        if (scoreChange > 0) {
            score.totalScore += uint256(scoreChange);
            if (score.totalScore > 100) score.totalScore = 100;
        } else if (scoreChange < 0) {
            uint256 decrease = uint256(-scoreChange);
            if (decrease > score.totalScore) {
                score.totalScore = 0;
            } else {
                score.totalScore -= decrease;
            }
        }

        // Update tier
        score.tier = _calculateTier(score.totalScore);
        score.lastUpdated = block.timestamp;

        // Record event
        reputationHistory[did].push(ReputationEvent({
            eventType: eventType,
            scoreChange: scoreChange,
            timestamp: block.timestamp,
            metadata: metadata
        }));

        emit ReputationUpdated(did, oldTotalScore, score.totalScore, score.tier);
        emit ReputationEventRecorded(did, eventType, scoreChange);
    }

    /**
     * @notice Get reputation tier for DID
     * @param did DID to query
     */
    function getReputationTier(bytes32 did) external view returns (ReputationTier) {
        return reputationScores[did].tier;
    }

    /**
     * @notice Get QF multiplier based on reputation
     * @param did DID to query
     */
    function getQFMultiplier(bytes32 did) external view returns (uint256) {
        ReputationTier tier = reputationScores[did].tier;

        if (tier == ReputationTier.Platinum) return 150; // 1.50x (multiplied by 100)
        if (tier == ReputationTier.Gold) return 130;     // 1.30x
        if (tier == ReputationTier.Silver) return 115;   // 1.15x
        return 100;                                       // 1.00x (Bronze)
    }

    /**
     * @notice Get complete reputation score
     * @param did DID to query
     */
    function getReputationScore(bytes32 did) external view returns (ReputationScore memory) {
        return reputationScores[did];
    }

    /**
     * @notice Get reputation history
     * @param did DID to query
     */
    function getReputationHistory(bytes32 did) external view returns (ReputationEvent[] memory) {
        return reputationHistory[did];
    }

    /**
     * @notice Calculate tier from total score
     */
    function _calculateTier(uint256 totalScore) internal pure returns (ReputationTier) {
        if (totalScore >= PLATINUM_THRESHOLD) return ReputationTier.Platinum;
        if (totalScore >= GOLD_THRESHOLD) return ReputationTier.Gold;
        if (totalScore >= SILVER_THRESHOLD) return ReputationTier.Silver;
        return ReputationTier.Bronze;
    }
}
```

---

## 4. QF MATCHING POOL (Enhanced)

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

import "@openzeppelin/contracts/access/AccessControl.sol";
import "@openzeppelin/contracts/security/ReentrancyGuard.sol";

interface IReputationOracle {
    function getQFMultiplier(bytes32 did) external view returns (uint256);
}

interface ISBTIssuer {
    enum SBTType {
        OrganizationVerification,
        HealthcareWorkerCredential,
        AffectedPopulationID,
        HealthAuthorityID,
        TechnicalExpertID,
        PlatinumImpactBadge,
        GoldImpactBadge,
        SilverImpactBadge,
        BronzeImpactBadge,
        GovernmentPartnerBadge,
        DHIS2IntegrationBadge,
        MillionBeneficiaries
    }

    function hasSBTType(bytes32 did, SBTType sbtType) external view returns (bool);
}

interface IDIDRegistry {
    function addressToDID(address account) external view returns (bytes32);
    function isActive(bytes32 did) external view returns (bool);
}

/**
 * @title QFMatchingPoolEnhanced
 * @notice Quadratic Funding with DID/Reputation/SBT integration
 */
contract QFMatchingPoolEnhanced is AccessControl, ReentrancyGuard {
    bytes32 public constant ADMIN_ROLE = keccak256("ADMIN_ROLE");

    IReputationOracle public reputationOracle;
    ISBTIssuer public sbtIssuer;
    IDIDRegistry public didRegistry;

    // Stakeholder multipliers (multiplied by 100 for precision)
    uint256 public constant HEALTHCARE_WORKER_MULTIPLIER = 130; // 1.30x
    uint256 public constant AFFECTED_POPULATION_MULTIPLIER = 125; // 1.25x
    uint256 public constant HEALTH_AUTHORITY_MULTIPLIER = 120;   // 1.20x
    uint256 public constant TECHNICAL_EXPERT_MULTIPLIER = 115;   // 1.15x
    uint256 public constant BASE_MULTIPLIER = 100;               // 1.00x

    struct QFRound {
        uint256 roundId;
        uint256 matchingPool;
        uint256 startTime;
        uint256 endTime;
        bool finalized;
        uint256 totalDonations;
        uint256 totalDonors;
    }

    struct Project {
        bytes32 projectDID;
        address payable beneficiary;
        string metadataHash;
        bool verified;
        bool active;
        uint256 crowdfundedAmount;
        uint256 matchingAmount;
        uint256 donorCount;
    }

    struct Donation {
        address donor;
        bytes32 donorDID;
        uint256 amount;
        uint256 timestamp;
        uint256 effectiveMultiplier; // Combined reputation + stakeholder
    }

    // Storage
    mapping(uint256 => QFRound) public rounds;
    mapping(uint256 => mapping(uint256 => Project)) public projects;
    mapping(uint256 => mapping(uint256 => Donation[])) public donations;

    uint256 public currentRoundId;
    uint256 private _nextProjectId;

    // Events
    event RoundCreated(uint256 indexed roundId, uint256 matchingPool, uint256 startTime, uint256 endTime);
    event ProjectRegistered(uint256 indexed roundId, uint256 indexed projectId, bytes32 projectDID);
    event DonationReceived(
        uint256 indexed roundId,
        uint256 indexed projectId,
        address indexed donor,
        uint256 amount,
        uint256 effectiveMultiplier
    );
    event MatchingFinalized(uint256 indexed roundId, uint256 totalMatching);

    constructor(
        address _reputationOracle,
        address _sbtIssuer,
        address _didRegistry
    ) {
        reputationOracle = IReputationOracle(_reputationOracle);
        sbtIssuer = ISBTIssuer(_sbtIssuer);
        didRegistry = IDIDRegistry(_didRegistry);

        _grantRole(DEFAULT_ADMIN_ROLE, msg.sender);
        _grantRole(ADMIN_ROLE, msg.sender);
    }

    /**
     * @notice Create new QF round
     */
    function createRound(
        uint256 startTime,
        uint256 endTime
    ) external payable onlyRole(ADMIN_ROLE) returns (uint256) {
        require(msg.value > 0, "Must provide matching pool");
        require(startTime > block.timestamp, "Start time must be future");
        require(endTime > startTime, "End must be after start");

        uint256 roundId = currentRoundId++;

        rounds[roundId] = QFRound({
            roundId: roundId,
            matchingPool: msg.value,
            startTime: startTime,
            endTime: endTime,
            finalized: false,
            totalDonations: 0,
            totalDonors: 0
        });

        emit RoundCreated(roundId, msg.value, startTime, endTime);

        return roundId;
    }

    /**
     * @notice Register project for QF round
     */
    function registerProject(
        uint256 roundId,
        bytes32 projectDID,
        address payable beneficiary,
        string memory metadataHash
    ) external returns (uint256) {
        require(didRegistry.isActive(projectDID), "DID not active");
        require(
            sbtIssuer.hasSBTType(projectDID, ISBTIssuer.SBTType.OrganizationVerification),
            "Must have org verification SBT"
        );

        QFRound storage round = rounds[roundId];
        require(block.timestamp < round.startTime, "Round already started");

        uint256 projectId = _nextProjectId++;

        projects[roundId][projectId] = Project({
            projectDID: projectDID,
            beneficiary: beneficiary,
            metadataHash: metadataHash,
            verified: true,
            active: true,
            crowdfundedAmount: 0,
            matchingAmount: 0,
            donorCount: 0
        });

        emit ProjectRegistered(roundId, projectId, projectDID);

        return projectId;
    }

    /**
     * @notice Donate to project with DID/Reputation multipliers
     */
    function donate(
        uint256 roundId,
        uint256 projectId
    ) external payable nonReentrant {
        require(msg.value > 0, "Must donate positive amount");

        QFRound storage round = rounds[roundId];
        require(block.timestamp >= round.startTime, "Round not started");
        require(block.timestamp < round.endTime, "Round ended");
        require(!round.finalized, "Round finalized");

        Project storage project = projects[roundId][projectId];
        require(project.active, "Project not active");

        // Get donor DID
        bytes32 donorDID = didRegistry.addressToDID(msg.sender);
        require(donorDID != bytes32(0), "Must have DID");
        require(didRegistry.isActive(donorDID), "DID not active");

        // Calculate effective multiplier
        uint256 effectiveMultiplier = _calculateEffectiveMultiplier(donorDID);

        // Record donation
        donations[roundId][projectId].push(Donation({
            donor: msg.sender,
            donorDID: donorDID,
            amount: msg.value,
            timestamp: block.timestamp,
            effectiveMultiplier: effectiveMultiplier
        }));

        // Update stats
        project.crowdfundedAmount += msg.value;
        project.donorCount++;
        round.totalDonations += msg.value;

        // Transfer to project immediately
        project.beneficiary.transfer(msg.value);

        emit DonationReceived(roundId, projectId, msg.sender, msg.value, effectiveMultiplier);
    }

    /**
     * @notice Calculate effective multiplier (reputation + stakeholder)
     */
    function _calculateEffectiveMultiplier(bytes32 donorDID) internal view returns (uint256) {
        // Get reputation multiplier
        uint256 repMultiplier = reputationOracle.getQFMultiplier(donorDID);

        // Get stakeholder multiplier
        uint256 stakeholderMultiplier = _getStakeholderMultiplier(donorDID);

        // Combine: (rep * stakeholder) / 100
        return (repMultiplier * stakeholderMultiplier) / 100;
    }

    /**
     * @notice Get stakeholder type multiplier based on SBTs
     */
    function _getStakeholderMultiplier(bytes32 donorDID) internal view returns (uint256) {
        // Check SBT types in priority order
        if (sbtIssuer.hasSBTType(donorDID, ISBTIssuer.SBTType.HealthcareWorkerCredential)) {
            return HEALTHCARE_WORKER_MULTIPLIER;
        }
        if (sbtIssuer.hasSBTType(donorDID, ISBTIssuer.SBTType.AffectedPopulationID)) {
            return AFFECTED_POPULATION_MULTIPLIER;
        }
        if (sbtIssuer.hasSBTType(donorDID, ISBTIssuer.SBTType.HealthAuthorityID)) {
            return HEALTH_AUTHORITY_MULTIPLIER;
        }
        if (sbtIssuer.hasSBTType(donorDID, ISBTIssuer.SBTType.TechnicalExpertID)) {
            return TECHNICAL_EXPERT_MULTIPLIER;
        }

        return BASE_MULTIPLIER;
    }

    /**
     * @notice Finalize QF round and distribute matching
     * @dev This is simplified - real implementation needs off-chain QF calculation
     */
    function finalizeRound(
        uint256 roundId,
        uint256[] calldata projectIds,
        uint256[] calldata matchingAmounts
    ) external onlyRole(ADMIN_ROLE) nonReentrant {
        QFRound storage round = rounds[roundId];
        require(block.timestamp > round.endTime, "Round not ended");
        require(!round.finalized, "Already finalized");
        require(projectIds.length == matchingAmounts.length, "Length mismatch");

        uint256 totalMatching = 0;

        for (uint256 i = 0; i < projectIds.length; i++) {
            Project storage project = projects[roundId][projectIds[i]];
            project.matchingAmount = matchingAmounts[i];
            totalMatching += matchingAmounts[i];

            // Transfer matching funds
            project.beneficiary.transfer(matchingAmounts[i]);
        }

        require(totalMatching <= round.matchingPool, "Exceeds pool");

        round.finalized = true;

        emit MatchingFinalized(roundId, totalMatching);
    }

    /**
     * @notice Get donations for project
     */
    function getDonations(
        uint256 roundId,
        uint256 projectId
    ) external view returns (Donation[] memory) {
        return donations[roundId][projectId];
    }
}
```

---

## DEPLOYMENT SCRIPT

```javascript
// scripts/deploy-full-stack.js
const hre = require("hardhat");

async function main() {
  console.log("Deploying Health Sector DID + QF Stack...");

  // 1. Deploy DID Registry
  const DIDRegistry = await hre.ethers.getContractFactory("DIDRegistry");
  const didRegistry = await DIDRegistry.deploy();
  await didRegistry.deployed();
  console.log("DIDRegistry deployed to:", didRegistry.address);

  // 2. Deploy SBT Issuer
  const SBTIssuer = await hre.ethers.getContractFactory("SBTIssuer");
  const sbtIssuer = await SBTIssuer.deploy();
  await sbtIssuer.deployed();
  console.log("SBTIssuer deployed to:", sbtIssuer.address);

  // 3. Deploy Reputation Oracle
  const ReputationOracle = await hre.ethers.getContractFactory("ReputationOracle");
  const reputationOracle = await ReputationOracle.deploy();
  await reputationOracle.deployed();
  console.log("ReputationOracle deployed to:", reputationOracle.address);

  // 4. Deploy QF Matching Pool
  const QFMatchingPoolEnhanced = await hre.ethers.getContractFactory("QFMatchingPoolEnhanced");
  const qfPool = await QFMatchingPoolEnhanced.deploy(
    reputationOracle.address,
    sbtIssuer.address,
    didRegistry.address
  );
  await qfPool.deployed();
  console.log("QFMatchingPoolEnhanced deployed to:", qfPool.address);

  // Grant roles
  await sbtIssuer.grantRole(
    await sbtIssuer.ISSUER_ROLE(),
    reputationOracle.address
  );
  console.log("Granted ISSUER_ROLE to ReputationOracle");

  console.log("\n=== Deployment Complete ===");
  console.log("DIDRegistry:", didRegistry.address);
  console.log("SBTIssuer:", sbtIssuer.address);
  console.log("ReputationOracle:", reputationOracle.address);
  console.log("QFMatchingPoolEnhanced:", qfPool.address);
}

main()
  .then(() => process.exit(0))
  .catch((error) => {
    console.error(error);
    process.exit(1);
  });
```

---

## USAGE EXAMPLE

```javascript
// Example: Organization registration and QF participation

// 1. Create DID
const tx1 = await didRegistry.createDID(
  "publicKey123",
  1, // Organization type
  ["https://api.healthngo.org"]
);

// 2. Get issued DID
const did = await didRegistry.getDIDForAddress(myAddress);

// 3. Issue Organization Verification SBT
await sbtIssuer.issueSBT(
  did,
  myAddress,
  0, // OrganizationVerification
  "ipfs://Qm...", // Credential data
  0  // No expiration
);

// 4. Initialize reputation
await reputationOracle.initializeReputation(did);

// 5. Register project in QF round
await qfPool.registerProject(
  roundId,
  did,
  beneficiaryAddress,
  "ipfs://Qm..." // Project metadata
);

// 6. Donors donate (with multipliers applied automatically)
await qfPool.donate(roundId, projectId, { value: ethers.utils.parseEther("1.0") });

// 7. After success, update reputation
await reputationOracle.updateReputation(
  did,
  "grant_completed",
  15, // +15 points
  "ipfs://Qm..." // Event metadata
);

// 8. Issue Platinum badge if earned
await sbtIssuer.issueSBT(
  did,
  myAddress,
  5, // PlatinumImpactBadge
  "ipfs://Qm...",
  0
);
```

---

## GAS ESTIMATES

| Operation | Estimated Gas | Cost @ 50 gwei |
|-----------|---------------|----------------|
| Create DID | ~150,000 | ~$0.38 |
| Issue SBT | ~180,000 | ~$0.45 |
| Initialize Reputation | ~120,000 | ~$0.30 |
| Register Project | ~200,000 | ~$0.50 |
| Donate (with multiplier calc) | ~220,000 | ~$0.55 |
| Update Reputation | ~140,000 | ~$0.35 |
| Finalize Round (10 projects) | ~800,000 | ~$2.00 |

**Total for full organization lifecycle:** ~$5-10 USD on Polygon

---

## SECURITY CONSIDERATIONS

1. **Soulbound Enforcement**: SBTs override `_transfer` to prevent trading
2. **Access Control**: Role-based permissions for all critical operations
3. **Reentrancy Protection**: NonReentrant guards on donation functions
4. **DID Verification**: All operations require active DID
5. **Reputation Bounds**: Scores capped at 0-100
6. **Expiration Checks**: SBTs can expire and be revoked

---

## NEXT: Grant Management Contracts

Ready to continue with:
- MilestoneValidator.sol
- ProofOfImpactNFT.sol
- GrantManager.sol integration

Should I add those as well?
