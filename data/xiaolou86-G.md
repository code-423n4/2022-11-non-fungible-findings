1. Use unchecked for arithmetic in the for loop where it is unsigned and won't overflow.

File: Exchange.sol

177:         for (uint8 i=0; i < executionsLength; i++) {

184:         for (uint8 i = 0; i < executionsLength; i++) {

307:         for (uint8 i = 0; i < orders.length; i++) {

598:         for (uint8 i = 0; i < fees.length; i++) {