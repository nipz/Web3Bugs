# Classification Standard

In this document, we present the process we use to classify bugs in our study, along with examples of ambiguous cases. We also provide a description of each bug label as a reference, which can be found at the end of this document.

It is important to note that classifying functional bugs can be a subjective process, and we welcome any suggestions for improving our classification standards.

## Process
To classify a bug, we follow these steps:

+ First, we validate whether the bug is within the scope of our study. Our focus is on exploitable bugs in smart contracts, so many bugs may be excluded. If the bug falls into any of the __O__ categories, it is considered out of scope.
+ We then validate whether the bug can be found by tools with simple and generic testing oracles. To do so, we investigate how the bug is exploited. If any oracle mentioned by the __L__ categories can detect the exploit, we classify it as an __L__ bug. It is important to note that this is an over-approximation of current vulnerability detection techniques. As long as there is an oracle that can detect the exploit, we assume the detection tool can detect it (regardless of the tool's effectiveness). For example, we assume there is no path exploration issue for symbolic execution and any constraint can be solved in time.
+ Any bugs that remain after the previous steps are labeled as __S__ bugs.
    + We first investigate the root cause of the bug. If the root cause can be classified as __S1__ to __S6__, we label it accordingly.
    + For the remaining bugs, we investigate how they can be exploited. If the way of exploit matches any of the __SE__ types, we label it accordingly.
    + Any remaining bugs are labeled as SC.

## Ambiguous Cases

__WIP__

## Bug Labels

### Out-of-scope Bugs

These are bugs that fall outside the scope of our study and are thus not analyzed further.

+ __O1__: These vulnerabilities can only be exploited by privileged users (e.g., rug pull), or when the privileged users make mistakes (e.g., applying incorrect configuration during deployment).
+ __O2__: We cannot access the source code of the project.
+ __O3__: These vulnerabilities can only be exploited with further actions by victim users (e.g., [EIP-4626 inflation attacks](https://ethereum-magicians.org/t/address-eip-4626-inflation-attacks-with-virtual-shares-and-assets/12677))
+ __O4__: Bugs that occur in off-chain components.
+ __O5__: Typo or trivial bugs that render the contract non-deployable or non-functional. We believe these types of bugs are unlikely to occur in contracts that are ready for audit or have been deployed.
+ __O6__: Bugs that are not considered as such by the project. This can be due to disagreements between the auditors and the project (common in early contests), no explicit code affected by the bug, or intentional behavior that aligns with the business model (where the business model may be flawed).
+ __O7__: Doubtful findings, which we believe may be invalid, duplicated, or non-critical (common in early contests).

### Bugs with Simple and General Testing Oracles

These are bugs that can be detected using simple and general oracles and do not require an in-depth understanding of the code semantics.

+ __L1__: Reentrancy.
+ __L2__: Rounding issues or precision loss.
+ __L3__: Bugs that are caused by using uninitialized variables.
+ __L4__: Bugs that are caused by exceeding the gas limitation.
+ __L5__: Storage collision and confusion between proxy and implementation.
+ __L6__: Arbitrary external function call.
+ __L7__: Integer overflow and underflow.
+ __L8__: Revert issues caused by low-level calls or external libraries.
+ __L9__: Bugs that are caused by writing to memory that does not apply to the storage.
+ __LA__: Cryptographic issues.
+ __LB__: Using `tx.origin`.

### Bugs that Require High-level Semantical Oracles

These are bugs that require high-level semantical oracles to detect, as they arise from inconsistencies between the code implementation and the business model.

+ __S1__: Price oracle manipulation.
    + __S1-1__: AMM price oracle manipulation.
    + __S1-2__: Sandwich attack.
    + __S1-3__: Non-AMM price oracle manipulation.
+ __S2__: ID-related violations.
    + __S2-1__: ID can be arbitrarily set by users or lack of ID validation. ID can also be a project-specified variable (e.g., hash) or an address.
    + __S2-2__: Shared resource (e.g., token) without proper locks.
    + __S2-3__: ID uniqueness violation (i.e., an ID should be unique but it is not).
+ __S3__: Erroneous state updates.
    + __S3-1__: Missing state update.
    + __S3-2__: Incorrect state updates, e.g., a state update that should not be there.
+ __S4__: Business-flow atomicity violations.
    + __S4-1__: Lack of proper locks for a business flow consisting of multiple transactions.
+ __S5__: Privilege escalation and access control issues.
    + __S5-1__: Users can update privileged state variables arbitrarily (caused by lack of ID-unrelated input sanitization).
    + __S5-2__: Users can invoke some functions at a time they should not be able to do so.
    + __S5-3__: Privileged functions can be called by anyone or at any time.
+ __S6__: Erroneous accounting.
    + __S6-1__: Incorrect calculating order.
    + __S6-2__: Returning an unexpected value that deviates from the expected semantics specified for the contract.
    + __S6-3__: Calculations performed with incorrect numbers (e.g., `x = a + b` ==> `x = a + c`).
    + __S6-4__: Other accounting errors (e.g., `x = a + b` ==> `x = a - b`).
+ __SE__: Broken business models due to unexpected operations
    + __SE-1__: Unexpected function invocation sequences (e.g., external calls to dependent contracts).
    + __SE-2__: Unexpected environment or contract conditions (e.g., ChainLink returning outdated data or significant slippage occurring).
    + __SE-3__: A given function is invoked multiple times unexpectedly.
    + __SE-4__: Unexpected function arguments.
+ __SC__: Contract implementation-specific bugs. These bugs are difficult to categorize into the above categories.