# Random FYIs

This page is dedicated to random "byte" sized pieces of information that I've learned over time.

## Wide Characters

A wide character is a computer character datatype that generally has a size greater than the traditional 8-bit character. The increased datatype size allows for the use of larger coded character sets. ([source](https://en.wikipedia.org/wiki/Wide_character))

Wide char is similar to char data type, except that wide char take up twice the space and can take on much larger values as a result. char can take 256 values which corresponds to entries in the ASCII table. On the other hand, wide char can take on 65536 values which corresponds to UNICODE values. ([source](https://www.geeksforgeeks.org/wide-char-and-library-functions-in-c/))

* Just like the type for character constants is char, the type for wide character is wchar_t.

* This data type occupies 2 or 4 bytes depending on the compiler being used.

* Mostly the wchar_t datatype is used when international languages like Japanese are used.

### Example

```cpp
#include <iostream> 
using namespace std; 

int main() 
{ 
    // char type array string 
    char name[] = "FOOBAR"; 
    cout << name << endl; 

    // wide-char type array string 
    wchar_t wname[] = L"FOOBAR"; 
    wcout << wname << endl; 

    return 0; 
} 
```

# 
