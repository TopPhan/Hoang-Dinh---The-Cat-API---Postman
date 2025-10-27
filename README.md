🐈 The Cat API Testing Project - https://api.thecatapi.com/v1

📅 Execution Period: October 19, 2025 – October 26, 2025

👤 Tester: Hoàng Đỉnh

---
OVERVIEW

Hello! This project showcases how I use Postman scripting to take API
focusing on CRUD operations for image and breed data, ensuring stability and speed in complex workflows.

---
QUICK SETUP & DEMO


The Cat API specification here: https://documenter.getpostman.com/view/5578104/RWgqUxxh#8606c7c6-338e-46aa-8f1a-3335ed2b8127


1. Download & Import: Grab both the Collection (Hoàng Đỉnh - The Cat API.postman_collection.json) and the Environment (MY_SERVICE_API_KEY=DEV.postman_environment.json).

2. Select & Go: Ensure the MY_SERVICE_API_KEY=DEV Environment is selected.

3. Run Collection: Use the Collection Runner to execute the main Image Validation (CRUD) folder. The entire process will run and validate itself automatically!


---
PROJECT STRUCTURE

Hoàng Đỉnh - The Cat API.postman_collection.json: 	Main Postman collection with test scripts
MY_SERVICE_API_KEY=DEV.postman_environment.json:	Environment file with API key

Test Flow.xlsx:										Visual flowchart of the test logic

schemas/											JSON schema definitions

screenshots/										Screenshots of test results,variables,request...etc.


---
TEST FLOW LOGIC

The test suite follows a structured flow based on conditional execution:

1. **GET /breeds**
   - ✅ Status code 200
   - ✅ JSON schema validation for get all breeds
   - ✅ Save bread_id variable for later upload image request
   - ✅ Breed name, origin, and country code match expected values

2. **POST /images/upload**
   - ✅ Status code 201
   - ✅ JSON Schema validation for uploaded image
   - ✅ Save all response fields as `expected_*` variables, UploadImage_id(primary key) for later test.
   - ✅ If upload succeeds, move to next request. if it fails, stop the test.

3. **GET /images?limit=10**
   - ✅ Status code 200
   - ✅ Confirm uploaded image appears in response
   - ✅ Compare each field with `expected_*` variables by find UploadImage_id(primary key)
   - ✅ JSON Schema validation get my image
   - ✅ If UploadImage_id is found in my image, move to next request. if it fails, stop the test.

4. **GET /images/{image_id}**
   - ✅ Status code 200
   - ✅ Validate image detail data matches uploaded image
   - ✅ JSON Schema validation and field checks for get detail image
   - ✅ If image detail data matches uploaded image, move to next request. if it fails, stop the test.

5. **DELETE /images/{image_id}**
   - ✅ Status code 204 (no content)
   - ✅ Delete uploaded image
   - 🔁 Retry logic if deletion fails (max 3 attempts)
   - ✅ Confirm deletion success by retry **3.GET /images?limit=10**
         ->  UploadImage_id is not found in my image, test done !.


---
PERFORMANCE & RELIABILITY TECHNIQUES

To ensure speed and consistency, the project applies several techniques:

** Conditional Execution

    - Uses pm.execution.setNextRequest() to control flow based on test outcomes.
	
    - Stops execution immediately if critical steps fail (e.g., missing UploadImage_id).
	
** Dynamic Variable Management

	- Stores runtime data like UploadImage_id, base_url, api-key, etc.
	
	- Enables cross-request validation with high precision.
	
** Schema Validation with AJV

	- Uses the Ajv library to validate JSON responses against strict schemas.
	
	- Provides detailed error messages for debugging.
	
** Smart Retry Logic

	- Implements retry mechanism for DELETE requests (up to 3 attempts).
	
	- Improves reliability when API responses are inconsistent.
	
** Performance Checks

	- Validates response time : etc (< 500ms) and payload size etc (< 2KB).
	
	- Ensures the API remains fast and lightweight.
	
** Security Header Validation

	- Verifies headers like x-frame-options and x-content-type-options.
	
	- Ensures basic security compliance.

---
TEST SUMMARY

- Total tests executed: 36
- Execution time: ~10.6 seconds
- Average response time: ~1.6 seconds
- Pass rate: >90%
- Failures are logged with detailed messages and conditional halts.


---
SPRINT PLANNING & AGILE WORKFLOW

This project followed Agile with four focused sprints, each delivering key components of the automated testing suite:

Sprint 1 (19.10.25 - 20.10.25): API Exploration & Environment Setup
- Research The Cat API documentation
- Define test objectives and endpoints
- Configure Postman environment and variables
- Design test flow logic in Excel

Sprint 2 (21.10.25 - 24.10.25): Collection Design & Test Scripting
- Build Postman collection with CRUD requests
- Implement test scripts for each endpoint
- Validate schemas using Ajv
- Apply conditional execution and dynamic variables

Sprint 3 (25.10.25 - 25.10.25): Automation & Performance Validation
- Automate test sequence with Collection Runner
- Add response time and size checks
- Validate security headers
- Optimize retry logic and error handling

Sprint 4 (26.10.25 - 26.10.25): Documentation & Finalization
- Capture screenshots and test results
- Finalize README and test instructions
- Organize project files, schemas, and screenshots


---
NOTES:
- The project was conducted independently by a single tester, with the use of automation tools.
- Using automated testing tools:
- 
  _ Collection runner
  
  _ Postman Environment
  
  _ Ajv - "Another JSON Schema Validator"
 
