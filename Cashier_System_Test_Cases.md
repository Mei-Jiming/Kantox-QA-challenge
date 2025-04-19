# 🧪 Cashier System Test Cases

## 📝 Overview

This document contains a comprehensive list of test cases designed to validate the functionality and robustness of the **Cashier System** described below.

The goal is to:

- Ensure all **promotional rules** are correctly applied.
- Validate the system's behavior under **normal**, **edge**, and **error** conditions.
- Verify **file-based configuration** works as expected.

---

## 🛒 System Summary

**The system supports the following products and discount rule logic:**

| Product Code | Name         | Price  |
| ------------ | ------------ | ------ |
| GR1          | Green Tea    | £3.11  |
| SR1          | Strawberries | £5.00  |
| CF1          | Coffee       | £11.23 |

**Available Discount Rule Types:**

- **FreeRule**: Buy N, get N free
- **ReducedPriceRule**: Buy more than N, pay reduced unit price
- **FractionPriceRule**: Buy more than N, pay X% of original price

---

## ✅ Assumptions

- GR1 → FreeRule (Buy 1 Get 1 Free)  
- SR1 → ReducedPriceRule (>2 items, £4.50 each)  
- CF1 → FractionPriceRule (>3 items, 80% of £11.23 = £8.98)  

- We assume this product (CH1) exists in the system:
  - Product Code: CH1
  - Name: Chocolate
  - Price: £1.99
  - No promotion rule applies

---

## 1️⃣ Functional Test Cases

### GR1 - FreeRule

| TC ID | Description      | Input                              | Expected Total |
|-------|------------------|------------------------------------|----------------|
| TC01  | Buy 1 GR1        | `["GR1"]`                          | £3.11          |
| TC02  | Buy 2 GR1        | `["GR1", "GR1"]`                   | £3.11          |
| TC03  | Buy 3 GR1        | `["GR1", "GR1", "GR1"]`            | £6.22          |
| TC04  | Buy 4 GR1        | `["GR1", "GR1", "GR1", "GR1"]`     | £6.22          |

### SR1 - ReducedPriceRule

| TC ID | Description      | Input                              | Expected Total |
|-------|------------------|------------------------------------|----------------|
| TC05  | Buy 1 SR1        | `["SR1"]`                          | £5.00          |
| TC06  | Buy 2 SR1        | `["SR1", "SR1"]`                   | £10.00         |
| TC07  | Buy 3 SR1        | `["SR1", "SR1", "SR1"]`            | £13.50         |

### CF1 - FractionPriceRule

| TC ID | Description      | Input                              | Expected Total |
|-------|------------------|------------------------------------|----------------|
| TC08  | Buy 1 CF1        | `["CF1"]`                          | £11.23         |
| TC09  | Buy 3 CF1        | `["CF1", "CF1", "CF1"]`            | £33.69         |
| TC10  | Buy 4 CF1        | `["CF1", "CF1", "CF1", "CF1"]`     | £35.92(keep 2 digits after the decimal point,caculate the discount part first then time amount)        |

### Mixed Orders

| TC ID | Description                                | Input                                                             | Expected Total         |
|-------|--------------------------------------------|------------------------------------------------------------------|------------------------|
| TC11  | 2xGR1 + 3xSR1                              | `["GR1", "GR1", "SR1", "SR1", "SR1"]`                            | £3.11 + £13.50 = £16.61 |
| TC12  | 3xGR1 + 4xCF1                              | `["GR1", "GR1", "GR1", "CF1", "CF1", "CF1", "CF1"]`            | £6.22 + £35.92 = £42.14 |
| TC13  | 3xSR1 + 4xCF1              | `[ "SR1", "SR1", "SR1", "CF1", "CF1", "CF1", "CF1"]`                     | £13.50 + £35.92 = £49.42  |
| TC14  | GR1 + SR1 + CF1 with rules all applied + CH1   | `["GR1", "GR1", "SR1", "SR1", "SR1", "CF1", "CF1", "CF1", "CF1", "CH1"]` | £52.53 +  £1.99 = £54.52               |

---

## 2️⃣ Configuration Robustness Test Cases

| TC ID | Description                    | Input/Action                    | Expected Behavior                                                                 |
|-------|--------------------------------|---------------------------------|----------------------------------------------------------------------------------|
| TC15  | Missing rules.yml              | Delete/rename file              | System shows clear error                                                         |
| TC16  | Empty rules.yml                | Empty file                      | System loads with no discounts                                                   |
| TC17  | Invalid YAML syntax            | Misaligned indent or missing :  | System fails to load config, logs error or skips broken part if fallback exists |
| TC18  | Missing fields in rule         | No 'type' or invalid structure  | Rule ignored or error thrown, no crash                                           |
| TC19  | Modify rule and reload system  | Change promo logic + restart    | New rule applies after reload                                                    |

---

## 3️⃣ Regression Scenarios

### Payment Method Compatibility

| TC ID | Description                      | Payment Type    | Expected Result                     |
|-------|----------------------------------|-----------------|-------------------------------------|
| TC20  | Discounted item + Cash           | Cash            | Payment succeeds, Display discount details and payment information on the printed receipt  |
| TC21  | Discounted item + Credit Card    | Credit Card     | Payment succeeds, Display discount details and payment information on the printed receipt |
| TC22  | Discounted item + Mobile Pay     | Mobile Pay      | Payment succeeds, Display discount details and payment information on the printed receipt  |

### Logging / Summary Reporting(If the system includes logging)

| TC ID | Description                             | Input                    | Expected Behavior                                                                 |
|-------|-----------------------------------------|--------------------------|----------------------------------------------------------------------------------|
| TC23  | Log shows breakdown of discount per item| Mixed promo items        | Log lists item, quantity, rule type, discounted total                                |
| TC24  | End-of-day summary includes promo stats | Bulk purchase w/ promos | Summary includes promo totals, quantity, and impact                             |
| TC25  | Low stock triggers restock alert        | Buy 98 GR1, stock = 100  | Remaining = 2 → System logs and prompts restock                                 |

### Receipt Verification

| TC ID | Description                      | Input                  | Expected Receipt Content                                                |
|-------|----------------------------------|------------------------|-------------------------------------------------------------------------|
| TC26  | Receipt shows applied promos with tax info and barcode info     | GR1, SR1, CF1 purchase with discount rules| Line items show quantity, unit price, promo rule, and discounted subtotal, tax (until 04/2025 GR1:Green Tea is 10%, SR1:Strawberries is 4%, CF1:Coffee is 21% confirm 1.all these tax caculation is correct and 2.if the item is free shouldn´t be caculated)  and the barcord should be print out porperly|

### Return Policy

| TC ID | Description                        | Return Input        | Expected Result                                                |
|-------|------------------------------------|---------------------|-----------------------------------------------------------------|
| TC27  | Return 1 of 2 GR1 (BOGO)           | Return 1 GR1        | Scan the barcode and the info shows correctly and no refund (partial BOGO not eligible)                           |
| TC28  | Return 2 of 2 GR1                  | Return 2 GR1        | Scan the barcode and the info shows correctly and full refund as full set returned                               |
| TC29  | Return 1 of 3 SR1                  | Return 1 SR1        | Scan the barcode and the info shows correctly and refund based on discounted or original price                   |
| TC30  | Return 1 CF1                       | Return 1 CF1        | Scan the barcode and the info shows correctly and refund based on discounted or original price              |

---


