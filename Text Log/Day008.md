# Day 8
### Sep 14, 2025

I've been on a big hiatus which is not nice. So I went on to youtube to learn how to set up a VS Code project by a [video](https://www.youtube.com/watch?v=qeEcV6u1kV4) by Visual Studio Code official youtube channel. Then I learnt how to setup a basic CMake project in VS Code by this [video](https://www.youtube.com/watch?v=_BWU5mWqVA4). Here's the stuff I learnt from the CMake Video.

<details>
  <summary> CMakeLists.txt file </summary>
  
  ```
  cmake_minimum_required(VERSION 3.10.0) #Describes the minimum CMake version required
  project(MyCMakeProject VERSION 0.1.0 LANGUAGES C CXX) #Define a project name and the languages used

  include(CTest) #Use this for testing purposes
  enable_testing() #Enable Testing

  add_executable(MyCMakeProject HelloWorld.cpp) #Add the executable file to the project

  set_property(TARGET MyCMakeProject PROPERTY CXX_STANDARD 17) #Set the target to the CMake project and specify version of C++ used
  ```
</details>

<details>
  <summary> CMakePresets.json file </summary>
  
  ```
  {
    "version": 3,
    "configurePresets": [
        {
            "name": "default",
            "description": "default settings for my cmake project",
            "hidden": false,
            "generator": "Unix Makefiles",
            "binaryDir": "${workspaceFolder}/build",
            "cacheVariables": {
                "CMAKE_BUILD_TYPE": "Debug",
                "CMAKE_EXPORT_COMPILE_COMMANDS": "YES"
            }
        }
    ]
}
  ```
  - `"name": "default"`: The name of the preset is default
  - `"generator": "Unix Makefiles"`: MacOS uses this generator
  - `"binaryDir": "${workspaceFolder}/build"`: Store build files in this directory
  - `"CMAKE_BUILD_TYPE": "Debug"`: Always specify some build type
</details>

After setup till this part is done by following the video, this what I did:
- In the search bar I hit `>reload` -> `Developer: Reload Window`
- I went to the CMake icon on the left side bar
- I went to search bar I hit `>target` -> `CMake: Set Build Target`
- An option popped up to select presets, I selected the `default` preset I had created
- Then I entered the project name `MyCMakeProject` and hit enter
- I hit `configure` and then `build` in the Project Status Menu
- I hit the play button in the bottom tool bar

