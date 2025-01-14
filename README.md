# MinimalGL
PC Intro creation tool. It supports GLSL.

PC Intro is a category of mega-demo (demo scene) that generates sound and video with a small executable file of about several kilobytes that runs on a windows PC and competes in the content.
The PC Intro executable is required to work alone. It is forbidden to refer to external files or data on the network (the exception is the use of dlls that are standard equipment of the OS).
Both sound and video must be generated by some algorithm at runtime. And how to reduce the file size is important.

PC Intro has subcategories such as PC 1K Intro with file size less than 1024 bytes, PC 4K Intro with file size less than 4096 bytes, PC 8K Intro with file size less than 8192 bytes, PC 64K Intro with file size less than 65536 bytes.
MinimalGL is a tool that specializes in creating PC 4K Intros.


# screenshot
Exporting the created shader as an executable file.
![screen_shot_gfx](https://user-images.githubusercontent.com/11882108/82467562-c1ad0800-9afc-11ea-8582-5ef5dbdf2e0f.png)

The sound generated by the shader is being played while the waveform is being visualized.
![screen_shot_snd](https://user-images.githubusercontent.com/11882108/82468262-8fe87100-9afd-11ea-8f94-ebf531cb53be.png)

A user texture is read and used as a skybox.
* User textures cannot be used from exported executable files.
![screen_shot_user_texture](https://user-images.githubusercontent.com/11882108/83782228-7c770180-a6ca-11ea-8ab6-a051cbe10515.png)


# 準備

MinimalGL To use , you need the following tools:

- Microsoft Visual Studio  
	2019 以降を推奨。

- shader_minifier.exe と crinkler.exe  
	https://github.com/laurentlb/Shader_Minifier/releases  
	http://www.crinkler.net/  
	Copy it to the same directory as minimal_gl.exe or a directory in the path
(Placed in the same directory as minimal_gl.exe has priority).

#Usage

MinimalGL The simple flow of creating a PC 4K Intro using is as follows.

1. prepare shader file
Copy from the appropriate *.gfx.glsl *.snd.glsl file under example/.

2.Open graphics shader file
Drag and drop the \*.gfx.glsl file to the main window, or
Select File > Open Graphics Shader from the menu and load the \*.gfx.glsl file.

3. Open sound shader file
Drag and drop the \*.snd.glsl file to the main window, or
Select File > Open Sound Shader from the menu and load the \*.snd.glsl file.

4. Editing the shader
MinimalGL does not have a shader editing function.
Shader editing is done by directly rewriting the shader file with your own text editor.
When the timestamp of the currently drawing (playing) glsl file is updated,
It is immediately recompiled and reflected in the drawing result (playback result)

5. Export to executable
Select File > Export Executable from the menu.
Specify the executable file name to be generated in Output file and click OK.
If the export is successful, the file size will be displayed in a message box.

6. minify  
	If the file size is not less than 4096 bytes, file size reduction work (minify) is performed.
The minified shader code (\*.inl file) generated at the same time when the executable file is exported,
Shorten the shader code by referring to the compression result report (\*.crinkler_report.html).
Adjust the settings when exporting the executable file,
You can also reduce the file size by turning off unnecessary functions from the drawing settings ([Setup] → [Render Settings]).

7.Complete  
Just in case, please test the operation in the GPU environment of nVidia AMD Intel companies.


# Functions list

-Graphics drawing by shader
A single quadrilateral polygon is displayed on the entire screen and drawn by the fragment shader.

- Sound generation by shader
Sound generation by compute shader.

- shader hot reload
Automatically reloads shader files as soon as they are updated.
A mode that reloads without resetting the elapsed time is also available for live coding applications (select [Setup] → [Preference Settings] from the menu).

- Executable export
Exports the current graphics and sound content to an executable file.
shader code minify with shader_minifier (https://github.com/laurentlb/Shader_Minifier),
and executable file compression by crinkler (http://www.crinkler.net/) is applied.

- Project file export/import
Export the current state (current shader file name, camera position, drawing settings, export settings, etc.) to a project file.
You can restore the state by importing the project file.

- screenshot capture
Capture screenshots as png files in Unorm8 RGBA format.

- camera control
You can use the camera operation function with a mouse (use is optional).

- Cubemap capture
If you are using camera control,
You can capture the omnidirectional state seen from the current camera position as a cubemap to a dds file in FP32 RGBA format.

- sound capture
Save the sound generation result to wav file in float 2ch format.

- Save image sequence
Save the graphics generation result as a sequential png file in Unorm8 RGBA format.

- User texture
Any image file (currently only png and dds are supported) can be used as a texture.
Up to 4 textures can be registered. Note that this feature is not available on exported exes.

- Pause, slow play/slow rewind, fast forward/rewind


# List of functions related to graphics

- Use of backbuffer texture
The drawing result of the previous frame can be used as a texture.

- Multiple Render Targets (MRT)
Supports up to MRT4. Backbuffer textures can also be used for the number of MRTs.

- mipmap generation
Mipmap the backbuffer texture.

- LDR/HDR rendering
Supports rendering in LDR (Unorm8 RGBA) and HDR (FP16 FP32 RGBA).


# convert twigl shader to executable file

Shaders created with geeker_300_es or geeker_MRT from https://twigl.app/ can be ported to MinimalGL and exported to an executable file.
See the twigl compatible example under examples for details.

# troubleshooting

- works differently than twigl
Please check if any of the following items apply to you.

- using uniform float s
twigl.app's uniform float s is the average of the samples around the sound playback position,
MinialGL's twigl-compatible example is not averaged (due to processing load issues as it runs on-the-fly in the shader).
So the behavior of effects that depend on uniform float s is not compatible.

- contains undefined behavior
Such as code where the first argument of the pow function is negative.

- there is an uninitialized variable
- 
- Shader Minifier fails
Shader Minifier has the following limitations.

- Comma not allowed in ternary operator
The code below fails to parse.
```
i==0?a=0.,b=0.:0;
```
It should be modified as below.
```
if(i==0)a=0.,b=0.;
```

- Do not multiply vector types by 0
```
vec3a = vec3(1.) * .0;
```
Code like this
```
vec3 a =.0;
```
, and the float->vec3 conversion causes a shader compilation error.

- Restrictions on how to specify the number of array elements
The code below fails to parse.
```
float[4] a = float[](0.0, 1.0, 2.0, 3.0);
```
It should be modified as below.
```
float a[4] = float[](0.0, 1.0, 2.0, 3.0);
		```
# Limitations
MinimalGL has the following limitations.
- Windows exe only.
- Vertex shaders are not available.
- Sounds can only be generated by compute shaders.
# License
- of external origin
- Under src/external/cJSON
Follows the cJSON license.
Source: https://github.com/DaveGamble/cJSON
- under src/external/imgui
Follows imgui's license.
Source: https://github.com/ocornut/imgui
- Under src/external/stb
Follows stb's license.
Source: https://github.com/nothings/stb
- under src/GL/ and src/KHR/
Follow the license written in the source code.
Source: https://github.com/KhronosGroup/OpenGL-Registry
Source: https://github.com/skaslev/gl3w
- examples/ and below
The main function included in the twigl compatible sample code uses twigl's default shader.
Subject to the twigl license.
Source: https://github.com/doxas/twigl
- 上記以外  
	(C) Yosshin (aka 0x4015)  
	Apache License Version 2.0 が適用されます。  
	This software includes the work that is distributed in the Apache License 2.0
# 謝辞 Special Thanks
- Mentor/TBC and Blueberry/Loonies  
	The creators of Crinkler, a great compression tool.
- LLB/Ctrl-Alt-Test  
	The creator of Shader Minifier, a great minify tool.
