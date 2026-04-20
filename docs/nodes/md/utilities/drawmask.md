# Draw Mask

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Draw Mask provides an interactive canvas in the UI where you can paint a grayscale mask, which is then converted into a normalized MASK tensor. The node enforces a specific output width and height and handles the special case of an empty mask by returning an all-zero tensor with the requested dimensions. This makes it a convenient way to craft inpainting, outpainting, or region-of-interest masks directly inside a workflow.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/utilities/drawmask.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use Draw Mask when you need a custom, hand-drawn mask to control where an operation should apply, such as inpainting only inside a region, selectively editing a subject, or protecting background areas from changes. It typically appears early in an image pipeline, feeding its MASK output into nodes that accept masks for blending, inpainting, compositing, or region-aware generation. Upstream, the canvas is configured directly in the Salt UI; downstream, connect the mask to masking-related nodes such as diffusion samplers with mask support or image blending/compositing nodes that take a MASK input.

Configure width and height to match the images you intend to process so the mask aligns correctly with your image tensors. The brush_size parameter controls how large the brush strokes appear on the canvas, which is helpful for quickly covering large areas or for fine detailing. brush_color defines the grayscale value of the stroke (0 = black, 255 = white); in most masking workflows white is interpreted as the active or affected region and black as background, but always confirm how your downstream node interprets mask values. Common patterns include creating an inpainting mask over damaged areas, painting regions to protect from style transfer, or marking areas for targeted enhancement.

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
<tr><td style="word-wrap: break-word;">canvas</td><td>True</td><td style="word-wrap: break-word;">CANVAS</td><td style="word-wrap: break-word;">The interactive drawing surface controlled by the Salt UI. Internally this is a base64-encoded image string (for example, a data URL) representing the grayscale mask you paint. The user does not manually type or wire this value; it is managed automatically when you draw on the canvas attached to this node.</td><td style="word-wrap: break-word;">data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAgAAAAIACAYAAAB... (truncated base64 canvas data)</td></tr>
<tr><td style="word-wrap: break-word;">width</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Output mask width in pixels. The canvas UI is constrained to this width, and the exported mask tensor will have this exact width. Must be between 32 and 2048 inclusive. Changing this value dynamically updates the canvas aspect ratio in the UI.</td><td style="word-wrap: break-word;">768</td></tr>
<tr><td style="word-wrap: break-word;">height</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Output mask height in pixels. The canvas UI is constrained to this height, and the exported mask tensor will have this exact height. Must be between 32 and 2048 inclusive. Changing this value dynamically updates the canvas aspect ratio in the UI.</td><td style="word-wrap: break-word;">1024</td></tr>
<tr><td style="word-wrap: break-word;">brush_size</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Brush diameter in pixels for drawing on the canvas. Must be between 1 and 100. Larger values help cover broad areas quickly; smaller values are useful for detailed work and precise edges. The brush size change is reflected immediately in the UI.</td><td style="word-wrap: break-word;">24</td></tr>
<tr><td style="word-wrap: break-word;">brush_color</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Brush intensity in grayscale, from 0 (black) to 255 (white). The drawn stroke pixels will use this value. Typically 255 is used to mark fully active or masked regions, and 0 is used for background, but you can use intermediate values if downstream nodes support partial mask strengths.</td><td style="word-wrap: break-word;">255</td></tr>
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
<tr><td style="word-wrap: break-word;">mask</td><td style="word-wrap: break-word;">MASK</td><td style="word-wrap: break-word;">A single-channel mask tensor of shape (1, height, width) with floating-point values in the range [0.0, 1.0]. When the canvas contains a drawing, it is converted to grayscale, resized to the specified width and height, normalized by dividing by 255, and turned into this tensor. If the canvas is completely empty (all pixels black), the node instead returns an all-zero tensor with the requested dimensions. This MASK can be wired into any node expecting a mask tensor.</td><td style="word-wrap: break-word;">A tensor shaped [1, 1024, 768] where most values are 0.0 (background) and a hand-drawn region has values near 1.0, representing the area where inpainting or editing should occur.</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: The node itself is lightweight, but very high resolutions (close to 2048x2048) can increase memory use and processing time in downstream image nodes that consume this mask.
- **Limitations**: The canvas image is always converted to 8-bit grayscale and normalized to [0,1]; color masks or multi-channel masks are not supported.
- **Behavior**: If the drawn mask is completely empty (all pixels are black), the node bypasses image resizing and instead creates an all-zero tensor of shape (1, height, width) to ensure stable output dimensions.
- **Behavior**: The mask is always resized to exactly the width and height you specify, which can stretch or compress your strokes if you adjust dimensions after drawing. For precise alignment, set the final resolution first and then paint your mask.

## Troubleshooting
- **Mask does not line up with my image**: This usually means the mask width/height differ from the image resolution used downstream. Set Draw Mask's width and height to match your image node's output resolution before drawing.
- **Mask appears to have no effect**: Either the canvas is empty or brush_color was set to 0 (black). Draw on the canvas using a non-zero brush_color (commonly 255) and check that the downstream node interprets white as the active region.
- **Mask edges look softer or different than expected**: Because the mask is resized with high-quality resampling to the specified width and height, changing dimensions after drawing can slightly blur or distort the strokes. Define the target resolution first, then re-draw the mask if needed.
- **Effects apply in the opposite region than intended**: Some downstream nodes treat white as the region to edit, others as the region to protect. If the effect is inverted relative to what you expect, adjust the mask usage in the downstream node or redraw by painting the opposite area.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../images/assets/utilities/drawmask/example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

