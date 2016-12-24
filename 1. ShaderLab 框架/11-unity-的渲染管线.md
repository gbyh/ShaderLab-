##Unity’s Rendering Pipeline
Shaders define both how an object looks by itself (its material properties) and how it reacts to the light. Because lighting calculations must be built into the shader, and there are many possible light & shadow types, writing quality shaders that “just work” would be an involved task. To make it easier, Unity has Surface Shaders, where all the lighting, shadowing, lightmapping, forward vs. deferred rendering things are taken care of automatically.

This document describes the pecularities of Unity’s lighting & rendering pipeline and what happens behind the scenes of Surface Shaders.

####Rendering Paths

How lighting is applied and which Passes of the shader are used depends on which Rendering Path is used. Each pass in a shader communicates its lighting type via Pass Tags.

In Forward Rendering, ForwardBase and ForwardAdd passes are used.
In Deferred Shading, Deferred pass is used.
In legacy Deferred Lighting, PrepassBase and PrepassFinal passes are used.
In legacy Vertex Lit, Vertex, VertexLMRGBM and VertexLM passes are used.
In any of the above, to render Shadows or a depth texture, ShadowCaster pass is used.

####Forward Rendering path

ForwardBase pass renders ambient, lightmaps, main directional light and not important (vertex/SH) lights at once. ForwardAdd pass is used for any additive per-pixel lights; one invocation per object illuminated by such light is done. See Forward Rendering for details.

If forward rendering is used, but a shader does not have forward-suitable passes (i.e. neither ForwardBase nor ForwardAdd pass types are present), then that object is rendered just like it would in Vertex Lit path, see below.

####Deferred Shading path

Deferred pass renders all information needed for lighting (in built-in shaders: diffuse color, specular color, smoothness, world space normal, emission). It also adds lightmaps, reflection probes and ambient lighting into the emission channel. See Deferred Shading for details.

Legacy Deferred Lighting path

PrepassBase pass renders normals & specular exponent; PrepassFinal pass renders final color by combining textures, lighting & emissive material properties. All regular in-scene lighting is done separately in screen-space. See Deferred Lighting for details.

Legacy Vertex Lit Rendering path

Since vertex lighting is most often used on platforms that do not support programmable shaders, Unity can’t create multiple shader variants internally to handle lightmapped vs. non-lightmapped cases. So to handle lightmapped and non-lightmapped objects, multiple passes have to be written explicitly.

Vertex pass is used for non-lightmapped objects. All lights are rendered at once, using a fixed function OpenGL/Direct3D lighting model (Blinn-Phong)
VertexLMRGBM pass is used for lightmapped objects, when lightmaps are RGBM encoded (PC and consoles). No realtime lighting is applied; pass is expected to combine textures with a lightmap.
VertexLMM pass is used for lightmapped objects, when lightmaps are double-LDR encoded (mobile platforms). No realtime lighting is applied; pass is expected to combine textures with a lightmap.