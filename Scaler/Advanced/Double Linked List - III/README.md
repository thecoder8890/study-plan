# Advanced/Double Linked List - III

## Files in this topic:

- [Add Two Numbers as Lists.py](Add Two Numbers as Lists.py)
- [Clone a Linked List.py](Clone a Linked List.py)
- [Copy List.py](Copy List.py)
- [Flatten a linked list.py](Flatten a linked list.py)
- [LRU Cache.py](LRU Cache.py)
- [LinkedListsIII.pdf](LinkedListsIII.pdf)
- [Merge K Sorted Lists.py](Merge K Sorted Lists.py)
- [Palindrome List.py](Palindrome List.py)
- [Swap List Nodes in pairs.py](Swap List Nodes in pairs.py)

## Description

This section covers advanced topics and complex problems related to Doubly Linked Lists, building on fundamental linked list operations. It may include problems involving intricate pointer manipulations, list transformations, or applications of doubly linked lists in designing other data structures or algorithms.

### Pseudocode for Add Two Numbers as Lists.py
```pseudocode
// Assuming a ListNode class is defined with attributes:
//   val: integer value of the node
//   next: pointer to the next ListNode or null

FUNCTION addTwoNumbers(list_A_head, list_B_head):
  // Create a dummy head node for the result list. This simplifies list construction.
  dummy_head_node = new ListNode(0)
  // current_tail_ptr will point to the last node in the result list being constructed.
  current_tail_ptr = dummy_head_node

  pointer_A = list_A_head
  pointer_B = list_B_head
  carry = 0 // Initialize carry for addition

  // Loop as long as there are digits in either input list or a carry remains from the last sum.
  WHILE pointer_A IS NOT NULL OR pointer_B IS NOT NULL OR carry > 0:
    // Get value from list A's current node, or 0 if list A is exhausted.
    value_A = 0
    IF pointer_A IS NOT NULL THEN
      value_A = pointer_A.val
      pointer_A = pointer_A.next // Move to next node in list A
    ENDIF

    // Get value from list B's current node, or 0 if list B is exhausted.
    value_B = 0
    IF pointer_B IS NOT NULL THEN
      value_B = pointer_B.val
      pointer_B = pointer_B.next // Move to next node in list B
    ENDIF

    // Calculate sum of current digits and carry from previous position.
    current_position_sum = value_A + value_B + carry

    // Determine the digit for the new node in the result list (sum MOD 10).
    new_digit = current_position_sum MOD 10
    // Determine the carry for the next position (sum / 10).
    carry = current_position_sum / 10 // Integer division

    // Create a new node with the calculated digit and append it to the result list.
    new_result_node = new ListNode(new_digit)
    current_tail_ptr.next = new_result_node
    // Move the tail pointer to this newly added node.
    current_tail_ptr = new_result_node

  ENDWHILE // Loop continues until both lists are processed and no carry remains.

  // The actual sum list starts from the node after the dummy head.
  RETURN dummy_head_node.next
ENDFUNCTION
```

### Pseudocode for Clone a Linked List.py
```pseudocode
// Assuming a RandomListNode class is defined with attributes:
//   label: value of the node
//   next: pointer to the next RandomListNode or null/None
//   random: pointer to another RandomListNode in the list or null/None

FUNCTION clonelist(original_list_head):
  // Step 1: Initialize a hash map (dictionary) to store mappings
  // from each node in the original list to its corresponding new cloned node.
  // Also, map None to None to correctly handle null next/random pointers.
  original_node_to_cloned_node_map = { null : null }

  // Step 2: First Iteration - Create a copy of each node and store mappings.
  // Traverse the original list. For each original node, create a new node
  // with the same label/value, and store the (original_node -> new_node) mapping.
  // At this stage, only labels are copied; next and random pointers of new nodes are not yet set.

  current_node_original = original_list_head
  WHILE current_node_original IS NOT NULL:
    // Create a new node with the same label as the original node.
    newly_cloned_node = new RandomListNode(current_node_original.label)

    // Add the mapping from the original node to its new clone into the map.
    original_node_to_cloned_node_map[current_node_original] = newly_cloned_node

    // Move to the next node in the original list.
    current_node_original = current_node_original.next
  ENDWHILE

  // Step 3: Second Iteration - Assign 'next' and 'random' pointers for the cloned nodes.
  // Traverse the original list again.
  current_node_original = original_list_head
  WHILE current_node_original IS NOT NULL:
    // Get the cloned node that corresponds to the current original node.
    cloned_node_for_current_original = original_node_to_cloned_node_map[current_node_original]

    // Set the 'next' pointer of the cloned node.
    // original_node.next points to an original node (or null).
    // Use the map to find the cloned counterpart of original_node.next.
    cloned_node_for_current_original.next = original_node_to_cloned_node_map[current_node_original.next]

    // Set the 'random' pointer of the cloned node.
    // original_node.random points to an original node (or null).
    // Use the map to find the cloned counterpart of original_node.random.
    cloned_node_for_current_original.random = original_node_to_cloned_node_map[current_node_original.random]

    // Move to the next node in the original list.
    current_node_original = current_node_original.next
  ENDWHILE

  // Step 4: The head of the cloned list is the cloned node corresponding to the original list's head.
  RETURN original_node_to_cloned_node_map[original_list_head]
ENDFUNCTION
```

### Pseudocode for Swap List Nodes in pairs.py
```pseudocode
// Assuming a ListNode class is defined with attributes:
//   val: integer value of the node
//   next: pointer to the next ListNode or null

FUNCTION swapPairsOfNodes(A_head_of_list):
  // Create a dummy node to act as a predecessor for the head, simplifying edge cases.
  dummy_node = new ListNode(0)
  // `tail_of_swapped_part` will always point to the last node of the
  // already correctly swapped portion of the list. It starts at the dummy node.
  tail_of_swapped_part = dummy_node
  tail_of_swapped_part.next = A_head_of_list // Link dummy to the original list head

  // `current_node_ptr` is used to iterate through the list, pointing to the
  // first node of the pair to be potentially swapped.
  current_node_ptr = A_head_of_list

  // Loop as long as there are at least two nodes remaining to form a pair.
  WHILE current_node_ptr IS NOT NULL AND current_node_ptr.next IS NOT NULL:
    // Identify the nodes in the current pair and the node following the pair.
    first_in_pair = current_node_ptr          // Node A in Python code
    second_in_pair = current_node_ptr.next    // Node A.next (n2n) in Python code
    node_after_pair = second_in_pair.next   // Node A.next.next (f) in Python code

    // Perform the swap and update links:
    // 1. The `tail_of_swapped_part` (which was before first_in_pair) now points to second_in_pair.
    tail_of_swapped_part.next = second_in_pair  // Python: ans.next = n2n

    // 2. second_in_pair (which is now first in the pair) points to first_in_pair.
    second_in_pair.next = first_in_pair       // Python: (ans=n2n).next = n

    // 3. first_in_pair (which is now second in the pair) points to node_after_pair.
    //    This correctly links to the rest of the list or to NULL if it's the end.
    //    The Python code's `ans.next = None` followed by `A=f` and then the next iteration's
    //    `ans.next = n2n` (or the final `if A: ans.next = A`) achieves this linking.
    //    For clarity, we link it directly here in a standard iterative swap.
    first_in_pair.next = node_after_pair      // This corresponds to Python's deferred linking logic

    // Advance the `tail_of_swapped_part` to the new end of the swapped portion,
    // which is now `first_in_pair`.
    tail_of_swapped_part = first_in_pair

    // Move `current_node_ptr` to the start of the next pair to be processed.
    current_node_ptr = node_after_pair
  ENDWHILE

  // If there was an odd number of nodes, `current_node_ptr` might still point to the last unswapped node.
  // The line `first_in_pair.next = node_after_pair` inside the loop correctly handles
  // linking to this last odd node if `node_after_pair` was that node.
  // If the loop finished because `current_node_ptr` became NULL or `current_node_ptr.next` became NULL,
  // `tail_of_swapped_part.next` is already correctly pointing to `current_node_ptr` (which could be NULL or the last node).
  // The Python code handles this with `if A: ans.next = A;` which is `tail_of_swapped_part.next = current_node_ptr;`

  RETURN dummy_node.next // The `next` of the dummy node is the new head of the modified list.
ENDFUNCTION
```

### Pseudocode for Copy List.py
```pseudocode
// Assuming a RandomListNode class is defined with attributes:
//   label: value of the node
//   next: pointer to the next RandomListNode or null/None
//   random: pointer to another RandomListNode in the list or null/None

FUNCTION copyRandomList(original_head_node):
  // Step 1: Initialize a hash map (dictionary) to store mappings from original nodes to new cloned nodes.
  // Include a mapping for None to None to correctly handle null next/random pointers.
  map_original_to_new = { null : null }

  // Step 2: First Iteration - Create a clone for each node and populate the map.
  // Traverse the original list. For each node, create a new node with the same label.
  // Store the mapping: original_node -> cloned_node.
  current_original_ptr = original_head_node
  WHILE current_original_ptr IS NOT NULL:
    // Create a new node with the same label as the current original node.
    newly_cloned_node = new RandomListNode(current_original_ptr.label)
    // Store this mapping.
    map_original_to_new[current_original_ptr] = newly_cloned_node
    // Move to the next node in the original list.
    current_original_ptr = current_original_ptr.next
  ENDWHILE

  // Step 3: Second Iteration - Assign 'next' and 'random' pointers for all cloned nodes.
  // Traverse the original list again.
  current_original_ptr = original_head_node
  WHILE current_original_ptr IS NOT NULL:
    // Get the cloned node that corresponds to the current_original_ptr node.
    cloned_node_counterpart = map_original_to_new[current_original_ptr]

    // Set the 'next' pointer of the cloned_node_counterpart.
    // current_original_ptr.next points to an original node (or null).
    // map_original_to_new[current_original_ptr.next] gives its cloned counterpart.
    cloned_node_counterpart.next = map_original_to_new[current_original_ptr.next]

    // Set the 'random' pointer of the cloned_node_counterpart similarly.
    cloned_node_counterpart.random = map_original_to_new[current_original_ptr.random]

    // Move to the next node in the original list.
    current_original_ptr = current_original_ptr.next
  ENDWHILE

  // Step 4: Return the head of the fully cloned list.
  // This is the cloned node that corresponds to the original_list_head.
  RETURN map_original_to_new[original_head_node]
ENDFUNCTION
```

### Pseudocode for Palindrome List.py
```pseudocode
// Assuming a ListNode class is defined with attributes:
//   val: integer value of the node
//   next: pointer to the next ListNode or null

FUNCTION is_list_palindrome(A_list_head):
  // Handle edge cases: empty list or single-node list are palindromes.
  IF A_list_head IS NULL OR A_list_head.next IS NULL THEN
    RETURN 1 // Represents true (is a palindrome)
  ENDIF

  // Step 1: Find the middle of the linked list.
  // The Python code uses a dummy node before `A_list_head` for `slow` initialization.
  // `slow_ptr` will end up pointing to the node just before the start of the second half.
  dummy_node_for_slow = new ListNode(0) // Value doesn't matter
  dummy_node_for_slow.next = A_list_head
  slow_ptr = dummy_node_for_slow
  fast_ptr = A_list_head // Python initializes fast to A directly

  WHILE fast_ptr IS NOT NULL AND fast_ptr.next IS NOT NULL:
    slow_ptr = slow_ptr.next
    fast_ptr = fast_ptr.next.next
  ENDWHILE
  // After this loop, `slow_ptr.next` is the head of the second half of the list.

  // Step 2: Split the list into two halves and reverse the second half.
  head_of_second_part = slow_ptr.next // Head of the part to be reversed
  slow_ptr.next = NULL               // Terminate the first part of the list

  // Reverse the second part (starting from head_of_second_part)
  previous_node = NULL
  current_node = head_of_second_part
  WHILE current_node IS NOT NULL:
    next_temp_node = current_node.next
    current_node.next = previous_node // Reverse the link
    previous_node = current_node     // Move previous_node forward
    current_node = next_temp_node    // Move current_node forward
  ENDWHILE
  // `previous_node` now points to the head of the reversed second part.
  head_of_reversed_second_part = previous_node

  // Step 3: Compare the first half with the reversed second half.
  pointer_to_first_half = A_list_head
  pointer_to_reversed_second_half = head_of_reversed_second_part

  is_palindrome_flag = 1 // Assume true initially
  WHILE pointer_to_first_half IS NOT NULL AND pointer_to_reversed_second_half IS NOT NULL:
    IF pointer_to_first_half.val != pointer_to_reversed_second_half.val THEN
      is_palindrome_flag = 0 // Mismatch found
      BREAK WHILE
    ENDIF
    pointer_to_first_half = pointer_to_first_half.next
    pointer_to_reversed_second_half = pointer_to_reversed_second_half.next
  ENDWHILE

  // Optional: Restore the original list structure by reversing the second half again
  // and re-linking it to the first half. This is good practice if the list
  // structure needs to be preserved after the check. The Python code does not do this.

  RETURN is_palindrome_flag
ENDFUNCTION
```

### Pseudocode for Merge K Sorted Lists.py
```pseudocode
// Assuming a ListNode class is defined with attributes:
//   val: integer value of the node
//   next: pointer to the next ListNode or null

CLASS Solution:
  // Main function to merge K sorted linked lists.
  // A_list_of_list_heads: A list where each element is the head of a sorted linked list.
  FUNCTION mergeKLists(A_list_of_list_heads):
    num_input_lists = length of A_list_of_list_heads

    // Base Cases for the recursive division of the list of lists:
    IF num_input_lists == 0 THEN
      RETURN NULL // No lists to merge, result is an empty list.
    ENDIF
    IF num_input_lists == 1 THEN
      // Only one list remains, which is already sorted and merged by definition.
      RETURN A_list_of_list_heads[0]
    ENDIF

    // Divide: Split the list of K lists into two halves.
    mid_index = num_input_lists / 2 // Integer division

    // Create sub-lists for recursive calls.
    first_half_of_lists = sublist of A_list_of_list_heads from index 0 to mid_index - 1
    second_half_of_lists = sublist of A_list_of_list_heads from index mid_index to num_input_lists - 1

    // Conquer: Recursively call mergeKLists on each half.
    // This will result in two fully merged and sorted lists.
    merged_list_from_first_half = this.mergeKLists(first_half_of_lists)
    merged_list_from_second_half = this.mergeKLists(second_half_of_lists)

    // Combine: Merge the two sorted lists obtained from the recursive calls.
    final_merged_list_head = this.mergeTwoSortedLists(merged_list_from_first_half, merged_list_from_second_half)

    RETURN final_merged_list_head
  ENDFUNCTION


  // Helper function to merge two already sorted linked lists.
  // h1 and h2 are the head nodes of the two sorted lists to be merged.
  FUNCTION mergeTwoSortedLists(h1_list_head, h2_list_head):
    // Base Cases for merging two individual lists:
    IF h1_list_head IS NULL AND h2_list_head IS NULL THEN // Both lists are empty
      RETURN NULL
    ENDIF
    IF h1_list_head IS NULL THEN // First list is empty
      RETURN h2_list_head
    ENDIF
    IF h2_list_head IS NULL THEN // Second list is empty
      RETURN h1_list_head
    ENDIF

    // Recursive step for merging:
    // Compare the values at the heads of the two lists.
    // The node with the smaller value becomes the head of the merged list.
    // The 'next' pointer of this chosen head is then set to the result of merging
    // the rest of its list with the other entire list.

    result_merged_head = NULL
    IF h1_list_head.val <= h2_list_head.val THEN
      result_merged_head = h1_list_head
      result_merged_head.next = this.mergeTwoSortedLists(h1_list_head.next, h2_list_head)
    ELSE // h2_list_head.val < h1_list_head.val
      result_merged_head = h2_list_head
      result_merged_head.next = this.mergeTwoSortedLists(h1_list_head, h2_list_head.next)
    ENDIF

    RETURN result_merged_head
  ENDFUNCTION
ENDCLASS
```

### Pseudocode for LRU Cache.py
```pseudocode
CLASS LRUCache:
  // Attributes of the LRUCache class:
  //   cache_storage: A dictionary (hash map) to store key-value pairs.
  //                  Allows O(1) average time access to values by key.
  //   usage_tracker_deque: A double-ended queue (deque) to maintain the order of key usage.
  //                        The rightmost element is the most recently used (MRU).
  //                        The leftmost element is the least recently used (LRU).
  //                        This deque stores the keys.
  //   capacity_limit: The maximum number of key-value pairs the cache can hold.

  // Constructor
  FUNCTION initialize_LRUCache(capacity_value):
    INITIALIZE this.cache_storage as an empty dictionary
    INITIALIZE this.usage_tracker_deque as an empty deque
    this.capacity_limit = capacity_value
  ENDFUNCTION

  // Get method: Retrieves the value for a given key from the cache.
  FUNCTION get(key):
    IF key IS NOT PRESENT IN this.cache_storage THEN
      // Key is not in the cache.
      RETURN -1
    ELSE
      // Key is present in the cache.
      // 1. Mark this key as most recently used by moving it to the end of the deque.
      //    (Note: `remove_element` in a standard deque can be O(N) where N is deque size.
      //     A more optimized LRU uses a custom doubly linked list for O(1) move.)
      this.usage_tracker_deque.remove_element(key)
      this.usage_tracker_deque.append_to_right_end(key) // Add to the MRU end

      // 2. Return the value associated with the key.
      RETURN this.cache_storage[key]
    ENDIF
  ENDFUNCTION

  // Set method: Inserts or updates a key-value pair in the cache.
  FUNCTION set(key, value):
    IF key IS PRESENT IN this.cache_storage THEN
      // Key already exists. This is an update.
      // Mark this key as most recently used by moving it to the end of the deque.
      this.usage_tracker_deque.remove_element(key)
      // The key will be re-appended to the deque's end later.
      // The value in cache_storage will be updated.
    ELSE IF length of this.cache_storage IS EQUAL TO this.capacity_limit THEN
      // Cache is full and this is a new key.
      // Evict the least recently used item before inserting the new one.
      // 1. Get the least recently used key from the front of the deque.
      lru_key_to_evict = this.usage_tracker_deque.pop_from_left_end()
      // 2. Remove the lru_key_to_evict from the cache_storage dictionary.
      REMOVE lru_key_to_evict FROM this.cache_storage
    ENDIF

    // Now, either the key was already present (and removed from its old position in deque)
    // or there is space for the new key (either cache was not full, or an item was evicted).

    // Add the key to the end of the deque (marking it as most recently used/added).
    this.usage_tracker_deque.append_to_right_end(key)
    // Insert or update the key-value pair in the cache_storage dictionary.
    this.cache_storage[key] = value
    // Typically, set operations do not return a value.
  ENDFUNCTION
ENDCLASS
```

### Pseudocode for Flatten a linked list.py
```pseudocode
// Assuming a ListNode class is defined with attributes:
//   val: integer value of the node
//   right: pointer to the next ListNode in the same horizontal level, or null
//   down: pointer to the head of a sorted sub-list linked vertically, or null

// Main function to flatten the multi-level linked list
FUNCTION flatten(root_node_of_level):
  // Base Case for recursion: If the current level is empty or has only one node,
  // it's considered "flattened" with respect to its 'right' pointers.
  // The 'down' list from this single node (if any) is already a flattened list.
  IF root_node_of_level IS NULL OR root_node_of_level.right IS NULL THEN
    RETURN root_node_of_level
  ENDIF

  // Step 1: Find the middle node of the current horizontal level list.
  middle_node_of_level = find_middle_horizontal_node(root_node_of_level)

  // Step 2: Split the current horizontal level list into two halves.
  // The first half starts at root_node_of_level.
  // The second half starts at the node after middle_node_of_level.
  head_of_first_half = root_node_of_level
  head_of_second_half = middle_node_of_level.right
  // Terminate the first half by setting the 'right' pointer of its last node (middle_node_of_level) to null.
  middle_node_of_level.right = NULL

  // Step 3: Recursively flatten both halves.
  // Each recursive call will return the head of a fully flattened, sorted list (linked by 'down' pointers).
  flattened_list_from_first_half = flatten(head_of_first_half)
  flattened_list_from_second_half = flatten(head_of_second_half)

  // Step 4: Merge the two sorted, flattened lists (which are linked by 'down' pointers).
  final_merged_and_flattened_list_head = merge_two_sorted_vertical_lists(flattened_list_from_first_half, flattened_list_from_second_half)

  RETURN final_merged_and_flattened_list_head
ENDFUNCTION


// Helper function to find the middle node of a list connected by 'right' pointers
FUNCTION find_middle_horizontal_node(head_of_horizontal_list):
  IF head_of_horizontal_list IS NULL OR head_of_horizontal_list.right IS NULL THEN
    RETURN head_of_horizontal_list // List is empty or has one node
  ENDIF

  slow_pointer = head_of_horizontal_list
  fast_pointer = head_of_horizontal_list

  // Move slow_pointer one step and fast_pointer two steps at a time using 'right' pointers.
  WHILE fast_pointer.right IS NOT NULL AND fast_pointer.right.right IS NOT NULL:
    slow_pointer = slow_pointer.right
    fast_pointer = fast_pointer.right.right
  ENDWHILE

  // When fast_pointer reaches the end, slow_pointer is at the middle node (or first of two for even length).
  RETURN slow_pointer
ENDFUNCTION


// Helper function to merge two sorted lists that are linked by 'down' pointers
FUNCTION merge_two_sorted_vertical_lists(list1_head_vertical, list2_head_vertical):
  // Base Cases for recursive merge:
  IF list1_head_vertical IS NULL THEN
    RETURN list2_head_vertical // If list1 is empty, result is list2
  ENDIF
  IF list2_head_vertical IS NULL THEN
    RETURN list1_head_vertical // If list2 is empty, result is list1
  ENDIF

  // Recursive Merge Step:
  merged_list_head_node = NULL
  IF list1_head_vertical.val <= list2_head_vertical.val THEN
    // list1_head_vertical's node comes first.
    merged_list_head_node = list1_head_vertical
    // Recursively merge the rest of list1 (list1_head_vertical.down) with the entirety of list2.
    // The result of this merge becomes the 'down' pointer of merged_list_head_node.
    merged_list_head_node.down = merge_two_sorted_vertical_lists(list1_head_vertical.down, list2_head_vertical)
  ELSE // list2_head_vertical.val < list1_head_vertical.val
    // list2_head_vertical's node comes first.
    merged_list_head_node = list2_head_vertical
    // Recursively merge list1 with the rest of list2 (list2_head_vertical.down).
    merged_list_head_node.down = merge_two_sorted_vertical_lists(list1_head_vertical, list2_head_vertical.down)
  ENDIF

  RETURN merged_list_head_node
ENDFUNCTION
```
