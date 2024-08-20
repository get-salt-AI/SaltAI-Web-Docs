---
description: To ensure fair usage and manage our system capacity, we have implemented limits on how much users can utilize our workflow execution services.
---

# Rate Limits

To ensure fair usage and manage our system capacity, we have implemented limits on how much users can utilize our workflow execution services. These limits are designed to prevent misuse while supporting common usage patterns.

## About our limits

- Limits are set based on your plan type: Free or Individual (Paid).
- Limits apply to two key areas: workflow editor executions and API executions.
- The limits outlined below are our standard limits. For higher, custom limits, please contact us.

## Usage Limits

Each plan has specific limits on how many workflow executions you can queue at a time. Once you reach the usage limit of your plan, you will need to wait for current jobs to complete before submitting new ones.

| Plan       | Workflow Editor Queued Executions | API Queued Executions |
| ---------- | --------------------------------- | --------------------- |
| Free       | 2 pending jobs                    | 5 pending jobs        |
| Individual | 5 pending jobs                    | 10 pending jobs       |

## Understanding Rate Limits

Our rate limits are measured in terms of queued requests. If you exceed the rate limits, you will receive an error message and will need to wait for current jobs to complete before submitting new ones.

### Workflow Editor Rate Limits

- **Free Plan**: Maximum of 2 pending jobs at any given time.
- **Individual (Paid) Plan**: Maximum of 5 pending jobs at any given time.

### API Rate Limits

- **Free Plan**: Maximum of 5 pending jobs at any given time.
- **Individual (Paid) Plan**: Maximum of 10 pending jobs at any given time.

## Handling Rate Limit Errors

If you exceed the rate limits, you will receive a 429 (Too Many Requests) error. Please wait until a pending job has complete before retrying the request.

## Upgrading Your Plan

If you find that you frequently hit the rate limits on the Free plan, consider upgrading to the Individual (Paid) plan for higher limits and additional features. Learn more at getsalt.ai/pricing.
