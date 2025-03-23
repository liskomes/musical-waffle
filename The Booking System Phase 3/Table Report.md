| Page / Feature	| Guest |	Reserver | Administrator |
| :---         |     :---:      |     :---:      |     :---:      |
| / (index)	| | | |		
| └─ View resource form	| ❌	| ✅	| ✅ |
| └─ Can see "Add a new resource" button | ❌	| ✅	| ✅ |
| └─ Can see "Add a new reservation" button | ❌	| ✅	| ✅ |
| /resources	| | | |	
| └─ Create a new resource	| ⚠️(Note 3*)	| ✅	| ✅ |
| /reservation	| | ⚠️(Note 4*) | |		
| └─ Change a reserver from own booked resources	| ❌	| ⚠️(Note 1*)	| ✅ |
| └─ Change a reserver from all booked resources	| ❌	| ❌	| ✅ |
| └─ Change a reserved resource from own booked resources	| ❌	| ✅	| ✅ |
| └─ Change a reserved resource from all booked resources	| ❌	| ❌	| ✅ |
| └─ Change a reservation time from own booked resources	| ❌	| ✅(Note 2*)	| ✅(Note 2*) |
| └─ Change a reservation time from all booked resources	| ❌	| ❌(Note 2*)	| ✅(Note 2*) |
| └─ Create a new reservation	| ❌ | ✅ | ✅ |
| /admin ⚠️(Note 5*)| | | |
| /profile ⚠️(Note 5*)| | | |		

Symbols used:
✅ Pass
❌ Fail
⚠️ Attention

Notes:
1. It's not good that a reserver user can change the reserver to whoever he/she wants
2. Changing the reservation end time to smaller than start time throws an error
3. Quest can add new resources if he/she goes directly to resources page
4. Reserver can see all reservation forms even he/she is not the reserver when he/she goes directly to the page (e.g. http://localhost:8000/reservation?id=3). 
5. Not implemented
