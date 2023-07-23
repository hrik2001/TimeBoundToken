Title: Time Bound Token (TBT)  
Author: Ricsson Ngo (@Timelord.Eth), Shatabarto Bhattacharya (@riksucks)  
Email: ricsson@timeswap.io, shatabarto@timeswap.io

## Abstract

Time Bound Token (TBT) is a utility token that enables the transfer and management of ownership of an asset's timeline. TBT represents time-bound assets such as rentable NFTs, escrow positions, time-restricted governance, generalized options, renting real estates, and more. By dividing tokens into periodic timelines, TBT allows different addresses to own specific periods, facilitating flexible ownership and utility for various use cases.

## Motivation

The motivation behind the Time Bound Token (TBT) is to establish a standardized approach for managing time-bound assets on the Ethereum blockchain. By providing a token-based representation of ownership within specific time periods, TBT aims to enhance the functionality and versatility of time-bound assets in decentralized applications and smart contracts.

## Specification

1. TBT Diagram: A visual representation of 100 Time Bound Token A with a monthly period is provided in the following figure
![image](https://github.com/hrik2001/TimeBoundToken/assets/11733600/d24bc7d6-ce3e-4100-8d41-68c52b28c4f1)


2. Ownership and Utility: TBT serves as a utility token, granting the owner the right to utilize the underlying asset during specific time periods. Ownership is determined based on the claim of the timeline by the user during the present time.

3. Recurring Payments: TBT enables recurring payments and benefits based on the ownership timeline. Users can call the utility function as long as the present time falls within their ownership period.

4. Token Transfer: TBT can be transferred between addresses, allowing the transfer of ownership rights and token amounts. Transfers must adhere to the ownership periods and should be divisible by the defined periods.

5. Interface Draft: The proposed interface for TBT is as follows:

```solidity
interface TimeBoundToken {
    function period(uint256 id) external view returns (uint256 seconds);
    function shift(uint256 id) external view returns (uint256 seconds);

    function behavior(uint256 id) external view returns (address); // Should it be behavior or behaviour?
    function behaviors(uint256[] calldata ids) external view returns (address[]) // Alternative

    function totalSupply(uint256 id) external view returns (uint256 amount);
    function totalSupply(uint256[] calldata ids) external view returns (uint256[] memory amounts); // Alternative

    function balanceOf(address owner, uint256 id) external view returns (TimeDelta[] memory timeDeltas);
    function balanceOf(address owner, uint256[] calldata ids) external view returns (TimeDelta[][] memory timeDeltas); // Alternative

    function initialize() external returns (uint256 id);

    struct TransferRequest {
        address to;
        TokenRequest[] tokenRequests;
        bytes data;
    }

    struct TokenRequest {
        uint256 id;
        bool fromMsgSender;
        uint64 start;
        uint64 end;
        uint256 amount;
        bytes data;
    }

    struct TransferResponse {
        TokenResponse[] tokenResponses;
        bytes data;
    }

    struct TokenResponse {
        uint256 amount;
        bytes data;
    }

    function transfer(TransferRequest[] calldata request) external returns (TransferResponse[] memory response);
}

struct TimeDelta {
    uint64 time;
    int192 delta;
}
```
The TimeBoundToken interface includes the following functions:

- period(uint256 id): Returns the number of seconds in one period for a specific TBT ID.
- shift(uint256 id): Returns the number of seconds representing the initial shift for counting the period.

- behavior(uint256 id): Returns the address of the behavior contract of the TBT given the id.
- behaviors(uint256[] calldata ids): An alternative where we can get an array of behavior contract for the given array of ids.

- totalSupply(uint256 id): Returns the the total supply of the TBT given the id.
- totalSupply(uint256[] calldata ids): An alternative where we can get an array of total supply for the given array of ids.

- balanceOf(address owner, uint256 id): Returns an array of TimeDelta structs representing the timeline positions. Each TimeDelta struct contains a timestamp (time) and the corresponding change in token balance (delta) for that time period.
- balanceOf(address owner, uint256[] calldata ids): An alternative where we can get an array of TimeDeltas for the given array of ids.

- initialize(): Allows the behavior contract to initialize the TBT contract.

- transfer(TransferRequest[] calldata request): Allows the owner to transfer TBT tokens to a target addresses within a specified ownership period. The start and end parameters represent the start and end times of the ownership period, and the amount parameter specifies the number of tokens being transferred. Additional data can be passed through the data parameter.


## Roadmap
- Approaches for transferring Time Bound Tokens (TBT): We will try this approach of the transfer function, which enables batch transfers of TBTs by accepting an array of TransferRequest, which determines the addresses to transfer TBT, each with an array of TokenRequest. The transfer returns an array of TransferResponse, each with an array of TokenResponse:

```
/// @param to The address to which to transfer the TBT.
/// @notice When the to address is zero, burn the tokens.
/// @notice If the to address is a human owned address, simply transfer all the tokens.
/// @notice If the to address is a contract address, the contract has to implement an interface to accept how much to token to receive.
/// @param tokenRequests An array of token request.
/// @param data A custom data to send to the to address contract.
struct TransferRequest {
    address to;
    TokenRequest[] tokenRequests;
    bytes data;
}
```

```
/// @param id The TBT id.
/// @param fromMsgSender true if it is transferred from the msg sender, false if it is minted.
/// @param start the start time tick of the TBT, disregarded if the TBT is minted or burnt.
/// @param end the end time tick of the TBT, disregarded if the TBT is minted or burnt.
/// @param amount The amount of TBT to be transferred.
/// @param data A custom data sent to the TBT implementation.
struct TokenRequest {
    uint256 id;
    bool fromMsgSender;
    uint64 start;
    uint64 end;
    uint256 amount;
    bytes data;
}
```

```
/// @param tokenResponses An array of token response.
/// @param data The custom data sent from the contract.
struct TransferResponse {
    TokenResponse[] tokenResponses;
    bytes data;
}
```

```
/// @param amount The amount of TBT received by the to address.
/// @param data The custom data sent from the TBT implementation.
struct TokenResponse {
    uint256 amount;
    bytes data;
}
```

The roadmap for the implementation of this potential change involves conducting thorough gas cost analysis, evaluating the security implications, and engaging in discussions with the Ethereum community to gather feedback and assess the feasibility and benefits of this optimization. Any proposed changes to the interface would be subject to further review, iteration, and community consensus.

We will add more information about the interface that the contract has to implement to accept TBT token.

## Future Scope
The introduction of TBT opens up new possibilities for financializing time-bound assets. Protocols can leverage TBT for creating automated market makers (AMMs), allowing the swapping of TBT tokens between different timelines or trading present and future tokens. Furthermore, TBT can enhance existing protocols and attract new designs in the fixed maturity financial space, bridging the gap between decentralized finance (DeFi) and traditional finance.

Additionally, TBT can empower renting NFTs for gaming, enabling game guilds to rent out their NFTs to players in exchange for earning game rewards. The flexibility and functionality of renting out NFTs can be significantly enhanced with TBT.

## Implementation and Feedback

The provided interface draft of TBT serves as an initial implementation proposal. Feedback and discussions from the Ethereum community are highly encouraged to refine the specification and address potential improvements or concerns. The authors plan to iterate on this proposal and work towards turning it into an official EIP, incorporating community feedback and addressing any outstanding issues.
