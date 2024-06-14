# Deploying workflows via Salt API

## Overview

Deploy to API enables you to transform your workflows into production-ready APIs, allowing you to seamlessly integrated advanced AI capabilities into your app quickly.

- **Scalability:** Your deployment will be able to handle increasing loads without compromising performance.
- **Accessibility:** Simplify the arduous process of deploying an AI workflow, and enable production-ready use with the click of a button.
- **Speed:** Lightning fast deployments enables rapid iteration of workflows.

## Deploying to API

1. Once your workflow is ready for production use, navigate to the deploy button in the workflow editor.
2. Select the "Deploy to API" option.
3. You will receive a unique endpoint that you can execute via CURL.

## Sending Inputs

### Example cURL Request

```
curl -X POST --location "<http://salt-api-dev.getsalt.ai/api/v1/deployments/7824a62e-8222-454d-9ef4-bf1c7746fed4/executions/>" \\
-H "Content-Type: application/json" \\
-d '{ "callback": "<CALLBACK URL>" }'
```

### Example Request Body

```json
{
  "callback": "<CALLBACK URL>",
  "workflow_input": {
    "14": {
      "value": {
        "url": "<https://imageurl.com/image.png>"
      },
      "value_type": "REFERENCE"
    }
  }
}
```

When the CURL command and the respective object are executed, you will receive this response:

```json
{
  "execution_id": "d919a4b6-01cc-4e1a-ad7a-c59a066576b8"
}
```

## Salt Input Node

The Salt Input Node is an official node developed by the Salt team. This node allows you to expose input parameters of your workflow for end user consumption.

<aside>
ℹ️ It is recommended to give the Salt Input Node a unique name for easier reference in your request body.

</aside>

### Supported Value Types

- RAW Types: Strings, Integers, Float, Boolean
- Reference Types: URLs
- Asset: Base64 Image / Video Data

## Receiving a response via webhook

As workflow run time can vary from seconds to minutes, the output will not be available immediately. To retrieve the final result, please provide a webhook for us to call when the results are ready.

### Example Response

```json
{
  "result": ["<https://storage.googleapis.com/>....."],
  "execution_id": "d919a4b6-01cc-4e1a-ad7a-c59a066576b8"
}
```

## Troubleshooting

### Common Errors

- **Incorrect Input Structure:** If you provide an incorrect input structure in the curl command, an error message will be received.

### Example Error Response

```json
{
  "error": "Invalid input structure"
}
```
