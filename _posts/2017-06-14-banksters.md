Next months I will spend plenty of time developing a [Bitcoin](https://en.wikipedia.org/wiki/Bitcoin) wallet - a web/desktop application where users will be able to store their Bitcoins, make transfers and payments.

Recently after being involved in this new project some of my friends asked me many questions regarding Bitcoin and block chain. I am not an expert in the area and, to be honest, before jumping into this project I was not very interested in the topic. To be able to respond in the best possible way to all the questions, I've started to read parts of [Satoshi Nakamoto](https://en.wikipedia.org/wiki/Satoshi_Nakamoto)'s Bitcoin [reference implementation](https://bitcoin.org/en/bitcoin-paper) and I feel fascinated with it.

On top of that, last week the Spanish ["Banco Popular" was sold to Banco Santander for just one Euro](https://econews.pt/2017/06/07/santander-acquires-banco-popular-for-one-euro/); a process where some people lost all their savings due to *Banco Popular*'s stock market shares value dropped to zero after the acquisition. It breaks my heart when I see on the news elderly explaining how they have lost all their savings after trusting the bankers of *Popular*'s offices on investing all their savings in their shares.

I consider this situation horrible even more when the main responsible for the *Popular*'s collapse and rescue, the ex-president Emilio Saracho, is leaving the bank with a payment of 1,5 million € just for his short four months of service in the entity. Concern about this situation and the term **bankster** (banker + gangster) is the order of the day in Spain.

So yes, I am in part a big supporter of the Bitcoin idea and I am also happy with this new project not because of the complexity of the project, but because of the meaning and philosophy behind Bitcoin. Banks are evil and trade on temptation, it is hard to know which kind of use banks do with the money you deposit - although it exists some alternatives, i.e. [this one](https://www.triodos.es/es/conozca-triodos-bank/que-hacemos/) in Spain.


# Ok dude, but what is a Bitcoin?

Well, a Bitcoin is a cryptocurrency that instead of having to be printed like normal money it needs to be mined - a process of adding transaction records to Bitcoin's shared public ledger of past transactions, also called **block chain**.

Bitcoin exists and works because the people who use it have faith that other people also trust the system. One goal of the Bitcoin is to avoid any centralized control as mentioned before, so every participant has access to the public ledger (block chain) and maintains their own copy of the ledger. Bitcoin network relies on block chain. All the confirmed transactions are included in block chain, the integrity and the chronological order of the block chain are enforced with cryptography.

One surprising consequence of this philosophy is that from the ledger everyone can see everyone else's balances (system uses account numbers instead of names). When there is a new transaction the user broadcasts the message, so everyone across the entire world updates their ledger. On the other hand, you can also use the system to send and receive money without the need of maintaining a ledger.

The way to avoid that other users spend your money is by cryptography: when a new account number is created, it comes along with a private key mathematically linked. Every transaction message is encrypted with the private key of the user resulting in the creation of a signature. Another function allows other people to check the signature, making sure that it applies to that specific transaction.

The control of the order in transactions is critical, as network delays may cause transactions arrive in different orders at different places. The way to avoid those problems is by first locating those transactions in a pool of new/pending transactions, and from there they will be sorted to a giant chain that locks in their order.

To select which transaction is next, participants select a pending transaction of their choice and begin trying to solve a special problem (cryptographic hash) that will link to the end of the chain. The first person to find a solution wins, and gets to have their transaction selected as the next in the chain.

Every time someone wins the lottery to pick the next transaction in the chain, new bitcoins are created out and awarded to the account of the winner, which is a convenient way to randomly distribute money into the world. Bitcoin has a limit of creation, in a hundred of years no more money will be created and participants will only be paid from fees added on to transactions.


# Conclusion

This is a brief way to explain what is Bitcoin in general. If you still think how come something like a Bitcoin (just bits of raw data) can have some value; you need to think that in fact, it has no difference with the money you can have in your wallet as it has no intrinsic value. At the end, real money is just paper or numbers in your bank account.

Bitcoin in some way represents a democratization form of money. As it is decentralized, which means no bank or government controls it, only the users are the ones controlling it, and Bitcoins can be sent directly from user to user without any fee from banks, credit card companies or other intermediaries.

