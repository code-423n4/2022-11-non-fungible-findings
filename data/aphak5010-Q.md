# Non Fungible Trading Low Risk and Non-Critical Issues
## Summary
| Risk      | Title | File | Instances
| ----------- | ----------- | ----------- | ----------- |
| N-00 | Remove TODO comments | Pool.sol | 1 |
| N-01 | Event is missing indexed fields | Pool.sol, Exchange.sol | 5 |
| N-02 | Line too long | Exchange.sol | 1 |
| N-03 | Remove commented out code | Exchange.sol | 1 |
| N-04 | Make `public` functions that are not called internally `external` | Pool.sol | 2 |

## [N-00] Remove TODO comments
TODO comments should be removed from production code.  

There is 1 instance of this.  
[https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Pool.sol#L18](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Pool.sol#L18)  

## [N-01] Event is missing indexed fields
Each event should use three indexed fields if it has three or more fields.  

There are 5 instances of events that do not have 3 indexed fields.  

[https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Pool.sol#L23](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Pool.sol#L23)  

[https://github.com/code-423n4/2022-11-non-fungible/blob/e9451f78069999cfd8fdd126b87241eb94e93078/contracts/Exchange.sol#L90](https://github.com/code-423n4/2022-11-non-fungible/blob/e9451f78069999cfd8fdd126b87241eb94e93078/contracts/Exchange.sol#L90)  

[https://github.com/code-423n4/2022-11-non-fungible/blob/e9451f78069999cfd8fdd126b87241eb94e93078/contracts/Exchange.sol#L99](https://github.com/code-423n4/2022-11-non-fungible/blob/e9451f78069999cfd8fdd126b87241eb94e93078/contracts/Exchange.sol#L99)  

[https://github.com/code-423n4/2022-11-non-fungible/blob/e9451f78069999cfd8fdd126b87241eb94e93078/contracts/Exchange.sol#L100](https://github.com/code-423n4/2022-11-non-fungible/blob/e9451f78069999cfd8fdd126b87241eb94e93078/contracts/Exchange.sol#L100)  

[https://github.com/code-423n4/2022-11-non-fungible/blob/e9451f78069999cfd8fdd126b87241eb94e93078/contracts/Exchange.sol#L105](https://github.com/code-423n4/2022-11-non-fungible/blob/e9451f78069999cfd8fdd126b87241eb94e93078/contracts/Exchange.sol#L105)  

## [N-02] Line too long
The maximum line length should be 164 characters.  
That's because GiHub will not display lines longer than that on the screen without scrolling.  

There is 1 instance of this.  

[https://github.com/code-423n4/2022-11-non-fungible/blob/e9451f78069999cfd8fdd126b87241eb94e93078/contracts/Exchange.sol#L230](https://github.com/code-423n4/2022-11-non-fungible/blob/e9451f78069999cfd8fdd126b87241eb94e93078/contracts/Exchange.sol#L230)  

## [N-03] Remove commented out code
Any code that is not needed should be removed from the production codebase.  

There is 1 instance of this.  
[https://github.com/code-423n4/2022-11-non-fungible/blob/e9451f78069999cfd8fdd126b87241eb94e93078/contracts/Exchange.sol#L282](https://github.com/code-423n4/2022-11-non-fungible/blob/e9451f78069999cfd8fdd126b87241eb94e93078/contracts/Exchange.sol#L282)  

## [N-04] Make `public` functions that are not called internally `external`
It is best practice to set the function visibility for functions that are not called by a contract internally to `external`.  

There are 2 instances of this.  
[https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Pool.sol#L44](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Pool.sol#L44)  

[https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Pool.sol#L58](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Pool.sol#L58)  

