---
description: Salt AI offers a unique feature that allows you to deploy and run your workflows through the `Salt AI` Discord bot.
---

# Deploying and Running Workflows on Discord

Salt AI offers a unique feature that allows you to deploy and run your workflows through the `Salt AI` Discord bot. This guide will walk you through the process of deploying your workflows to Discord and using them within the Discord platform.

## Deploying to Discord

When you're ready to deploy your workflow to Discord:

1. Click the `Deploy` button in the top right corner of the workflow editor.
2. You'll be prompted to give your workflow a name. Choose a unique, descriptive slug – this will be how users discover your workflow through the Salt AI bot.
3. Click `Deploy` to finalize the process.

After successful deployment, you'll see a confirmation screen. You can copy the workflow command to your clipboard from this screen.

You can manage and deactivate your deployments at any time in the Deployments tab of your dashboard.

## Installing the Salt AI Discord Bot

To use Salt AI workflows in Discord:

1. Go to the [Salt AI bot authorization page](https://discord.com/oauth2/authorize?client_id=1113909066587185274).
2. Click the `Add to Server` button.
3. Select the server where you want to add the bot, or choose to add it to your direct messages.

## Running Workflows in Discord

Once your workflow is deployed and the Salt AI bot is added to your server:

1. Use the `/workflows` command in any channel where the bot is present.
2. You can directly enter your workflow's name or use the command format `/workflow your-workflow-name`.

3. Click `Run workflow` to execute it.
4. Fill in the required input values (one for each `SaltInput` node in your workflow).
5. For image or file attachments, you'll be prompted to upload them in your direct messages.
6. After submitting all inputs, your workflow will execute, and the outputs will appear in the original thread or channel.

## Showcasing Your Workflows

To share your workflows with the Salt AI community:

1. Join the [Salt Discord](https://discord.gg/saltai) and complete onboarding.
2. Go to the [#flow-catalog](https://discord.gg/FcbmPDf3E7) channel.
3. Follow the example template to share your workflow.
4. Run your workflow using the Salt AI bot in a comment and pin the comment for easy access.

## Troubleshooting

- If executions fail due to workflow errors (e.g., invalid API keys), you may need to adjust your workflow or contact our support team.
- For connection errors, you can retry the execution by right-clicking (or tap-holding on mobile) > `Apps` > `Retry`.

## Need Help?

If you encounter any issues or have questions about the Discord deployment feature, please reach out to our team via the [#support](https://discord.com/channels/1151592612525002822/1212167911771217961) channel in our Discord.

Salt's Discord integration allows you to easily share your workflows with real users as lightweight applications, without the need for front-end development. This feature bridges the gap between AI development and user interaction, making your AI applications more accessible and interactive.
