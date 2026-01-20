# Day 10
### November 23, 2025

[Link to current edition of the game engine repository](https://github.com/SeaweedBrainn/GameEngineProject/tree/fb472a65bc69d289f11a4d6f91aba6eb4f5f316b)

Today I got the main game loop running and am getting a frame rate out of the thing. I continued following [BennyBox's tutorial playlist](https://youtube.com/playlist?list=PLEETnX-uPtBXP_B2yupUKlflXBznWIlL5&si=IXih23VENDWYb3b4) and completed till the #2 video. The tutorial is in Java but I am writing in C++ using GLFW.

> ### Window Class and OpenGL

First I created a Window class which is gonna manage the main window of the game engine. I created a static window as a private member, and I found that static member variables have to be initialized in the cpp file for the class, and cannot be mentioned by using the dot operator but need the scope resolution operator. For example: `GLFWwindow* Window::window = nullptr;`.

I learnt the basics of how GLFW works. You need to do `glfwInit();` to initialize GLFW. You can use GLFW hints to confugure the type of OpenGL context. A new `GLFWwindow*` can be created by `glfwCreateWindow(width, height, title.c_str(), nullptr, nullptr);` which returns a nullptr if window creation fails. Then we set the context associated with the window as the current one by `glfwMakeContextCurrent(window);`. An [OpenGL context](https://wikis.khronos.org/opengl/OpenGL_Context) stores all of OpenGL. I also disabled [V-sync](https://www.reddit.com/r/explainlikeimfive/comments/19fh3uj/comment/kjjokt6/?utm_source=share&utm_medium=web3x&utm_name=web3xcss&utm_term=1&utm_content=share_button) by `glfwSwapInterval(0);` (Value of 1 enables it). In all the entire process to create and initialize a new window looks like:
```
glfwInit();

glfwWindowHint(GLFW_CONTEXT_VERSION_MAJOR, 3);
glfwWindowHint(GLFW_CONTEXT_VERSION_MINOR, 3);
glfwWindowHint(GLFW_OPENGL_PROFILE, GLFW_OPENGL_CORE_PROFILE);
glfwWindowHint(GLFW_RESIZABLE, GL_FALSE);

window = glfwCreateWindow(width, height, title.c_str(), nullptr, nullptr);
glfwMakeContextCurrent(window);
glfwSwapInterval(0);  // 0 = disable V-Sync
```
For the rendering, you need to swap buffers and process events. Check the [GLFW docs](https://www.glfw.org/docs/latest/quick.html) to understand the theory behind both. The code for rendering thus looks like:
```
glfwPollEvents();
glfwSwapBuffers(window);
```
Usually you render until you press the close button, the status of which can be checked by `glfwWindowShouldClose(window);`.
Finally, when you have to stop rendering a window you terminate the glfw session by `glfwTerminate();`

> ### Time class and using the chrono library
I had to create a time class which gets the current time in nano seconds as well as holds 1 second in nano seconds as a static variable. It also holds the delta time variable which would be the time between two successive frames. You can get the current time by:
```
auto now = std::chrono::steady_clock::now();
return std::chrono::duration_cast<std::chrono::nanoseconds>(
  now.time_since_epoch()
).count();
```
Steady clock never goes backward and so is ideal for game engines. We take the current time point from the steady clock, cast it into nanoseconds and extract the raw integer number of nanoseconds.

> ### Main Component Class
This will be the main component of the game engine which handles the game loop. It would create a new main component object and start it. Inside the run method, the logic is as follows: While the game engine is running, calculate the time between your last render and your current render, this time is unprocessed and we need to process it to process multiple updates till we catch up to the expected frame rate or `frameTime` if it is possible. Then it renders once. So we are carrying out rendering once after multiple updates such that the game loop catches up. Since we set the frameCap to be 5000 (which is 5000 frames per second) so the loop is always playing catch up and we are never actually able to reach 5000 fps as the refresh rate of computers cannot be very low. 

> ### Game Class
This class is just a shell right now with the basic functionalities which will be implemented later.
