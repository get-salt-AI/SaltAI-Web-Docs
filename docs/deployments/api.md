---
description: The Deploy to API feature in Salt AI allows you to transform your workflows into production-ready APIs.
---

# Deployments: API

## Overview

The `Deploy to API` feature in Salt AI allows you to transform your workflows into production-ready APIs, enabling seamless integration of advanced AI capabilities into your applications.

Key benefits:

- **Scalability:** Your deployment can handle increasing loads without compromising performance.
- **Accessibility:** Simplify the complex process of deploying an AI workflow with a single click.
- **Speed:** Lightning-fast deployments enable rapid iteration of workflows.

## Deploying to API

1. Ensure your workflow is ready for production use and your most recent run was successful.
2. In the workflow editor, locate and click the `Deploy` button in the header and select `Deploy to API`, or click the rocket-ship icon on the right-side panel. This will open the deployments tab in the right-side panel.
3. Select the `API` option at the top of the deployments tab.
4. Change the visibility of inputs and outputs as desired.
5. Click the `Deploy` button to create the new API deployment.
6. You'll receive a unique endpoint for executing your workflow via API calls.

![Deployment Process](../images/deployments-api1.png)

### Configuring Input and Output Visibility

If you have intermediate input or output nodes that you don't want to expose to API users, you can change the visibility of those fields when configuring the deployment.

- **Hidden Inputs** will be automatically defaulted to their current value, and attempts to set them via the API will be treated as an invalid input.
- **Hidden Outputs** will not be included in the API response sent to the callback URL.

![Configuring Input and Output Visibility](../images/deployments-api2.png)

## Making API Requests

### Example cURL Request

```bash
curl -X POST https://salt-api-dev.salt.ai/api/v1/deployments/7824a62e-8222-454d-9ef4-bf1c7746fed4/executions/ \
-H "Content-Type: application/json" \
-d '{
  "callback": "https://your-callback-url.com",
  "workflow_input": {
    "1": {
      "value": "My text input",
      "value_type": "RAW"
    }
  }
}'
```

### Request Body Structure

```json
{
	"callback": "<CALLBACK URL>",
	"workflow_input": {
		"<input_node_id>": {
			"value": "<input_value>",
			"value_type": "<RAW>"
		}
		// ...
	}
}
```

### Response to API Request

Upon successful submission, you'll receive a response containing the execution ID:

```json
{
	"execution_id": "d919a4b6-01cc-4e1a-ad7a-c59a066576b8"
}
```

### Supported Value Types

1. `RAW`: For basic data types (Strings, Integers, Floats, Booleans)
2. `REFERENCE`: For URL inputs (coming soon)
3. `ASSET`: For Base64-encoded image or video data (coming soon)

## Receiving Results via Webhook

Due to varying execution times, Salt AI delivers results asynchronously through a webhook. Provide a callback URL in your request to receive the final results.

### Example Webhook Response

```json
{
	"output_name": ["https://storage.googleapis.com/...."],
	"execution_id": "d919a4b6-01cc-4e1a-ad7a-c59a066576b8"
}
```

## Troubleshooting

### Common Errors

1. **Invalid Input Structure:** Occurs when the request body doesn't match the expected format.
2. **Missing Callback URL:** Ensure you provide a valid callback URL for receiving results.
3. **Deployment Not Found:** Check that you're using the correct deployment ID in your API endpoint.

### Example Error Response

```json
{
	"error": "Invalid input structure",
	"details": "Missing required field 'workflow_input'"
}
```

## Best Practices

1. **Input Validation:** Implement thorough input validation in your workflow to handle unexpected inputs gracefully.
2. **Error Handling:** Design your workflow to handle potential errors and provide meaningful error messages.

By following this guide, you can effectively deploy your Salt AI workflows as scalable, production-ready APIs. For further assistance or advanced use cases, please refer to our additional documentation or contact our support team.
