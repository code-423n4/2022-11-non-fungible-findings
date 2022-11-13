**Exchange**
- L240/291/573 - When we use a require and throw an exception it is important to show a understandable message, this is important because it makes the user better understand the reason why it is reverted.

- L128/129/130/323/332/341 - In the setting functions it is validated that the parameters are not set to zero, but the variables can take a value of 0 in the constructor, therefore at the time of deployment it could be in an incorrect state. It should be validated in the constructor that the variables are not set to zero.

- L174-182/487-489/497-502 - There is commented code that should be removed to avoid confusion.

- L387/393/401/412 - The name order.order is terribly confusing, since two things that represent the same thing cannot have the same name. a possible name, it could be that the input of type Input is called input.

- L4 - The Initializable abstract contract is imported, but it is never used. Therefore, it is best to remove it.


**Pool**
- L45/48/71/72 - When we use a require and throw an exception it is important to show a understandable message, this is important because it makes the user better understand the reason why it is reverted.


