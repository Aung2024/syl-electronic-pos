# Electronics Shop POS Concepts

This project is a mobile-responsive web POS and business management app for a medium-sized electronics shop. It is deployed as a static frontend on Netlify and uses Firebase Auth + Firestore for backend data.

## Scope

- English-only UI for v1.
- Two roles: Admin and Sales.
- Barcode-based product scanning using Code128/EAN-13 compatible values.
- Purchase / supplier stock-in, credit receivables and payables, expenses, reporting, and thermal receipts are included.
- Offline mode, Myanmar language UI, multi-branch, SMS, and accounting export are out of scope for v1.



## Roles

- Admin: can manage products, pricing, FX, purchases, credits, expenses, reports, and POS.
- Sales: can use POS, scan products, add/remove cart items, complete sales, and print receipts.



## Core Data Model

Firestore collections:

- `users`: user profile and role.
- `products`: product catalog and stock.
- `settings`: app settings such as FX, base FX, rounding, and margin bands.
- `sales`: sale header records.
- `saleItems`: individual product rows for each sale.
- `suppliers`: supplier records.
- `purchases`: purchase / stock-in records.
- `credits`: receivables and payables.
- `creditPayments`: partial payment history.
- `expenses`: categorized business expenses.
- `expenseCategories`: expense category names.



## Product Fields

- `type`: `HA` for Household Appliances or `IA` for Industry Appliances.
- `name`
- `sku`: auto-generated internal code using `TYPE-NAME-SERIAL-YEAR` (example: `HA-LIGHTBULB-001-26`).
- `barcode`: auto-generated numeric code used for POS scanning and label printing.
- `unit`: dropdown values such as `pcs`, `ft`, `m`, `roll`, `box`, `set`, `pair`.
- `cost`: weighted average unit cost across stock batches.
- `cogs`: weighted average delivery/packing/handling cost per unit across stock batches.
- `marginPercent`: optional product-specific override.
- `price`: computed selling price in MMK.
- `stockQty`
- `active`

## Stock In From Products Screen

New products and restocks are saved from the Products screen.

When stock is added:

1. Enter unit cost for the current batch.
2. Enter batch COGS for the whole delivery/packing charge.
3. Enter qty to add.
4. Choose supplier (existing or new).
5. Choose payment type (`paid` or `payable`).

Batch COGS is divided by qty before averaging:

```text
BatchCogsPerUnit = BatchCOGS / Qty
AvgCost = weighted average of old stock cost and new batch unit cost
AvgCOGS = weighted average of old stock COGS per unit and new batch COGS per unit
LandedCost = AvgCost + AvgCOGS
```

Example: 10 bulbs at 5,000 MMK with 5,000 MMK taxi charge becomes 5,500 MMK landed cost per bulb.

## Auto Pricing

Formula:

```text
LandedCost = Cost + COGS
SellingPrice = round( LandedCost * (1 + MarginPercent / 100) * (CurrentFX / BaseFX) )
```

Margin source:

1. Product margin override if set.
2. Matching margin band from settings if product override is empty.
3. Default margin from settings.

Rounding is controlled by settings, usually nearest 100 or 1,000 MMK.

## Suppliers

- Supplier CRUD lives in the Suppliers screen.
- Purchase history is shown there as a read-only log.
- Product stock-in also creates supplier and purchase records when qty is added.

## Barcode Strategy

- `sku` is the internal product code.
- `barcode` is the scannable value used at POS and on printed labels.
- Admin prints Code128 labels from product management.
- POS uses a focused barcode input so USB/Bluetooth scanners work as keyboard input.

## Receipt Printing

- Receipt rendering uses browser print with a 58mm/80mm print stylesheet.
- The client selects the installed thermal printer in the browser print dialog.
- Printer drivers and hardware setup are outside the app scope.



## Deployment Notes

- Add Firebase web app config in `assets/js/firebase-config.js`.
- Enable Firebase Authentication with email/password.
- Create user documents in `users` with role `admin` or `sales`.
- Deploy the static folder to Netlify.
- The client pays Firebase, Netlify, domain, and hardware costs separately.

