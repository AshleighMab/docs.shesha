---
sidebar_label: Security Classes and Interfaces
sidebar_position: 5
title: Security Classes and Interfaces
---

# Security Classes and Interfaces

Shesha's security model is built from a small set of classes and interfaces that work together: one manages roles, one checks whether the current user holds a permission, and one ties a permission check to a specific protected endpoint, form, or other object. Understanding how these fit together helps when you need to check permissions from custom code, rather than relying on the built-in `[SheshaAuthorize]` attribute or the Permissions setting in the form designer.

---

## RoleManager

`RoleManager` extends ABP's own `AbpRoleManager<Role, User>` and is responsible for role bookkeeping - creating, renaming, and looking up roles. Two methods are overridden with Shesha-specific behaviour:

- `CheckDuplicateRoleNameAsync(expectedRoleId, name, displayName)` - throws a friendly error if another role already uses the given name or display name.
- `GetRoleByName(roleName)` - looks up a role by name, throwing if none exists.

Everything else (assigning roles to users, granting permissions to a role, and so on) comes from the inherited `AbpRoleManager<Role, User>` and ABP's permission management, not from custom methods on this class.

---

## IShaPermissionChecker

`IShaPermissionChecker` extends ABP's `IPermissionChecker` and is the interface you inject to ask "does the current user have this permission?":

```csharp
public interface IShaPermissionChecker: IPermissionChecker
{
    Task ClearPermissionsCacheForUserAsync(long userId, int? tenantId);
    Task ClearPermissionsCacheAsync();

    Task<bool> IsGrantedAsync(string permissionName, EntityReferenceDto<string> permissionedEntity);
    Task<bool> IsGrantedAsync(long userId, string permissionName, EntityReferenceDto<string> permissionedEntity);
    bool IsGranted(string permissionName, EntityReferenceDto<string> permissionedEntity);
    bool IsGranted(long userId, string permissionName, EntityReferenceDto<string> permissionedEntity);
}
```

`permissionedEntity` lets a permission check be scoped to a specific entity (for example, a permission that only applies within a particular organisation), matching the entity-scoped roles described elsewhere in the security docs. Pass `null` for a plain, unscoped permission check. The inherited `AuthorizeAsync(requireAll, permissionNames)` from `IPermissionChecker` throws if the current user lacks the required permission(s), and is what Shesha's own authorization pipeline calls internally (see below).

---

## IPermissionedObjectManager

Shesha represents every securable endpoint, form, and other protected object as a **Protected Object** (`type@action`, for example `Shesha.Core.Person@Update`), each with its own configured access level and required permissions. `IPermissionedObjectManager` is the service that reads and writes that configuration - `GetOrDefaultAsync`, `GetAllFlatAsync`/`GetAllTreeAsync`, and `SetPermissionsAsync` are the methods you would use to inspect or change a protected object's permissions from code. See [Endpoint Permissions](/docs/fundamentals/security/endpoint-permissions) for how this system works end to end.

---

## ObjectPermissionChecker

`ObjectPermissionChecker` (implementing `IObjectPermissionChecker`) is what actually enforces access to a protected object, combining the two services above: it looks up the object's configured access level and required permissions via `IPermissionedObjectManager`, then calls `IShaPermissionChecker.AuthorizeAsync` with those permissions.

**Example - How a request is authorized internally:**

```csharp
var permission = await _permissionedObjectManager.GetOrDefaultAsync($"{permissionedObject}@{method}", objectType);

// requireAll is false - the user only needs ONE of the listed permissions, not all of them
await _permissionChecker.AuthorizeAsync(false, permission.ActualPermissions.ToArray());
```

This is the same check that runs automatically for every `[SheshaAuthorize]`-protected endpoint - you would only call it directly if you need to authorize something outside that normal request pipeline.

:::note
There is no Shesha-specific `ICurrentUser` interface, `UserManager` with custom role/permission methods, or `ScopedRole` entity in the framework. To get the current user's ID, inject ABP's standard `IAbpSession` (`AbpSession.UserId`); to manage user accounts, use ABP's standard `UserManager<User>`. Permission checks always go through `IShaPermissionChecker` as described above.
:::
