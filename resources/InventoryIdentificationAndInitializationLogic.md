# 📦 Inventory: Search and Creation Logic

This section describes the logic behind two key methods responsible for **finding and creating inventory records** in the system.

---

## 🔧 Methods Overview

### 1. `firstOrCreateVerified(...)`

Responsible for both **searching and creating** inventory records.
> This method requires a `Seller` and `FNSKU`, which are not always available in all reports from Amazon APIs.

### 2. `firstRelevant(...)`

Responsible for **searching only**.
> Used when only `SKU` is available (not `FNSKU`) or when there is no seller context at the time of lookup.

---

## 🧠 `firstOrCreateVerified(...)`

```php
public function firstOrCreateVerified(
    Seller $seller,
    Marketplace $marketplace,
    string $fnsku,
    string $sku = null,
    string $asin = null
): Inventory
```

### Parameters:
- `seller` — the seller performing the action;
- `marketplace` — the marketplace (e.g., amazon.com);
- `fnsku` — Fulfillment Network SKU (required for inventory creation);
- `sku` — internal SKU of the product (optional);
- `asin` — Amazon’s unique product ID (optional).

---

### 🔍 Step-by-Step Logic

#### Step 1: Get Listing (if ASIN is provided)
```php
$listing = !empty($asin) ? $this->getListing($seller, $marketplace, $asin) : null;
```
- **Why:** We try to find or create a listing for the given ASIN on Amazon.
- **Purpose:** Allows linking the inventory record to an official Amazon listing.

#### Step 2: Get Product (if SKU is provided)
```php
$product = !empty($sku) ? $this->getProduct($seller, $sku, $listing) : null;
```
- **Why:** If SKU is present, we find or create a product for the company.
- **Purpose:** So the inventory can be tied to a concrete product.

#### Step 3: Use Parent Marketplace (if exists)
```php
$inventoryMarketplace = $marketplace->getParentMarketplace() ?? $marketplace;
```
- **Why:** Use the unified parent marketplace (e.g., Amazon EU) when applicable.
- **Purpose:** Avoid duplication across regional marketplaces.

#### Step 4: Find Inventory by FNSKU and Product
```php
if ($product !== null) {
    $inventory = $this->inventoryRepository->firstRelevantWithProduct(...);
```
- **Why:** This is the most precise search: product + fnsku.
- **Purpose:** Reuse existing records when possible.

#### Step 5: If not found — search by FNSKU without Product
```php
    if ($inventory === null) {
        $inventory = $this->inventoryRepository->firstRelevantWithoutProduct(...);
        if ($inventory !== null) {
            $inventory->setProductId($product->getId())->save();
        }
    }
}
```
- **Why:** Fallback if record exists but isn’t linked to a product yet.
- **Purpose:** Auto-link to fill the gap.

#### Step 6: If no Product at all — search just by FNSKU
```php
} else {
    $inventory = $this->inventoryRepository->firstRelevant(...);
}
```
- **Why:** We search without product context.
- **Purpose:** Better to return something than nothing.

#### Step 7: If still not found — create Inventory
```php
if ($inventory === null) {
    $inventory = $this->inventoryRepository->firstOrCreate(array_filter([...]));
}
```
- **Why:** All lookups failed — we create a new record.
- **Note:** `array_filter()` avoids saving empty values.

#### Step 8: Link Listing (if not already linked)
```php
if ($inventory->getListingId() === null && $listing !== null) {
    $inventory->setListingId($listing->getId());
    $inventory->setAsin($listing->getAsin());
    $inventory->save();
}
```
- **Why:** Ensure the inventory links to the correct listing.

#### Step 9: Mark Inventory as Verified (if all data matches)
```php
if (!$inventory->isVerified()
    && !empty($inventory->getAsin())
    && !empty($inventory->getFnsku())
    && $inventory->getAsin() === $asin
    && $inventory->getFnsku() === $fnsku
) {
    $inventory->setVerified(true);
    $inventory->save();
}
```
- **Why:** Ensure quality and trust in inventory data.

#### Step 10: Return the Inventory
```php
return $inventory;
```

---

## 🔍 `firstRelevant(...)`

```php
public function firstRelevant(
    Company $company,
    Marketplace $marketplace,
    string $skuOrFnsku
): ?Inventory
```

### Purpose:
Find the most relevant inventory record using either SKU or FNSKU.  
Used when only limited product context is available (e.g., no seller, no FNSKU).  
This method can also establish product relationships if inconsistencies are found.

---

### 🔎 Step-by-Step Logic

#### Step 1: Use Parent Marketplace
```php
$inventoryMarketplace = $marketplace->getParentMarketplace() ?? $marketplace;
```
- **Why:** Normalize across child marketplaces.

#### Step 2: Get Product (or create one if missing)
```php
$product = $this->productService->firstRelevantOrCreate($company, $skuOrFnsku);
```
- **Why:** Interprets the input as either SKU or FNSKU and guarantees a product exists.

#### Step 3: Search for Inventory by SKU
```php
$inventory = $this->inventoryRepository->firstRelevantBySku(...);
```
- **Why:** First attempt to find inventory by clean product SKU.

#### Step 4: Return immediately if found
```php
if ($inventory !== null) {
    return $inventory;
}
```

#### Step 5: Fallback — more flexible inventory lookup
```php
$inventory = $this->inventoryRepository->firstRelevant(...);
```
- **Why:** Handles edge cases (e.g., inventory not directly tied to the product).

#### Step 6: If mismatch — assign product as a variation (child)
```php
if ($inventory !== null
    && $inventory->getProductId() !== null
    && $inventory->getProductId() !== $product->getId()
    && $product->getParentId() === null
    && $product->getSku() === $skuOrFnsku
) {
    $product->setParentId($inventory->getProductId())->save();
}
```
- **Why:** Fixes inconsistencies by nesting the current product under an existing one.

#### Step 7: Return Inventory
```php
return $inventory;
```

---

## 🧩 Additional Notes

- Both methods rely on helper methods that internally create or resolve `Product` and `Listing` records as needed.
- `firstOrCreateVerified()` is the most complete and assertive method, but it **requires more context (like `Seller`)**.
- `firstRelevant()` is safer to use in uncertain environments, such as **partial API responses** or **data repair pipelines**.
