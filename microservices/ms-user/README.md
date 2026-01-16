# User Microservice

This service manages user registration, authentication, and profile management with gRPC server for internal service communication.

## Testing Details

### Unit Test Coverage

**User Entity Tests:**
- Valid user creation
- Email format validation
- Password hashing on creation
- Auto-generated UUID and timestamps

**CreateUser Use Case Tests:**
- Successful user registration
- Duplicate email throws DuplicateEmailError
- Password is hashed before saving
- Password not returned in response

**LoginUser Use Case Tests:**
- Successful login with correct credentials
- Invalid email throws InvalidCredentialsError
- Invalid password throws InvalidCredentialsError
- Password comparison with bcrypt

**UpdateUser Use Case Tests:**
- Update user profile successfully
- Email change to duplicate throws DuplicateEmailError
- Same email update allowed (no error)
- User not found throws UserNotFoundError
- Password re-hashed if changed

**DeleteUser Use Case Tests:**
- Successful user deletion
- User not found throws UserNotFoundError