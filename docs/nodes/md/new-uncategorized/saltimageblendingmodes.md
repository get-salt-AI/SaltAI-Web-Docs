# Composite Images

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node composites two image batches using familiar blending modes such as multiply, screen, overlay, and more. It can optionally use masks to localize where the blend applies and supports fine-grained control over blend intensity, including per-image variation. It outputs a new image batch where each image from the first batch has been blended with a corresponding image from the second batch.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/new-uncategorized/saltimageblendingmodes.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you want to creatively combine two sets of images, apply filmic or Photoshop-like blending effects, or mix features from different images in a controlled way. It typically appears after image generation or transformation nodes that produce two compatible image batches (e.g., one base render and one effect/texture layer) and before further color grading, upscaling, or final compositing nodes such as `Image Overlay`, `SaltImagePasteCrop`, or `SaltImageComposite`. Connect `images_a` as your primary/base images and `images_b` as your secondary/effect images. Choose a `mode` to define how pixel values are mathematically combined. Use `blend_percentage` to control how strongly the blend is applied; you can pass either a single scalar for all images or a list to vary per image. Optionally, supply `masks` when you want the blend only in specific regions (e.g., highlights, subject area, or background). For best results, ensure that both input batches are aligned semantically and that any provided masks have reasonable coverage and soft edges if you want smooth transitions. This node fits naturally in "ImageBlend" workflows along with nodes like `SaltFilmicTransitions`, `Image Blend`, and `PorterDuffImageComposite` when constructing advanced compositing pipelines.

## Inputs

<div style="overflow-x: auto;">
<table style="table-layout: fixed; width: 100%;">
<colgroup>
<col style="width: 15%;">
<col style="width: 10%;">
<col style="width: 15%;">
<col style="width: 30%;">
<col style="width: 30%;">
</colgroup>
<thead><tr><th>Field</th><th>Required</th><th>Type</th><th>Description</th><th>Example</th></tr></thead>
<tbody>
<tr><td style="word-wrap: break-word;">images_a</td><td>True</td><td style="word-wrap: break-word;">IMAGE</td><td style="word-wrap: break-word;">Primary image batch to be used as the base layer. Must be a tensor-style image batch where all images share the same dimensions and color mode once converted internally. Each image in this batch is the reference size; images from `images_b` and `masks` will be resized to match these dimensions.</td><td style="word-wrap: break-word;">A batch of 4 RGB images sized 768x768 representing clean character renders: [image_01_base.png, image_02_base.png, image_03_base.png, image_04_base.png].</td></tr>
<tr><td style="word-wrap: break-word;">images_b</td><td>True</td><td style="word-wrap: break-word;">IMAGE</td><td style="word-wrap: break-word;">Secondary image batch to be composited onto `images_a` using the selected blending mode. If this batch has fewer images than `images_a`, the last image in `images_b` is reused for the remaining positions. Each image is resized to match the corresponding `images_a` image.</td><td style="word-wrap: break-word;">A batch of 2 texture or lighting pass images (e.g., bokeh, scratches, grain) that will be blended with 6 base renders; the second texture will be reused for base images 3–6.</td></tr>
<tr><td style="word-wrap: break-word;">mode</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Blending mode used to combine `images_a` and `images_b`. Supported modes: `normal`, `color`, `color_burn`, `color_dodge`, `darken`, `difference`, `exclusion`, `hard_light`, `hue`, `lighten`, `multiply`, `overlay`, `screen`, `soft_light`. Each mode changes how brightness and color channels from the two images interact.</td><td style="word-wrap: break-word;">multiply</td></tr>
<tr><td style="word-wrap: break-word;">blend_percentage</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Intensity of the blend between 0.0 and 1.0. A value of 0.0 means no visible effect (result is `images_a`), while 1.0 means the full effect of the blending mode as computed. Internally this can be either a single float applied to all images or a list of floats for per-image control.</td><td style="word-wrap: break-word;">0.65</td></tr>
<tr><td style="word-wrap: break-word;">masks</td><td>False</td><td style="word-wrap: break-word;">MASK</td><td style="word-wrap: break-word;">Optional mask batch that defines where the blending is applied. Masks are resized to match their corresponding `images_a` and converted to single-channel luminance. Areas with higher mask values receive more of the blended result, and lower values preserve more of the original `images_a`. If there are fewer masks than images, the last mask is reused.</td><td style="word-wrap: break-word;">A batch of 3 grayscale masks where white highlights the subject’s face and hair (full blend), mid-gray covers clothing (partial blend), and black covers background (no blend).</td></tr>
</tbody>
</table>
</div>

## Outputs

<div style="overflow-x: auto;">
<table style="table-layout: fixed; width: 100%;">
<colgroup>
<col style="width: 20%;">
<col style="width: 20%;">
<col style="width: 35%;">
<col style="width: 25%;">
</colgroup>
<thead><tr><th>Field</th><th>Type</th><th>Description</th><th>Example</th></tr></thead>
<tbody>
<tr><td style="word-wrap: break-word;">images</td><td style="word-wrap: break-word;">IMAGE</td><td style="word-wrap: break-word;">A batch of blended images, one per input in `images_a`. Each output image is the same size and mode as its corresponding `images_a` image, with `images_b` blended in using the chosen mode, optional mask, and blend percentage. The output is a standard image tensor that can be passed directly into further image processing or saving nodes.</td><td style="word-wrap: break-word;">A batch of 4 1024x1024 RGB images where cinematic light streaks from `images_b` have been overlaid on `images_a` using `screen` mode at 0.8 intensity, preserving original shadows while brightening highlights.</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: All `images_b` and mask inputs are resized to match each `images_a` frame; large resolutions or big batches will increase memory usage and runtime.
- **Limitations**: The node expects valid image-like tensor inputs; mismatched or empty batches can lead to runtime errors rather than silent skipping.
- **Behavior**: If `images_b` contains fewer images than `images_a`, the last image from `images_b` is reused for remaining blends, which can be intentional (single effect layer) or accidental if unnoticed.
- **Behavior**: When `masks` are provided, blending is spatially modulated by the mask and further scaled by `blend_percentage`; with no masks, the blend intensity is uniform across the entire image.

## Troubleshooting
- **Batch size mismatch**: If the number of images in `images_a` and `images_b` is not what you expected, you may see repeated textures or inconsistent results. Verify upstream nodes to ensure both batches have the intended sizes or deliberately rely on reuse of the last `images_b` image.
- **Unexpected full-strength blending**: If the blend looks too strong despite low `blend_percentage`, check whether you are also using a mask with very high values (near white everywhere); reduce mask intensity or soften edges.
- **Mask alignment issues**: If blended regions appear shifted or scaled incorrectly, confirm that the masks logically correspond to `images_a` content. The node resizes masks to match `images_a`, so wildly different aspect ratios can distort mask placement.
- **Crashes or type errors**: Errors mentioning masks or tensors often indicate a non-mask input wired into `masks` or a missing image batch. Ensure `masks` is a valid MASK output from another node and that both `images_a` and `images_b` are connected to image-producing nodes.
