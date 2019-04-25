# STRUCTURES
- [STRUCTURES](#structures)
  - [PREREQUISITES](#prerequisites)
    - [FILES](#files)
  - [LEARNING OBJECTIVES](#learning-objectives)
  - [BACKGROUND](#background)
  - [STRUCT USE CASE](#struct-use-case)
  - [STRUCT SYNTAX](#struct-syntax)
  - [STRUCT STORAGE](#struct-storage)
  - [NESTED STRUCTURES](#nested-structures)
  - [STRUCT EXERCISE](#struct-exercise)
  - [TYPEDEF](#typedef)
  - [TYPEDF EXERCISE](#typedf-exercise)
  - [SIZEOF STRUCT](#sizeof-struct)
  - [SIZEOF EXCERCISE](#sizeof-excercise)
  - [STRUCT POINTER SYNTAX](#struct-pointer-syntax)
  - [FINAL EXERCISE](#final-exercise)
  - [REVIEW](#review)

## PREREQUISITES

* understanding of pointers
* understanding of arrays
* understanding of simple data types(int, char, char*...)
* memory allocation

### FILES 

structs.h
```c
#include <stdio.h>
#include <fcntl.h>
#include <stdlib.h>
#include <sys/stat.h>
#include <unistd.h>
#include <string.h>

#define FILE_NAME "employees.csv"
#define LINE_SIZE 32

void readFile(char **fileContents, int *fileSize);
void parseContents(char *fileContents, int fileSize);
void parseLine(char *line);
```

struct_skeleton.c
```c
#include "struct.h"

int main(int argc, char **argv)
{
	char *fileContents = NULL;
	int fileSize = 0;
	readFile(&fileContents, &fileSize);
	parseContents(fileContents, fileSize);
	return 0;
}

void readFile(char ** fileContents, int *fileSize)
{
	int fd = -1;
	ssize_t bytesRead = 0;
	struct stat st = {0};
	fd = open(FILE_NAME, O_RDONLY);
	if(fd == -1)
	{
		perror("open");
		exit(EXIT_FAILURE);
	}

	//get the size of the file
	fstat(fd, &st);
	*fileSize = st.st_size;

	*fileContents = calloc(*fileSize + 1, sizeof(char));
	if(*fileContents == NULL)
	{
		printf("Allocation error\n");
		close(fd);
		exit(EXIT_FAILURE);
	}
	bytesRead = read(fd, *fileContents, *fileSize);
	if(bytesRead == -1)
	{
		perror("read");
		close(fd);
		exit(EXIT_FAILURE);
	}

	if(close(fd) == -1)
	{
		perror("close");
		exit(EXIT_FAILURE);
	}

}

void parseContents(char *fileContents, int fileSize)
{
	char *currentLine = strtok(fileContents, "\n");
	
	//skip the first line
	currentLine = strtok(NULL, "\n");
	while(currentLine != NULL)
	{
		parseLine(currentLine);
		currentLine = strtok(NULL, "\n");
		break;
	}
}

void parseLine(char *line)
{
	char *nextElement = strtok(line, ",");
	while(nextElement != NULL)
	{
		printf("%s\n", nextElement);
		nextElement = strtok(NULL, ",");
	}
}

```

## LEARNING OBJECTIVES

* typedef
* purpose of structs 
* use case of structs
* operators ( DOT, &, *, -> )

## BACKGROUND

* what is a struct?
    - it is a data type made up of other data types.
    - it is a way to store those data types contiguously in memory
* why do we use structs?
    -structs are used in order to effiently store and access related pieces of information.

## STRUCT USE CASE

* Why would we use a structure?
    - Structures are very useful for storing related data, such as data that may be stored in a table in a database.
    - Example: There is a database table that represents an employee.  The employee has a name, address, phone number...
* Take a look at the provided file struct_skeleton.c.  The function parseLine is going to parse each line of employees.csv and extract information such as name, address, phone number...
    - How can we store this information and return it?

## STRUCT SYNTAX

* We need to create a structure in order to store the employee information.
* Structure syntax:

```c
struct _myStruct
{
    int member1;
    int member2;
    char* member3;
};
```
* Components:
    - struct - This is a keyword letting the compiler know that you are creating a new data type.

    - _myStruct - The unique name that you are giving to your structure.

    - Together the entire name of the new data type is:
        - struct _myStruct
        - You can declare variables of this new type in your code as follows:
            - struct _myStruct myStructVariable;
        - The previous struct can be initialized to all 0’s by adding:
            - struct _myStruct myStructVariable = {0};
* Giving values to the members of the struct:
    - The dot (.) operator will allow you to access members of the structure.

```c
myStructVariable.member1 = 0x1234
myStructVariable.member2 = 0xABCD;
myStructVariable.member3 = calloc(10, sizeof(char));
```

## STRUCT STORAGE

* Let’s take a look at what this structure now looks like in memory.  The structure variable itself represents the address of member1.


|MEMORY ADDRESS  |VALUE  |VARIABLE TYPE / NAME  |
|---------|---------|---------|
|0xFFFFFFFC |[0X00][0X10][0X00][0X00]| myStructureVariable.member3   |
|0xFFFFFFF8 |[0xCD][0XAB][0X00][0X00]| myStructureVariable.member2   |
|0xFFFFFFF4 |[0X34][0X12][0X00][0X00]| struct _myStruct myStructurevariable  myStructureVariable.member1|

Note - assume the call to calloc returns the memory address 0x1000

## NESTED STRUCTURES

* Since a struct itself is a data type, a struct can be a member of another struct.  For example:

```c
struct _innerStruct{
	int var1;
	int var2;
};
struct _mainStruct{
	struct _innerStruct inner1;
	struct _innerStruct inner2;
};
```

## STRUCT EXERCISE 

* Modify the structs.h file to add the following:

    - Create a new structure to represent a first and last name.  The members are:
        -firstName
        -lastName

    - Create a new structure to represent a phone number.  The members are:
        - areaCode
        - phoneNumber

    - Create  a new structure to represent address.  The members are:
        - street
        - city
        - county
        - state
        - zipCode

    - Create a new structure to represent an employee.  An employee has the following information:
        - fullName (struct)
        - companyname
        - address (struct)
        - phone1 (struct)
        - phone2 (struct)
        - email
        - website

## TYPEDEF

* Used to give a type a new name
    - example
```c
typedef unsigned char BYTE;
```
    - Now BYTE can be used as an abbreviation for unsigned char:
```c
BYTE b1;
```

* Use with struct

```c
typedef struct _myStruct{
	int var1;
	int var2;
}myStruct;
myStruct str1 = {0}; (instead of struct _myStruct str1 = {0};)
```

## TYPEDF EXERCISE

* Modify all of the structs that were created by using the typedef keyword and removing the struct keyword when using them.

## SIZEOF STRUCT

* The sizeof macro can be used for structs just like any other data type.  The result is the same of the sizeof each data type inside the struct.

    - e.g.:
```c
typedef struct _point{
	int x;
	int y;
} point;
sizeof(point) will be 8 because it is made up of two 4 byte members.
```

## SIZEOF EXCERCISE

* Determine the sizeof each struct that has been created thus far.

## STRUCT POINTER SYNTAX

* Earlier we saw that to access member in a struct we use the syntax:

```c
structVar.member = ...
```

* If the variable is a pointer to a struct, there is a different way to access its members:

```c
structPtr->member = ...  [same as  (*structPtr).member = ... ]
```

```c
typedef struct _point {
int x;
		int y;
	} point;
point p = calloc(1, sizeof(point));
p->x = 10;
p->y = 20;
```

## FINAL EXERCISE 

* We are going to finish the implementation in structs.c. 
    - Create a static array of 500 employee structures to store the information in the employees.csv file.  

        - The readFile function does not need to be changed.  This function will read the entire file contents into a char*.  
    
    - Create a pointer to an employee structures and allocate space for 500 employee structures (use calloc).  

    - Modify the parseContents function to accept this pointer.  
        - Each repetition of the loop shall move to the next element in the array.  

    - Modify parseLine function to accept a pointer to an employee structure.
        -Populate members of the employee structure as it reads through the line.  

    - Create new functions to print the structures that you have created.
```C
print(fullName name)
{
	printf(“%s\n”, “Full Name Struct:”);
	printf(“%s %s\n”, name->firstName, name->lastName);
}
```

- Implement the functions for each structure, and then create a function to print the entire array of employees.

## REVIEW

* what we learned:

    - use case of structs
    - syntax of structs 
    - typedef
    - Struct pointers
    - Structs within structs



