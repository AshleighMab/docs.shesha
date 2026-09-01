---
sidebar_label: User Registration
title: User Registration
---

# User Registration

Shesha gives users a way to create their own account through a public registration form, as an alternative to an administrator creating accounts for them (see [User Management](/docs/for-administrators/user-management) for the admin-created flow). You control whether self-registration is available at all, which method users register with, and whether they need to complete extra information before their account is fully active.

---

## Configuration Options

These settings are managed through Shesha's settings mechanism (`UserManagementSettings`), not a configuration file, so they can be changed without redeploying the application.

### Supported Registration Method

Only one registration method can be active at a time.

| Option | Behaviour |
|---|---|
| **None** | New user registration is disabled entirely. The Register link is hidden from the login page. |
| **Email Address** | Users register with an email address. Email verification is mandatory. |
| **Mobile Number** | Users register with a mobile number. Verification via OTP is mandatory. |

![Disabled](images/registration/no-registration.png)

With either Email Address or Mobile Number selected, the Register link appears on the login page and the user must verify the method they registered with before they can log in.

![Enabled](images/registration/registration.png)
![Mobile](images/registration/mobile-otp.png)

:::note A fourth method exists but isn't part of this flow
The underlying `SupportedRegistrationMethods` list also defines an `OAuth` value, reserved for registering via an external identity provider. This is a separate mechanism from the extension point described in [Custom Authentication Providers](/docs/fundamentals/security/authentication#custom-authentication-providers), and is not yet a selectable option in this registration flow.
:::

### Require Email Verification

When email registration is used, Shesha sends a verification email containing a link that is unique to that registration, expires after a set time, and can only be used once.

![Verification](images/registration/verification-link.png)

### User Email as Username

Controls whether the registered email address is also used as the account's username.

- **False** (default): the user supplies a separate username, and usernames must be unique.
- **True**: the email address itself is used as the username.

### Go to URL After Registration

The URL the user is redirected to once registration completes successfully.

### Additional Registration Information

Lets you require the user to fill in an extra form after the basic registration step, before their account is considered fully registered.

When this is enabled, the new account's registration record is created with `IsComplete = false`. The user must submit the configured form and call `POST /api/services/app/UserManagement/CompleteRegistration` (passing their user ID) to mark it complete. If they log in before completing it, the login endpoint detects the incomplete registration and redirects them back to that form instead of letting them in.

---

## Process Flow

1. The user submits the public registration form (first name, last name, email or mobile number, password, and any other required fields).
2. Shesha creates the `User` and its linked `Person` record, and a `ShaUserRegistration` record tracking whether extra information is still outstanding.
3. If email or mobile verification is required, the user verifies via the emailed link or the OTP before they can log in.
4. If Additional Registration Information is enabled and not yet completed, the user is redirected to that form on their next login attempt instead of being let in.
5. Once verification (and any additional information) is complete, the user is redirected to the configured **Go to URL After Registration**.

---

## Customising the Registration Form

The base registration form only ever collects and saves the fields defined on the DTO behind the registration endpoint (`POST /api/services/app/UserManagement/Create`): username, password, password confirmation, first name, last name, mobile number, email address, type of account, and whether the user must change their password on first login.

You can't add extra fields to this base form and have them saved automatically - the endpoint only maps the properties defined on that DTO. If you need to collect more information at signup, such as a department or a set of terms and conditions, use **Additional Registration Information** above instead. It captures that data through a separate form completed right after the base registration step, rather than extending the base form itself.

---

## Registering a Custom Person Entity

Out of the box, self-registration always creates a base `Person` record. The registration endpoint's `CreateAsync` method is not declared `virtual`, and it's hard-wired to `IRepository<Person, Guid>` - so there's no built-in setting or override interface that lets self-registration create a subclass of `Person` instead.

If your application needs self-registered users to end up as a custom `Person` subclass, this isn't a supported customisation point today. Replacing the built-in `IUserManagementAppService` implementation to change this behaviour would be a much larger change than the other extension points on this page, and isn't officially documented - treat it as an open gap rather than something to attempt without further guidance.
