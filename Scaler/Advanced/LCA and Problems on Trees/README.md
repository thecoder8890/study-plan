# Advanced/LCA and Problems on Trees

## Files in this topic:

- [Boundary Traversal Of Binary Tree.py](Boundary Traversal Of Binary Tree.py)
- [Diameter of binary tree.py](Diameter of binary tree.py)
- [Problems_on_Binary_trees.pdf](Problems_on_Binary_trees.pdf)

## Description

This section focuses on algorithms related to finding the Lowest Common Ancestor (LCA) of nodes in trees, along with various other advanced problems and traversals specific to binary trees and general tree structures.

### Pseudocode for Boundary Traversal Of Binary Tree.py
```pseudocode
// Assuming a TreeNode class with 'val', 'left', and 'right' attributes.

CLASS Solution_For_BoundaryTraversal:
  // Instance variable to store the result of the traversal.
  // Needs to be initialized (e.g., in constructor or at the start of `solve`).
  boundary_nodes_accumulator_list: List of Integers

  CONSTRUCTOR():
    this.boundary_nodes_accumulator_list = new empty List
  ENDCONSTRUCTOR

  // Helper: Get left boundary (top-down, excluding leaves)
  FUNCTION get_left_boundary(node):
    IF node IS NULL OR (node.left IS NULL AND node.right IS NULL) THEN // Is null or is a leaf
      RETURN
    ENDIF
    ADD node.val TO boundary_nodes_accumulator_list
    IF node.left IS NOT NULL THEN
      get_left_boundary(node.left) // Prioritize left child
    ELSE IF node.right IS NOT NULL THEN
      get_left_boundary(node.right) // Go right only if no left child
    ENDIF
  ENDFUNCTION

  // Helper: Get right boundary (bottom-up, excluding leaves)
  FUNCTION get_right_boundary(node):
    IF node IS NULL OR (node.left IS NULL AND node.right IS NULL) THEN // Is null or is a leaf
      RETURN
    ENDIF
    IF node.right IS NOT NULL THEN
      get_right_boundary(node.right) // Prioritize right child
    ELSE IF node.left IS NOT NULL THEN
      get_right_boundary(node.left) // Go left only if no right child
    ENDIF
    ADD node.val TO boundary_nodes_accumulator_list // Add after recursive calls for bottom-up
  ENDFUNCTION

  // Helper: Get all leaf nodes (left-to-right)
  FUNCTION get_all_leaves(node):
    IF node IS NULL THEN
      RETURN
    ENDIF
    IF node.left IS NULL AND node.right IS NULL THEN // Is a leaf node
      ADD node.val TO boundary_nodes_accumulator_list
      RETURN // No further traversal from a leaf for this purpose
    ENDIF
    get_all_leaves(node.left)  // Traverse left subtree
    get_all_leaves(node.right) // Traverse right subtree
  ENDFUNCTION

  // Main function for boundary traversal
  FUNCTION solve_boundary_traversal(root_node_A):
    // Reset accumulator for multiple calls if Solution object is reused.
    boundary_nodes_accumulator_list = new empty List

    IF root_node_A IS NULL THEN
      RETURN boundary_nodes_accumulator_list // Empty boundary for empty tree
    ENDIF

    // 1. Add the root node's value (if it's not a leaf, it's part of boundary).
    // The problem usually includes the root unless it's the only node (and thus a leaf).
    // Python code adds root value unconditionally first.
    ADD root_node_A.val TO boundary_nodes_accumulator_list

    // 2. Traverse and add left boundary nodes (excluding the root if already added, and excluding leaves).
    get_left_boundary(root_node_A.left)

    // 3. Traverse and add all leaf nodes from left to right.
    // These calls should ensure leaves of subtrees are added.
    // If root_node_A itself is a leaf, it's added in step 1. The calls to get_left_boundary(A.left) etc.
    // will not process A.left if A is a leaf. The leaves calls should not re-add A.left if it's a leaf.
    // The Python code calls leaves(A.left) and leaves(A.right).
    // If A.left is a leaf, left_boundary(A.left) returns, then leaves(A.left) adds it. Correct.
    get_all_leaves(root_node_A.left)
    get_all_leaves(root_node_A.right)

    // 4. Traverse and add right boundary nodes (excluding root, excluding leaves, in bottom-up order).
    get_right_boundary(root_node_A.right)

    RETURN boundary_nodes_accumulator_list
  ENDFUNCTION
ENDCLASS
```

### Pseudocode for Diameter of binary tree.py
```pseudocode
// Assuming TreeNode class with 'val', 'left', 'right' attributes.

CLASS Solution_TreeDiameter:
  // Main function to get the diameter.
  FUNCTION solve_get_diameter(A_root_node):
    // Call the helper function which returns (diameter_in_edges, height_in_edges).
    diameter_edges, height_edges = calculate_diameter_and_height_edges(A_root_node)

    // The helper calculates diameter in terms of edges.
    // Conventionally, diameter of an empty tree or a single-node tree (leaf) is 0 edges.
    // Python code returns -1 from helper for null, leading to diameter 0 for a leaf.
    IF A_root_node IS NULL THEN RETURN 0 ENDIF
    IF A_root_node.left IS NULL AND A_root_node.right IS NULL THEN RETURN 0 ENDIF

    RETURN diameter_edges
  ENDFUNCTION

  // Recursive helper function: calculates diameter and height of a subtree.
  // Both diameter and height are returned in terms of *number of edges*.
  // Height of a leaf node is 0. Height of a null node is -1.
  // Diameter of a leaf node is 0. Diameter of a null node is -1 (or 0 by convention for empty tree).
  DEFINE FUNCTION calculate_diameter_and_height_edges(current_node):
    // Base Case: If the current node is null (empty subtree).
    IF current_node IS NULL THEN
      RETURN (-1, -1) // (diameter_edges = -1, height_edges = -1)
    ENDIF

    // Recursively get diameter and height for left and right subtrees.
    left_subtree_diameter_edges, left_subtree_height_edges = this.calculate_diameter_and_height_edges(current_node.left)
    right_subtree_diameter_edges, right_subtree_height_edges = this.calculate_diameter_and_height_edges(current_node.right)

    // Height of the current subtree (in edges).
    current_height_edges = maximum(left_subtree_height_edges, right_subtree_height_edges) + 1

    // Diameter of the current subtree (in edges).
    // Path through current_node: (height of left subtree + 1 edge) + (height of right subtree + 1 edge)
    //                            = left_subtree_height_edges + right_subtree_height_edges + 2
    path_through_current_node_diameter = left_subtree_height_edges + right_subtree_height_edges + 2

    current_diameter_edges = maximum(left_subtree_diameter_edges,
                                     right_subtree_diameter_edges,
                                     path_through_current_node_diameter)

    RETURN (current_diameter_edges, current_height_edges)
  ENDFUNCTION
ENDCLASS
```
