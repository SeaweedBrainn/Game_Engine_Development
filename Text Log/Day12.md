# Day 12
### Jan 3, 2026

[Link to current edition of the game engine repository](https://github.com/SeaweedBrainn/GameEngineProject/tree/5b5588517237bd800f97014a0c427984b3aa84a1)

Today I configured using the GLAD loader for openGL instead of GLEW. I also continued following [BennyBox's tutorial playlist](https://youtube.com/playlist?list=PLEETnX-uPtBXP_B2yupUKlflXBznWIlL5&si=IXih23VENDWYb3b4) and completed till the #8 video to try and setup the Mesh class.

<hr />

> ### Configuring GLAD using conan package manager
I had done the setup for GLFW and other libraries already, see [Day9.md](https://github.com/SeaweedBrainn/Game_Engine_Development/blob/4137a936b74d4ed1f557bebfeedd489a64a8d5f5/Text%20Log/Day9.md) for details on how to do it. Now I was just going through learnopengl.com and realized that they are using GLAD as a loader instead of GLEW which is what ill be using to learn openGL so might as well change it. To my conanfile.py I added the line `self.requires("glad/0.1.36")` under the `requirements(self)` function. I used the terminal and went into the directry where my project is and ran `conan install . -sbuild_type=Debug -of=conan/deb --build=missing`. I got an error that `Module Not Found: jinja2 couldn't be located`, GLAD has a depedency of jinja2 which is a python module. In the console I found that the version of python which the conan was using was located at `/opt/homebrew/Frameworks/Python.framework/Versions/3.13/bin/python3.13`, so just installing jinja2 on my mac using pip install was not working. I finally ran `/opt/homebrew/Frameworks/Python.framework/Versions/3.13/bin/python3.13 -m pip install --break-system-packages jinja2
` to install it at the correct location. The `--break-system-packages` part was necessary as without it I was getting errors and the module was not being installed.  Again I ran `conan install . -sbuild_type=Debug -of=conan/deb --build=missing` in the project directory and this time it worked. <br /><br />

I also had to be careful that in the conanfile.py I put in the 0.1.36 version of GLAD and not the latest 2.0.8. For all GLAD conan recipes check out [this link](https://conan.io/center/recipes/glad). This is because glad1 has the glad.h header file which we need but glad2 doesn't. After that I went into cmakelists.txt and added `find_package(GLAD REQUIRED)`, I also updated this statement, `target_link_libraries(${PROJECT_NAME} glfw GLEW::GLEW glm::glm glu::glu glad::glad)`. Note: If something breaks its always a good idea to delete the conan and build folders, re-run the conan build command, and re-cofigure cmake (see [Day9.md](https://github.com/SeaweedBrainn/Game_Engine_Development/blob/4137a936b74d4ed1f557bebfeedd489a64a8d5f5/Text%20Log/Day9.md) for details). After this now I can include GLAD in my C++ files by the header `<glad/glad.h>`. Always include GLAD before GLFW, this is because GLAD has some headers which are also included by GLFW causing errors.

<details>
    <summary>Cmakelists.txt</summary>
    
```
cmake_minimum_required(VERSION 3.25.0) 
project(GameEngine VERSION 0.1.0 LANGUAGES C CXX) 

find_package(GLFW3 REQUIRED)
find_package(GLEW REQUIRED)
find_package(GLM REQUIRED)
find_package(GLAD REQUIRED)

# Add all .cpp files in the src directory and its subdirectories
file(GLOB_RECURSE CPP_SOURCES src/*.cpp)

# Add all .h files in the src directory and its subdirectories
file(GLOB_RECURSE HEADER_FILES src/*.h)

add_executable(${PROJECT_NAME} ${CPP_SOURCES} ${HEADER_FILES}) 

target_link_libraries(${PROJECT_NAME} glfw GLEW::GLEW glm::glm glu::glu glad::glad)

set_property(TARGET ${PROJECT_NAME} PROPERTY CXX_STANDARD 17)  
```
</details>
<details>
    <summary>conanfile.py</summary>

```
from conan import ConanFile 

class GameEngineProject(ConanFile):
    generators = ("CMakeToolchain","CMakeDeps")
    settings = ("os","build_type","arch","compiler")

    def requirements(self):
        self.requires("glfw/3.4")
        self.requires("glew/2.2.0")
        self.requires("glm/1.0.1")
        self.requires("glad/0.1.36")
    
    def build_requirements(self):
        self.tool_requires("cmake/[>=3.25]")

    def layout(self):
        self.folders.generators = ""
```
</details>
<hr />

> ### Input class logic change
I changed the logic of the input class to be a bit more optimized than earlier just using static boolean arrays to store the current status of each key. Then every frame we compared the key status this frame and key status last frame to see if the key was newly pressed or released or not. I also moved the `Input::update()` below `game->input()` (which calls the `Input::getKeyDown()` function) in the maincomponent run loop so that the boolean arrays are refreshed only after its new state has been checked. In the `maincomponent::Render()` method, I moved `Window::Render()` below `game->render()` so that game renders the mesh first and then the window shows that.

> ### RenderUtil class
I created a class for rendering utilities which only has a `RenderUtil::clearScreen()` function for now. The function `glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);` can be used to clear the color and depth buffers by bitwise or. However, to use this function, GLAD has to be initialized when creating the context as its a glfw pointer function. I went into the Window.cpp class and added this after making the context current to initialize GLAD. 
```
if (!gladLoadGLLoader((GLADloadproc)glfwGetProcAddress)) {
        std::cerr << "Failed to initialize GLAD" << std::endl;
        std::exit(-1);
    }
```
To use the function I also needed to initialize the graphics (or use fallback defaults). So I created an `RenderUtil::initGraphics()` function and called it in the main function of the main component after creating the window. I called the `RenderUtil::clearScreen()` in the `MainComponent::Render()` function before everything else. It doesn't do anything for now. Here's some info about the InitGraphics function.
```
void RenderUtil::initGraphics()
{
    glClearColor(0.0f, 0.0f, 0.0f, 0.0f); //Clear color is set to black (RGBA)

    glFrontFace(GL_CW); //Define front face as the face drawn clockwise
    glCullFace(GL_BACK); //Define the back face to be the face to be culled
    glEnable(GL_CULL_FACE); //Cull the back face as we dont need to render the face which is not towards the camera
    glEnable(GL_DEPTH_TEST); //Enables using the z or the depth value for vertices not on the same deoth

    //TODO: Depth Clamp for later

    glEnable(GL_FRAMEBUFFER_SRGB); //Get free gamma correction
}
```
<hr />

> ### Vertex and Mesh Classes (and Utils)
Next I created a Vertex class to hold Vertex data, for now it only has a vector3f member, later on, itll have more info. It also has a static SIZE member variable which is the number of floats involved in a vertex (3 for now: x, y, and z). Then I created a Mesh class that's gonna draw the mesh. This class didn't work for me unfortunately since Bennybox was using OpenGL 1.0 and I am using OpenGL 3.0 which have some differences in how they are implement. I shall explain the logic for his method, then ill explain how it works in 3.0. <br /> <br />

The mesh class only has two member variables, `unsigned int vbo` and `int size`. vbo is the handle which stores like the pointer to array buffer data. You generate the buffer using `glGenBuffers(1, &vbo);`. I don't know yet what this means unfortunately. A list of vertices can be obtained in the form of `std::vector<vertex> vertices`, but will have to be converted into a flat array insert it into the buffer data. I created a new class Utils to handle this function, which output a buffer array `std::vector<float> buffer`. the handle vbo has to be bound by ` glBindBuffer(GL_ARRAY_BUFFER, vbo);`, now any buffer operations performed would happen on vbo. Then we do `glBufferData(GL_ARRAY_BUFFER, buffer.size() * sizeof(float), buffer.data(), GL_STATIC_DRAW);` to put the vertex data into the vbo. The second argument is the size of the buffer array in bytes. Finally to draw you once again bind vbo and then run `glDrawArrays(GL_TRIANGLES, 0, size);`. Here `size` is the number of vertices drawn, and 0 is the index of the element from where to start. This size is the same as the size member varaible of the class. However there are a few other functions which need to be called, which i did not really understand yet. My plan is to go through learnopengl.com to learn how to render a mesh using vertices and then change my logic here. From my preliminary research, I need another object called vao and I also need to write a fragment shader to be able to do anything. Heres my draw function which I called in the `Game::render()` method.
```
void Mesh::draw(){
    glEnableVertexAttribArray(0);

    glBindBuffer(GL_ARRAY_BUFFER, vbo);
    glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, Vertex::SIZE * sizeof(float), 0);

    glDrawArrays(GL_TRIANGLES, 0, size);

    glDisableVertexAttribArray(0);
}
```
