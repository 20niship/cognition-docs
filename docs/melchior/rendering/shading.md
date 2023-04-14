Lighting is essential in computer graphics. Scenes without lighting seem too  flat, making it hard to perceive the shape of objects. In here we will  explore the basic lighting and shading models.

A lighting model determines how light is reflected on a particular  point. The perceived colour on that point depends on a number of  parameters, for instance, the light direction, the viewer direction, the properties of the material, to name a few.

A shading model is related to how lighting models are used to lit a  surface. For instance, we can compute a single colour value per  triangle, **Flat** shading; compute the colour for the vertices of a triangle and interpolate the colour values for points inside the triangle, **Gouraud** shading; or even compute the colour for every surface point, **Phong** shading.

Lighting is deeply related to colour. When an object is lit we  observe colour, otherwise, if no light reaches an object looks  completely black.

Colour in CG is composed of several terms, namely:

- **diffuse**: light reflected by an object in every direction. This is what we commonly call the colour of an object.
- **ambient**: used to simulate bounced lighting. It  fills the areas where direct light can’t be found, thereby preventing  those areas from becoming too dark. Commonly this value is proportional  to the diffuse colour.
- **specular**: this is light that gets reflected more  strongly in a particular direction, commonly in the reflection of the  light direction vector around the surface’s normal. This colour is not  related to the diffuse colour.
- **emissive**: the object itself emits light.

The next figure shows the effect the first three color component when an object is lit. To define an object’s material we define values for  each of the above components.

[![materials](https://www.lighthouse3d.com/wp-content/uploads/2012/12/materials.jpg)](https://www.lighthouse3d.com/wp-content/uploads/2012/12/materials.jpg)

*Figure: From left to right: diffuse; ambient; diffuse + ambient; diffuse+ambient+specular*

Lights come in many packages as well. The most common light types, and easier to implement, are: **directional**, **point**, and **spotlights**.

In a **directional** light, we assume that all light rays are parallel, as if the light was placed infinitely far  away, and distance implied no attenuation. For instance, for all  practical purposes, for an observer on planet earth, the light that  arrives from the sun is directional. This implies that the light  direction is constant for all vertices and fragments, which makes this  the easiest type of light to implement.

**Point** lights spread their rays in all directions, just like an ordinary lamp, or even the sun if we were to model the solar system.

**Spotlights** are point lights that only emit light in a particular set of directions. A common approach is to  consider that the light volume is a cone, with its apex at the light’s  position. Hence, an object will only be lit if it is inside the cone.

![lights](https://www.lighthouse3d.com/wp-content/uploads/2012/12/lights.jpg)

*Figure: Light types. From left to right: directional, point, and spotlights.*

There are many approaches to compute the light that is reflected by  an object towards the camera, or eye. To compute this value for a  particular point we need to know where does the light come from, what is the angle between the light vector and the surface normal (a vector  perpendicular to the surface), where is the camera, and some other  settings. The equation that allows us to compute the colour, or  reflected light for a particular point dictates which settings will be  used and how.

As mentioned before our options are not limited to the lighting  equation. Considering a triangle we can compute the lighting for each  vertex, and then use interpolation for each fragment inside the  triangle. Or we can interpolate all the available vertex data, send it  to the fragment shader and then compute the colour per fragment. These  shading models are orthogonal to the lighting equations, in the sense  that we can combine any equation with any of the above shading models to lit our scene.

When fixed functionality was available, no need to write shaders,  lighting was computed per vertex. The colour of each vertex was then  send to the rasterization and interpolation phases where the colour was  interpolated for each computed fragment. The fixed functionality was an  implementation of the Gouraud shading model. The results were not that  great since interpolation is not the best way to compute lighting which  can vary in a multitude of ways inside a polygon. Imagine a spotlight  hitting the centre of a triangle, but none of its vertices. The result  would be a unlit triangle, which is clearly incorrect.

Gouraud shading made sense back when we typically had a lot more  pixels than vertices, and way less computing power. Nowadays we have a  lot more computing power, which in turn allows us to use larger and  larger numbers of polygons in a scene. These two features combined with  the poor results of lighting per vertex led to the current prevalent  trend of performing lighting per pixel, i.e. the Phong shading model.

All the above concentrates on the point being lit, the light sources  and the camera position. But what about shadows? That would imply  testing if any other object is in the path from the point being lit to  each light source. And why can’t other objects act as light sources  themselves, reflecting the light that hits them towards our point,  thereby contributing to its final colour? And refractions? Lighting is a very complex issue, some may even say that lighting is THE issue in  computer graphics.

Anyway, we have to start somewhere, so we are sticking with the  simple assumption that we are lighting a point, without considering any  interaction with the remaining geometry, only the camera and the light  sources. In the next sections we’re going to see how to write shaders to simulate all the above mentioned types of lights, both per vertex and  per pixel.

Source code for all the light types, and shading models, is available: [L3DLighting](https://www.lighthouse3d.com/wp-content/uploads/2012/12/L3DLighting1.zip). The zip file includes a Visual Studio 2010 project. To try the  different light/shading model combinations see function “setupShaders”



# Directional Light


[![lambert](https://www.lighthouse3d.com/wp-content/uploads/2012/12/lambert.jpg)](https://www.lighthouse3d.com/wp-content/uploads/2012/12/lambert.jpg)

To compute the intensity of the reflected light, assuming a Lambertian  diffuse reflection model, we compute the cosine between the vector  pointing towards the light (L) and the normal vector (N). The cosine can be computed based on the dot product:

![cos(\alpha) = \frac{L \cdot N}{ |L| \times |N|} ](https://s0.wp.com/latex.php?latex=cos%28%5Calpha%29+%3D+%5Cfrac%7BL+%5Ccdot+N%7D%7B+%7CL%7C+%5Ctimes+%7CN%7C%7D+&bg=ffffff&fg=000&s=0)

Hence, assuming that vectors *L* and *N* are  normalised, we can compute the cosine as the dot product between these  two vectors. The result of the dot product then gets multiplied by the  diffuse component of the object’s material, *Kd*. The lighting equation is:

![I = K_d \times cos(\alpha) = K_d \times (N \cdot L) ](https://s0.wp.com/latex.php?latex=I+%3D+K_d+%5Ctimes+cos%28%5Calpha%29+%3D+K_d+%5Ctimes+%28N+%5Ccdot+L%29+&bg=ffffff&fg=000&s=0)

In order to perform operations with multiple vectors, these must be in the same space. There are typically many [spaces](https://www.lighthouse3d.com/tutorials/glsl-tutorial/spaces-and-matrices/) where we can work on. Local space, world space, and camera space, are  the most common to work with vectors. Regarding lighting, we can define  the light’s direction, and other properties, in any of these spaces.  When we define a light in local, or model, space, it works as if the  light is attached to the object, like the light bulb in a desk lamp, as  in Luxo Jr. from Pixar. Defining a light in world space works as if the  light is placed in the 3D world we are building, regardless of the  camera or any object. Working with lights in camera space means that the light is defined relatively to the camera (the camera in this space is  placed at the origin, looking down on the negative Z axis). Whenever the camera moves, the light follows. A miner’s helmet lamp is an example of such light, if we consider the eyes of the miner to be the camera.

For shaders to work with multiple lights, potentially in multiple  spaces, it makes life easier for the shader programmer to assume that  all lights are defined in the same space. Camera space is a common  option. This implies pre-transforming the light’s properties from the  space in which they are defined to camera space.

Hence, we either transform the lights properties, such as direction  and position, in the application and send these values in camera space  to the shader, or we’ll have to consider where the light has been  defined and transform those properties accordingly inside the shader.

If the light is defined in world space, sending it as is to the  shader implies that it must be transformed inside the shader to camera  space, i.e. the vector representing the light’s direction (actually the  vector points towards the light) must be transformed by the [view matrix](https://www.lighthouse3d.com/tutorials/glsl-tutorial/spaces-and-matrices/). Another option is to assume that the application already provides this  data in camera space. When considering a light in world space the  light’s properties are commonly constant at least during the duration of a frame, and sometimes during the whole application. This second  approach implies a one time computation, at most per frame, on the  application side vs. performing the same transformation for each vertex, which can be in a very large number. In here we’re going to adopt the  second approach, the application providing the transformed light’s  properties to the shader. As mentioned before, the vectors must be  normalised in order to simplify the cosine computation, hence the  application will also be responsible for the normalisation of the  light’s vectors.

This is pretty simple if using the [VSMathLib](http://www.lighthouse3d.com/very-simple-libs/vsml/) and [VSShaderLib](http://www.lighthouse3d.com/very-simple-libs/vsshader-lib-very-simple-shader-library/). Assuming the light’s direction is stored in a variable named `l_dir` inside a block named `Lights`, the following snippet at the beginning of the rendering function should do the trick.

[?](https://www.lighthouse3d.com/tutorials/glsl-tutorial/directional-lights/#)

```cpp
// load the identity matrix
vsml->loadIdentity(VSMathLib::VIEW);
// set camera
vsml->lookAt(camX, camY, camZ, 0,0,0, 0,1,0);
 
float lightDir[4] = {1.0f, 1.0f, 1.0f, 0.0f}; // light dir in world space
float res[4]; // here we'll store the light's direction in camera space
// transform light to camera space, normalise it, and send to GLSL
vsml->multMatrixPoint(VSMathLib::VIEW, lightDir, res);
vsml->normalize(res);
shader.setBlockUniform("Lights", "l_dir", res);

```

Assuming that we’re going to work in camera space, the normals must  be transformed from local space to camera space. We are going to use the normal matrix for this purpose. 

In all shaders, the matrix `m_pvm` (`mat4`)  stands for a matrix that is computed as the multiplication of the  projection, view, and model matrices. The normal matrix is referred to  as `m_normal` (`mat3`).

The vertex shader must receive as inputs the position and normal of  each vertex, and output the computed colour. We also need the above  mentioned matrices and the direction towards the light, lightDir (vec4), and the diffuse colour of the material, diffuse (vec4).

Although it may look excessive in this first example, we’re going to use [uniform blocks](https://www.lighthouse3d.com/tutorials/glsl-tutorial/uniform-blocks/) for the matrices, material, and light properties as these will help to keep the code clean later on.

We are also going to use blocks for [intershader communication](https://www.lighthouse3d.com/tutorials/glsl-tutorial/inter-shader-communication/).

The vertex shader could be written as follows:

[?](https://www.lighthouse3d.com/tutorials/glsl-tutorial/directional-lights/#)

```
#version 330` `layout (std140) uniform Matrices {``  ``mat4 m_pvm;``  ``mat3 m_normal;``};` `layout (std140) uniform Materials {``  ``vec4 diffuse;``};` `layout (std140) uniform Lights {``  ``vec3 l_dir;  ``// camera space``};` `in vec4 position;  ``// local space``in vec3 normal;   ``// local space` `// the data to be sent to the fragment shader``out Data {``  ``vec4 color;``} DataOut;` `void` `main () {``  ``// transform normal to camera space and normalize it``  ``vec3 n = normalize(m_normal * normal);` `  ``// compute the intensity as the dot product``  ``// the max prevents negative intensity values``  ``float` `intensity = max(dot(n, l_dir), 0.0);` `  ``// Compute the color per vertex``  ``DataOut.color = intensity * diffuse;` `  ``// transform the vertex coordinates``  ``gl_Position = m_pvm * position; ``}
```

The output colour will be interpolated per fragment, based on the `gl_Position` values, and the resulting per fragment value will be fed to the  fragment shader. This shader only has to output the received colour.

[?](https://www.lighthouse3d.com/tutorials/glsl-tutorial/directional-lights/#)

```
#version 330` `in Data {``  ``vec4 color;``} DataIn;` `out vec4 outputF;` `void` `main() {` `  ``outputF = DataIn.color;``}
```

The following image shows this shaders in action:

![diffuse](https://www.lighthouse3d.com/wp-content/uploads/2012/12/diffuse.jpg)

The lit surfaces look nicely curved, but the surfaces facing away  from the light are completely dark. A simplistic solution to overcome  that is to add a little bit of ambient light. A simple implementation  adds the ambient term to the resulting colour.

![I = K_d \times (N \cdot L) + K_a ](https://s0.wp.com/latex.php?latex=I+%3D+K_d+%5Ctimes+%28N+%5Ccdot+L%29+%2B+K_a+&bg=ffffff&fg=000&s=0)

The ambient term is commonly a scaled down version of the diffuse  term. In the figure below the ambient term is actually a quarter of the  diffuse term. If this is the true for all materials then another option  to compute the colour is as follows:

![I = K_d \times (N \cdot L) + K_d \times 0.25 = K_d \times ((N \cdot L) + 0.25) ](https://s0.wp.com/latex.php?latex=I+%3D+K_d+%5Ctimes+%28N+%5Ccdot+L%29+%2B+K_d+%5Ctimes+0.25+%3D+K_d+%5Ctimes+%28%28N+%5Ccdot+L%29+%2B+0.25%29+&bg=ffffff&fg=000&s=0)

Depending on the material’s ambient setting, this can create very  bright pictures because in the areas where the model is lit the ambient  colour will also be added. Another option is to compute the maximum  between both components. Under this approach, the ambient term can be  seen as threshold providing a minimum level of lighting in those areas  where the model isn’t lit.

![I = max(K_d \times (N \cdot L), K_a) ](https://s0.wp.com/latex.php?latex=I+%3D+max%28K_d+%5Ctimes+%28N+%5Ccdot+L%29%2C+K_a%29+&bg=ffffff&fg=000&s=0)

The following images show how the above solutions look like in our case:

![diffuseambient](https://www.lighthouse3d.com/wp-content/uploads/2012/12/diffuseambient.jpg)

*Figure: left: diffuse only; midlle: diffuse + ambient; right: max(diffuse, ambient)*

In our shader we’ll go for the maximum. The vertex shader is very  similar, the following shows only the relevant bits, with the  differences highlighted:

[?](https://www.lighthouse3d.com/tutorials/glsl-tutorial/directional-lights/#)

```
layout (std140) uniform Materials {``  ``vec4 diffuse;``  ``vec4 ambient;``};` `...` `void` `main () {` `  ``...` `  ``// compute the color as the maximum between the two components``  ``DataOut.color = max(intensity * diffuse, ambient);` `  ``...``}
```

The fragment shader is the same as before.

Next we’ll deal with the material’s specular term.