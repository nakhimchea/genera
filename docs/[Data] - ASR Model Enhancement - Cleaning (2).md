# [Audio] ASR Model Enhancement - Cleaning (2)

## Two main task (use n8n + gemini 2.5 flash):

**A.** Convert all number (khmer and english) into khmer number word (while preserving all existing english and khmer number word)

**B.** Convert all khmer and english number word as well as khmer number into english number

## Created 3 workflow in total:

### 1. Master Workflow
It reads a csv file containing the text data, identifies which texts contain numbers using regex pattern matching, and orchestrates batch processing by calling the slave workflow. The regex pattern matching is not very strict, as long as the text contain digits or number words, it goes through. (see attached image below for master workflow)

![Master Workflow](image1.png)

### 2. Slave Workflow
Performs the cleaning on the text that contains number by using two sequential LLM calls to convert the same input text into two different formats: first converting digits to Khmer number words, second converting all number words to English number. The workflow process 10 row at a time, handle rate limiting, and save the results to a csv file incrementally. The output file contains 2 new column: "to_khmer_num_word" and "to_english_num". (see attached image below for slave workflow)

![Slave Workflow](image2.png)

### 3. Reorder Workflow
Simply sort the output from slave workflow into its original and correct index. In slave workflow, there's 2 path that the text goes through: text that contains number goes through LLM while the rest goes to a separate path so the output has messed up order. That's why this workflow is needed. (see attached image below for reorder workflow)

![Reorder Workflow](image3.png)

## Sample Output

![Sample Output](image4.png)

**Total is about 29K row**
