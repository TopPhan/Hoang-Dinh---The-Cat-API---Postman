🐈 The Cat API Testing Project - https://api.thecatapi.com/v1

📅 Execution Period: October 19, 2025 – October 26, 2025

👤 Tester: Hoàng Đỉnh
<img width="1341" height="611" alt="image" src="https://github.com/user-attachments/assets/deed6e15-cd44-44c3-b59f-22e8beed5895" />

---
> A structured, automated API testing collection built with **Postman** targeting [The Cat API](https://api.thecatapi.com/v1). Covers a full **CRUD image lifecycle** with conditional execution flow, AJV JSON Schema validation, dynamic variable chaining, retry logic, performance checks, and security header verification. 
---

![Test Flow](https://github.com/user-attachments/assets/233531fb-bf65-475e-9ca4-d76cc3383676)





## 📋 Table of Contents

- [Project Overview](#-project-overview)
- [Tech Stack & Tools](#-tech-stack--tools)
- [Project Structure](#-project-structure)
- [Quick Setup & Run](#-quick-setup--run)
- [Test Flow & Architecture](#-test-flow--architecture)
- [Test Cases Breakdown](#-test-cases-breakdown)
- [Key Techniques Explained](#-key-techniques-explained)
- [Collection Variables](#-collection-variables)
- [Test Summary & Results](#-test-summary--results)
- [Planning](#-sprint-planning)

---

## 🌿 Project Overview

This project demonstrates API testing skills using **Postman** to validate **The Cat API** — a public REST API for cat breed and image data. The test suite focuses on a full **Create → Read → Verify → Delete** (CRUD) image workflow, where each request is conditionally chained to the next based on the previous step's success.

| Metric | Result |
|---|---|
| 🧪 Total Test Cases (`pm.test`) | **29 assertions** across 5 requests |
| ✅ Pass Rate | **> 90%** |
| ⏱️ Total Execution Time | **~10.6 seconds** |
| ⚡ Average Response Time | **~1.6 seconds** |
| 📅 Execution Period | **October 19 – 26, 2025** |
| 👤 Author | **Hoàng Đỉnh** |

### 🌟 Key Strengths

- **Conditional execution flow** — uses `pm.execution.setNextRequest()` to skip or halt the entire chain if a critical step fails, preventing false positives from cascading.
- **AJV Schema Validation** — strict JSON Schema (draft-07) is compiled and validated at runtime inside Postman test scripts for every response.
- **Dynamic variable chaining** — all fields from `POST /images/upload` are saved as `expected_*` collection variables and cross-validated in subsequent requests using the uploaded image's `id` as a primary key.
- **Smart retry logic** — `DELETE /images/{id}` automatically retries up to 3 times before aborting with a critical error.
- **Performance & security checks** — every request validates response time, payload size, `Content-Type`, `x-frame-options`, and `x-content-type-options` headers.

---

## 🛠️ Tech Stack & Tools

| Tool | Version | Purpose |
|---|---|---|
| Postman | 11.68.2 | API client, test runner, scripting |
| Collection Runner | Built-in | Automated sequential test execution |
| AJV (Another JSON Schema Validator) | Built-in (Postman sandbox) | JSON Schema validation |
| Postman Environment | — | API key & runtime variable storage |
| Collection Variables | — | Cross-request dynamic data sharing |
| The Cat API | v1 | System under test |

---

## 🧱 Project Structure

```
Hoang-Dinh---The-Cat-API---Postman/
│
├── Hoàng Đỉnh - The Cat API.postman_collection.json   # Main collection with all test scripts
├── MY_SERVICE_API_KEY=DEV.postman_environment.json    # Environment file (API key)
│
├── Test Flow.xlsx                                     # Visual test flow design & planning
│
├── schemas/                                           # JSON Schema definition files (draft-07)
│   ├── breedSchemaObject.json                         # Schema for GET /breeds response object
│   ├── uploadschemaObject.json                        # Schema for POST /images/upload response
│   ├── myImageObject.json                             # Schema for GET /images (my images list)
│   └── imageDetailObject.json                        # Schema for GET /images/{id}/analysis
│
└── screenshots/                                       # Test execution evidence
    ├── Collection Variable.jpg
    ├── Get-All-Breeds - Params.jpg
    ├── Get-All-Breeds - Pre-request-scripts.jpg
    ├── Get-All-Breeds - Test results.jpg
    ├── Post-Upload-Image - Body.jpg
    ├── Post-Upload-Image - Pre-request-scripts.jpg
    ├── Post-Upload-Image - Test results.jpg
    ├── Get-My-Image - Params.jpg
    ├── Get-My-Image - Test results.jpg
    ├── Get-Image-Detail - Pre-request-scripts.jpg
    ├── Get-Image-Detail - Test results.jpg
    ├── Delete-Image - Test results.jpg
    ├── Runner Collection (1–4).jpg
    └── Test Flow.JPG
```

---

## 🚀 Quick Setup & Run

### Prerequisites

- [Postman](https://www.postman.com/downloads/) installed (v10+ recommended)
- A valid API key from [The Cat API](https://thecatapi.com/) — register for free

### Step 1 — Import Files

Import **both** files into Postman via **File → Import**:

```
Hoàng Đỉnh - The Cat API.postman_collection.json
MY_SERVICE_API_KEY=DEV.postman_environment.json
```

### Step 2 — Select Environment

In the top-right dropdown, select:

```
MY_SERVICE_API_KEY=DEV
```

> ⚠️ If using your own API key, update the `api-key` value in the environment file before running.

### Step 3 — Run the Collection

1. Click the `...` menu on the collection → **"Run collection"**
2. Select the **"Image Validation (CRUD)"** folder
3. Click **"Run Hoàng Đỉnh - The Cat API"**

The entire CRUD flow executes automatically, self-validating at every step.

---

## 🔄 Test Flow & Architecture

### Conditional Execution Chain

```
┌─────────────────────────────────────────────────────┐
│             Collection Runner Starts                │
└──────────────────────┬──────────────────────────────┘
                       │
          ┌────────────▼────────────┐
          │  1. GET /breeds          │
          │  → Save firstbreeds_id   │
          └────────────┬────────────┘
                       │ setNextRequest("Post-Upload-Image")
          ┌────────────▼────────────┐
          │  2. POST /images/upload  │
          │  → Save UploadImage_id   │
          │  → Save expected_* vars  │
          └────────────┬────────────┘
              ✅ pass  │  ❌ fail → STOP (null)
                       │ setNextRequest("Get-My-Image")
          ┌────────────▼────────────┐
          │  3. GET /images?limit=10 │
          │  → Find UploadImage_id   │
          │  → Compare expected_*    │
          └────────────┬────────────┘
              ✅ found │  ❌ not found → STOP (null)
                       │ setNextRequest("Get-Image-Detail")
          ┌────────────▼────────────┐
          │  4. GET /images/{id}     │
          │  → Verify detail data    │
          └────────────┬────────────┘
              ✅ match │  ❌ mismatch → STOP (null)
                       │ setNextRequest("Delete-Image")
          ┌────────────▼────────────┐
          │  5. DELETE /images/{id}  │
          │  → Retry up to 3 times   │
          └────────────┬────────────┘
              ✅ 204   │  ❌ after 3 retries → STOP (null)
                       │ setNextRequest("Get-My-Image")
          ┌────────────▼────────────┐
          │  3. GET /images (again)  │
          │  → Confirm ID is GONE    │
          └────────────┬────────────┘
                       │
          ✅ Test Complete — Image lifecycle verified!
```

### Architecture Overview

```
┌────────────────────────────────────────────────────────┐
│                  POSTMAN ENVIRONMENT                   │
│              api-key: live_S9hN...                     │
└──────────────────────┬─────────────────────────────────┘
                       │ injects header into every request
┌──────────────────────▼─────────────────────────────────┐
│               COLLECTION VARIABLES                     │
│  base_url · sub_id · firstbreeds_id · UploadImage_id  │
│  expected_id · expected_url · expected_width · ...     │
└──────────────────────┬─────────────────────────────────┘
                       │ shared across all requests
┌──────────────────────▼─────────────────────────────────┐
│              TEST SCRIPTS (pm.test)                    │
│  Status Code · Schema (AJV) · Data Validation          │
│  Headers · Security · Response Time · Payload Size     │
└──────────────────────┬─────────────────────────────────┘
                       │ drives
┌──────────────────────▼─────────────────────────────────┐
│           CONDITIONAL FLOW CONTROL                     │
│         pm.execution.setNextRequest()                  │
│               Stop / Retry / Continue                  │
└────────────────────────────────────────────────────────┘
```

---

## 🧪 Test Cases Breakdown

### 1. `GET /breeds`
**Endpoint:** `GET https://api.thecatapi.com/v1/breeds`
![Get-All-Breeds - Test results](https://github.com/user-attachments/assets/fb7b8280-7ee1-41de-9f05-ff35f58abd7b)


| # | Test Case | Assertion |
|---|---|---|
| TC1 | API call succeeds | Status code `200` |
| TC2 | Schema Validation | All breed fields match JSON Schema draft-07 via AJV |
| TC3 | Verify specific breed details | `name`, `origin`, `country_codes` match collection variables |
| TC4 | Image URL format is correct | URL contains `https://` and `.jpg` |
| TC5 | Content-Type & Connection headers | `application/json`, `keep-alive` |
| TC6 | Critical Security Headers | `x-frame-options: SAMEORIGIN`, `x-content-type-options: nosniff` |
| TC7 | Response time | `< 500ms` |

> **Post-script:** Saves `firstbreeds_id` → chains to `Post-Upload-Image`

---

### 2. `POST /images/upload`
**Endpoint:** `POST https://api.thecatapi.com/v1/images/upload`
![Post-Upload-Image - Test results](https://github.com/user-attachments/assets/802c8bc8-842f-4866-8522-2ed693413996)


| # | Test Case | Assertion |
|---|---|---|
| TC1 | Image uploaded successfully | Status code `201` |
| TC2 | Schema Validation | Upload response matches strict schema (`additionalProperties: false`) |
| TC3 | Verify sub_id and breed_ids | Values match pre-set collection variables |
| TC4 | Content-Type & Connection headers | `application/json`, `keep-alive` |
| TC5 | Critical Security Headers | `x-frame-options: SAMEORIGIN`, `x-content-type-options: nosniff` |
| TC6 | Response time | `< 5s` |
| TC7 | Response size | Payload `< 2 KB` |

> **Post-script:** Saves `UploadImage_id` (primary key) + all response fields as `expected_*` variables → chains to `Get-My-Image`
>
> **Fail-safe:** If `actual_upload.id` is missing → `setNextRequest(null)` — execution halts immediately

---

### 3. `GET /images?limit=10`
**Endpoint:** `GET https://api.thecatapi.com/v1/images?limit=10`
![Get-My-Image - Test results](https://github.com/user-attachments/assets/e99ccc72-5bae-4504-bfee-6a13571801a3)


| # | Test Case | Assertion |
|---|---|---|
| TC1 | API call succeeds | Status code `200` |
| TC2 | Schema Validation | Array of image objects matches full JSON Schema (nested breed schema included) |
| TC3 | Data Validation: uploaded image found | `UploadImage_id` exists in response array; all 7 fields (`id`, `url`, `sub_id`, `width`, `height`, `original_filename`, `breed_ids`) match `expected_*` variables |
| TC4 | Content-Type & Connection headers | `application/json`, `keep-alive` |
| TC5 | Critical Security Headers | `x-frame-options: SAMEORIGIN`, `x-content-type-options: nosniff` |
| TC6 | Response time | `< 1s` |
| TC7 | Response size | Payload `< 2 KB` |

> **Post-script:** If `UploadImage_id` not found → STOP. Otherwise → chains to `Get-Image-Detail`

---

### 4. `GET /images/{image_id}/analysis`
**Endpoint:** `GET https://api.thecatapi.com/v1/images/{UploadImage_id}/analysis`
![Get-Image-Detail - Test results](https://github.com/user-attachments/assets/8fd560fd-8d83-4754-9874-f45228716040)


| # | Test Case | Assertion |
|---|---|---|
| TC1 | API call succeeds | Status code `200` |
| TC2 | Schema Validation | Analysis object matches schema (`vendor`, `image_id`, `created_at`, `labels[]`) |
| TC3 | Data Validation | `image_id` equals `UploadImage_id`, `vendor` matches expected |
| TC4 | Content-Type & Connection headers | `application/json`, `keep-alive` |
| TC5 | Critical Security Headers | `x-frame-options: SAMEORIGIN`, `x-content-type-options: nosniff` |
| TC6 | Response time | `< 500ms` |
| TC7 | Response size | Payload `< 2 KB` |

> **Post-script:** Sets `imageDetail_condition` flag → chains to `Delete-Image`

---

### 5. `DELETE /images/{image_id}`
**Endpoint:** `DELETE https://api.thecatapi.com/v1/images/{UploadImage_id}`
![Delete-Image - Test results](https://github.com/user-attachments/assets/40204c93-0cfe-4c67-bbfb-a31522b9e67c)


| # | Test Case | Assertion |
|---|---|---|
| TC1 | Image deleted successfully | Status code `204` (No Content) |

**Retry logic:**
```
DELETE /images/{id}
    ├─ ✅ 204 → back to GET /images → confirm ID is NOT found → ✅ Done
    ├─ ❌ retry 1/3 → DELETE again
    ├─ ❌ retry 2/3 → DELETE again
    └─ ❌ retry 3/3 → STOP — Critical failure logged
```

> **On success:** Resets `delete_retry_count = 0` → loops back to `Get-My-Image` to confirm deletion.
> **On 3 failures:** `setNextRequest(null)` + throws critical error with failure count.

---

## 🔬 Key Techniques Explained

### Conditional Execution with `setNextRequest()`
Every request explicitly directs the runner to the next step via `pm.execution.setNextRequest("RequestName")`. On any critical failure, `pm.execution.setNextRequest(null)` halts the entire chain — preventing meaningless downstream tests from running against broken state.

### AJV JSON Schema Validation
The Postman sandbox includes the **AJV** library. Each request compiles a strict JSON Schema (draft-07) at runtime and validates the actual response. If any field is missing, of the wrong type, or violates a constraint, the test throws a detailed error with exactly which field failed:

```javascript
const Ajv = require('ajv');
const ajv = new Ajv({ allErrors: true, verbose: true });
const validate = ajv.compile(schemaObject);
const isValid = validate(responseBody);
if (!isValid) throw new Error("Schema FAILED:\n" + JSON.stringify(validate.errors, null, 2));
```

### Dynamic Variable Chaining with `expected_*` Pattern
After `POST /images/upload`, a loop saves every response field as a collection variable with the `expected_` prefix:
```javascript
for (const key in actual_upload) {
    pm.collectionVariables.set(`expected_${key}`, String(actual_upload[key]));
}
```
This enables precise field-by-field comparison in `GET /images` — using `UploadImage_id` as a primary key to find the exact image in the returned array, then comparing all 7 fields against the saved expected values.

### Smart Retry Logic for DELETE
The `delete_retry_count` collection variable tracks attempts. On each failure, the count increments and `setNextRequest(pm.info.requestName)` re-runs the same request. After 3 consecutive failures, execution halts completely with a critical error message.

### Performance & Security Assertions
Every request validates:

| Check | Threshold |
|---|---|
| Response time (reads) | `< 500ms` |
| Response time (upload) | `< 5s` |
| Response time (list) | `< 1s` |
| Payload size | `< 2 KB` |
| `x-frame-options` | `SAMEORIGIN` |
| `x-content-type-options` | `nosniff` |
| `Content-Type` | `application/json` |
| `Connection` | `keep-alive` |

---

## 📦 Collection Variables

| Variable | Set By | Used By | Purpose |
|---|---|---|---|
| `base_url` | Pre-set | All requests | Base API URL |
| `sub_id` | Pre-set | `POST /images/upload` | Sub-identifier for uploaded images |
| `breeds_name` | Pre-set | `GET /breeds` TC3 | Expected breed name |
| `breeds_origin` | Pre-set | `GET /breeds` TC3 | Expected breed origin |
| `breeds_country_codes` | Pre-set | `GET /breeds` TC3 | Expected country code |
| `firstbreeds_id` | `GET /breeds` | `POST /images/upload` | Breed ID to tag the uploaded image |
| `UploadImage_id` | `POST /images/upload` | `GET /images`, `GET /images/{id}`, `DELETE` | Primary key for tracking the uploaded image |
| `expected_*` | `POST /images/upload` | `GET /images` TC3 | All uploaded image fields for cross-validation |
| `delete_retry_count` | `DELETE` script | `DELETE` retry loop | Tracks deletion retry attempts (max 3) |
| `imageDetail_condition` | `GET /images/{id}` | Flow control | Flag to control next request routing |

---

## 📊 Test Summary & Results

| Request | Method | Endpoint | `pm.test` Count | Status |
|---|---|---|---|---|
| Get-All-Breeds | GET | `/breeds` | 7 | ✅ Pass |
| Post-Upload-Image | POST | `/images/upload` | 7 | ✅ Pass |
| Get-My-Image | GET | `/images?limit=10` | 7 | ✅ Pass |
| Get-Image-Detail | GET | `/images/{id}/analysis` | 7 | ✅ Pass |
| Delete-Image | DELETE | `/images/{id}` | 1 | ✅ Pass |
| **Total** | | | **29 assertions** | **> 90% pass rate** |

**Collection Runner Stats:**
- Total execution time: ~10.6 seconds
- Average response time: ~1.6 seconds
- Execution: fully automated, no manual steps required after pressing Run

---

## 📅 Planning

```
Stage 1 (Oct 19–20) — API Exploration & Environment Setup
├── Research The Cat API documentation and endpoints
├── Define test objectives and CRUD scope
├── Configure Postman environment and collection variables
└── Design full test flow logic in Excel (Test Flow.xlsx)

Stage 2 (Oct 21–24) — Collection Design & Test Scripting
├── Build all 5 requests in the CRUD collection
├── Write pm.test() assertions for each endpoint
├── Implement AJV JSON Schema validation for all responses
└── Apply conditional execution and dynamic variable chaining

Stage 3 (Oct 25) — Automation & Performance Validation
├── End-to-end automation via Collection Runner
├── Add response time and payload size assertions
├── Validate security response headers
└── Implement DELETE retry logic and fail-safe handling

Stage 4 (Oct 26) — Documentation & Finalization
├── Capture screenshots of all test results, params, and scripts
├── Write README and finalize setup guide
└── Organize schemas, screenshots, and project files
```

---

## 🔗 References

- 📘 [The Cat API Postman Docs](https://documenter.getpostman.com/view/5578104/RWgqUxxh#8606c7c6-338e-46aa-8f1a-3335ed2b8127)
- 🐱 [The Cat API Website](https://thecatapi.com/)
- 📦 [AJV — Another JSON Schema Validator](https://ajv.js.org/)

---

## 👤 Author

**Hoàng Đỉnh** — QA / API Test Engineer

---

*This project is designed for portfolio demonstration purposes, showcasing advanced Postman scripting for real-world API validation.*
 
