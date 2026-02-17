---
title: 'ValueType Boxing'
date: Tue, 08 Mar 2005 00:49:21 +0000
draft: false
tags: ['C#', '.NET']
---

In .NET an interesting phehomenon occurs known as "boxing". Let me illustrate the problem and then we will look at the possible solutions:

```csharp
using System;
using System.Collections;

class Test {
	struct BankAccount { /* A simple BankAccount structure */
		public float Balance; /* Balance in the account */
		public BankAccount(float _Balance) { /* overloaded-contructor */
			Balance = _Balance;
		}
		/* A method to charge the account */
		public void ChargeAccount(float Fee) { 
			Balance-=Fee;
		}
	}

	public static void Main() {
		ArrayList Accounts = new ArrayList(); /* An array */

		Accounts.Add(new BankAccount(100)); 
		Accounts.Add(new BankAccount(200)); 	  
		Accounts.Add(new BankAccount(300)); 	

		/* Line: 21 */
		/* Go through each account and ChargeAccount 5.0 */
		foreach(BankAccount Account in Accounts) {
			Account.ChargeAccount((float)5.0);
			Console.WriteLine("Boxed: " + Account.Balance);
		}

		/* Go through each account and print the balance */
		foreach(BankAccount Account in Accounts) {
			Console.WriteLine("Un-boxed: " + Account.Balance);
		}
	}
} 
```

The program outputs the following:

```bash
Boxed: 95
Boxed: 195
Boxed: 295
Un-boxed: 100
Un-boxed: 200
Un-boxed: 300 
```

Although you would most likely expect (or at least want) to get the following output:

```bash
Boxed: 95
Boxed: 195
Boxed: 295
Un-boxed: 95
Un-boxed: 195
Un-boxed: 295 
```

So what happened? If you look at line 21, we assumed that Account was a reference copy to Accounts which it isn't. Account actually is a value-type (copied) value for each Accounts. And that's why when you do a Account.ChargeAccount the copied Account is charged and therefore you see the change only in the first foreach loop. In the second foreach loop you once again start referring to the original Accounts values which was 100,200,300 respectively.

To solve the problem you have to reference the actual object when doing ChargeAccount, something similar to this:

```csharp
using System;
using System.Collections;

class Test {
	struct BankAccount {
		public float Balance;
		public BankAccount(float _Balance) {
			Balance = _Balance;
		}
		public void ChargeAccount(float Fee) {
			Balance-=Fee;
		}
	}

	public static void Main() {
		BankAccount[] Accounts = new BankAccount[3];
		Accounts[0] = new BankAccount(100);
		Accounts[1] = new BankAccount(200);
		Accounts[2] = new BankAccount(300);
	
		for(int i=0;i<accounts.Length;++i) {
			Accounts[i].ChargeAccount((float)5.0);
			Console.WriteLine("Boxed: " + Accounts[i].Balance);
		}

		for(int i=0;i<accounts.Length;++i) {
			Console.WriteLine("Un-Boxed: " + Accounts[i].Balance);
		}
	}
} 
```

Notice however, this requires you to give a size of the array prior to using the array. This might be an inconvenience and there is a way to solve that problem also. We'll look into that next time.