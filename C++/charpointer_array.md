# char, char[], char*, char**

## char

**char is a single character from the ASCII table**

## char[]

**char[] is an array of multiple character in a sequential memory block**<br>
**The last char of a char array is used for a '\0' character, which indicates the end of a c-string**

### Initialization examples

~~~~c++
//Creates an char array with 5 character (4 usable chars one '\0')
char cp[5];
~~~~

~~~~c++
//Creates an char array with 5 character (4 usable chars one '\0')
char cp[5] = "HEAD";
~~~~

## char*


**char\* is a pointer that points to an address of a char**<br>
**char\* can also point to the start of a char array**

### Initialization examples

~~~~c++
//char pointer that points to a single char
char* cp = new char{'a'};
~~~~

~~~~c++
//Init char pointer with already existing char
char c = 'A';
char* cp = &c;
~~~~

~~~~c++
//char pointer that points to the start of a char array with 4 character and a '\0' character
char* cp = new char[5]**
~~~~

~~~~c++
//char pointer that points to the start of a char array "Hello" with 5 character and a '\0' character
char* cp = new char[] = "Hello";
~~~~

~~~~c++
//Same as above, but this time the array, that the pointer points to is not newly created memory in the heap, but already existing memory in the stack
char arr[] = "Hello"
char* cp = arr
~~~~

# char**

**A char\*\* stores an address of a char\*, so it points to a char\***<br>
**The point/usage of a char\*\* is, that it can store an array of char arrays, so multiple c-strings**


## Initialization examples

~~~~c++
//Create a char** and store 3 c-strings/3 char arrays in sequential memory<br>
char** charPointerPointer = new char*[3];
char** charPointerPointerStartAddress = charPointerPointer;

//First char array
*charPointerPointer = new char[]{"Hello1"};
++charPointerPointer;

//Second char array
*charPointerPointer = new char[]{"Hello2"};
++charPointerPointer;

//Third char array
*charPointerPointer = new char[]{"Hello3"};

charPointerPointer = charPointerPointerStartAddress;

//Print all 3 c-strings
for(int i = 0; i < 3; ++i)
{
    std::cout<<"charPointerPointer["<<i<<"]: "<<charPointerPointer[i]<<std::endl;
}
~~~~
