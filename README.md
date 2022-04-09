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
```
function updateStakes(uint256 _creationCost, uint256 _maxAttendanceBonus) public onlyAdmin{
        creationCost = _creationCost;
        maxAttendanceBonus = _maxAttendanceBonus;
    }
```




Function UpdateStake() This is used to update the creation cost and maximum Bonus attainable. 

### Number3
``` function createEvent(
        string _name, string _date, string _location, 
        uint24 _participantLimit, address _organiser, uint256 _requiredStake) 
        private {
        require(forwardStake(creationCost, _organiser), "Must forward funds to the reward manager");
        numberOfEvents += 1;
        events[numberOfEvents].name = _name;
        events[numberOfEvents].date = _date;
        events[numberOfEvents].location = _location;
        events[numberOfEvents].participantLimit = _participantLimit;
        events[numberOfEvents].organiser = _organiser;
        events[numberOfEvents].requiredStake = _requiredStake;
        emit EventCreated(numberOfEvents, _organiser);
    } 
```

Function createEvent() This is a private function  used to pass in the data reqired to set up the event based on the parameters set in the struct, and it ensures that the value of the creation cost and organiser are set.
 ### Number4 
 ```
  function getEvent(uint256 _index) public view returns(string, string, string, uint24, uint8, uint24, uint256){
        return (events[_index].name, events[_index].date, events[_index].location, events[_index].participantLimit, 
            events[_index].state, events[_index].registered, events[_index].requiredStake);
    }
```
Function getEvent() This is a public view function  used to get the event deatails by passing in the index number and it returns all the values associated with the event at creation.

 ### Number5
 ```
  function getEventStake(uint256 _index) public view returns(uint256){
        return events[_index].requiredStake;
    }
```
Function getEventStake() This returns the stake amount required to create an event.

 ### Number6
 ```
  function createEventDebug(
        string _name, string _date, string _location, 
        uint24 _participantLimit, address _organiser, uint256 _requiredStake) 
        external onlyToken {
        numberOfEvents += 1;
        events[numberOfEvents].name = _name;
        events[numberOfEvents].date = _date;
        events[numberOfEvents].location = _location;
        events[numberOfEvents].participantLimit = _participantLimit;
        events[numberOfEvents].organiser = _organiser;
        events[numberOfEvents].requiredStake = _requiredStake;
        emit EventCreated(numberOfEvents, _organiser);
    }
```
Function createEventDebug() This is an external function that can only be called by the token manager and it maps the index to the data required for an event creation.

### Number7
```
 function increaseParticipantLimit(uint256 _index, uint24 _limit) public onlyManager(_index){
        require(events[_index].participantLimit < _limit, "Limit can only be increased");
        events[_index].participantLimit = _limit;
    }
```
Function increaseParticipantLimit() This is a public function that can only be called by the manager, this is used to increase the participant limit by passing in the index of the event.

### Number8
```
function endEvent(uint256 _index) public onlyManager(_index){
        require(events[_index].state == 1, "Event not active");
        events[_index].state = 2;
        calcPayout(_index);
        emit EventConcluded(_index, msg.sender, events[_index].state);
    }
```
Function endEvent() This is a public function that can only be called by the manager, this is used to used to set the event status to inactive. and the internal Calcpayout() function is called, safemath is applied here to prevent overflow and underflow error.

### Number9
```
function cancelEvent(uint256 _index) public onlyManager(_index){
        require(events[_index].state == 1, "Event not active");
        events[_index].state = 3;
        emit EventConcluded(_index, msg.sender, events[_index].state);
    }
```
Function cancelEvent() This is a public function that can only be called by the manager, this is used to used to set the staus of an already created event to inactive.
### Number9
```
function forwardStake(uint256 _amount, address _member) internal returns(bool) {
        ERC20Basic token = ERC20Basic(tokenManager);
        token.transfer(_member, _amount);    
        return true;
    }
```
Function forwardStake() This is internal function is used to to transfer token value from member to a memeber address.
### Number10
```
function rsvp(uint256 _index, address _member) private {
        require(forwardStake(events[_index].requiredStake, _member), "Must forward funds to the reward manager");
        /// Send stake to reward manager
        /// Updated state
        memberState[_index][_member] = 1;
        events[_index].totalStaked.add(events[_index].requiredStake);
        events[_index].registered += 1;
        emit MemberRegistered(_index, _member);
    }
```
Function rsvp() This is a private function that checks if the funds needed has been transfered to reward manager. if this condition is met, it set sets the event status to created(1). and add the new address to the members list.

### Number 11
```
 function rsvpDebug(uint256 _index, address _member) external onlyToken {
        /// Send stake to reward manager
        /// Updated state
        memberState[_index][_member] = 1;
        events[_index].totalStaked.add(events[_index].requiredStake);
        events[_index].registered += 1;
        emit MemberRegistered(_index, _member);
    }
```
Function rsvpDebug() This is an external function that can only be called by the token manager, adds the new input to the registered participants storage.




