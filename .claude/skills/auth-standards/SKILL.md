---

name: auth-standards
description: Implement login, logout, JWT authentication, token refresh, session management, biometrics, route guards and role-based access.
--------------------------------------------------------------------------------------------------------------------------------------------

# Flutter Authentication & Authorisation Standards

## When to use

Use this skill when implementing:

* Login
* Logout
* Session handling
* JWT authentication
* Refresh token management
* Biometric authentication
* Route guards
* Role-based access
* Forgot password
* MPIN authentication

## Related Skills

Use alongside:

* architecture
* api-standards
* data-storage
* unit-testing

## Skill Usage

When implementing any authentication or authorisation feature in this project, follow every rule in this file exactly.

Apply these standards to:

* Login
* Logout
* Token management
* Token refresh
* Session handling
* Biometric authentication
* MPIN authentication
* Role-based access
* Route guards

Architecture, folder structure, dependency injection, state management and feature structure must follow the architecture skill.

---

# Authentication & Authorisation Standards

## 1. Authentication Overview

This project uses JWT-based authentication with silent token refresh and biometric re-authentication.

| Flow              | Mechanism                                        |
| ----------------- | ------------------------------------------------ |
| Primary login     | Email + Password → JWT access + refresh token    |
| Token persistence | FlutterSecureStorage only                        |
| Token renewal     | Silent refresh via AuthInterceptor               |
| Re-authentication | Biometric (Fingerprint / Face ID) via local_auth |
| Session expiry    | Inactivity timeout (30 min)                      |
| Logout            | Token revocation + full secure storage clear     |

---

## 2. Token Lifecycle

Login → Save access token + refresh token in Secure Storage

↓

API Request → AuthInterceptor injects Bearer token

↓

401 Response → Attempt silent refresh

↓

Refresh Success → Save new token → Retry request

↓

Refresh Failure → Clear storage → Route guard redirects to login

### Token Storage Keys

Always use AppConstants.

Never use raw strings.

```dart
AppConstants.tokenKey
AppConstants.refreshTokenKey
AppConstants.userIdKey
```

### Token Save

```dart
await _storage.saveToken(data['access_token']);
await _storage.saveRefreshToken(data['refresh_token']);
await _storage.write(
  AppConstants.userIdKey,
  data['user']['id'],
);
```

### Token Clear

```dart
await _storage.clearAll();
```

---

## 3. AuthInterceptor Rules

Location:

```text
lib/core/network/interceptors/auth_interceptor.dart
```

Rules:

* Inject Authorization Bearer token on every request.
* Attempt one refresh only.
* Save refreshed token.
* Retry original request.
* Clear storage on refresh failure.
* Never retry infinitely.

---

## 4. Login Flow

### LoginUsecase

Responsibilities:

* Validate credentials
* Call repository
* Return Either<Failure, UserEntity>

### Validation Rules

| Field    | Rule                       |
| -------- | -------------------------- |
| Email    | Required + valid email     |
| Password | Required + minimum 6 chars |

Validation must happen before API execution.

### Auth State

```text
idle
 ↓
loading
 ↓
authenticated
```

or

```text
idle
 ↓
loading
 ↓
error
```

After login:

1. Save tokens
2. Save user
3. Update state
4. Navigate to dashboard
5. Warm caches

---

## 5. Logout Flow

Required order:

1. Revoke token on server (best effort)
2. Clear Secure Storage
3. Clear caches
4. Reset providers
5. Redirect to login

Never skip storage cleanup.

---

## 6. Forgot Password Flow

Flow:

User enters email

↓

POST forgot-password

↓

Always show success response

↓

User opens email

↓

Reset password

Rules:

* Prevent user enumeration.
* Never expose account existence.

---

## 7. Biometric Authentication

Use:

```dart
local_auth
```

Use biometrics for:

* Viewing sensitive information
* Fund transfers
* Password changes
* MPIN changes

Never use biometrics as the only authentication method.

Always maintain a fallback.

### Biometric Preference

Store preference in Hive.

```dart
await preferencesBox.put(
  'biometric_enabled',
  true,
);
```

---

## 8. Session Management

Session timeout:

```text
30 minutes
```

Requirements:

* Reset timer on interaction.
* Logout on timeout.
* Redirect to login.
* Validate token on app resume.

---

## 9. Route Guards

Use GoRouter redirect guards.

Public routes:

```text
/login
/forgot-password
```

Protected routes:

```text
/dashboard
/profile
/accounts
/transactions
/transfer
```

Rules:

* All protected routes require authentication.
* Route guard is the single enforcement point.

---

## 10. Role-Based Access

Roles:

```dart
customer
relationship_manager
admin
```

Rules:

* Validate at UseCase layer.
* Never rely solely on UI visibility.
* Return UnauthorizedFailure when access is denied.

---

## 11. MPIN

Requirements:

* 4–6 digits
* Store hashed value only
* Use a slow key-derivation hash: PBKDF2 (≥100,000 iterations), bcrypt, or Argon2 — never a fast general-purpose hash
* Unique random salt per user, generated at MPIN creation
* Lock after 3 failures
* Require password re-authentication after lockout

Never:

* Store plaintext MPIN
* Log MPIN values
* Hash MPIN with SHA-256/SHA-1/MD5 alone — a 4–6 digit PIN has only 10⁴–10⁶ possible values and is brute-forceable in seconds against a fast hash, even with a salt

---

## 12. Password & Sensitive Field Masking

* All password, PIN and MPIN input fields must set `obscureText: true`
* Provide a show/hide toggle only for password fields, never for MPIN/OTP entry
* Disable copy/paste and predictive text/autofill suggestions on PIN and MPIN fields
* Mask sensitive identifiers in the UI — account numbers, card numbers — showing only the last 4 digits (e.g. `**** **** **** 1234`)
* Never display full account/card numbers, CVV, or MPIN in logs, crash reports, or analytics events

---

## 13. Login Attempt Throttling

Apply the same brute-force protection to password login as MPIN:

* Lock the account after 5 consecutive failed login attempts
* Apply exponential backoff between retries after the 3rd failure
* Lock duration: minimum 15 minutes, or require an OTP/email-based unlock
* Return the same generic error message on lockout as on invalid credentials — never reveal whether the account exists or how many attempts remain

---

## 14. Device Integrity & Screen Protection

* Check for root (Android) / jailbreak (iOS) at app start using a dedicated package (e.g. `flutter_jailbreak_detection`); block or warn before allowing login on a compromised device
* Enable `FLAG_SECURE` on Android and disable screen capture/recording on iOS for screens showing balances, transaction details, card details, or MPIN entry
* Re-validate device integrity after app resume from background, not only at cold start

---

## 15. Security Checklist

* Store tokens only in Secure Storage
* Use HTTPS only
* Never log passwords
* Never log tokens
* Never log MPIN
* Mask passwords, PINs, account numbers and card numbers in the UI
* Implement session timeout
* Use route guards
* Use role validation
* Limit refresh attempts
* Limit login attempts
* Detect root/jailbreak before allowing authentication
* Block screenshots/screen recording on sensitive screens
* Prevent user enumeration

---

## 16. Folder Structure

```text
lib/
├── core/
│   ├── auth/
│   └── network/
│       └── interceptors/
│
└── features/
    └── auth/
        ├── domain/
        ├── data/
        └── presentation/
```

Architecture details must follow the architecture skill.

---

## 17. Do NOT

* Do not store tokens in Hive.
* Do not store tokens in SharedPreferences.
* Do not store plaintext MPIN.
* Do not hash MPIN with a fast hash (SHA-256/SHA-1/MD5) instead of PBKDF2/bcrypt/Argon2.
* Do not skip logout cleanup.
* Do not bypass route guards.
* Do not retry refresh endlessly.
* Do not expose account existence.
* Do not log secrets.
* Do not display full account/card numbers in the UI.
* Do not allow unlimited login attempts.
* Do not skip root/jailbreak checks before authentication.
* Do not enforce authorization only in the UI.

---

## Output

Authentication features generated using this skill must:

* Follow Clean Architecture
* Follow Feature First Structure
* Use Repository Pattern
* Use Riverpod
* Use GoRouter route guards
* Use Secure Storage
* Use Either<Failure, T>
* Include tests
* Follow the architecture skill

## Project Priorities

1. Security
2. Scalability
3. Clean Architecture
4. Testability

All authentication implementations must follow these priorities.
