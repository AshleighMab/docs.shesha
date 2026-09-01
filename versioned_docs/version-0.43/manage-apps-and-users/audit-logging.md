---
sidebar_label: Audit Logging
title: Audit Logging
---

# Audit Logging

Shesha's audit trail is built on top of [Abp.EntityHistory](https://aspnetboilerplate.com/Pages/Documents/Entity-History), with a number of Shesha-specific additions layered on top: a ready-made DataTable view of an entity's history, extension methods for logging custom events, and attributes that control how property changes and related-entity changes are described.

---

## Audit Trail DataTable Configuration

Implemented in the `Shesha.Application` module's `EntityHistoryAppService` class (`GetAuditTrailAsync`). Use it to show the audit trail for a specific entity in a DataTable control.

### Columns

| Column | Description |
|---|---|
| **Type of event** | Type of audited event (Created, Updated, Child object updated, child object added, etc.) or changes of properties |
| **Description** | Main description of the event - the changed properties, the name of an added or removed child entity, or a custom event description. If several properties changed at once, all the changes are shown as a single description |
| **User** | Full name of the user who made the change or triggered the event |
| **Date** | Date of the event |

---

## Entity History for Anonymous Users (e.g. a Background Job)

By default, changes made by anonymous users (such as a background job with no logged-in user) are not recorded. Add the following to `PostInitialize` in `SheshaWebHostModule.cs` (in the `Shesha.Web.Host` module's Startup folder) to enable it:

```cs
public override void PostInitialize()
{
    Configuration.EntityHistory.IsEnabledForAnonymousUsers = true;
}
```

---

## Adding Custom Audit Events

These extension methods, defined in `Shesha.NHibernate.EntityHistory.EntityHistoryExtension`, let you add any custom event to an entity's audit trail.

### A Simple Event With a Description

```cs
public static void AddHistoryEvent(this object entity, string description)
```

**Example:**

```cs
user.Password = newPassword;
user.AddHistoryEvent("Password reset");
```

___

### An Event With a Type and a Description

```cs
public static void AddHistoryEvent(this object entity, string eventName, string description)
```

**Example:**

```cs
user.Password = newPassword;
user.AddHistoryEvent("Password reset", "Password reset by Administrator");
```

___

### A Custom Property Change Description

Use a custom description for a specific property change instead of the standard description.

```cs
public static void AddPropertyChangeDescription(this object entity, string description, string propertyName)
// or
public static void AddPropertyChangeDescription<TModel, TValue>(this TModel entity, string description, Expression<Func<TModel, TValue>> property)
```

**Example:**

```cs
user.IsActive = false;
user.AddPropertyChangeDescription("User inactivated", "IsActive");
// or
user.IsActive = false;
user.AddPropertyChangeDescription("User inactivated", p => p.IsActive);
```

This change is shown in the audit trail as `User inactivated`, instead of `"IsActive" was changed from "true" to "false"`.

___

### A Property Change Comment

A custom comment for a specific property change, added after the standard description rather than replacing it.

```cs
public static void AddPropertyChangeComment(this object entity, string description, string propertyName)
// or
public static void AddPropertyChangeComment<TModel, TValue>(this TModel entity, string description, Expression<Func<TModel, TValue>> property)
```

**Example:**

```cs
user.IsActive = false;
user.AddPropertyChangeComment("User inactivated", "IsActive");
// or
user.AddPropertyChangeComment("User inactivated", p => p.IsActive);
```

This change is shown in the audit trail as `"IsActive" was changed from "true" to "false" (User inactivated)`.

---

## Property-Level Audit Attributes

### Audited Boolean Values

Show a custom message instead of the standard property-change description when a boolean property changes.

```cs
[AttributeUsage(AttributeTargets.Property)]
public class AuditedBooleanAttribute : Attribute
{
    public string EventText { get; set; }
    public string TrueText { get; set; }
    public string FalseText { get; set; }

    public AuditedBooleanAttribute(string trueText, string falseText)
}
```

**Example:**

```cs
[AuditedBoolean("SMS Based One-Time-Passwords enabled", "SMS Based One-Time-Passwords disabled")]
public virtual bool OtpEnabled { get; set; }
```

This is shown in the audit trail as `SMS Based One-Time-Passwords enabled` (instead of `"OtpEnabled" was changed from "false" to "true"`), or as `SMS Based One-Time-Passwords disabled` for the reverse change.

___

### Audit a Property Change as a Custom Event

Show a custom description and event type, built by a class that inherits from `EntityHistoryEventCreatorBase`.

```cs
[AttributeUsage(AttributeTargets.Property)]
public class AuditedAsEventAttribute : Attribute
{
    public Type EventCreator { get; set; }
    public bool SaveFullInfo { get; set; }

    public AuditedAsEventAttribute(Type eventCreator, bool saveFullInfo = true)
}
```

**Example:**

```cs
private class SchoolInformationStatusEventCreator : EntityHistoryEventCreatorBase<RefListSchoolInformationStatus>
{
    public override EntityHistoryEventInfo CreateEvent(EntityChangesInfo<RefListSchoolInformationStatus> change)
    {
        return CreateEvent("School information status changed",
            change.NewValue == RefListSchoolInformationStatus.Submitted
                ? "Submitted for verification"
                : change.NewValue == RefListSchoolInformationStatus.Approved
                    ? "Submitted as final"
                    : "Not submitted");
    }
}

[AuditedAsEvent(typeof(SchoolInformationStatusEventCreator))]
public virtual RefListSchoolInformationStatus? SchoolInformationStatus { get; set; }
```

This is shown in the audit trail as `School information status changed: Not submitted`, `School information status changed: Submitted for verification`, or `School information status changed: Submitted as final`, instead of the raw `"SchoolInformationStatus" was changed from "X" to "Y"`.

---

## Including Related and Child Entity Audit Trails

These class-level attributes pull audit events from related or child entities into the audit trail of the entity being viewed.

### Events From a Related Entity

```cs
[AttributeUsage(AttributeTargets.Property)]
public class DisplayChildAuditTrailAttribute : Attribute
{
    public string[] AuditedFields { get; set; }
}
```

`AuditedFields` is the array of child-entity field names to display. If provided, you also need to specify which action types (`Created`, `Updated`, `Deleted`) should be shown.

**Example:**

```cs
[DisplayChildAuditTrail]
[Display(Name = "Parent")]
public virtual Parent SubmittedBy { get; set; }
```

Shows changes to the related `Parent` entity, reached through the `SubmittedBy` field.

___

### Events From Generic Child Entities (e.g. Notes, Attachments, Notifications)

Generic entities typically implement `IEntityWithMultipleOwnerTypes`.

:::note Watch the spelling
This attribute is spelled `DisplayGenericEntitesAuditTrailAttribute` - missing the "i" in "Entites" - not the more natural-looking `DisplayGenericEntitiesAuditTrailAttribute`. Your code needs to match this exact spelling, as shown below, or it won't compile.
:::

```cs
[AttributeUsage(AttributeTargets.Class, AllowMultiple = true)]
public class DisplayGenericEntitesAuditTrailAttribute : Attribute
{
    public Type EntityType { get; set; }
    public string OwnerIdField { get; set; }
    public string OwnerTypeField { get; set; }
    public string DisplayName { get; set; }
    public string NameField { get; set; }
    public string CategoryField { get; set; }
    public object CategoryValue { get; set; }

    public DisplayGenericEntitesAuditTrailAttribute(Type entityType)
}
```

- **EntityType** - type of the related generic entity (e.g. `StoredFile`, `Note`, `Comment`)
- **OwnerIdField** - field on the generic entity holding the owner's ID. Defaults to `OwnerId`
- **OwnerTypeField** - field on the generic entity holding the owner's type. Defaults to `OwnerType`
- **DisplayName** - the type name shown in the audit trail. If empty, uses `EntityAttribute()?.FriendlyName` of `EntityType`, or a friendly name derived from the class name
- **NameField** - field on the generic entity used as its display name. If empty, looks for a field with `EntityDisplayNameAttribute`, or falls back to `.ToString()`
- **CategoryField** / **CategoryValue** - optionally restrict this to generic entities where `CategoryField` equals `CategoryValue` (for example, showing only `Note` entities of a specific category)

**Example:**

```cs
[DisplayGenericEntitesAuditTrail(typeof(Comment), NameField = "Text")]
public class SchoolApplication : FullAuditedEntity<Guid>
```

Shows events from related `Comment` entities, using the comment's `Text` field as the event's description.

___

### Events From Many-to-Many Related Entities

The join entity must implement `IFullAudited`.

```cs
[AttributeUsage(AttributeTargets.Class, AllowMultiple = true)]
public class DisplayManyToManyAuditTrailAttribute : Attribute
{
    public Type ManyToManyEntityType { get; set; }
    public string DisplayName { get; set; }
    public string OwnEntityField { get; set; }
    public string RelatedEntityField { get; set; }
    public Type RelatedEntityType { get; set; }
    public bool AnyRelatedEntityType { get; set; }
    public string NameField { get; set; }
    public string[] AuditedFields { get; set; }

    public DisplayManyToManyAuditTrailAttribute(Type manyToManyEntityType, string relatedEntityField)
}
```

- **ManyToManyEntityType** - the join entity's type
- **DisplayName** - defaults to the join entity type's friendly name
- **OwnEntityField** - field on the join entity referencing this entity. If empty, Shesha looks for a field of this entity's own type
- **RelatedEntityField** - field on the join entity referencing the related entity
- **RelatedEntityType** - the related entity's type (optional, useful when the join entity's related type is itself a base type)
- **AnyRelatedEntityType** - when true, matches related entities of any type reachable through `RelatedEntityField` (useful for an inherited hierarchy, e.g. `Employee` extends `Person`)
- **NameField** - field on the related entity used as its display name
- **AuditedFields** - as above, the array of child-entity fields to display alongside the required action types

**Example:**

```cs
public class ShaRoleAppointedPerson : FullAuditedEntity<Guid>
{
    public virtual Person Person { get; set; }
    public virtual ShaRole Role { get; set; }
}

[DisplayManyToManyAuditTrail(typeof(ShaRoleAppointedPerson), "Person", DisplayName = "Member")]
public class ShaRole : FullAuditedEntity<Guid>, IMayHaveTenant
```

Shows events from related `Person` entities as `Member` changes, using `ShaRoleAppointedPerson` as the join entity - for example, `"Member" added: <Full Name of person>`.

The reverse relationship can be declared on the other side too:

```cs
[DisplayManyToManyAuditTrail(typeof(ShaRoleAppointedPerson), "Role", DisplayName = "Role Appointment")]
public class Person : FullAuditedEntity<Guid>, IMayHaveTenant
```

___

### Events From Many-to-One Related Entities

```cs
[AttributeUsage(AttributeTargets.Class, AllowMultiple = true)]
public class DisplayManyToOneAuditTrailAttribute : Attribute
{
    public Type ManyToOneEntityType { get; set; }
    public string DisplayName { get; set; }
    public string RelatedEntityField { get; set; }
    public string NameField { get; set; }
    public string[] AuditedFields { get; set; }

    public DisplayManyToOneAuditTrailAttribute(Type manyToOneEntityType)
}
```

**Example:**

```cs
[DisplayManyToOneAuditTrail(typeof(DepartmentUser), DisplayName = "School user")]
public class School : Facility { }

public class DepartmentUser : GDEPerson
{
    [Audited]
    public virtual School School { get; set; }
}
```

Shows events from related `DepartmentUser` entities as `School user` changes, e.g. `"School user" added: <Full Name of department user>`.

___

### Ending the Audit Trail at a Specific Value

Stop showing further audit records once a property reaches a given value.

```cs
[AttributeUsage(AttributeTargets.Class, AllowMultiple = true)]
public class PropertyChangeToStopAuditTrailAttribute : Attribute
{
    public string PropertyName { get; set; }
    public string PropertyValue { get; set; }

    public PropertyChangeToStopAuditTrailAttribute(string propertyName, string propertyValue)
}
```

**Example:**

```cs
[PropertyChangeToStopAuditTrail("SchoolVerificationOutcome", "Deleted By Parent")]
public class SchoolApplication : FullAuditedEntity<Guid>
```

Once `SchoolVerificationOutcome` changes to `Deleted By Parent`, any audit records added after that point are not displayed for this entity.

---

## Adding History Events Directly Via SQL

If you make direct database changes outside the application, you can add matching entity history events with stored procedures instead.

### Adding a List of Changes

```sql
exec [dbo].[Core_AddEntityHistoryEvents] @changeTime, @reason, @tenantId, @userId, @changes
```

| Parameter | Description |
|---|---|
| `@changeTime` | Date and time of the changes. `Null` is replaced with the current time |
| `@reason` | Reason for the changes |
| `@tenantId` | Tenant ID |
| `@userId` | User ID |
| `@changes` | A table-valued parameter of type `Core_EntityHistoryItem` listing the changes |

```sql
CREATE TYPE Core_EntityHistoryItem AS TABLE
(
    ChangeType tinyint,              -- 0 = Created, 1 = Updated, 2 = Deleted
    EntityId nvarchar(48),
    EntityTypeFullName nvarchar(192), -- fully qualified name, namespace included, assembly excluded

    PropertyName nvarchar(96),
    PropertyTypeFullName nvarchar(256),
    NewValue nvarchar(512),
    OldValue nvarchar(512),

    Description nvarchar(512)        -- optional
)
```

**Example - unlock all locked users, with a property change recorded:**

```sql
Declare @changes Core_EntityHistoryItem

insert into @changes
select 1 /* Updated */, Id, 'Shesha.Authorization.Users.User', 'IsLocked', 'System.Boolean', 'false', 'true', 'User unlocked'
from Core_Persons
where IsLocked = 1

exec [dbo].[Core_AddEntityHistoryEvents] null /* current time */, 'Support ticket #12345: Unlock all users', null, 1 /* Admin user ID */, @changes
```

This shows as a `User updated` event with the description `'IsLocked' was changed from 'true' to 'false'` (or, since a description was supplied, `User unlocked`).

**Example - the same change without property data, recorded as a plain event:**

```sql
Declare @changes Core_EntityHistoryItem

insert into @changes
select 1 /* Updated */, Id, 'Shesha.Authorization.Users.User', null, null, null, null, 'User unlocked'
from Core_Persons
where IsLocked = 1

exec [dbo].[Core_AddEntityHistoryEvents] null, 'Support ticket #12345: Unlock all users', null, 1, @changes
```

This shows as a `User unlocked` event with no property-change detail.

___

### Adding a Single Change

```sql
exec [dbo].[Core_AddSingleEntityHistoryEvent] @changeTime, @reason, @tenantId, @userId, @changeType, @entityId, @entityTypeFullName, @propertyName, @propertyTypeFullName, @newValue, @oldValue, @description
```

| Parameter | Description |
|---|---|
| `@changeTime` | Date and time of the change |
| `@reason` | Reason for the change |
| `@tenantId` | Tenant ID |
| `@userId` | User ID |
| `@changeType` | 0 = Created, 1 = Updated, 2 = Deleted |
| `@entityId` | ID of the changed entity |
| `@entityTypeFullName` | Fully qualified entity type name (namespace included, assembly excluded) |
| `@propertyName` | Name of the changed property |
| `@propertyTypeFullName` | Fully qualified property type name |
| `@newValue` | New value |
| `@oldValue` | Old value |
| `@description` | Optional description |

**Example - unlock a specific user, with property change data:**

```sql
exec [dbo].[Core_AddSingleEntityHistoryEvent] null, 'Support ticket #12345: Unlock all users', null, 1, 1, 123456, 'Shesha.Authorization.Users.User', 'IsLocked', 'System.Boolean', 'false', 'true', 'User unlocked'
```

**Example - the same change without property data:**

```sql
exec [dbo].[Core_AddSingleEntityHistoryEvent] null, 'Support ticket #12345: Unlock all users', null, 1, 1, 123456, 'Shesha.Authorization.Users.User', null, null, null, null, 'User unlocked'
```
