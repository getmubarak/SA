
# Authentication
* Trust boundaries have been identified, and users are authenticated across trust boundaries.
* Single sign-on is used when there are multiple systems in the application.
* Passwords are stored as a salted hash, not plain text.
* Strong passwords or password phrases are enforced.
* Passwords are not transmitted in plain text.

# Authorization
* Trust boundaries have been identified, and users are authorized across trust boundaries.
* Resources are protected with authorization on identity, group, claims or role.
* Role-based authorization is used for business decisions.
* Resource-based authorization is used for system auditing.
* Claims-based authorization is used for federated authorization based on a mixture of information such as identity, role, permissions, rights, and other factors.

# Auditing
* Are audit logs created by the application or system ensure transactions are date/time stamped along with who made the transaction?
* Does your application logs all security-level important events like successful and failed authentication, data access, modification, network access, etc.? The log should include time of the event, user identity, location with machine name, etc. Identify which events are being logged

# Validation
* Validation is performed both at presentation and business logic layer
* Trust boundaries are identified, and all the inputs are validated when they cross the trust boundary.
* A centralized validation approach is used.
* Validation strategy constrains, rejects, and sanitizes malicious input.
* Input data is validated for length, format, and type.
* Client-side validation is used for user experience and server-side validation is used for security.

# Asset Handling
* In Transit
* In Rest

# Session Management
* Use framework’s default session management: Custom session management can have multiple vulnerabilities. Ensure there is no custom manager is being used and the application framework’s default session management is used.
* Does your application have short session life?
* do you destroy the session identifier after logout?
* do you destroy all active sessions on reset password (or offer to)?

# Configuration Management
* Least-privileged process and service accounts are used.
* All the configurable application information is identified.
* Sensitive information in the configuration is encrypted.
* Access to configuration information is restricted.
* If there is a configuration UI, it is provided as a separate administrative UI.

# Exception Handling


