The contract with my modifier:

```solidity
contract Owned {

address private owner;
address private newOwner;


modifier onlyOwner() {
    require(msg.sender == owner);
    _;
}

/// @notice The Constructor assigns the message sender to be `owner`
function Owned() {
    owner = msg.sender;
}


function changeOwner(address _newOwner) onlyOwner {

    newOwner = _newOwner;

}

function acceptOwnership() {
    if (msg.sender == newOwner) {
        owner = newOwner;
        newOwner = 0x0000000000000000000000000000000000000000;
    }
}
}
```

### TokenController (contract A):
```solidity
 contract TokenController is Owned {

SapienToken private sapienToken;

event Mint(address indexed to, uint256 amount);

function TokenController(address _sapien) {

    sapienToken = SapienToken(_sapien);

}

function changeBasicToken(address _sapien) onlyOwner {

    sapienToken = SapienToken(_sapien);

}

function mint(address _to, uint256 _amount) onlyOwner returns (bool) {
    sapienToken.increaseTotal(_amount);
    sapienToken.addToBalance(_to, _amount);
    Mint(_to, _amount);
    return true;
}

}
```

### SapienToken (contract B):

```solidity
contract SapienToken is ERC20Basic, Owned {

using SafeMath for uint256;

mapping(address => uint256) balances;

string public name = "NAME";
string public symbol = "SYMBOL";
uint256 public decimals = 18;


function SapienToken() {


}


function transfer(address _to, uint256 _value) public returns (bool) {

require(_to != address(0));

balances[msg.sender] = balances[msg.sender].sub(_value);
balances[_to] = balances[_to].add(_value);
Transfer(msg.sender, _to, _value);
return true;

}


function balanceOf(address _owner) public constant returns (uint256 balance) {
return balances[_owner];
}

function increaseTotal(uint256 _amount) public onlyOwner {

totalSupply = totalSupply.add(_amount);

}

function addToBalance(address _to, uint256 _amount) public onlyOwner {

balances[_to] = balances[_to].add(_amount);

}

}
```
