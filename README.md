# Kantox QA Challenge

This repository contains the solutions to the QA challenge, including manual test design and API validation.

## 📁 Contents

- `Cashier_System_Test_Cases.md` – A comprehensive Markdown document with functional, regression, and edge case test scenarios designed for a simplified cashier system.
- `Kantox_API_Testing_Postman_Collection` – A Postman collection covering basic and edge API test cases related to product and rule validation.

## ✅ Task Overview

### 1. Manual Test Design
- Full test case matrix covering:
  - Promotional rule logic (FreeRule, ReducedPriceRule, FractionPriceRule)
  - Configuration edge cases (YAML handling)
  - Regression tests (logging, receipt, return policy, performance assumptions)

### 2. API Testing with Postman
- RESTful testing of endpoints simulating product scanning and rule application


## 🚀 How to Use

1. Open the Markdown file in any Markdown viewer to explore test coverage.
2. Import the Postman collection into your Postman workspace.


## 📌 Notes

- All assumptions are documented in the Markdown file.
- The system uses YAML instead of a database, and this has been considered in test strategy.

