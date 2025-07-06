# Advanced/LinkedList- II

## Files in this topic:

- [Linked List Cheat Sheet.pdf](Linked List Cheat Sheet.pdf)
- [Linked_List_II_.pdf](Linked_List_II_.pdf)
- [List Cycle.py](List Cycle.py)
- [Merge Two Sorted Lists.py](Merge Two Sorted Lists.py)
- [Remove Loop from Linked List.py](Remove Loop from Linked List.py)
- [Reorder List.py](Reorder List.py)
- [Sort List.py](Sort List.py)

## Description

This section delves into more advanced problems and manipulations of Linked Lists. Topics likely include cycle detection and removal, complex list reordering, sorting linked lists efficiently, and merging multiple sorted lists.

### Pseudocode for List Cycle.py
```pseudocode
// Assuming a ListNode class is defined with attributes:
//   val: integer value of the node
//   next: pointer to the next ListNode or null

CLASS Solution_ListCycleDetection:

  // Main method: Detects a cycle and returns the starting node of the cycle.
  // If no cycle, returns null.
  FUNCTION detectCycle(A_list_head):
    IF A_list_head IS NULL THEN
      RETURN NULL // An empty list has no cycle.
    ENDIF

    // Step 1: Use Floyd's Tortoise and Hare algorithm to detect if a cycle exists
    // and find a meeting point of the slow and fast pointers within the cycle.
    cycle_check_result = this.find_meeting_point_in_cycle(A_list_head)
    cycle_exists = cycle_check_result.has_cycle
    meeting_node = cycle_check_result.node_where_met

    IF cycle_exists IS TRUE THEN
      // Step 2: If a cycle is found, determine the starting node of the cycle.
      // The Python code uses a method based on cycle length.

      //   a. Calculate the length of the cycle.
      length_of_detected_cycle = this.get_length_of_cycle(meeting_node)

      //   b. Initialize two pointers, ptr_A and ptr_B, both at the head of the list.
      ptr_A = A_list_head
      ptr_B = A_list_head

      //   c. Advance ptr_B by `length_of_detected_cycle` steps.
      FOR _ FROM 1 TO length_of_detected_cycle:
        IF ptr_B IS NULL THEN
          // This should not happen if a cycle was correctly detected and length calculated.
          RETURN NULL // Indicates an issue or inconsistent state.
        ENDIF
        ptr_B = ptr_B.next
      ENDFOR

      //   d. Move both ptr_A and ptr_B one step at a time.
      //      They will meet at the starting node of the cycle.
      WHILE ptr_A != ptr_B:
        IF ptr_A IS NULL OR ptr_B IS NULL THEN
          RETURN NULL // Safety, should not happen if logic is correct.
        ENDIF
        ptr_A = ptr_A.next
        ptr_B = ptr_B.next
      ENDWHILE
      RETURN ptr_A // This is the starting node of the cycle.
    ELSE
      // No cycle was found.
      RETURN NULL
    ENDIF
  ENDFUNCTION


  // Helper: Detects a cycle using Floyd's algorithm.
  // Returns a structure/pair: (has_cycle: boolean, node_where_met: ListNode or null)
  DEFINE FUNCTION find_meeting_point_in_cycle(head_node_param):
    slow_pointer = head_node_param
    fast_pointer = head_node_param

    WHILE fast_pointer IS NOT NULL AND fast_pointer.next IS NOT NULL:
      slow_pointer = slow_pointer.next        // Moves one step
      fast_pointer = fast_pointer.next.next // Moves two steps

      IF slow_pointer == fast_pointer THEN
        // Cycle detected. slow_pointer is a node within the cycle.
        RETURN (has_cycle: true, node_where_met: slow_pointer)
      ENDIF
    ENDWHILE

    // If loop finishes, fast_pointer reached null (end of list), so no cycle.
    RETURN (has_cycle: false, node_where_met: NULL)
  ENDFUNCTION


  // Helper: Calculates the length of a cycle, given a node known to be in the cycle.
  DEFINE FUNCTION get_length_of_cycle(any_node_inside_cycle):
    IF any_node_inside_cycle IS NULL THEN
      RETURN 0 // Or raise error, as this function expects a valid node in a cycle.
    ENDIF

    iterator_node = any_node_inside_cycle.next
    current_cycle_length = 1

    WHILE iterator_node != any_node_inside_cycle:
      IF iterator_node IS NULL THEN
        // This means it wasn't a cycle, or node was not in cycle. Error condition.
        RETURN 0 // Or raise error
      ENDIF
      iterator_node = iterator_node.next
      current_cycle_length = current_cycle_length + 1
    ENDWHILE

    RETURN current_cycle_length
  ENDFUNCTION
ENDCLASS
```

### Pseudocode for Sort List.py
```pseudocode
// Assuming ListNode class with 'val' and 'next' attributes.

CLASS Solution_SortLinkedList:

  // Main function to sort a linked list using Merge Sort.
  FUNCTION sortList(A_head_node):
    // Base Case: If the list is empty or has only one element, it's already sorted.
    IF A_head_node IS NULL OR A_head_node.next IS NULL THEN
      RETURN A_head_node
    ENDIF

    // Step 1: Find the middle of the list to split it into two halves.
    // `prev_of_slow` will point to the node just before the middle (tail of first half).
    // `slow_ptr` will point to the middle node (head of the second half).
    prev_of_slow = NULL
    slow_ptr = A_head_node
    fast_ptr = A_head_node

    WHILE fast_ptr IS NOT NULL AND fast_ptr.next IS NOT NULL:
      prev_of_slow = slow_ptr
      slow_ptr = slow_ptr.next
      fast_ptr = fast_ptr.next.next
    ENDWHILE

    // Split the list into two halves:
    // The first half ends at `prev_of_slow`. Terminate it by setting `prev_of_slow.next` to NULL.
    IF prev_of_slow IS NOT NULL THEN // Check in case list had only 2 nodes initially, prev_of_slow could be head
      prev_of_slow.next = NULL
    ENDIF

    head_of_first_half = A_head_node
    head_of_second_half = slow_ptr // `slow_ptr` is the head of the second sublist.

    // Step 2: Recursively sort the two halves.
    sorted_first_half = this.sortList(head_of_first_half)
    sorted_second_half = this.sortList(head_of_second_half)

    // Step 3: Merge the two sorted halves.
    fully_sorted_list_head = this.merge_sorted_lists_iterative(sorted_first_half, sorted_second_half)

    RETURN fully_sorted_list_head
  ENDFUNCTION


  // Helper function to merge two sorted linked lists iteratively.
  // head1 and head2 are heads of the two sorted lists to be merged.
  FUNCTION merge_sorted_lists_iterative(head1, head2):
    // Handle cases where one or both lists are empty.
    IF head1 IS NULL THEN RETURN head2 ENDIF
    IF head2 IS NULL THEN RETURN head1 ENDIF

    // Determine the head of the merged list and initialize a tail pointer for construction.
    merged_list_head = NULL
    current_tail = NULL

    IF head1.val <= head2.val THEN
      merged_list_head = head1
      current_tail = head1
      head1 = head1.next
    ELSE
      merged_list_head = head2
      current_tail = head2
      head2 = head2.next
    ENDIF

    // Loop as long as both lists still have nodes to compare.
    WHILE head1 IS NOT NULL AND head2 IS NOT NULL:
      IF head1.val <= head2.val THEN
        current_tail.next = head1
        head1 = head1.next
      ELSE
        current_tail.next = head2
        head2 = head2.next
      ENDIF
      current_tail = current_tail.next
    ENDWHILE

    // Append any remaining nodes from either list1 or list2.
    IF head1 IS NOT NULL THEN
      current_tail.next = head1
    ENDIF
    IF head2 IS NOT NULL THEN
      current_tail.next = head2
    ENDIF

    RETURN merged_list_head
  ENDFUNCTION
ENDCLASS
```

### Pseudocode for Reorder List.py
```pseudocode
// Assuming ListNode class with 'val' and 'next' attributes.

FUNCTION reorder_linked_list(A_head):
  // Handle cases with 0, 1, or 2 nodes, as they don't need reordering or are already reordered.
  IF A_head IS NULL OR A_head.next IS NULL OR A_head.next.next IS NULL THEN
    RETURN A_head
  ENDIF

  // Step 1: Find the middle of the linked list.
  // `slow_ptr` will point to the start of the second half.
  // `fast_ptr` is used to locate the middle.
  slow_ptr = A_head
  fast_ptr = A_head
  WHILE fast_ptr IS NOT NULL AND fast_ptr.next IS NOT NULL:
    slow_ptr = slow_ptr.next
    fast_ptr = fast_ptr.next.next
  ENDWHILE
  // `slow_ptr` is now the head of the second half.

  // Step 2: Split the list into two halves.
  // Find the node just before `slow_ptr` to terminate the first half.
  // (The Python code implicitly does this by not needing `prev_slow` if `slow` is reversed
  // and then merged carefully. For clarity, an explicit split is often shown).
  // Let `head_first_half = A_head`.
  // The node `prev_to_slow` that points to `slow_ptr` needs `prev_to_slow.next = NULL`.
  // Python's `slow` points to the actual head of the second half.
  // To split: iterate from `A_head` until `current.next == slow_ptr`, then `current.next = NULL`.
  // This step is missing in the direct Python translation but is crucial for conceptual clarity.
  // The Python code relies on the merge step to wire things correctly without explicit split.
  // Let's assume `head_second_half_unreversed = slow_ptr`.

  // Step 3: Reverse the second half of the list.
  prev_node_in_reversal = NULL
  current_node_to_reverse = slow_ptr // This is head_second_half_unreversed
  // Before reversing, the first half (ending at node before `slow_ptr`) should be detached.
  // To find node before `slow_ptr`:
  temp_ptr_to_find_end_of_first_half = A_head
  WHILE temp_ptr_to_find_end_of_first_half.next != slow_ptr:
      temp_ptr_to_find_end_of_first_half = temp_ptr_to_find_end_of_first_half.next
  ENDWHILE
  temp_ptr_to_find_end_of_first_half.next = NULL // Explicitly split

  WHILE current_node_to_reverse IS NOT NULL:
    next_node_original = current_node_to_reverse.next
    current_node_to_reverse.next = prev_node_in_reversal
    prev_node_in_reversal = current_node_to_reverse
    current_node_to_reverse = next_node_original
  ENDWHILE
  // `prev_node_in_reversal` is now the head of the reversed second half.
  head_reversed_second_half = prev_node_in_reversal

  // Step 4: Merge the first half and the reversed second half by interleaving.
  ptr_first_half = A_head
  ptr_second_half = head_reversed_second_half

  // Python's merge loop: `while second.next:`
  // This means loop continues as long as the reversed second half has at least two nodes.
  WHILE ptr_second_half IS NOT NULL AND ptr_second_half.next IS NOT NULL:
    // Store next pointers before re-wiring
    next_in_first_half = ptr_first_half.next
    next_in_second_half = ptr_second_half.next

    // Interleave: first_half node points to second_half node
    ptr_first_half.next = ptr_second_half
    // second_half node points to original next of first_half node
    ptr_second_half.next = next_in_first_half

    // Advance pointers
    ptr_first_half = next_in_first_half
    ptr_second_half = next_in_second_half
  ENDWHILE

  // If `ptr_second_half` is not NULL here, it's the last node of the (original) second list.
  // It needs to be linked after the current `ptr_first_half`.
  // The Python code handles this via the loop condition `while second.next`.
  // When `second.next` is null, `second` is the last node.
  // In the iteration *before* that, `first.next` was set to `second_previous`.
  // `second_previous.next` was set to `first`.
  // The Python code's tuple assignment `first.next, first = second, first.next` and
  // `second.next, second = first, second.next` handles this interleaving correctly.
  // The loop `while second.next:` in Python correctly terminates and links.
  // If list lengths differ, `ptr_first_half` will be the tail of the longer part.
  // The last node of the shorter part (`ptr_second_half` if it's the last one) gets linked correctly.

  RETURN A_head // The list is modified in-place, A_head remains the head.
ENDFUNCTION
```

### Pseudocode for Merge Two Sorted Lists.py
```pseudocode
// Assuming ListNode class is defined with attributes:
//   val: integer value of the node
//   next: pointer to the next ListNode or null

FUNCTION mergeTwoSortedLists_iterative(A_head_list1, B_head_list2):
  // Handle cases where one or both lists are empty at the start.
  IF A_head_list1 IS NULL THEN
    RETURN B_head_list2
  ENDIF
  IF B_head_list2 IS NULL THEN
    RETURN A_head_list1
  ENDIF

  // Determine the head of the merged list and initialize a tail pointer.
  merged_list_actual_head = NULL
  tail_ptr_of_merged_list = NULL

  IF A_head_list1.val <= B_head_list2.val THEN
    merged_list_actual_head = A_head_list1
    tail_ptr_of_merged_list = A_head_list1
    A_head_list1 = A_head_list1.next // Advance pointer of list A
  ELSE
    merged_list_actual_head = B_head_list2
    tail_ptr_of_merged_list = B_head_list2
    B_head_list2 = B_head_list2.next // Advance pointer of list B
  ENDIF

  // Loop as long as both lists have nodes remaining.
  WHILE A_head_list1 IS NOT NULL AND B_head_list2 IS NOT NULL:
    IF A_head_list1.val <= B_head_list2.val THEN
      // Append the current node from list A to the merged list.
      tail_ptr_of_merged_list.next = A_head_list1
      A_head_list1 = A_head_list1.next // Advance list A's pointer
    ELSE // B_head_list2.val < A_head_list1.val
      // Append the current node from list B to the merged list.
      tail_ptr_of_merged_list.next = B_head_list2
      B_head_list2 = B_head_list2.next // Advance list B's pointer
    ENDIF
    // Move the tail pointer of the merged list to the newly appended node.
    tail_ptr_of_merged_list = tail_ptr_of_merged_list.next
  ENDWHILE

  // After the loop, one of the lists might still have remaining nodes.
  // Append the rest of the non-exhausted list to the end of the merged list.
  IF A_head_list1 IS NOT NULL THEN
    tail_ptr_of_merged_list.next = A_head_list1
  ENDIF
  // It's also possible B_head_list2 has remaining nodes. The Python code uses two separate if statements.
  // Only one of these can be true if the WHILE loop condition was `A and B`.
  IF B_head_list2 IS NOT NULL THEN
    tail_ptr_of_merged_list.next = B_head_list2
  ENDIF

  RETURN merged_list_actual_head
ENDFUNCTION
```

### Pseudocode for Remove Loop from Linked List.py
```pseudocode
// Assuming ListNode class with 'val' and 'next' attributes.

CLASS Solution_RemoveListCycle:

  // Main method to detect and remove a cycle. Returns the head of the list.
  FUNCTION solve_remove_cycle(A_head):
    IF A_head IS NULL OR A_head.next IS NULL THEN
      RETURN A_head // No cycle possible in empty or single-node list.
    ENDIF

    // Step 1: Detect cycle and find meeting point using Floyd's algorithm.
    detection_info = this.helper_detect_cycle_and_find_meeting_node(A_head)
    has_cycle = detection_info.cycle_exists
    meeting_node_in_cycle = detection_info.node_met_at

    IF has_cycle IS TRUE THEN
      // Step 2: A cycle exists. Find the start of the cycle.
      // The Python code uses a method involving cycle length.

      //   a. Calculate the length of the cycle.
      cycle_length = this.helper_calculate_cycle_length(meeting_node_in_cycle)

      //   b. Initialize two pointers, p1 and p2, at the head of the list.
      p1_for_cycle_start = A_head
      p2_advanced_by_cycle_len = A_head

      //   c. Advance p2_advanced_by_cycle_len by `cycle_length` steps.
      FOR _ FROM 1 TO cycle_length:
        p2_advanced_by_cycle_len = p2_advanced_by_cycle_len.next
        // Assuming cycle exists and length is correct, p2 will not become NULL prematurely.
      ENDFOR

      //   d. `node_before_p2_when_met` will track the node that `p2_advanced_by_cycle_len`
      //      was at in the previous iteration. When p1 and p2 meet, this will be
      //      the node whose `next` pointer needs to be set to NULL to break the cycle.
      //      This corresponds to `prev` in the Python code.
      node_before_p2_when_met = NULL
      // Python initializes `prev = A`. This is simpler. If cycle starts at A, this `prev` logic is fine.

      // If cycle starts at head, p1 and p2 (after p2 advanced) might already be the same.
      IF p1_for_cycle_start == p2_advanced_by_cycle_len THEN
          // Cycle starts at head. Find the tail of the cycle.
          node_before_p2_when_met = p1_for_cycle_start // Start from cycle start
          FOR _ FROM 1 TO cycle_length - 1:
              node_before_p2_when_met = node_before_p2_when_met.next
          ENDFOR
      ELSE
          // Move both p1_for_cycle_start and p2_advanced_by_cycle_len one step at a time until they meet.
          // `node_before_p2_when_met` keeps track of `p2_advanced_by_cycle_len`'s previous position.
          WHILE p1_for_cycle_start != p2_advanced_by_cycle_len:
            node_before_p2_when_met = p2_advanced_by_cycle_len // Update before p2 moves
            p1_for_cycle_start = p1_for_cycle_start.next
            p2_advanced_by_cycle_len = p2_advanced_by_cycle_len.next
          ENDWHILE
          // Now, p1_for_cycle_start is the starting node of the cycle.
          // node_before_p2_when_met is the node whose `next` points to this cycle start.
      ENDIF

      // Step 3: Remove the cycle by setting the `next` pointer of `node_before_p2_when_met` to NULL.
      IF node_before_p2_when_met IS NOT NULL THEN
        node_before_p2_when_met.next = NULL
      ENDIF
      // Else: This might indicate a single-node list that formed a cycle to itself,
      // which should be handled by `node_before_p2_when_met` being the node itself.
      // The Python code's simple `prev.next = None` covers this more generally.

    ENDIF // End of IF has_cycle IS TRUE

    RETURN A_head // Return the head of the (potentially modified) list.
  ENDFUNCTION


  // Helper: Detects cycle and returns (boolean, meeting_node). Same as in List Cycle.py.
  DEFINE FUNCTION helper_detect_cycle_and_find_meeting_node(head):
    slow = head
    fast = head
    WHILE fast IS NOT NULL AND fast.next IS NOT NULL:
      slow = slow.next
      fast = fast.next.next
      IF slow == fast THEN
        RETURN (cycle_exists: true, node_met_at: slow)
      ENDIF
    ENDWHILE
    RETURN (cycle_exists: false, node_met_at: NULL)
  ENDFUNCTION


  // Helper: Calculates cycle length given a node in the cycle. Same as in List Cycle.py.
  DEFINE FUNCTION helper_calculate_cycle_length(node_in_cycle):
    IF node_in_cycle IS NULL THEN RETURN 0 ENDIF
    ptr = node_in_cycle.next
    len = 1
    WHILE ptr != node_in_cycle:
      IF ptr IS NULL THEN RETURN 0 ENDIF // Should not happen
      ptr = ptr.next
      len = len + 1
    ENDWHILE
    RETURN len
  ENDFUNCTION
ENDCLASS
```
