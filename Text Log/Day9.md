# Day 9
### September 15, 2025

[Link to current edition of the game engine repository](https://github.com/SeaweedBrainn/GameEngineProject/tree/c6af6ca7a6389df22411291712acf677997509f0)

Soo today I'm off learning about conan and finally using it to install and manage external libraries in my project. So I started off by `brew install conan` in my Terminal because you need that to run conan. I found this beautiful [video](https://www.youtube.com/watch?v=U-_RbUqDSTc) going into basics for setting up a project with conan.

First off all I created a new project because I wanted to start from the beginning. I did the set up in it from the stuff I learnt yesterday, nothing special. My CMakeProject name aka the executable file is called just `GameEngine`

Then I made a conanfile.py and I learnt that using it better than using conanfile.txt since it offers much more versatility
<details> 
  <summary> Contents of my conanfile.py </summary>

  ```
from conan import ConanFile #Library to import

class GameEngineProject(ConanFile): #Make the class of the project name inherit from ConanFile
  generators = ("CMakeToolchain","CMakeDeps") #Generators needed for working with CMake
  settings = ("os","build_type","arch","compiler") #conan regenerates all files if any of these settings are changed

  def requirements(self): #Check conan recipes to see which requires to use
    self.requires("glfw/3.4") #glfw library
    self.requires("glew/2.2.0") #glew library to help glfw
    self.requires("glm/cci.20230113") #openGL math library
        
  def build_requirements(self):
    self.tool_requires("cmake/[>=3.25]") #Make sure people with above this CMake version can only use this
    
  def layout(self):
    self.folders.generators = "" #Optional
  ```
</details>

Then I created a conan profile which basically tells what all are the default settings for the project. I did this by going to the folder where the .conan2 folder is which was `~/.conan2`. where I ran the command `conan profile detect` which created the profile for me.

Then I ran `>Reload` again from the VS Code search bar. Then I went to the terminal, changed directory to my root folder of my project and ran `conan install . -sbuild_type=Debug -of=conan/deb --build=missing` which installed the packages yayy. The conan files were stored at the `conan/deb` folder.

I also went to `CMakePresets.json` and added a cache variable: `"CMAKE_TOOLCHAIN_FILE": "${workspaceFolder}/conan/deb/conan_toolchain.cmake"`

I went to `CMakeLists.txt` and added 
```
  find_package(GLFW3 REQUIRED)
  find_package(GLEW REQUIRED)
  find_package(GLM REQUIRED)

  target_link_libraries(${PROJECT_NAME} glfw GLEW::GLEW glm::glm glu::glu)
```

<details>
  <summary> My CMakeLists.txt atp </summary>

  ```
    cmake_minimum_required(VERSION 3.25.0) 
  project(GameEngine VERSION 0.1.0 LANGUAGES C CXX) 

  find_package(GLFW3 REQUIRED)
  find_package(GLEW REQUIRED)
  find_package(GLM REQUIRED)

  # Add all .cpp files in the src directory and its subdirectories
  file(GLOB_RECURSE CPP_SOURCES src/*.cpp)

  # Add all .h files in the src directory and its subdirectories
  file(GLOB_RECURSE HEADER_FILES src/*.h)

  add_executable(${PROJECT_NAME} src/main.cpp ${CPP_SOURCES} ${HEADER_FILES}) 

  target_link_libraries(${PROJECT_NAME} glfw GLEW::GLEW glm::glm glu::glu)

  set_property(TARGET ${PROJECT_NAME} PROPERTY CXX_STANDARD 17)
  ```
</details>

  It is always good to `>reload`, and Configure to see if everything is working smoothly.

  Then I got a [sample code](https://learnopengl.com/code_viewer.php?code=getting-started/hello-triangle-exercise1) and ran it to confirm my setup is working as intended

<hr />
> ### Note
Later on I looked up learnopengl.com and realized I should be using GLAD as a loader instead of GLEW since learnopengl.com uses GLAD. The process for it was a bit complicated and I documented it in [Day12.md](https://github.com/SeaweedBrainn/Game_Engine_Development/blob/d85a2acd418d9d61e0d066723d1e994f6ca46a22/Text%20Log/Day12.md).
