It's being already more than four months working full-time in Javascript and I miss already my time creating Python scripts.

Today I decided to break with it, and build with Python a simple implementation of a [Blockchain](https://en.wikipedia.org/wiki/Blockchain) that should help me to dive again into basic Python concepts.

Don't be scared if you don't have any experience with the topic. Actually, the concept behind Blockchain is pretty simple and it will only take just a few lines to build our simple 'not-decentralized version'.


# What is Blockchain?

Quoting Wikipedia, Blockchain is *a continuously growing list of records, called blocks, which are linked and secured using cryptography. Each block typically contains a hash pointer as a link to a previous block, a timestamp and transaction data. By design, blockchains are inherently resistant to modification of the data*.

The brain behind this concept is the person or group of people well-known by the pseudonym of [Satoshi Nakamoto](https://en.wikipedia.org/wiki/Satoshi_Nakamoto). But since then, Blockchain has evolved into something greater that has the potential to transform many industries and our daily lives.

Let's not focus on the potential of the Blockchain and directly jump into one of the most important concepts you need to know to understand the technology, **hash functions**.

## Cryptographic Hash Functions

A hash function is a function that takes a particular input - typically called message, and applies mathematical transformations that finishes with a returning output - typically called digest, tag or hash.

You can hash any kind of data to create the digital signature using any of the long list of existing possible functions like md5, sha256 or bcrypt ([please use bcrypt!!](https://medium.com/@danboterhoven/why-you-should-use-bcrypt-to-hash-passwords-af330100b861)).


# Blocks

For our Blockchain implementation, each block will contain a list of transactions, the creation time, the hash value of the previous block, and the actual hash of the block.

The key resides on the way the new hash for the block is created, as it assures that the chain of blocks is consistent and unbreakable. For each new block in the chain, the new hash will use as a message or input the combination of the transaction list and the previous hash block.

That means that if something change in some earlier block, the consecutive hashes (or digital signatures) of the next blocks should change or the chain will become inconsistent.

```python
import hashlib
import datetime

class Block():
    previous_hash = ""
    transactions = []
    time = ""
    block_hash = ""

    def __init__(self, prev_hash, transaction_list):
        self.transactions = transaction_list
        self.previous_hash = prev_hash
        messages = [self._hash(x) for x in transaction_list] + [prev_hash]
        self.block_hash = self._hash("".join(messages))
        self.time = str(datetime.datetime.now())

    def _hash(self, string):
        return hashlib.sha256(string).hexdigest()
```

# Our Blockchain

Our Blockchain class will contain the list of blocks in the chain and the representation of the last block appended in the list (only used to quickly retrieve the last hash value for the creation of the possible new entry block in the chain).

In addition, during the class initialization we will need to create a hardcoded block also called the **genesis block**, which is the first block in the chain that has no previous block.

The transactions in our Blockchain will be simple informative strings.

```python
class Blockchain():
    block_list = []
    last_block = None

    def __init__(self):
        # needed an initial block
        genesis_transactions = ["satoshi sent 2BTC to jose"]
        genesis_block = Block('', genesis_transactions)
        self.last_block = genesis_block
        self.block_list.append(genesis_block)
        print('Blockchain created with genesis_block hash', genesis_block.block_hash)
        print('Blockchain size', len(self.block_list))

    def add_block(self, transactions):
        block = Block(self.last_block.block_hash, transactions)
        self.last_block = block
        self.block_list.append(block)

    # static method, you can call it without initializing the class
    # in order to get info about the possible hash algorithms
    def available_algorithms():
        print('available:')
        print(hashlib.algorithms_available) # algorithms present in the module and OpenSSI
        print('guaranteed:')
        print(hashlib.algorithms_guaranteed) # algorithms present in the module
```

To test our *pseudo Blockchain*:

```python
bc = Blockchain() # inheritance of class Blockchain
bc.add_block(["jose sent 1BTC to evan", "evan sent 0.5BTC to satoshi"])
print("lenght of the chain: ")
print(len(bc.block_list) - 1) # minus one because of the genesis_block
print(bc)
```

# Improve our class: special methods

My friend [@eliasdorneles](https://twitter.com/eliasdorneles) introduced to me how cool and potential are the special methods or magic functions for the Python Data Model by reading the first chapter of the book [Fluent Python](http://shop.oreilly.com/product/0636920032519.do) - which I strongly recommend to read if you are a Python newbie.

To summarize the chapter in just one sentence, we use magic functions to redefine those operations in Python that were generic for a group of types and which were intended to work even for objects that didn’t have methods at all.

For example, we want that our Blockchain class acts like a dictionary, but we don't want to *delete* and *set* from created blocks, we just want to access to the blocks by index, and be able to retrieve the length of the chain by calling the built-in function len(). In this case, by just defining the function *__len__()* and *__getitem__(key)* we can achieve our purpose -  no need in this case to define *__setitem__(key, value)* or *__delitem__(key)*.

In addition, we note that when running the previous code the output of the last *print(bc)* returns an ugly *<__main__.Blockchain instance at 0x7f9a10481200>*. We also want to make the string conversion of the class more meaningful for the users when the function *print* is called. By just defining the function *__str__()* we can achieve our purpose.

```python
class Blockchain():
    block_list = []
    last_block = None

    def __init__(self):
        # needed an initial block
        genesis_transactions = ["satoshi sent 2BTC to jose"]
        genesis_block = Block('', genesis_transactions)
        self.last_block = genesis_block
        self.block_list.append(genesis_block)
        print('Blockchain created with genesis_block hash', genesis_block.block_hash)
        print('Blockchain size', len(self.block_list))

    def __getitem__(self, index):
        return self.block_list[index + 1]

    def __len__(self):
        return len(self.block_list) - 1

    def __str__(self):
        # multi-line
        message = """Total of {lenght} block(s)
            Last block:
            - transactions: {txs}
            - hash: {hash}
            """
        return message.format(lenght = str(len(self)), txs = str(self.previous_block.transactions), hash = str(self.previous_block.block_hash))

    def add_block(self, transactions):
        block = Block(self.last_block.block_hash, transactions)
        self.last_block = block
        self.block_list.append(block)
```

Now when the execute the script, we can access to the blocks in our Blockchain by index, get the real length of the chain by using the *len()* function, or the *print(bc)* will output in the console something as nice as:

```python
Total of 1 block(s)
Last block:
- transactions: ['jose sent 1BTC to evan', 'evan sent 0.5BTC to satoshi']
- hash: c6d52d5840b31cc4f79b6aca78acfa59c7507a92db20f667b2ce23befcdd8850
```

