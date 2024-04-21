+++
title = 'Tokenization'
date = 2024-04-19T10:16:36-07:00
draft = false
tags = [ "LLM", "Tokenization", "BPE", "GPT"]
+++

### What is tokenization ?

Machine learning models operate on numerical representations of input data. Language models, such as LLMs, work with text input.
Tokenization is the process of converting the text into smaller chunks called tokens, which are then assigned numerical representations. 
The collection of unique tokens forms what we call a vocabulary. It is essentially a dictionary that maps each token to a unique integer. The size of the vocabulary is determined by the number of unique tokens in the corpus.

The vocabulary sizes of some popular LLMs are as follows:

* GPT-2: The vocabulary size is 50,257.
* GPT-3: The vocabulary size is approximately 175 billion.
* Llama 1: The vocabulary size is 32,000.
* Llama 2: The vocabulary size is also 32,000.

Larger vocabularies allow the model to understand and generate a wider range of words and phrases. However, they also require more computational resources (embedding size and softmax size increases) to train and use.


Broadly there are three types of tokenization we can do:

1. **Character Tokenization**: The text is broken down into characters, each character including whitespace and punctuation becomes a token. For example, 'hello' would be tokenized as ['h', 'e', 'l', 'l', 'o'].

2. **Word Tokenization**: In this method, the text is broken down into words. Each word becomes a token. For example, "it will rain today" would be tokenized as ["it", "will", "rain", "today"].

3. **Sub Word Tokenization**: This method splits the text into smaller meaningful units, such as n-grams (sequence of n adjacent symbols). Some of the ways to do subword tokenization are: 

    <p align="center">
        <img src="/blog-content/tokenization/tokenization_n_grams.svg" />
    </p>

    - Byte Pair Encoding (BPE): Merges the frequent pair of tokens into single token. 
    - WordPiece:
    - Sentence Piece

Selecting the appropriate tokenization method is crucial for NLP tasks. Incorrect tokenization can cause significant issues in language models (LLMs). The below are some of the reasons why LLMs fails due to tokenization.

* LLMs struggle with basic text processing tasks like string reversal.
* They falter in simple arithmetic.
* LLMs favor YAML over JSON.
* LLMs halts on encountering text such as '<|endoftext|>'.
* LLMs works poorly for languages other than english. 

<br>

### Visualizing Tokenization

Lets use the [tiktokenizer.vercel.app](https://tiktokenizer.vercel.app/) to visualize how GPT-2 splits the text into tokens.

<style>
table, th, td {
  border:1px solid black;
  
}
</style>

<table style="width:100% ">
  <tr>
    <th style="width:50%; text-align: center">Prompt</th>
    <th style="text-align: center">Tokenized (GPT-2)</th>
  </tr>
  
  <tr>
    <td>
        Egg.<br>
        A countryman has a goose that lays a golden egg every day, which he takes to market and sells. He becomes wealthy, but becomes impatient with the goose because it only lays one egg a day.
    </td>
    <td>
        <p style="text-align: center">Token Count: 45</p>
        <img src="/blog-content/tokenization/engl_gpt2_token_vis.png"/>
    </td>
  </tr>

  <tr>
    <td>
        계란.<br>
        한 시골 사람에게 매일 황금알을 낳는 거위 한 마리가 있는데, 그는 그것을 시장에 내다 팔고 있습니다. 그는 부자가 되었지만 하루에 알을 한 개밖에 낳지 못하는 거위를 참을 수 없게 됩니다.
    </td>
    <td>
        <p style="text-align: center">Token Count: 228</p>
        <img src="/blog-content/tokenization/korean_gpt2_token_vis.png"/>
    </td>
  </tr>

  <tr>
    <td>
        <code>
            <pre>
def is_prime(n):
    if n <= 1:
        return False
    for i in range(2, int(n**0.5) + 1):
        if n % i == 0:
        return False
    return True
            </pre>
        </code>
    </td>
    <td>
        <p style="text-align: center">Token Count: 63</p>
        <img src="/blog-content/tokenization/python_gpt2_vis.png"/>
    </td>
  </tr>
</table>

Each color represents a different token. GPT-2 splits English text primarily based on words along with whitespace. Interestingly, “Egg” becomes two tokens: [‘E’, ‘gg’].

The same English text in the Korean language occupies more tokens. This can be due to multiple reasons such as:

   - Dataset Bias:  GPT might be trained on datasets with a larger proportion of English text compared to Korean. This can lead the tokenizer to be more efficient at handling English. As a result, it might need more tokens to represent the same meaning in Korean.
   - Korean language characteristics: Korean inherently uses more characters per word than English. This simply means that Korean needs more building blocks to express the same idea, leading to a higher token count.

GPT-2 doesn’t perform well with programming languages like Python. It seems to struggle with Python indentation, often requiring multiple tokens to represent it. On the other hand, GPT-4 excels at identifying Python indentation, resulting in a lower token count for the same code snippet. 

<table style="width:100% ">
  <tr>
    <th style="width:50%; text-align: center">Tokenized (GPT-4)</th>
    <th style="text-align: center">Tokenized (GPT-2)</th>
  </tr>
  
  <tr>
    <td>
        <p style="text-align: center">Token Count: 51</p>
        <img src="/blog-content/tokenization/python_gpt4_vis.png"/>
    </td>
    <td>
        <p style="text-align: center">Token Count: 63</p>
        <img src="/blog-content/tokenization/python_gpt2_vis.png"/>
    </td>
  </tr>
</table>

<br>

### Context Length

We have discussed the importance of token count and how a tokenizer that can tokenize input using a small number of tokens is desirable. The reason for a lower token count is due to the context length of Language Models (LLMs).

The context length of LLMs is the maximum number of tokens a model can process at once, meaning it is the number of tokens to which the model can attend. Context length plays a crucial role in LLMs and affects their ability to retain and attend to information from very old sequences. This directly impacts the quality of tasks such as summarization, long-term planning, and coherence in conversations.

Therefore, it is **essential to have a tokenizer that can efficiently compress and tokenize the input text in the smallest number of tokens possible** to take advantage of the available context length.

The context length of some popular LLMs are as follows:

* GPT-2: 1024 tokens
* GPT-3: 2048 tokens
* GPT-4: 32k tokens
* Llama 1: 2,048 tokens
* Llama 2: 4,096 tokens

<br>


