# [Inference] Adding Audit Trail on Inference Engine (DateTime, Input, Output, System Prompt, ...)

Implemented an audit logging mechanism for the inference engine to improve traceability, observability, and compliance.

## What's Included

* Captures and stores the following for every inference request:
    * Date & Time of the request
    * Model name
    * System Prompt
    * User Input
    * Model Output
    * Token usage (prompt, completion, total)
    * Latency (ms)
    * Request ID / Response ID

## How It Works

* Added a custom ASGI middleware to the vLLM OpenAI-compatible API server.
* Middleware intercepts requests and responses for `/v1/chat/completions` and `/v1/completions`.
* Handles both streaming and non-streaming responses:
    * Reads request payload to extract system prompt and user input.
    * Tees the response stream to capture the final model output without breaking client streaming.
* Writes one beautified JSON file per request (instead of appending to a single log file):
    * Files are stored under `../mac/audit/YYYY-MM-DD/` with unique names: `chat_completions_<shortid>_<timestamp>.json`
    * Each file contains all captured fields in a human-readable format (indent=2).
* Directory structure example:
    ```
    ../mac/audit/
    └── 2025-10-01/
        └── chat_completions_4a7f2c9b_20251001T040509Z.json
    ```

## Architecture Diagram

```
Client → vLLM API Server → [Custom Middleware] → JSON Audit File
```
