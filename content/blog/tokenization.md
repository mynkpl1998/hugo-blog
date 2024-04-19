+++
title = 'Tokenization'
date = 2024-04-19T10:16:36-07:00
draft = false
+++

### What is tokenization ?

Machine learning models operate on numerical representations of input data. Language models, such as LLMs, work with text input.
Tokenization is the process of converting the text into smaller chunks called tokens, which are then assigned a numerical representations. 

Broadly there are three types of tokenization we can do:

1. **Character Tokenization**: The text is broken down into characters, each character including whitespace and punctuation becomes a token. For example, 'hello' would be tokenized as ['h', 'e', 'l', 'l', 'o'].

2. **Word Tokenization**: In this method, the text is broken down into words. Each word becomes a token. For example, "it will rain today" would be tokenized as ["it", "will", "rain", "today"].

3. **Sub Word Tokenization**: This method splits the text into smaller meaningful units, such as n-grams (sequence of n adjacent symbols). Some of the ways to do subword tokenization are: 

    <p align="center">
        <img src="/blog-content/tokenization/tokenization_n_gram.svg" />
    </p>

    - Byte Pair Encoding (BPE): Merges the frequent pair of tokens into single token. 
    - WordPiece:
    - Sentence Piece