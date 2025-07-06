# Advanced/Heaps - II

## Files in this topic:

- [Ath largest element.py](Ath largest element.py)
- [Heaps_2.pdf](Heaps_2.pdf)

## Description

This section likely delves into more advanced uses of Heaps (Priority Queues), including problems involving running medians, complex scheduling, or combinations of heaps with other data structures and algorithms.

### Pseudocode for Ath largest element.py
```pseudocode
// Requires a Min-Heap data structure.
// Operations: ADD_TO_HEAP, REMOVE_MIN_FROM_HEAP, PEEK_MIN, GET_HEAP_SIZE.

FUNCTION stream_Ath_largest_element(A_rank_value, B_element_stream):
  num_elements_in_stream = length of B_element_stream

  // `k_largest_elements_min_heap` will store the `A_rank_value` largest elements
  // encountered so far in the stream. Since it's a min-heap, its root (smallest element)
  // will be the A_rank_value-th largest among these `A_rank_value` elements.
  k_largest_elements_min_heap = new MinHeap()

  // `output_ans_list` will store the A_rank_value-th largest element after processing each stream element.
  output_ans_list = empty list

  // Process each element from the input stream B_element_stream
  FOR i_idx FROM 0 TO num_elements_in_stream - 1:
    current_stream_element = B_element_stream[i_idx]

    // Add the current element to the min-heap.
    ADD current_stream_element TO k_largest_elements_min_heap

    // If the size of the heap exceeds A_rank_value, it means we have more than
    // A_rank_value "candidates" for the largest elements. Remove the smallest
    // one from the heap to maintain exactly A_rank_value largest elements.
    IF GET_HEAP_SIZE(k_largest_elements_min_heap) > A_rank_value THEN
      REMOVE_MIN_FROM_HEAP(k_largest_elements_min_heap)
    ENDIF

    // After processing the current_stream_element and adjusting the heap:
    // Check if we have processed at least A_rank_value elements.
    IF GET_HEAP_SIZE(k_largest_elements_min_heap) < A_rank_value THEN
      // Not enough elements seen yet to determine the A_rank_value-th largest.
      // Python code uses `i < A-1` condition for this.
      ADD -1 TO output_ans_list
    ELSE
      // The heap now contains the A_rank_value largest elements seen so far.
      // The root of the min-heap (PEEK_MIN) is the smallest of these,
      // which is the A_rank_value-th largest element.
      ADD PEEK_MIN(k_largest_elements_min_heap) TO output_ans_list
    ENDIF
  ENDFOR

  RETURN output_ans_list
ENDFUNCTION
```
