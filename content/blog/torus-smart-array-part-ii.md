---
title: 'Torus: Smart Array - Part II'
date: Tue, 30 Nov 2004 03:49:28 +0000
draft: false
tags: ['Robotics', 'Algorithms']
---

[As I was discussing](/blog/vacuum-cleaner/) I am working on an intelligent vacuum cleaner. In this article I will discuss how we did our state management for the surface the robot is vacuuming.

In order to maintain the state information for the surface, one of things we had for our intelligent Roomba was a char array (VACUUMED, UNKNOWN, BLOCKED are the possible values for each cell). However a simple array wouldn't work because the robot could be physically placed anywhere on the floor, yet our starting position would always be (0,0). This was a problem. One of the solutions proposed and implemented by many others in my class was to _shift_ the array to accomodate the space in the room. We on the other hand took a different turn; in Microsoft lingo our array was a smart array. Our array acted like a torus which meant that it would wrap around the boundaries. If for instance, the array size is: 60x60 then a reference to the point (70,40) would translate to: (19,40); in this sample case the wrap occured in the x-direction. This took care of the problem of shifting the array. Read on to see the C code for the torus functions.

_Next time I will discuss how we managed to maintain the direction we were travelling in._

```c
#define GRID_SIZE 10 // The size of the grid

// Possible states a cell can be in
#define UNKNOWN  0x0 // State is not known by the system
#define VACUUMED 0x1 // It was already vacuumed
#define BLOCKED  0x2 // It was found blocked
#define BUFFER_SIZE 512 // buffer size of the direction (fffrr..) string

// Possible directions
#define XPOS 0x0
#define XNEG 0x1
#define YPOS 0x2
#define YNEG 0x3

// The memory where the state information will be stored
char grid[GRID_SIZE][GRID_SIZE]; // square matrix
//The current X position of the torus updated whenever moved
short int torus_currentx = GRID_SIZE/2; 
// The current Y position of the torus updated whenever moved
short int torus_currenty = GRID_SIZE/2; 
// The current direction of the torus updated  whenever moved
char torus_currentDir = XPOS; 

short torus_position(short);
void torus_init(void);
void torus_setcurrentstate(char);
void torus_moverelative(short, short);
void torus_findnextcell(char *);

/***  Begin Torus code ***/
void torus_init() {
        short i,j;
        for(i=0;i<grid_SIZE;i++) {
                for(j=0;j<grid_SIZE;j++) {
                        grid[i][j]=UNKNOWN;
                }
        }
}
void torus_setcurrentstate(char state) {
        if(torus_currentx < GRID_SIZE && torus_currenty < GRID_SIZE) {
                grid[torus_currenty][torus_currentx]=state;
        }
}
//
void torus_moverelative(short fwd, short right) {
        if (fwd>0){
                switch(torus_currentDir){
                case XPOS:
                        torus_currentx++;
                        break;
                case XNEG:
                        torus_currentx--;
                        break;
                case YPOS:
                        torus_currenty++;
                        break;
                case YNEG:
                        torus_currenty--;
                        break;
                }
        }
        else if (right<0){
                switch(torus_currentDir){
                case XPOS:
                        torus_currentDir=YPOS;
                        break;
                case XNEG:
                        torus_currentDir=YNEG;
                        break;
                case YPOS:
                        torus_currentDir=XNEG;
                        break;
                case YNEG:
                        torus_currentDir=XPOS;
                        break;
                }
        }
        else if (right>0){
                switch(torus_currentDir){
                case XPOS:
                        torus_currentDir=YNEG;
                        break;
                case XNEG:
                        torus_currentDir=YPOS;
                        break;
                case YPOS:
                        torus_currentDir=XPOS;
                        break;
                case YNEG:
                        torus_currentDir=XNEG;
                        break;
                }
        }
        if (fwd<0){
                switch(torus_currentDir){
                case XPOS:
                        torus_currentx--;
                        break;
                case XNEG:
                        torus_currentx++;
                        break;
                case YPOS:
                        torus_currenty--;
                        break;
                case YNEG:
                        torus_currenty++;
                        break;
                }
        }
}
// uses a shortest path algorithm to find the closest unknown position
// and makes directions
void torus_findnextcell(char* buffer){
        int tArr[GRID_SIZE][GRID_SIZE] = {{0}};
        int pos=0,j,len = 1;
        const int qSize = 2*GRID_SIZE;
        int loop = 1, i = 1, x = torus_currentx, y = torus_currenty;
        int q[2*GRID_SIZE][3]={{0}};
        int qFront = 0, qEnd=0;
        char tempDir,oDir;

        // load up the q with the current position
        tArr[x][y] = 1;
        q[qEnd][0] = x;
        q[qEnd][1] = y;
        q[qEnd++][2] = 1;

        // perform a bredth-first search marking the positions with distance
        while(loop){

                // if we have exhausted the q by this point, we are done vacuuming
                if(qFront == qEnd){
                        buffer[0]='\0';
                        return;
                }

                // dq next position and check edges
                x = q[qFront][0];
                y = q[qFront][1];
                len = q[qFront][2] + 1;
                qFront = (qFront+1)%qSize;

                if(x+1<grid_SIZE && grid[x+1][y]!=BLOCKED){
                        if(tArr[x+1][y] == 0){
                                tArr[x+1][y] = len;
                                if(grid[x+1][y]==UNKNOWN){
                                        x++;
                                        loop = 0;
                                }
                                q[qEnd][0] = x+1;
                                q[qEnd][1] = y;
                                q[qEnd][2]= len;
                                qEnd = (qEnd+1)%(qSize);
                        }
                }
                if(loop && x-1>=0 && grid[x-1][y]!=BLOCKED){
                        if(tArr[x-1][y] == 0){
                                tArr[x-1][y] = len;
                                if(grid[x-1][y]==UNKNOWN){
                                        x--;
                                        loop = 0;
                                }
                                q[qEnd][0] = x-1;
                                q[qEnd][1] = y;
                                q[qEnd][2]= len;
                                qEnd = (qEnd+1)%(qSize);
                        }
                }
                if(loop && y+1<grid_SIZE && grid[x][y+1]!=BLOCKED){
                        if(tArr[x][y+1] == 0){
                                tArr[x][y+1] = len;
                                if(grid[x][y+1]==UNKNOWN){
                                        y++;
                                        loop = 0;
                                }
                                q[qEnd][0] = x;
                                q[qEnd][1] = y+1;
                                q[qEnd][2]= len;
                                qEnd = (qEnd+1)%(qSize);
                        }
                }
                if(loop && y-1>=0 && grid[x][y-1]!=BLOCKED){
                        if(tArr[x][y-1] == 0){
                                tArr[x][y-1] = len;
                                if(grid[x][y-1]==UNKNOWN){
                                        y--;
                                        loop = 0;
                                }
                                q[qEnd][0] = x;
                                q[qEnd][1] = y-1;
                                q[qEnd][2]= len;
                                qEnd = (qEnd+1)%(qSize);
                        }
                }
        }

        // we've found an unknown position, we just need to record
        // the path to take...but we are at the destination, so 
        // you have to think kinda backwards, at the end we will
        // reverse the string
        loop = 1;
        buffer[pos++] = 'f';
        len--;
        if(x+1<grid_SIZE && tArr[x+1][y] == len){
                oDir = XNEG;
                x++;
        }
        else if(x-1>=0 && tArr[x-1][y] == len){
                oDir = XPOS;
                x--;
        }
        else if(y+1<grid_SIZE && tArr[x][y+1] == len){
                oDir = YNEG;
                y++;
        }
        else if(y-1>=0 && tArr[x][y-1] == len){
                oDir = YPOS;
                y--;
        }

        tempDir = torus_currentDir;

        for(i=len-1;i>0;i--){
                if(x+1<grid_SIZE && tArr[x+1][y] == i){
                        if(tempDir == YPOS)
                                buffer[pos++]='r';
                        if(tempDir == YNEG)
                                buffer[pos++]='l';
                        tempDir = XNEG;
                        x++;
                }
                else if(x-1>=0 && tArr[x-1][y] == i){
                        if(tempDir == YPOS)
                                buffer[pos++]='l';
                        if(tempDir == YNEG)
                                buffer[pos++]='r';
                        tempDir = XPOS;
                        x--;
                }
                else if(y+1<grid_SIZE && tArr[x][y+1] == i){
                        if(tempDir == XPOS)
                                buffer[pos++]='l';
                        if(tempDir == XNEG)
                                buffer[pos++]='r';
                        tempDir = YNEG;
                        y++;
                }
                else if(y-1>=0 && tArr[x][y-1] == i){
                        if(tempDir == XPOS)
                                buffer[pos++]='r';
                        if(tempDir == XNEG)
                                buffer[pos++]='l';
                        tempDir = YPOS;
                        y--;
                }
                buffer[pos++]='f';
        }

        // using the last known direction of the robot, we need to
        // find out how much to turn to start on the path
        if(torus_currentDir == XPOS){
                if(tempDir == YPOS)
                        buffer[pos++]='l';
                else if(tempDir == YNEG)
                        buffer[pos++]='r';
                else if(tempDir == XNEG){
                        buffer[pos++]='r';
                        buffer[pos++]='r';
                }
        }
        else if(torus_currentDir == XNEG){
                if(tempDir == YPOS)
                        buffer[pos++]='r';
                else if(tempDir == YNEG)
                        buffer[pos++]='l';
                else if(tempDir == XPOS){
                        buffer[pos++]='r';
                        buffer[pos++]='r';
                }
        }
        else if(torus_currentDir == YPOS){
                if(tempDir == XPOS)
                        buffer[pos++]='r';
                else if(tempDir == XNEG)
                        buffer[pos++]='l';
                else if(tempDir == YNEG){
                        buffer[pos++]='r';
                        buffer[pos++]='r';
                }
        }
        if(torus_currentDir == YNEG){
                if(tempDir == XPOS)
                        buffer[pos++]='l';
                else if(tempDir == XNEG)
                        buffer[pos++]='r';
                else if(tempDir == YPOS){
                        buffer[pos++]='r';
                        buffer[pos++]='r';
                }
        }

        // now reverse the string and end it and wee are done
        for(i=0,j=pos-1;j>i;i++,j--){
                tempDir = buffer[i];
                buffer[i]= buffer[j];
                buffer[j]=tempDir;
        }

        buffer[pos]='\0';
}
/*** End Torus Code ***/ 
```