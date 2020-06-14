### Initial Research:

The purpose of Pacman is for the player to ear all of the dots placed around the maze to go to the next level. However, Pac-man is followed by 4 different ghosts. If any of the ghosts come in contact with him the player loses one of the three lives and the level is restarted.

The only way in which Pac-Man can counter-attack the ghosts is by eating one of the four larger 'dots'(energizers) located in the maze.
When this happens the ghosts become frightened, run away from him and become vulnerable to Pac-Man and then he can eat them, giving the player extra bonus points.
The energizers only last for a certain amount of time and depending on the level the effect lasts shorter.

Throughout the game, two fruits appear in the maze. One appears when Pacman has eaten 70 dots and the last one appears when he has eaten 170.


For my PacMan game, I needed to implement: One PacMan, four Ghosts, Dots, Energizers, Fruits and a Maze.

My initial idea to create the project was to have all the different parts of the game as different classes and all run through the main.cpp file. This design would have been very complex to code and link the objects together.

Therefore, I decided to follow a design which was suggested by Jon Macey on his lectures. 

![Game_design1](https://user-images.githubusercontent.com/62644767/84599598-1e38e400-ae6b-11ea-98fc-d6037df017f1.JPG)

Main: This part of the program will start up the project and will create a window where the game will happen. 

Arena: Arena will create the maze and load up the Pacman on it.

Game Item: This class is an enum class. It holds various objects which will appear in the maze.

Game Object: The class defines what the game items will do.


## Staring off


#Maze requirements

-Load image
To create the maze I first used an image in black and white. With the colour black, I drew the walls of the maze.
In order to load the image I had to use ngl/Image.h class and load up the image using this code:
ngl::Image img(_filename); and was passed from main.cpp  as  Maze maze("maps/map6.png");

-Get colour B and white
Once it was loaded, with the use of two for loops, the code checks the colour of the position x and y to see if it is black. 
If it is, then it sets on that position an Itemtype  Wall otherwise it sets an Itemtype of Empty.
for (unsigned int y = 0; y < m_height; ++y)
{
for (unsigned int x = 0; x < m_width; ++x)
{
if {(img.getColour(x, y) == ngl::Vec4::zero())
setItem(x, y, ItemType::Wall, m_objects[Wall]);}

Else
{setItem(x, y, ItemType::Empty, m_objects[Empty]);}
}
}
Then, in the void Arena:: draw() const, to check that the location of the walls were right, I printed a ‘W’ wherever the Wall was allocated. 
![First_Wall](https://user-images.githubusercontent.com/62644767/84604854-e6dd2e00-ae90-11ea-9ede-caf8ce365145.JPG)

Once this was done, I started working on the 3D map. 
On the line where I printed out the Ws on the output window, I passed on the x and y for each of the walls to drawWall(unsigned int _x, unsigned int _y) const

Here, the position is set and by using shaders and a VAOPrimitive ‘cube’ the walls are drawn.
![maze_1](https://user-images.githubusercontent.com/62644767/84604866-fa889480-ae90-11ea-861d-33973af30c83.JPG)

Now, I wanted to create the dots and believed the best way of setting the location of the dots would be by adding dots in the drawn map, so I created a new map with red dots on it.
However, It was a lot harder than what I expected it to be. 
First I used the same line “if (img.getColour(x, y) == ngl::Vec4::zero())”  which checks for the colour black, and changed ‘zero’ for red in the ngl Vec4 class. Set the values to ‘1.0f,0.0f,0.0f,1.0f’ and also ‘255.0f,0.0f,0.0f,255.0f’.

As this method did not work and various other tried did not work either I came up with the idea of just checking the values that were grabbed by ‘img.getColour()’ and print it out the values.
I only got numbers, which is what I expected and then found the patterns which were patterns of colours. 

![map_maze_dots_BIG](https://user-images.githubusercontent.com/62644767/84605096-8f3fc200-ae92-11ea-9ce5-3afc587128a7.JPG)

I proceeded by creating 3 colour arrays; white, red, black.
I then created an array called currentColour to hold 4 values.
Once in the second for loop, I set in currentColour the colours grabbed by img.getColour so that I could then check in the following if statements if the currentColour was white, red or black.

I also created a new Itemtype ‘White’, and only use empty for the default setting.
 int white[4] = { 255, 255, 255, 255 }; 
    int red[4] = { 255, 0, 0, 255 };
    int black[4] = { 0, 0, 0, 255 };
    int currentColor[4] = { 0, 0, 0, 0};
    for (unsigned int y = 0; y < m_height; ++y)
    {
        for (unsigned int x = 0; x < m_width; ++x)
        {
            currentColor[0] = img.getColour(x, y)[0];
            currentColor[1] = img.getColour(x, y)[1];
            currentColor[2] = img.getColour(x, y)[2];
            currentColor[3] = img.getColour(x, y)[3];
            
            if (isEqual(currentColor, white)) {
                setItem(x, y, ItemType::White, m_objects[White]);
            }
            if (isEqual(currentColor, black)) {
                setItem(x, y, ItemType::Wall, m_objects[Wall]);
            }
            if (isEqual(currentColor, red)) {
                setItem(x, y, ItemType::Fruit, m_objects[Fruit]);
            }
     }
                
}
Once this was done, I changed the code where it outputs a letter to refer to an item and selected ‘F’ for fruit and ‘-’ for white, which would draw the path.

![Wall_Fruit_White](https://user-images.githubusercontent.com/62644767/84605108-b4343500-ae92-11ea-94d4-8c6633b83914.JPG)

As I was happy with the results, I carried on to create the dots, which I wanted to be spheres. 
At first, created ‘trolls’ as it was an ngl::VAOPrimitive that was pre-made, however, I shortly changed it to spheres using the ngl::CreateSpheres function and then Used to create the Pacman player. 
As this worked, I then created a floor and started changing the colours of the walls

This is the final result for the maze:
![final_pacman_NOGHOST](https://user-images.githubusercontent.com/62644767/84605121-cdd57c80-ae92-11ea-8253-828f083c39f4.JPG)



# Main requirements and SDL

Main.cpp is the part of the program which starts it all. It is in charge of creating the window using SDL and creating an OpenGLContext to be able to use OpengGL and set up shaders.
It also takes in all the key events needed to make Pacman move.It takes in both, SDL_KEYDOWN and SDL_KEYUP.

On SDL_KEYDOWN with a switch statement, it passes on information to xDir and yDir which are then passed onto the arena class to check the position of the player.
I also added SDLK_ESCAPE so that when it is pressed ‘quit’ becomes true and the while loop is stoped, meaning the program closes.





## Final game structure

![Game_design2](https://user-images.githubusercontent.com/62644767/84600923-c56e4900-ae74-11ea-8d32-fc21a1e78019.JPG)

switch (event.key.keysym.sym)
                {
                case SDLK_ESCAPE: quit = true; break;
                case SDLK_LEFT:  xDir = 1; break;
                case SDLK_RIGHT: xDir = -1; break;
                case SDLK_UP:    yDir = 1; break;
                case SDLK_DOWN:  yDir = -1; break;
                }
                break;
On SDL_KEYUP, the values come back to 0 meaning Pacman is no longer moving. 

Here in main, the arena is also updated every millisecond arena.update(0.1f);
The last function in main.cpp is ‘setupShaders’ where two different shaders are created and then used in the rest of the program to add colour to Pacman, the walls, dots and the floor.


# Pacman requirements:

When creating Pacman, I had to take into consideration various things. 
Firstly, the shape, he had to be a sphere, so I used the ngl::VAOPrimitives::instance()->createSphere function in Maze.cpp

Secondly, it should not be able to go through the walls but he should be able to walk through the dots and make them disappear. But also go from the right side of the maze to the left and vice versa using the pathways on the side.

Lastly, control the position according to what the user decided using the keys.

I began by doing the controls, which I followed and used the code given as an example in our lectures. 

Pacman firstly appears in the map in position 4,4. Whenever there is a key event Pacman’s position is updated in the Maze::update() function. 

Here it is used the current position and then a new position. Once there is a key event, a check is made. If the direction where Pacman is trying to go is a wall, the variable newPos will not be updated to have a new position. Therefore Pacman will not be able to move. This if statement is done twice, once to check on the direction in the Y-axis and direction in the X-axis. If in the wanted position there is no wall, then the new position
if (dir.x != 0 && getItem(pos.x + dir.x, pos.y + dir.y) != ItemType::Wall)
{ 	
	newPos.x = pos.x + dir.x;
}
if (dir.y != 0 && getItem(pos.x + dir.x, pos.y + dir.y) != ItemType::Wall)
{
        newPos.y = pos.y + dir.y;
 }

Now Pacman did not walk through the walls, but he walked through the fruits and would not make them disappear. 

What I first did was to add and if statement that would check if the position where he was had and Itemtype of fruit that on that position the ItemType would change to white instead. 

This worked nicely, however, it only worked if Pacman would stop on that exact position, meaning it would only work when the SDL event was SDL_KEYUP.

if (pos.x && pos.y && getItem(newPos.x + dir.x, newPos.y + dir.y) == ItemType::Fruit)
    {
        setItem(newPos.x, newPos.y, ItemType::White, m_objects[White]);
        setItem(pos.x, pos.y, ItemType::White, m_objects[White]);

So what I had to do is to change the pos.x and pos.y to newPos.x and newPos.y so not only the current position where Pacman is set turns the fruit into ‘white’. 

 if (newPos.x && newPos.y && getItem(pos.x + dir.x, pos.y + dir.y) == ItemType::Fruit)
    {
        setItem(newPos.x, newPos.y, ItemType::White, m_objects[White]);


I also had to adjust the code so when Pacman uses the right or left paths he appears in the other side of the maze.
So in order to do that I had to add two more if statements, one for when Pacman goes through the right path and one for the left path. 
The coordinates on the maze where slightly off to my surprise so 0x was at the end of the far right side of the image instead of being in the centre.



if (newPos.x >= m_width) { // if the new position is bigger than the maze width
            newPos.x = 1;  // go to position 1. This is left to right
}        
        if (newPos.x <= 0) { // if the position is smaller or equal to cero 
            newPos.x = m_width;// the new positon is at the end of the maze’s width. Right to left
}

# Ghosts requirements

There are four different types of ghosts in the game. Each one of them has a different colour and a different ‘personality’, due to the difference in their code. Each ghost chases after Pacman in a different way. 

Red Ghost: 
The red ghosts use Pacman’s current position in order to choose his text tile/position. He is the one who follows him the closest. 
His speed is also increased after each level by 5%.

Pink Ghost
This ghost uses an algorithm to predict where Pacman is heading by using Pamans position and also direction to then select a location four tiles ahead of Pacman’s position. 

Blue ghost
Inky actually uses both Pac-Man's position/facing as well as the red ghosts’ position in his calculation. He starts by selecting a tile two tiles in front of Pac-Man in his current direction. Then gets a vector from the red ghost’s position to this tile, and then doubling the length of the vector. The tile that this new, extended vector ends on will be where he heads to.

Orange ghost:
This ghosts changes between two algorithms. Before he moves to a new tile, he checks his proximity to Pacman, it is further than 8 tiles, he uses the same method as the red ghost. 
Otherwise, he uses a fixed pattern on the bottom of the maze.


This is a quick pseudo-code I would have used as guidance for the algorithms in the game

Pac-Man.pos, target_pos, clyde_target = random_tiles
while game == in_play:
    case player of:
      Pac-Man: if ghost.distance <= 5:
                   move 1 tile away from ghost
               else:
                   move 1 tile toward target_pos
      Blinky:  move 1 tile toward Pac-Man.pos
      Inky:    move 1 tile toward (Pac-Man.pos + 4) // 4 tiles in front of Pac-Man
      Clyde:   if Clyde.pos == clyde_target:
                   clyde_target = (clyde_target + 1) % 10
               else:
                   move 1 tile toward clyde_target

    if Pac-Man.pos == ghost.pos:
        game = over // Pac-Man loses
    else if Pac-Man.pos == target_pos:
        game = over // Pac-Man wins

Pac-Man Ivan Wang (2014)


I started creating one ghost, which was going to be the red one, but I was only able to create the setup of the Ghost class and Object and created a sphere representing its initial position.

# Steps to work on

GUI
I tried to use SDL2_ttf library in order to generate some text on the screen, to add a Pac-Man logo and to show the player the scores after eating each dot. However, I kept on getting errors with the linker in Visual Studios and I was unable to use it. 

OBJ
To further improve the game and make it more interesting I wanted to add ghost objects and fruits which were meshes I made. I did not have enough time to upload them and make it work. 




## Final Program Design
![Game_design2](https://user-images.githubusercontent.com/62644767/84605411-ee9ed180-ae94-11ea-80ca-9fb7d9d0753e.JPG)


Sources:
Understanding Pac-Man Ghost behaviour- Chad Birch https://gameinternals.com/understanding-pac-man-ghost-behavior


Pac-Man:  Ivan Wang, Jacob Weiss, Judy H and Francisco Rojo
http://ideate.xsead.cmu.edu/gallery/projects/pac-man

40 years on, Pac-Man Recreated with AT by NVIDIA Researchers. Isha Salian (2020)

https://blogs.nvidia.com/blog/2020/05/22/gamegan-research-pacman-anniversary/





(maybe add an image of flow control) vS (flow control of someone else PAC man game)
