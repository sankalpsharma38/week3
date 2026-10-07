# Week 3 – Debugging, Refactoring & Performance Optimization Report

## 1. Project Objective

The objective was to take the existing VueJS authentication module and perform the debugging, refactoring, maintainability, and performance work required by the Week 3 internship task.

The project uses Vue 3, Pinia, Vue Router, and Vite. The application provides registration, login, logout, protected dashboard access, form validation, and browser persistence.

## 2. Important project context

A separate buggy Week 3 starter application was not supplied. The available application was the completed Week 2 authentication module. Therefore, this submission does **not** falsely claim that a third-party starter contained these exact defects.

Instead, the Week 3 baseline was derived from the existing working architecture and the task requirements. Controlled problematic patterns are documented in the `baseline/` directory, and the final `src/` implementation corrects them.

## 3. Main issues addressed

### 3.1 Duplicated validation

Login and registration forms require similar validation behavior. Keeping rules directly inside both views makes future changes repetitive.

**Fix:** validation functions were moved to `src/utils/validators.js`.

### 3.2 Whole-form validation during field interaction

Validating every field whenever one field loses focus can show errors before the user has interacted with those fields.

**Fix:** `validateField()` checks only the field associated with the blur event, while submit performs complete validation.

### 3.3 Password confirmation

An empty confirmation should be reported as required before checking whether it matches the password.

**Fix:** `validatePasswordConfirmation()` performs the checks in that order.

### 3.4 Repeated form markup

Login and registration contained repeated label/input/error structures.

**Fix:** `AuthField.vue` provides a reusable field component.

### 3.5 Browser storage handling

Direct browser storage operations can fail and persisted JSON can become invalid.

**Fix:** `src/utils/storage.js` wraps reads, writes, and removal in safe operations. Persisted objects are normalized before being placed into Pinia state.

### 3.6 Password persistence

The final demonstration does not store the raw password. It stores a SHA-256 digest as `passwordHash`.

This is an educational improvement only. Production authentication should use server-side password hashing such as Argon2id, bcrypt, or scrypt and should not depend on frontend localStorage authentication.

### 3.7 Redirect safety

A login redirect should not blindly accept arbitrary query-string values.

**Fix:** `getSafeRedirect()` accepts internal paths beginning with one `/` and rejects values such as `//external.example`.

### 3.8 Duplicate submissions

Authentication is asynchronous after hashing is introduced.

**Fix:** Login and Register use `isSubmitting` to prevent repeated submissions and disable the submit button while the operation is active.

### 3.9 Route loading

The three views use dynamic imports in the router.

**Fix:** route-level lazy loading was retained as an explicit performance optimization.

## 4. Testing matrix

| Test | Expected result |
|---|---|
| Empty registration | Required validation messages |
| Invalid email | Email validation message |
| Short password | Password-length message |
| Empty confirmation | Confirmation-required message |
| Mismatched confirmation | Password mismatch message |
| Duplicate email | Registration rejected |
| Valid registration | User saved and Login displayed |
| Correct login | Dashboard displayed |
| Wrong password | Generic authentication error |
| Dashboard while logged out | Redirect to Login |
| Refresh while logged in | Session restored |
| Logout | Session removed |
| Corrupt storage | Invalid data ignored safely |
| Storage failure | Application-level error |
| Unsafe redirect | Safe fallback used |
| Repeated submit clicks | Duplicate operation prevented |

## 5. Performance methodology

The assignment requests before-and-after browser DevTools measurements. Since those measurements are machine- and browser-dependent, this project intentionally does not invent numerical results.

Use the procedure in `PERFORMANCE_REPORT.md` to collect identical baseline and final measurements. This keeps the report reproducible and evidence-based.

## 6. Maintainability improvements

The final source separates responsibilities:

- `components/` – reusable presentation components
- `views/` – page-level screens
- `stores/` – authentication state and business logic
- `utils/validators.js` – validation rules
- `utils/storage.js` – persistence abstraction
- `utils/crypto.js` – password digest helper
- `utils/redirect.js` – redirect validation
- `router/` – navigation and access control

This structure makes future changes easier because each concern has a clear location.

## 7. Security note

This remains a frontend educational demonstration. A SHA-256 digest is not a replacement for a production password-hashing system. Real applications should authenticate against a backend, hash passwords using a password-specific algorithm on a trusted server, use HTTPS, and implement appropriate session protection.

## 8. Conclusion

The Week 3 revision transforms the working Week 2 authentication module into a more modular and resilient implementation. The work addresses validation duplication, reusable components, persistence failures, invalid persisted state, redirect safety, repeated submissions, and route-level code splitting.

The final project and documentation are organized for submission as a single ZIP archive.
