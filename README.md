# Contract-Review
## This is a review of the Event management contract from Linium Labs.
#### Source code: https://github.com/LinumLabs/ProteaV2Contracts/blob/master/contracts/EventManager.sol


##### This contract is an implementation of event management, which allows users to set the parameters needed for their events, and get rewarded in the process by earning bonuses.


## Imported contracts
The contract has the following dependencies
 
- Safemath 
- Reward Issuer Interface
- ERC223 Receiver
Erc 223: allows for safe transfer of tokens to wallets and cotracts in order to ensure that the address the address being transfered to can receive tokens. This is done to improve the ERC20 Transfer function  which only allows tranfer of tokens to EOA.


It has a struct that stores the details of the event 

```
struct EventData{
        string name;
        string date;
        string location;
        uint24 participantLimit;
        uint8 state;        
        uint24 registered;
        uint24 attended;
        address organiser;
        // 0: Not created
        // 1: Created
        // 2: Concluded
        // 3: Cancelled
        uint256 requiredStake;
        uint256 totalStaked;
        uint256 payout;
    }
    
 ```


### Number1

```
 function initContract(address _tokenManager, address _rewardManager, uint256 _creationCost, uint256 _maxAttendanceBonus) public onlyAdmin {
        tokenManager = _tokenManager;
        rewardManager = _rewardManager;
        creationCost = _creationCost;
        maxAttendanceBonus = _maxAttendanceBonus;
    }
 ```

Function InitContract() can only be used by the admin, this is used to set the token manager, Rewards manager, Creation cost and the maximum bonus that can be achieved by attending.


### Number2
![updateStake](https://user-images.githubusercontent.com/74251327/162555790-7c235ff7-816f-47ca-8415-e43fb8bd1a1f.png)




Function UpdateStake() This is used to update the creation cost and maximum Bonus attainable. 

### Number3
Function createEvent() This is a private function  used to pass in the data reqired to set up the event based on the parameters set in the struct, and it ensures that the value of the creation cost and organiser are set.
 ### Number4 
Function getEvent() This is a public view function  used to get the event deatails by passing in the index number and it returns all the values associated with the event at creation.

 ### Number5
Function getEventStake() This returns the stake amount required to create an event.

 ### Number6
Function createEventDebug() This is an external function that can only be called by the token manager and it maps the index to the data required for an event creation.

### Number7
Function increaseParticipantLimit() This is a public function that can only be called by the manager, this is used to increase the participant limit by passing in the index of the event.

### Number8
Function endEvent() This is a public function that can only be called by the manager, this is used to used to set the event status to inactive. and the internal Calcpayout() function is called, safemath is applied here to prevent overflow and underflow error.

### Number9
Function cancelEvent() This is a public function that can only be called by the manager, this is used to used to set the staus of an already created event to inactive.
### Number9
Function forwardStake() This is internal function is used to to transfer token value from member to a memeber address.
### Number10
Function rsvp() This is a private function that checks if the funds needed has been transfered to reward manager. if this condition is met, it set sets the event status to created(1). and add the new address to the members list.

### Number 11
Function rsvpDebug() This is an external function that can only be called by the token manager, adds the new input to the registered participants storage.




