# Deploying workflows via Salt API

## Overview

`Deploy to API` enables you to transform your workflows into production-ready APIs, allowing you to seamlessly integrated advanced AI capabilities into your app quickly.

- **Scalability:** Your deployment will be able to handle increasing loads without compromising performance.
- **Accessibility:** Simplify the arduous process of deploying an AI workflow, and enable production-ready use with the click of a button.
- **Speed:** Lightning fast deployments enables rapid iteration of workflows.

## Deploying to API

1. Once your workflow is [ready for production use](https://docs.getsalt.ai/workflows/#preparing-your-workflow-for-deployment), navigate to the `Deploy` button in the workflow editor.
2. Select the `Deploy to API` option.
3. You will receive a unique endpoint that you can execute via CURL.

![deployments1](images/deployments1.png)

<aside>
ℹ️ The most recent job execution must be successful in order to proceed with deployment.
</aside>

## Sending inputs

### Example cURL request

```
curl -X POST --location "<https://salt-api-dev.getsalt.ai/api/v1/deployments/7824a62e-8222-454d-9ef4-bf1c7746fed4/executions/>"
-H "Content-Type: application/json"
-d '{ "callback": "<CALLBACK URL>" }'
```

### Example request body

```json
{
  "callback": "<CALLBACK URL>",
  "workflow_input": {
    "input_name": {
      "value": "My text input",
      "value_type": "RAW"
    },
    "input_name2": {
      "value": {
        "url": "<https://imageurl.com/image.png>"
      },
      "value_type": "REFERENCE"
    },
  }
}
```

When the CURL command and the respective object are executed, you will receive this response:

```json
{
  "execution_id": "d919a4b6-01cc-4e1a-ad7a-c59a066576b8"
}
```

## SaltInput node

The `SaltInput` node allows you to expose input parameters for end-user interaction within your workflow. Each one represents an input property in the `workflow_input` object of your API call's request body.

<aside>
ℹ️ It is recommended to give each SaltInput node a unique name for easier reference when constructing your request body. If a node is unnamed, it will be named by its node id number, e.g. "14".
</aside>

### Supported value types

- `RAW`: Strings, Integers, Float, Boolean
- `Reference`: URLs
- `Asset`: Base64 Image / Video Data

## Receiving a response via webhook

Workflow run times can vary from seconds to minutes. To manage this, Salt delivers results asynchronously via a webhook. Provide a callback URL to receive the final results once they are ready.

### Example response

```json
{
  "output_name": ["<https://storage.googleapis.com/>....."],
  "execution_id": "d919a4b6-01cc-4e1a-ad7a-c59a066576b8"
}
```

## Troubleshooting

### Common errors

- **Incorrect input structure:** If you provide an incorrect input structure in the curl command, you will receive an error message.

### Example error response

```json
{
  "error": "Invalid input structure"
}
```
