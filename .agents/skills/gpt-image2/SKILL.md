---
name: gpt-image2
description: Generate or edit images with gpt-image2.0 using clear prompt construction and a concise execution workflow. Use when the user asks to 作图, 画图, 生图, 生成图片, 出图, 做图, 设计图片, 修图, 改图, 换风格, or otherwise requests image creation or image editing.
---

# GPT Image 2.0

## Purpose

Use this skill when the user wants an image generated or edited. The goal is to turn the user's intent into a complete image prompt and execute the image request directly unless an essential detail is missing.

## Workflow

1. Identify the task type:
   - New image: create an image from a text description.
   - Image edit: modify an attached or referenced image.
   - Variation: keep the same subject while changing style, composition, mood, color, or format.

2. Extract the creative brief:
   - Subject: people, object, product, scene, logo, poster, UI, character, etc.
   - Style: photo, illustration, 3D render, anime, watercolor, flat vector, cinematic, product shot, etc.
   - Composition: framing, angle, background, focal point, layout, and negative space.
   - Constraints: aspect ratio, text requirements, brand colors, realism, age, clothing, pose, lighting, and forbidden elements.
   - Output purpose: avatar, banner, poster, ad, icon, mockup, concept art, social image, game asset, etc.

3. Ask at most one clarification question only when the request cannot be executed responsibly:
   - Missing source image for an edit.
   - Ambiguous identity or copyrighted character request that needs a safer alternative.
   - Required exact text where the wording is not supplied.
   - Conflicting requirements such as "transparent background" and "full scenic background".

4. Build the final prompt:
   - Start with the concrete subject and action.
   - Add style, medium, camera/composition, lighting, palette, materials, and background.
   - Include explicit constraints such as "no text", "transparent background", or "leave space on the left".
   - For edits, describe what must stay unchanged before describing what changes.

5. Execute with the image generation tool:
   - For new images, send the final prompt directly.
   - For edits, use the provided image plus precise edit instructions.
   - After generation, do not add extra commentary unless the user asked for explanation or variants.

## Prompt Pattern

```text
[Subject and action], [style/medium], [composition and framing], [lighting],
[color palette/materials], [background/environment], [purpose or format],
[constraints: no text, transparent background, exact text, aspect ratio, etc.]
```

## Edit Prompt Pattern

```text
Keep [unchanged parts] exactly the same. Change [specific elements] to
[new appearance]. Preserve [identity, pose, perspective, lighting, background,
or style] unless explicitly changed. Avoid [forbidden elements].
```

## Quality Checklist

- The prompt is specific enough to produce one coherent image.
- The task type is clear: new image, edit, or variation.
- Any required text is provided verbatim.
- Important constraints are explicit.
- The response uses the image tool directly instead of only describing how to make the image.
