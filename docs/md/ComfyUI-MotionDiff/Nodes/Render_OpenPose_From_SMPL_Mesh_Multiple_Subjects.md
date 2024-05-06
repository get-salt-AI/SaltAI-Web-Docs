---
tags:
- SMPL
- SMPLModel
---

# Render OpenPose from SMPL Multiple
## Documentation
- Class name: `Render_OpenPose_From_SMPL_Mesh_Multiple_Subjects`
- Category: `MotionDiff/smpl`
- Output node: `False`

This node is designed to render OpenPose visualizations from SMPL mesh data for multiple subjects. It focuses on converting the skeletal structure of multiple human figures, as represented by SMPL models, into OpenPose format, which is a widely used framework for human body pose estimation.
## Input types
### Required
- **`smpl_multi_subjects`**
    - The SMPL model data for multiple subjects, which is essential for generating OpenPose visualizations of each subject's pose.
    - Comfy dtype: `SMPL_MULTIPLE_SUBJECTS`
    - Python dtype: `List[Dict[str, Any]]`
## Output types
- **`image`**
    - Comfy dtype: `IMAGE`
    - The rendered OpenPose visualization as an image, showcasing the pose estimations of multiple subjects.
    - Python dtype: `torch.Tensor`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class Render_OpenPose_From_SMPL_Mesh_Multiple_Subjects:
    @classmethod
    def INPUT_TYPES(s):
        return {
            "required": {
                "smpl_multi_subjects": ("SMPL_MULTIPLE_SUBJECTS", )
            },
        }
    RETURN_TYPES = ("IMAGE",)
    CATEGORY = "MotionDiff/smpl"
    FUNCTION = "render"
    def render(self, smpl_multi_subjects):
        render_openpose = smpl_multi_subjects[2].get("render_openpose", None)
        if render_openpose is None:
            raise NotImplementedError("render_openpose")
        return (render_openpose().float() / 255., )

```
