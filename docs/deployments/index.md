---
description: Vew, manage, and monitor your deployed workflows.
---

# Deployments

Deployments enable the execution of your workflow without the need to run it from the editor and are a great way to share your workflow with others and integrate them into other applications. They are managed in the Deployments tab of the workflow editor, where you can create, view, and manage deployments of your workflow.

![Deployment Tab Open in Editor](deployments1.png)

## Availability

<div class="pro-callout">
  <strong>PRO Feature</strong><br>
  Deployments are part of our PRO tier features. New users can explore these capabilities during the free trial period to evaluate how they fit into their workflows.
</div>

## Deployment Types

Deployments can be of two types:

- **Webform**: A deployment that creates a new webform for your workflow, which can be used to execute the workflow via a webform hosted on Salt's website, results are viewable immediately in the webform page.
- **API**: A deployment that creates a new API endpoint for your workflow, which can be used to execute the workflow via API calls, and provided a webhook URL for Salt to call when the workflow completes.

## Understanding Deployments

Deployments in Salt AI represent a snapshot of a workflow at a specific point in time and . It's important to understand the following key concepts:

1. **Snapshot Nature**: When you deploy a workflow, you're creating a static snapshot of that workflow as it exists at that moment.

2. **Independence from Source**: Once deployed, a deployment is independent of the original workflow in the editor. Changes made to the workflow after deployment will not affect the existing deployment until you "redeploy" it from the deployments tab.

3. **Redeployment Process**: To update a deployment with changes made to a workflow, you must use the "redeploy" button in the deployments tab, make any relevant changes to the deployment configuration, and then click "Re-Deploy".

## Troubleshooting

If you encounter issues with a deployment:

1. Check the deployment's status to ensure it's running.
2. Verify that the deployment represents the version of the workflow you intend to use.
3. If needed, delete the current deployment and redeploy the workflow to ensure you're using the most up-to-date version.
4. For Discord bot deployments, verify the bot's permissions in your Discord server.

<aside> ℹ️ Remember, changes made to workflows after deployment will not affect existing deployments. To incorporate workflow changes, you must update and redeploy your deployment.</aside>

For more detailed information on creating specific types of deployments, please refer to our [Webform Deployment](webform.md) and [API Deployment](api.md) guides.
