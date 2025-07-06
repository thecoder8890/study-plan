# Advanced/Problem on Trees

## Files in this topic:

- [Deserialize Binary Tree.py](Deserialize Binary Tree.py)
- [Equal Tree Partition.py](Equal Tree Partition.py)
- [Next Pointer Binary Tree.py](Next Pointer Binary Tree.py)
- [Odd and Even Levels.py](Odd and Even Levels.py)
- [Path Sum.py](Path Sum.py)
- [Problems_on_treeees.pdf](Problems_on_treeees.pdf)
- [Serialize Binary Tree.py](Serialize Binary Tree.py)

## Description

This section covers a variety of problems related to tree data structures, primarily binary trees. Topics include tree traversals, construction from traversals, properties like path sums, partitioning, serialization/deserialization, and specialized pointer manipulations.

### Pseudocode for Deserialize Binary Tree.py
```pseudocode
// Assuming TreeNode class with 'val', 'left', 'right' attributes.
// Assumes Deque data structure with operations: add_to_right_end (append),
// pop_from_left_end (popleft), is_empty, get_length, and initialize from list.

FUNCTION deserialize_tree_from_level_order(A_level_order_values_list):
  // If the input list is empty, the tree is empty.
  IF A_level_order_values_list IS EMPTY THEN
    RETURN NULL
  ENDIF

  // Use a deque to efficiently access values from the level-order list.
  values_deque = new Deque(A_level_order_values_list)

  // `parents_deque` will store nodes whose children need to be assigned.
  parents_deque = new Deque()

  // Create the root node from the first value.
  // Python code does not check if the first value itself is -1 (null root).
  // Assuming first value corresponds to a valid root if list is not empty.
  root_node_val = values_deque.pop_from_left_end()
  IF root_node_val == -1 THEN // If -1 can represent a null root for an otherwise non-empty list
    RETURN NULL
  ENDIF
  root_node = new TreeNode(root_node_val)
  parents_deque.add_to_right_end(root_node) // Add root to process its children

  // Process nodes level by level using the parents_deque.
  WHILE parents_deque IS NOT EMPTY:
    // The Python code processes all nodes at the current level before moving to next.
    // This is typical for BFS-like construction.
    num_nodes_at_current_level = parents_deque.get_length()

    FOR _ FROM 1 TO num_nodes_at_current_level:
      current_parent = parents_deque.pop_from_left_end()

      // Attempt to create and link the left child.
      IF values_deque IS NOT EMPTY THEN
        left_val = values_deque.pop_from_left_end()
        IF left_val != -1 THEN // -1 signifies a null child in the input list
          current_parent.left = new TreeNode(left_val)
          parents_deque.add_to_right_end(current_parent.left) // Add new child to process its children
        ELSE
          current_parent.left = NULL // Explicitly set to null if -1
        ENDIF
      ELSE
        // No more values in the input list for a left child; implies tree ends here or malformed input.
        current_parent.left = NULL
      ENDIF

      // Attempt to create and link the right child.
      IF values_deque IS NOT EMPTY THEN
        right_val = values_deque.pop_from_left_end()
        IF right_val != -1 THEN
          current_parent.right = new TreeNode(right_val)
          parents_deque.add_to_right_end(current_parent.right)
        ELSE
          current_parent.right = NULL
        ENDIF
      ELSE
        // No more values for a right child.
        current_parent.right = NULL
      ENDIF
    ENDFOR // End processing nodes at current level
  ENDWHILE // End while parents_deque is not empty

  RETURN root_node
ENDFUNCTION
```

### Pseudocode for Equal Tree Partition.py
```pseudocode
// Assuming TreeNode class with 'val', 'left', 'right' attributes.
// This solution uses a class attribute or a global-like set to store subtree sums.

CLASS Solution_EqualTreePartitionCheck:
  // `all_subtree_sums_set` will store the sum of each subtree encountered during DFS.
  // It must be reset for each call to the main `solve` method if the Solution object is reused.
  ATTRIBUTE all_subtree_sums_set : Set of Integers

  CONSTRUCTOR():
    this.all_subtree_sums_set = new empty Set()
  ENDCONSTRUCTOR

  // Main function to check if the tree can be partitioned into two subtrees of equal sum
  // by removing a single edge.
  FUNCTION solve_can_tree_be_equally_partitioned(A_tree_root_node):
    // Reset the set for the current call.
    this.all_subtree_sums_set = new empty Set()

    // Step 1: Perform a DFS traversal to calculate the sum of all nodes in the tree (total_sum)
    // and to populate `all_subtree_sums_set` with the sums of all possible subtrees
    // (except the sum of the entire tree itself, and typically not sums of empty subtrees if children are null).
    total_sum_of_tree = this.dfs_and_collect_subtree_sums(A_tree_root_node)

    // Step 2: Check conditions for a valid partition:
    // a) The total_sum_of_tree must be an even number.
    IF total_sum_of_tree MOD 2 != 0 THEN
      RETURN 0 // False (Cannot partition an odd sum into two equal integer sums)
    ENDIF

    // b) Half of the total_sum_of_tree must be present in `all_subtree_sums_set`.
    //    This means there exists a subtree whose sum is exactly half of the total sum.
    //    Removing the edge connecting this subtree to its parent would create the partition.
    target_half_sum = total_sum_of_tree / 2 // Integer division

    // Special case: if total_sum_of_tree is 0, target_half_sum is 0.
    // We need to ensure that if target_half_sum is 0, it corresponds to a non-empty subtree sum
    // unless the entire tree is just a single node with value 0 (in which case, no partition possible by removing an edge).
    // The Python code's logic `if A.left: s.add(l)` means 0 is added only if it's sum of non-null subtree.
    // If `total_sum_of_tree == 0` and `0` is in `s`, means a subtree sums to 0, and the other part also sums to 0.
    // This condition needs to be careful about the case `total_sum_of_tree == 0`.
    // The check `total // 2 in s` works. If total is 0, `total//2` is 0. If `s` contains 0 from a valid subtree, it's true.
    // If `A` is just a single node with value 0: `total=0`. `s` is empty. `0 in s` is false. Returns 0. Correct.
    IF target_half_sum IS PRESENT IN this.all_subtree_sums_set THEN
      // Exception: if total_sum_of_tree is 0, and target_half_sum (0) is in the set,
      // this is only a valid partition if the tree has more than one node.
      // (The problem implies removing an edge, so single node tree cannot be partitioned).
      // The set `s` will not contain `total_sum_of_tree` due to how it's populated.
      // If `total_sum_of_tree == 0` and `0 IN s`, it means a *proper* subtree sums to 0.
      // This implies the other part also sums to 0. This is a valid partition.
      // The Python code `total//2 in s` correctly handles this.
      RETURN 1 // True (Partition is possible)
    ELSE
      RETURN 0 // False (No such subtree sum found)
    ENDIF
  ENDFUNCTION


  // Recursive DFS helper:
  // 1. Calculates the sum of the subtree rooted at `node`.
  // 2. Adds the sums of `node.left`'s subtree and `node.right`'s subtree to `all_subtree_sums_set`.
  DEFINE FUNCTION dfs_and_collect_subtree_sums(node):
    // Base Case: If node is null, its subtree sum is 0.
    IF node IS NULL THEN
      RETURN 0
    ENDIF

    // Recursively calculate sums of left and right subtrees.
    left_subtree_total_sum = this.dfs_and_collect_subtree_sums(node.left)
    right_subtree_total_sum = this.dfs_and_collect_subtree_sums(node.right)

    // If the left child exists, its subtree sum is a candidate for partitioning.
    IF node.left IS NOT NULL THEN
      ADD left_subtree_total_sum TO this.all_subtree_sums_set
    ENDIF

    // If the right child exists, its subtree sum is a candidate for partitioning.
    IF node.right IS NOT NULL THEN
      ADD right_subtree_total_sum TO this.all_subtree_sums_set
    ENDIF

    // Return the sum of the subtree rooted at the current `node`.
    RETURN left_subtree_total_sum + right_subtree_total_sum + node.val
  ENDFUNCTION
ENDCLASS
```

### Pseudocode for Serialize Binary Tree.py
```pseudocode
// Note: The Python filename is "Serialize Binary Tree.py", but the implemented `solve` method
// actually performs DESERIALIZATION of a binary tree from a level-order list representation
// where -1 indicates a null child. This pseudocode describes the deserialization logic found.

// Assuming TreeNode class with 'val', 'left', 'right' attributes.
// Assumes Deque data structure with operations: add_to_right_end (append),
// pop_from_left_end (popleft), is_empty, get_length, and initialize from list.

FUNCTION deserialize_tree_from_level_order_list(A_level_order_values_with_nulls):
  // If the input list of values is empty, the tree is empty.
  IF A_level_order_values_with_nulls IS EMPTY THEN
    RETURN NULL
  ENDIF

  // Use a deque for efficient removal of values from the front (level-order processing).
  values_input_deque = new Deque(A_level_order_values_with_nulls)

  // `parent_nodes_queue` will store parent nodes whose children need to be processed and linked.
  parent_nodes_queue = new Deque()

  // Create the root node using the first value from the values_input_deque.
  root_node_value = values_input_deque.pop_from_left_end()
  // If the first value itself signifies a null tree (e.g., list starts with -1).
  IF root_node_value == -1 THEN
    RETURN NULL
  ENDIF
  tree_root_node = new TreeNode(root_node_value)
  parent_nodes_queue.add_to_right_end(tree_root_node) // Add root to queue to process its children

  // Process nodes level by level using the parent_nodes_queue (BFS-like construction).
  WHILE parent_nodes_queue IS NOT EMPTY:
    num_parents_this_level = parent_nodes_queue.get_length()

    FOR _ FROM 1 TO num_parents_this_level:
      current_parent_node_to_process = parent_nodes_queue.pop_from_left_end()

      // Attempt to create and link the left child.
      IF values_input_deque IS NOT EMPTY THEN
        left_child_node_value = values_input_deque.pop_from_left_end()
        IF left_child_node_value != -1 THEN // -1 signifies a null child.
          current_parent_node_to_process.left = new TreeNode(left_child_node_value)
          parent_nodes_queue.add_to_right_end(current_parent_node_to_process.left)
        ELSE
          current_parent_node_to_process.left = NULL
        ENDIF
      ELSE
        current_parent_node_to_process.left = NULL // Not enough values in input.
      ENDIF

      // Attempt to create and link the right child.
      IF values_input_deque IS NOT EMPTY THEN
        right_child_node_value = values_input_deque.pop_from_left_end()
        IF right_child_node_value != -1 THEN
          current_parent_node_to_process.right = new TreeNode(right_child_node_value)
          parent_nodes_queue.add_to_right_end(current_parent_node_to_process.right)
        ELSE
          current_parent_node_to_process.right = NULL
        ENDIF
      ELSE
        current_parent_node_to_process.right = NULL // Not enough values in input.
      ENDIF
    ENDFOR
  ENDWHILE

  RETURN tree_root_node
ENDFUNCTION
```

### Pseudocode for Path Sum.py
```pseudocode
// Assuming TreeNode class with 'val', 'left', 'right' attributes.

CLASS Solution_TreePathSum:

  // Main public function that initiates the path sum check.
  // Returns 1 if a root-to-leaf path with the target sum exists, 0 otherwise.
  FUNCTION hasPathSum(A_root_of_tree, B_target_path_sum):
    // Call the recursive helper function to perform the DFS search.
    path_found_boolean = this.dfs_path_sum_exists(A_root_of_tree, B_target_path_sum)

    // Convert the boolean result to an integer (1 for true, 0 for false).
    IF path_found_boolean IS TRUE THEN
      RETURN 1
    ELSE
      RETURN 0
    ENDIF
  ENDFUNCTION

  // Recursive Depth-First Search (DFS) helper function.
  // Checks if there is a path from `current_node` to a leaf such that the sum of values
  // along this path equals `sum_to_achieve_from_this_node`.
  DEFINE FUNCTION dfs_path_sum_exists(current_node, sum_to_achieve_from_this_node):
    // Base Case 1: If current_node is null, this path segment is invalid.
    IF current_node IS NULL THEN
      RETURN false
    ENDIF

    // Calculate the sum remaining to be achieved from children of current_node.
    remaining_sum_for_children = sum_to_achieve_from_this_node - current_node.val

    // Base Case 2: If current_node is a leaf node (no left or right children).
    IF current_node.left IS NULL AND current_node.right IS NULL THEN
      // Check if the path sum up to this leaf equals the original target sum.
      // This means the `remaining_sum_for_children` (after subtracting current_node.val) must be 0.
      IF remaining_sum_for_children == 0 THEN
        RETURN true // Valid root-to-leaf path with target sum found.
      ELSE
        RETURN false // Path ends at leaf, but sum does not match target.
      ENDIF
    ENDIF

    // Recursive Step: Explore left and right subtrees if they exist.
    // A path is considered found if it exists in either the left OR the right subtree.

    // Check left subtree
    found_in_left = this.dfs_path_sum_exists(current_node.left, remaining_sum_for_children)
    IF found_in_left IS TRUE THEN
      RETURN true // Path found in left subtree
    ENDIF

    // Check right subtree (only if not found in left)
    found_in_right = this.dfs_path_sum_exists(current_node.right, remaining_sum_for_children)
    IF found_in_right IS TRUE THEN
      RETURN true // Path found in right subtree
    ENDIF

    // If path not found in either left or right subtree from this node.
    RETURN false
    // Python code `return l or r` is more concise:
    // RETURN (this.dfs_path_sum_exists(current_node.left, remaining_sum_for_children) OR
    //         this.dfs_path_sum_exists(current_node.right, remaining_sum_for_children))
    // This relies on dfs_path_sum_exists(NULL, ...) returning false.
  ENDFUNCTION
ENDCLASS
```

### Pseudocode for Odd and Even Levels.py
```pseudocode
// Assuming TreeNode class with 'val', 'left', 'right' attributes.
// Assumes a Deque data structure for Level-Order Traversal (BFS).
// Operations: ADD_TO_END (enqueue), POP_FROM_FRONT (dequeue), IS_EMPTY, GET_SIZE.

FUNCTION calculate_sum_difference_odd_even_levels(A_tree_root):
  // Initialize the result: (Sum of odd level values) - (Sum of even level values)
  net_sum_difference = 0

  // If the tree is empty, the difference is 0.
  IF A_tree_root IS NULL THEN
    RETURN net_sum_difference
  ENDIF

  // Queue for BFS to process nodes level by level.
  processing_queue = new Deque()
  ADD A_tree_root TO END of processing_queue // Start with the root node.

  // `is_odd_level_flag` tracks if the current level is odd (true) or even (false).
  // Level 1 (root) is considered odd.
  is_odd_level_flag = true

  // Process the tree level by level.
  WHILE processing_queue IS NOT EMPTY:
    // Get the number of nodes at the current level.
    nodes_count_at_current_level = GET_SIZE(processing_queue)

    // Iterate through all nodes at the current level.
    FOR _ FROM 1 TO nodes_count_at_current_level:
      current_node = processing_queue.pop_from_front() // Dequeue a node

      // Add its value to `net_sum_difference` if it's an odd level,
      // subtract if it's an even level.
      IF is_odd_level_flag IS TRUE THEN
        net_sum_difference = net_sum_difference + current_node.val
      ELSE // is_odd_level_flag IS FALSE (even level)
        net_sum_difference = net_sum_difference - current_node.val
      ENDIF

      // Enqueue children of the current_node for processing in the next level.
      IF current_node.left IS NOT NULL THEN
        processing_queue.add_to_end(current_node.left)
      ENDIF
      IF current_node.right IS NOT NULL THEN
        processing_queue.add_to_end(current_node.right)
      ENDIF
    ENDFOR // Finished processing all nodes at the current level.

    // Toggle the level flag for the next level.
    is_odd_level_flag = NOT is_odd_level_flag
  ENDWHILE // End of level-order traversal.

  RETURN net_sum_difference
ENDFUNCTION
```

### Pseudocode for Next Pointer Binary Tree.py
```pseudocode
// Assuming TreeLinkNode class with 'val', 'left', 'right', and 'next' attributes.
// The 'next' pointer is initially null for all nodes.

CLASS Solution_PopulateNextRightPointers:

  // Main function to populate the 'next' pointers for all nodes.
  // Modifies the tree in-place.
  FUNCTION connect(root_param):
    IF root_param IS NULL THEN
      RETURN // Nothing to do for an empty tree
    ENDIF

    // `level_head` points to the first node of the current level being processed.
    level_head = root_param

    // Outer loop iterates through levels of the tree.
    WHILE level_head IS NOT NULL:
      // `current_parent_node` iterates through nodes of the `level_head`'s level
      // using the `next` pointers that were set in the previous pass (for the level above).
      current_parent_node = level_head

      // Inner loop iterates across the current level being processed.
      WHILE current_parent_node IS NOT NULL:
        // Task 1: Connect left child's `next` pointer.
        IF current_parent_node.left IS NOT NULL THEN
          IF current_parent_node.right IS NOT NULL THEN
            // If a right sibling exists, left child's `next` points to it.
            current_parent_node.left.next = current_parent_node.right
          ELSE
            // No direct right sibling. Find the next available node for `left.next`
            // by looking at children of subsequent nodes in the parent level.
            current_parent_node.left.next = this.get_next_available_child_node(current_parent_node.next)
          ENDIF
        ENDIF

        // Task 2: Connect right child's `next` pointer.
        IF current_parent_node.right IS NOT NULL THEN
          // Right child's `next` points to the first child of the next node in the parent level.
          current_parent_node.right.next = this.get_next_available_child_node(current_parent_node.next)
        ENDIF

        // Move to the next node in the current parent level.
        current_parent_node = current_parent_node.next
      ENDWHILE // End of processing nodes in the current parent level

      // Move to the start of the next level for processing.
      // This is the first child found in the `level_head`'s level.
      // Python code logic: if level.left, else if level.right, else get_next_child(level.next)
      IF level_head.left IS NOT NULL THEN
        level_head = level_head.left
      ELSE IF level_head.right IS NOT NULL THEN
        level_head = level_head.right
      ELSE
        // If current level_head has no children, find the next node in its level
        // that does have children, and use its first child as the start of the next level.
        level_head = this.get_next_available_child_node(level_head.next)
      ENDIF
    ENDWHILE // End of processing all levels

    // The function modifies the tree in-place. No explicit return value in Python.
  ENDFUNCTION


  // Helper function: Given `node_in_parent_level`, find the first child (left preferred, then right)
  // by traversing the `next` pointers starting from `node_in_parent_level`.
  // This is used to find the `next` for a child whose parent doesn't have a closer sibling child.
  DEFINE FUNCTION get_next_available_child_node(node_in_parent_level):
    current_node_scanner = node_in_parent_level
    WHILE current_node_scanner IS NOT NULL:
      // Check for left child first
      IF current_node_scanner.left IS NOT NULL THEN
        RETURN current_node_scanner.left
      ENDIF
      // Then check for right child
      IF current_node_scanner.right IS NOT NULL THEN
        RETURN current_node_scanner.right
      ENDIF
      // Move to the next node in the same parent level
      current_node_scanner = current_node_scanner.next
    ENDWHILE
    // If no child is found in any subsequent nodes on this level.
    RETURN NULL
  ENDFUNCTION
ENDCLASS
```
