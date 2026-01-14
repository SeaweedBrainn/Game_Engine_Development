# Day 14
### Jan 3, 2026

[Link to current edition of the game engine repository](https://github.com/SeaweedBrainn/GameEngineProject/tree/0bd933039e030eb5aaf71d6a1ad39cca254777f4)

I added a Shader class to manage the shaders and a Transform class implementing a basic transformation translation matrix. I continued following [BennyBox's tutorial playlist](https://youtube.com/playlist?list=PLEETnX-uPtBXP_B2yupUKlflXBznWIlL5&si=IXih23VENDWYb3b4) and completed till the #12 video.

<hr />

> ### Shader Class
I changed the shader code from the Mesh class to having its own Shader Class, I am storing the program IDs in the shader class for now. So I can take in shader paths in, compile, and link the shaders into the shader program. Then when i have to use the program, I can bind the shader. I had to delete the copy constructor and the assignment operator for the class since I am storing a GLuint program ID, so if i do `shader = Shader(path)`, then the destructor for the temporary rvalue is called after generating the program ID, which deletes the program that was configured with the ID that shader is now holding. As a result after declaring a Shader variable, I can only add data in it by the member functions and then linking them. In the future it would be nice to implement move semantics.

<hr />

> ### Refactored Mesh Class and Transform Class
I added a `float* flattenedData` variable to the Matrix4f class as I need 1D flattened matrix to set a matrix4fv uniform in the shader class. I also added capabilities in the class to Inititialze a translation matrix if the (x,y,z) values are provided. <br /> <br />

I also created a Transform class that holds the translation values from the origin, I can set translation and then get the transformation matrix based on that translation from the class. I implemented it in the Game.cpp class to move mesh left and right. I also needed my vertex shader to have a uniform mat4 with which I multiplied my vertex coords. This mat4 was Transformation matrix I passed in the Game::Render method.
