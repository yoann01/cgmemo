# GUILTY GEAR Xrd -SIGN- The Secret of Using Real-time 3D Graphics Technology to Realize Animation Screen Rendering, Part 1 - 2

![zhuanlan.zhihu.com](https://zhuanlan.zhihu.com/p/552377973)

## lighting and shading (2)

In cell animation-like 2D graphics, the presence of "specular highlights" that appear due to specular reflections (highlights that depend on the line of sight) is often not very noticeable. However, in GUILTY GEAR Xrd -SIGN-, this specular highlight is also added to the lighting as a result of adjustment based on the "grammar for adding highlights in hand-drawn illustrations".

![](https://pic3.zhimg.com/80/v2-550f68339d08f73a08114f2a5bea2542_720w.webp)

So what is that grammar?

It's difficult to express it in one word, but to give some examples, like skin and clothes, even if they are close to each other, if the materials are different, the highlights will not stick together. It's not exactly correct for , and it's like highlighting along uneven boundaries or material boundaries.

![](https://pic4.zhimg.com/80/v2-e809a006f92f30c1b39177d9cc3a72db_720w.webp)

Specular highlights are controlled by the "ease of highlighting" parameter stored in the B (=blue) channel of the texture for lighting control. Since this parameter adjusts the intensity of the specular reflection calculation results, the maximum value results in burnt-in highlights, and conversely, the smaller the value, the more the highlights fade.

By the way, the R channel of the lighting control texture is the "specular highlight intensity" parameter, and a larger value is set for metal and smooth material parts

![](https://pic2.zhimg.com/80/v2-b55eef1acf93d43d63b686d62b09c125_720w.webp)

However, even with all these efforts, the development team was still not convinced. What is lacking in the aim of achieving a "perfect cel-animated taste"?

![](https://pic4.zhimg.com/80/v2-efa237ce6081c4e907d923905200a05b_720w.webp)

### Mr. Motomura:
It's "color". As a result of the toon shader, the light and dark are created, but I got the impression that the light and dark were somewhat monotonous. With a simple toon shader process that just multiplies the shaded areas with a single shade, I felt that the texture was lacking in persuasiveness and richness.

On the other hand, at the production site of TV animation, there is an artist who specializes in color design. So, the colors are set individually. I thought that the difference lies here.

Of course, it is not possible to make detailed individual settings like cell animation. Therefore, as a result of researching systematic implementation, the person in charge of color design for TV animation instinctively examines the "ambient light color of the scene" and "the light transmittance of the material to be expressed". , It seems that he arrived at the inference that he decided the color to be set. Mr. Motomura said that when he tried to implement it based on this reasoning, he was able to obtain a result that was quite close to the ideal, and decided to include it in the final specification.

The actual mechanism is not that complicated. First, prepare a texture with a distribution of "colors that are likely to appear when shaded" corresponding to the basic texture (Base Texture) applied to the 3D model. This texture is called "SSS texture" (SSS: SubSurface Scattering) for convenience within the development team.

If the pixel is shaded as a result of lighting, the color obtained by multiplying the value of this SSS texture and the "ambient light color" is determined as the shade color. On the other hand, if the lighting result is bright, the SSS texture value is ignored, so only the light source color is affected.

It seems that the development team was satisfied with this carving process, which brought it closer to a cel-animation-like coloring.

![](https://pic3.zhimg.com/80/v2-1e349deb287ca99992ae672c92e613c2_720w.webp)

Specifically, what kind of effect appears, for example, the shadow that appears on the character's skin color becomes slightly reddish. In addition, the color saturation of the clothes remains in the shade of the clothes. In other words, the SSS texture is composed of such "reddish" and "colors that retain the saturation of clothes".

The SSS texture doesn't simulate subsurface scattering, so the name may not be exactly correct (laughs). As a supplementary explanation, I think that the SSS texture simply represents "how much light passes through the material". The color of shadows on thin paper is light, isn't it? It is the texture of such an image.


## Contour secret (1)

Contour lines (line drawings) play a significant role in the elements that make up the anime-style visuals of GUILTY GEAR Xrd -SIGN-. In terms of the inking part in manga, GUILTY GEAR Xrd -SIGN- implements a combination of two approaches for this expression.

The "Back-Facing" method is used for the contour lines of the 3D model, which is the most basic line drawing.

Normally, when trying to draw a 3D model with a GPU, the polygons on the back side of the viewpoint are discarded as "invisible" and out of the drawing target. This mechanism is called "Backface Culling", which is based on the idea that "the polygons on the back side of the character model facing the front cannot be seen from the point of view anyway, so they are not drawn". .

On the other hand, in drawing with the back surface method, the rendering that reverses this mechanism of back surface culling is combined. In other words, "reverse" means to reverse the processing system that normally "does not draw the back", that is, "draws the back and does not draw the front".

![](https://pic3.zhimg.com/80/v2-30c13bd830b2b337b9dd3bfa0dfe344e_720w.webp)

To explain the flow of processing, as the first step, the 3D model is slightly expanded and reversed "back culling" is performed. As a result, a black silhouette of the 3D model is drawn, so save this for the time being.

As a second step, the 3D model is rendered at its original size using a normal processing system. Then, in the final stage, the black silhouette and the normal rendering result are combined. Most of the black silhouette is overwritten by normal rendering results, but the black silhouette is a slightly expanded 3D model, so only the outline remains as a result.

In the GUILTY GEAR Xrd -SIGN- rendering pipeline, Z-buffer advance is performed, so a nearly perfect outline can be obtained at the first stage. So I feel that the final synthesis phase is unnecessary, but the concept is like this.

![](https://pic4.zhimg.com/80/v2-035055a53d014be6522bffc384dc9173_720w.webp)

In fact, this method is a classic that has been used before the rise of programmable shader technology, but GUILTY GEAR Xrd -SIGN- makes full use of the vertex shader to implement an original extension of this classic method. are doing.

![](https://pic3.zhimg.com/80/v2-1c0e02dc28d967f24ffc5eadfb6dd0ba_720w.webp)

Arc System Works' own extensions were made to reproduce the hand-drawn drawing touch, as well as control parts that prevent lines from becoming too thin or too thick, regardless of camera zoom or character perspective. It is a control part that adds strength and weakness to the thickness of the line in the curved and straight parts. The line drawing of GUILTY GEAR Xrd -SIGN-, as if drawn by actually running the pen, is born from the control of such vertex shaders.

### Mr. Motomura:

I chose the back-face method because I felt that it was advantageous for the artist to freely control the strength of the line drawing. In the GUILTY GEAR Xrd -SIGN- 3D model, the vertex color has a "thickness control value when drawing lines", which allows you to add strength to the line drawing. With this, it's possible to create expressions like the ones you see in anime hand-drawn drawings, where the cheeks are thick and taper toward the chin.

![](https://pic4.zhimg.com/80/v2-f01ef0fe96215f0179605c0386d5bb63_720w.webp)

According to Mr. Motomura, the usage breakdown of vertex colors in GUILTY GEAR Xrd -SIGN- is as follows.

- [x] R: Offset for the shadow determination threshold. 1 is the standard, and areas that tend to be shadowed are darker. 0 always casts a shadow
- [x] G: Coefficient for how much the contour is expanded according to the distance from the camera
- [x] B: Z offset value of contour line
- [x] A: Contour thickness factor. 0.5 is standard, 1 is the maximum thickness, 0 is no outline

G and A are parameters related to outline thickness control. R is the hand-crafted ambient occlusion-like self-occlusion factor, which was also mentioned in Lighting and Shading (1). B is a coefficient that determines how much the depth direction (Z direction) is offset (=offset) with respect to the viewpoint when dilating using the back method. As a result, the outline can be erased. According to Mr. Motomura, it was a parameter that was included to prevent unwelcome contour lines such as wrinkles appearing in the hair and under the nose of the face.

![](https://pic4.zhimg.com/80/v2-67e495d2f9ee9999c6fd27883308da7b_720w.webp)

### Mr. Ishiwata:

The reason I didn't use a post-effect approach was because I thought it would be difficult to control the strength of the line thickness like this time. With this back-face line drawing mechanism, it is possible to adjust how the lines will appear on the actual machine from the stage of 3D model creation, which is performed by the artist, so we can create the model and how the outline will appear at the same time. Shin.

Mr. Ishiwatari's "post-effect approach" is to draw lines in real-time photo retouching with a pixel shader on the rendering result. Specifically, by detecting the depth price difference in the rendering result, or by detecting the step of the inner product value of the pixel unit line of sight (= line of sight vector) and the direction of the surface (= normal vector), the contour Determine line pixels. This method is often adopted when it is judged that the geometry load is too high with the back surface method.

![](https://pic3.zhimg.com/80/v2-82a32fda1ebb578c63d01e0d3469e1f2_720w.webp)

## Contour secret (2)

~New development!?

In GUILTY GEAR Xrd -SIGN-, it is said that another method of line drawing is added to the grooves, seams, and muscle ridges of clothes and accessories to express outlines.

Mr. Motomura:

There are places where outlines cannot be drawn with the back surface method, such as grooves in the 3D structure. There is no choice but to insert texture mapping in such places, but if texture mapping is done normally, jaggies will appear when the camera is zoomed in, and the difference from the beautiful outline by the back method will be noticeable. That's right. Therefore, I went back to the basics of "under what circumstances does jaggy appear in texture mapping?"

The result was a unique line-drawing technique known 　within the development team as the " Motomura-style line ."

![](https://pic2.zhimg.com/80/v2-dabeabf2b2e33b3a60e3f2016db0924d_720w.webp)

In the first place, jaggies in texture mapping become apparent when a certain texel (= pixel that constitutes texture) is drawn as a single texel on a polygon surface. On the other hand, if there are adjacent texels, unlike the case of a single texel, the outline of the square texel shape practically disappears, so the jaggy feeling is less likely to be exposed.

![](https://pic4.zhimg.com/80/v2-a803aed2dddf917b742e352d11e22e2b_720w.webp)

However, even if they are adjacent to each other, if they are diagonally above or below, the texels are virtually identical to a single state, resulting in a jaggy feeling. In other words, a set of pixels in which texels are arranged in horizontal or vertical lines can add a bokeh effect, but avoid the jaggy effect .

![](https://pic2.zhimg.com/80/v2-611c7f399bcb6b97379da05f8e29149d_720w.webp)

It's a matter of course, but when Mr. Motomura realized this, he created a `` texture that consists of only vertical and horizontal lines as solid lines that are given as contour lines'' . After that, when applying it to the 3D model, where you want diagonal lines and curves, apply distortion and bending so that it is mapped UV map (= where each polygon on the 3D model corresponds to the texture map data that expresses the

　Now, when you try texture mapping with this method, it's amazing. Beautiful and smooth line drawing was obtained even with textures of not so high resolution.

Below is the texture for the Motomura line that was actually used. Note that the texture for the Motomura line is stored in a single color on the α channel of the texture map for lighting control because only line drawing information is sufficient.

![](https://pic2.zhimg.com/80/v2-594284da12b3b3b933e1f650ae9b9241_720w.webp)

In the example of the Motomura line, you can see that it has a mysterious texture that looks like a city planning map, with only orthogonal line segments. It can be a contour line. The muscle protuberances have an elliptical hemispherical shape, and in the Motomura line, they are ``mapped into an elliptical border line of a square''.
　Since the square is elliptical, the area inside the square is stretched and distorted considerably. So, if there are characters or patterns inside this square, it will naturally be distorted, but since this texture is only for adding contour lines, such characters and patterns are not included. Since only lines are visible visually, the distortion caused by this method does not appear as a visual problem.

　Of course, texture mapping is done based on UVs that have been distorted and bent, and if the character or viewpoint moves, the texture will be affected by scaling and rotation. As a result, the line segments that are drawn are also affected by it and become curved or diagonal lines, but since bilinear filtering is applied in texture mapping, such curves and diagonal lines has a moderate degree of bokeh. And this is just the right anti-aliasing effect.

![](https://pic1.zhimg.com/80/v2-4b0a3911807c024ece4bf8c3ea4bbfc0_720w.webp)

　By the way, the strength and weakness of thickness can also be seen in the line segments drawn by the Motomura line. However, the texture itself for the Motomura line does not have such subtle line segment strength, and the line thickness is almost uniform. How is the strength of the drawn line added even though the thickness of the texture line is uniform?

!!!Mr. Motomura:
    The strength and weakness of the line drawn with the Motomura style line is added in the design of the UV map. If you want to make a thick line, you can devise a UV map so that the line representation texels on the texture are widely allocated to the polygon surface.

I have a feeling that this Motomura style line can be widely applied to game graphics other than anime touches, depending on how it is done.

![](https://pic4.zhimg.com/80/v2-7c38ab0776d0c7f248860c10c3a9c19f_720w.webp)

