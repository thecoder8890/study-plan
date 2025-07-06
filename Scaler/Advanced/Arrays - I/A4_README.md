# Pseudocode for A4.py

```pseudocode
CLASS Solution:
  METHOD solve(self, A_num_beggars, B_donations_list):
    // `A_num_beggars` is the total number of beggars (N).
    // `B_donations_list` is a list of donations, where each donation is [start_index, end_index, amount].
    // Indices are 1-based.

    // Initialize `final_amounts_array` of size A_num_beggars with all zeros.
    // This array will be used as a "difference array".
    // `final_amounts_array[i]` will store the net change in donations starting at beggar `i`.
    final_amounts_array = array of size A_num_beggars, initialized with all 0s

    // Process each donation to populate the difference array.
    FOR EACH donation_info IN B_donations_list:
      // Extract donation details. `beg` and `end` are 1-based.
      start_beggar_1_based = donation_info[0]
      end_beggar_1_based = donation_info[1]
      amount_donated = donation_info[2]

      // Convert 1-based `start_beggar_1_based` to 0-based index for the array.
      start_idx_0_based = start_beggar_1_based - 1

      // Add `amount_donated` at `start_idx_0_based`.
      // This signifies that from this beggar onwards, everyone's collection increases by `amount_donated`.
      // (This effect will be counteracted later if the range ends before the last beggar).
      IF start_idx_0_based >= 0 AND start_idx_0_based < A_num_beggars THEN // Check array bounds
          final_amounts_array[start_idx_0_based] = final_amounts_array[start_idx_0_based] + amount_donated
      ENDIF

      // To limit the donation range to `end_beggar_1_based`, we need to subtract `amount_donated`
      // from the beggar immediately following `end_beggar_1_based`.
      // The 0-based index for this "next beggar" is `end_beggar_1_based`.
      // (Since `end_beggar_1_based` is the 1-based end of range, beggar `end_beggar_1_based` is at 0-index `end_beggar_1_based - 1`.
      // The next beggar is at 0-index `end_beggar_1_based`).
      index_after_range_0_based = end_beggar_1_based

      // If this "next beggar" index is within the bounds of the array, apply the subtraction.
      // The Python condition `if end-1 != A-1:` means if `end_beggar_1_based - 1` (0-based end) is not the last beggar index,
      // which is equivalent to `if end_beggar_1_based < A_num_beggars:`.
      IF index_after_range_0_based < A_num_beggars THEN
        final_amounts_array[index_after_range_0_based] = final_amounts_array[index_after_range_0_based] - amount_donated
      ENDIF
    ENDFOR

    // Convert the difference array into the actual final amounts by calculating prefix sums.
    // `final_amounts_array[i]` will now become the sum of all changes up to beggar `i`.
    FOR i FROM 0 TO A_num_beggars - 1:
      IF i == 0 THEN
        // The first beggar's amount is simply the net change stored at index 0. No modification needed.
        // (Python `pass` statement does this)
      ELSE
        // For subsequent beggars, their actual amount is the net change at their position
        // plus the accumulated actual amount of the previous beggar.
        final_amounts_array[i] = final_amounts_array[i] + final_amounts_array[i-1]
      ENDIF
    ENDFOR

    RETURN final_amounts_array
  ENDMETHOD
ENDCLASS
```
