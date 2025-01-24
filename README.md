# Bitcoin-Stacks Bridge

A secure and robust cross-chain bridge enabling trustless asset transfers between Bitcoin and Stacks networks. Built with enterprise-grade security features including multi-validator consensus, timelocks, and comprehensive transaction validation.

## Overview

The Bitcoin-Stacks Bridge is a sophisticated smart contract system that facilitates secure cross-chain transactions between Bitcoin and the Stacks blockchain. It implements a multi-validator architecture with consensus mechanisms, ensuring trustless and verifiable asset transfers.

## Key Features

- **Multi-Validator Architecture**

  - Minimum consensus requirement for transaction validation
  - Dynamic validator management system
  - Active validator tracking and status verification

- **Security Measures**

  - Timelock protection for emergency operations (24-hour delay)
  - Circuit breakers for emergency scenarios
  - Comprehensive transaction validation
  - Pausable functionality for risk mitigation
  - Balance tracking and management

- **Transaction Processing**
  - Secure deposit initiation and confirmation
  - Validated withdrawals with BTC address verification
  - Emergency withdrawal mechanism with timelock protection

## Technical Specifications

### Constants

- Minimum Deposit Amount: 100,000 units
- Maximum Deposit Amount: 1,000,000,000 units
- Required Confirmations: 6
- Minimum Validators: 3
- Emergency Timelock: 144 blocks (approximately 24 hours)

### Data Structures

#### Deposits

```clarity
{
    amount: uint,
    recipient: principal,
    processed: bool,
    confirmations: uint,
    timestamp: uint,
    btc-sender: (buff 33)
}
```

#### Validators

```clarity
{
    active: bool,
    added-at: uint
}
```

#### Validator Signatures

```clarity
{
    signature: (buff 65),
    timestamp: uint
}
```

## Core Functions

### Bridge Administration

#### `initialize-bridge`

- Initializes the bridge in an active state
- Restricted to contract deployer

#### `pause-bridge`

- Pauses all bridge operations
- Emergency function restricted to contract deployer

### Validator Management

#### `add-validator`

- Adds a new validator to the system
- Parameters:
  - `validator`: Principal address of the validator
- Restrictions:
  - Only contract deployer can add validators
  - Cannot add zero address
  - Cannot add existing validators

#### `remove-validator`

- Removes an existing validator
- Parameters:
  - `validator`: Principal address of the validator
- Restrictions:
  - Only contract deployer can remove validators
  - Validator must be active

### Bridge Operations

#### `initiate-deposit`

- Initiates a new cross-chain deposit
- Parameters:
  - `tx-hash`: Bitcoin transaction hash (32 bytes)
  - `amount`: Deposit amount
  - `recipient`: Recipient address on Stacks
  - `btc-sender`: Bitcoin sender address (33 bytes)
- Validations:
  - Bridge must not be paused
  - Amount within valid range
  - Caller must be active validator
  - Transaction hash must be valid and unique

#### `confirm-deposit`

- Confirms a deposit after validation
- Parameters:
  - `tx-hash`: Bitcoin transaction hash
  - `signature`: Validator signature (65 bytes)
- Process:
  - Validates transaction and signature
  - Updates recipient balance
  - Marks deposit as processed
  - Updates total bridged amount

#### `withdraw`

- Processes withdrawal requests
- Parameters:
  - `amount`: Withdrawal amount
  - `btc-recipient`: Bitcoin recipient address (34 bytes)
- Validations:
  - Bridge must not be paused
  - Sufficient balance check
  - Updates balances and total bridged amount

#### `emergency-withdraw`

- Emergency withdrawal function with timelock
- Parameters:
  - `amount`: Withdrawal amount
  - `recipient`: Recipient address
- Restrictions:
  - Only contract deployer
  - 24-hour timelock
  - Sufficient bridge balance

## Error Handling

### Authorization Errors

- `ERROR-NOT-AUTHORIZED` (u1000)
- `ERROR-BRIDGE-PAUSED` (u1006)
- `ERROR-INVALID-VALIDATOR-ADDRESS` (u1007)

### Transaction Errors

- `ERROR-INVALID-AMOUNT` (u1001)
- `ERROR-INSUFFICIENT-BALANCE` (u1002)
- `ERROR-INVALID-BRIDGE-STATUS` (u1003)
- `ERROR-INVALID-SIGNATURE` (u1004)
- `ERROR-ALREADY-PROCESSED` (u1005)
- `ERROR-INVALID-RECIPIENT-ADDRESS` (u1008)
- `ERROR-INVALID-BTC-ADDRESS` (u1009)
- `ERROR-INVALID-TX-HASH` (u1010)

### Consensus Errors

- `ERROR-INSUFFICIENT-VALIDATORS` (u1011)
- `ERROR-TIMELOCK-NOT-EXPIRED` (u1012)

## Security Considerations

1. **Validator Management**

   - Minimum validator requirement prevents centralization
   - Active validator tracking ensures system integrity
   - Strict validator addition/removal controls

2. **Transaction Security**

   - Multi-step validation process
   - Signature verification
   - Duplicate transaction prevention
   - Amount limits and balance checks

3. **Emergency Controls**
   - Bridge pause functionality
   - Timelocked emergency withdrawals
   - Circuit breaker mechanisms
