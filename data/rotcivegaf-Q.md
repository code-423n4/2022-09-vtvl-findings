# QA report

## Low Risk
| L-N    |Issue|Instances|
|:------:|:----|:-------:|
| [L&#x2011;01] | Open question, the response could change the contract behavior | 1 |

Total: 1 instances over 1 issues

## Non-critical

| N-N    |Issue|Instances|
|:------:|:----|:-------:|
| [N&#x2011;01] | Unused imports | 2 |
| [N&#x2011;02] | Non-library/interface files should use fixed compiler versions, not floating ones | 1 |
| [N&#x2011;03] | Should be mock contracts | 2 |
| [N&#x2011;04] | Unused commented code lines | 5 |
| [N&#x2011;05] | Lint | 17 |
| [N&#x2011;06] | Declare the return variable in the returns | 1 |
| [N&#x2011;07] | Add the ability to withdraw ETH | 1 |
| [N&#x2011;08] | Open TODO | 1 |
| [N&#x2011;09] | Lines are too long | 66 |
| [N&#x2011;10] | Typo | 1 |

Total: 97 instances over 10 issues

## Low Risk

### [L-01] Open question, the response could change the contract behavior

It's an open question: `Should we allow this?`, but don't allow the scenario of `initialSupply_`
and `maxSupply_`, both are `0`

```solidity
File: /contracts/token/VariableSupplyERC20Token.sol

22        // max supply == 0 means mint at will.
23        // initialSupply_ == 0 means nothing preminted
24        // Therefore, we have valid scenarios if either of them is 0
25        // However, if both are 0 we might have a valid scenario as well - user just wants to create a token but doesn't want to mint anything
26        // Should we allow this?
```

## Non-critical

### [N-01] Unused imports

```solidity
File: /contracts/AccessProtected.sol

05 import "@openzeppelin/contracts/access/Ownable.sol";
```

```solidity
File: /contracts/VTVLVesting.sol

06 import "@openzeppelin/contracts/access/Ownable.sol";
```

### [N‑02] Non-library/interface files should use fixed compiler versions, not floating ones

```solidity
File: /contracts/token/VariableSupplyERC20Token.sol

2 pragma solidity ^0.8.14;
```

### [N‑03] Should be mock contracts

The **/contracts/token/FullPremintERC20Token.sol** and **/contracts/token/VariableSupplyERC20Token.sol** are used as helpers for development, move to test folder and name with Mock at start like: MockFullPremintERC20Token.sol and MockVariableSupplyERC20Token.sol

### [N-04] Unused commented code lines

There are a lot of commented code lines with this code could be important for the contract behavior

```solidity
File: /contracts/token/FullPremintERC20Token.sol

9    // uint constant _initialSupply = 100 * (10**18);
```

```solidity
File: /contracts/VTVLVesting.sol

113        // Save gas, omit further checks
114        // require(_claim.linearVestAmount + _claim.cliffAmount > 0, "INVALID_VESTED_AMOUNT");
115        // require(_claim.endTimestamp > 0, "NO_END_TIMESTAMP");

131        // We don't even need to check for active to be unset, since this function only
132        // determines that a claim hasn't been set
133        // require(_claim.isActive == false, "CLAIM_ALREADY_EXISTS");

135        // Further checks aren't necessary (to save gas), as they're done at creation time (createClaim)
136        // require(_claim.endTimestamp == 0, "CLAIM_ALREADY_EXISTS");
137        // require(_claim.linearVestAmount + _claim.cliffAmount == 0, "CLAIM_ALREADY_EXISTS");
138        // require(_claim.amountWithdrawn == 0, "CLAIM_ALREADY_EXISTS");

261        // require(_endTimestamp > 0, "_endTimestamp must be valid"); // not necessary because of the next condition (transitively)
```

### [N-05] Lint

Add space between `if` and `(`, `if (`:

```solidity
File: /contracts/token/VariableSupplyERC20Token.sol

31        if(initialSupply_ > 0) {

40        if(mintableSupply > 0) {
```

```solidity
File: /contracts/VTVLVesting.sol

151        if(_claim.isActive) {

154            if(_referenceTs > _claim.endTimestamp) {

160            if(_referenceTs >= _claim.cliffReleaseTimestamp) {

166            if(_referenceTs > _claim.startTimestamp) {
```

Remove space:

```solidity
File: /contracts/VTVLVesting.sol

From:
426                ) private  hasNoClaim(_recipient) {
To:
426                ) private hasNoClaim(_recipient) {

From:
97    @dev  To determine this, we check that a claim:
To:
97    @dev To determine this, we check that a claim:

From:
426        require( _claim.amountWithdrawn < finalVestAmt, "NO_UNVESTED_AMOUNT");
To:
426        require(_claim.amountWithdrawn < finalVestAmt, "NO_UNVESTED_AMOUNT");

331 \n

366 \n

413 \n
```

Wrong identation:

```solidity
File: /contracts/VTVLVesting.sol

From:
246            address _recipient,
247            uint40 _startTimestamp,
248            uint40 _endTimestamp,
249            uint40 _cliffReleaseTimestamp,
250            uint40 _releaseIntervalSecs,
251            uint112 _linearVestAmount,
252            uint112 _cliffAmount
253                ) private  hasNoClaim(_recipient) {
To:
246        address _recipient,
247        uint40 _startTimestamp,
248        uint40 _endTimestamp,
249        uint40 _cliffReleaseTimestamp,
250        uint40 _releaseIntervalSecs,
251        uint112 _linearVestAmount,
252        uint112 _cliffAmount
253    ) private  hasNoClaim(_recipient) {

From:
270        require(
271            (
272                _cliffReleaseTimestamp > 0 &&
273                _cliffAmount > 0 &&
274                _cliffReleaseTimestamp <= _startTimestamp
275            ) || (
276                _cliffReleaseTimestamp == 0 &&
277                _cliffAmount == 0
278        ), "INVALID_CLIFF");
To:
270        require(
271            (
272                _cliffReleaseTimestamp > 0 &&
273                _cliffAmount > 0 &&
274                _cliffReleaseTimestamp <= _startTimestamp
275            ) || (
276                _cliffReleaseTimestamp == 0 &&
277                _cliffAmount == 0
278            ),
279            "INVALID_CLIFF"
280        );

From:
318            address _recipient,
319            uint40 _startTimestamp,
320            uint40 _endTimestamp,
321            uint40 _cliffReleaseTimestamp,
322            uint40 _releaseIntervalSecs,
323            uint112 _linearVestAmount,
324            uint112 _cliffAmount
325                ) external onlyAdmin {
To:
318        address _recipient,
319        uint40 _startTimestamp,
320        uint40 _endTimestamp,
321        uint40 _cliffReleaseTimestamp,
322        uint40 _releaseIntervalSecs,
323        uint112 _linearVestAmount,
324        uint112 _cliffAmount
325    ) external onlyAdmin {

From:
340        uint112[] memory _cliffAmounts)
341        external onlyAdmin {
To:
340        uint112[] memory _cliffAmounts
341    ) external onlyAdmin {

From:
344        require(_startTimestamps.length == length &&
345                _endTimestamps.length == length &&
346                _cliffReleaseTimestamps.length == length &&
347                _releaseIntervalsSecs.length == length &&
348                _linearVestAmounts.length == length &&
349                _cliffAmounts.length == length,
350                "ARRAY_LENGTH_MISMATCH"
351        );
To:
344        require(
345            _startTimestamps.length == length &&
346            _endTimestamps.length == length &&
347            _cliffReleaseTimestamps.length == length &&
348            _releaseIntervalsSecs.length == length &&
349            _linearVestAmounts.length == length &&
350            _cliffAmounts.length == length,
351            "ARRAY_LENGTH_MISMATCH"
352        );
```

### [N-06] Declare the return variable in the returns

```solidity
File: /contracts/VTVLVesting.sol

From:
147    function _baseVestedAmount(Claim memory _claim, uint40 _referenceTs) internal pure returns (uint112) {
148        uint112 vestAmt = 0;
To:
147    function _baseVestedAmount(Claim memory _claim, uint40 _referenceTs) internal pure returns (uint112 vestAmt) {
```

### [N-07] Add the ability to withdraw ETH

The comment of the `withdrawOtherToken` function says: "This contract controls/vests token at "tokenAddress". However, someone might send a different token. To make sure these don't get accidentally trapped, give admin the ability to withdraw them (to their own address)."
The contract must also have a function to withdraw ETH in case someone sends ETH

### [N-08] Open TODO

Open TODO can point to architecture or programming issues that still need to be resolved.

```solidity
File: /contracts/VTVLVesting.sol

From:
266        // Potential TODO: sanity check, if _linearVestAmount == 0, should we perhaps force that start and end ts are the same?
```

### [N‑09] Lines are too long

Usually lines in source code are limited to [80](https://softwareengineering.stackexchange.com/questions/148677/why-is-80-characters-the-standard-limit-for-code-width) characters. Today's screens are much larger so it's reasonable to stretch this in some cases. Since the files will most likely reside in GitHub, and GitHub starts using a scroll bar in all cases when the length is over [164](https://github.com/aizatto/character-length) characters, the lines below should be split when they reach that length

Over 100 characters:
```solidity
File: /contracts/token/FullPremintERC20Token.sol

 7 // VariableSupplyERC20Token could be used instead, but it needs to track available to mint supply (extra slot)

10      constructor(string memory name_, string memory symbol_, uint256 supply_) ERC20(name_, symbol_) {
```

```solidity
File: /contracts/token/VariableSupplyERC20Token.sol

 8 @notice A ERC20 token contract that allows minting at will, with limited or unlimited supply.

14     @notice A ERC20 token contract that allows minting at will, with limited or unlimited supply. No burning possible

18     @param initialSupply_ - How much to immediately mint (saves another transaction). If 0, no mint at the beginning.

19     @param maxSupply_ - What's the maximum supply. The contract won't allow minting over this amount. Set to 0 for no limit.

21     constructor(string memory name_, string memory symbol_, uint256 initialSupply_, uint256 maxSupply_) ERC20(name_, symbol_) {

25         // However, if both are 0 we might have a valid scenario as well - user just wants to create a token but doesn't want to mint anything

30         // Note: the check whether initial supply is less than or equal than mintableSupply will happen in mint fn.

49     // Example: if the user can burn tokens already assigned to vesting schedules, it could be unable to pay its obligations.
```

```solidity
File: /contracts/AccessProtected.sol

 9 @dev Exposes the onlyAdmin modifier, which will revert (ADMIN_ACCESS_REQUIRED) if the caller is not the owner nor the admin.
```

```solidity
File: /contracts/VTVLVesting.sol

23    * This gets reduced as the users are paid out or when their schedules are revoked (as it is not reserved any more).

24    * In other words, this represents the amount the contract is scheduled to pay out at some point if the

34        // Gives us a range from 1 Jan 1970 (Unix epoch) up to approximately 35 thousand years from then (2^40 / (365 * 24 * 60 * 60) ~= 35k)

36        uint40 endTimestamp; // When does the vesting end - the vesting goes linearly between the start and end timestamps

37        uint40 cliffReleaseTimestamp; // At which timestamp is the cliffAmount released. This must be <= startTimestamp

44        uint112 amountWithdrawn; // how much was withdrawn thus far - released at the cliffReleaseTimestamp

69    event ClaimRevoked(address indexed _recipient, uint112 _numTokensWithheld, uint256 revocationTimestamp, Claim _claim);

88    @dev Could be using public claims var, but this is cleaner in terms of naming. (getClaim(address) as opposed to claims(address)).

135        // Further checks aren't necessary (to save gas), as they're done at creation time (createClaim)

143    @notice Pure function to calculate the vested amount from a given _claim, at a reference timestamp

147    function _baseVestedAmount(Claim memory _claim, uint40 _referenceTs) internal pure returns (uint112) {

164            // Calculate the linearly vested amount - this is relevant only if we're past the schedule start

165            // at _referenceTs == _claim.startTimestamp, the period proportion will be 0 so we don't need to start the calc

167                uint40 currentVestingDurationSecs = _referenceTs - _claim.startTimestamp; // How long since the start

172                // Calculate the linear vested amount - fraction_of_interval_completed * linearVestedAmount

173                // Since fraction_of_interval_completed is truncatedCurrentVestingDurationSecs / finalVestingDurationSecs, the formula becomes

174                // truncatedCurrentVestingDurationSecs / finalVestingDurationSecs * linearVestAmount, so we can rewrite as below to avoid

176                uint112 linearVestAmount = _claim.linearVestAmount * truncatedCurrentVestingDurationSecs / finalVestingDurationSecs;

184        // Rationale: no matter how we calculate the vestAmt, we can never return that less was vested than was already withdrawn.

185        // Case where this could be relevant - If the claim is inactive, vestAmt would be 0, yet if something was already withdrawn

186        // on that claim, we want to return that as vested thus far - as we want the function to be monotonic.

202    @notice Calculate the total vested at the end of the schedule, by simply feeding in the end timestamp to the function above.

212    @notice Calculates how much can we claim, by subtracting the already withdrawn amount from the vestedAmount at this moment.

235    @notice Permission-unchecked version of claim creation (no onlyAdmin). Actual logic for create claim, to be run within either createClaim or createClaimBatch.

236    @dev This'll simply check the input parameters, and create the structure verbatim based on passed in parameters.

240    @param _cliffReleaseTimestamp - The timestamp when the cliff is released (must be <= _startTimestamp, or 0 if no vesting)

241    @param _releaseIntervalSecs - The release interval for the linear vesting. If this is, for example, 60, that means that the linearly vested amount gets released every 60 seconds.

242    @param _linearVestAmount - The total amount to be linearly vested between _startTimestamp and _endTimestamp

243    @param _cliffAmount - The amount released at _cliffReleaseTimestamp. Can be 0 if _cliffReleaseTimestamp is also 0.

256        require(_linearVestAmount + _cliffAmount > 0, "INVALID_VESTED_AMOUNT"); // Actually only one of linearvested/cliff amount must be 0, not necessarily both

260        // -> Conclusion: we want to allow this, for founders that might have forgotten to add some users, or to avoid issues with transactions not going through because of discoordination between block.timestamp and sender's local time

261        // require(_endTimestamp > 0, "_endTimestamp must be valid"); // not necessary because of the next condition (transitively)

262        require(_startTimestamp < _endTimestamp, "INVALID_END_TIMESTAMP"); // _endTimestamp must be after _startTimestamp

264        require((_endTimestamp - _startTimestamp) % _releaseIntervalSecs == 0, "INVALID_INTERVAL_LENGTH");

266        // Potential TODO: sanity check, if _linearVestAmount == 0, should we perhaps force that start and end ts are the same?

269        // Both or neither of _cliffReleaseTimestamp and _cliffAmount must be set. If cliff is set, _cliffReleaseTimestamp must be before or at the _startTimestamp

290        // Our total allocation is simply the full sum of the two amounts, _cliffAmount + _linearVestAmount

294        // Still no effects up to this point (and tokenAddress is selected by contract deployer and is immutable), so no reentrancy risk

295        require(tokenAddress.balanceOf(address(this)) >= numTokensReservedForVesting + allocatedAmount, "INSUFFICIENT_BALANCE");

208    @dev This'll simply check the input parameters, and create the structure verbatim based on passed in parameters.

312    @param _cliffReleaseTimestamp - The timestamp when the cliff is released (must be <= _startTimestamp, or 0 if no vesting)

313    @param _releaseIntervalSecs - The release interval for the linear vesting. If this is, for example, 60, that means that the linearly vested amount gets released every 60 seconds.

314    @param _linearVestAmount - The total amount to be linearly vested between _startTimestamp and _endTimestamp

315    @param _cliffAmount - The amount released at _cliffReleaseTimestamp. Can be 0 if _cliffReleaseTimestamp is also 0.

326        _createClaimUnchecked(_recipient, _startTimestamp, _endTimestamp, _cliffReleaseTimestamp, _releaseIntervalSecs, _linearVestAmount, _cliffAmount);

330    @notice The batch version of the createClaim function. Each argument is an array, and this function simply repeatedly calls the createClaim.

354            _createClaimUnchecked(_recipients[i], _startTimestamps[i], _endTimestamps[i], _cliffReleaseTimestamps[i], _releaseIntervalsSecs[i], _linearVestAmounts[i], _cliffAmounts[i]);

369        // we can use block.timestamp directly here as reference TS, as the function itself will make sure to cap it to endTimestamp

382        // Reduce the allocated amount since the following transaction pays out so the "debt" gets reduced

400        uint256 amountRemaining = tokenAddress.balanceOf(address(this)) - numTokensReservedForVesting;

428        // The amount that is "reclaimed" is equal to the total allocation less what was already withdrawn

432        _claim.isActive = false;    // This effectively reduces the liability by amountRemaining, so we can reduce the liability numTokensReservedForVesting by that much

441    @dev This contract controls/vests token at "tokenAddress". However, someone might send a different token.

442    To make sure these don't get accidentally trapped, give admin the ability to withdraw them (to their own address).

446        require(_otherTokenAddress != tokenAddress, "INVALID_TOKEN"); // tokenAddress address is already sure to be nonzero due to constructor
```

### [N-10] Typo

```solidity
File: /contracts/token/VariableSupplyERC20Token.sol

From:
107                // Next, we need to calculated the duration truncated to nearest releaseIntervalSecs
To:
107                // Next, we need to calculate the duration truncated to nearest releaseIntervalSecs
```
