# Intermediate/Hashing - I

## Files in this topic:

- [A1.py](A1.py)
- [A2.py](A2.py)
- [A3.py](A3.py)
- [A4.py](A4.py)
- [HW1.py](HW1.py)
- [HW2.py](HW2.py)

## Description

This section covers foundational concepts and problems related to Hashing - I at an Intermediate level.

### Pseudocode for A1.py
```pseudocode
IMPORT accumulate from itertools
IMPORT Counter from collections

CLASS Solution:
  METHOD solve(self, A):
    // Calculate prefix sums of A
    prefix_sums = list of accumulated sums of A

    // Check if 0 is present in prefix_sums
    IF 0 is in prefix_sums THEN
      RETURN 1
    ELSE
      // Count frequencies of each prefix sum
      counts = Counter(prefix_sums)

      // Check if any prefix sum occurs 2 or more times
      FOR key, value IN counts.items():
        IF value >= 2 THEN
          RETURN 1
        ENDIF
      ENDFOR

      // If no zero sum subarray is found by the above conditions
      RETURN 0
    ENDIF
  ENDMETHOD
ENDCLASS
```

### Pseudocode for A2.py
```pseudocode
IMPORT Counter from collections

CLASS Solution:
  METHOD solve(self, A):
    // Count frequencies of each element in A
    counts = Counter(A)

    // Iterate through the elements of A
    FOR number IN A:
      // If the count of the current number is greater than 1
      IF counts[number] > 1 THEN
        // Return the first such repeating number
        RETURN number
      ENDIF
    ENDFOR

    // If no repeating number is found
    RETURN -1
  ENDMETHOD
ENDCLASS
```

### Pseudocode for A3.py
```pseudocode
CLASS Solution:
  METHOD solve(self, A):
    // Create a set of elements in A for quick lookup
    element_set = set(A)

    // Initialize a counter for valid pairs
    count = 0

    // Iterate through all possible pairs (A[i], A[j]) with i < j
    FOR i FROM 0 TO length(A) - 1:
      FOR j FROM i + 1 TO length(A) - 1:
        // Calculate the sum of the pair
        current_sum = A[i] + A[j]

        // Check if this sum exists as an element in the original list A
        IF current_sum IS IN element_set THEN
          count = count + 1
        ENDIF
      ENDFOR
    ENDFOR

    RETURN count
  ENDMETHOD
ENDCLASS
```

### Pseudocode for A4.py
```pseudocode
IMPORT Counter from collections

CLASS Solution:
  METHOD solve(self, A, B):
    // Count frequencies of elements in list A
    counts_A = Counter(A)

    // Count frequencies of elements in list B
    counts_B = Counter(B)

    // Initialize an empty list to store the intersection
    intersection_result = []

    // Iterate through the unique elements and their counts in counts_A
    FOR element, count_in_A IN counts_A.items():
      // Check if the element also exists in counts_B
      IF element IS IN counts_B THEN
        // Determine the number of times the element appears in the intersection
        // This is the minimum of its counts in A and B
        count_in_intersection = minimum(count_in_A, counts_B[element])

        // Extend the intersection_result list with the element, repeated count_in_intersection times
        APPEND element TO intersection_result, count_in_intersection times
      ENDIF
    ENDFOR

    RETURN intersection_result
  ENDMETHOD
ENDCLASS
```

### Pseudocode for HW1.py
```pseudocode
IMPORT defaultdict from collections

CLASS Solution:
  METHOD solve(self, A, B):
    // Initialize running_sum to 0, which will store the sum of elements encountered so far.
    running_sum = 0
    // Initialize ans to 0, which will count the number of subarrays summing to B.
    ans = 0

    // Initialize a defaultdict 'm' to store frequencies of running sums.
    // If a sum is not yet a key, it defaults to 0.
    m = defaultdict with default value 0

    // Iterate through each number 'n' in the input list A
    FOR n IN A:
      // Add the current number to the running_sum
      running_sum = running_sum + n

      // Check if the running_sum itself is equal to B.
      // This accounts for subarrays starting from the beginning of A.
      IF running_sum == B THEN
        ans = ans + 1
      ENDIF

      // Check if (running_sum - B) exists as a key in the map 'm'.
      // If it exists, it means there was a previous prefix sum 'P' such that
      // current_running_sum - P = B.
      // The value m[running_sum - B] gives the number of times such a prefix sum 'P' occurred.
      // Each occurrence signifies a subarray ending at the current position that sums to B.
      IF (running_sum - B) IS IN m THEN
        ans = ans + m[running_sum - B]
      ENDIF

      // Increment the frequency of the current running_sum in the map 'm'.
      m[running_sum] = m[running_sum] + 1
    ENDFOR

    // Return the total count of subarrays summing to B.
    RETURN ans
  ENDMETHOD
ENDCLASS
```

### Pseudocode for HW2.py
```pseudocode
IMPORT Counter from collections

CLASS Solution:
  METHOD solve(self, A_string):
    // Count frequencies of each character in A_string
    char_counts = Counter(A_string)
    length_A = length of A_string

    // Check if the length of the string is even
    IF length_A % 2 == 0 THEN
      // For an even length string to be a permutation of a palindrome,
      // all character counts must be even.
      FOR character, count IN char_counts.items():
        IF count % 2 != 0 THEN
          RETURN 0 // Not possible to form a palindrome
        ENDIF
      ENDFOR
      RETURN 1 // Possible to form a palindrome
    ELSE
      // For an odd length string to be a permutation of a palindrome,
      // at most one character can have an odd count.
      odd_counts_allowed = 1
      FOR character, count IN char_counts.items():
        IF count % 2 == 0 THEN
          CONTINUE // Even count is fine
        ENDIF

        // If count is odd
        IF count % 2 != 0 THEN
          IF odd_counts_allowed == 1 THEN
            odd_counts_allowed = odd_counts_allowed - 1 // Use up the one allowed odd count
          ELSE
            RETURN 0 // More than one character has an odd count
          ENDIF
        ENDIF
      ENDFOR
      RETURN 1 // Possible to form a palindrome
    ENDIF
  ENDMETHOD
ENDCLASS
```
