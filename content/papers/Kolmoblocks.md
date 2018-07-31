---
title: "Kolmoblocks"
fullTitle: "Kolmoblocks: composability for content distribution based on Merkle DAGs"
name: "Dmitry Borzov"
date: 2018-07-15T18:02:42-04:00
external_link: ""
weight:
draft: false
---
Kolmogorov data blocks (or kolmoblocks) is a data block serialization format for content-
addressable network protocols based on cryptohash naming scheme. Kolmoblocks can be thought
of as scripts for a Turing-complete DSL that take other Merkle data blocks as an input and out-
put the target data block. Its design assures block composability, deterministic reproducibility and
eliminates the problem of serialization format versioning hell.
<!--more-->

## Table Of Content

- [Introduction](#introduction)
- [Background](#background)
  - [Content Addressable Network Protocols](#content-addressable-network-protocols)
  - [Block Composability](#block-composability)
  - [The Great Divide](#the-great-divide)
- [Design](#design)
  - [Blocks as Code](#blocks-as-code)
  - [Kolmoblock Language](#kolmoblock-language)
  - [Performance vs Expressiveness](#performance-vs-expressiveness)
  - [Lambdablocks](#lambdablocks)
  - [Gas](#gas)
  - [Header](#header)
  - [Clients and Servers](#clients-and-servers)
- [Applications](#applications)
  - [Origin Agnostic CDNs](#origin-agnostic-cdns)
  - [A Blockchain to Rule Them All](#a-blockchain-to-rule-them-all)

## Introduction

Content-addressable network protocols based on hash naming scheme (hash-based CANPs), such as IPFS[[1](https://github.com/ipfs/specs)] or dat[[2](https://www.datprotocol.com)], provide unique advantages over traditional hostaddressable network protocols. The nodes of such networks are able to request and retrive content by its cryptographically secure hash from any other peer node.
Current proposals for hash-based CANPs offset their promise with unique challenges:

1. **no composability:** Let’s say the target blob is a slight iteration over already distributed one: it could be just a single bit flip. The contentaddressable network based on hash-naming scheme would treat such a block as a completely new one; there is no capability to signal that a target block can be composed out of the other data blocks.
2. **format versioning hell:** the context of the block’s origination (such as what producer software version was used, the asumptions on the target consumer software and so on) defines the chosen block’s serialization & compression format. Yet, the channels for distribution of both producer and consumer software are usually different from the channels used to distribute/share the data itself. That leads to the unintented effect of non-reusable and non-desirializable data blocks outside of their immediate use.

Kolmoblocks (Kolmogorov data blocks) is a block serialization format that addresses these issues. Kolmoblocks enable free-form block composability and selfdocumentation of the block’s format by design.
Kolmogorov blocks are programming scripts for a special DSL (referred to as the kolmoblock language) that can take other kolmoblocks as an input and output (or ”render”) the target block. Kolmoblock language interpreter specification makes kolmoblocks immutable, reproducable and Turing-complete.

## Background

This section outlines the context behind the problem and the proposition value the kolmoblock design attempts to address. It also denes the terminology used
in the article.

### Content Addressable Network Protocols

The network protocols at the backbone of today’s Internet (IP, TCP, HTTP) are based on the telephony’s model: they enable point-to-point communication across the network. However, the overwhelming share of today’s networks’ bandwidth is dedicated to what can be called content distribution: consumers retrieving identifiable blocks of data (blobs) such as software, text or mutimedia.

Just like the telephone network is a poor vehicle for traditional broadcast content distribution technologies such as cable television, host-addressable nature of the current protocol stack leads to network inefficiencies.

Consider, for example, a situation of several people in a room where each is using their own device to watch the same video. Each device will have to request and download its own copy of the same video from the provider’s data center, as there is no way for the local devices to coordinate and share the retrival of the content.

Another example is a user of a cloud file storage service such as Dropbox trying to access the document she has on her laptop from her phone. It’s reasonable to expect that the requested data will have to make a round trip to a cloud provider’s server on the other side of the Globe even though the source of the data could be less than a meter away from its destination.

Content-addressable network protocols (CANPs) address inefficiencies like these by letting the network nodes request the network by its content, not the hosting provider. Since content consumers only care about what they get, not who they get this from, CANPs provide natural advantages for content distribution.

Different proposals exist for the content naming scheme to be used for CANPs. The naming scheme where the cryptographic hash of data is used to identify content in CANPs (or hash-based CANPs) is an elegant solution that provides some unique advantages over the other proposals:

1. **self-certifying:** Since the content’s authenticity can be checked, consumers don’t need to extend their trust to the nodes they interact with in order to retrieve the content from them
2. **data reuse:** hash-based naming means data’s identity does not include any specifics on its publisher, hash-based naming leads to data reuse by design. For example, common datasets such as the Human Genome Project datasets or the UTF8 codepoint mapping table maybe be shared, published and consumed by multiple applications and agents that may not even be aware of each other’s existence.

Hash-based CANPs are a perfect fit for content distribution.

### Block Composability

Lets look into an example of using hash-based CANPs for distributing a computer operating system (OS) distro (such as Ubuntu Linux). The OS developer publishes the hash of the distribution of the most recent release so that the other network nodes may retrieve it based on this hash from any network peer that is willing to serve it to them.

Now, let’s say, one day a security issue within the OS distribution was identified and the OS developer issues a security update to their OS distribution. They make the necessary fix, distribute the new version and update their published hash of the most recent OS release.

Even though the updated distro can differ from the previous version by a single bit flip, since the cryptographic hash of the new version is completely different, the network would treat the new data block as completely new data.

As a result, a consumer that might have the old version of the OS distribution and would have been able to reproduce the new version by applying a small diff to the version it alreasy has, is forced to download the new version all over again.

To help with situations like these, the network protocol needs to have a mechanism to communicate to the nodes that it is possible to compose a data block from the previosuly distributed blocks.

Within our example, the network peers can be adviced that there are two ways to obtain the new distro’s data block: either download the whole block, or only download the diff (that can be orders of magnitude smaller in size) and apply it to the data block of the old version of the distribution.

With such a feature, the consumers that happen to already have the old version of the distribution would probably opt for only downloading and applying the diff, and the consumers that don’t have the old version (because they joined the network later, for example) would still be able to download the new version as one data block.

Support for being able to convey that data blocks can be composed out of other data blocks will be refered to as block composability.

### The Great Divide

There is a great divide in computing of today: the data and the software to deserialize and interpret it are distributed along two very different channels.

Data only has value if the consumer has access to the software that supports the serialization format used for it. However, installing software is not an easy ordeal as it means user have to trust both the original software developers and the software distribution system they rely upon.

Data and the specification of the serialization format it relies upon needs to be distributed along the same channel.

## Design

This section provides an architectural overview of kolmoblocks.

### Blocks as Code

Let’s choose to take the first 5 digits of SHA256 of data blocks as our illustrative cryptographic hash function. For example, ”ABABABF”, an ASCII-serialized string, can be can identified by its hash **CC646**.

Nodes of the the hash-based CANP network are designed to be able to request their peers for this data block by its hash, and the peer node to send the requested data in response.

Let’s consider the scenario where the serving node sends the requested block to the consumer not in a raw byte form, but as the following dynamic programming language script (say, a Python script):

```python
render = lambda : "ABABABF"
```

The consumer would still be able to to retrieve the requested block sent in such format by:

1. running this block in an interpreter and saving the output into a file
2. validate that the output is indeed the requested data block by checking its hash

Note that sending the data as the programming script only marginally changed the total size of data sent, yet it provides unique capabilities. For example, this script outputs the same target block yet is smaller in size:

```python
render = lambda : "AB"*3 + "F"
```

Sending data as programs that output the target block is the key idea behind the kolmoblocks. Just like in the contrieved example above, the consumers run kolmoblocks in a dedicated sandboxed environment to retrieve the target block and validate its authenticity by comparing the hash.

Now consider distributing the block **F3025:** ”ABABABF-FBABABA”. The following script takes the block **CC646** as a dependancy block and outputs the target block **F3025:**

```python
# kolmoblock
# target block: F3025 #
# dependancy blocks: CC646
def render():
  return dep('CC646') + "-" + dep('CC646')[::-1]
```

Kolmoblocks enable block composability: being able to refer and access other data blocks in its code. Such blocks are referred to as block dependencies and need to be ren-
dered anf evaluated in order for the target block to be rendered.

### Kolmoblock Language

Kolmoblocks cannot be written for an existing general purpose programming interpreter. In order to assure security and reproducability, the kolmoblock interpreter needs to be run in a sandboxed execution environment. Such a domain-specific interpreter and the programming language it relies upon is referred to as **kolmolang** (a Kolmogorov block somain-specific language).
Kolmolang’s domain of use defines the features it needs to have:

1.	**homoiconic macros:** in Kolmoblock language, the code is a just a data structure abstract syntax tree (AST) of the interpreter is the data structure
2.	**determenistic virtual state machine:** kolmoblocks enable determenistic, reproducable rendering across platforms and nodes. Kolmoblock language is parsed in a domain-specific virtual machine, defined by the set of recognizable opcodes

Kolmolang is fundamentally a Turing-complete dynaming programming language of Lisp programming language family [[4](arxiv.org/abs/1608.02621)] which makes it meet these requirements.
Among other things, the design decisions behind kolmoblocks were informed by the lessons from the other domain specific virtual machine environments: Javascript,
JVM, Webassembly and Ethereum Virtual Machine.

### Performance vs Expressiveness

Among other things, the design decisions behind kolmoblocks were informed by the lessons from the other domain specific virtual machine environments: Javascript,
JVM, Webassembly and Ethereum Virtual Machine.

### Lambdablocks

So far, we have introduced kolmolang-based programs in the context of kolmoblocks, the point o which was to encode target blocks. We can also have general purpose kolmolang code in the rendered (target) data blocks as well.

Let’s call data blocks that have kolmolang cpode lambdablocks to highlight their properties functional programming. Just like the namesake concept of lambda functions, lambdablocks can be considered anonymous in the sense that their identifier (the cryptohash of the code) is based on what the code does, that is on its essense, not
the context it was created for.
The following lambdablock contains a function that returns n-th Fibonacci number:

```python
# lambdablock 509ED
def f(n):
  if n == 0: return 0
  elif n == 1: return 1
  else: return f(n-1)+f(n-2)
```

Lambdablock code can be imported and executed within kolmoblocks. This lets us separate and reuse decoding code.
As an example, consider the kolmoblock in which the data is compressed with Huffman encoding:

```python
# kolmoblock
# target block: F3025
# dependancy blocks: 77650
def render():
  huffman_decode = eval(dep(’77650’))
  huffman_tree = [’AB’,
          [’BA’,
              [’F’, ’-’]
          ]
      ]
encoded = 0b000110111110101010
return huffman_decode(huffman_tree,encoded)
```

This kolmoblock imports and calls the lambdablock that contains the decoding logic:

```python
# lambdablock 77650
def f(huffman_tree, encoded_string):
  output = ’’
  cur = 0
  while (cur < len(encoded_string)):
    node = huffman_tree
    while type(node) is list:
      bit = encoded_string[cur]
      cur += 1
      node = node[0] if bit else node[1]
    output += node
  return output
```

### Gas

Turing completeness of the kolmoblock language leads to an issue of the halting problem: a malicious kolmoblock may overload the block renderer by making it run in an infinite loop.

To address this issue, the kolmoblock’s header is required to specify block’s ”gas” value: a metric on the computational resources required to render the block. The kolmoblock will be discarded and ignored by the block renderer if it is found to exceed it’s advertised gas limit.

### Header

Kolmoblock’s header contains metadata the consumers need for an informed decision on whether they should retrieve and render it:

1.	**hash function lambdablock** specifying the cryptographic hash function used to calculate the data block’s target hash
2.	**hash of the target data block:** the hash of the target data block that kolmoblock outputs if rendered. Note that multiple kolmoblocks can have the same target data block.
3.	**size** of the kolmoblock
4.	**list of hashes of dependancy data blocks:** the hashes of data blocks that are needed for the kolmoblock rendering.
5.	**gas:** an advertised metric on the computational resources needed to render the block
6.	**cryptographic signature:** of the block producer

When a kolmoblock-based CANP node expresses an interest in a data block, the peers only return headers of the kolmoblocks that have that data block as a target. The requesting node is then able to use the header metadata to make a decision on which kolmoblock it would choose to pursue by an algorithm such as the following:

```python
def evaluate_rendering_cost_of(kolmoblock):
  total_gas = 0
  total_bandwidth = 0
  if is_rendered(kolmoblock):
    return total_gas, total_bandwidth
  total_gas += kolmoblock.gas
  if not is_fetched(kolmoblock):
      total_bandwidth += kolmoblock.size
  for dep in kolmoblock.dependencies:
    dep_gas, dep_band = evaluate_rendering_cost_of(kolmoblock)
    total_gas += dep_gas
    dep_band += dep_band
  return total_gas, total_bandwidth
```

Kolmoblock header can have an invalid information, whether due to malicious intent or an error. It is in the best interest of kolmoblock servers and consumers to **validate** all kolmoblocks they handle and discard and ignore the invalid ones.
Validating a kolmoblock includes:

1.	running the block and validating that the output has the advertised target hash
2.	making sure that the advertised gas limit is not exceeded

### Clients and Servers

While specifying what kolmoblock-based CANPs might look like is outside of the scope of this paper, it is illustrative to think through some of the practices networks that use kolmoblocks might adopt.
Let’s say a kolmoblock-based CANP client node is interested in fetching a data block. It announces its interest to the other nodes and gets a list of kolmoblocks with the data block of interest as the target block. The client can make an estimate of the cost of obtaining and rendering each of the available komoblocks. An example algorithm for that is outlined at Fig. 1. And then optimize to fetch and render the kolmoblock that would take the least of resources to retrieve and render.
With cryptohash-addressable naming, the same data can be shared under different blocks due to being partitioned into blocks differently.Consider an example illustated in the following figure, the text of a novel is distributed both as one block, **OC71D** and as 3 separate blocks for each book (**CA590, DDAA0, A099B**).

![Fig.2](/img/Kolmoblocks/fig2.PNG)

That leads to redundancy and data duplication. A kolmoblock-based server may address this by doing **compactification:** composing new, more efficient kolmoblocks that are still able to reproduce all the target blocks. The quoted kolmoblock makes having to store **OC71D** in a raw format unnecessary.

## Applications

In this section, we go through potential applications of kolmoblocks.

### Origin Agnostic CDNs

Adoption of technologies whose value is based on network effect have an inherent chicken-and-egg problem to it. Early adopters tend to see little value in it since its support by other network nodes is minimal. For technologies of this nature to be succesful, it is important to identify an application where the adopters get value even with the limited adoption of the technology by the other agents. For kolmoblocks, origin-agnostic CDNs can be just that.

Both content providers and ISPs are incentivized to have CDN arrangements. IPSs would like to cache as much content as they can within their network to lower their transit costs. Content providers, on their end, want to lower their hosting costs, and both parties are interested in lower latency and better experience for customers.

ISPs can’t just start caching all the traffic on their side:

1. TLS-based encryption is widely adopted as the best practice to certify the content’s origin and authenticity to the customer and leads to ISPs only seeing encrypted traffic;
2. it is hard to solve the cache expiration problem generally, without being able to make any assumptions about the content provider

As a result, ISPs only are able to cache content from the providers they have custom arrangements with (such as a contractual relationship), either direct ones, or via a ternary party, such as a CDN company. Setting up and managing an arrangement like this usually comes with overhead costs (legal, engineering, managing relationships with mediatory parties).

If we are to take the total number of Internet Autonomous Systems as a proxy for the number of ISPs accross the world (in tens of thousands), that are spread across juristictions and estimate the content producers for which caching content would have brought considerable advantage by Alexa’s list (500), we see that solving this with the current approach would lead to having to maintain an order of 10,000,000 contractual relationships. It is easy to see that this approach does not scale. Lets see how kolmoblock-based CDN would be able to address this situation.

As an example, consider the content producer (let’s call them **example-video-site.com**) setting up the following services:

1.	a Kolmoblock-based CANP server that serves the hosted content to other Kolmoblock-based CANP peers
2.	setting up an HTTPS gateway to their content where the hosted data blocks are identified by their hash-based ids as part of the url. For example, <https://kolmoblocks.examplevideo-site.com/CC646> would serve the **CC646** data block

This setup can be announced to the network with DNS records like this:

```sh
_kolmoblocks_.tcp.example-video-site.com. IN URI 10 1 "https://kolmoblocks.example-video-site.com/<ID>"
_kolmoblocks._udp.example-video-site.com. IN URI 10 1 "kolmoblocks://<ID>"
```

The content provider’s dynamic site may refer to kolmoblocks via its HTTPS gateway link so ISPs and customers see it just as another HTTP endpoint.

However, an CDN server controlled by the ISP would be able to identify this content provider supports kolmoblocks. It would be able to overwrite the DNS record for the content provider’s kolmoblock gateway to point at its own kolmoblock gateway, retrieve the requested kolmoblocks from the content provider using a kolmoblockbased CANP, and serve the cached content to the user.

Kolmoblocks enable parties to be able to cache content without having to maintain a direct relationship between each other.

## A Blockchain to Rule Them All

Recent years witnessed an explosion in interest and activity in systems based on the Merkle DAG data structure, also refered to as the blockchain technology. Some
examples of designs with wider public adoption are git, IPFS, Bitcoin, Ethereum.

At the core of these technologies is one global cryptohash-based address space. Yet the systems are not interoperatable due to the variance in the format used for Merkle links. As an example, Bitcoin blocks refer to other blocks with hex-encoded SHA-256, and git hashing function can be described by the following lambdablock:

```python
# lambdablock
def hash_function(block):
  calculate_sha1 = eval(dep(’BD734’))
  prefix = "commit %s\0" % len(block)
  return calculate_sha1(prefix + block)
```

That is, git prepends the blob with the string **"commit"**, decimal-encoded size of the blob in bytes and the NUL byte and then takes an SHA1 hash of it.

This variance leads to the same content named and referenced in different formats across Merkle DAG-based
systems.

There is value in making Merkle links interoperatable across technologies. As an example, you would be able to reference a blockchain transaction in your git commit. It open even more possibilities with Ethereum dapps where you can link to the content stored elsewhere (be that IPFS or other cryptocurrencies) in your smart contracts.

To enable Merkle Link interoperability, Protocol Labs proposed IPLD, a universal content identifier format that supports multiple cryptohash formats used across systems. Adopters of IPLD are able to . Yet IPLD cannot be adopted retroactively to existing Merkle DAG blocks since changing the hash serialization format used in Merkle link changes the hash of the block itself which leads to breaking any existing Merkle links that are used to refer to this block itself.

Kolmoblocks allow you retroactively include any Merkle DAG blocks without breaking their links.

To see how this is possible, consider the following git commit object:

```sh
$ git cat-file commit d6cd1e2bd19e03a81 tree
9bedf67800b292398 parent de1eaf515ebea46de
```

This git object contains multiple Merkle links in git hashing format: the **tree** link refers to the git object that point to the root of the file directory descriptor, and **parent** refers to the parent commit.

We would like to convert these Merkle links to a more universal format, such as the IPLD one to make these links universal and interoperatable. For illustrative purposes, lets the format of our choice is we we prefix the git links with `git-link:`

```sh
tree git-link:9bedf67800b292398
parent git-link:9bedf67800b292398
```

Now we can make the block have the original hash with just a little change to the hash function lambdablock:

```python
# lambdablock
def hash_function(block):
  git_formatted = block.replace(’git-link:’, ’’)
  calculate_sha1 = eval(dep(’BD734’))
  prefix = "commit %s\0" % len(git_formatted)
  return calculate_sha1(prefix + git_formatted)
```
