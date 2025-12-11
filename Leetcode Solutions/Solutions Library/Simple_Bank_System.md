## Simple Bank System
**Language:** python
**Tags:** bank,financial-transactions,object-oriented-programming,data-validation

### Description:
<p>This code implements a <code>Bank</code> class that manages a set of bank accounts and provides basic transaction functionalities like transfer, deposit, and withdrawal.</p>
<hr>
<h3>1. Overview &amp; Intent</h3>
<p>The <code>Bank</code> class simulates a simple banking system. Its primary purpose is to:</p>
<ul>
<li>Initialize a fixed set of accounts with their starting balances.</li>
<li>Allow validation of account numbers.</li>
<li>Facilitate transfers between two accounts, checking for validity and sufficient funds.</li>
<li>Enable deposits into an account.</li>
<li>Enable withdrawals from an account, checking for validity and sufficient funds.
All transaction methods return <code>True</code> on success and <code>False</code> on failure (e.g., invalid account, insufficient funds).</li>
</ul>
<hr>
<h3>2. How It Works</h3>
<ul>
<li><strong>Initialization (<code>__init__</code>)</strong>:<ul>
<li>Takes a <code>list</code> of integers (<code>balance</code>) representing the initial balances for each account.</li>
<li>Stores this list internally as <code>self.accounts</code>.</li>
<li>Calculates and stores the total number of accounts as <code>self.num_accounts</code>.</li>
</ul>
</li>
<li><strong>Account Validation (<code>_is_valid_account</code>)</strong>:<ul>
<li>A private helper method that checks if a given <code>account</code> number (1-indexed) falls within the valid range (1 to <code>self.num_accounts</code>).</li>
</ul>
</li>
<li><strong>Transfer (<code>transfer</code>)</strong>:<ul>
<li>First, it validates both <code>account1</code> and <code>account2</code> using <code>_is_valid_account</code>. Returns <code>False</code> if either is invalid.</li>
<li>Then, it checks if <code>account1</code> has sufficient funds (<code>self.accounts[account1 - 1] &lt; money</code>). Returns <code>False</code> if not.</li>
<li>If all checks pass, it debits <code>money</code> from <code>account1</code> and credits <code>money</code> to <code>account2</code>.</li>
<li>Returns <code>True</code> upon successful transfer.</li>
</ul>
</li>
<li><strong>Deposit (<code>deposit</code>)</strong>:<ul>
<li>Validates the target <code>account</code>. Returns <code>False</code> if invalid.</li>
<li>If valid, it adds <code>money</code> to the account's balance.</li>
<li>Returns <code>True</code> upon successful deposit.</li>
</ul>
</li>
<li><strong>Withdraw (<code>withdraw</code>)</strong>:<ul>
<li>Validates the target <code>account</code>. Returns <code>False</code> if invalid.</li>
<li>Checks if the account has sufficient funds (<code>self.accounts[account - 1] &lt; money</code>). Returns <code>False</code> if not.</li>
<li>If all checks pass, it subtracts <code>money</code> from the account's balance.</li>
<li>Returns <code>True</code> upon successful withdrawal.</li>
</ul>
</li>
</ul>
<hr>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong>Data Structure for Accounts</strong>:<ul>
<li>A Python <code>list</code> (<code>self.accounts</code>) is used to store account balances.</li>
<li><strong>Trade-off</strong>: This is efficient for sequential integer account IDs (0-indexed internally, 1-indexed externally). It allows O(1) access to any account's balance by index. However, it implies a fixed number of accounts determined at initialization and doesn't easily support non-sequential or dynamically added/removed accounts without list re-structuring.</li>
</ul>
</li>
<li><strong>Account Numbering</strong>:<ul>
<li>Public facing methods (e.g., <code>transfer</code>, <code>deposit</code>) use 1-indexed account numbers, while internal list access uses 0-indexed values (<code>account - 1</code>).</li>
<li><strong>Trade-off</strong>: This aligns with common user expectations (account 1, account 2, etc.) but introduces a potential for off-by-one errors if not consistently handled.</li>
</ul>
</li>
<li><strong>Error Handling</strong>:<ul>
<li>Transaction methods return a <code>boolean</code> (<code>True</code>/<code>False</code>) to indicate success or failure.</li>
<li><strong>Trade-off</strong>: Simple and avoids exceptions, but provides limited information about <em>why</em> an operation failed. Callers need to implement their own logic to determine the specific failure reason.</li>
</ul>
</li>
<li><strong>Helper Method <code>_is_valid_account</code></strong>:<ul>
<li>Encapsulates the logic for checking account validity, promoting code reuse and readability across transaction methods.</li>
</ul>
</li>
</ul>
<hr>
<h3>4. Complexity</h3>
<ul>
<li><strong>Time Complexity</strong>:<ul>
<li><code>__init__</code>: O(N), where N is the number of initial accounts, primarily due to <code>len(balance)</code> and storing the list.</li>
<li><code>_is_valid_account</code>: O(1) – simple comparison.</li>
<li><code>transfer</code>: O(1) – involves a few array accesses, comparisons, and arithmetic operations, all constant time.</li>
<li><code>deposit</code>: O(1) – array access and arithmetic operation.</li>
<li><code>withdraw</code>: O(1) – array access, comparison, and arithmetic operation.</li>
</ul>
</li>
<li><strong>Space Complexity</strong>:<ul>
<li>O(N), where N is the number of accounts, to store the <code>self.accounts</code> list.</li>
</ul>
</li>
</ul>
<hr>
<h3>5. Edge Cases &amp; Correctness</h3>
<ul>
<li><strong>Invalid Account Numbers</strong>: Correctly handled by <code>_is_valid_account</code>. Any account number outside the <code>[1, num_accounts]</code> range will cause the transaction to fail and return <code>False</code>.</li>
<li><strong>Insufficient Funds</strong>: Correctly handled by <code>transfer</code> and <code>withdraw</code>. Transactions attempting to debit more than available will fail and return <code>False</code>.</li>
<li><strong>Transfer to Self (<code>account1 == account2</code>)</strong>: The code correctly handles this. Funds are debited and immediately credited to the same account, resulting in no net change, which is an acceptable behavior.</li>
<li><strong>Empty Initial <code>balance</code> List</strong>: If <code>balance</code> is <code>[]</code>, <code>self.num_accounts</code> will be <code>0</code>. Any attempt to access <code>account - 1</code> or validate an account will correctly fail (e.g., <code>_is_valid_account(1)</code> would be <code>1 &lt;= 1 &lt;= 0</code> which is <code>False</code>).</li>
<li><strong>Zero or Negative <code>money</code></strong>:<ul>
<li>The current implementation <em>does not explicitly prevent</em> zero or negative <code>money</code> values.</li>
<li>If <code>money</code> is <code>0</code>, transactions will appear to succeed but have no effect on balances.</li>
<li>If <code>money</code> is negative:<ul>
<li><code>deposit(account, -X)</code> would effectively become a withdrawal of <code>X</code>.</li>
<li><code>withdraw(account, -X)</code> would effectively become a deposit of <code>X</code>.</li>
<li><code>transfer(acc1, acc2, -X)</code> would effectively transfer <code>X</code> from <code>acc2</code> to <code>acc1</code>.</li>
<li>The <code>if self.accounts[account1 - 1] &lt; money:</code> check for negative money will always pass if the balance is non-negative, as a positive balance is always greater than a negative amount.</li>
</ul>
</li>
<li>This behavior might be unexpected and undesirable for typical banking operations.</li>
</ul>
</li>
</ul>
<hr>
<h3>6. Improvements &amp; Alternatives</h3>
<ul>
<li><strong>Input Validation for <code>money</code></strong>:<ul>
<li>Add checks in <code>transfer</code>, <code>deposit</code>, and <code>withdraw</code> to ensure <code>money &gt; 0</code>. This prevents unintended behavior with zero or negative transaction amounts.</li>
</ul>
</li>
<li><strong>Error Handling with Exceptions</strong>:<ul>
<li>Instead of returning <code>False</code>, raise custom exceptions (e.g., <code>InvalidAccountError</code>, <code>InsufficientFundsError</code>). This provides more specific feedback to the caller, allowing for differentiated error handling.</li>
<li>Example: <code>raise ValueError("Invalid account number")</code> or custom <code>BankError("Insufficient funds")</code>.</li>
</ul>
</li>
<li><strong>Consistent Indexing</strong>:<ul>
<li>Consider making accounts 0-indexed throughout, or explicitly handling the 1-to-0 index conversion at the API boundary (e.g., <code>account_idx = account - 1</code>). This reduces potential off-by-one errors.</li>
</ul>
</li>
<li><strong>Type Hinting</strong>:<ul>
<li>Replace comment-based type hints (<code>:type balance: List[int]</code>) with standard Python type hints (e.g., <code>balance: list[int]</code>, <code>account: int</code>, <code>-&gt; bool</code>). This improves static analysis and readability.</li>
</ul>
</li>
<li><strong>Dynamic Accounts</strong>:<ul>
<li>If the number of accounts needs to change or account IDs are not sequential, a <code>dict</code> (e.g., <code>self.accounts: dict[int, int]</code>) mapping account ID to balance would be a better choice. This would change access to <code>self.accounts.get(account_id, None)</code> or <code>self.accounts[account_id]</code>, but maintain O(1) average time complexity.</li>
</ul>
</li>
<li><strong>Refactor Duplicated Logic</strong>:<ul>
<li>The <code>_is_valid_account</code> check is good. The <code>if self.accounts[account - 1] &lt; money:</code> check is duplicated in <code>transfer</code> and <code>withdraw</code>. This could be extracted into another private helper (e.g., <code>_has_sufficient_funds</code>).</li>
</ul>
</li>
</ul>
<hr>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>Security</strong>:<ul>
<li>This implementation is a basic simulation and has no security features. In a real banking system, robust authentication, authorization, logging, auditing, and input sanitization (if inputs were strings) would be critical. Any external caller can perform any operation without verification.</li>
<li>The current design is susceptible to race conditions in a multi-threaded or concurrent environment. Multiple transactions operating on the same account simultaneously could lead to incorrect balances (e.g., two withdrawals at the same time might both pass the balance check, leading to an overdraft or incorrect final balance).</li>
</ul>
</li>
<li><strong>Performance</strong>:<ul>
<li>The core transaction operations (<code>transfer</code>, <code>deposit</code>, <code>withdraw</code>) are O(1), making them extremely performant for a single-threaded environment.</li>
<li>For very high-throughput scenarios in a concurrent system, the lack of explicit locking or atomic operations would become a significant performance and correctness bottleneck due to data corruption, rather than a slow algorithm.</li>
</ul>
</li>
</ul>


### Code:
```python
class Bank(object):

    def __init__(self, balance):
        """
        :type balance: List[int]
        """
        self.accounts = balance
        self.num_accounts = len(balance)

    def _is_valid_account(self, account):
        """
        Helper to check if an account number is within the valid range (1 to num_accounts).
        """
        return 1 <= account <= self.num_accounts

    def transfer(self, account1, account2, money):
        """
        :type account1: int
        :type account2: int
        :type money: int
        :rtype: bool
        """
        # Check if both account numbers are valid
        if not self._is_valid_account(account1) or not self._is_valid_account(account2):
            return False

        # Check if account1 has sufficient funds
        if self.accounts[account1 - 1] < money:
            return False

        # Perform the transfer
        self.accounts[account1 - 1] -= money
        self.accounts[account2 - 1] += money
        return True

    def deposit(self, account, money):
        """
        :type account: int
        :type money: int
        :rtype: bool
        """
        # Check if the account number is valid
        if not self._is_valid_account(account):
            return False

        # Perform the deposit
        self.accounts[account - 1] += money
        return True

    def withdraw(self, account, money):
        """
        :type account: int
        :type money: int
        :rtype: bool
        """
        # Check if the account number is valid
        if not self._is_valid_account(account):
            return False

        # Check if the account has sufficient funds
        if self.accounts[account - 1] < money:
            return False

        # Perform the withdrawal
        self.accounts[account - 1] -= money
        return True
```
