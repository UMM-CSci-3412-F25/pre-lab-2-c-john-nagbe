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
   