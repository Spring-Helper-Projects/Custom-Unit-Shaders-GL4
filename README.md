# Custom-Unit-Shaders-GL4
## GL4 compliant implementation of CUS
lua code is GPL v2, shaders are MIT as of 2025

Written by Beherith and ivand for BAR, made portable by FLOZi

Out of the box supplies unit and feature normal mapping, swaying tree features and also applies damage shaders to units which blend in the wreck texture and distorts the vertices. Also enables [Metal/Roughness PBR](https://blog.turbosquid.com/2023/07/27/an-intro-to-physically-based-rendering-material-workflows-and-metallic-roughness/) using Green and Blue channels of texture2 respectively, replacing Recoil default. With a little setup can be used to make animated tank treads.

## Adding To Your Game
If you use base content gadget handler you should just be able to drop the files in. If you have your own local copies of `callins.lua` and `gadgets.lua` you may need to update from base content.

## Using cus_defs.lua
Most game devs will not need to edit any file provided here other than [LuaRules/Configs/cus_defs.lua](LuaRules/Configs/cus_defs.lua). The file specifies several options at the top which can be enabled for each `uniformBin` specified in the first return table. You may wish to modify `defaultunit` but are unlikely to need to modify those for features. 

The uniform bin controls which [uniforms](https://www.khronos.org/opengl/wiki/Uniform_(GLSL)) are passed to the shaders.

### Recognised Options

`OPTION_SHADOWMAPPING` - Enables self shadowing

`OPTION_NORMALMAPPING` - Applies normalmapping

`OPTION_SHIFT_RGBHSV`	- currently a No-op due to performance concerns

`OPTION_VERTEX_AO` - Per vertex Ambient Occlusion

`OPTION_FLASHLIGHTS` - All emissive (texture2 red channel) will strobe in brightness

`OPTION_TREADS_U` - Treads that scroll left-right (texture U coord)

`OPTION_TREADS_V` - Treads that scroll up-down (texture V coord)

`OPTION_HEALTH_TEXTURING` - Gradually overlays wreck texture as unit gets damaged (units only)

`OPTION_HEALTH_DISPLACE` - Gradually bends vertices out of shape as unit gets damaged

`OPTION_HEALTH_TEXRAPTORS` - BAR specific; Progressive blood pooling on damaged raptors based on low heightmap values stored in alpha channel of normal map

`OPTION_MODELSFOG` - Applies linear fog effect to units based on zoom level

`OPTION_TREEWIND` - Makes objects sway gently in the breeze (i.e. for tree features)

`OPTION_PBROVERRIDE` - Forces Recoil default tex2 (non PBR) behaviour

### Recognised uniforms

`bitOptions` - Determines which of the above options are enabled for units assigned to this uniformBin

`baseVertexDisplacement` - If using `OPTION_HEALTH_DISPLACE` and `OPTION_HEALTH_TEXTURING` determines the starting distortion

`brightnessFactor` - How bright the units in this uniformBin will appear

`treadRect` - Defines the region (in pixels) on the texture that the tank treads belong to; left, top, width, height

`treadLinkWidth` - Defines the width (in pixels) of a single track link on the texture

`treadSpeedMult` - Allows to speed up or reverse direction (with negative values) tank tread 

## Recognised UnitDef & FeatureDef customParams

### Units & Features

`uniformbin` - Places the object into the defined uniform bin from the `uniformBins` table defined in [cus_defs.lua](LuaRules/Configs/cus_defs.lua).

`normaltex` - The filepath to the normal map texture

### Units only

`wrecktex1` - Overrides the wreck texture1 for this unit when using `OPTION_HEALTH_TEXTURING`, use this if it does not have an engine supported corpse, or you want to use a texture other than texture1 of the corpse model

`wrecktex2` - As above but for texture2

`wrecktexn` - As above but for normal map texture

`useskinning` - Enables skinning

### Features only

`treeshader` - Assigns to the `tree` uniform bin. Must be set to `'yes'`. Equivalent to using `customParams.uniformbin = 'tree'`. Kept for compatibility with BAR.

`cuspbr` - Assigns to the `featurepbr` uniform bin. Equivalent to using `customParams.uniformbin = 'featurepbr'`. Most features have texture2 designed for Spring/Recoil default shaders so PBR is disabled by default. If you make a new, non-wreck feature with a PBR texture2, use this. Kept for compatibility with BAR.
