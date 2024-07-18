---
description: Seamlessly upload models directly from Huggingface or CivitAI, streamlining your development process.
---

# Importing Models

Self service model importing is here! You can now seamlessly upload models directly from Huggingface or CivitAI, streamlining your development process.

## Importing from CivitAI

!!! warning "Public Models Only"

    Some models hosted on CivitAI require authentication to download. Currently, this is not supported on Salt.

Watch this short tutorial to learn how to easily import models directly from CivitAI.

<video controls>
  <source src="/videos/model-import-tutorial.mp4" type="video/mp4">
  Your browser does not support the video tag.
</video>

## Importing from Hugging Face

!!! Warning "Note about Models"

    When downloading a model from Huggingface, ensure you are copying the link directly to the primary file. Huggingface repositories often include additional files.

Watch this short tutorial to learn how to easily import models directly from Hugging Face.

<video  controls>
  <source src="/videos/model-import-huggingface.mp4" type="video/mp4">
  Your browser does not support the video tag.
</video>

## Frequently Asked Questions

### Q: What file types are supported for model imports?

A: Salt supports .safetensors, .ckpt, and .pth file types.

### Q: Can I import multiple models at once?

A: Yes, you can import multiple models in parallel.

### Q: How long does it take to import a model?

A: Some models can be very large in filesize. It could take up to 10 minutes for large models to be avaialble on the platform.

### Q: Why can't I import a specific model?

A: We do not support importing private models from either CivitAI or Hugging Face. If the creator of the asset requires you to login, or marks the model as private, you will not be able to import it on Salt.

If you encounter any issues not covered here or need further assistance, don’t hesitate to reach out to us through **[Discord](https://discord.com/channels/1151592612525002822/1212167911771217961)**.
