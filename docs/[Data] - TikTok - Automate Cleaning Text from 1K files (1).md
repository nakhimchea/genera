[Data] TikTok - Automate Cleaning Text from 1K files (1)

Prompt Deepseek:
You are an expert in cleaning and preparing transcript data for Khmer, English, and Chinese languages. Your task is to process raw transcript data and remove specific types of "noise" to create a clean, high-quality dataset.
Here are the MANDATORY rules you must follow for every input:
Input Format: The input will always be a JSON object with source, content, and origin fields, like this: {"source":"tiktok","content":"Some text here","origin":"audio_01"}
Output Format: The final output must be a single, valid JSONL object. It must contain only the source and content fields. Do not include the origin field. The output should be formatted as follows: {"source":"[Tiktok]","content":"[cleaned_text]"} Do not add any additional text, explanations, or fields.
Cleaning Rules: You must perform the following cleaning operations on the content field. If the entire content of the content field needs to be removed based on the rules below, **do not output a JSONL line for that item at all.** Only output a line for items that have non-empty content after cleaning.
Music/Song Lyrics: Remove the entire content if it consists solely of music lyrics, instrumental descriptions (e.g., "[music]"), or is labeled as "song," "music," or "no speech detected."
Example 1 (Lyrics):
Input content: One two three four I think I'm losing my mind Trying to stay inside the lines
Output content: ""
Example 2 (Label):
Input content: [music] or no speech detected
Output content: ""
Non-standard Languages: Remove the entire content if it contains languages other than Khmer, English, or Chinese.
LLM-specific Tokens: Remove the entire line if it contains tokens or phrases specific to a large language model's output, such as explanations of audio content or error messages.
Empty or Null Content: If the input content is an empty string ("") or null, the output content must also be an empty string ("").
Time Stamps: Do not remove the entire line. Instead, remove only the time stamp or time range markers, keeping the rest of the text. Time stamps are typically in the format [00:00:00] or [00:00:00 - 00:00:00].
Example:
Input content: [00:01:25] Hello, how are you?
Output content: Hello, how are you?
Duplicated Phrases/Words: Remove the entire content if it contains a phrase, word, or sentence repeated multiple times in a row, often due to a transcription loop or error. This type of error is characterized by lengthy, redundant text.
Final JSONL Object: After applying all the rules, construct the final JSONL object with the cleaned content and the original source label.
Here is the text to clean:   {{ $json["content"] }}
￼<Image 1>
Audio Data Cleaning Summary
A total of 1,000 audio files were processed for transcription and cleaning. After applying quality filters and cleaning procedures, 890 files were retained with acceptable clean text outputs, resulting in an 89% usability rate. The remaining ~10% (110 files) were deemed unusable due to various factors as described in Deepseek prompt.
Tokenization Summary
The cleaned dataset was tokenized using two different tokenizers for comparative analysis:
* PrahokBART 
    * Total tokens: 424,239
* Nicolas Tokenizer
    * Total tokens: 366,697
