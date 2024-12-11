# Salt v1.0 Migration Guide

With the launch of Salt v1.0, we understand you may have questions about transitioning your existing workflows. This guide will help you understand your options and choose the best path forward.

## Understanding the Changes

Salt v1.0 represents a complete platform rebuild with a new node catalog that offers improved functionality and performance.Additionally, we have made the decision to not support many of the community nodes and models that we had previously. As a result, workflows built on previous versions are not directly compatible with the new platform. 

## Your Migration Options

### 1. Export and Preserve Your Workflows
- All existing workflows can be exported as ComfyUI-compatible JSON files
- This preserves your work and enables transition to alternative platforms
- Export process is available through your Salt dashboard

### 2. Choose Your Hosting Solution

#### Serverless Options

**ComfyICU**
[https://comfy.icu/](https://comfy.icu/)
- Serverless cloud hosting with pay-per-use billing
- 80GB memory allocation
- Parallel GPU support
- Ideal for variable workload requirements

**Modal**
[https://modal.com/docs/examples/comfyapp](https://modal.com/docs/examples/comfyapp)
- Interactive and API-based workflow execution
- Pay-as-you-go pricing
- Suitable for both development and production deployments

#### Managed Platforms

**ComfyDeploy**
[https://comfydeploy.com/](https://comfydeploy.com/)
- Collaborative workspace with version control
- One-click API deployment
- Managed, scalable GPU infrastructure
- Team-oriented features and permissions
- Ideal for organizations and teams


**RunComfy**
[https://www.runcomfy.com/](https://www.runcomfy.com/)
- High-speed GPU access
- No technical setup required
- Custom node support
- Model installation capabilities
- Best for users seeking simplicity

#### Infrastructure Providers

**AWS**
[https://aws.amazon.com/](https://aws.amazon.com/)
- Most cost-effective for large-scale operations
- Requires technical expertise
- Minimum 8 GPU rental requirement
- Best for teams with DevOps capabilities

**Alternative Providers**
- Vast.ai: Budget-friendly GPU rental
- Massed Compute: Coupon-based discounts available
- Runpod: Serverless workflow hosting
- Cerebrium: Pay-per-use AI infrastructure
- Baseten: API deployment focus

### 3. Transition to Salt v1.0

If you're ready to take advantage of our new features:
- Build new workflows using the enhanced node catalog
- Utilize smart connectors for data integration
- Access improved LLM capabilities
- Leverage new knowledge base features


## Choosing the Right Path

Consider these factors when selecting your migration strategy:

1. Workflow Complexity
   - Simple workflows may be easily rebuilt in v1.0
   - Complex workflows might benefit from ComfyUI compatibility

2. Resource Requirements
   - Evaluate GPU needs and usage patterns
   - Consider cost implications of different hosting options

3. Team Structure
   - Single user vs team collaboration needs
   - Technical expertise available

4. Integration Requirements
   - API needs
   - External system connections
   - Custom node requirements

We're committed to supporting your transition, whether you choose to move to Salt v1.0 or explore alternative platforms. Our support team is ready to assist with specific questions and challenges throughout your migration journey.
