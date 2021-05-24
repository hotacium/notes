sorting_problem_0.md
# Sorting Problem

This parts presents several algorithms that solve the following **sorting problem**:

Input: A sequence of `n` numbers (a1, a2, ... an).  
Output: A permutation (reordering) (a1, a2, ... an) of the input sequence such that `a'1 <= a'2 ... a'n`

## The structure of the data

In practice, the numbers to be sorted are rarely isolated values.  
Each is usually part of a collection of data called **record**. Each record contains a **key**, which is the value to be sorted. The remainder of the record consists of **satellite data**, which are usually carried around with the key.  
If each record includes a large amount of satellite data, we often permute an array of **pointers** to the records rather thatn the records themselves in order to **minimize** data movement.



```
record = [key + main data + satellite data]
