# Matrices in Computer Graphics

- [Matrices in Computer Graphics](#matrices-in-computer-graphics)
  - [Homogeneous coordinates](#homogeneous-coordinates)
  - [Transformation matrices](#transformation-matrices)
    - [An introduction to matrices](#an-introduction-to-matrices)
    - [Translation matrices](#translation-matrices)
    - [The Identity matrix](#the-identity-matrix)
    - [Scaling matrices](#scaling-matrices)
    - [Rotation matrices](#rotation-matrices)
    - [Cumulating transformations](#cumulating-transformations)
  - [The Model, View and Projection matrices](#the-model-view-and-projection-matrices)
    - [The Model matrix](#the-model-matrix)
    - [The View matrix](#the-view-matrix)
    - [The Projection matrix](#the-projection-matrix)
    - [Cumulating transformations : the ModelViewProjection matrix](#cumulating-transformations--the-modelviewprojection-matrix)

> The engines don’t move the ship at all. The ship stays where it is and the engines move the universe around it.
>
> Futurama

**This is the single most important tutorial of the whole set. Be sure to read it at least eight times.**

## Homogeneous coordinates

Until then, we only considered 3D vertices as a (x,y,z) triplet. Let’s introduce w. We will now have (x,y,z,w) vectors.

This will be more clear soon, but for now, just remember this :

- If w == 1, then the vector (x,y,z,1) is a position in space.
- If w == 0, then the vector (x,y,z,0) is a direction.

> (In fact, remember this forever.)

What difference does this make ? Well, for a rotation, it doesn’t change anything. When you rotate a point or a direction, you get the same result. However, for a translation (when you move the point in a certain direction), things are different. What could mean “translate a direction” ? Not much.

Homogeneous coordinates allow us to use a single mathematical formula to deal with these two cases.

## Transformation matrices

### An introduction to matrices

Simply put, a matrix is an array of numbers with a predefined number of rows and colums. For instance, a 2x3 matrix can look like this :

$$\begin{bmatrix}
2 & 5 & 7\\
9 & 8 & 1
\end{bmatrix}$$

In 3D graphics we will mostly use 4x4 matrices. They will allow us to transform our (x,y,z,w) vertices. This is done by multiplying the vertex with the matrix :

$$\begin{bmatrix}
a & b & c & d\\
e & f & g & h\\
i & j & k & l\\
m & n & o & p
\end{bmatrix}\times
\begin{bmatrix}
x\\
y\\
z\\
v
\end{bmatrix}=
\begin{bmatrix}
ax + by + cz + dw\\
ex + fy + gz + hw\\
ix + jy + kz + lw\\
mx + ny + oz + pw
\end{bmatrix}
$$

> **Matrix x Vertex (in this order !!) = TransformedVertex**

This isn’t as scary as it looks. Put your left finger on the a, and your right finger on the x. This is ax. Move your left finger to the next number (b), and your right finger to the next number (y). You’ve got by. Once again : cz. Once again : dw. ax + by + cz + dw. You’ve got your new x ! Do the same for each line, and you’ll get your new (x,y,z,w) vector.

Now this is quite boring to compute, an we will do this often, so let’s ask the computer to do it instead.

**In C++, with GLM:**

```cpp
    glm::mat4 myMatrix;
    glm::vec4 myVector;
    // fill myMatrix and myVector somehow
    glm::vec4 transformedVector = myMatrix * myVector; // Again, in this order ! this is important.
```

**In GLSL :**

```c
    mat4 myMatrix;
    vec4 myVector;
    // fill myMatrix and myVector somehow
    vec4 transformedVector = myMatrix * myVector; // Yeah, it's pretty much the same than GLM
    // ( have you cut’n pasted this in your code ? go on, try it)
```

### Translation matrices

These are the most simple tranformation matrices to understand. A translation matrix look like this :

$$\begin{bmatrix}
1 & 0 & 0 & X\\
0 & 1 & 0 & Y\\
0 & 0 & 1 & Z\\
0 & 0 & 0 & 1
\end{bmatrix}$$

where X,Y,Z are the values that you want to add to your position.

So if we want to translate the vector (10,10,10,1) of 10 units in the X direction, we get :

$$\begin{bmatrix}
1 & 0 & 0 & 10\\
0 & 1 & 0 & 0\\
0 & 0 & 1 & 0\\
0 & 0 & 0 & 1
\end{bmatrix}\times
\begin{bmatrix}
10 \\
10 \\
10 \\
1\end{bmatrix}=
\begin{bmatrix}
1 * 10 + 0 * 10 + 0 * 10 + 10 * 1\\
0 * 10 + 1 * 10 + 0 * 10 + 0 * 1\\
0 * 10 + 0 * 10 + 1 * 10 + 0 * 1\\
0 * 10 + 0 * 10 + 0 * 10 + 1 * 1
\end{bmatrix}=
\begin{bmatrix}
10 + 0 + 0 + 10\\
0 + 10 + 0 + 0\\
0 + 0 + 10 + 0\\
0 + 0 + 0 + 1
\end{bmatrix}=
\begin{bmatrix}
20 \\
10 \\
10 \\
1\end{bmatrix}
$$

… and we get a (20,10,10,1) homogeneous vector ! **Remember:** the 1 means that it is a position, not a direction. So our transformation didn’t change the fact that we were dealing with a position, which is good.

Let’s now see what happens to a vector that represents a direction towards the -z axis : (0,0,-1,0)

$$\begin{bmatrix}
1 & 0 & 0 & 10\\
0 & 1 & 0 & 0\\
0 & 0 & 1 & 0\\
0 & 0 & 0 & 1
\end{bmatrix}\times
\begin{bmatrix}
0 \\
0 \\
-1 \\
0\end{bmatrix}=
\begin{bmatrix}
1 * 0 + 0 * 0 + 0 * 0 + 10 * 0\\
0 * 0 + 1 * 0 + 0 * 0 + 0 * 0\\
0 * -1 + 0 * -1 + 1 * -1 + 0 * -1\\
0 * 0 + 0 * 0 + 0 * 0 + 1 * 0
\end{bmatrix}=
\begin{bmatrix}
0 + 0 + 0 + 0\\
0 + 0 + 0 + 0\\
0 + 0 + -1 + 0\\
0 + 0 + 0 + 0
\end{bmatrix}=
\begin{bmatrix}
0 \\
0 \\
-1 \\
0\end{bmatrix}
$$

… ie our original (0,0,-1,0) direction, which is great because as I said ealier, moving a direction does not make sense.

So, how does this translate to code ?

In C++, with GLM:

```cpp
    #include <glm/gtx/transform.hpp> // after <glm/glm.hpp>
 
    glm::mat4 myMatrix = glm::translate(glm::mat4(), glm::vec3(10.0f, 0.0f, 0.0f));
    glm::vec4 myVector(10.0f, 10.0f, 10.0f, 0.0f);
    glm::vec4 transformedVector = myMatrix * myVector; // guess the result
```

In GLSL :

```c
    vec4 transformedVector = myMatrix * myVector;
```

Well, in fact, you almost never do this in GLSL. Most of the time, you use glm::translate() in C++ to compute your matrix, send it to GLSL, and do only the multiplication :

### The Identity matrix

This one is special. It doesn’t do anything. But I mention it because it’s as important as knowing that multiplying A by 1.0 gives A.

$$\begin{bmatrix}
1 & 0 & 0 & 0\\
0 & 1 & 0 & 0\\
0 & 0 & 1 & 0\\
0 & 0 & 0 & 1
\end{bmatrix}\times
\begin{bmatrix}
x \\
y \\
z \\
w\end{bmatrix}=
\begin{bmatrix}
1 * x + 0 * y + 0 * z + 0 * w\\
0 * x + 1 * y + 0 * z + 0 * w\\
0 * x + 0 * y + 1 * z + 0 * w\\
0 * x + 0 * y + 0 * z + 1 * w
\end{bmatrix}=
\begin{bmatrix}
x + 0 + 0 + 0\\
0 + y + 0 + 0\\
0 + 0 + z + 0\\
0 + 0 + 0 + w
\end{bmatrix}=
\begin{bmatrix}
x \\
y \\
z \\
w\end{bmatrix}
$$

In C++ :

```cpp
    glm::mat4 myIdentityMatrix = glm::mat4(1.0f);
```

### Scaling matrices

Scaling matrices are quite easy too :

$$\begin{bmatrix}
x & 0 & 0 & 0\\
0 & y & 0 & 0\\
0 & 0 & z & 0\\
0 & 0 & 0 & 1
\end{bmatrix}$$

So if you want to scale a vector (position or direction, it doesn’t matter) by 2.0 in all directions :

$$\begin{bmatrix}
2 & 0 & 0 & 0\\
0 & 2 & 0 & 0\\
0 & 0 & 2 & 0\\
0 & 0 & 0 & 1
\end{bmatrix}\times
\begin{bmatrix}
x \\
y \\
z \\
w\end{bmatrix}=
\begin{bmatrix}
2 * x + 0 * y + 0 * z + 0 * w\\
0 * x + 2 * y + 0 * z + 0 * w\\
0 * x + 0 * y + 2 * z + 0 * w\\
0 * x + 0 * y + 0 * z + 1 * w
\end{bmatrix}=
\begin{bmatrix}
2 * x + 0 + 0 + 0\\
0 + 2 * y + 0 + 0\\
0 + 0 + 2 * z + 0\\
0 + 0 + 0 + 1 * w
\end{bmatrix}=
\begin{bmatrix}
2x \\
2y \\
2z \\
w\end{bmatrix}
$$

and the w still didn’t change. You may ask : what is the meaning of “scaling a direction” ? Well, often, not much, so you usually don’t do such a thing, but in some (rare) cases it can be handy.

(notice that the identity matrix is only a special case of scaling matrices, with (X,Y,Z) = (1,1,1). It’s also a special case of translation matrix with (X,Y,Z)=(0,0,0), by the way)

In C++ :

```cpp
    // Use #include <glm/gtc/matrix_transform.hpp> and #include <glm/gtx/transform.hpp>
    glm::mat4 myScalingMatrix = glm::scale(2.0f, 2.0f ,2.0f);
```

### Rotation matrices

These are quite complicated. I’ll skip the details here, as it’s not important to know their exact layout for everyday use. For more information, please have a look to the Matrices and Quaternions FAQ (popular resource, probably available in your language as well). You can also have a look at the Rotations tutorials

In C++ :

```cpp
    // Use #include <glm/gtc/matrix_transform.hpp> and #include <glm/gtx/transform.hpp>
    glm::vec3 myRotationAxis( ??, ??, ??);
    glm::rotate( angle_in_degrees, myRotationAxis );
```

### Cumulating transformations

So now we know how to rotate, translate, and scale our vectors. It would be great to combine these transformations. This is done by multiplying the matrices together, for instance :

```cpp
    TransformedVector = TranslationMatrix * RotationMatrix * ScaleMatrix * OriginalVector;
```

**!!! BEWARE !!!** This lines actually performs the scaling FIRST, and THEN the rotation, and THEN the translation. This is how matrix multiplication works.

Writing the operations in another order wouldn’t produce the same result. Try it yourself :

- make one step ahead ( beware of your computer ) and turn left;
- turn left, and make one step ahead

As a matter of fact, the order above is what you will usually need for game characters and other items : Scale it first if needed; then set its direction, then translate it. For instance, given a ship model (rotations have been removed for simplification) :

- The wrong way :
  - You translate the ship by (10,0,0). Its center is now at 10 units of the origin.
  - You scale your ship by 2. Every coordinate is multiplied by 2 relative to the origin, which is far away… So you end up with a big ship, but centered at 2*10 = 20. Which you don’t want.
- The right way :
  - You scale your ship by 2. You get a big ship, centered on the origin.
  - You translate your ship. It’s still the same size, and at the right distance.

Matrix-matrix multiplication is very similar to matrix-vector multiplication, so I’ll once again skip some details and redirect you the the Matrices and Quaternions FAQ if needed. For now, we’ll simply ask the computer to do it :

in C++, with GLM :

```cpp
  glm::mat4 myModelMatrix = myTranslationMatrix * myRotationMatrix * myScaleMatrix;
  glm::vec4 myTransformedVector = myModelMatrix * myOriginalVector;
```

in GLSL :

```c
  mat4 transform = mat2 * mat1;
  vec4 out_vec = transform * in_vec;
```

## The Model, View and Projection matrices

For the rest of this tutorial, we will suppose that we know how to draw Blender’s favourite 3d model : the monkey Suzanne.

The Model, View and Projection matrices are a handy tool to separate transformations cleanly. You may not use this (after all, that’s what we did in tutorials 1 and 2). But you should. This is the way everybody does, because it’s easier this way.

### The Model matrix

This model, just as our beloved red triangle, is defined by a set of vertices. The X,Y,Z coordinates of these vertices are defined relative to the object’s center : that is, if a vertex is at (0,0,0), it is at the center of the object.

(IMAGE1)

We’d like to be able to move this model, maybe because the player controls it with the keyboard and the mouse. Easy, you just learnt do do so : ```translation*rotation*scale```, and done. You apply this matrix to all your vertices at each frame (in GLSL, not in C++!) and everything moves. Something that doesn’t move will be at the center of the world.

(IMAGE2)

Your vertices are now in World Space. This is the meaning of the black arrow in the image below : We went from Model Space (all vertices defined relatively to the center of the model) to World Space (all vertices defined relatively to the center of the world).

(IMAGE3)

We can sum this up with the following diagram :

(IMAGE4)

### The View matrix

Let’s quote Futurama again :

> The engines don’t move the ship at all. The ship stays where it is and the engines move the universe around it.

(IMAGE5)

When you think about it, the same applies to cameras. It you want to view a moutain from another angle, you can either move the camera… or move the mountain. While not practical in real life, this is really simple and handy in Computer Graphics.

So initially your camera is at the origin of the World Space. In order to move the world, you simply introduce another matrix. Let’s say you want to move your camera of 3 units to the right (+X). This is equivalent to moving your whole world (meshes included) 3 units to the LEFT ! (-X). While you brain melts, let’s do it :

```cpp
  // Use #include <glm/gtc/matrix_transform.hpp> and #include <glm/gtx/transform.hpp>
  glm::mat4 ViewMatrix = glm::translate(glm::mat4(), glm::vec3(-3.0f, 0.0f ,0.0f));
```

Again, the image below illustrates this : We went from World Space (all vertices defined relatively to the center of the world, as we made so in the previous section) to Camera Space (all vertices defined relatively to the camera).

(IMAGE6)

Before you head explodes from this, enjoy GLM’s great glm::lookAt function:

```cpp
glm::mat4 CameraMatrix = glm::lookAt(
    cameraPosition, // the position of your camera, in world space
    cameraTarget,   // where you want to look at, in world space
    upVector        // probably glm::vec3(0,1,0), but (0,-1,0) would make you looking upside-down, which can be great too
);
```

Here’s the compulsory diagram :

(IMAGE7)

This is not over yet, though.

### The Projection matrix

We’re now in Camera Space. This means that after all theses transformations, a vertex that happens to have x==0 and y==0 should be rendered at the center of the screen. But we can’t use only the x and y coordinates to determine where an object should be put on the screen : its distance to the camera (z) counts, too ! For two vertices with similar x and y coordinates, the vertex with the biggest z coordinate will be more on the center of the screen than the other.

This is called a perspective projection :

(IMAGE8)

And luckily for us, a 4x4 matrix can represent this projection:

```cpp
  // Generates a really hard-to-read matrix, but a normal, standard 4x4 matrix nonetheless
  glm::mat4 projectionMatrix = glm::perspective(
    glm::radians(FoV), // The vertical Field of View, in radians: the amount of "zoom". Think "camera lens". Usually between 90° (extra wide) and 30° (quite zoomed in)
    4.0f / 3.0f,       // Aspect Ratio. Depends on the size of your window. Notice that 4/3 == 800/600 == 1280/960, sounds familiar ?
    0.1f,              // Near clipping plane. Keep as big as possible, or you'll get precision issues.
    100.0f             // Far clipping plane. Keep as little as possible.
);
```

One last time :

We went from Camera Space (all vertices defined relatively to the camera) to Homogeneous Space (all vertices defined in a small cube. Everything inside the cube is onscreen).

And the final diagram :

(IMAGE9)

Here’s another diagram so that you understand better what happens with this Projection stuff. Before projection, we’ve got our blue objects, in Camera Space, and the red shape represents the frustum of the camera : the part of the scene that the camera is actually able to see.

(IMAGE10)

Multiplying everything by the Projection Matrix has the following effect :

(IMAGE11)

In this image, the frustum is now a perfect cube (between -1 and 1 on all axes, it’s a little bit hard to see it), and all blue objects have been deformed in the same way. Thus, the objects that are near the camera ( = near the face of the cube that we can’t see) are big, the others are smaller. Seems like real life !

Let’s see what it looks like from the “behind” the frustum :

(IMAGE12)

Here you get your image ! It’s just a little bit too square, so another mathematical transformation is applied (this one is automatic, you don’t have to do it yourself in the shader) to fit this to the actual window size :

(IMAGE13)

And this is the image that is actually rendered !

### Cumulating transformations : the ModelViewProjection matrix

… Just a standard matrix multiplication as you already love them !

```cpp
    // C++ : compute the matrix
    glm::mat4 MVPmatrix = projection * view * model; // Remember : inverted !
```

```c
    // GLSL : apply it
    transformed_vertex = MVP * in_vertex;
```
