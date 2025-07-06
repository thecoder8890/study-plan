# Advanced/Greedy Algorithms

## Files in this topic:

- [Another Coin Problem.py](Another Coin Problem.py)
- [Assign Mice to Holes.py](Assign Mice to Holes.py)
- [Distribute Candy.py](Distribute Candy.py)
- [Finish Maximum Jobs.py](Finish Maximum Jobs.py)
- [Free Cars.py](Free Cars.py)
- [Greedy.pdf](Greedy.pdf)
- [The ship company.py](The ship company.py)

## Description

This section focuses on Greedy Algorithms, an algorithmic paradigm that makes the locally optimal choice at each stage with the hope of finding a global optimum. It covers various problems where greedy approaches yield correct and efficient solutions.

### Pseudocode for Another Coin Problem.py
```pseudocode
// Assumes availability of mathematical functions: FLOOR, LOGARITHM_BASE_5, POWER.

FUNCTION solve_min_coins_with_powers_of_5(A_target_sum):
  coins_count = 0
  current_remaining_sum = A_target_sum

  // Greedily pick the largest power-of-5 coin that is less than or equal to
  // the current_remaining_sum until the sum becomes 0.
  WHILE current_remaining_sum > 0:
    // Determine the largest integer 'exponent' such that 5^exponent <= current_remaining_sum.
    // This is equivalent to exponent = FLOOR(LOGARITHM_BASE_5(current_remaining_sum)).
    // For example, if current_remaining_sum is 47, log5(47) is ~2.39, so exponent is 2. Coin value is 5^2=25.
    // If current_remaining_sum is 4, log5(4) is ~0.86, so exponent is 0. Coin value is 5^0=1.
    IF current_remaining_sum == 0 THEN BREAK WHILE ENDIF // Safety, though covered by WHILE

    largest_exponent = FLOOR(LOGARITHM_BASE_5(current_remaining_sum))

    // Calculate the value of the coin to be used (5 ^ largest_exponent)
    coin_value_to_use = POWER(5, largest_exponent)

    // Subtract this coin's value from the remaining sum
    current_remaining_sum = current_remaining_sum - coin_value_to_use

    // Increment the count of coins used
    coins_count = coins_count + 1
  ENDWHILE

  RETURN coins_count
ENDFUNCTION
```

### Pseudocode for Assign Mice to Holes.py
```pseudocode
FUNCTION assign_mice_to_holes_minimize_max_time(A_mouse_positions_list, B_hole_positions_list):
  // Step 1: Sort the positions of the mice.
  SORT A_mouse_positions_list ASCENDING

  // Step 2: Sort the positions of the holes.
  SORT B_hole_positions_list ASCENDING

  // Number of mice (should be equal to number of holes as per typical problem statement).
  num_pairs = length of A_mouse_positions_list

  // Initialize a variable to store the maximum time taken by any mouse to reach its assigned hole.
  // This is what we want to minimize overall, and the greedy approach finds this minimized maximum.
  overall_max_time = 0

  // Step 3: Iterate through the sorted lists and pair the i-th mouse with the i-th hole.
  // Calculate the time taken for each such pair and find the maximum among these times.
  FOR i FROM 0 TO num_pairs - 1:
    current_mouse_position = A_mouse_positions_list[i]
    current_hole_position = B_hole_positions_list[i]

    // Time taken for this mouse to reach this hole is the absolute difference of their positions.
    time_for_this_assignment = ABSOLUTE_VALUE(current_hole_position - current_mouse_position)

    // Update overall_max_time if the time for this assignment is greater.
    IF time_for_this_assignment > overall_max_time THEN
      overall_max_time = time_for_this_assignment
    ENDIF
  ENDFOR

  // The `overall_max_time` found by this greedy pairing strategy is the minimized maximum time.
  RETURN overall_max_time
ENDFUNCTION
```

### Pseudocode for The ship company.py
```pseudocode
// Requires MinHeap and MaxHeap data structures (or MinHeap simulating MaxHeap).
// Operations: ADD_TO_HEAP, REMOVE_MIN/MAX_FROM_HEAP, PEEK_MIN/MAX.

FUNCTION solve_ticket_costs(A_num_tickets_to_buy, B_num_counters, C_initial_ticket_counts_list):
  // min_cost_heap stores current ticket counts (prices) for finding minimum price.
  min_cost_heap = new MinHeap()
  // max_cost_heap stores current ticket counts (prices) for finding maximum price.
  // Python uses a min-heap with negative values to simulate a max-heap.
  max_cost_heap = new MaxHeap()

  // Populate both heaps with the initial ticket counts from each counter.
  FOR EACH initial_count IN C_initial_ticket_counts_list:
    IF initial_count > 0 THEN // Only consider counters with available tickets
      ADD initial_count TO min_cost_heap
      ADD initial_count TO max_cost_heap
      // If simulating max-heap with min-heap: ADD (-initial_count) TO simulated_max_heap
    ENDIF
  ENDFOR

  accumulated_min_cost = 0
  accumulated_max_cost = 0

  tickets_processed_count = 0

  // Loop A_num_tickets_to_buy times, as we need to buy this many tickets.
  WHILE tickets_processed_count < A_num_tickets_to_buy:
    // --- Calculate Minimum Cost component for buying one ticket ---
    // Greedily pick the ticket from the counter currently offering the lowest price (fewest tickets).
    IF min_cost_heap IS NOT EMPTY THEN
      current_min_price = PEEK_MIN(min_cost_heap)
      REMOVE_MIN from min_cost_heap

      accumulated_min_cost = accumulated_min_cost + current_min_price

      // If that counter still has tickets left, add its new count (price) back to the heap.
      IF current_min_price - 1 > 0 THEN
        ADD (current_min_price - 1) TO min_cost_heap
      ENDIF
    ELSE
      // Should not happen if A_num_tickets_to_buy <= total initial tickets.
      // Indicates an issue or all tickets bought for min_cost path.
      BREAK WHILE // Or handle error
    ENDIF

    // --- Calculate Maximum Cost component for buying one ticket ---
    // Greedily pick the ticket from the counter currently offering the highest price (most tickets).
    IF max_cost_heap IS NOT EMPTY THEN
      current_max_price = PEEK_MAX(max_cost_heap)
      // If simulating with min-heap: current_max_price = -PEEK_MIN(simulated_max_heap)
      REMOVE_MAX from max_cost_heap
      // If simulating: REMOVE_MIN from simulated_max_heap

      accumulated_max_cost = accumulated_max_cost + current_max_price

      // If that counter still has tickets left, add its new count (price) back to the heap.
      IF current_max_price - 1 > 0 THEN
        ADD (current_max_price - 1) TO max_cost_heap
        // If simulating: ADD (-(current_max_price - 1)) TO simulated_max_heap
      ENDIF
    ELSE
      // Should not happen if A_num_tickets_to_buy <= total initial tickets.
      BREAK WHILE // Or handle error
    ENDIF

    tickets_processed_count = tickets_processed_count + 1
  ENDWHILE

  RETURN [accumulated_max_cost, accumulated_min_cost]
ENDFUNCTION
```

### Pseudocode for Free Cars.py
```pseudocode
// Requires a Min-Heap data structure.
// Operations: ADD_TO_HEAP, REMOVE_MIN_FROM_HEAP, PEEK_MIN, IS_HEAP_EMPTY.

FUNCTION solve_maximize_car_profit(A_car_deadlines, B_car_profits):
  num_cars_available = length of A_car_deadlines
  IF num_cars_available == 0 THEN
    RETURN 0
  ENDIF

  // Step 1: Create a list of car objects/tuples, each (deadline, profit).
  car_data_list = empty list
  FOR i FROM 0 TO num_cars_available - 1:
    ADD car(deadline: A_car_deadlines[i], profit: B_car_profits[i]) TO car_data_list
  ENDFOR

  // Step 2: Sort cars primarily by their deadlines in ascending order.
  // If deadlines are same, secondary sort criteria (e.g., by profit) might matter
  // but standard greedy algorithm usually just needs deadline sort.
  SORT car_data_list ASCENDING based on 'deadline'

  // `scheduled_car_profits_heap` stores profits of cars currently chosen to be sold.
  // It's a min-heap, so its root is the car with the minimum profit among those chosen.
  scheduled_car_profits_heap = new MinHeap()

  current_time = 0 // Represents number of time slots filled / cars scheduled.
  total_accumulated_profit = 0
  MODULO_CONST = 10^9 + 7

  // Step 3: Iterate through each car, sorted by deadline.
  FOR EACH current_car IN car_data_list:
    deadline_of_current_car = current_car.deadline
    profit_of_current_car = current_car.profit

    IF current_time < deadline_of_current_car THEN
      // There's an available time slot before or at the car's deadline.
      // Greedily schedule this car.
      total_accumulated_profit = total_accumulated_profit + profit_of_current_car
      ADD_TO_HEAP(scheduled_car_profits_heap, profit_of_current_car)
      current_time = current_time + 1 // One more time slot used.
    ELSE
      // current_time >= deadline_of_current_car.
      // All available time slots up to this car's deadline might be full.
      // We can only schedule this car if its profit is greater than the
      // minimum profit of a car already scheduled within these `current_time` slots.
      IF IS_HEAP_EMPTY(scheduled_car_profits_heap) IS FALSE AND profit_of_current_car > PEEK_MIN(scheduled_car_profits_heap) THEN
        // This current car is more profitable. Swap it with the least profitable car in the schedule.
        profit_of_least_profitable_scheduled_car = PEEK_MIN(scheduled_car_profits_heap)
        REMOVE_MIN_FROM_HEAP(scheduled_car_profits_heap)

        // Update total profit: subtract old, add new.
        total_accumulated_profit = total_accumulated_profit - profit_of_least_profitable_scheduled_car
        total_accumulated_profit = total_accumulated_profit + profit_of_current_car

        // Add the current car's profit to the heap.
        ADD_TO_HEAP(scheduled_car_profits_heap, profit_of_current_car)
        // `current_time` (number of scheduled cars) does not change as it's a swap.
      ENDIF
    ENDIF
  ENDFOR

  RETURN total_accumulated_profit % MODULO_CONST
ENDFUNCTION
```

### Pseudocode for Finish Maximum Jobs.py
```pseudocode
// Assumes A_start_times and B_finish_times are lists of the same length,
// where A_start_times[i] and B_finish_times[i] correspond to the i-th job.

FUNCTION solve_max_non_overlapping_jobs(A_start_times, B_finish_times):
  num_total_jobs = length of A_start_times

  // If there are no jobs, no jobs can be completed.
  IF num_total_jobs == 0 THEN
    RETURN 0
  ENDIF

  // Step 1: Create a list of job objects or tuples, each containing (start_time, finish_time).
  // This helps in sorting based on finish times while keeping start times associated.
  list_of_jobs = empty list
  FOR i FROM 0 TO num_total_jobs - 1:
    ADD job_details(start: A_start_times[i], finish: B_finish_times[i]) TO list_of_jobs
  ENDFOR

  // Step 2: Sort the jobs based on their finish times in non-decreasing order.
  // This is the crucial greedy choice: always consider the job that finishes earliest.
  SORT list_of_jobs ASCENDING based on 'finish' time of job_details

  // Step 3: Select the first job (the one with the earliest finish time).
  count_of_selected_jobs = 1
  // Keep track of the finish time of the most recently selected job.
  finish_time_of_last_job_taken = list_of_jobs[0].finish

  // Step 4: Iterate through the rest of the sorted jobs.
  FOR i FROM 1 TO num_total_jobs - 1:
    current_job = list_of_jobs[i]

    // If the current job's start time is after or equal to the finish time
    // of the last job taken, then this current job does not overlap and can be selected.
    IF current_job.start >= finish_time_of_last_job_taken THEN
      // Select this job
      count_of_selected_jobs = count_of_selected_jobs + 1
      // Update the finish time to this current job's finish time
      finish_time_of_last_job_taken = current_job.finish
    ENDIF
  ENDFOR

  RETURN count_of_selected_jobs
ENDFUNCTION
```

### Pseudocode for Distribute Candy.py
```pseudocode
FUNCTION calculate_min_candies_for_ratings(A_children_ratings):
  num_children = length of A_children_ratings

  // Handle edge cases for empty or single child list
  IF num_children == 0 THEN
    RETURN 0
  ENDIF
  IF num_children == 1 THEN
    RETURN 1 // Single child gets one candy
  ENDIF

  // candies_needed_left_pass[i] will store candies for child i based on left-to-right scan.
  candies_needed_left_pass = new array of size num_children
  // candies_needed_right_pass[i] will store candies for child i based on right-to-left scan.
  candies_needed_right_pass = new array of size num_children

  // --- First Pass: Iterate from Left to Right ---
  // The first child always gets at least one candy.
  candies_needed_left_pass[0] = 1
  FOR i FROM 1 TO num_children - 1:
    IF A_children_ratings[i] > A_children_ratings[i-1] THEN
      // If current child has a higher rating than the child to their left,
      // they must receive more candies than their left neighbor.
      candies_needed_left_pass[i] = candies_needed_left_pass[i-1] + 1
    ELSE
      // Otherwise (rating is less than or equal to left neighbor),
      // they receive the minimum 1 candy (for now, right neighbor check comes later).
      candies_needed_left_pass[i] = 1
    ENDIF
  ENDFOR

  // --- Second Pass: Iterate from Right to Left ---
  // The last child always gets at least one candy.
  candies_needed_right_pass[num_children - 1] = 1
  FOR i FROM num_children - 2 DOWNTO 0: // Iterate from second-to-last down to first child
    IF A_children_ratings[i] > A_children_ratings[i+1] THEN
      // If current child has a higher rating than the child to their right,
      // they must receive more candies than their right neighbor.
      candies_needed_right_pass[i] = candies_needed_right_pass[i+1] + 1
    ELSE
      // Otherwise (rating is less than or equal to right neighbor),
      // they receive the minimum 1 candy (for now, left neighbor check already done).
      candies_needed_right_pass[i] = 1
    ENDIF
  ENDFOR

  // --- Combine Results ---
  // The final number of candies for each child `i` must satisfy both conditions
  // (being greater than left neighbor if rating is higher, AND greater than right neighbor if rating is higher).
  // So, child `i` gets maximum(candies_needed_left_pass[i], candies_needed_right_pass[i]).
  total_candies_distributed = 0
  FOR i FROM 0 TO num_children - 1:
    final_candies_for_child_i = maximum(candies_needed_left_pass[i], candies_needed_right_pass[i])
    total_candies_distributed = total_candies_distributed + final_candies_for_child_i
  ENDFOR

  RETURN total_candies_distributed
ENDFUNCTION
```
