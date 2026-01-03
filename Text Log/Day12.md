# Day 12
### Jan 3, 2026

[Link to current edition of the game engine repository]()

Today I configured using the GLAD loader for openGL instead of GLEW. I also continued following [BennyBox's tutorial playlist](https://youtube.com/playlist?list=PLEETnX-uPtBXP_B2yupUKlflXBznWIlL5&si=IXih23VENDWYb3b4) and completed till the #6 video.

<hr />

> ### Configuring GLAD using conan package manager
I had done the setup for GLFW and other libraries already, see [Day9.md](https://github.com/SeaweedBrainn/Game_Engine_Development/blob/4137a936b74d4ed1f557bebfeedd489a64a8d5f5/Text%20Log/Day9.md) for details on how to do it. Now I was just going through learnopengl.com and realized that they are using GLAD as a loader instead of GLEW which is what ill be using to learn openGL so might as well change it. To my conanfile.py I added the line `self.requires("glad/0.1.36")` under the `requirements(self)` function. I used the terminal and went into the directry where my project is and ran `conan install . -sbuild_type=Debug -of=conan/deb --build=missing`. I got an error that `Module Not Found: jinja2 couldn't be located`, GLAD has a depedency of jinja2 which is a python module. In the console I found that the version of python which the conan was using was located at `/opt/homebrew/Frameworks/Python.framework/Versions/3.13/bin/python3.13`, so just installing jinja2 on my mac using pip install was not working. I finally ran `/opt/homebrew/Frameworks/Python.framework/Versions/3.13/bin/python3.13 -m pip install --break-system-packages jinja2
` to install it at the correct location. The `--break-system-packages` part was necessary as without it I was getting errors and the module was not being installed.  Again I ran `conan install . -sbuild_type=Debug -of=conan/deb --build=missing` in the project directory and this time it worked. <br /><br />

I also had to be careful that in the conanfile.py I put in the 0.1.36 version of GLAD and not the latest 2.0.8. For all GLAD conan recipes check out [this link](https://conan.io/center/recipes/glad). This is because glad1 has the glad.h header file which we need but glad2 doesn't. After that I went into cmakelists.txt and added `find_package(GLAD REQUIRED)`, I also updated this statement, `target_link_libraries(${PROJECT_NAME} glfw GLEW::GLEW glm::glm glu::glu glad::glad)`. Note: If something breaks its always a good idea to delete the conan and build folders, re-run the conan build command, and re-cofigure cmake (see [Day9.md](https://github.com/SeaweedBrainn/Game_Engine_Development/blob/4137a936b74d4ed1f557bebfeedd489a64a8d5f5/Text%20Log/Day9.md) for details). After this now I can include GLAD in my C++ files by the header `<glad/glad.h>`.

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
