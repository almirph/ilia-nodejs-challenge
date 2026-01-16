# Wallet Microservice

This service manages financial transactions (CREDIT/DEBIT) and balance calculations for users.

## Testing Details

### Unit Test Coverage

**Transaction Entity Tests:**
- Valid transaction creation
- Invalid amount (≤ 0) throws error
- Invalid type throws error
- Auto-generated UUID and timestamps

**CreateTransaction Use Case Tests:**
- Successful CREDIT transaction
- Successful DEBIT with sufficient balance
- DEBIT rejected with insufficient balance
- User validation via gRPC
- Invalid user throws UserNotFoundError
- Invalid amount throws InvalidAmountError

**GetBalance Use Case Tests:**
- Returns 0 for new user (no transactions)
- Calculates balance correctly with mixed transactions
- CREDIT increases balance
- DEBIT decreases balance