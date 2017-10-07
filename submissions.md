# Bug Bounty Submissions
We'll be collecting bug bounty submissions and their responses here. Please be sure to take a look before making a submission. Thank you!

### M.L.

#### Submission 1

Hi,

I think I found a bug in your AirSwapToken.sol code. Your ico mentions
that there will be 500 million tokens in circulation but the following
line of code sets the supply to 5 trillion :

uint256 public constant totalSupply = 5000000000000;

#### Submission 2

Hi,

Think I found another one. In the following code :

BalanceLocked(msg.sender, balanceLocks[msg.sender].amount, _value,
_expiry); balanceLocks[msg.sender] = BalanceLock(_value, _expiry);

You broadcast that the balance has been locked before it has actually
been locked. If the lock fails after broadcast there could be some
inconsistent state depending on what you are doing in the event
subscription code.

### Response (Phil)

#### Submission 1

Please see the EIP20 standard:
https://github.com/ethereum/EIPs/blob/master/EIPS/eip-20-token-standard.md

Specifically the section "decimals

Returns the number of decimals the token uses - e.g. 8, means to divide
the token amount by 100000000 to get its user representation."

AirSwap has four decimal places, so 5000000000000 / (10^4) = 500M as
specified.

#### Submission 2

There is no way for the line you indicated to fail without throwing,
reverting the log event above it.  The order here is not a bug.


No security critical issues or bugs are present, so I recommend no
changes to the contract.

### R.M.C.F.

#### Submission 1

To whom it may concern,

I'm currently making my way through your bountied contracts and will
likely be in contact again, but first wanted to report that in your
constructor function of AirSwapToken.sol, it doesn't look like _balance
is not asserted to be less than totalSupply, so if it is purposefully
or accidentally set to be greater than it, balances[_deployer] will
underflow into an enormous number.

#### Submission 2

To whom it may concern,

Here's a follow-up with more findings:

AirSwapToken/StandardToken.sol:

1. availableBalance will throw instead of return 0 if more or as much
is locked than not

2. The approve/transferFrom ERC20 race condition is not mitigated

3. Short Address Attack is also not mitigated (but not as much of a
problem either)

4. Transfers do not require a valid address (can be 0)

Exchange.sol:

5. makerAmount and takerAmount seem to both never be asserted to be
greater than 0

6. makerAddress and takerAddress can both be 0 and not fail

### Response (Phil)

#### Submission 1

This is covered in our Token audit:
https://github.com/airswap/contracts/blob/master/audits/phil-daian/airswap-token-contract-audit-v0.pdf
and is intended functionality / non-security critical.  As we see
there, the deployer would have to be malicious to underflow this value,
and this could easily be checked by users before tokens become
transferable.

#### Submission 2


(1) is not possible.  See the token audit, and look for the green
highlights around balance underflows.  The intended invariant is that
balanceLocks[x] <= balances[x], and this is enforced in line
https://github.com/airswap/contracts/blob/517a9275a3f4d5f4d039c6db078d15d67d0f63fa/contracts/AirSwapToken.sol#L81

If you do find a way to cause balanceLocks[x] >= balances[x] with a
non-expired balance lock, please let us know as this is an issue we
would pay a bounty for.

(2) is no longer an EIP20 compliant suggestion.  The EIP20 document at
https://github.com/ethereum/EIPs/blob/master/EIPS/eip-20-token-standard.md
now explicitly instructs developers *not* to include this mitigation in
the specification for approve.

For (3), short address mitigation is not a standard recommendation for
ERC20s (see the Consensys and Zeppelin standard tokens); the suggested
fix for this is at the application layer, and this is mentioned in
several places in the audit.

For (4), we do not wish to waste gas checking for a 0-invalid address;
it is not a standard practice, or a universal security recommendation,
and there is never a way to exclude all invalid addresses from a
contract.

For (5), this is also intended functionality, there is no reason not to
allow people to trade for 0 if they so choose.  Restrictions at amounts
are also application-layer features.

For (6), see (4).


No security critical issues or bugs are present, so I recommend no
changes to the contract.


### S.C.


#### Submission 1

I was able to sign up with Air Swap, prior to your early closing and
have followed the Telegram Desktop, as part of the community.  I see
that there was a bug bounty for contracts that was given based within
the Exchange.sol, and AirSwapToken.sol .  Given the large audience your
team is sharing bug bounties with, I found it odd, to find a spelling
mistake, on the primary page – As you can see below, I’ve highlighted
the mistake, and wish to be provided a low amount of ETH based on my
findings. Here is my Ethereum address.
0x82961424d5cf32c311bbed183d5c4e47d0b54c6f 😊 But in all seriousness, I
am not a software/hardware coder of any sort. I just mine with a few R9
290X’s here in Canada, and make a few bucks a day – so would this
count? I’m grateful to be able to sign up early, and hope to use
AirSwap in some way, in my daily life!



I hope this letter helps your team out, and was a small pause in what
I’m sure is an eventful start to your company.


Seriously though....RIGHT on the main Air Swap page? depdencies? Hahaha



Cheers folks 😃


### Response (Phil)

This was fixed by the AirSwap team and a small token sum should be
transferred because a change did result (though it is not a code bug or
security critical issue).

No security critical issues or bugs are present, so I recommend no
changes to the contract.
