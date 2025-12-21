## The G-Buffer

The G-buffer is the collective term of all textures used to store lighting-relevant data for the final lighting pass. This includes:
- A position vector used to calculate the fragment position for lightDir and viewDir
- A colour vector know as albedo
- A normal vector
- A specular intensity float
- All light source position and colour vectors
- The player's view vector

With these vectors, we can calculate the Blinn-Phong lighting. There is no limit in OpenGL to what we can store in a texture, so it makes sense to store all per-fragment data in one or multiple screen-filled textures of the G-buffer and use these later in the lighting pass. As the G-buffer will have the same size as the lighting pass's 2D quad, we get the exact same fragment data as a forward rendering setting, but as as one-to-one mapping in the lighting pass.

```c++
while(true) {
	// 1. geometry pass: render all geometric/colour data to the g-buffer
	glBindFramebuffer(GL_FRAMEBUFFER, gBuffer);
	glClearColor(0.0, 0.0, 0.0, 1.0);
	glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER);
	gBufferShader.use();
	for(Object obj : Objects) {
		configureShader();
		obj.draw();
	}
	
	// 2. lighting pass: use g-buffer to calculate the scene's lighting
	glBindFramebuffer(GL_FRAMEBUFFER, 0);
	lightingPassShader.use();
	bindAllGBufferTextures();
	setLightingUniforms();
	renderQuad();
}
```

For the geometry pass, we initialise a framebuffer object called `gBuffer` that has multiple colour attachment buffers and a single depth renderbuffer object.
- For the position and normal texture, we use a high-precision texture (16 bit)
- For the albedo and specular texture, we can use the default texture precision

```c++
unsigned int gBuffer;
glGenFramebuffers(1, &gBuffer);
glBindFramebuffer(GL_FRAMEBUFFER, gBuffer);
unsigned int gPosition, gNormal, gColor;

// position colour buffer
glGenTextures(1, &gPosition);
glBindTexture(GL_TEXTURE_2D, gPosition);
glTexImage2D(GL_TEXTURE_2D, 0, GL_RGBA16F, windowWidth, windowHeight, 0, GL_RGBA, GL_FLOAT, NULL);
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MIN_FILTER, GL_NEAREST);
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MAG_FILTER, GL_NEAREST);
glFramebufferTexture2D(GL_FRAMEBUFFER, GL_COLOR_ATTACHMENT0, GL_TEXTURE_2D, gPosition, 0);

// normal colour buffer
glGenTextures(1, &gNormal);
glBindTexture(GL_TEXTURE_2D, gNormal);
glTexImage2D(GL_TEXTURE_2D, 0, GL_RGBA16F, windowWidth, windowHeight, 0, GL_RGBA, GL_FLOAT, NULL);
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MIN_FILTER, GL_NEAREST);
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MAG_FILTER, GL_NEAREST);
glFramebufferTexture2D(GL_FRAMEBUFFER, GL_COLOR_ATTACHMENT1, GL_TEXTURE_2D, gNormal, 0);

// colour colour buffer
glGenTextures(1, &gColor);
glBindTexture(GL_TEXTURE_2D, gColor);
glTexImage2D(GL_TEXTURE_2D, 0, GL_RGBA, windowWidth, windowHeight, 0, GL_RGBA, GL_UNSIGNED_BYTE, NULL);
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MIN_FILTER, GL_NEAREST);
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MAG_FILTER, GL_NEAREST);
glFramebufferTexture2D(GL_FRAMEBUFFER, GL_COLOR_ATTACHMENT2, GL_TEXTURE_2D, gColor, 0);
```

- We create three buffers associated with the gBuffer
- The colour and specular intensity are combined in a single RGBA texture, saving us from having to create another colour buffer texture.

Next, we have to render into the G-buffer.

```
#version 330 core

layout (location = 0) out vec4 gPosition;
layout (location = 1) out vec4 gNormal;
layout (location = 2) out vec4 gAlbedoSpec;

struct Material {
  sampler2D texture_specular1;
  sampler2D texture_diffuse1;
  float shininess;
};

in V_OUT {
  vec3 position;
  vec3 normal;
  vec2 texCoords;
} f_in;

uniform Material material;

void main() {    
  gPosition = f_in.position;
  gNormal = f_in.normal;
  gAlbedoSpec.rgb = texture(material.texture_diffuse1, f_in.texCoords).rgb;
  gAlbedoSpec.a = texture(material.texture_specular1, f_in.texCoords).r;
}
```

As we use multiple render targets, the layout specifier tells OpenGL to which colour buffer of the active framebuffer we render to.
