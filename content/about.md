---
title: "About"
date: 2018-07-19T18:35:05-04:00
draft: false
---

In kolmoblocks, data chunks (or data blocks) are identified by their cryptographically strong hashes. For example, ”ABABABF”, an ASCII-serialized string, can be can identified by its SHA-256: **CC6460622F8AFE1A4DC7A92E34D53709115B0E39CCC3F323A35FA5965DB006D6** (we will contactinate hashes in the examples to only the first 5 digits of its hex from here to keep it sane).

Such a naming scheme provides you with the global address space of data, where data block's id only depends on the data itself, not where it came from.

One can note that such a naming scheme was implemented in [IPFS](ipfs.io) or [git](https://git-scm.com/).

The key idea of kolmoblocks is to combine the hash-based naming scheme with the concept of sending data as programs that output that data.

For example, lets say you want to send someone that example string from above, ”ABABABF”. You can send instead a program that outputs that string:

```python
def render():
    return "ABABABF"
```
The consumer would be able to to retrieve the requested block sent in such format by:

1. running this block in an interpreter and saving the output into a file
2. validate that the output is indeed the requested data block by checking its hash

Sending data as the programs only marginally changes the total size of data sent, yet it provides unique capabilities. For example, this script outputs the same target block yet is smaller in size:

```python
def render():
    return "AB"*3 + "F"
```

Now lets make it possible to reference and "import" other data blocks by their hash in such programs. 

Consider distributing a data block **F3025:**: ”ABABABF-FBABABA”. The following script takes the block **CC646** as a dependancy block and outputs the target block **F3025:**

```python
# kolmoblock
# target block: F3025 #
# dependancy blocks: CC646
def render():
  return dep('CC646') + "-" + dep('CC646')[::-1]
```

You could not just import raw data, but also "code" from the "codeblocks", the ones that contain the algorithms/logic.

Consider a codeblock that contains Huffman-encoding decoding function:
```python
# codeblock 77650
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
We can reference and "import" that function in our kolmoblock for the same "ABABABF-FBABABA" string encoded with the Huffman tree:
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

More details can be found in the [documentation section](/documentation).
