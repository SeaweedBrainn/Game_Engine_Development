# Day 2
I am following [this video by Bennybox](https://youtu.be/yt-0nObJZf8?si=W-0Nr4HTORfoo0-8) to open a window. He is using eclipse IDE with Java but I know C++. He is using lwjgl and slick-utl external libraries for Java to work with OpenGL. I found GLFW and GLAD as alternatives to lwjgl. I found Allegro as an alternative to slick-utl.

I tried installing [GLFW external library](https://www.glfw.org/download) in Clion but wasn't able to do because skill issue. I also tried in eclipse but could not do as well.

I finally shifted to Visual Studio and used [this youtube video](https://www.youtube.com/watch?v=7-dL6a5_B3I) to be able to integrate GLFW and GLAD in VS Code. I also installed [Allegro](https://github.com/liballeg/allegro_wiki/wiki/Quickstart) using the knowledge I had gained from the youtube video on how to work in the tasks.json file, but I don't know if it works yet since I didn't get a chance to use it. 

I also used ChatGPT's help to learn to reduce repetition in including all those dylib files in the tasks.json file. To include the all the dynamic libraries with a shorter syntax use `-L${workspaceFolder}/dependencies/library` and then list each dylib as `-lglfw.3.4` without using either `lib` or `dylib`.

