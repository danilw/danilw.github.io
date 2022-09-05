* * * * *

### Shader-Gallery only for good shaders (and shader-related study)

This is backup/copy of Shader-Gallery section from my Discord.

Main point of this - use case of linked shaders here must be not rare, or/and shader combine/show lots of techniques.

* * * * *

My blog posts with **basic** links:

[iq and Fabrice articles, glsl reference, and other basic links](https://github.com/danilw/danilw.github.io/tree/master/blog/Into_Shadertoy_and_Shaders_useful_links_and_tips)\
[about float/int bits/text/UI](https://arugl.medium.com/games-in-the-gpu-shaders-a912414b1894)

* * * * *

**Global illumination 3D:**\
https://www.shadertoy.com/view/4d3fWX - voxels Light Propagation \
https://www.shadertoy.com/view/WdlyWs, remake https://www.shadertoy.com/view/ssjczV - Voxel Cone Tracing or SDFGI

* * * * *

**Area lights 3D:**\
https://www.shadertoy.com/view/ldfGWs -  based on Brian Karis's Siggraph 2013\
https://eheitzresearch.wordpress.com/415-2/ - Polygonal-Light Shading with Linearly Transformed Cosines, there WebGL Demo link in bottom.\
**Analytic Area Light:**\
https://www.shadertoy.com/view/4dVGDt - square\
https://www.shadertoy.com/view/4tXSR4 - triangle

* * * * *

**Water 3D:**\
https://www.vertexshaderart.com/art/YdpjoX8LmWLnbtXQv - basic vertex water\
https://www.shadertoy.com/view/NlKGWK - best by performance optimized water, based on CC0/MIT water

* * * * *

**Complex production ready study/materials: (actually useful)**\
**The technical art of sea of thieve** - https://dl.acm.org/doi/10.1145/3214745.3214820 video(scroll down there) about Water, Clouds, vertex-Ropes\
**Graphics Studies Compilation** - http://www.adriancourreges.com/blog/2020/12/29/graphics-studies-compilation/ list of graphic study from modern games (I recommend to read Doom 2016, Witcher3, GTA5)

* * * * *

https://www.shadertoy.com/view/slKXWV Explicit Radiosity  by Mathis\
(I tried to adopt it(in few last days) for some use for my render, it way too comlex there it easier to rewrite everything from scratch, in this shader cubemap render UV of planes and use voxels for Radiosity lighting, thats all (also used mipmaps, very expensive for 32bit cubemap)))\
(I mean this is not "very useful shader" you can not just copy paste its code, use it just as an example)\
https://juretriglav.si/compressing-global-illumination-with-neural-networks/ - similar stuff but using NN for GI 

* * * * *

https://blog.demofox.org/2020/07/11/interpolating-data-over-arbitrary-shapes-with-laplaces-equation-and-walk-on-spheres/ Interpolating Data Over Arbitrary Shapes (shaders examples)

> in this blog post we talked about how to use **monte carlo integration and the walk on spheres algorithm to interpolate data between very arbitrary borders in any dimension.**

* * * * *

This Wicked Engine blog https://wickedengine.net/ has lots of good shader-related study\
Examples:\
http://www.joshbarczak.com/blog/?p=667 Geometry shaders are not making efficient use of the GPU hardware (blog post from 2015, nothing changed)\
https://wickedengine.net/2020/09/06/variable-rate-shading-first-impressions/ Variable Rate Shading\
https://wickedengine.net/2020/01/05/tile-based-optimization-for-post-processing/ Tile-based optimization for post processing

* * * * *

**Raytrace 3D quadratic surfaces:**\
https://www.shadertoy.com/view/ttBBRz - with correct UV\
https://www.shadertoy.com/view/ltKBzG - return only normal(and depth obviously), this code has few errors, I dont remember exact problems they visible when you rotate this shape in some angles. I fixed those errors in this shader(look iBilinearPatch function) https://www.shadertoy.com/view/NslGRN and added self-intersection support (still no UV)\
https://www.shadertoy.com/view/wlBcWc - example shader of complex surfaces, and interesting conversation in comments there\
https://www.shadertoy.com/view/ftsGz2 - Strange Jellyfish one more example of complex surface.\
https://www.shadertoy.com/view/XddfW7 - testing shader quartic roots algorithms from 3 people, also has functions to build quartic surface for few basic shapes(sphere/cube/torus/ellipse can be helpful as examples I think)\
https://www.shadertoy.com/view/flBfzm - similar as above, but works on 32bit floats without noise\
https://www.shadertoy.com/view/7tlBW8 - simple Quadrics \
https://www.shadertoy.com/view/fddBD2 - Generalized Ellipsoid intersection\
https://www.shadertoy.com/view/7dSBWz - Cyclides (more similar in mla shadertoy profile)\
Interesting point about raytracing Quad surface:

> Raymarching used to be described as a variant of Newton's method.
> It can certainly be described as finding the zeros of a function by using an estimate of the derivative - in the ray marching case,

* * * * *

CC0 shadertoy font to texture generator https://github.com/otaviogood/shader_fontgen\
Alternative/more complex MSDF https://github.com/Chlumsky/msdf-atlas-gen\

* * * * *

**Improved Normals from Depth**  https://www.shadertoy.com/view/fsVczR \
Original https://atyuwen.github.io/posts/normal-reconstruction \
My Godot port https://godotforums.org/discussion/30111/improved-normal-from-depth-shader

* * * * *

**Volex city-like generators, 2d/3d:**\
**3d:**\
https://www.shadertoy.com/view/NddBDM - The Infinite City III\
https://www.shadertoy.com/view/fldXWS - 2d Subdivision with 3-rd height\
https://www.shadertoy.com/view/fdyGDt - 3d Subdivision, for more look its author Tater profile\
**2d:**\
https://www.shadertoy.com/view/slXGDl - The Infinite Pattern\
https://www.shadertoy.com/view/NdcBzf - Dungeon Generator\
https://www.shadertoy.com/view/stdGz4 - 2d Subdivision\
https://www.shadertoy.com/view/sdBczK - 2d Subdivision, for more just search on Shadertoy "tag=subdiv"

My city https://www.shadertoy.com/view/Ntcyz7 - can be interesting as pathtracer implementaion of Voxel-city with interior.

* * * * *

https://www.shadertoy.com/view/ftcyW4 4D Cube

* * * * *

CSG but without raymarching, raytracing CSG\
https://www.shadertoy.com/view/Nl3yDN



