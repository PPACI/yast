[![Build Status](https://travis-ci.org/PPACI/yase.svg?branch=master)](https://travis-ci.org/PPACI/yase)
# Yase
Yet Another Sequence Encoder - encode sequences to vector of vectors in python !

## Why Yase ?
Yase enable you to encode **any** sequence which can be represented by **string**
to be encoded into a **list of word-vector** representation.

When searching over a tool to encode a sentence as a list of word-vector, it was clear that there was no
simple tool to use. And so, i decided to create Yase.

Note : If you only want to get the word-vector of a word, or average of word-vector in sentence,
you should probably better check [Spacy](https://github.com/explosion/spaCy).

## Requirements
Yase requirements are :
* [numpy](https://github.com/numpy/numpy)
* [tqdm](https://github.com/tqdm/tqdm)

## Mapping file
The mapping should be a columnar file like :
```
<token> <vector value>
token1 0.1 0.6 -1.2
token2 0.6 -2.3 3.4
```

All data should be separated by space, thus no space is allowed in token.
You should be able to directly use [Facebook Fast Text pretrained word vector](https://github.com/facebookresearch/fastText/blob/master/pretrained-vectors.md) as mapping.

## Input file
Input file should be a list of text, with one sample per line.
```
hello world
Yase is awesome !
```
The default separator is a space " " but any regular expression can be provided.

**Note that Yase is case insensitive**

## How to use
yase is command line tool. You can install by with :`pip install git+https://github.com/PPACI/yase.git`

```
>> yase
usage: yase [-h] --input input.txt [--input-encoding UTF8] --output
               output.txt --mapping mapping.vec [--mapping-encoding UTF8]
               [--separator \ |\.|\,] [--no-replace]
               [--cleaning-json cleaning.json]

Yet Another Sequence Encoder

optional arguments:
  -h, --help            show this help message and exit
  --input input.txt     Path to file to encode
  --input-encoding UTF8
                        encoding of input file. UTF8 by default
  --output output.txt   Path to output file
  --mapping mapping.vec
                        Path to mapping file
  --mapping-encoding UTF8
                        encoding of mapping file. UTF8 by default
  --separator \ |\.|\,  regular expression used to split the input sequence
  --no-replace          don't clean input data
  --cleaning-json cleaning.json
                        Path to your own json replacement file for cleaning.
                        Will use the included replacement file otherwise.

```

If you wanted to use the english word vector for an input file like previously described :
```
yase --input "input.txt" --output "output.csv" --mapping "wiki.en.vec" 
```

## Output format
The idea behind yase is to be as easy as possible to integrate it in all data science processing.

Yase output it's your data as **CSV**.

The only problem with CSV is that it's difficult to integrate multi-dimensional array. So we had to find a compromise..

Yase encode the vector columns in JSON format, which is easily readable and is very similar to python array representation.

The output file will be similar to :

|inputs|vectors|
|:----:|:-----:|
|hello world|[[1,1,1],[2,2,2]]
|yase is awesome !|[[3,3,3],[4,4,4]]|

## Cleaning
Yase will **automatically** try to clean your input file by applying regex in the right order.

For example : `Hello I'm yase.Nice to meet you` will magically become `Hello I m yase . Nice to meet you`.

Remember that yase is case insensitive. So yase will understand as `hello i m yase . nice to meet you`.

Lastly, if your mapping doesn't include a mapping for ".", you will obtain vectors for `hello i m yase nice to meet you`

Of course, you can disable this behaviour by providing `--no-replace` argument.

### Providing your own replacement file
You can do this by providing a path to your file with `--cleaning-json`.

The replacement file is a json like :
```json
{
  "\"": "",
  "'": "",
  ",": " , ",
  "\\.": " . ",
  "  ": " "
}
```
Input are regex, so remind to escape . or *. 

Note that replacement are made in the same order as in the json. So here, the first replacement will be to remove "

## How to load a yase output ?

As said previously, the choice made with Yase make it possible to use it as simply as :

```python
import pandas, json

csv = pandas.read_csv("output.csv")
csv.vectors = csv.vectors.apply(json.loads)

csv.head()
```

Note that [Pandas](https://github.com/pandas-dev/pandas) is not mandatory but very recommended for data science.

## TODO

* Optimize Mapping loading time
* Optional argument to output fixed size vectors for all input sequences
* Surely lot of thing !

## Can i contribute ?

Off course ! If you want to improve Yase, your idea / pull requests / issues are welcomed !