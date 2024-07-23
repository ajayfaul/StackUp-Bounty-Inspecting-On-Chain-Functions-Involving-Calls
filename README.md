# StackUp-Bounty-Inspecting-On-Chain-Functions-Involving-Calls

This Repository is contianed related to Bounty - Inspecting On-Chain Functions Involving Calls

## Introduction

- **Protocol Name:** Centrifuge
- **Category:** RWA Tokenization
- **Smart Contract:** ERC20 (Centrifuge Token Contract)

## Function Analysis

- **Function Name:** permit
- **Block Explorer Link:** [Centrifuge Token Contract on Etherscan](https://etherscan.io/token/0xc221b7e65ffc80de234bbb6667abdd46593d34f0#code#L1)

- **Function Code:**

```solidity
// --- Approve by signature ---
    function permit(address owner, address spender, uint value, uint deadline, uint8 v, bytes32 r, bytes32 s) external {
        require(deadline >= block.timestamp, 'cent/past-deadline');
        bytes32 digest = keccak256(
            abi.encodePacked(
                '\x19\x01',
                DOMAIN_SEPARATOR,
                keccak256(abi.encode(PERMIT_TYPEHASH, owner, spender, value, nonces[owner]++, deadline))
            )
        );
        address recoveredAddress = ecrecover(digest, v, r, s);
        require(recoveredAddress != address(0) && recoveredAddress == owner, 'cent-erc20/invalid-sig');
        allowance[owner][spender] = value;
        emit Approval(owner, spender, value);
    }
```

- **Used Encoding/Decoding or Call Method:** `abi.encodePacked`, `abi.encode`.

## Explanation

- **Purpose:** The permit function that exist on this ERC20 Centrifuge Token Contract is designed to approve token transfers using off-chain signatures, which makes the process more efficient and user-friendly. This feature is especially useful in (DeFi) decentralized finance and (RWA) real-world asset tokenization applications, where users have to securely and effectively authorize transactions.
- **Detailed Usage:**

  - **Encoding:** `abi.encodePacked` and `abi.encode` are used to create a hash (digest) that represents the permit parameters which include the `owner`, `spender`, `value`, `nonce`, and `deadline`.Both the hash and the signature are then sent to the contract for validation or this hash is signed off-chain by the token owner.
  - **ecrecover:** This function is used to verify the signature. The `ecrecover` function recovers the address that created the signature owner address (`recoveredAddress`) and verifies that it is identical or matches to the `owner` address.
  - **Nonce Handling:** This `nonces[owner]++` is used to increase the nonce of the owner of the respective signature to protect both signature and Nonce being played or an replay attack, the former Amount is signed and approved the nonce must be increment from the above line in this contract.
  - **Approval Update:** This used to check If the signature is valid, the `allowance` of the `spender` or the owner is updated with the specified `value`, and an `Approval` event is emitted.

- **Impact:** This `permit` function allows users to accept token transfers based on a signature as opposed to an actual transaction in the chain and hence extends functionality of how you can interact with contracts. This helps to save gas fees related to approvals and contributes in making the users experience smoother. It provides a fast, secure way to conduct transactions essential for handling and trading tokenized real-world assets when it comes to RWA tokenization.
