# char, char*, char[] 

## char

**char is a single character from the ASCII table**

## char[]

**char[] is an array of multiple character in a sequential memory block**<br>

### Initialization examples

//Creates an char array with 5 character (4 usable chars one '\0')<br>
**char cp[5];**<br>

//Creates an char array with 5 character (4 usable chars one '\0')<br>
**char cp[5] = "HEAD";**<br>

## char*

**char\* is a pointer that points to an address of a char**<br>
**char\* can also point to the start of a char array**

### Initialization examples

//char pointer that points to a single char<br>
**char\* cp = new char{'a'}**

//ú<br>
**char c;**<br>
**char\* cp = &c;**<br>

//char pointer that points to the start of a char array with 4 character and a '\0' character <br>
**char\* cp = new char[5]**

//char pointer that points to the start of a char array "Hello" with 5 character and a '\0' character <br>
**char\* cp = new char[] = "Hello"**

//Same as above, but this time the array, that the pointer points to is not newly created memory in the heap, but already existing memory not in the heap<br>
**char arr[] = "Hello"**<br>
**char\* cp = arr**


