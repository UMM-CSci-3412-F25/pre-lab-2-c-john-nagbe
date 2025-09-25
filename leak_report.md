# Leak report

_Use this document to describe whatever memory leaks
you find in `clean_whitespace.c` and how you might fix
them. You should also probably remove this explanatory
text._

1. When I ran the valgrind command on the 'check_whitespace.c' file, I got an error stating that there 
was a memory leak in c file (similar to the error example in the instruction for this pre lab). While looking through the code,
I found that this leak was due to the 'strip' function dynamically allocating memory using the 'calloc', but the returned pointer is never actually freed by the caller.
The memory is allocated for the stripped string and the 'result' is returned as a 'const char*'. However, in 'is_clean', 'strip(str)' is called and stores the result in 'cleaned' but never frees it.

- To fix this, I added an 'if' statement that:
    - uses the 'strip()' function to compare the original string and the cleaned string
    - and frees the memory allocated for the 'cleaned' string to prevent memory leaks.

2. When I ran valgrind on test file i got another memory leak error. The leak was happening because in the test file we are comparing the result but we are still not freeing the return heap memory.
So for every test that calls 'strip', valgrind sees a leak.

- To fix this:
    - I freed the result after each test by:
      - storing the result in a temporary variable, asserting, and then freeing it.
   
   
However: Running the code again, there was still two more errors 
1. "free(): invalid pointer". this was happening because the test.cpp file's test harness calls free() on the pointer returned by your strip function. So, When your strip function returns "", it returns a pointer to a string literal. This pointer is not valid for free() because it was never allocated by calloc.
  - Solution:
      - I modified the 'strip' function to always return a dynamically allocated string. Even if the result is an empty string, it should still 'calloc' a small amount of memory for it.
2. Valgrind was successfully running the test suite, and all 10 tests passed. However, it detected a memory leak. The test report showed that 1 byte in 1 block is "definitely lost".
   - The memory leak was happening in the 'is_clean' function. the function was calling strip(str), which returns a dynamically allocated string, but was not always freeing the memory that strip returns.
 - Solution:
