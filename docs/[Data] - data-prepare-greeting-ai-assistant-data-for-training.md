# [Data] Prepare greeting AI assistant data for training

This task involves creating a dataset for greetings and interactive messages between a user and an AI LLM. A total of 200 messages were run to generate the data. Below is the format sample:
```json
{"messages":[{"role":"system","content":"You are a helpful Khmer assistant. Answer clearly and accurately."},{"role":"user","content":"ជំរាបសួរ! ខ្ញុំមានរឿងចង់សួរអ្នកបន្តិច តើអ្នកអាចជួយបានទេ?"},{"role":"assistant","content":"បាទ/ចាស ខ្ញុំត្រៀមខ្លួនជាស្រេចដើម្បីជួយអ្នកនូវរាល់ចម្ងល់ ឬបញ្ហាផ្សេងៗ។ សូមកុំស្ទាក់ស្ទើរក្នុងការសួរខ្ញុំ។"}]}
```

## Prompt:
You are an expert at creating natural, diverse, and realistic chat conversations in Khmer for training language models.
 
**YOUR TASK:** Analyze the provided prompt and response, then create a natural conversation between a human user and an AI assistant.
 
**INSTRUCTIONS:**
 
1. **Understand the context:** 
   * Read the prompt and response carefully
   * Identify what the REAL user need or question would be in a natural conversation
   * The prompt might be awkwardly phrased or from an AI perspective - transform it into how a REAL HUMAN would ask
 
2. **Create DIVERSE and NATURAL user messages:**
   * Think: "What would a real person naturally ask or say to get this kind of response?"
   * **BE CREATIVE - Don't always use just "សួស្តី"!** 
   * Vary the greetings and questions:
     * Casual greetings: "ជំរាបសួរ", "សួស្តី"
     * Direct questions: "អ្នកអាចជួយខ្ញុំបានទេ?", "ខ្ញុំត្រូវការជំនួយបន្តិច"
     * Specific needs: based on what the response implies
   * Make it sound like how Cambodian people really talk
 
3. **Create HELPFUL and NATURAL assistant responses:**
   * Translate and adapt the "chosen" response
   * **Remove ALL AI names/brands** (SuperAGI, ChatGPT, Claude, OpenAI, "assistant created by", etc.)
   * **IMPORTANT - Avoid redundant greetings:**
     * If user says "ជំរាបសួរ" → DON'T start with "ជំរាបសួរ! បាទ/ចាស"
     * If user says "សួស្តី" → DON'T start with "សួស្តី! បាទ/ចាស"
     * Choose ONE response style:
       * Either: "បាទ/ចាស [direct response]" (no greeting)
       * Or: "សួស្តី/ជំរាបសួរ! [response]" (no បាទ/ចាស)
       * Or: Just start directly with helpful content
   * **Vary the response style** - don't always say the same thing
   * Keep it natural and conversational in Khmer
 
4. **Add variety based on context:**
   * If the original prompt mentions specific topics (pets, weather, tasks), reflect that in the user message
   * Make the conversation feel realistic and contextual
 
**CRITICAL OUTPUT RULES:**
* Return ONLY valid JSON starting with { and ending with }
* **Output COMPACT JSON on a single line with NO line breaks**
* NO markdown code fences (no ```json or ```)
* NO explanatory text before or after the JSON
* Properly escape all quotes inside content fields using \"
* Complete the entire JSON structure
 
**Input:** Prompt: {{ $json.prompt }} Response: {{ $json.chosen }}
 
**Required Output Format:** 
```json
{
  "messages": [
    {
      "role": "system",
      "content": "You are a helpful Khmer assistant. Answer clearly and accurately."
    },
    {
      "role": "user",
      "content": "[Creative, natural, VARIED Khmer message]"
    },
    {
      "role": "assistant",
      "content": "[Natural Khmer response - NO redundant greetings, no AI brand names]"
    }
  ]
}
```
 
**Examples of GOOD natural responses:**
 
**User:** "សួស្តី! ខ្ញុំមានចម្ងល់មួយ តើខ្ញុំអាចសួរបានទេ?"
* ✅ GOOD: "បាទ/ចាស បាន។ ខ្ញុំនៅទីនេះដើម្បីជួយ។ សូមសួរមកទៅ។"
* ✅ GOOD: "ពិតណាស់! មានអ្វីចង់សួរ?"
* ❌ BAD: "ជំរាបសួរ! បាទ/ចាស បាន។" (redundant greeting)
 
**User:** "ជំរាបសួរ! ខ្ញុំឆ្ងល់ថាអ្នកអាចជួយអ្វីខ្លះ?"
* ✅ GOOD: "បាទ/ចាស ខ្ញុំអាចជួយបានច្រើនយ៉ាង ដូចជាឆ្លើយសំណួរ ផ្តល់ព័ត៌មាន ជាដើម។"
* ✅ GOOD: "ខ្ញុំអាចជួយឆ្លើយសំណួរ ផ្តល់ព័ត៌មាន និងច្រើនទៀត។ តើអ្នកត្រូវការជំនួយរឿងអ្វី?"
* ❌ BAD: "សួស្តី! បាទ/ចាស..." (redundant greeting)
 
**User:** "សួស្តី អ្នកមានសត្វចិញ្ចឹមអត់?"
* ✅ GOOD: "អត់ទេ ខ្ញុំគ្មានសត្វចិញ្ចឹមទេ។ តើអ្នកត្រូវការជំនួយរឿងអ្វី?"
* ✅ GOOD: "ខ្ញុំមិនមានសត្វចិញ្ចឹមទេ ព្រោះខ្ញុំជាកម្មវិធី។ ប៉ុន្តែខ្ញុំអាចជួយអ្នកបាន!"
 
**IMPORTANT: Be creative and natural! Avoid mixing formal greetings with "បាទ/ចាស" - pick one style and stick with it!**

Now process the input above and return ONLY the JSON output.
