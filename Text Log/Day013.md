# Day 13
### Jan 5, 2026

[Link to current edition of the game engine repository](https://github.com/SeaweedBrainn/GameEngineProject/tree/90ab1c2a98beb7b6c9f13a0707d2238168b42075)

Today I actually learnt how to use OpenGL to render a triangle, it involved using shaders and openGL objects. I followed the tutorial in [https://learnopengl.com](https://learnopengl.com). I followed the tutorials on the Hello Window and Hello Triangle pages. Everything was pretty straight forward and the website explained the entire procedure very clearly.

After learning how to render a triangle on my own using vertex data (and practicing that a million times), I integrated the same code in this project, for now my mesh class holds the shaders and all the initialization of the shader but I will change that later on. 

I was facing a small problem that the triangle was not rendering in this game engine and I realized later on that that was due to the direction I mentioned the vertices with, since we were setting the front face to be the face drawn clockwise and the back face was culled. Nothing more than that for today. Its honestly very fun lol.
