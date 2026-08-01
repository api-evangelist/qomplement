---
name: Extract structured data from a document
description: Upload one or more documents to the Qomplement StructDatafy API and retrieve the extracted structured data as an async job.
api: openapi/qomplement-openapi-original.json
operations:
  - extract_document_v1_extract_post
  - get_job_v1_jobs__job_id__get
  - download_job_result_v1_jobs__job_id__download_get
---

# Extract structured data from a document

Base URL: `https://developer-api.qomplement.com/v1`

## Auth
Send `Authorization: Bearer sd_...` on every request. Get a key at https://developers.qomplement.com. Missing/invalid keys return 401/403 ("Could not validate credentials").

## Steps
1. **Submit the document** — `POST /v1/extract` (`extract_document_v1_extract_post`) as `multipart/form-data`: `files` (one or more documents, 30+ formats supported), optional `model` (`qomplement-OCR-v1` default or `qomplement-OCR-XL-v1` for high precision), optional `schema` (JSON schema of target fields), and `output_format` (`json`|`csv`|`xml`, default `json`). Returns a job.
2. **Poll for completion** — `GET /v1/jobs/{job_id}` (`get_job_v1_jobs__job_id__get`) until `status` is `completed` or `failed`. Or receive the `extract` webhook callback instead of polling.
3. **Retrieve the result** — read `result` from the job, or `GET /v1/jobs/{job_id}/download` (`download_job_result_v1_jobs__job_id__download_get`) for the output file.

## Rules
- Rate limit: 60 req/min, 10,000/month per key. On 429, honor `Retry-After` (seconds).
- Errors use `{error, detail, code}` (not RFC 9457). `422` = validation/processing failure; `code: processing_error` means OCR/extraction failed.
- No idempotency key is supported; do not blindly retry a non-idempotent submit.
