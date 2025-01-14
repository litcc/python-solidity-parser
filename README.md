# python-solidity-parser
An experimental Solidity parser for Python built on top of a robust ANTLR4 grammar.

**ⓘ** This is a **python3** port of the [javascript antlr parser](https://github.com/federicobond/solidity-parser-antlr) maintained by [@federicobond](https://github.com/federicobond/). Interfaces are intentionally following the javascript implementation and are therefore not pep8 compliant.



## Install

```
#> pip3 install solidity_parser
#> python3 -m solidity_parser <parse|outline> <path_to_contract.sol>   # print parse tree or sourceUnit outline
```

## HowTo

```python

import sys
import pprint

from solidity_parser import parser

sourceUnit = parser.parse_file(sys.argv[1], loc=False) # loc=True -> add location information to ast nodes
pprint.pprint(sourceUnit)  
# see output below

```

output:
````
{'type': 'SourceUnit',
 'children': [{'type': 'PragmaDirective',
               'name': 'solidity',
               'value': '^0.4.22'},
              {'type': 'ContractDefinition'},
               'baseContracts': [],
               'kind': 'contract',
               'name': 'SimpleAuction',
               'subNodes': [{'initialValue': None,
                             'type': 'StateVariableDeclaration',
                             'variables': [{'expression': None,
                                            'isDeclaredConst': False,
                                            'isIndexed': False,
                                            'isStateVar': True,
                                            'name': 'beneficiary',
                                            'type': 'VariableDeclaration',
                                            'typeName': {'name': 'address',
                                                         'type': 'ElementaryTypeName'},
                                            'visibility': 'public'}]},
...
````

### Nodes

Parse-tree nodes can be accessed both like dictionaries or via object attributes. Nodes always carry a `type` field to hint the type of AST node. The start node is always of type `sourceUnit`.

## Accessing AST items in an Object Oriented fashion

```python
# ... continuing from previous snippet

# subparse into objects for nicer interfaces:
# create a nested object structure from AST
sourceUnitObject = parser.objectify(sourceUnit)

# access imports, contracts, functions, ...  (see outline example in __main__.py)
sourceUnitObject.imports  # []
sourceUnitObject.pragmas  # []
sourceUnitObject.contracts.keys()  # get all contract names
sourceUnitObject.contracts["contractName"].functions.keys()  # get all functions in contract: "contractName"
sourceUnitObject.contracts["contractName"].functions["myFunction"].visibility  # get "myFunction"s visibility (or stateMutability)
```


## Generate the parser

Update the grammar in `./solidity-antlr4/Solidity.g4` and run the antlr generator script to create the parser classes in `solidity_parser/solidity_antlr4`.
```
#> bash script/antlr4.sh
```



## 更新核心库

下载Solidity.g4文件：
https://github.com/antlr/grammars-v4/tree/master/solidity
该文件定义最新Solidity语法

安装antlr4:
https://github.com/antlr/antlr4/blob/master/doc/getting-started.md

生成python目标

在Solidity.g4文件的指定目录下运行

需要使用 java11

```python -D
antlr4 -Dlanguage=Python3 -visitor Solidity.g4
venv/bin/antlr4 -Dlanguage=Python3 -visitor solidity-antlr4/Solidity.g4 -o solidity_parser/solidity_antlr4/
```
即可生成相关目标文件，将生成的文件替换掉 src/solidity_parser/solidity_antlr4下的所有文件




