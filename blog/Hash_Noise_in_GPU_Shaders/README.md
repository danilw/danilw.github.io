# Hash Noise in GPU Shaders

Short summary of GPU-hash:

![](https://danilw.github.io/GLSL-howto/vulkan_sh_launcher/images_bugs/XcjGWw_hash_info.jpg)

Content:
--------

1.  Short notes-some info about image with information.
2.  **Main reason** why result of fract-hash is not same on GPU compare to CPU.
3.  How broken sin-hash.
4.  Good GPU-hash-functions links.
5.  Two examples of how GPU hash and noise is non-consistent.
6.  Links to gifs hash-Bugs-comparison.

Short notes to Summary-image:
=============================

Conistent:
----------

Means - result will be the same.\
And CPU-GPU and on different GPUs will be the same.

Example - in pixel `[0,0]` result of `hash([self_pos]+[0,1])` should be equal to result in pixel `[0,1]` of `hash([self_pos])`.\
Then this hash is Consistent.

Equal:
------

*Float-s generated from different sources can not be equal.*\
By "Equal" - I mean "very close".

Example - `0.12300001` generated by a hash in the`[0,0]` pixel *is equal* to `0.1230000101` generated in the`[0,1]` pixel.

Most stable, best hash and noise:
---------------------------------

Is texture - but "as hash" it bad idea to use texture because huge cost in time of texture-memory reading.\
For something like a height-map - use texture to have the same height-map on every GPU, and CPU side will have the same.

Texture as noise - is best noise.\
Because you can pre-generate very complex noise instead of *waiting for real-time procedural-noise generation,* which will be much slower than reading a pre-generated texture pixel.

Link to [Summary-image](https://www.shadertoy.com/view/4fsSRn).
-----------------------------------------------------------------------------------------------------------------

Main reason why result of fract-hash is not same on GPU compare to CPU:
=============================

Because result of:

```c
// CPU
floor((900./37.)*1000.)/1000. // = 24.323999

// GPU
floor((900./37.+min(iTime,0.))*1000.)/1000. // = 24.324001
```

Result of `floor((900./37.)*1000.)/1000.` is 24.323999 on CPU and 24.324001 on GPU.

And use of fract-hash just explode difference.

Look this shader [https://www.shadertoy.com/view/mdfSz8](https://www.shadertoy.com/view/mdfSz8)

-----------------------------------------

How broken sin-hash-noise:
==========================

![](https://danilw.github.io/GLSL-howto/vulkan_sh_launcher/images_bugs/stK3WG_comp.png)

fract-hash noise on left, right is sin-hash-noise <https://www.shadertoy.com/view/stK3WG>

sin-hash and noise are completely broken:
-----------------------------------------

Noise-alhorithm is - "interpolation of hash by generating/getting values of hash in neighbor pixels by adding neighbor shift to position".

And as you see on image - expected is what on left side smooth interpolation.\
*But because sin-hash can not generate "values of neighbor" for every pixel around - you see broken tiles, randomly broken.*

Good GPU hash:
==============

fract hash - <https://www.shadertoy.com/view/4djSRW>
----------------------------------------------------

Example of fract-hash:

```c
// not always needed, and not always fix
// #define FIX_FRACT_HASH 1000.

float hash12(vec2 p)
{
#ifdef FIX_FRACT_HASH
    p = sign(p)*(floor(abs(p))+floor(fract(abs(p))*FIX_FRACT_HASH)/FIX_FRACT_HASH);
#endif
    vec3 p3  = fract(vec3(p.xyx) * .1031);
    p3 += dot(p3, p3.yzx + 33.33);
    return fract((p3.x + p3.y) * p3.z);
}

```

![](https://danilw.github.io/GLSL-howto/vulkan_sh_launcher/images_bugs/4fSXDd.png)

*Example of broken fract hash* [https://www.shadertoy.com/view/4fSXDd](https://www.shadertoy.com/view/4fSXDd) *or* [https://www.shadertoy.com/view/lcBSWd](https://www.shadertoy.com/view/lcBSWd)


Notice - `#define FIX_FRACT_HASH` this is a "bad" fix I found to fix fract hash, look examples below, this fix is not always needed.\
*Only if you actually have a bugged visual look that comes from a hash - you can try this fix.*

int-hash - there multiple shaders with good int-hash:
-----------------------------------------------------

1.  <https://www.shadertoy.com/view/WttXWX> - FabriceNeyret2 "Best" Integer Hash\
This hash made by **Chris Wellons - [ Prospecting for Hash Functions](https://nullprogram.com/blog/2018/07/31/)**.
3.  <https://www.shadertoy.com/view/XlGcRh> - markjarzynski Comparing different hash functions for GPU Rendering. [Paper link.](http://www.jcgt.org/published/0009/03/02/)

*Remember that int-hash is multiple times slower than fract-hash.*\
If the source of random is float value - result of int-hash will be "non consistent" same as fract-hash so just use fract-hash.

Example int-hash code from FabriceNeyret2 shader:
*Hash by Chris Wellons -[ Prospecting for Hash Functions](https://nullprogram.com/blog/2018/07/31/).*

```c
#define hashi(x)   lowbias32(x)
#define hash(x)  ( float( hashi(x) ) / float( 0xffffffffU ) )

//bias: 0.17353355999581582 ( very probably the best of its kind )
uint lowbias32(uint x)
{
    x ^= x >> 16;
    x *= 0x7feb352dU;
    x ^= x >> 15;
    x *= 0x846ca68bU;
    x ^= x >> 16;
    return x;
}

```

int-seed-hash:
--------------

Use any int-hash and do instead of `uint x` as input - use `seed` that you generate with hash and do `seed++` after every hash.

Example from Godot Particle shader code:

```c
float rand_from_seed(inout uint seed) {
 int k;
 int s = int(seed);
 if (s == 0)
 s = 305420679;
 k = s / 127773;
 s = 16807 * (s - k * 127773) - 2836 * k;
 if (s < 0)
  s += 2147483647;
 seed = uint(s);
 return float(seed % uint(65536)) / 65535.0;
}

float rand_from_seed_m1_p1(inout uint seed) {
 return rand_from_seed(seed) * 2.0 - 1.0;
}

uint hash(uint x) {
 x = ((x >> uint(16)) ^ x) * uint(73244475);
 x = ((x >> uint(16)) ^ x) * uint(73244475);
 x = (x >> uint(16)) ^ x;
 return x;
}

// correct usage:
// uint seed = hash(some_INDEX); // particle index for example
// float random_hash = rand_from_seed(seed);
//... latter in code
// float random_hash2 = rand_from_seed(seed);

```

Noise:
------

Use texture-noise.

Examples of how Hash and procedural Noise is non consistent:
============================================================

Example 1 - City shader <https://www.shadertoy.com/view/Ntcyz7>
--------------------------------------------------------------

![](https://danilw.github.io/GLSL-howto/vulkan_sh_launcher/images_bugs/Ntcyz7_hash_bug.jpg)

<https://www.shadertoy.com/view/Ntcyz7>

A screenshot shows what is broken.

The logic of the shader is - I generate "height map - heigh of building and number of rooms in each building" in hash - very simple `get_heightField` function in Common around line 133.

To trigger a bug - look for`#define show_hash_bug` in Common.

Bug - some lighting in the building is "broken", like they have different heights in some parts of logic.\
I think, *can be wrong* - because some parts of logic being pre-calculated on CPU and those parts use the hash22 function.\
Look in Common`get_heightField` line `pos+=hash_zero_GPU;`

Fix - just forsing all calls to `get_heightField` to be on GPU by adding `hash_zero_GPU` that is `hash_zero_GPU=min(float(iFrame),0.);`

Replacing fract-hash with int-hash - bug still here:
----------------------------------------------------

![](https://danilw.github.io/GLSL-howto/vulkan_sh_launcher/images_bugs/Ntcyz7_Uhash_bug.jpg)

same bug with int-hash <https://www.shadertoy.com/view/Ntcyz7>

Look in Common `#define show_hash_bug_use_uint_random`

The ultimate fix in this City shadere case would be - just use a height-map in the texture.\
*Or - generate height-voxel-map from uint-hash once at shader code start - but it huge overhead generating entire voxel map in every pixel.*

Example 2 - Rough Seas shader <https://www.shadertoy.com/view/Xc23DW>
---------------------------------------------------------------------

*Note - I use the Rough Seas shader to show bug in my fork of original.*\
Original by Dave_Hoskins - <https://www.shadertoy.com/view/dtXGW4>

![](https://danilw.github.io/GLSL-howto/vulkan_sh_launcher/images_bugs/Xc23DW_1.jpg)

Water is vibile broken on screenshot <https://www.shadertoy.com/view/Xc23DW>

This screenshot from Nvidia-Vulkan.

`chrome.exe --use-angle=vulkan --enable-features=Vulkan,DefaultANGLEVulkan,VulkanFromANGLE`

If you see same bug as on screenshot - look `#define USE_FIXED_FLOAT_RANGE_FIX_HASH`

To test uint hash `#define TEST_UINT_HASH`\
And to see more int-hash bugs `#define MORE_UINT_HASH_BUGS`

![](https://danilw.github.io/GLSL-howto/vulkan_sh_launcher/images_bugs/Xc23DW_2.jpg)

with `#define TEST_UINT_HASH and #define MORE_UINT_HASH_BUGS`

*For this screenshot I move camera-mouse little left from the middle.*

It looks similar to what fract-hash shows.\
This can show that - if source of random is a float - any hash is not consistent.

*I made a comparison-gif of this Rough Seas shader on Nvidia-AMD GPUs, you can see the difference. Look below.*

Links to gifs hash-Bugs-comparison:
===================================

Made in this shader - <https://www.shadertoy.com/view/Xc23DW>\
*Comparison between Nvidia and AMD GPU visual results.*

On GIFs square on the left top - Nvidia is green - AMD is red.

1.  No changes to code - [original](https://danilw.github.io/GLSL-howto/vulkan_sh_launcher/images_bugs/Xc23DW_orig.gif) gif.
2.  With `#define USE_FIXED_FLOAT_RANGE_FIX_HASH` - [fixed gif](https://danilw.github.io/GLSL-howto/vulkan_sh_launcher/images_bugs/Xc23DW_float_fix.gif).\
    *Notice** - since used fract hash - if frach-hash were "consistent" - there should be no difference in screenshots, but even "pixel-hash pattern on water" is different, also "clouds on water" and some small geometry diferences.*
3.  With `#define TEST_UINT_HASH` - [uint hash result gif](https://danilw.github.io/GLSL-howto/vulkan_sh_launcher/images_bugs/Xc23DW_uint.gif).\
    *Notice* - same as above - is uint-hash were consistent images should be exact same, but they not exact same.
4.  With `#define TEST_UINT_HASH` and `#define USE_FIXED_FLOAT_RANGE_FIX_HASH` - [fixed uint gif](https://danilw.github.io/GLSL-howto/vulkan_sh_launcher/images_bugs/Xc23DW_uint_float_fix.gif).\
    *Notice* - huge line on middle is fixed because "float fix".\
    But "pixel noise" not exact same - still not consistent.
5.  With `#define TEST_UINT_HASH` and `#define MORE_UINT_HASH_BUGS` - just for fun [more bugs gif](https://danilw.github.io/GLSL-howto/vulkan_sh_launcher/images_bugs/Xc23DW_uint_more.gif).
6.  With `#define TEST_TEXTURE_NOISE `---[ texture gif](https://danilw.github.io/GLSL-howto/vulkan_sh_launcher/images_bugs/Xc23DW_texture.gif).\
    *Notice* - texture read with `textureLod` no mimpaps involved, only interpolation - and image still not exact same.
