# Junkshop Buying and Selling Record System

Data source for an AppSheet app that records what a junkshop **buys** from sellers and **sells** to buyers.
Workbook: `JUNKSHOP_BUY__SELL.xlsx` (6 sheets: 1 notes sheet and 5 data tables).

## Links

- [Open the AppSheet app](https://www.appsheet.com/Template/AppDef?appName=NewApp-956409098-26-09-19-4&utm_source=share_app_link)
- [Open the Google Sheet](https://docs.google.com/spreadsheets/d/1HS6fw9gV9hfNlGpMxpWHoBICqtFChjMfjcPN-4Z23e4/edit?usp=sharing)

## Tables

| Sheet | Rows | Key | Ref columns |
|---|---|---|---|
| `Sellers` | 27 | `SellerID` | none |
| `MaterialTypes` | 10 | `MaterialID` | none |
| `PurchaseTransactions` | 22 | `TransactionID` | `SellerID` → Sellers, `MaterialID` → MaterialTypes |
| `PurchaseItems` | 47 | `ItemKey` (format `TransactionID-MaterialID`) | `TransactionID` → PurchaseTransactions, `MaterialID` → MaterialTypes |
| `SalesTransactions` | 25 | `SaleID` | `MaterialID` → MaterialTypes |

Row counts exclude the header rows. The `README` sheet inside the workbook holds shorter notes on the same layout.

## Columns

| Sheet | Columns |
|---|---|
| `Sellers` | `SellerID`, `Name`, `ContactNumber` |
| `MaterialTypes` | `MaterialID`, `MaterialName`, `BuyPricePerKilo`, `SellPricePerKilo` |
| `PurchaseTransactions` | `TransactionID`, `SellerID`, `MaterialID`, `TransactionDate` |
| `PurchaseItems` | `ItemKey`, `TransactionID`, `MaterialID`, `WeightKilos` |
| `SalesTransactions` | `SaleID`, `MaterialID`, `WeightKilos`, `Quantity`, `SaleDate`, `BuyerName` |

`MaterialTypes` lists 10 materials (`M01`–`M10`): Aluminum Cans, Copper Wire, Scrap Iron, PET Plastic Bottles, Cardboard, Scrap Steel, Brass, Used Lead Batteries, Newspaper, Tin Cans. Prices are per kilo. In every material the sell price is higher than the buy price.

## How the tables relate

- One seller can have many purchase transactions (`PurchaseTransactions.SellerID` → `Sellers`).
- One purchase transaction can have many items (`PurchaseItems.TransactionID` → `PurchaseTransactions`).
- Each purchase item, purchase transaction and sale refers to one material (`MaterialID` → `MaterialTypes`).
- `SalesTransactions` is not linked to purchases. It only records what was sold, to whom, and when.

## Entity relationship diagram

![Junkshop Buy and Sell Record System ERD](erd.png)

<img src="VENTOZILLADA_AiraShane_Final_ERD.drawio (1).png">

The diagram shows these one-to-many (1:M) relationships:

- `Sellers` 1 → M `Purchased Transactions`
- `Purchased Transactions` 1 → M `Purchased Items`
- `MaterialTypes` 1 → M `Purchased Items`
- `MaterialTypes` 1 → M `SalesTransaction`

In the diagram, `Purchased Transactions` has no `MaterialID`, and `Purchased Items` uses `TransactionID` + `MaterialID` together as its key. The workbook differs on both points (see Known data problems).

## Workbook conventions

- Row 1 holds column names. Use them exactly as the AppSheet column names.
- Row 2 is meant for the AppSheet data type and role. Only `Sellers` has it filled in (`Text — KEY`, `Text`, `Phone`). It is blank in the other four sheets.
- Yellow data cells mark the Key column. Blue data cells mark Ref columns.
- Sample data uses these ID formats: `S001`, `M01`, `T001`, `T002-M02`, `SL001`. Names, phone numbers and amounts are invented.
- Dates cover 2026-08-03 to 2026-10-03. Most are stored as text (`YYYY-MM-DD`) and a few as true dates.

## Setting up in AppSheet

1. Add each data sheet under **Data > Tables**.
2. Confirm each table's Key column (see the Tables section).
3. Set each Ref column's related table (see the Tables section).
4. Set column types: Phone for `ContactNumber`, Decimal for prices and weights, Date for `TransactionDate` and `SaleDate`.

## Known data problems

These are in the file as it stands. Fix them before using the data for real.

**Rows added by the app or by testing**

- `Sellers`: `S000` (Aira Shane ventozillada) and `78c4ecab` (Jayver Enero). `78c4ecab` has an auto-generated ID instead of the `S###` pattern.
- `PurchaseTransactions`: `2d90f1bc` and `ce437a64` (both dated 2026-10-03). They have auto-generated IDs and no matching rows in `PurchaseItems`.
- `PurchaseItems`: four rows whose `ItemKey` is not `TransactionID-MaterialID`: `MO1`, `M02` and `M03` (all under `T001`) and `M01` (under `T025`). Their weights are 0.1, 0.05, 0.05 and 0.11 kg. `M02` and `M03` both point to `T001` and `M02`. `T005-M04` also has a 0.11 kg weight, which looks like test data.
- `SalesTransactions`: `SL025` has a weight of 0.11 kg, a date of 2026-09-26 and the buyer "Aira Shane ventozillada".

**Broken references**

- 8 `PurchaseItems` rows point to transactions that do not exist in `PurchaseTransactions`. Seven are for `T021`–`T024`, and one is for `T025`. `PurchaseTransactions` only has `T001`–`T020` plus the two auto-generated IDs above.

**Structure**

- `SalesTransactions.Quantity` is empty in all 25 rows.
- `PurchaseTransactions` has a `MaterialID` column, but `PurchaseItems` already records the material per line. Of the 39 items whose transaction exists, only 6 have the same material as their transaction; 33 differ. The two columns contradict each other, so the `PurchaseItems` one should be treated as the real record.
- The workbook does not match the diagram. `PurchaseTransactions` has an extra `MaterialID` column, and `PurchaseItems` uses a single text `ItemKey` as its key instead of the `TransactionID` + `MaterialID` pair.
- There are no formulas anywhere. Totals such as amount paid (`WeightKilos` × `BuyPricePerKilo`) are not calculated yet.

## Data notice

Names and phone numbers are made up for practice. Do not enter real personal data without consent and proper storage.
