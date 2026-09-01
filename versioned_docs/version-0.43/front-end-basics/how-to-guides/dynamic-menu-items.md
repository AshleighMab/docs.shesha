---
sidebar_label: Dynamic Menu Items
title: Dynamic Menu Items
---

# Dynamic Menu Items

A Button or Button Group component normally gets its items from a fixed list you configure once in the designer - you add each button by hand, and that list only changes when a configurator edits it again. Dynamic items replace that fixed list with one generated from data instead: for the no-code option described below, one button is generated for every record a data source (a URL endpoint, or a Shesha entity with optional filters) returns when the component loads, so the buttons shown reflect whatever records exist in that data source rather than a hand-typed list.

For example, the tutorial below builds a button group from a list of Enquiry Templates fetched from an API. Each template in the response becomes its own button, and each button's click action is configured once, using that template's data (its `id`) to decide what happens - rather than a configurator having to add and wire up one button per template by hand, and again every time a template is added or removed.

Shesha supports this two ways: a no-code option built into the Button/Button Group item type, and a fully custom code-based option for anything the built-in option can't express.

---

## No-Code Approach

Set an item's **Item Type** to **Dynamic item(s)** on the Button or Button Group configurator.

![alt text](images/DynamicMenu1.png)

Then choose a data source:

- **URL** - fetch data from a custom API endpoint.
- **Entity** - fetch data from a specified entity, with optional filters.

![alt text](images/DynamicMenu2.png)

### URL Data Source

- **Custom Endpoint** - the endpoint to fetch data from.
- **Query Param** - add key/value query parameters for the request.
- **Label Property** / **Tooltip Property** - which field on each returned record to use as the button's label and tooltip.
- **Button Type** - one of `primary`, `ghost`, `dashed`, `link`, `text`, or `default`.
- **Action configuration** - what happens when a generated button is clicked.

![alt text](images/url.png)

Once configured, one button is generated per record returned by the endpoint:

![alt text](images/view.png)

Displaying the items in a Button Group instead of individual buttons renders them as a dropdown:

![alt text](images/group1.png)

![alt text](images/groupResult.png)

:::tip Accessing the clicked item's data
Inside a generated button's action configuration, the record it was generated from is available as `dynamicItem`. Log it (for example in a Navigate action's arguments, via a script) to see its shape.
:::

![alt text](images/dynamicItem.png)

![alt text](images/dynamicItemData.png)

### Entity Data Source

- **Entity Type** - the entity to fetch records from.
- **Filters** - optional filters to narrow down which records generate buttons.

![alt text](images/filters.png)

- **Label Property** / **Tooltip Property**, **Button Type**, and **Action configuration** work the same way as the URL data source above.

---

## Code-Based Approach

For cases the no-code option can't express, you can register your own dynamic items evaluator using `DynamicActionsProvider` and a `DynamicItemsEvaluationHook`, both exported from `@shesha-io/reactjs`. The example below builds a set of buttons from a list of templates fetched from a custom API, each starting an "Enquiry" workflow from a different template.

This requires the `swr` package: `npm i swr@2.1.5` in the project root.

In `adminportal/src/components/templates`, create `model.ts`:

```typescript
export interface TemplateDefinition {
  id: string;
  name: string;
}

export interface Template {
  items: TemplateDefinition[];
}
```

And `fetchers.ts`:

```typescript
export const URLS = {
  GET_ALL_TEMPLATES: `/api/dynamic/Shesha.Tutorial/Template/GetAll`,
};

export function GetMember() {
  const queryString = window.location.search;
  const urlParams = new URLSearchParams(queryString);
  return urlParams.get("id");
}
```

And `hooks.ts`:

```typescript
import { IAjaxResponse, useSheshaApplication } from "@shesha-io/reactjs";
import { Template, TemplateDefinition } from "./model";
import axios from "axios";
import { URLS } from "./fetchers";
import useSWR from "swr";

export const useTemplates = () => {
  const { backendUrl, httpHeaders } = useSheshaApplication();

  const fetcher = async () => {
    try {
      const res = await axios.get<IAjaxResponse<Template>>(URLS.GET_ALL_TEMPLATES, {
        baseURL: backendUrl,
        headers: httpHeaders,
      });
      return res.data.result.items;
    } catch (error) {
      console.error(error);
      throw error;
    }
  };

  return useSWR([URLS.GET_ALL_TEMPLATES, httpHeaders], fetcher, {
    refreshInterval: 0,
    revalidateOnFocus: false,
  });
};
```

In `adminportal/utils`, create `configurableActions.ts` with helpers for building action configurations:

```typescript
import {
  IConfigurableActionConfiguration,
  INavigateActoinArguments,
} from "@shesha-io/reactjs";

const ACTION_CONFIG_TYPE = "action-config";

export const makeActionConfig = <TArgs = any>(
  props: Pick<
    IConfigurableActionConfiguration<TArgs>,
    "actionName" | "actionOwner" | "actionArguments" | "onSuccess" | "onFail"
  >
): IConfigurableActionConfiguration<TArgs> => ({
  _type: ACTION_CONFIG_TYPE,
  actionName: props.actionName,
  actionOwner: props.actionOwner,
  actionArguments: props.actionArguments,
  handleSuccess: Boolean(props.onSuccess),
  onSuccess: props.onSuccess,
  handleFail: Boolean(props.onFail),
  onFail: props.onFail,
});

export const makeNavigateActionConfig = (
  props: Pick<
    IConfigurableActionConfiguration<INavigateActoinArguments>,
    "actionArguments" | "onSuccess" | "onFail"
  >
): IConfigurableActionConfiguration<INavigateActoinArguments> => ({
  _type: ACTION_CONFIG_TYPE,
  actionName: "Navigate",
  actionOwner: "shesha.common",
  actionArguments: props.actionArguments,
  handleSuccess: Boolean(props.onSuccess),
  onSuccess: props.onSuccess,
  handleFail: Boolean(props.onFail),
  onFail: props.onFail,
  version: 2,
});
```

In `adminportal/src/components/templates/dynamic-templates`, create `enquiry-initiate.tsx` - the evaluator hook that turns each template into a button, plus the provider that registers it:

```typescript
import React, { PropsWithChildren, useMemo } from "react";
import { FC } from "react";
import {
  DynamicActionsProvider,
  ButtonGroupItemProps,
  DynamicItemsEvaluationHook,
  useAppConfigurator,
  IButtonItem,
} from "@shesha-io/reactjs";
import { useTemplates } from "../hooks";
import {
  makeActionConfig,
  makeNavigateActionConfig,
} from "utils/configurableActions";
import { GetMember } from "../fetchers";

export interface IWorkflowInstanceStartActionsProps {}

const useEnquiryInitiateActions: DynamicItemsEvaluationHook = (args) => {
  const { data, isLoading, error } = useTemplates();
  const { configurationItemMode } = useAppConfigurator();

  const operations = useMemo<ButtonGroupItemProps[]>(() => {
    if (!data || isLoading || error) return [];

    return data.map<IButtonItem>((p) => ({
      id: p.id,
      name: p.name,
      label: p.name,
      itemType: "item",
      itemSubType: "button",
      sortOrder: 0,
      actionConfiguration: makeActionConfig({
        actionName: "API Call",
        actionOwner: "shesha.common",
        actionArguments: {
          verb: "post",
          url: `/api/dynamic/Shesha.Tutorial/Enquiry/Create`,
          parameters: [
            { id: "CBc2Xng3M682kZwGhAwHt", key: "member", value: GetMember() },
            { id: "AKc2Xng3M682kZwGhAwHt", key: "template", value: p.id },
          ],
          sendStandardHeaders: true,
        },
        onSuccess: makeNavigateActionConfig({
          actionArguments: {
            navigationType: "url",
            url: "/dynamic/ShaCompanyName.ShaProjectName/member-enquiry",
            queryParameters: [
              { key: "id", value: "{{actionResponse.id}}" },
              { key: "mode", value: "edit" },
            ],
          },
        }),
        onFail: makeActionConfig({
          actionName: "Show Confirmation Dialog",
          actionOwner: "shesha.common",
          actionArguments: { title: "Error", content: "Failed to initiate enquiry!", okText: "", cancelText: "" },
        }),
      }),
    }));
  }, [args.item, data, configurationItemMode]);

  return operations;
};

export const EnquiryInitiateActions: FC<PropsWithChildren<IWorkflowInstanceStartActionsProps>> = ({ children }) => (
  <DynamicActionsProvider id="enquiry-initiate" name="Enquiry Initiate" useEvaluator={useEnquiryInitiateActions}>
    {children}
  </DynamicActionsProvider>
);
```

Then, in the same directory, create an `index.tsx` to combine this provider with any others of the same scope:

```typescript
import React, { FC, PropsWithChildren } from "react";
import { EnquiryInitiateActions } from "./enquiry-initiate";

export interface IEnquiryActionsProviderProps {}

export const EnquiryActionsProvider: FC<PropsWithChildren<IEnquiryActionsProviderProps>> = ({ children }) => {
  return <EnquiryInitiateActions>{children}</EnquiryInitiateActions>;
};
```

Finally, mount your provider inside the app's provider tree, `adminportal/src/app/app-provider.tsx`:

```typescript
"use client";

import React, { FC, PropsWithChildren } from "react";
import {
  GlobalStateProvider,
  ShaApplicationProvider,
  StoredFilesProvider,
  MainLayout,
  useNextRouter,
} from "@shesha-io/reactjs";
import { AppProgressBar } from "next-nprogress-bar";
import { useTheme } from "antd-style";
import { EnquiryActionsProvider } from "@/components/templates/dynamic-templates";

export interface IAppProviderProps {
  backendUrl: string;
}

export const AppProvider: FC<PropsWithChildren<IAppProviderProps>> = ({ children, backendUrl }) => {
  const nextRouter = useNextRouter();
  const theme = useTheme();

  return (
    <GlobalStateProvider>
      <AppProgressBar height="4px" color={theme.colorPrimary} shallowRouting />
      <ShaApplicationProvider backendUrl={backendUrl} router={nextRouter} noAuth={false}>
        <EnquiryActionsProvider>
          <StoredFilesProvider baseUrl={backendUrl} ownerId={""} ownerType={""}>
            <MainLayout noPadding>{children}</MainLayout>
          </StoredFilesProvider>
        </EnquiryActionsProvider>
      </ShaApplicationProvider>
    </GlobalStateProvider>
  );
};
```

With the provider registered, set the item's **Item Type** to **Dynamic item(s)** on the Button Group configurator to use it:

![Image](./images/dynamic-menu.png)
![Image](./images/button-group.png)

:::note
This example redirects the user to a `member-enquiry` form after a template is selected, which then renders differently depending on which template was chosen - for example a Renewal, Assistance, Cancellation, or Feedback layout. That form-level behavior is specific to this tutorial's own forms, not something the framework provides directly.
:::
