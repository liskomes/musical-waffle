| Page / Feature	| Guest |	Reserver | Administrator |
| :---         |     :---:      |     :---:      |     :---:      |
| / (index)	| | | |		
| └─ View resource form	| ❌	| ✅	| ✅ note added |
| └─ Create a new resource	| ❌	| ✅	| ✅ |
| /reservation	| | | |		
| └─ Change a reserver from own booked resources	| ❌	| ⚠️(Note 1*)	| ✅ |
| └─ Change a reserver from all booked resources	| ❌	| ❌	| ✅ |
| └─ Change a reserved resource from own booked resources	| ❌	| ✅	| ✅ |
| └─ Change a reserved resource from all booked resources	| ❌	| ❌	| ✅ |
| └─ Change a reservation time from own booked resources	| ❌	| ✅(Note 2*)	| ✅(Note 2*) |
| └─ Change a reservation time from all booked resources	| ❌	| ❌(Note 2*)	| ✅(Note 2*) |
| └─ Create a new reservation	| ❌ | ✅ | ✅ |

Symbols used:
✅ Pass
❌ Fail
⚠️ Attention

Notes:
1. It's not good that reserver user can change the reserver to whoever he/she wants
2. Changing the reservation end time to smaller than start time throws an error
