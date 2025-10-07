# [Data] Research- Data Chat Model 

* **Purpose**: To identify data formatting standards for preparing Khmer-language datasets suitable for supervised fine-tuning (SFT) of LLaMA-based chat models.

* **Schema**: 
```json
{
  "source": "ams | wikipedia | news | forum | other",  // origin of content
  "source_date": "YYYY-MM-DD",                        // optional
  "validation_label": "truth | false | unknown",      // quality/validation flag
  "toxic_label": "safe | unsafe",                     // toxicity annotation
  "tags": ["business_economy", "education"],          // topical tags (can be multiple)
  "messages": [
    { "role": "system", "content": "You are a helpful Khmer assistant. Answer clearly and accurately." },
    { "role": "user", "content": "..." },
    { "role": "assistant", "content": "..." }
    // multi-turn continues strictly alternating user ↔ assistant
  ]
}
```

## Examples

### 1) Single-turn
```json
{
  "source": "wikipedia",
  "source_date": "2024-11-20",
  "validation_label": "truth",
  "toxic_label": "safe",
  "tags": ["geography"],
  "messages": [
    { "role": "system", "content": "You are a helpful Khmer assistant. Answer clearly and accurately." },
    { "role": "user", "content": "តើរាជធានីកម្ពុជា ជាអ្វី?" },
    { "role": "assistant", "content": "រាជធានីកម្ពុជា គឺជាទីក្រុងភ្នំពេញ។" }
  ]
}
```

### 2) Multi-turn
```json
{
  "source": "forum",
  "validation_label": "truth",
  "toxic_label": "safe",
  "tags": ["travel","advice"],
  "messages": [
    { "role": "system", "content": "You are a helpful Khmer assistant. Answer clearly and accurately." },
    { "role": "user", "content": "ខ្ញុំគ្រោងទៅសៀមរាប សូមផ្តល់អនុសាសន៍ទីកន្លែងទៅលេង?" },
    { "role": "assistant", "content": "អាចទៅអង្គរវត្ត បាយ័ន តាប្រសាទ និងផ្សារឈើ។" },
    { "role": "user", "content": "តើពេលណាដែលល្អបំផុតដើម្បីទៅ?" },
    { "role": "assistant", "content": "រដូវប្រាំង ពីខែកញ្ញាដល់ខែកុម្ភៈ អាកាសធាតុនឹងត្រជាក់សមស្របសម្រាប់ទស្សនា។" },
    { "role": "user", "content": "តើមានអាហារដែលគួរតែសាកនៅសៀមរាប?" },
    { "role": "assistant", "content": "អាចសាកអាមុកត្រី បាយឆា និងនំបញ្ចុកខ្មែរ ដែលគេពេញនិយមនៅសៀមរាប។" }
  ]
}
```
