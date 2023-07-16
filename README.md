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
    function transfer(address to, uint256 id, uint64 start, uint64 end, uint256 amount, bytes calldata data) external;
    function balanceOf(address owner, uint256 id) external view returns (bytes memory balance);
    function decodeBalance(bytes memory balance) external pure returns (TimeDelta[] memory timeDeltas);
}

struct TimeDelta {
    uint64 time;
    int192 delta;
}
```
The TimeBoundToken interface includes the following functions:

- period(uint256 id): Returns the number of seconds in one period for a specific TBT ID.
- shift(uint256 id): Returns the number of seconds representing the initial shift for counting the period.
- transfer(address to, uint256 id, uint64 start, uint64 end, uint256 amount, bytes calldata data): Allows the owner to transfer TBT tokens to a target address within a specified ownership period. The start and end parameters represent the start and end times of the ownership period, and the amount parameter specifies the number of tokens being transferred. Additional data can be passed through the data parameter.
- balanceOf(address owner, uint256 id): Returns a byte array that represents the complete timeline position of an owner. The caller must use the decodeBalance function to decode the byte array into an array of TimeDelta structs.
- decodeBalance(bytes memory balance): Decodes the byte array balance into an array of TimeDelta structs representing the timeline positions. Each TimeDelta struct contains a timestamp (time) and the corresponding change in token balance (delta) for that time period.
## Roadmap
- While the current interface includes the decodeBalance function to interpret the byte array balance, there are considerations for a potential improvement in the design of the Time Bound Token (TBT) contract. Working with dynamic arrays in Solidity can be challenging due to gas efficiency and potential security vulnerabilities.
As an alternative, an optimized approach could be considered. This approach would involve modifying the `balanceOf` function to return an array of fixed size instead of a dynamic array. By defining a fixed size, the contract can ensure efficiency and reduce the risk of potential vulnerabilities.
The decision to replace `decodeBalance` with a fixed-size array returned by balanceOf would require careful consideration of the expected maximum number of time periods and the gas cost associated with storing and retrieving this data. It would also necessitate updates to the contract logic to handle the fixed-size array representation.
- Approaches for Transferring Time Bound Tokens (TBT): The Time Bound Token (TBT) contract introduces three different approaches for transferring TBTs. The author intends to test these approaches to determine the most efficient and practical solution for TBT transfers. Approach 1 is the transfer1 function, which enables batch transfers of TBTs by accepting arrays of token IDs, start times, end times, amounts, and optional data. Approach 2 is the transfer2 function, which introduces a more structured approach to TBT transfers. It accepts an array of TimeBoundParam structs, where each struct encapsulates the token ID, start time, end time, and amount for a TBT transfer. Approach 3 is the transfer3 function, which includes the onReceived callback function. This function allows the caller to receive updated information about the transferred TBTs and enables the receiving contract to process the transferred TBTs and perform additional actions.
The TimeBoundParam struct consists of the following fields:
```
struct TimeBoundParam {
    uint256 id;
    uint64 start;
    uint64 end;
    uint256 amount;
}
```

The roadmap for the implementation of this potential change involves conducting thorough gas cost analysis, evaluating the security implications, and engaging in discussions with the Ethereum community to gather feedback and assess the feasibility and benefits of this optimization. Any proposed changes to the interface would be subject to further review, iteration, and community consensus.

## Future Scope
The introduction of TBT opens up new possibilities for financializing time-bound assets. Protocols can leverage TBT for creating automated market makers (AMMs), allowing the swapping of TBT tokens between different timelines or trading present and future tokens. Furthermore, TBT can enhance existing protocols and attract new designs in the fixed maturity financial space, bridging the gap between decentralized finance (DeFi) and traditional finance.

Additionally, TBT can empower renting NFTs for gaming, enabling game guilds to rent out their NFTs to players in exchange for earning game rewards. The flexibility and functionality of renting out NFTs can be significantly enhanced with TBT.

## Implementation and Feedback

The provided interface draft of TBT serves as an initial implementation proposal. Feedback and discussions from the Ethereum community are highly encouraged to refine the specification and address potential improvements or concerns. The authors plan to iterate on this proposal and work towards turning it into an official EIP, incorporating community feedback and addressing any outstanding issues.
