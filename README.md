# blockchain-experiment
This project is help those who want to learn how to do blockchain

The Genesis Block
Before we build the first block, we need to create a class for all of blocks to abide by. It should have the attributes:

Index – it’s position in the blockchain
Previous Hash – the hash of the block that came before the current block
Timestamp – the time the block was created
Data – the information (e.g. transactions) that the block carries
Hash – the hash of the block itself.

We implement it like this:

class Block:
    def __init__(self, index, previousHash, timestamp, data, currentHash):
        self.index = index
        self.previousHash = previousHash
        self.timestamp = timestamp
        self.data = data
        self.currentHash = currentHash
        
 Now we can create our first block! This is commonly referred to as the genesis block:

def getGenesisBlock():
    return Block(0, '0', '1496518102.896031', "My very first block :)", '0q23nfa0se8fhPH234hnjldapjfasdfansdf23')

blockchain = [getGenesisBlock()]

Your genesis block doesn’t have to be exactly like this. The only requirements are that the genesis has index of 0 and the timestamp is less than the current time. The previous has doesn’t matter, data can be anything you want, and we’ll learn about hashing next!

Hashing Blocks
Now that we have our first block, how do we get a hash for it? Python has a library which includes SHA256 – the hashing algorithm that Bitcoin and others use. To install this package, go to the command line and enter:

pip install hashlib
We will create two functions for ease of use, but only the first is actually necessary – calculateHash and calculateHashForBlock. The second function depends on the first, but sometimes it’s easier to enter a block and get the hash.

def calculateHash(index, previousHash, timestamp, data):
    value = str(index) + str(previousHash) + str(timestamp) + str(data)
    sha = hashlib.sha256(value.encode('utf-8'))
    return str(sha.hexdigest())

def calculateHashForBlock(block):
    return calculateHash(block.index, block.previousHash, block.timestamp, block.data)
We essentially convert all of the block information into one large string. Then we encode it, and finally hash it with hashlib.sha256(). 

Creating New Blocks
Now that we have our first block and can hash blocks, we need a function to add blocks to the blockchain. This will incorporate a function that can retrieve our latest block.

import time

def getLatestBlock():
    return blockchain[len(blockchain)-1]

def generateNextBlock(blockData):
    previousBlock = getLatestBlock()
    nextIndex = previousBlock.index + 1
    nextTimestamp = time.time()
    nextHash = calculateHash(nextIndex, previousBlock.currentHash, nextTimestamp, blockData)
    return Block(nextIndex, previousBlock.currentHash, nextTimestamp, nextHash)
Validation
So far we can:

Create a genesis block
Hash blocks
Create additional blocks
But how can we be sure these blocks are legitimate? And what about checking the entire chain? Let’s start with a function that checks if two blocks are the same (to make sure no one alters the genesis block):

def isSameBlock(block1, block2):
    if block1.index != block2.index:
        return False
    elif block1.previousHash != block2.previousHash:
        return False
    elif block1.timestamp != block2.timestamp:
        return False
    elif block1.data != block2.data:
        return False
    elif block1.currentHash != block2.currentHash:
        return False
    return True
Now we can write a function that takes the previous block and the new block as inputs. It determines if the block is valid based on hashes and indices matching up:

def isValidNewBlock(newBlock, previousBlock):
    if previousBlock.index + 1 != newBlock.index:
        print('Indices Do Not Match Up')
        return False
    elif previousBlock.currentHash != newBlock.previousHash:
        print("Previous hash does not match")
        return False
    elif calculateHashForBlock(newBlock) != newBlock.hash:
        print("Hash is invalid")
        return False
    return True
Using the two functions above, we can check the validity of an entire chain by iterating over the entire list in a for loop:

def isValidChain(bcToValidate):
    if not isSameBlock(bcToValidate[0], getGenesisBlock()):
        print('Genesis Block Incorrect')
        return False
    
    tempBlocks = [bcToValidate[0]]
    for i in range(1, len(bcToValidate)):
        if isValidNewBlock(bcToValidate[i], tempBlocks[i-1]):
            tempBlocks.append(bcToValidate[i])
        else:
            return False
    return True
Now we can check our chain!

Putting it together
All the code together looks like:

 
import hashlib
import time

class Block:
    def __init__(self, index, previousHash, timestamp, data, currentHash):
        self.index = index
        self.previousHash = previousHash
        self.timestamp = timestamp
        self.data = data
        self.currentHash = currentHash

def getGenesisBlock():
    return Block(0, '0', '1496518102.896031', "My very first block :)", '0q23nfa0se8fhPH234hnjldapjfasdfansdf23')

blockchain = [getGenesisBlock()]

def calculateHash(index, previousHash, timestamp, data):
    value = str(index) + str(previousHash) + str(timestamp) + str(data)
    sha = hashlib.sha256(value.encode('utf-8'))
    return str(sha.hexdigest())

def calculateHashForBlock(block):
    return calculateHash(block.index, block.previousHash, block.timestamp, block.data)

def getLatestBlock():
    return blockchain[len(blockchain)-1]

def generateNextBlock(blockData):
    previousBlock = getLatestBlock()
    nextIndex = previousBlock.index + 1
    nextTimestamp = time.time()
    nextHash = calculateHash(nextIndex, previousBlock.currentHash, nextTimestamp, blockData)
    return Block(nextIndex, previousBlock.currentHash, nextTimestamp, nextHash)

def isSameBlock(block1, block2):
    if block1.index != block2.index:
        return False
    elif block1.previousHash != block2.previousHash:
        return False
    elif block1.timestamp != block2.timestamp:
        return False
    elif block1.data != block2.data:
        return False
    elif block1.currentHash != block2.currentHash:
        return False
    return True

def isValidNewBlock(newBlock, previousBlock):
    if previousBlock.index + 1 != newBlock.index:
        print('Indices Do Not Match Up')
        return False
    elif previousBlock.currentHash != newBlock.previousHash:
        print("Previous hash does not match")
        return False
    elif calculateHashForBlock(newBlock) != newBlock.hash:
        print("Hash is invalid")
        return False
    return True

def isValidChain(bcToValidate):
    if not isSameBlock(bcToValidate[0], getGenesisBlock()):
        print('Genesis Block Incorrect')
        return False
    
    tempBlocks = [bcToValidate[0]]
    for i in range(1, len(bcToValidate)):
        if isValidNewBlock(bcToValidate[i], tempBlocks[i-1]):
            tempBlocks.append(bcToValidate[i])
        else:
            return False
    return True
