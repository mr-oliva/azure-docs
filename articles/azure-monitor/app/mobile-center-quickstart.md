---
title: Monitor mobile or Universal Windows Platform apps with Azure Monitor Application Insights
description: This tutorial provides instructions to quickly set up a mobile or Universal Windows Platform app for monitoring with Azure Monitor Application Insights and App Center.
ms.topic: tutorial
ms.date: 11/15/2022
ms.custom: mvc
ms.devlang: java, swift
---

# Start analyzing your mobile or UWP app with App Center and Application Insights

This tutorial guides you through connecting your app's App Center instance to Application Insights. With Application Insights, you can query, segment, filter, and analyze your telemetry with more powerful tools than are available from the [Analytics](/mobile-center/analytics/) service of App Center.

In this tutorial, you learn how to:

> [!div class="checklist"]
> * Connect an app's App Center instance to Application Insights.
> * Modify your app to send custom telemetry to Application Insights.
> * Query custom telemetry in Log Analytics.
> * Analyze conversion, retention, and navigation patterns in your app.

## Prerequisites

To complete this tutorial, you need:

- An Azure subscription.
- An iOS, Android, Xamarin, Universal Windows Platform (UWP), or React Native app.

If you don't have an Azure subscription, create a [free](https://azure.microsoft.com/free/) account before you begin.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-instrumentation-key-deprecation.md)]

## Sign up with App Center
To begin, create an account and [sign up with App Center](https://appcenter.ms/signup?utm_source=ApplicationInsights&utm_medium=Azure&utm_campaign=docs).

## Onboard to App Center

Before you can use Application Insights with your mobile app, you need to onboard your app to [App Center](/mobile-center/). Application Insights doesn't receive telemetry from your mobile app directly. Instead, your app sends custom event telemetry to App Center. Then, App Center continuously exports copies of these custom events into Application Insights as the events are received. (This description doesn't apply to the [Application Insights JS SDK](https://github.com/Microsoft/ApplicationInsights-JS) or the [React Native plug-in](https://github.com/Microsoft/ApplicationInsights-JS/tree/master/extensions/applicationinsights-react-native) where telemetry is sent directly to Application Insights.)

To onboard your app, follow the App Center quickstart for each platform your app supports. Create separate App Center instances for each platform:

* [iOS](/mobile-center/sdk/getting-started/ios)
* [Android](/mobile-center/sdk/getting-started/android)
* [Xamarin](/mobile-center/sdk/getting-started/xamarin)
* [Universal Windows](/mobile-center/sdk/getting-started/uwp)
* [React Native](/mobile-center/sdk/getting-started/react-native)

## Track events in your app

After your app is onboarded to App Center, it needs to be modified to send custom event telemetry by using the App Center SDK.

To send custom events from iOS apps, use the `trackEvent` or `trackEvent:withProperties` methods in the App Center SDK. Learn more about [tracking events from iOS apps](/mobile-center/sdk/analytics/ios).

```Swift
MSAnalytics.trackEvent("Video clicked")
```

To send custom events from Android apps, use the `trackEvent` method in the App Center SDK. Learn more about [tracking events from Android apps](/mobile-center/sdk/analytics/android).

```Java
Analytics.trackEvent("Video clicked")
```

To send custom events from other app platforms, use the `trackEvent` methods in their App Center SDKs.

To make sure your custom events are being received, go to the **Events** tab under the **Analytics** section in App Center. It can take a couple minutes for events to show up after they're sent from your app.

## Create an Application Insights resource

After your app sends custom events and these events are received by App Center, you need to create an App Center-type Application Insights resource in the Azure portal:

1. Sign in to the [Azure portal](https://portal.azure.com/).
1. Select **Create a resource** > **Developer tools** > **Application Insights**.

    > [!NOTE]
    > If this is your first time creating an Application Insights resource, you can learn more by reading [Create an Application Insights resource](../app/create-new-resource.md).

    A configuration box appears. Use the following table to fill out the input fields.

    | Settings        |  Value           | Description  |
   | ------------- |:-------------|:-----|
   | Name     | Some globally unique value, like "myApp-iOS" | Name that identifies the app you're monitoring. |
     | Resource group     | A new resource group, or an existing one from the menu | The resource group in which to create the new Application Insights resource. |
   | Location | A location from the menu | Choose a location near you, or near where your app is hosted. |

1. Select **Create**.

If your app supports multiple platforms like iOS and Android, it's best to create separate Application Insights resources. Create one for each platform.

## Export to Application Insights

In your new Application Insights resource on the **Overview** page, copy the instrumentation key from your resource.

In the [App Center](https://appcenter.ms/) instance for your app:

1. On the **Settings** page, select **Export**.
1. Select **New Export** > **Application Insights** > **Customize**.
1. Paste your Application Insights instrumentation key into the box.
1. Consent to increasing the usage of the Azure subscription that contains your Application Insights resource. Each Application Insights resource is free for the first 1 GB of data received per month. Learn more about [Application Insights pricing](https://azure.microsoft.com/pricing/details/application-insights/).

Remember to repeat this process for each platform your app supports.

After [export](/mobile-center/analytics/export) is set up, each custom event received by App Center is copied into Application Insights. It can take several minutes for events to reach Application Insights, so if they don't show up immediately, wait a few minutes before you diagnose further.

To give you more data when you first connect, the most recent 48 hours of custom events in App Center are automatically exported to Application Insights.

## Start monitoring your app

Application Insights can query, segment, filter, and analyze the custom event telemetry from your apps, beyond the analytics tools that App Center provides.

### Query your custom event telemetry

1. On the Application Insights **Overview** page, select **Logs**.

   The Application Insights Logs portal associated with your Application Insights resource will open. The Logs portal lets you directly query your data by using the Log Analytics query language, so you can ask arbitrarily complex questions about your app and its users.

1. Open a new tab in the Logs portal and paste in the following query. It returns a count of how many distinct users have sent each custom event from your app in the last 24 hours, sorted by these distinct counts.

   ```AIQL
   customEvents
   | where timestamp >= ago(24h)
   | summarize dcount(user_Id) by name 
   | order by dcount_user_Id desc 
   ```

   ![Screenshot that shows the Logs portal.](./media/mobile-center-quickstart/analytics-portal-001.png)

   1. Select the query by clicking anywhere on the query in the text editor.
   1. Then select **Run** to run the query.

   Learn more about [Application Insights Analytics](../logs/log-query-overview.md) and the [Log Analytics query language](/azure/data-explorer/kusto/query/).

### Segment and filter your custom event telemetry

On the Application Insights **Overview** page, select **Users** in the table of contents.

   ![Screenshot that shows the Users tool icon.](./media/mobile-center-quickstart/users-icon-001.png)

   The Users tool shows how many users of your app clicked certain buttons, visited certain screens, or performed any other action that you're tracking as an event with the App Center SDK. If you've been looking for a way to segment and filter your App Center events, the Users tool is a great choice.

   ![Screenshot that shows the Users tool.](./media/mobile-center-quickstart/users-001.png)

   For example, segment your usage by geography by selecting **Country or region** in the **Split by** dropdown box.

### Analyze conversion, retention, and navigation patterns in your app

On the Application Insights **Overview** page, select **User Flows** in the table of contents.

   ![Screenshot that shows the User Flows tool.](./media/mobile-center-quickstart/user-flows-001.png)

   The User Flows tool visualizes which events users send after some starting event. It's useful for getting an overall picture of how users navigate through your app. It can also reveal places where many users are churning from your app or repeating the same actions over and over.

   In addition to User Flows, Application Insights has several other user behavior analytics tools to answer specific questions:

   * **Funnels**: Analyze and monitor conversion rates.
   * **Retention**: Analyze how well your app retains users over time.
   * **Workbooks**: Combine visualizations and text into a shareable report.
   * **Cohorts**: Name and save specific groups of users or events so they can be easily referenced from other analytics tools.

## Clean up resources

If you don't want to continue using Application Insights with App Center, turn off export in App Center and delete the Application Insights resource. This step will prevent you from being charged further by Application Insights for this resource.

To turn off export in App Center:

1. In App Center, go to **Settings** and select **Export**.
1. Select the Application Insights export you want to delete. Then select **Delete export** at the bottom and confirm.

To delete the Application Insights resource:

1. On the left menu in the Azure portal, select **Resource groups**. Then choose the resource group in which your Application Insights resource was created.
1. Open the Application Insights resource you want to delete. Then select **Delete** on the top menu of the resource and confirm. This action permanently deletes the copy of the data that was exported to Application Insights.

## Next steps

> [!div class="nextstepaction"]
> [Understand how customers are using your app](../app/usage-overview.md)
