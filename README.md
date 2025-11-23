# Project Documentation

## 0. Library Installation on Linux

This project relies on several libraries. Some are bundled within the project (glad, imgui, stb_image), while others need to be installed on your system.

Based on `CMakeLists.txt`, you need to install the following dependencies:

*   **GLFW3**: For window creation and context management.
*   **Assimp**: For model loading.
*   **OpenGL**: Standard OpenGL libraries.

You can install these on Ubuntu/Debian-based systems using `apt`:

```bash
sudo apt-get update
sudo apt install cmake git build-essential libglfw3 libglfw3-dev libx11-dev libxrandr-dev libxi-dev libxxf86vm-dev libxcursor-dev libwayland-dev libxkbcommon-dev xorg-dev libgl1-mesa-dev mesa-common-dev mesa-utils doxygen graphviz libsoil-dev libglm-dev libassimp-dev libglew-dev libglfw3-dev libxinerama-dev libxcursor-dev libxi-dev
```

*Note: Standard OpenGL and system libraries (X11, dl, pthread, etc.) are usually available by default or installed with build-essentials/graphics drivers.*

## 1. How to use Shader class

The `Shader` class abstracts the details of compiling and linking vertex and fragment shaders.

**Include:**
```cpp
#include <rg/Shader.hpp>
```

**Initialization:**
Create a `Shader` object by providing paths to your vertex and fragment shader source files.

```cpp
Shader myShader("resources/shaders/vertex_shader.vs", "resources/shaders/fragment_shader.fs");
```

**Usage:**
To activate the shader program before drawing:
```cpp
myShader.use();
```

**Setting Uniforms:**
The class provides utility functions to set uniform variables in the shader.
```cpp
// Bool, Int, Float
myShader.setBool("useTexture", true);
myShader.setInt("texture1", 0);
myShader.setFloat("mixValue", 0.5f);

// Vectors
myShader.setVec3("lightColor", 1.0f, 1.0f, 1.0f);
myShader.setVec3("objectColor", glm::vec3(1.0f, 0.5f, 0.31f));

// Matrices
glm::mat4 projection = glm::perspective(glm::radians(45.0f), (float)width/(float)height, 0.1f, 100.0f);
myShader.setMat4("projection", projection);
```

## 2. How to use Model class

The `Model` class handles loading 3D models using the Assimp library. It processes meshes and textures automatically.

**Include:**
```cpp
#include <rg/Model.hpp>
```

**Initialization:**
Create a `Model` object by providing the path to the model file (e.g., .obj, .fbx).

```cpp
Model ourModel("resources/models/backpack.obj");
```

**Usage:**
To draw the model, you need to pass an active `Shader` object to the `Draw` function. Ensure the shader is active and has all necessary uniforms set (like view/projection matrices).

```cpp
myShader.use();
// ... set view/projection matrices ...
ourModel.Draw(myShader);
```

## 3. How to do the basic ImGui setup

This project bundles ImGui. To use it in your `main.cpp`, you need to initialize the context, setup the platform/renderer backends, and render the draw data.

**Includes:**
```cpp
#include <imgui.h>
#include <imgui_impl_glfw.h>
#include <imgui_impl_opengl3.h>
```

**Initialization (before render loop):**
```cpp
// Setup Dear ImGui context
IMGUI_CHECKVERSION();
ImGui::CreateContext();
ImGuiIO& io = ImGui::GetIO(); (void)io;

// Setup Dear ImGui style
ImGui::StyleColorsDark();

// Setup Platform/Renderer backends
ImGui_ImplGlfw_InitForOpenGL(window, true);
ImGui_ImplOpenGL3_Init("#version 330");
```

**Rendering (inside render loop):**
```cpp
while (!glfwWindowShouldClose(window)) {
    // ... other rendering code ...

    // Start the Dear ImGui frame
    ImGui_ImplOpenGL3_NewFrame();
    ImGui_ImplGlfw_NewFrame();
    ImGui::NewFrame();

    // Define UI elements
    ImGui::Begin("Hello, world!");
    ImGui::Text("This is some useful text.");
    ImGui::End();

    // Rendering
    ImGui::Render();
    ImGui_ImplOpenGL3_RenderDrawData(ImGui::GetDrawData());

    glfwSwapBuffers(window);
    glfwPollEvents();
}
```

**Cleanup (after render loop):**
```cpp
ImGui_ImplOpenGL3_Shutdown();
ImGui_ImplGlfw_Shutdown();
ImGui::DestroyContext();
```
