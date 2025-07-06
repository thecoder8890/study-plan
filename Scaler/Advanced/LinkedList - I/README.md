# Advanced/LinkedList - I

## Files in this topic:

- [Delete middle node of a Linked List.py](Delete middle node of a Linked List.py)
- [K reverse linked list.py](K reverse linked list.py)
- [Linked-List.py](Linked-List.py)
- [Linked_List_adv_I.pdf](Linked_List_adv_I.pdf)
- [Middle element of linked list.py](Middle element of linked list.py)
- [Remove Duplicates from Sorted List.py](Remove Duplicates from Sorted List.py)
- [Remove Nth Node from List End.py](Remove Nth Node from List End.py)
- [Reverse Link List II.py](Reverse Link List II.py)
- [Reverse Linked List.py](Reverse Linked List.py)

## Description

This section introduces foundational and intermediate concepts related to Linked Lists at an advanced level. It covers various operations such as traversal, insertion, deletion, reversal, finding middle elements, and handling specific list structures or problems.

### Pseudocode for Delete middle node of a Linked List.py
```pseudocode
// Assuming a ListNode class is defined with attributes:
//   val: integer value of the node
//   next: pointer to the next ListNode or null

FUNCTION delete_middle_node(A_head):
  // Handle edge cases: empty list or list with one node.
  // If empty, no middle node to delete.
  IF A_head IS NULL THEN
    RETURN NULL
  ENDIF
  // If single node, deleting it results in an empty list.
  IF A_head.next IS NULL THEN
    RETURN NULL
  ENDIF

  // Use a dummy node to simplify handling the head and nodes near the head.
  // `prev_node_of_slow` will track the node immediately preceding `slow_node`.
  dummy = new ListNode(0) // Value of dummy doesn't matter
  dummy.next = A_head
  prev_node_of_slow = dummy

  slow_node_ptr = A_head
  fast_node_ptr = A_head

  // Move `fast_node_ptr` two steps and `slow_node_ptr` one step at a time.
  // When `fast_node_ptr` reaches the end, `slow_node_ptr` will be at or near the middle.
  WHILE fast_node_ptr IS NOT NULL AND fast_node_ptr.next IS NOT NULL:
    prev_node_of_slow = slow_node_ptr // Update `prev` before moving `slow`
    slow_node_ptr = slow_node_ptr.next
    fast_node_ptr = fast_node_ptr.next.next
  ENDWHILE

  // At this point, `slow_node_ptr` is the middle node to be deleted.
  // (If even number of nodes, it's the second of the two middle nodes).
  // `prev_node_of_slow` is the node right before `slow_node_ptr`.

  // Bypass `slow_node_ptr` to delete it from the list.
  IF prev_node_of_slow IS NOT NULL AND slow_node_ptr IS NOT NULL THEN // Ensure they are valid
    prev_node_of_slow.next = slow_node_ptr.next
  ENDIF
  // The `slow_node_ptr` is now effectively removed.

  // The head of the modified list is pointed to by `dummy.next`.
  RETURN dummy.next
ENDFUNCTION
```

### Pseudocode for K reverse linked list.py
```pseudocode
// Assuming a ListNode class is defined with attributes:
//   val: integer value of the node
//   next: pointer to the next ListNode or null

FUNCTION k_reverse_list_in_groups(A_list_head, B_group_size):
  // Base Cases for recursion:
  // 1. If group size B is 1 or less, no reversal is needed for groups.
  // 2. If the list (or current segment) is empty, nothing to reverse.
  IF B_group_size <= 1 OR A_list_head IS NULL THEN
    RETURN A_list_head
  ENDIF

  // Pointers for reversing the current group of B_group_size nodes:
  current_node = A_list_head
  previous_node = NULL

  // Counter for nodes reversed in the current group.
  nodes_reversed_in_group_count = 0

  // Reverse up to B_group_size nodes in the current segment.
  WHILE current_node IS NOT NULL AND nodes_reversed_in_group_count < B_group_size:
    next_node_to_process = current_node.next // Store the next node before changing current_node.next
    current_node.next = previous_node     // Reverse the pointer of current_node

    // Move pointers one step forward for the reversal process
    previous_node = current_node
    current_node = next_node_to_process

    nodes_reversed_in_group_count = nodes_reversed_in_group_count + 1
  ENDWHILE

  // After the loop, for the current segment:
  // - `previous_node` is the new head of the reversed segment.
  // - `A_list_head` (the original head of this segment) is now the tail of this reversed segment.
  // - `current_node` points to the start of the next segment of the list (which also needs to be k-reversed).

  // If there are more nodes left in the list (`current_node` is not null),
  // recursively call k_reverse_list_in_groups for the remaining part of the list.
  // The `next` pointer of `A_list_head` (which is now the tail of the current reversed group)
  // should point to the head of the k-reversed remaining list.
  IF current_node IS NOT NULL THEN
    // `A_list_head` is the original head of this segment, now it's the tail of the reversed part.
    // Its `next` should connect to the head of the next k-reversed segment.
    A_list_head.next = k_reverse_list_in_groups(current_node, B_group_size)
  ENDIF
  // If `current_node` IS NULL, it means we reached the end of the list (or the segment was shorter than B_group_size).
  // In this case, `A_list_head.next` would have been correctly set to NULL if `previous_node` was its original `next`.
  // The reversal ensures `A_list_head.next` is the node that was previously before it, or NULL.
  // The Python code's `A.next = self.reverseList(cur, k)` handles `cur` being `None` correctly.

  // `previous_node_in_group` is the new head of this k-reversed segment (and thus the overall head if this was the first segment).
  RETURN previous_node
ENDFUNCTION
```

### Pseudocode for Reverse Linked List.py
```pseudocode
// Assuming a ListNode class is defined with attributes:
//   val: integer value of the node
//   next: pointer to the next ListNode or null

FUNCTION reverse_list_recursively(current_head_node):
  // Base Case: If the list is empty or has only one node,
  // it's already reversed (or is its own reverse).
  IF current_head_node IS NULL OR current_head_node.next IS NULL THEN
    RETURN current_head_node
  ENDIF

  // Recursive Step:
  // 1. Let `node_after_head` be the second node in the current list segment (current_head_node.next).
  node_after_head = current_head_node.next

  // 2. Recursively reverse the rest of the list starting from `node_after_head`.
  //    `new_head_of_reversed_sublist` will be the head of the reversed "tail" portion of the list.
  //    For example, if list is 1->2->3->4, and current_head_node is 1,
  //    this call reverses 2->3->4 into 4->3->2, and `new_head_of_reversed_sublist` becomes 4.
  new_head_of_reversed_sublist = reverse_list_recursively(node_after_head)

  // 3. After the recursive call returns, `node_after_head` (which was originally current_head_node.next)
  //    is now the TAIL of the reversed sublist.
  //    Make this tail's `next` pointer point back to `current_head_node`.
  //    Example: If sublist was 2->3->4, it becomes 4->3->2. `node_after_head` is still node 2.
  //             So, node_2.next = current_head_node (node 1). List becomes 4->3->2->1.
  node_after_head.next = current_head_node

  // 4. The `current_head_node` is now the new tail of the overall reversed list being constructed.
  //    Set its `next` pointer to NULL.
  //    Example: node_1.next = NULL. List is 4->3->2->1->NULL.
  current_head_node.next = NULL

  // 5. The `new_head_of_reversed_sublist` (which was returned by the recursive call on the tail)
  //    remains the head of the fully reversed list. Return it up the call stack.
  RETURN new_head_of_reversed_sublist
ENDFUNCTION
```

### Pseudocode for Remove Nth Node from List End.py
```pseudocode
// Assuming ListNode class with 'val' and 'next' attributes.

CLASS Solution_RemoveNthNode:
  // Helper function to calculate the length of a linked list.
  FUNCTION get_list_length(head_node):
    count = 0
    current = head_node
    WHILE current IS NOT NULL:
      count = count + 1
      current = current.next
    ENDWHILE
    RETURN count
  ENDFUNCTION

  // Main function to remove the Bth node from the end of list A.
  FUNCTION removeNthFromEnd(A_head, B_nodes_from_end):
    list_actual_length = this.get_list_length(A_head)

    // Python code's specific handling for B > list_actual_length: removes the head.
    IF B_nodes_from_end > list_actual_length THEN
      IF A_head IS NOT NULL THEN
        RETURN A_head.next
      ELSE
        RETURN NULL // Original list was already empty
      ENDIF
    ENDIF

    // If B_nodes_from_end == list_actual_length, it means remove the head node.
    // This case is correctly handled by the two-pointer logic with a dummy node.
    // No, if B_nodes_from_end == list_actual_length, and B_nodes_from_end > list_actual_length was false,
    // this means B_nodes_from_end == list_actual_length.
    // The Python code: fast pointer moves B steps. If B=L, fast becomes NULL.
    // Then while(fast) loop doesn't run. slow remains dummy. slow.next = slow.next.next deletes head. Correct.

    // Create a dummy node to simplify edge cases like removing the actual head.
    dummy_start_node = new ListNode(0) // Value doesn't matter
    dummy_start_node.next = A_head

    // `slow_ptr_prev_target` will point to the node just BEFORE the B-th node from the end.
    slow_ptr_prev_target = dummy_start_node
    // `fast_ptr_scout` is used to create a gap of B nodes.
    fast_ptr_scout = A_head // Python code starts fast pointer at A_head

    // Advance `fast_ptr_scout` B_nodes_from_end steps into the list.
    FOR _ FROM 1 TO B_nodes_from_end:
      IF fast_ptr_scout IS NOT NULL THEN
        fast_ptr_scout = fast_ptr_scout.next
      ELSE
        // This condition (B too large) should be caught by the initial B > list_actual_length check.
        // If reached, implies an issue or B was non-positive.
        RETURN dummy_start_node.next // Or error
      ENDIF
    ENDFOR

    // Now, `fast_ptr_scout` is B_nodes_from_end steps ahead of where `A_head` was.
    // `slow_ptr_prev_target` is at `dummy_start_node`.
    // Move both pointers forward until `fast_ptr_scout` reaches the end of the list (NULL).
    // When `fast_ptr_scout` becomes NULL, `slow_ptr_prev_target` will be pointing
    // to the node immediately preceding the B-th node from the end.
    WHILE fast_ptr_scout IS NOT NULL:
      slow_ptr_prev_target = slow_ptr_prev_target.next
      fast_ptr_scout = fast_ptr_scout.next
    ENDWHILE

    // `slow_ptr_prev_target` now points to the (L-B)-th node from the start (0-indexed),
    // which is the node just before the B-th node from the end.
    // Delete the B-th node from the end by bypassing it.
    IF slow_ptr_prev_target IS NOT NULL AND slow_ptr_prev_target.next IS NOT NULL THEN
      node_to_be_removed = slow_ptr_prev_target.next
      slow_ptr_prev_target.next = node_to_be_removed.next
      // node_to_be_removed can be freed if required by language
    ENDIF

    RETURN dummy_start_node.next // Head of the modified list
  ENDFUNCTION
ENDCLASS
```

### Pseudocode for Remove Duplicates from Sorted List.py
```pseudocode
// Assuming a ListNode class is defined with attributes:
//   val: integer value of the node
//   next: pointer to the next ListNode or null

FUNCTION remove_duplicates_from_sorted_list(A_original_head):
  // Handle an empty input list.
  IF A_original_head IS NULL THEN
    RETURN NULL
  ENDIF

  // Create a dummy node to act as the head of the new list (which will contain unique elements).
  // `new_list_tail_ptr` will always point to the last node of this new list being constructed.
  dummy_node = new ListNode(0) // Value of dummy node is irrelevant
  new_list_tail_ptr = dummy_node

  // `last_value_added_to_new_list` keeps track of the value of the most recent node
  // added to the new list, to compare against current node's value.
  last_value_added_to_new_list = NULL // Or a sentinel value not possible in list nodes

  // `current_node_scanner` iterates through the original list.
  current_node_scanner = A_original_head

  WHILE current_node_scanner IS NOT NULL:
    // Check if this is the first node being considered for the new list,
    // OR if its value is different from the last node added to the new list.
    IF last_value_added_to_new_list IS NULL OR current_node_scanner.val != last_value_added_to_new_list THEN
      // This node is unique (or the first one). Add it to the new list.

      // Update the value of the last unique element seen.
      last_value_added_to_new_list = current_node_scanner.val

      // Link the current tail of the new list to this unique node.
      new_list_tail_ptr.next = current_node_scanner

      // Advance the tail of the new list to this newly added node.
      new_list_tail_ptr = new_list_tail_ptr.next

      // Store the next node from the original list before potentially modifying current_node_scanner.next.
      next_node_in_original_list = current_node_scanner.next

      // Ensure the new tail of the constructed list points to NULL.
      // This is important because current_node_scanner might still be pointing to further nodes
      // in the original list (which could be duplicates we want to skip for the new list).
      new_list_tail_ptr.next = NULL

      // Move to the next node in the original list.
      current_node_scanner = next_node_in_original_list
    ELSE
      // current_node_scanner.val is a duplicate of the last node added to the new list.
      // Skip this node by advancing the scanner in the original list.
      current_node_scanner = current_node_scanner.next
    ENDIF
  ENDWHILE

  // The new list (with duplicates removed) starts from the node after the dummy_node.
  RETURN dummy_node.next
ENDFUNCTION
```

### Pseudocode for Middle element of linked list.py
```pseudocode
// Assuming a ListNode class is defined with attributes:
//   val: integer value of the node
//   next: pointer to the next ListNode or null

FUNCTION get_middle_element_value(A_list_head):
  // If the list is empty, there's no middle element.
  // (Problem constraints might guarantee a non-empty list).
  IF A_list_head IS NULL THEN
    RETURN some_error_or_default_value // e.g., -1, or raise an error
  ENDIF

  // Initialize two pointers, 'slow_ptr' and 'fast_ptr', both starting at the head of the list.
  slow_ptr = A_list_head
  fast_ptr = A_list_head

  // Traverse the list: move 'slow_ptr' one step at a time, and 'fast_ptr' two steps at a time.
  // When 'fast_ptr' reaches the end of the list (or goes one step beyond),
  // 'slow_ptr' will be pointing to the middle element.
  WHILE fast_ptr IS NOT NULL AND fast_ptr.next IS NOT NULL:
    slow_ptr = slow_ptr.next        // Move slow pointer one step
    fast_ptr = fast_ptr.next.next // Move fast pointer two steps
  ENDWHILE

  // After the loop, 'slow_ptr' is at the middle node.
  // If the list has an even number of elements, 'slow_ptr' will be at the second of the two middle nodes.
  // If the list was not empty, slow_ptr will not be NULL here.
  RETURN slow_ptr.val
ENDFUNCTION
```

### Pseudocode for Reverse Link List II.py
```pseudocode
// Assuming a ListNode class is defined with attributes:
//   val: integer value of the node
//   next: pointer to the next ListNode or null

FUNCTION reverse_segment_of_linked_list(A_head, B_start_pos, C_end_pos):
  // If B_start_pos equals C_end_pos, no reversal is needed.
  IF B_start_pos == C_end_pos THEN
    RETURN A_head
  ENDIF
  IF A_head IS NULL THEN
    RETURN NULL
  ENDIF

  // Create a dummy node to simplify handling cases where the reversal starts from the head.
  dummy_node = new ListNode(0) // Value is irrelevant
  dummy_node.next = A_head

  // Step 1: Traverse to find the node just before the start of the segment to be reversed.
  // This node will be `node_before_segment_start`.
  node_before_segment_start = dummy_node
  FOR count FROM 1 TO B_start_pos - 1:
    // Guard against B_start_pos being out of bounds
    IF node_before_segment_start.next IS NULL THEN RETURN A_head ENDIF
    node_before_segment_start = node_before_segment_start.next
  ENDFOR

  // `first_node_of_segment` is the B-th node, where the reversal begins.
  first_node_of_segment = node_before_segment_start.next
  IF first_node_of_segment IS NULL THEN RETURN A_head ENDIF // B_start_pos is out of bounds

  // Step 2: Reverse the sublist from position B to C.
  // `prev_in_reversal` will become the new head of the reversed sublist.
  // `current_in_reversal` iterates through the sublist to be reversed.
  prev_in_reversal = NULL
  current_in_reversal = first_node_of_segment
  num_nodes_to_reverse = C_end_pos - B_start_pos + 1

  FOR count FROM 1 TO num_nodes_to_reverse:
    IF current_in_reversal IS NULL THEN // C_end_pos is out of bounds
        // This implies the list ended before C_end_pos was reached.
        // Reversal done up to available nodes. Re-link what was reversed.
        node_before_segment_start.next = prev_in_reversal // Link to new head of (partially) reversed segment
        IF first_node_of_segment IS NOT NULL THEN
            first_node_of_segment.next = NULL // End of the reversed part
        ENDIF
        RETURN dummy_node.next
    ENDIF
    next_node_temp = current_in_reversal.next
    current_in_reversal.next = prev_in_reversal
    prev_in_reversal = current_in_reversal
    current_in_reversal = next_node_temp
  ENDFOR
  // After loop:
  // - `prev_in_reversal` is the head of the reversed segment (was C-th node).
  // - `first_node_of_segment` is the tail of the reversed segment (was B-th node).
  // - `current_in_reversal` is the node that was originally after the C-th node (start of the rest of the list).

  // Step 3: Connect the reversed segment back into the main list.
  // Link the node before the segment (node_before_segment_start) to the new head of the reversed segment (prev_in_reversal).
  node_before_segment_start.next = prev_in_reversal

  // Link the tail of the reversed segment (first_node_of_segment) to the part of the list
  // that comes after the reversed segment (current_in_reversal).
  IF first_node_of_segment IS NOT NULL THEN
    first_node_of_segment.next = current_in_reversal
  ENDIF

  RETURN dummy_node.next // Return the head of the overall modified list
ENDFUNCTION
```

### Pseudocode for Linked-List.py
```pseudocode
// Definition of a ListNode
CLASS ListNode:
  ATTRIBUTES:
    val: AnyType     // Value stored in the node
    next: ListNode OR Null // Pointer to the next node in the list

  CONSTRUCTOR(initial_value):
    this.val = initial_value
    this.next = Null
  ENDCONSTRUCTOR
ENDCLASS


// Definition of a LinkedList
CLASS LinkedList:
  ATTRIBUTES:
    head: ListNode OR Null // Pointer to the first node of the list
    ll_len: Integer       // Stores the current number of nodes in the list

  CONSTRUCTOR():
    this.head = Null
    this.ll_len = 0
  ENDCONSTRUCTOR

  // Internal helper function to get the node at a specific index and its predecessor.
  // Returns a pair: (predecessor_node, node_at_index).
  // If index is 0, predecessor_node is Null.
  FUNCTION __get_node_and_predecessor(target_0_based_index):
    IF target_0_based_index == 0 THEN
      RETURN (Null, this.head)
    ELSE
      predecessor = Null
      current = this.head
      count_down = target_0_based_index
      WHILE count_down > 0 AND current IS NOT Null:
        predecessor = current
        current = current.next
        count_down = count_down - 1
      ENDWHILE
      // `current` is now the node at target_0_based_index (or Null if index out of bounds)
      // `predecessor` is the node before `current`.
      RETURN (predecessor, current)
    ENDIF
  ENDFUNCTION

  // Inserts a new node with `value_to_insert` at `target_0_based_index`.
  FUNCTION insert_at_index(target_0_based_index, value_to_insert):
    // Python code allows insertion at index == current length (append).
    IF target_0_based_index < 0 OR target_0_based_index > this.ll_len THEN
      // Index out of bounds for insertion according to Python's specific logic.
      // Python's `if index > self.ll_len: return`
      RETURN // Or handle error
    ENDIF

    new_node = new ListNode(value_to_insert)

    IF target_0_based_index == 0 THEN // Insert at the head
      new_node.next = this.head
      this.head = new_node
    ELSE
      // Find the predecessor of the insertion point.
      // Python's `__get_node` returns (p,c). If index=0, p is None.
      // If inserting at index `i`, we need node at `i-1`.
      predecessor, node_at_target_idx = this.__get_node_and_predecessor(target_0_based_index)
      // Python: `p, c = self.__get_node(index)`
      // If `not p and not c` (empty list, index 0): Python `self.head = ListNode(val)`
      // If `not p` (insert at head): Python `new.next = c; self.head = new`
      // If `p` exists: Python `temp = p.next; p.next = new; new.next = temp`
      // This means `__get_node_internal` should return node at `index-1` if index > 0.
      // The current `__get_node_internal` returns pred and current for `target_index`.
      // Let's adjust to match Python's `insert_at` logic more directly.

      // Simplified logic matching Python's direct usage:
      IF target_0_based_index == 0 THEN // Python `if not p:` when index=0
          new_node.next = this.head
          this.head = new_node
      ELSE
          // Need predecessor of target_0_based_index
          predecessor_node = this.head
          FOR _ FROM 1 TO target_0_based_index - 1: // Iterate target_0_based_index-1 times
              predecessor_node = predecessor_node.next
          ENDLOOP
          new_node.next = predecessor_node.next
          predecessor_node.next = new_node
      ENDIF
    ENDIF
    this.ll_len = this.ll_len + 1
  ENDFUNCTION

  // Deletes the node at `target_0_based_index`.
  FUNCTION delete_at_index(target_0_based_index):
    IF target_0_based_index < 0 OR target_0_based_index >= this.ll_len THEN
      RETURN // Index out of bounds, nothing to delete
    ENDIF

    dummy_sentry = new ListNode(0) // Value doesn't matter
    dummy_sentry.next = this.head

    node_before_deletion_target = dummy_sentry
    // Traverse `target_0_based_index` times to get `node_before_deletion_target`
    // to point to the node just before the one to be deleted.
    FOR _ FROM 1 TO target_0_based_index: // Iterate target_0_based_index times
        node_before_deletion_target = node_before_deletion_target.next
    ENDFOR

    // node_to_be_deleted is node_before_deletion_target.next
    IF node_before_deletion_target.next IS NOT Null THEN // Check if there's a node to delete
        node_before_deletion_target.next = node_before_deletion_target.next.next // Bypass
    ENDIF

    this.head = dummy_sentry.next // Update head in case the original head was deleted
    this.ll_len = this.ll_len - 1
  ENDFUNCTION

  // Returns a string representation of the linked list.
  FUNCTION get_string_representation():
    list_of_node_values_as_strings = empty list
    current_node = this.head
    WHILE current_node IS NOT Null:
      ADD string_value_of(current_node.val) TO list_of_node_values_as_strings
      current_node = current_node.next
    ENDWHILE
    RETURN JOIN list_of_node_values_as_strings WITH " " (space character)
  ENDFUNCTION
ENDCLASS


// Global instance of LinkedList (as in the Python file structure)
// GLOBAL_LINKED_LIST_INSTANCE = new LinkedList()

// Global wrapper functions that operate on GLOBAL_LINKED_LIST_INSTANCE
FUNCTION global_insert_node(one_based_position, value_to_insert):
  zero_based_index = one_based_position - 1
  CALL GLOBAL_LINKED_LIST_INSTANCE.insert_at_index(zero_based_index, value_to_insert)
ENDFUNCTION

FUNCTION global_delete_node(one_based_position):
  zero_based_index = one_based_position - 1
  CALL GLOBAL_LINKED_LIST_INSTANCE.delete_at_index(zero_based_index)
ENDFUNCTION

FUNCTION global_print_ll():
  PRINT GLOBAL_LINKED_LIST_INSTANCE.get_string_representation()
ENDFUNCTION
```
