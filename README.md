# Semantic Role Labeling with Attention Based Encoder-Decoder
Check out my own backprop system demo here: https://github.com/JingyangYU63/ml-web-app/blob/main/neural_network.ipynb

## What is SRL?

In natural language processing, semantic role labeling (SRL) is the process that assigns labels to words or phrases in a sentence that indicates their semantic role in the sentence, such as that of an agent, predicate, or patient.

Who did what to whom at when at where?
[Agent] [Predicate] [Patient] [Time] [Location]
I wrote this slide this afternoon in Cornell CIS lounge.

## Why SRL? Semantic Understanding!

Performing SRL Task = Semantic Understanding

Semantic understanding is cornerstone of:
- machine translation
- information extraction
- text summarization
- question answering

## PropBank Frames scheme

- Arg0: Agent
- Arg1: Patient
- Arg2: Instrument, benefactive, attribute
- ArgM-LOC: Locative modifiers indicate where some action takes place. 
- ArgM-TMP: Temporal ArgMs show when an action took place, such as "in 1987", "last Wednesday","soon" or "immediately". 

The notion of a locative is not restricted to physical locations, but abstract locations are being marked as LOC as well , like "[in his speech]-LOC he was talking about â¦" Also included in the time notion are adverbs of frequency (eg. often always, sometimes), adverbs of duration (for a year/in an year), order (eg. first), and repetition (eg. again).

## Raw Text to Corpus

Convert each example into 5 question-answer pairs:

E.g., âI wrote this slide this afternoon in Cornell CIS loungeâ
- Input 1: wrote [SEPT] I wrote this slide this afternoon in Cornell CIS lounge. ARG0
- Output 1:  <ð >  I  </ð > 
- Input 2: wrote [SEPT] I wrote this slide this afternoon in Cornell CIS lounge. ARG1
- Output 2:  <ð >  this slide  </ð >
- Input 3: wrote [SEPT] I wrote this slide this afternoon in Cornell CIS lounge. ARG2
- Output 3:  <ð >   </ð >  (Explanation: because there's no ARG2 in this sentence)
- Input 4: wrote [SEPT] I wrote this slide this afternoon in Cornell CIS lounge. ARGM-TMP
- Output 4:  <ð > this afternoon  </ð > 
- Input 5: wrote [SEPT] I wrote this slide this afternoon in Cornell CIS lounge.  ARGM-LOC
- Output 5:  <ð > Cornell CIS lounge  </ð >

In the Named Entity Recognition (NER) task, I treated it as a sequence-tagging task. I tried to tag the sentence word by word, using special token to mark the start and end of a named entity tag. But it turned out that some of the named entity tags start without a start token or end without an end token, which could be trouble-shooting to distinguish the boundary between two independent but consecutive entities that shared the same tag.

## Data Preprocessing

- Handling unknown words: replaces words in the training data by <UNK> based on their frequency
- Add paddings to maintain fixed length for each sentence
- Divide original dataset into training/ validation / test sets

In such a closed vocabulary system the test set can only contain words from this known lexicon. One way to create an open vocabulary system is to model these potential unknown words in the test set by adding a pseudo-word called <UNK>. For example we can replace by <UNK> all words that occur fewer than n times in the training set, where n is some small number.

## Contextual Word Embeddings

pre-trained GloVe embeddings:
- is based on leveraging global word-to-word co-occurrence counts
leveraging the entire corpus
- is trained using matrix factorization techniques on word-context
matrix

## Attention-based Encoder Decoder

<img width="937" alt="image" src="https://user-images.githubusercontent.com/73151841/223310936-2a64b6d6-9bcf-48e7-a390-cfc74e47f949.png">

Intuition: When labeling a sentence, we pay special attention to the word or phrase that we are labeling as well as other phrase worth an attention (e.g. predicate).

## Bidirectional LSTM Encoder

<img width="577" alt="image" src="https://user-images.githubusercontent.com/73151841/223310659-21d07add-d06a-4ea4-b2fe-24c18c915829.png">

Using bidirectional will run your inputs in two ways, one from past to future and one from future to past and what differs this approach from unidirectional is that in the LSTM that runs backwards you preserve information from the future and using the two hidden states combined you are able in any point in time to preserve information from both past and future.

## Attention Context Vector

<img width="769" alt="image" src="https://user-images.githubusercontent.com/73151841/223310780-82b805b2-0510-4fd5-9450-88f716c6c16d.png">

- Dynamically derive a context vector, c, from encoder hidden states at each step, i, during decoding; refer to each as ci
- Take all of the encoder hidden states into account
- Condition the computation of the current decoder state on ci (and prior hidden state and previous output)

## BFSâ  Beam Searchâ

- View decoding as heuristic state-space search
- Beam search allows this while controlling the exponential growth of the search space
- Happens only in predicting process!!

We cares about the overall probability of the whole sequence instead of probability of a single label. But itâs very expensive to traverse (i.e. BFS is costly) through all possible combination of the labels, so we set a fixed width to limit the exponential growth.

Beam Search Illustration:
<img width="489" alt="image" src="https://user-images.githubusercontent.com/73151841/223311220-384e14b9-7a61-40bd-88ec-05a511edf4d8.png">

## Bayesian Optimization: good news for continuous hyperparameters!

Hyperparameter set:
- Learning rate
- Momentum  coefficient
- Epsilon term (for numerical stability)
- Weight decay (L2 penalty)

Unlike the grid search and random search, itâs an informed search method, meaning that it learns from previous iterations. 

## Model-level Parallelism - Teacher Forcing

Teacher forcing in the decoder, i.e., force the system to use the gold target token from training as the next input x_t+1

Benefit: Decoder computes the embedding layer with parallelism, speeding up training

<img width="444" alt="image" src="https://user-images.githubusercontent.com/73151841/223311619-1d16a78d-a1c8-425f-9bac-66d5be34f8c6.png">

## Optimization-level Parallelism

- Break down the work of computing batch gradient evenly to multiple threads!
- Instead of using PyTorchâs built-in optimizer (e.g.  torch.optim.Adam), write my own backprop system!

Check out my own backprop system demo here: https://github.com/JingyangYU63/ml-web-app/blob/main/neural_network.ipynb
