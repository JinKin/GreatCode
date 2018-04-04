pragma solidity ^0.4.19;

contract Utils {
    modifier validAddress(address _address) {
        require(_address != address(0));
        _;
    }

    function safeAdd(uint256 _x, uint256 _y) internal pure returns (uint256) {
        uint256 _z = _x + _y;
        assert(_z >= _x);
        return _z;
    }

    function safeSub(uint256 _x, uint256 _y) internal pure returns (uint256) {
        assert(_x >= _y);
        return _x - _y;
    }

    function safeMul(uint256 _x, uint256 _y) internal pure returns (uint256) {
        uint256 _z = _x * _y;
        assert(_x == 0 || _z / _x == _y);
        return _z;
    }
    
    function safeDiv(uint256 _x, uint256 _y) internal pure returns (uint256) {
        assert(_y != 0); 
        uint256 _z = _x / _y;
        assert(_x == _y * _z + _x % _y); 
        return _z;
    }

}

contract Ownable {
    address public owner;

    function Ownable() public {
        owner = msg.sender;
    }

    modifier onlyOwner() {
        require(msg.sender == owner);
        _;
    }
    function transferOwnership(address newOwner) onlyOwner public {
        require(newOwner != address(0));
        owner = newOwner;
    }
}

contract ERC20Token {
    function balanceOf(address who) public constant returns (uint256);
    function transfer(address to, uint256 value) public returns (bool);
    function allowance(address owner, address spender) public constant returns (uint256);
    function transferFrom(address from, address to, uint256 value) public returns (bool);
    function approve(address spender, uint256 value) public returns (bool);
    event Transfer(address indexed _from, address indexed _to, uint256 _value);
    event Approval(address indexed _owner, address indexed _spender, uint256 _value);
}

contract StandardToken is ERC20Token, Utils, Ownable {
 
    bool public transfersEnabled = true;  
    
    mapping (address => uint256) public balanceOf;
    mapping (address => mapping (address => uint256)) public allowed;

    modifier transfersAllowed {
        assert(transfersEnabled);
        _;
    }

    function disableTransfers(bool _disable) public onlyOwner {
        transfersEnabled = !_disable;
    }
    
    function transfer(address _to, uint256 _value) public validAddress(_to) transfersAllowed returns (bool success){
        require(balanceOf[msg.sender] >= _value && balanceOf[_to] + _value > balanceOf[_to]); 
        
        balanceOf[msg.sender] = safeSub(balanceOf[msg.sender], _value);
        balanceOf[_to] = safeAdd(balanceOf[_to], _value);
        Transfer(msg.sender, _to, _value);
        return true;
    }

    function transferFrom(address _from, address _to, uint256 _value) public validAddress(_from) validAddress(_to) transfersAllowed returns (bool success){
        require(balanceOf[_from] >= _value && allowed[_from][msg.sender] >= _value && balanceOf[_to] + _value > balanceOf[_to]); 
        
        allowed[_from][msg.sender] = safeSub(allowed[_from][msg.sender], _value);
        balanceOf[_from] = safeSub(balanceOf[_from], _value);
        balanceOf[_to] = safeAdd(balanceOf[_to], _value);
        Transfer(_from, _to, _value);
        return true;
    }

    function approve(address _spender, uint256 _value) public validAddress(_spender) returns (bool success){
        require(balanceOf[msg.sender] >= _value); 
        allowed[msg.sender][_spender] = _value;
        Approval(msg.sender, _spender, _value);
        return true;
    }

    function balanceOf(address _owner) public validAddress(_owner) constant returns (uint256 balance) {
        return balanceOf[_owner];
    }
    
    function allowance(address _owner, address _spender) public validAddress(_owner) validAddress(_spender) constant returns (uint256 remaining) {
        return allowed[_owner][_spender];
    }
}

contract AIPCToken is StandardToken {

    string public constant name = "AIPC";
    string public constant symbol = "AIC";
    uint8 public constant decimals = 18;
    
    uint public constant etherCap = 50000 ether; 
    uint public constant tokenRate = 2520;
    uint public constant startDate = 1523116800;
    uint public constant endDate = 1525708799;
    
    uint private preBonusPhase = startDate + 10 * 1 days;
    uint private preBonusRate = 2898;
    uint private preBonusMax1  = 3150 * 10**4 * BASE;
    uint private firstBonusPhase = startDate + 20 *1 days;
    uint private firstBonusRate = 2772;
    uint private secondBonusRate = 2646;
    uint private preBonusMax2 = 9450 * 10**4 * BASE;
    
    uint constant BASE = 10**18;
    
    uint256 public constant totalSupply = 21000 * 10**4 * BASE;
    uint public crowdsaleCap = 12600 * 10**4 * BASE;  
    uint public fundTeamCap = 1050 * 10**4 * BASE;       
    uint public teamAllocation = 3150 * 10**4 * BASE; 
    uint public rewardAllocation = 2100 * 10**4 * BASE;
    uint public fundingAllocation = 2100 * 10**4 * BASE; 
    
    bool public groupAllocated = false; 
    bool public fundTeamAllocated = false; 
    
    uint public groupUnlockAt = startDate + 4 * 30 days;
    uint public fundTeamUnlockAt = startDate + 7 * 30 days;

    uint public presaleEtherRaised = 0;
    uint public presaleTokenRaised = 0;
    uint public presaleState1Raised = 0;
    uint public presaleState2Raised = 0;

    bool public preFinish = false;
    bool public finish = false;
    bool public halted = false;
    
    address public constant founderWallet = 0xF89771Ff53bEC4e77c6Af397eCCe6dDeb99c9d83;
    address public constant teamWallet = 0x3aFB561691c39a95a6EaD91017cC4b82B763470D;
    address public constant fundTeamWallet = 0x3bD0cb3C67405AA4ef41E49D5972c852865c40e5;
    address public constant rewardWallet = 0x86Af22495DA8B26629bB3ed9E34BCF3c4Cf3B0DC;
    address public constant fundingWallet = 0x1AF151E1E07E872258774A366360d6f4ae420d9C;
    
    address group1 = 0x71BdD6E0163180f211547E41876E58Bdd046a0C7;
    address group2 = 0x76DddEe57F51265E1Ea72e9cCC77076F9A94CB0F;

    function AIPCToken() {

        balanceOf[teamWallet] = teamAllocation;
        balanceOf[rewardWallet] = rewardAllocation;
        balanceOf[fundingWallet] = fundingAllocation;
     
        balanceOf[founderWallet] = totalSupply - balanceOf[teamWallet] - balanceOf[rewardWallet] - balanceOf[fundingWallet];
        
        Transfer(0x0, teamWallet, balanceOf[teamWallet]);
        Transfer(0x0, rewardWallet, balanceOf[rewardWallet]);
        Transfer(0x0, fundingWallet, balanceOf[fundingWallet]);
        Transfer(0x0, founderWallet, balanceOf[founderWallet]);
    }

    function () payable public {
        if (msg.value < 0.1 ether || now < startDate || now > endDate || halted || finish) revert();
        buyTokens(msg.sender,msg.value);
    }

    function buyTokens(address _contributor, uint _amount)internal {
        uint maxTokenContribution = 0;
        uint contributionTokenAmount = 0;
        uint nextEthAmount = 0;
        uint tokensAmount = 0;
        uint returnEthAmount = 0;
        
        if (now < preBonusPhase){
            
            if (!preFinish){
                maxTokenContribution = preBonusMax1 - presaleState1Raised;
                contributionTokenAmount = preBonusRate * _amount;
                
                if (contributionTokenAmount >= maxTokenContribution){
                    contributionTokenAmount = maxTokenContribution;
                    nextEthAmount = _amount - safeDiv(contributionTokenAmount,preBonusRate);
                    preFinish = true;
                }
        
                tokensAmount += contributionTokenAmount;
                presaleEtherRaised += safeDiv(contributionTokenAmount,preBonusRate);
                presaleTokenRaised += contributionTokenAmount;
                presaleState1Raised += contributionTokenAmount;
                
                if (nextEthAmount != 0){
                    maxTokenContribution = preBonusMax1 + preBonusMax2 - presaleTokenRaised;
                    contributionTokenAmount = firstBonusRate * nextEthAmount;
    
                    if (contributionTokenAmount >= maxTokenContribution){
                        contributionTokenAmount = maxTokenContribution;
                        returnEthAmount = nextEthAmount - safeDiv(contributionTokenAmount,firstBonusRate);
                        finish = true;
                    }
                    
                    tokensAmount += contributionTokenAmount;
                    presaleEtherRaised += safeDiv(contributionTokenAmount,firstBonusRate);
                    presaleTokenRaised += contributionTokenAmount;
                    presaleState2Raised += contributionTokenAmount;
                } 
            }else{
                maxTokenContribution = preBonusMax2 - presaleState2Raised;
                contributionTokenAmount = firstBonusRate * _amount;
                
                if (contributionTokenAmount >= maxTokenContribution){
                    contributionTokenAmount = maxTokenContribution;
                    returnEthAmount = _amount - safeDiv(contributionTokenAmount,firstBonusRate);
                    finish = true;
                }
                    
                tokensAmount += contributionTokenAmount;
                presaleEtherRaised += safeDiv(contributionTokenAmount,firstBonusRate);
                presaleTokenRaised += contributionTokenAmount;
                presaleState2Raised += contributionTokenAmount;
            }
        }else if (now < firstBonusPhase){
            maxTokenContribution = preBonusMax2 - presaleState2Raised;
            contributionTokenAmount = firstBonusRate * _amount;
                
            if (contributionTokenAmount >= maxTokenContribution){
                contributionTokenAmount = maxTokenContribution;
                returnEthAmount = _amount - safeDiv(contributionTokenAmount,firstBonusRate);
                finish = true;
            }
                    
            tokensAmount += contributionTokenAmount;
            presaleEtherRaised += safeDiv(contributionTokenAmount,firstBonusRate);
            presaleTokenRaised += contributionTokenAmount;
            presaleState2Raised += contributionTokenAmount;
        }else if (now < endDate){
            maxTokenContribution = preBonusMax2 - presaleState2Raised;
            contributionTokenAmount = secondBonusRate * _amount;
                
            if (contributionTokenAmount >= maxTokenContribution){
                contributionTokenAmount = maxTokenContribution;
                returnEthAmount = _amount - safeDiv(contributionTokenAmount,secondBonusRate);
                finish = true;
            }
                    
            tokensAmount += contributionTokenAmount;
            presaleEtherRaised += safeDiv(contributionTokenAmount,secondBonusRate);
            presaleTokenRaised += contributionTokenAmount;
            presaleState2Raised += contributionTokenAmount;
        }

        balanceOf[founderWallet] = safeSub(balanceOf[founderWallet], tokensAmount);
        balanceOf[_contributor] = safeAdd(balanceOf[_contributor], tokensAmount);
        Transfer(founderWallet, _contributor, tokensAmount);
        
        founderWallet.send(_amount - returnEthAmount);
        if (returnEthAmount != 0) {
          _contributor.send(returnEthAmount);
        }
    }

    
    function transfer(address _to, uint256 _value) public validAddress(_to) transfersAllowed returns (bool success){
        if(!isSpecialTransfersAllowed(msg.sender)){
            revert();
            return false;
        }
            
        assert(super.transfer(_to, _value));
        return true;
    }

    function transferFrom(address _from, address _to, uint256 _value) public validAddress(_from) validAddress(_to) transfersAllowed returns (bool success){
        if(!isSpecialTransfersAllowed(msg.sender)){
            revert();
            return false;
        }
            
        assert(super.transferFrom(_from, _to, _value));
        return true;
    }
    
    function isSpecialTransfersAllowed (address _address) internal returns(bool){
        if(_address == group1 || _address == group2){
            if (groupAllocated){
                return true;
            }
            if (now >= groupUnlockAt){
                groupAllocated = true;
                return true;
            }
            return false;
        }
        if(_address == fundTeamWallet){
            if (fundTeamAllocated){
                return true;
            }
            if (now >= fundTeamUnlockAt){
                fundTeamAllocated = true;
                return true;
            }
            return false;
        }
        return true;
    }
    
    function halt() public onlyOwner{
        halted = true;
    }

    function unhalt() public onlyOwner{
        halted = false;
    }
}
