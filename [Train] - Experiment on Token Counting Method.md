# [Train] Experiment on Token Counting Method

## Purpose
To find which tokenizer best suited for counting the processed data.

## Task

### Testing
Testing counting the khmer preprocessed data with multiple tokenizer such as:
- Khmer-NLTK (Word Tokenizer)
- Km-tokenizer by Nicolas (SentencePiece Tokenizer)
- Qwen 2.5 Tokenizer (BPE)
- Llama 3.2 3B tokenizer (BPE)

### Output

#### Khmer NLTK:
- instruction: 730,036 tokens (25.1%)
- input: 90,597 tokens (3.1%)
- output: 2,089,100 tokens (71.8%)
- **Total Khmer tokens: 2,909,733**

#### Nicolas Khmer SentencePiece Tokenizer:
- instruction: 2,217,253 tokens (24.8%)
- input: 296,134 tokens (3.3%)
- output: 6,426,867 tokens (71.9%)
- **Total Khmer tokens: 8,940,254**

#### Qwen 2.5 1.5B Tokenizer:
- instruction: 4,418,795 tokens (24.8%)
- input: 574,119 tokens (3.2%)
- output: 12,819,661 tokens (72.0%)
- **Total content tokens: 17,812,575**

#### Llama 3.2 3B Tokenizer:
- instruction: 5,878,460 tokens (24.7%)
- input: 756,479 tokens (3.2%)
- output: 17,137,203 tokens (72.1%)
- **Total tokens: 23,772,142**

## Final Outcome
PrahokBart Tokenizer is used and here is the token count:
- Raw data = 861,581 token
- Trainable QA = 4,279,726 token
