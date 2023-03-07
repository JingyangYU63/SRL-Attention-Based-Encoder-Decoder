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

The notion of a locative is not restricted to physical locations, but abstract locations are being marked as LOC as well , like "[in his speech]-LOC he was talking about …" Also included in the time notion are adverbs of frequency (eg. often always, sometimes), adverbs of duration (for a year/in an year), order (eg. first), and repetition (eg. again).

## Raw Text to Corpus

Convert each example into 5 question-answer pairs:

E.g., “I wrote this slide this afternoon in Cornell CIS lounge”
- Input 1: wrote [SEPT] I wrote this slide this afternoon in Cornell CIS lounge. ARG0
- Output 1:  <𝑠>  I  </𝑠> 
- Input 2: wrote [SEPT] I wrote this slide this afternoon in Cornell CIS lounge. ARG1
- Output 2:  <𝑠>  this slide  </𝑠>
- Input 3: wrote [SEPT] I wrote this slide this afternoon in Cornell CIS lounge. ARG2
- Output 3:  <𝑠>   </𝑠>  (Explanation: because there's no ARG2 in this sentence)
- Input 4: wrote [SEPT] I wrote this slide this afternoon in Cornell CIS lounge. ARGM-TMP
- Output 4:  <𝑠> this afternoon  </𝑠> 
- Input 5: wrote [SEPT] I wrote this slide this afternoon in Cornell CIS lounge.  ARGM-LOC
- Output 5:  <𝑠> Cornell CIS lounge  </𝑠>
