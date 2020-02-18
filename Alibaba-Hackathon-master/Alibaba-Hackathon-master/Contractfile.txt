pragma solidity ^0.5.0;
contract Bank{
    address owner;
    //setting the owner using the constructur
    constructor() public {
        owner = msg.sender;
    }
    //modifer to verify whether the owner is using the account or not
    modifier onlyOwner {
        require(
            msg.sender == owner,
            "You must be the owner."
        );
        _;
    }
    //owner can self destroy the contract, as mentioned in the problem statement
    function close() public onlyOwner { 
         selfdestruct(msg.sender);
         //self destruct is the call that destroys the contract
    }
    //In the problem statement it is given that he can also use altcoin , intially he is allowed one eth and one alt type of coin
    //this can be extended such that altcoin balance is a array 
    //using byte32 data tyoe to reduce gas cost, string increases gas price heavily
    //if you are testing directly from solc please use hex value of string
    //example of a name 0x1234
    struct AccountHolder{
        bytes32 name;
        uint ethbalance;
        uint altcoinbalance;
    }
    mapping(address=>AccountHolder) AccountHolders;
    address[] public HolderAccountsAdresses;
    //Depositing function
    function deposit(address _address,bytes32 _name,uint _ethbalance,uint _altcoinbalance) public{
        AccountHolders[_address].name=_name;
        AccountHolders[_address].ethbalance=_ethbalance;
        AccountHolders[_address].altcoinbalance=_altcoinbalance;
        HolderAccountsAdresses.push(_address);
    }
    //check function which verifies whether the eth amount is present in account  or not.
    function check(address _address,uint _amount) public view returns(bool){
        if(AccountHolders[_address].ethbalance-_amount >=0){
            return true;
        }
        return false;
    }
    //Withdraw represents simmilar action of a ATM withdrawl
    function withdraweth(address _address,uint _amount) public returns(uint) {
        if(check(_address,_amount)==true){
            AccountHolders[_address].ethbalance-=_amount;
            return AccountHolders[_address].ethbalance;
        }
    }
    //Transer amount eth, it takes two addresses as parameters and transfers eth from one account to another
    function transfereth(address _address,address _address1,uint _amount) public returns(bool){
      if(check(_address,_amount)==true){
          //not using the send method here directly to reduce gas cost, in real time this operation 
          //will be replaced using send or transfer methods
            AccountHolders[_address].ethbalance-=_amount;
            AccountHolders[_address1].ethbalance+=_amount;
            return true;
        }
        return false;
    }
    //takes address as parameter and returns ethbalance of that account
    function balanceinfo(address _address) public view returns(uint){
        return AccountHolders[_address].ethbalance;
    }
    //Simmilar functions for  to alt coins
    function checkalt(address _address,uint _amount) public view returns(bool){
        if(AccountHolders[_address].altcoinbalance-_amount >=0){
            return true;
        }
        return false;
    }
    function withdrawealt(address _address,uint _amount) public returns(uint) {
        if(checkalt(_address,_amount)==true){
            AccountHolders[_address].altcoinbalance-=_amount;
            return AccountHolders[_address].altcoinbalance;
        }
    }
    function transferalt(address _address,address _address1,uint _amount) public returns(bool){
      if(checkalt(_address,_amount)==true){
            AccountHolders[_address].altcoinbalance-=_amount;
            AccountHolders[_address1].altcoinbalance+=_amount;
            return true;
        }
        return false;
    }
    function balanceinfoalt(address _address) public view returns(uint){
        return AccountHolders[_address].altcoinbalance;
    }
    
    
}