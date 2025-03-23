| Page / Feature	| Guest |	Reserver | Administrator |
| :---         |     :---:      |     :---:      |     :---:      |
| /	| | | |		
| └─ View booked resources	| ❌	| ✅	| ✅ |
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

WFUZZ:

Sites found:
=====================================================================
ID           Response   Lines    Word       Chars       Payload                                                              
=====================================================================

000000001:   200        375 L    1166 W     17989 Ch    "http://localhost:8000/"                                                              
000002362:   302        0 L      0 W        0 Ch        "logout"                                                                              
000002347:   200        32 L     137 W      1967 Ch     "login"                                                                               
000003341:   200        44 L     202 W      2978 Ch     "register"                                                                            
000003404:   200        38 L     157 W      2235 Ch     "resources"                                                                           
000003395:   401        0 L      1 W        12 Ch       "reservation"   

Api foulders found:
=====================================================================
ID           Response   Lines    Word       Chars       Payload                                                              
=====================================================================

000003404:   200        0 L      1031 W     49961 Ch    "resources"                                                                           
000003601:   401        0 L      1 W        12 Ch       "session"                                                                             
000004245:   200        0 L      1 W        612 Ch      "users"  
