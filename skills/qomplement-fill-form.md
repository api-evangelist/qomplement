---
name: Fill a PDF form or Excel template
description: Auto-fill a PDF form or populate an Excel template from source documents or field mappings using the Qomplement StructDatafy API.
api: openapi/qomplement-openapi-original.json
operations:
  - fill_pdf_v1_fill_pdf_post
  - fill_excel_v1_fill_excel_post
  - get_job_v1_jobs__job_id__get
  - download_job_result_v1_jobs__job_id__download_get
---

# Fill a PDF form or Excel template

Base URL: `https://developer-api.qomplement.com/v1`

## Auth
`Authorization: Bearer sd_...` on every request (key from https://developers.qomplement.com).

## Steps
1. **Submit the fill job** — for PDFs `POST /v1/fill/pdf` (`fill_pdf_v1_fill_pdf_post`); for spreadsheets `POST /v1/fill/excel` (`fill_excel_v1_fill_excel_post`). Provide the target template plus source documents or explicit field mappings as `multipart/form-data`. Returns a job.
2. **Poll for completion** — `GET /v1/jobs/{job_id}` (`get_job_v1_jobs__job_id__get`) until `status` is `completed` or `failed`, or wait for the `fill_pdf` / `fill_excel` webhook.
3. **Download the filled file** — `GET /v1/jobs/{job_id}/download` (`download_job_result_v1_jobs__job_id__download_get`).

## Rules
- `code: no_form_fields` means no fillable form fields were detected in the PDF — verify the template.
- Rate limit 60/min, 10,000/month; honor `Retry-After` on 429.
- Errors follow `{error, detail, code}`; `422` is a validation/processing error.
