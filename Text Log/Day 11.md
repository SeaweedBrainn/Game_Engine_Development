# Day 11
### Jan 2, 2026

[Link to current edition of the game engine repository](https://github.com/SeaweedBrainn/GameEngineProject/tree/e0fab85f3beb98ecd9d6d3a200f281d1fc727187)

Today I got the input, vector, matrix, and quaternion classes setup. I continued following [BennyBox's tutorial playlist](https://youtube.com/playlist?list=PLEETnX-uPtBXP_B2yupUKlflXBznWIlL5&si=IXih23VENDWYb3b4) and completed till the #6 video.

<hr />

> ### Input Class implementation with GLFW
The input implementation involved setting up receiving the keyboard and mouse inputs from the user. I created functions to return true when a certain key is pressed, keys are recognized using their unique keycodes. Same goes for the mouse buttons. I also created functions that whether a key was pressed just this frame or released this frame. I used doubly linked lists `std::list<int>` to store the keys pressed which is highly unoptimized but fine for now. GLFW has functions `glfwGetKey(glfwWindow* window, int key)` and `glfwGetMouseButton(glfwWindow* window, int mouseButton)` that return `GLFW_PRESS` when a certain key or mouse button is held respectively. The function `glfwGetCursorPos (GLFWwindow *window, double *xpos, double *ypos)` can be used to get the mouse position relative to the top left corner of the window and store said coordinates in `(&xpos, &ypos)`. Check out the [docs](https://www.glfw.org/docs/latest/group__input.html#gadd341da06bc8d418b4dc3a3518af9ad2) for more info on these functions. <br /> <br />

The implementation of the `getKeyDown(int keyCode)` function involved first getting the status of all 256 keycodes currently and storing it an array every frame. Then in the next frame (next game update loop), only unique keys that were pressed (i.e. those keycodes which were not found in the array generated in the previous update loop) were added to the DownKey list. The opposite logic was used for the `getKeyDown(int keyCode)` function.

<hr />

> ### Vectors, Matrix, Quaternion classes and Operator Overloading
These classes would be used later for doing math operations in the game engine. I created a vector2f, a vector3f, a matrix4f, and a quaternion class with basic functionalities such as setters, getters, arithmetic, dot products, cross products, rotation etc. Most of the "complicated" functions just had direct formulas which you need theory to understand from. I used [Bennybox's companion playlist](https://youtube.com/playlist?list=PLEETnX-uPtBUG4iRqc6bEBv5uxMXswlEL) to understand Quaternions, although I'd prefer to do my own research on them too. <br /> <br />

It had been a long time since I worked with operator overloading in C++ so it was a nice refresh of memory. iostream operators like `>>` need to be declared as friend functions of the class instead of performing normal member function overloading. the iostream reference also needs to be returned back to perform chain input or output. Here are some operator overloading syntax:
```
friend std::ostream& operator<<(std::ostream& os, const Vector2f& vector2f);

Vector2f operator+(const Vector2f& other) const;
```
<hr />

> ### A note about static member variables and functions
I probably didn't mention this earlier, but a new thing I found out. Static member variables of a class need to be declared outside of the class once even if they are not initialized. Static member functions cannot be access by using the dot operator and need the scope resolution operator to be accessed instead.
If this is a class:
```
class Input{
    private:
        static std::list<int> keys;
    public:
        static void displayKeys();
};
```
Then you need to write `std::list<int> keys;` somewhere outside the class and to call the function you need to write `Input::displayKeys()`.
