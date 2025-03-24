# Ferovinum AE take home task

## Setup and execution
There are no specialized python libraries (beyond PySpark) that I have used. Assuming the executing machine doesn't already have PySpark setup, I've included a `pip install` for it as part of my notebook. Setup on your end should just involve cloning my repo and executing it on jupyter / any other IDE that supports ipython notebooks.

## Assumptions
Based on visually inspecting the data, and the business questions, I've come up with the following assumptions.
* All transactions are recorded in chronological order. This means that, when I need to track inventory positions, I am simply adding up the `quantity` field ordered by the timestamp (which I have renamed as `snapshot_time`) until the current record. This also simplifies how I can track the start and end of month inventory positions.
* Realized Profit: This already exists as a column in this dataset, but I also confirmed that the logic here is simply `( Revenue - Cost )` for each `SELL` record.
* Finance datamart: I've interpreted the bonus point for this question as determining net inventory position _just_ for the set of top 3 SKUs for each month. This can easily be swapped to calculate net inventory for the whole dataset, by swapping out the input dataframe I use.
* Operations datamart: Since the exact calculation / logic for `inventory turnover rate` was not available, I relied on googling the definition of this metric. Among the results I got, I used the following definiton found [on this site](https://www.linnworks.com/blog/calculate-inventory-turnover/#:~:text=In%20addition%20to%20using%20financial,on%20hand%20=%20Inventory%20turnover%20rate), which is `UNITS SOLD / AVERAGE INVENTORY ON HAND`. I've also defined `AVERAGE INVENTORY ON HAND` as `(STARTING INVENTORY+ENDING INVENTORY)/2`. There were other definitions that used Cost of Goods Sold, Inventory value, etc., but since the point of this business question was inventory management, I've used the definition which references inventory quantities.

## Data Model
I've broken up the raw dataset into 4 dimensions (SKU, Product, Region, and Time), and 1 fact table for Transactions data. This should be agnostic of the business problem(s) for which I am performing my analyses.

![Data Model](fero_datamodel.png)

I _could_ further break down the product dimension into two separate dims: Product Category and Subvarietal, but this wouldn't really add any performance gains for a dataset as small as this (24 unique SKUs, ~100k total records).

Additionally, d_skus has two columns `grape_variety` and `grain_variety` that could potentially be condensed into one, since these appear to be mutually exclusive depending on the product type in question (Whisky, Bourbon, or Wine). Condensing these columns for a dataset of this scale would not result in significant performance gains, which is why I've left them as-is.


Thank you!
