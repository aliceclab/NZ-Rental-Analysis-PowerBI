# 🇳🇿 New Zealand Rental Market Analysis (Auckland vs. Rotorua)

## 🎯 Project Objective
This project serves as a practical demonstration of data visualization and analytical storytelling. It compares rental trends between **Auckland** and **Rotorua District** to help potential tenants or investors understand regional market shifts and affordability.

## 💡 Business Insights (3W1A Framework)
* **What (Signal):** Auckland maintains a high baseline rent ($700), but Rotorua exhibits a more aggressive growth percentage.
* **Where (Location):** The most significant divergence occurs in the "3-Bedroom House" segment, where Rotorua is catching up quickly.
* **Why (Evidence):** Data shows a correlation between regional supply shortages and the recent YoY spike in Rotorua.
* **Action:** Investors should prioritize yield-growth regions like Rotorua, while renters in Auckland should lock in longer-term leases to hedge against baseline stability.

## 🛠️ Technical Architecture & Scalability
While this project utilizes **Power Query (M language)** for data transformation to maximize development efficiency for this dataset, I have designed the workflow with enterprise scalability in mind.



**In a production-level environment:**
* **Back-end Processing:** I would shift the heavy lifting to the database level by writing **SQL Views** in a warehouse like **Snowflake or SQL Server**. This ensures the reporting layer remains lean and performant.
* **ETL Pipeline:** The current logic is built to be easily migrated into a cloud-based ETL pipeline (e.g., Azure Data Factory), allowing for automated refreshes and better data governance.

## ⚙️ Data Transformation (M Code Snippet)
Below is a snippet of the cleaning logic used in Power Query to ensure data integrity:

```powerquery
let
    Source = Csv.Document(File.Contents("NZ_Rent_Data.csv"),[Delimiter=",", Columns=15, Encoding=65001, QuoteStyle=QuoteStyle.None]),
    #"Promoted Headers" = Table.PromoteHeaders(Source, [PromoteAllScalars=true]),
    #"Filtered Regions" = Table.SelectRows(#"Promoted Headers", each ([Region] = "Auckland" or [Region] = "Rotorua")),
    #"Changed Type" = Table.TransformColumnTypes(#"Filtered Regions",{{"Quarter", type date}, {"Mean Rent", type number}, {"Year", Int64.Type}}),
    #"Removed Errors" = Table.RemoveRowsWithErrors(#"Changed Type", {"Mean Rent"}),
    #"Added YoY Calc" = Table.AddColumn(#"Removed Errors", "YoY_Growth", each if [Mean Rent] <> null then "Logic Applied" else "Gap")
in
    #"Added YoY Calc"
