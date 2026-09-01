---
sidebar_position: 10
sidebar_label: Permission Based Security Model
title: Permission Based Security Model
---

# Permission Based Security Model

Shesha adopts a permission-based model, meaning users can only perform restricted actions if they have been granted that specific permission. Think of it like having different keys for different doors in a building - you can only enter the rooms you are authorized to access.

For the underlying concepts (roles, permissions, scoped roles) and how permissions are enforced in code at the UI, API, and data levels, see [Authorization and Access Control](/docs/fundamentals/security/access-control). This article is a walkthrough of the concrete steps: creating a permission and assigning it to a role through the Configuration Studio.

:::note
This walkthrough assumes you have a Shesha project up and running. If you have not set one up yet, see [Getting Started](/docs/get-started/setting-up/).
:::

---

## Creating a Permission

From the homepage, navigate to the **Permissions Configurator** by clicking the **Permissions** button.

![Image](./images/permission-based-security-images/permission1.png)

On the **Permission Configurator** page, create a new permission by clicking **Create root**.

![Image](./images/permission-based-security-images/permission2.png)

This opens a modal where you specify:

1. The **Module** the permission belongs to.
2. The **Name** of the permission.
3. The **Display Name** of the permission, shown alongside other permissions in the list.
4. A **Description** of what the permission does.

![Image](./images/permission-based-security-images/permission3.png)

Click **Save** to create the permission. It now appears in the permissions list.

![Image](./images/permission-based-security-images/permission4.png)

You can also define a permission without a module.

![Image](./images/permission-based-security-images/permission5.png)

A permission saved without a module appears under the **no-module** section of the list.

![Image](./images/permission-based-security-images/permission6.png)

---

## Assigning a Permission to a Role

Next, assign the newly created permission to a role. Open the Roles modal by clicking **Roles**.

![Image](./images/permission-based-security-images/permission7.png)

A new Shesha application starts with a **System Administrator** role available. Click its magnifier icon to open it.

![Image](./images/permission-based-security-images/permission8.png)

Click **Edit**.

![Image](./images/permission-based-security-images/permission9.png)

Select the checkbox next to the newly created permission and click **Save**.

![Image](./images/permission-based-security-images/permission10.png)

---

## Restricting a Menu Item to the New Permission

To make an existing menu item, such as an administration menu group, visible only to users whose role has this permission, switch the application into **Edit Mode** using the **Live Mode** / **Edit Mode** toggle in the header. See [Toggling Edit Mode](/docs/front-end-basics/configured-views/toggling-edit-mode) for how this toggle works.

![Image](./images/permission-based-security-images/permission11.png)

Once in Edit Mode, the top menu bar changes and a notification confirms the application has switched to Edit Mode.

![Image](./images/permission-based-security-images/permission12.png)

While in Edit Mode, click **Permission** in the sidebar again to bring up the Edit Mode permission modal.

![Image](./images/permission-based-security-images/permission13.png)

Add the newly created permission to the menu item's permission list.

![Image](./images/permission-based-security-images/permission14.png)

:::note
Assigning permissions this way also applies to individual form components, not just menu items and forms.
:::

Only users whose role is granted this permission will now see the menu item.
