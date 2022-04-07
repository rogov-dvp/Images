# Mini-Porject 3

## Overview

This README covers the design choices, instructions to run the code, and sample outputs for both parts of this mini-project.

The first part to replicate a pure based scheme by implementing a portion of the translation from virtual address to physical address.  

The second part is to replicate a UNIX-like file system in C using only bitwise operations.

## Part 1 (Memory_Management)

### Design choices

To start, the main function accepts one argument, the filepath to the data input. The data input is composed of three parts. The first line is the number of bits for offset used on virtual addresses represented as `n`, the second line is the number of bits that would represent the page number (in this case, 16-`n`) as `m`, and finally all other lines are 16 bits unsigned integers (ie. betwenn 0 and 65,535)

The program first initalizes all variables. Then, checks if a file path has been provided. Once everything is set, the program reads the path line by line. First line, sets `n` and the second line sets `m`. Every line after that are a 16 bit unsigned integer used as data which represents a virtual address.

For each virtual address, the function ```calc_phys_address``` is called. This function will set the page number by shifting the virtual address to the right by `n` number of bits. Then a mask is built which sets the `n` most bits as 1s. This is used with `&` operator to extract the offset from the virtual address.

Finally, the program prints the virtual address, page number, and offset.

### How to run the program

Type the following to have the code run:

```cd Memory_Management``` Navigate to the memory management folder

```gcc memory_manager.c``` Compile the c code into an executable file

If on Windows:
```./a.exe input_memory.c``` An executable file will be created. Takes in the data filepath location that conatins the data input file.

If on UNIX:
```./a.out input_memory.c``` An executable OUT file will be created. Takes in the data filepath location that contains the data input file.

### Sample Output

![memory_manager_screenshot](https://github.com/rogov-dvp/Images/blob/master/memory_manager_sc.png?raw=true)

## Part 2 (File_Systems)

### Design choices

To start, the main function accepts the filepath as a parameter to the `main()`. This file contains the input and instructions for doing file system operations such as create, write, read, destroy, and list files on disk.

What is a disk?

Before, we enter the flow of the code, it is important to overview its 5 important functions which replicates the file system operations. These are:

| Function name | Inputs        | Descriptions  |
| ------------- |:-------------:| -----:|
| ```create()```| ```char* filename, int size, char* disk_name``` | The create function creates a file with the given input's filename and the number of blocks in the inode will contain. The disk is opened and the free blocks and inodes are read, and then the disk is closed. The function then searches through the inodes list if any are free. Next, the function finds the given input's size required to store the data in a free blocks. Once these are found, the super block is updated that the inode is in-use and which blocks are free. Finally, it prints the file's name, size, disk, inode, and assigned blocks.  |
| ```delete()```  | ```char* filename, char* disk_name```           | The delete function deletes the file from the disk. It does this by opening the disk, reads the number of free blocks, and the number of inodes. Search for the inode that matches the file name. If the inode is found, free the blocks inside the inode, and set inode as free. Records this information back in the super block. Finally, it closes the disk stream.|
| ```ls()```      | ```char* disk_name```                           | The ls function lists the names of all files on the disk. It opens up the disk and reads the iNodes. Then it lists the file name, inode index, the number of blocks in-use, and their blocks' pointers value. Finally, it closes the disk stream. |
| ```read()```    | ```char* filename, int blockNum, char* buf, char* disk_name``` | The read function reads the given input's block from the input's filename. It opens up the disk, and reads the iNodes. Then, it searches for the inode that contains the filename. If it finds it, then it finds the block number in the inode, reads the data in the block, and prints the data. Finally, it closes the disk stream. |
| ```write()```   | char* name, int blockNum, char* buf, char* disk_name| The write function writes the given input's block from the input's filename. It opens up the disk, reads the free blocks and inodes, and closes the disk stream. Then, finds the inode with the matching file name. Next, it opens the disk stream again, for writing this time. A pointer in the given file and specific block is placed and the writing of data begins. The disk is closed and the function prints where and partially what has been written.   |

The `main()` opens the input file where each line apart from the first line, is given as an instruction (operation, filename, and number of blocks to use). As well, an operation for listing the disk data which is it's own seperate instruction.

The program iterates through line by line and excecute the operation by calling the respective operation function (explained above). Once there are no more lines to read, the input file is closed and the disk is freed (for re-running program)


### How to run the program

```cd FileSystems``` Navigate to the memory management folder

```gcc myFileSystem.c``` Compile the c code into an executable file

If on Windows:
```./a.exe lab3input.c``` An executable file will be created. Takes in the data filepath location that conatins the data input file.

If on UNIX:
```./a.out lab3input.c``` An executable OUT file will be created. Takes in the data filepath location that contains the data input file.


### Sample Output

![file_system screenshot](https://github.com/rogov-dvp/Images/blob/master/part2_screenshot.png?raw=true)

## Contributers

Alex -
Part1 Code version
Documentation

Niklas -

Ramisa -


## Sources
[Reading from a file](https://www.geeksforgeeks.org/fgets-gets-c-language/)

[Split line by delimeter](https://www.educative.io/edpresso/splitting-a-string-using-strtok-in-c)

[Use strtok](https://stackoverflow.com/questions/26597977/split-string-with-multiple-delimiters-using-strtok-in-c)

[Reading and writing binary - part1](https://www.tutorialspoint.com/c_standard_library/c_function_fread.htm)

[Reading and writing binary - part2](https://stackoverflow.com/questions/34473625/modify-some-bytes-in-a-binary-file-in-c)
