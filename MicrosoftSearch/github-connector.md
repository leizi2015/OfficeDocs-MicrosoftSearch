--- 
title: "GitHub Cloud Knowledge Graph connector for Microsoft Search and Copilot" 
ms.author: rerabo
author: vivg
manager: ereza
audience: Admin
ms.audience: Admin 
ms.topic: install-set-up-deploy
ms.service: mssearch 
ms.localizationpriority: medium 
search.appverid: 
- BFB160 
- MET150 
- MOE150 
description: "Set up the GitHub Cloud Knowledge Microsoft 365 Copilot connector." 
ms.date: 05/20/2025
---

# GitHub Cloud Knowledge Microsoft 365 Copilot  connector (Preview)

The GitHub Cloud Knowledge Microsoft 365 Copilot connector allows you to index content from GitHub repositories, making it easily searchable within the Microsoft 365 ecosystem. This connector is ideal for organizations that use GitHub Cloud for documentation, project files, or content management. By integrating GitHub data with Microsoft Search, users can access relevant content directly within Microsoft 365 applications, streamlining workflows and reducing the need to switch between platforms. 

This article is for Microsoft 365 administrators or anyone who configures, runs, and monitors a GitHub Cloud Knowledge Copilot connector.

## Capabilities
- Index GitHub Cloud repositories, markdown, and text files to make project documentation accessible in Microsoft 365.
- Enable end users to ask questions in Copilot related to project documentation and technical guides.
   - How do I set up Project Alpha?
   - Where can I find the deployment instructions?
   - What is the architecture overview for this project?
   - Summarize key sections in project documentation for quick reference
- Use [Semantic search in Copilot](semantic-index-for-copilot.md) to enable users to find relevant content based on keywords, personal preferences, and social connections.

## Limitations
- Only repository metadata, markdown, and text files are indexed. Other GitHub entities such as issues, pull requests, and comments aren't indexed.
- Only markdown and text files up to 1 MB in size are supported. Larger files aren't indexed.

## Prerequisites
- You must be the **search admin** for your organization's Microsoft 365 tenant.
- Make sure that your GitHub instance is accessible via API.
- Configure and set up the GitHub App for authentication following the steps outlined [here](#set-up-a-github-app-for-authentication).
- Make sure that users who access indexed GitHub data have corresponding Microsoft Entra ID identities for permission mapping.
- For enterprise-managed users who authenticate via Single Sign-On (SSO), the account must be signed in before performing any actions, as the GitHub authentication flow does not currently support SSO login.

### Set Up a GitHub App for Authentication 
Follow the steps below to create a GitHub App for use with your Graph Connector:

1. In GitHub, click your profile photo (top right), select **Your organizations**, and choose the organization where the Graph Connector should pull data from.

   :::image type="content" alt-text="Screenshot that shows how to access 'Your organizations'." source="media/github-connector/organizations-nav.png" lightbox="media/github-connector/organizations-nav.png":::

2. On the organization overview page, click **Settings**.

   :::image type="content" alt-text="Screenshot that shows how to access 'Settings' within the organization page." source="media/github-connector/organization-overview.png" lightbox="media/github-connector/organization-overview.png":::

3. In the left sidebar, scroll down to **Developer settings** and click **GitHub Apps**.

   :::image type="content" alt-text="Screenshot that shows how to access GitHub Apps." source="media/github-connector/github-apps.png" lightbox="media/github-connector/github-apps.png":::

4. Click **New GitHub App**.

   :::image type="content" alt-text="Screenshot that shows entry point to creation of new app." source="media/github-connector/new-github-app.png" lightbox="media/github-connector/new-github-app.png":::

5. Configure the app:
   - **GitHub App name**: Enter a name of your choice.
   - **Homepage URL**: Copy the URL from your browser’s address bar (refer to the image if needed).
   - **Callback URL**:  
     - For Microsoft 365 Enterprise: `https://gcs.office.com/v1.0/admin/oauth/callback`  
     - For Microsoft 365 Government: `https://gcsgcc.office.com/v1.0/admin/oauth/callback`

       :::image type="content" alt-text="Screenshot that shows the initial part of the app configuration including name and URLs." source="media/github-connector/github-app1.png" lightbox="media/github-connector/github-app1.png":::

6. Check **Request user authorization (OAuth) during installation** and disable the **Webhook** option.

   :::image type="content" alt-text="Screenshot that of some check boxes required for the app configuration." source="media/github-connector/github-app2.png" lightbox="media/github-connector/github-app2.png":::

7. Set the following permissions:
   - **Repository permissions**: Contents (Read-only), Metadata (Read-only)
   - **Organization permissions**: Members (Read-only)
   - **Account permissions**: Email addresses (Read-only)

8. Under **Where can this GitHub App be installed**, select **Any account**, then click **Create GitHub App**.

   :::image type="content" alt-text="Screenshot that shows the final steps of the GitHub app set up." source="media/github-connector/github-app3.png" lightbox="media/github-connector/github-app3.png":::

9. On the GitHub App’s **General** page, generate and copy the **client secret** by clicking **Generate a new client secret**. Then click **Install App**.

   :::image type="content" alt-text="Screenshot that shows the credentials of the app including Client Id and Client secret." source="media/github-connector/github-app-credentials.png" lightbox="media/github-connector/github-app-credentials.png":::

10. Select the organization where you want the app to be installed. **After installation**, you're ready to configure the connector.

    :::image type="content" alt-text="Screenshot that shows the app installation dialog." source="media/github-connector/github-install.png" lightbox="media/github-connector/github-install.png":::

## Get Started

### 1. Display name 
A display name is used to identify each citation in Copilot, helping users easily recognize the associated file or item. Display name also signifies trusted content. Display name is also used as a [content source filter](/MicrosoftSearch/custom-filters#content-source-filters). A default value is present for this field, but you can customize it to a name that users in your organization recognize.

### 2. Authentication Type
#### Authentication type: GitHub App (on behalf of user)
- Enter your **Client ID** and **Client secret** from your GitHub App.
- Choose **Authorize** to sign in and grant access. We recommend using separate user accounts for OAuth authentication with each connection, as GitHub's rate limit is calculated individually per user.
	@@ -112,6 +111,10 @@ Choose a display name that helps users recognize the connection in a Copilot res
- Generate private key from your GitHub App following the [GitHub documentation](https://docs.github.com/en/apps/creating-github-apps/authenticating-with-a-github-app/managing-private-keys-for-github-apps).
- Enter your **Client ID** from your GitHub App, your organization name, and upload the private key generated from the last step.

#### Authentication type: GitHub App (installation)
- Generate private key from your GitHub App following the [GitHub documentation](https://docs.github.com/en/apps/creating-github-apps/authenticating-with-a-github-app/managing-private-keys-for-github-apps).
- Enter your **Client ID** from your GitHub App, your organization name, and upload the private key generated from the last step.

### 3. Roll out to limited audience
Deploy this connection to a limited user base if you want to validate it in Copilot and other Search surfaces before expanding the rollout to a broader audience. To know more about limited rollout, see [staged rollout](staged-rollout-for-graph-connectors.md).

## Custom Setup

Custom setup is for those admins who want to edit the default values for settings listed. Once you click on the "Custom Setup" option, you see three more tabs - Users, Content, and Sync.

### Users

**Access Permissions**

The GitHub Cloud Knowledge connector supports search permissions visible to **Only people with access to this data source** (default) or **Everyone**. If you choose **Only people with access to this data source**, indexed data will appear in the search results for users who have access to them. If you choose **Everyone**, indexed data will appear in the search results for all users.

**Map Identities**

In custom setup you can edit any of the default values for users, content, and sync.
To ensure correct permission enforcement, map GitHub user identities to Microsoft Entra ID. The following are the options:

- **Email**: Maps GitHub email to Microsoft Entra ID user properties.
- **Login**: Maps GitHub logins with Microsoft Entra ID user properties.
- **Name**: Maps GitHub name with Microsoft Entra ID user properties.

If direct mapping fails, use regular expressions (regex) to transform the data. For example: [a-zA-Z0-9]+ For personal accounts, mapping accuracy may be impacted due to variations in email domains and individual email visibility settings.

For more information about identity transformation, see [Map your non-Azure AD Identities](map-non-aad.md).
 
### Content

Choose the repositories and file types (initially markdown files and text documentation) you wish to make searchable.

Under **Manage Properties** you can add or remove available properties from your GitHub data source, assign a schema to the property (define whether a property is searchable, queryable, retrievable, or refinable), change the semantic label and add an alias to the property.

### Sync

The refresh interval determines how often your data is synced between the data source and the Graph connector index. There are two types of refresh intervals - full crawl and incremental crawl. For more information, see [refresh settings](configure-connector.md#guidelines-for-sync-settings).

The following are the default values:

- Incremental crawl runs every 15 minutes by default.
- Full crawl runs daily to ensure up-to-date indexing.

You can change the default values of refresh interval from here if you want to.

## Troubleshooting
After publishing your connection, you can review the status in the **Agents and connectors** section of the [admin center](https://admin.microsoft.com). To learn how to make updates and deletions, see [Manage your connector](manage-connector.md). 

>[!NOTE]
>
> When using the [Index Browser](connectors-index-search.md) to identify indexed items, repository and file names are **case-sensitive**. Make sure to match the exact casing to retrieve accurate results.

If you have issues or want to provide feedback, contact [Microsoft Graph | Support](https://developer.microsoft.com/en-us/graph/support).
