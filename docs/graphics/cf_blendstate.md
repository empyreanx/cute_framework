[//]: # (This file is automatically generated by Cute Framework's docs parser.)
[//]: # (Do not edit this file by hand!)
[//]: # (See: https://github.com/RandyGaul/cute_framework/blob/master/samples/docs_parser.cpp)
[](../header.md ':include')

# CF_BlendState

Category: [graphics](/api_reference?id=graphics)  
GitHub: [cute_graphics.h](https://github.com/RandyGaul/cute_framework/blob/master/include/cute_graphics.h)  
---

Defines how colors are mixed together when the GPU is drawing individual pixels.

Struct Members | Description
--- | ---
`bool enabled` | True enables modifying the default blend state.
`CF_PixelFormat pixel_format` | The pixel format to perform blend operations. This should match the target texture you're rendering to. See [CF_PixelFormat](/graphics/cf_pixelformat.md).
`bool write_R_enabled` | Set to true to blend the red component.
`bool write_G_enabled` | Set to true to blend the green component.
`bool write_B_enabled` | Set to true to blend the blue component.
`bool write_A_enabled` | Set to true to blend the alpha component.
`CF_BlendOp rgb_op` | The blend operator to use for the color components. See [CF_BlendOp](/graphics/cf_blendop.md).
`CF_BlendFactor rgb_src_blend_factor` | The factor to use for S in the blend equation for the color components. See remarks for details.
`CF_BlendFactor rgb_dst_blend_factor` | The factor to use for D in the blend equation for the color components. See remarks for details.
`CF_BlendOp alpha_op` | The blend operator to use for the alpha component. See [CF_BlendOp](/graphics/cf_blendop.md).
`CF_BlendFactor alpha_src_blend_factor` | The factor to use for S in the blend equation for the alpha component. See remarks for details.
`CF_BlendFactor alpha_dst_blend_factor` | The factor to use for D in the blend equation for the alpha component. See remarks for details.

## Remarks

There are many ways to blend two colors together to create all kinds of different effects. Defining how we draw one thing atop
another is called "compositing", or "image compositing". The actual operation of mixing two colors together to form a
a pixel is called blending. We describe blend equations as little math equations.

We can say the pixel is called P, while the input colors are S and D (source and destination). Modern GPUs provide some
common _operators_ for defining blend functions: add, subtract, reverse subtract, min and max (see [CF_BlendOp](/graphics/cf_blendop.md)). Cute Framework only
uses add, subtract, and reverse subtract, as min/max are not very cross-platform compatible. On each side of an _operator_ are the
_factors_ (see [CF_BlendFactor](/graphics/cf_blendfactor.md)).

Here is the add operator `CF_BLEND_OP_ADD`:

P = S + D

Recap: P is the pixel to write, S is the source factor, while D is the destination factor. Usually the D (destination factor) is
the old pixel value, while S (source factor) is a new image getting draw over old pixel contents. Therefor, P is the final color
after compositing a new image on top of an old image.

Blend factors (see [CF_BlendFactor](/graphics/cf_blendfactor.md)) are components of a color, including the alpha component. The most common setup for your
blend state is to use [premultiplied alpha](https://blog.demofox.org/2015/06/19/what-is-pre-multiplied-alpha-and-why-does-it-matter/) when loading your images.
Cute Framework internally loads up images in premultiplied alpha format for you. Cute Framework's default blend state is to use
_additive blending_. The blend function looks like this:

P = 1 x S + D x (1 - S.alpha)

Or re-written with Cute Framework enums:

P = S x CF_BLENDFACTOR_ONE CF_BLEND_OP_ADD D x CF_BLENDFACTOR_ONE_MINUS_SRC_ALPHA

Which can be setup with this kind of code:

```cpp
draw->material = cf_make_material();
CF_RenderState state = cf_render_state_defaults();
state.blend.enabled = true;
state.blend.rgb_src_blend_factor = CF_BLENDFACTOR_ONE;
state.blend.rgb_dst_blend_factor = CF_BLENDFACTOR_ONE_MINUS_SRC_ALPHA;
state.blend.rgb_op = CF_BLEND_OP_ADD;
state.blend.alpha_src_blend_factor = CF_BLENDFACTOR_ONE;
state.blend.alpha_dst_blend_factor = CF_BLENDFACTOR_ONE_MINUS_SRC_ALPHA;
state.blend.alpha_op = CF_BLEND_OP_ADD;
draw->render_states.add(state);
cf_material_set_render_state(draw->material, state);
```

You can of course define your own blend state in any way you like to perform all kinds of compositing effects. However, dynamically changing the
blend state will result in more draw calls to the GPU, which negatively affects performance. A major win for premultiplied alpha and
additive blending is that some common rendering needs can all be batched together within one draw call, without requiring a change to
the blend state. One example is rendering additively or with transparency in the same draw call. There are some other benefits that come from
this style as well. Here are some nice links on the topic.
- https://developer.nvidia.com/content/alpha-blending-pre-or-not-pre
- https://shawnhargreaves.com/blog/premultiplied-alpha.html

## Related Pages

[CF_BlendFactor](/graphics/cf_blendfactor.md)  
[CF_BlendOp](/graphics/cf_blendop.md)  
[CF_RenderState](/graphics/cf_renderstate.md)  
[cf_material_set_render_state](/graphics/cf_material_set_render_state.md)  
