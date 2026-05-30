// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract Registration{

    address payable public Regulatory_Authority;  // 0x20B38Da6a701c206820420dCfcB03FcB8720f206beddC4
    mapping(address=> bool) public Artisanal_Miner; //0xAb8483F64d9C6d1EcF9b849Ae677dD3315835cb2
    mapping(address=>bool) public Licensed_Buying_Centers; //0x4B20993Bc481177ec7E8f571ceCaE8A9e22C02db
    mapping(address => bool) public Large_Scale_Mining_Firm; //0x78731D3Ca6b7E34aC0F824c42a7cC18A495cabaB
    mapping(address => bool) public Gold_Bod; // 0x617F2E2fD72FD9D5503197092aC168c91465E7f2
    mapping(address => bool) public Data_Collection_Oracle; //0x17F6AD8Ef982297579C203069C1DbfFE4348c372
    mapping(address => bool) public Data_Processing_Oracle; //0x5c6B0f7Bf3E7ce046039Bd8FABdfD3f9F5021678
    mapping(address => bool) public Data_Analysis_Oracle; //0x03C6FcED478cBbC9a4FAB34eF9f40767739D1Ff7
    
    modifier onlyRegulatory_Authority{
        require(msg.sender == Regulatory_Authority, "Sender not authorized.");
        _;
    }  

   constructor()  {
        Regulatory_Authority = payable(msg.sender);
    }

    function registerArtisanal_Miner(address A1) external onlyRegulatory_Authority {
        require(!Artisanal_Miner[A1], "Artisanal_Miner exists already");
        Artisanal_Miner[A1] = true;
    }

    function registerLicensed_Buying_Centers(address B1) external onlyRegulatory_Authority {
        require(!Licensed_Buying_Centers[B1], "Licensed_Buying_Centers exists already");
        Licensed_Buying_Centers[B1] = true;
    }

    function registerLarge_Scale_Mining_Firm(address M1) external onlyRegulatory_Authority {
        require(!Large_Scale_Mining_Firm[M1], "Large_Scale_Mining_Firm exists already");
        Large_Scale_Mining_Firm[M1] = true;
    }

    function registerGold_Bod(address G1) external onlyRegulatory_Authority {
        require(!Gold_Bod[G1], "Gold_Bod exists already");
        Gold_Bod[G1] = true;
    }

    function registerData_Collection_Oracle(address O1) external onlyRegulatory_Authority {
        require(!Data_Collection_Oracle[O1], "Data_Collection_Oracle exists already");
        Data_Collection_Oracle[O1] = true;
    }

    function registerData_Processing_Oracle(address O2) external onlyRegulatory_Authority {
        require(!Data_Processing_Oracle[O2], "Data_Processing_Oracle exists already");
        Data_Processing_Oracle[O2] = true;
    }

    function registerData_Analysis_Oracle(address O3) external onlyRegulatory_Authority {
        require(!Data_Analysis_Oracle[O3], "Data_Analysis_Oracle exists already");
        Data_Analysis_Oracle[O3] = true;
    }
   
    function isRegulatory_Authority(address r) public view returns(bool) {
        return (Regulatory_Authority == r);
    }

    function Artisanal_MinerExists(address A1) public view returns(bool) {
        return Artisanal_Miner[A1];
    }

    function Licensed_Buying_CentersExists(address B1) public view returns(bool) {
        return Licensed_Buying_Centers[B1];
    }

    function Large_Scale_Mining_FirmExists(address M1) public view returns(bool) {
        return Large_Scale_Mining_Firm[M1];
    }

    function Gold_BodExists(address G1) public view returns(bool) {
        return Gold_Bod[G1];
    }

    function Data_Collection_OracleExists(address O1) public view returns(bool) {
        return Data_Collection_Oracle[O1];
    }

    function Data_Processing_OracleExists(address O2) public view returns(bool) {
        return Data_Processing_Oracle[O2];
    }

    function Data_Analysis_OracleExists(address O3) public view returns(bool) {
        return Data_Analysis_Oracle[O3];
    }
}
contract Legal_Concession {
    Registration public registrationContract;
    address payable public Regulatory_Authority;

    uint256 public totalMiningZones;
    uint256 public totalMiningClearanceRequests;

    int256 public constant BOUNDARY_BUFFER = 20; // 0.002 degrees if coordinates are scaled by 1e4

    constructor(address registration, address payable authorizedRegulator) {
        require(registration != address(0), "Invalid Registration SC");
        require(authorizedRegulator != address(0), "Invalid regulator");
        require(msg.sender == authorizedRegulator, "Sender not authorized");

        Regulatory_Authority = authorizedRegulator;
        registrationContract = Registration(registration);
    }

    modifier onlyRegulatory_Authority() {
        require(msg.sender == Regulatory_Authority, "Only Regulatory Authority");
        _;
    }

    modifier onlyRegisteredSupplyChainActor() {
        require(
            registrationContract.Artisanal_MinerExists(msg.sender) ||
            registrationContract.Licensed_Buying_CentersExists(msg.sender) ||
            registrationContract.Large_Scale_Mining_FirmExists(msg.sender),
            "Not registered supply-chain actor"
        );
        _;
    }

    struct MiningZone {
        uint256 zoneId;
        string zoneName;
        string region;
        int256 minLat;
        int256 maxLat;
        int256 minLon;
        int256 maxLon;
        bool isActive;
        uint256 createdAt;
    }

    enum ClearanceStatus {
        APPROVED_SOURCE,
        BOUNDARY_REVIEW,
        UNAUTHORIZED_SOURCE,
        FLAGGED_ZONE
    }

    struct MiningClearanceRequest {
        address requester;
        string actorRole;
        string region;
        int256 latitude;
        int256 longitude;
        ClearanceStatus status;
        string reason;
        uint256 timestamp;
    }

    mapping(uint256 => MiningZone) public miningZones;
    mapping(uint256 => MiningClearanceRequest) public miningClearanceRequests;
    mapping(bytes32 => bool) public flaggedZones;

    event NewMiningZoneRegistered(
        uint256 indexed zoneId,
        string zoneName,
        string region,
        string minLatDecimal,
        string maxLatDecimal,
        string minLonDecimal,
        string maxLonDecimal,
        address indexed registeredBy,
        uint256 timestamp
    );

    event ConcessionBoundaryUpdated(
    uint256 indexed zoneId,
    string[4] oldBoundaryDecimal,
    string[4] newBoundaryDecimal,
    address indexed updatedBy,
    uint256 timestamp
    );

    event FlaggedZoneUpdated(
        string latitudeDecimal,
        string longitudeDecimal,
        bool isFlagged,
        address indexed updatedBy,
        uint256 timestamp
    );

    event MiningClearanceRequested(
        uint256 indexed requestId,
        address indexed requester,
        string actorRole,
        string region,
        string latitudeDecimal,
        string longitudeDecimal,
        ClearanceStatus status,
        string reason,
        uint256 timestamp
    );

    event MiningClearanceStatusUpdated(
        uint256 indexed requestId,
        ClearanceStatus oldStatus,
        ClearanceStatus newStatus,
        string reason,
        address indexed updatedBy,
        uint256 timestamp
    );

    function createNewMiningZone(
        string calldata zoneName,
        string calldata region,
        int256 minLat,
        int256 maxLat,
        int256 minLon,
        int256 maxLon
    ) public onlyRegulatory_Authority returns (uint256) {
        require(maxLat > minLat, "Invalid latitude boundary");
        require(maxLon > minLon, "Invalid longitude boundary");

        totalMiningZones++;

        miningZones[totalMiningZones] = MiningZone({
            zoneId: totalMiningZones,
            zoneName: zoneName,
            region: region,
            minLat: minLat,
            maxLat: maxLat,
            minLon: minLon,
            maxLon: maxLon,
            isActive: true,
            createdAt: block.timestamp
        });

        emit NewMiningZoneRegistered(
            totalMiningZones,
            zoneName,
            region,
            formatCoordinate(minLat),
            formatCoordinate(maxLat),
            formatCoordinate(minLon),
            formatCoordinate(maxLon),
            msg.sender,
            block.timestamp
        );

        return totalMiningZones;
    }

    function updateConcessionBoundary(
    uint256 zoneId,
    int256 newMinLat,
    int256 newMaxLat,
    int256 newMinLon,
    int256 newMaxLon
)
    public
    onlyRegulatory_Authority
{
    require(zoneId > 0 && zoneId <= totalMiningZones, "Invalid zone ID");

    MiningZone storage zone = miningZones[zoneId];

    require(zone.isActive, "Mining zone inactive");
    require(newMaxLat > newMinLat, "Invalid latitude boundary");
    require(newMaxLon > newMinLon, "Invalid longitude boundary");

    string[4] memory oldBoundary = [
        formatCoordinate(zone.minLat),
        formatCoordinate(zone.maxLat),
        formatCoordinate(zone.minLon),
        formatCoordinate(zone.maxLon)
    ];

    string[4] memory newBoundary = [
        formatCoordinate(newMinLat),
        formatCoordinate(newMaxLat),
        formatCoordinate(newMinLon),
        formatCoordinate(newMaxLon)
    ];

    zone.minLat = newMinLat;
    zone.maxLat = newMaxLat;
    zone.minLon = newMinLon;
    zone.maxLon = newMaxLon;

    emit ConcessionBoundaryUpdated(
        zoneId,
        oldBoundary,
        newBoundary,
        msg.sender,
        block.timestamp
    );
}

    function updateFlaggedZone(
        int256 latitude,
        int256 longitude,
        bool isFlagged
    ) public onlyRegulatory_Authority {
        bytes32 zoneKey = keccak256(abi.encodePacked(latitude, longitude));
        flaggedZones[zoneKey] = isFlagged;

        emit FlaggedZoneUpdated(
            formatCoordinate(latitude),
            formatCoordinate(longitude),
            isFlagged,
            msg.sender,
            block.timestamp
        );
    }

    function requestMiningClearance(
        string calldata actorRole,
        string calldata region,
        int256 latitude,
        int256 longitude
    ) public onlyRegisteredSupplyChainActor returns (uint256) {
        bytes32 zoneKey = keccak256(abi.encodePacked(latitude, longitude));

        (bool insideLegalZone, bool insideBoundary) =
            _checkCoordinate(latitude, longitude);

        ClearanceStatus status;
        string memory reason;

        if (flaggedZones[zoneKey]) {
            status = ClearanceStatus.FLAGGED_ZONE;
            reason = "Previously flagged illegal mining zone.";
        } else if (insideLegalZone) {
            status = ClearanceStatus.APPROVED_SOURCE;
            reason = "Coordinate is inside a legal mining concession.";
        } else if (insideBoundary) {
            status = ClearanceStatus.BOUNDARY_REVIEW;
            reason = "Coordinate is within boundary buffer. Manual review required.";
        } else {
            status = ClearanceStatus.UNAUTHORIZED_SOURCE;
            reason = "Coordinate is outside all legal mining concessions.";
        }

        totalMiningClearanceRequests++;

        miningClearanceRequests[totalMiningClearanceRequests] =
            MiningClearanceRequest({
                requester: msg.sender,
                actorRole: actorRole,
                region: region,
                latitude: latitude,
                longitude: longitude,
                status: status,
                reason: reason,
                timestamp: block.timestamp
            });

        emit MiningClearanceRequested(
            totalMiningClearanceRequests,
            msg.sender,
            actorRole,
            region,
            formatCoordinate(latitude),
            formatCoordinate(longitude),
            status,
            reason,
            block.timestamp
        );

        return totalMiningClearanceRequests;
    }

    function updateMiningClearanceStatus(
        uint256 requestId,
        ClearanceStatus newStatus,
        string calldata reason
    ) public onlyRegulatory_Authority {
        require(
            requestId > 0 && requestId <= totalMiningClearanceRequests,
            "Invalid clearance request ID"
        );

        MiningClearanceRequest storage request =
            miningClearanceRequests[requestId];

        ClearanceStatus oldStatus = request.status;

        request.status = newStatus;
        request.reason = reason;
        request.timestamp = block.timestamp;

        emit MiningClearanceStatusUpdated(
            requestId,
            oldStatus,
            newStatus,
            reason,
            msg.sender,
            block.timestamp
        );
    }

    function _checkCoordinate(
        int256 latitude,
        int256 longitude
    ) internal view returns (bool insideLegalZone, bool insideBoundary) {
        for (uint256 i = 1; i <= totalMiningZones; i++) {
            MiningZone memory zone = miningZones[i];

            if (!zone.isActive) continue;

            if (
                latitude >= zone.minLat &&
                latitude <= zone.maxLat &&
                longitude >= zone.minLon &&
                longitude <= zone.maxLon
            ) {
                insideLegalZone = true;
            }

            if (
                latitude >= zone.minLat - BOUNDARY_BUFFER &&
                latitude <= zone.maxLat + BOUNDARY_BUFFER &&
                longitude >= zone.minLon - BOUNDARY_BUFFER &&
                longitude <= zone.maxLon + BOUNDARY_BUFFER
            ) {
                insideBoundary = true;
            }
        }
    }

    function formatCoordinate(int256 value) public pure returns (string memory) {
        bool negative = value < 0;
        uint256 absValue = uint256(negative ? -value : value);

        uint256 integerPart = absValue / 10000;
        uint256 decimalPart = absValue % 10000;

        return string(
            abi.encodePacked(
                negative ? "-" : "",
                uintToString(integerPart),
                ".",
                padZeros(decimalPart)
            )
        );
    }

    function padZeros(uint256 value) internal pure returns (string memory) {
        if (value < 10) return string(abi.encodePacked("000", uintToString(value)));
        if (value < 100) return string(abi.encodePacked("00", uintToString(value)));
        if (value < 1000) return string(abi.encodePacked("0", uintToString(value)));
        return uintToString(value);
    }

    function uintToString(uint256 value) internal pure returns (string memory) {
        if (value == 0) return "0";

        uint256 temp = value;
        uint256 digits;

        while (temp != 0) {
            digits++;
            temp /= 10;
        }

        bytes memory buffer = new bytes(digits);

        while (value != 0) {
            digits -= 1;
            buffer[digits] = bytes1(uint8(48 + uint256(value % 10)));
            value /= 10;
        }

        return string(buffer);
    }
}

contract Illegal_Mining_Detection {
    Registration public registrationContract;
    Legal_Concession public legalConcessionContract;

    address payable public Regulatory_Authority;
    address public Data_Collection_Oracle;
    address public Data_Processing_Oracle;
    address public Data_Analysis_Oracle;

    constructor(
        address registrationAddress,
        address legalConcessionContractAddress,
        address payable authorizedRegulator,
        address authorizedDataCollectionOracle,
        address authorizedDataProcessingOracle,
        address authorizedDataAnalysisOracle
    ) {
        require(registrationAddress != address(0), "Invalid Registration SC");
        require(legalConcessionContractAddress != address(0), "Invalid Legal SC");
        require(authorizedRegulator != address(0), "Invalid regulator");
        require(authorizedDataCollectionOracle != address(0), "Invalid collection oracle");
        require(authorizedDataProcessingOracle != address(0), "Invalid processing oracle");
        require(authorizedDataAnalysisOracle != address(0), "Invalid analysis oracle");
        require(msg.sender == authorizedRegulator, "Sender not authorized");

        Regulatory_Authority = authorizedRegulator;
        Data_Collection_Oracle = authorizedDataCollectionOracle;
        Data_Processing_Oracle = authorizedDataProcessingOracle;
        Data_Analysis_Oracle = authorizedDataAnalysisOracle;

        registrationContract = Registration(registrationAddress);
        legalConcessionContract = Legal_Concession(legalConcessionContractAddress);
    }

    modifier onlyRegulatory_Authority() {
        require(msg.sender == Regulatory_Authority, "Only Regulatory Authority");
        _;
    }

    modifier onlyDataCollectionOracle() {
        require(msg.sender == Data_Collection_Oracle, "Only Data Collection Oracle");
        _;
    }

    modifier onlyDataProcessingOracle() {
        require(msg.sender == Data_Processing_Oracle, "Only Data Processing Oracle");
        _;
    }

    modifier onlyDataAnalysisOracle() {
        require(msg.sender == Data_Analysis_Oracle, "Only Data Analysis Oracle");
        _;
    }

    enum GeoSpatialRequestStatus {
        Requested,
        Collection_Completed,
        Processing_Completed,
        Analysis_Completed,
        Failed
    }

    struct GeoSpatialRequest {
        uint256 requestId;
        string region;
        int256[4] bbox;
        string startDate;
        string endDate;
        string metadataCID;
        string processedDataCID;
        string analysisResultCID;
        GeoSpatialRequestStatus status;
        address requestedBy;
        uint256 requestedAt;
    }

    uint256 public totalGeoSpatialRequests;

    mapping(uint256 => GeoSpatialRequest) public geoSpatialRequests;

    event GeoSpatialDataRequested(
        uint256 indexed requestId,
        string region,
        string startDate,
        string endDate,
        address indexed requestedBy,
        uint256 timestamp
    );

    event GeoSpatialDataCollected(
        uint256 indexed requestId,
        string metadataCID,
        address indexed collectedBy,
        uint256 timestamp
    );

    event GeoSpatialDataProcessed(
        uint256 indexed requestId,
        string processedDataCID,
        address indexed processedBy,
        uint256 timestamp
    );

    event GeoSpatialDataAnalysisCompleted(
        uint256 indexed requestId,
        string analysisResultCID,
        address indexed completedBy,
        uint256 timestamp
    );

    function requestGeoSpatialData(
        string calldata region,
        int256[4] calldata bbox,
        string calldata startDate,
        string calldata endDate
    ) public onlyRegulatory_Authority returns (uint256) {
        require(bbox[1] > bbox[0], "Invalid latitude range");
        require(bbox[3] > bbox[2], "Invalid longitude range");

        totalGeoSpatialRequests++;

        geoSpatialRequests[totalGeoSpatialRequests] =
            GeoSpatialRequest({
                requestId: totalGeoSpatialRequests,
                region: region,
                bbox: bbox,
                startDate: startDate,
                endDate: endDate,
                metadataCID: "",
                processedDataCID: "",
                analysisResultCID: "",
                status: GeoSpatialRequestStatus.Requested,
                requestedBy: msg.sender,
                requestedAt: block.timestamp
            });

        emit GeoSpatialDataRequested(
            totalGeoSpatialRequests,
            region,
            startDate,
            endDate,
            msg.sender,
            block.timestamp
        );

        return totalGeoSpatialRequests;
    }

    function submitCollectedGeoSpatialData(
        uint256 requestId,
        string calldata metadataCID
    ) public onlyDataCollectionOracle {
        require(requestId > 0 && requestId <= totalGeoSpatialRequests, "Invalid request ID");
        require(bytes(metadataCID).length > 0, "Metadata CID required");

        GeoSpatialRequest storage request = geoSpatialRequests[requestId];

        request.metadataCID = metadataCID;
        request.status = GeoSpatialRequestStatus.Collection_Completed;

        emit GeoSpatialDataCollected(
            requestId,
            metadataCID,
            msg.sender,
            block.timestamp
        );
    }

    function submitProcessedGeoSpatialData(
        uint256 requestId,
        string calldata processedDataCID
    ) public onlyDataProcessingOracle {
        require(requestId > 0 && requestId <= totalGeoSpatialRequests, "Invalid request ID");
        require(bytes(processedDataCID).length > 0, "Processed CID required");

        GeoSpatialRequest storage request = geoSpatialRequests[requestId];

        require(bytes(request.metadataCID).length > 0, "Metadata CID missing");

        request.processedDataCID = processedDataCID;
        request.status = GeoSpatialRequestStatus.Processing_Completed;

        emit GeoSpatialDataProcessed(
            requestId,
            processedDataCID,
            msg.sender,
            block.timestamp
        );
    }

    function submitGeoSpatialAnalysisResult(
        uint256 requestId,
        string calldata analysisResultCID
    ) public onlyDataAnalysisOracle {
        require(requestId > 0 && requestId <= totalGeoSpatialRequests, "Invalid request ID");
        require(bytes(analysisResultCID).length > 0, "Analysis CID required");

        GeoSpatialRequest storage request = geoSpatialRequests[requestId];

        require(bytes(request.processedDataCID).length > 0, "Processed CID missing");

        request.analysisResultCID = analysisResultCID;
        request.status = GeoSpatialRequestStatus.Analysis_Completed;

        emit GeoSpatialDataAnalysisCompleted(
            requestId,
            analysisResultCID,
            msg.sender,
            block.timestamp
        );
    }

    function getAnalysisResultCID(
        uint256 requestId
    ) public view returns (string memory) {
        require(requestId > 0 && requestId <= totalGeoSpatialRequests, "Invalid request ID");
        return geoSpatialRequests[requestId].analysisResultCID;
    }
}

contract Compliance_Management {
    Registration public registrationContract;
    Legal_Concession public legalConcessionContract;
    Illegal_Mining_Detection public illegalMiningDetectionContract;

    address public Regulatory_Authority;
    address public Data_Analysis_Oracle;

    uint256 public thresholdGDIScore = 5000;

    constructor(
        address registrationAddress,
        address legalConcessionContractAddress,
        address illegalMiningDetectionContractAddress,
        address payable authorizedRegulator,
        address dataAnalysisOracle
    ) {
        require(registrationAddress != address(0), "Invalid Registration SC");
        require(legalConcessionContractAddress != address(0), "Invalid Legal SC");
        require(illegalMiningDetectionContractAddress != address(0), "Invalid Detection SC");
        require(authorizedRegulator != address(0), "Invalid regulator");
        require(dataAnalysisOracle != address(0), "Invalid Data Analysis Oracle");
        require(msg.sender == authorizedRegulator, "Sender not authorized");

        Regulatory_Authority = authorizedRegulator;
        Data_Analysis_Oracle = dataAnalysisOracle;

        registrationContract = Registration(registrationAddress);
        legalConcessionContract = Legal_Concession(legalConcessionContractAddress);
        illegalMiningDetectionContract =
            Illegal_Mining_Detection(illegalMiningDetectionContractAddress);
    }

    modifier onlyRegulatory_Authority() {
        require(msg.sender == Regulatory_Authority, "Only Regulatory Authority");
        _;
    }

    modifier onlyDataAnalysisOracle() {
        require(msg.sender == Data_Analysis_Oracle, "Only Data Analysis Oracle");
        _;
    }

    modifier onlySupplyChainActor() {
        require(
            registrationContract.Artisanal_MinerExists(msg.sender) ||
            registrationContract.Licensed_Buying_CentersExists(msg.sender) ||
            registrationContract.Large_Scale_Mining_FirmExists(msg.sender),
            "Not registered supply-chain actor"
        );
        _;
    }

    enum GDIRiskClass {
        LOW,
        MODERATE,
        HIGH,
        CRITICAL
    }

    enum ViolationStatus {
        NO_VIOLATION,
        WATCHLIST,
        VIOLATION_DETECTED
    }

    enum ComplianceAction {
        NO_ACTION,
        WARNING_ISSUED,
        UNDER_INVESTIGATION,
        TRANSACTION_BLOCKED,
        STAKEHOLDER_SUSPENDED,
        ENFORCEMENT_ESCALATED
    }

    enum TransactionStatus {
        PENDING,
        APPROVED,
        FLAGGED,
        REJECTED
    }

    enum PenaltyStatus {
        NO_PENALTY,
        WARNING,
        FINE_RECOMMENDED,
        TRANSACTION_FREEZE,
        LICENSE_REVIEW,
        SUSPENDED,
        ESCALATED
    }

    struct GDIRecord {
        uint256 recordId;
        uint256 requestId;
        int256 latitude;
        int256 longitude;
        uint256 actualGDI;
        GDIRiskClass riskClass;
        string legalStatus;
        string decision;
        string evidenceHash;
        string analysisResultCID;
        address submittedBy;
        uint256 timestamp;
    }

    struct GDIRecordInput {
        uint256 requestId;
        int256 latitude;
        int256 longitude;
        uint256 actualGDI;
        GDIRiskClass riskClass;
        string legalStatus;
        string decision;
        string evidenceHash;
        string analysisResultCID;
    }

    struct GoldBatchInput {
        string goldBatchId;
        uint256 sourceGDIRecordId;
        int256 sourceLat;
        int256 sourceLon;
        uint256 batchActualGDI;
        uint256 quantityGrams;
        bool declaredCertified;
        bool declaredLegalOrigin;
        string evidenceHash;
    }

    struct GoldBatchTransaction {
        string goldBatchId;
        uint256 sourceGDIRecordId;
        address actor;
        int256 sourceLat;
        int256 sourceLon;
        uint256 actualGDI;
        uint256 quantityGrams;
        bool declaredCertified;
        bool declaredLegalOrigin;
        string evidenceHash;
        TransactionStatus status;
        uint256 updatedAt;
    }

    uint256 public totalGDIRecords;
    uint256 public totalGoldBatchTransactions;

    mapping(uint256 => GDIRecord) public gdiRecords;
    mapping(bytes32 => uint256) public coordinateToRecordId;
    mapping(uint256 => GoldBatchTransaction) public goldBatchTransactions;

    ComplianceAction public currentComplianceAction;
    string public currentComplianceReason;

    PenaltyStatus public currentPenaltyStatus;
    string public currentPenaltyReason;

    event ThresholdGDIScoreUpdated(
        string oldThresholdGDIDecimal,
        string newThresholdGDIDecimal,
        address indexed updatedBy,
        uint256 timestamp
        );
    event GDIRecordSubmitted(
        uint256 indexed recordId,
        uint256 indexed requestId,
        string latitudeDecimal,
        string longitudeDecimal,
         string actualGDIDecimal,
        GDIRiskClass riskClass,
        string evidenceHash,
        uint256 timestamp
    );

    event ViolationStatusReported(
        uint256 indexed recordId,
        string thresholdGDIDecimal,
        string actualGDIDecimal,
        ViolationStatus violationStatus,
        string decisionReason,
        address indexed reportedBy,
        uint256 timestamp
    );

    event ComplianceActionUpdated(
        ComplianceAction oldComplianceAction,
        ComplianceAction newComplianceAction,
        string enforcementReason,
        address indexed updatedBy,
        uint256 timestamp
    );



    event GoldBatchTransactionUpdated(
            uint256 indexed transactionId,
            string goldBatchId,
            uint256 indexed sourceGDIRecordId,
            address indexed actor,
            string sourceLatDecimal,
            string sourceLonDecimal,
            TransactionStatus status,
            string actualGDIDecimal,
            string evidenceHash,
            uint256 timestamp
        );

    event PenaltyStatusUpdated(
        PenaltyStatus oldPenaltyStatus,
        PenaltyStatus newPenaltyStatus,
        string penaltyReason,
        address indexed updatedBy,
        uint256 timestamp
    );

    function updateGDIThresholdScore(
        uint256 newThresholdGDIScore
    ) public onlyRegulatory_Authority {
        require(
            newThresholdGDIScore > 0 && newThresholdGDIScore <= 10000,
            "Invalid GDI threshold"
        );

        uint256 oldThresholdGDIScore = thresholdGDIScore;
        thresholdGDIScore = newThresholdGDIScore;

        emit ThresholdGDIScoreUpdated(
            formatGDI(oldThresholdGDIScore),
            formatGDI(newThresholdGDIScore),
            msg.sender,
            block.timestamp
        );
    }

    function submitCoordinateGDIRecord(
        GDIRecordInput calldata input
    )
        public
        onlyDataAnalysisOracle
        returns (uint256)
    {
        require(input.actualGDI <= 10000, "Invalid GDI");
        require(bytes(input.evidenceHash).length > 0, "Evidence hash required");
        require(bytes(input.analysisResultCID).length > 0, "Analysis CID required");

        _validateAnalysisCID(input.requestId, input.analysisResultCID);

        totalGDIRecords++;

        _storeGDIRecord(totalGDIRecords, input);

        coordinateToRecordId[
            keccak256(abi.encodePacked(input.latitude, input.longitude))
        ] = totalGDIRecords;

        _emitGDIRecordSubmitted(totalGDIRecords, input);

        return totalGDIRecords;
    }

    function reportViolationStatus(
        uint256 recordId
    ) public onlyRegulatory_Authority {
        require(recordId > 0 && recordId <= totalGDIRecords, "Invalid record");

        GDIRecord storage record = gdiRecords[recordId];

        ViolationStatus violationStatus;
        string memory decisionReason;

        if (record.actualGDI >= thresholdGDIScore) {
            violationStatus = ViolationStatus.VIOLATION_DETECTED;
            decisionReason = "Actual GDI exceeds threshold GDI. Illegal mining risk detected.";
        } else if (record.actualGDI >= thresholdGDIScore / 2) {
            violationStatus = ViolationStatus.WATCHLIST;
            decisionReason = "Actual GDI is approaching threshold. Monitoring required.";
        } else {
            violationStatus = ViolationStatus.NO_VIOLATION;
            decisionReason = "Actual GDI remains below threshold. No violation detected.";
        }

        emit ViolationStatusReported(
            recordId,
            formatGDI(thresholdGDIScore),
            formatGDI(record.actualGDI),
            violationStatus,
            decisionReason,
            msg.sender,
            block.timestamp
        );
    }

    function enforceComplianceAction(
        ComplianceAction newComplianceAction
    ) public onlyRegulatory_Authority {
        ComplianceAction oldComplianceAction = currentComplianceAction;

        currentComplianceAction = newComplianceAction;
        currentComplianceReason = _getComplianceReason(newComplianceAction);

        emit ComplianceActionUpdated(
            oldComplianceAction,
            newComplianceAction,
            currentComplianceReason,
            msg.sender,
            block.timestamp
        );
    }

    function updateGoldBatchTransaction(
        GoldBatchInput calldata input
    ) public onlySupplyChainActor returns (uint256) {
        require(bytes(input.goldBatchId).length > 0, "Gold batch ID required");
        require(input.quantityGrams > 0, "Quantity must be greater than zero");
        require(input.batchActualGDI <= 10000, "Invalid GDI score");
        require(
            input.sourceGDIRecordId > 0 &&
            input.sourceGDIRecordId <= totalGDIRecords,
            "Invalid GDI record"
        );

        GDIRecord storage source =
            gdiRecords[input.sourceGDIRecordId];

        require(
            keccak256(bytes(source.evidenceHash)) ==
            keccak256(bytes(input.evidenceHash)),
            "Evidence hash mismatch"
        );

        require(
            source.latitude == input.sourceLat &&
            source.longitude == input.sourceLon,
            "Source coordinate mismatch"
        );

        totalGoldBatchTransactions++;

        GoldBatchTransaction storage txRecord =
            goldBatchTransactions[totalGoldBatchTransactions];

        txRecord.goldBatchId = input.goldBatchId;
        txRecord.sourceGDIRecordId = input.sourceGDIRecordId;
        txRecord.actor = msg.sender;
        txRecord.sourceLat = input.sourceLat;
        txRecord.sourceLon = input.sourceLon;
        txRecord.actualGDI = input.batchActualGDI;
        txRecord.quantityGrams = input.quantityGrams;
        txRecord.declaredCertified = input.declaredCertified;
        txRecord.declaredLegalOrigin = input.declaredLegalOrigin;
        txRecord.evidenceHash = input.evidenceHash;
        txRecord.updatedAt = block.timestamp;

        if (input.batchActualGDI >= thresholdGDIScore) {
            txRecord.status = TransactionStatus.REJECTED;
        } else if (!input.declaredCertified) {
            txRecord.status = TransactionStatus.REJECTED;
        } else if (!input.declaredLegalOrigin) {
            txRecord.status = TransactionStatus.FLAGGED;
        } else {
            txRecord.status = TransactionStatus.APPROVED;
        }

        emit GoldBatchTransactionUpdated(
            totalGoldBatchTransactions,
            input.goldBatchId,
            input.sourceGDIRecordId,
            msg.sender,
            formatCoordinate(input.sourceLat),
            formatCoordinate(input.sourceLon),
            txRecord.status,
            formatGDI(input.batchActualGDI),
            input.evidenceHash,
            block.timestamp
        );

        return totalGoldBatchTransactions;
    }

    function updatePenaltyStatus(
        PenaltyStatus newPenaltyStatus
    ) public onlyRegulatory_Authority {
        PenaltyStatus oldPenaltyStatus = currentPenaltyStatus;

        currentPenaltyStatus = newPenaltyStatus;
        currentPenaltyReason = _getPenaltyReason(newPenaltyStatus);

        emit PenaltyStatusUpdated(
            oldPenaltyStatus,
            newPenaltyStatus,
            currentPenaltyReason,
            msg.sender,
            block.timestamp
        );
    }

    function _validateAnalysisCID(
        uint256 requestId,
        string calldata analysisResultCID
    ) internal view {
        string memory storedCID =
            illegalMiningDetectionContract.getAnalysisResultCID(requestId);

        require(
            keccak256(bytes(storedCID)) == keccak256(bytes(analysisResultCID)),
            "Analysis CID mismatch"
        );
    }

    function _storeGDIRecord(
        uint256 recordId,
        GDIRecordInput calldata input
    ) internal {
        GDIRecord storage r = gdiRecords[recordId];

        r.recordId = recordId;
        r.requestId = input.requestId;
        r.latitude = input.latitude;
        r.longitude = input.longitude;
        r.actualGDI = input.actualGDI;
        r.riskClass = input.riskClass;
        r.legalStatus = input.legalStatus;
        r.decision = input.decision;
        r.evidenceHash = input.evidenceHash;
        r.analysisResultCID = input.analysisResultCID;
        r.submittedBy = msg.sender;
        r.timestamp = block.timestamp;
    }

    function _emitGDIRecordSubmitted(
        uint256 recordId,
        GDIRecordInput calldata input
    ) internal {
        emit GDIRecordSubmitted(
            recordId,
            input.requestId,
            formatCoordinate(input.latitude),
            formatCoordinate(input.longitude),
            formatGDI(input.actualGDI),
            input.riskClass,
            input.evidenceHash,
            block.timestamp
        );
    }

    function _getComplianceReason(
        ComplianceAction action
    ) internal pure returns (string memory) {
        if (action == ComplianceAction.NO_ACTION) {
            return "No enforcement action required.";
        }
        if (action == ComplianceAction.WARNING_ISSUED) {
            return "Warning issued due to moderate GDI or early-stage compliance concern.";
        }
        if (action == ComplianceAction.UNDER_INVESTIGATION) {
            return "Manual investigation required due to boundary-zone anomaly or suspicious activity.";
        }
        if (action == ComplianceAction.TRANSACTION_BLOCKED) {
            return "Transaction blocked because the source zone is high-risk, unauthorized, or flagged.";
        }
        if (action == ComplianceAction.STAKEHOLDER_SUSPENDED) {
            return "Stakeholder suspended due to repeated or serious compliance violations.";
        }
        return "Case escalated to regulatory authority for enforcement action.";
    }

    function _getPenaltyReason(
        PenaltyStatus status
    ) internal pure returns (string memory) {
        if (status == PenaltyStatus.NO_PENALTY) {
            return "No penalty required.";
        }
        if (status == PenaltyStatus.WARNING) {
            return "Warning issued for minor or early-stage compliance concern.";
        }
        if (status == PenaltyStatus.FINE_RECOMMENDED) {
            return "Fine recommended due to confirmed compliance breach.";
        }
        if (status == PenaltyStatus.TRANSACTION_FREEZE) {
            return "Transaction freeze imposed due to high-risk or suspicious gold batch.";
        }
        if (status == PenaltyStatus.LICENSE_REVIEW) {
            return "License review required due to repeated or serious violations.";
        }
        if (status == PenaltyStatus.SUSPENDED) {
            return "Stakeholder suspended due to confirmed illegal mining or supply-chain violation.";
        }
        return "Case escalated to regulatory authority for further enforcement.";
    }

    function formatCoordinate(int256 value) public pure returns (string memory) {
        bool negative = value < 0;
        uint256 absValue = uint256(negative ? -value : value);

        uint256 integerPart = absValue / 10000;
        uint256 decimalPart = absValue % 10000;

        return string(
            abi.encodePacked(
                negative ? "-" : "",
                uintToString(integerPart),
                ".",
                padZeros(decimalPart)
            )
        );
    }

    function padZeros(uint256 value) internal pure returns (string memory) {
        if (value < 10) {
            return string(abi.encodePacked("000", uintToString(value)));
        }
        if (value < 100) {
            return string(abi.encodePacked("00", uintToString(value)));
        }
        if (value < 1000) {
            return string(abi.encodePacked("0", uintToString(value)));
        }
        return uintToString(value);
    }

    function uintToString(uint256 value) internal pure returns (string memory) {
        if (value == 0) {
            return "0";
        }

        uint256 temp = value;
        uint256 digits;

        while (temp != 0) {
            digits++;
            temp /= 10;
        }

        bytes memory buffer = new bytes(digits);

        while (value != 0) {
            digits -= 1;
            buffer[digits] =
                bytes1(uint8(48 + uint256(value % 10)));
            value /= 10;
        }

        return string(buffer);
    }


    function formatGDI(uint256 value) public pure returns (string memory) {
    uint256 integerPart = value / 10000;
    uint256 decimalPart = value % 10000;

    return string(
        abi.encodePacked(
            uintToString(integerPart),
            ".",
            padZeros(decimalPart)
        )
    );
    }
}

