**Exchange**
- L36/49/240/245/246/248/249/291/295/327/336/345/414/523/545/549/552/573/604/630/632/636 - Instead of using a require you can use ifs and an error custom, this would generate a lower cost of gas.

- L49/295/604 - REQUIRE()/REVERT() STRINGS LONGER THAN 32 BYTES COST EXTRA GAS

- L146/147/184/307/597/598 - When we initialize a variable and we want to set its default value, it is not necessary to set it, since it has that value by default.

- L184/307/598 - It is less expensive to do ++i or --i, rather than i++, i-- or i - 1.

- L240/242/243/248/249/251/258/259/260/261/265/266/267/274/275/276/278 - The object sell.order could be created in memory so as not to be constantly consulting the value inside sell. The same goes for buy.order.

- L307/598 - When the length of an array is used more than once, it is less expensive to create a variable in length memory and use that variable.

- L412 - It is less expensive to validate that uint != 0 than to validate uint > 0

- L457/458/578/581/607/608 - When a variable is only used once, it is not necessary to create a variable in storage, you can directly use the operation in the needed place.

- L664 - An else if is performed with the second value of assetType, but there are only two values, therefore only one else could be performed and less gas would be spent.



**Pool**
- L45/48/71/72 - Instead of using a require you can use ifs and an error custom, this would generate a lower cost of gas.

- L63 - REQUIRE()/REVERT() STRINGS LONGER THAN 32 BYTES COST EXTRA GAS

- L46/73/74 - As in line 45 it is validated that _balances[msg.sender] >= amount, when the subtraction is done the unchecked operation can be performed directly, since there is no way to generate an underflow. The same happens with the _transfer() function.
