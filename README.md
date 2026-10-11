# Refresco Stock Level Power BI Dashboard

## Project Summary

An interactive **Microsoft Power BI dashboard** developed to analyse spare-parts withdrawals, logbook completeness, and data-quality issues across multiple storerooms.

The dashboard includes an **item-level drill-through page** that enables maintenance and operations managers, the store coordinator, and planner to investigate material usage, withdrawal history, work-order records, component-recording status, and supporting notes.

I independently developed this dashboard as an internal company project, demonstrating an end-to-end analytics workflow involving **Power Query, relational data modelling, DAX, interactive visualisations, KPI development, and evidence-based recommendations**.

> **Confidentiality note:**  
> This is an internal company project. The live dashboard, underlying company data, and detailed drill-through records cannot be publicly shared. Only selected screenshots and non-sensitive project information are included in this portfolio.

---

## Project Overview

The project was developed to transform Excel-based spare-parts withdrawal logbook data from three storerooms — **C01, C02, and C03** — into an interactive Power BI report.

The dashboard helps users:

- Identify material-number issues across **Registered, Consumable, No SAP Number, and Needs Review** categories.
- Review withdrawal records with missing or incomplete information across all three storerooms.
- Compare recorded work orders and component information against missing or exception categories.
- Monitor changes in logbook completeness over time.
- Review material-level withdrawal summaries, including work-order status, component status, storeroom, and withdrawal frequency.
- Drill through to a selected material to examine usage, withdrawal history, work-order records, component status, staff activity, location information, and notes.

As of **10 October 2026**, the dashboard contains **304 withdrawal entries** and will continue to grow as new withdrawal records are added.

Users can filter the dashboard by:

- Withdrawal Date
- Staff Initials
- Storeroom

The findings in this README represent the overall dashboard results when no filters are applied.

---

## Power BI Dashboard

A downloadable `.pbix` file or public access link is not provided because the dashboard was developed using internal company data.

This restriction helps protect confidential operational information and prevents unauthorised access to company records.

### Overview Page

The **Overview** page provides a high-level view of spare-parts withdrawals, record statuses, and logbook completeness.

**Key metrics and visuals:**

- Withdrawal Entries
- WO Recorded %
- TECO Recorded %
- Component Recorded %
- Material Number Issue Reasons
- Withdrawals Missing Information by Storeroom
- WO Recorded vs Other Categories
- Component Recorded vs Other Categories
- Logbook Completeness Trend
- Item Withdrawal Summary

![Spare Parts Withdrawals Dashboard Overview](Refresco%20Stock%20Level%20%28Overview%20page%29.pbix.png)

### Item Withdrawal Detail Page

The **Item Withdrawal Detail** page allows users to investigate an individual material after drilling through from the Overview page.

**Key metrics and visuals:**

- Total Quantity Taken
- Work Orders Recorded
- Components Recorded
- Distinct Staff Initials
- Who Usually Takes This Item?
- Log Completeness for This Item
- Withdrawal History

![Item Withdrawal Detail](Refresco%20Stock%20Level%20%28Item%20Details%20page%29.pbix.png)

### Interactive Filters

The dashboard can be explored using:

- Withdrawal Date
- Initials
- Storeroom

Visuals cross-filter one another, allowing users to investigate specific withdrawal patterns, storerooms, staff activity, and individual materials.

---

# Data Preparation

## 1. Data Connection and Consolidation

The source workbook is stored in **SharePoint** and contains three worksheets:

- C01
- C02
- C03

Each worksheet represents a separate storeroom.

The workbook was connected to Power BI using:

**Get Data → SharePoint Folder**

The root SharePoint site URL was used instead of the direct workbook link, and authentication was completed using an **Organisational Account**.

Before combining the worksheets:

- Column names were standardised.
- Data types were standardised.
- A `Storeroom` column was added to each query using its worksheet name.
- C01, C02, and C03 were combined using **Append Queries as New** to create one consolidated `Withdrawals` table.

If the worksheet name is unavailable, the storeroom can also be derived from the location bin:

| Location Bin Prefix | Storeroom |
|---|---|
| 1 | C01 |
| 2 | C02 |
| 3 | C03 |

---

## 2. Initial Dataset Profile

The initial dataset contained:

| Area | Initial State |
|---|---:|
| Total withdrawal records | 265 |
| C01 | 60 |
| C02 | 150 |
| C03 | 55 |
| Recorded period | 18 May–21 September 2026 |
| Distinct material labels | 202 |
| Distinct work-order values | 186 |
| Exact duplicates | 0 |

The three worksheets had similar structures and strong completeness across the main fields, including withdrawal date, record date, material number, staff name, location bin, and work order.

---

## 3. Main Data-Quality Issues

Several issues had to be addressed before the data was suitable for analysis.

### Mixed quantity formats

The quantity field contained both numeric and text values, including examples such as:

- `9 meter`
- `14 LINKS`
- `CONSUMABLES`
- `??`

These entries prevented reliable calculations.

**Approach:**  
Numeric quantities were separated from units of measure and free-text comments.

### Invalid or inconsistent dates

Examples included:

- `30/02/2026`
- Dates entered in an incorrect sequence

**Approach:**  
Invalid or suspicious dates were flagged for verification.

### Inconsistent material-number entries

The material-number field contained valid SAP material numbers alongside descriptive labels such as:

- `CONSUMABLE`
- `NO AU NUMBER`
- `NOT STOCK ITEM`

**Approach:**  
Separate cleaned material-number and status fields were created.

### Mixed work-order entries

The work-order field contained both numeric work orders and operational comments such as:

- `PROJECT`
- `SHUTDOWN`
- `CANNOT OPEN WO`

**Approach:**  
Valid work orders were separated from exception categories.

### Inconsistent TECO and Component fields

Fields contained:

- YES
- NO
- blanks
- comments

**Approach:**  
Values were standardised into consistent categories for analysis.

### Inconsistent staff names and initials

Different names and initials sometimes referred to the same employee.

**Approach:**  
Staff records were mapped to standardised initials.

### External lookup dependencies

Some Excel formulas relied on another workbook, creating a refresh risk.

**Approach:**  
Required transformations were moved into the Power BI data-preparation process where possible.

### Embedded workbook images

More than 200 embedded images increased workbook size and contributed to import errors.

**Approach:**  
The analytical workflow focused on structured tabular data rather than embedded workbook objects.

---

## 4. Cleaning and Transformation

Separate cleaned fields were created for identifiers and their statuses.

### Material Number

Created fields:

- `Material Number`
- `Material Number Status`

Material-number statuses were grouped into:

- Registered
- Consumable
- No SAP Number
- Not a Stock Item
- Needs Review

### Work Order

Created fields:

- `Work Order`
- `WO Status`

Work-order statuses were grouped into:

- Recorded
- Missing
- Project
- Shutdown
- CCEP Trial
- Cannot Open WO
- Needs Review

### Quantity and UOM

Quantity values were separated into:

- Numeric Quantity
- Unit of Measure

### Staff

Staff names and initials were mapped into standardised staff identifiers.

### Withdrawal ID

A unique `Withdrawal ID` was created for each withdrawal transaction.

---

# Data Model

The model organises information into related subject areas.

### Withdrawals

The central transaction table containing each withdrawal event, including:

- Withdrawal dates
- Quantity taken
- Quantity in stock
- Work-order details
- Component status
- Staff initials
- Notes
- Item ID
- Location ID

### Items

Contains material-level information such as:

- Item ID
- Material Number
- Description
- Manufacturer Part Number (MPN)
- Material Number Status

### Storage Location

Contains location information such as:

- Location ID
- Location Bin
- Storeroom

### Completeness Fields

A supporting table used to display and sort completeness measures across fields such as:

- Work Order
- Component
- TECO
- Location Bin

### Relationships

`Item ID` and `Location ID` connect the dimension tables to the `Withdrawals` table using **one-to-many relationships**.

This structure allows item and storeroom filters to update withdrawal metrics and report visuals consistently.

---

# Key Data Insights

## Overall Withdrawal and Record Completeness

The dashboard currently contains **304 spare-parts withdrawal entries**.

| KPI | Result |
|---|---:|
| Work Order Recorded | 93.09% |
| TECO Recorded | 91.78% |
| Component Recorded | 78.62% |

Work-order and TECO recording are relatively complete, while component information has a noticeably lower completion rate.

This suggests that **component recording is the main logbook completeness issue** identified in the current dashboard.

---

## Material Number Data Quality

Most withdrawal entries contain a valid registered material number.

| Material Number Status | Entries |
|---|---:|
| Registered | 285 |
| Consumable | 12 |
| No SAP Number | 6 |
| Needs Review | 1 |

Registered materials represent the large majority of records.

However, consumables, items without SAP numbers, and records requiring review remain important because they may require different inventory or investigation processes.

---

## Missing Information by Storeroom

The number of withdrawal entries requiring investigation differs across the three storerooms.

| Storeroom | Entries to Investigate |
|---|---:|
| C01 | 7 |
| C02 | 31 |
| C03 | 35 |

C03 currently records the highest number of incomplete or problematic entries, followed closely by C02.

However, these counts should be compared with total withdrawal volume before concluding that one storeroom has poorer recording practices.

---

## Work Order Recording

The majority of withdrawals contain a recorded work order.

| WO Status | Entries |
|---|---:|
| Recorded | 283 |
| Missing | 15 |
| Needs Review | 2 |
| Project | 2 |
| CCEP Trial | 1 |
| Shutdown | 1 |

The high number of recorded work orders indicates generally strong documentation.

The exception categories also help distinguish valid operational circumstances from genuinely incomplete records.

---

## Component Recording

Component information is less complete than work-order information.

| Component Status | Entries |
|---|---:|
| Yes | 239 |
| No | 48 |
| Blank | 16 |
| Cannot Open WO | 1 |

Although most withdrawals have component information recorded, approximately one-fifth of records fall outside the `Yes` category.

This makes component recording a more significant data-quality issue than work-order recording.

---

## Logbook Completeness Trend

The dashboard shows that logbook completeness changes across the recorded months.

Completeness is relatively high in earlier periods but declines toward **September and October**, with October currently showing the lowest level.

This trend should be interpreted carefully because:

- Monthly withdrawal volumes differ.
- Recent records may still be awaiting review.
- Some information may not yet have been available when the withdrawal was first entered.

---

## Withdrawal Investigation Summary

The withdrawal summary combines:

- Material Number
- Work Order Status
- Component Status
- Storeroom
- Withdrawal Count

This allows users to identify incomplete or unusual records and drill through to the **Item Withdrawal Detail** page for deeper investigation.

---

# Recommendations

## 1. Improve Component Recording

Component information has the lowest completion rate at **78.62%**, with:

- 48 entries recorded as `No`
- 16 blank entries

Recommended actions:

- Reinforce the requirement to record component information when parts are withdrawn.
- Review entries marked `No` or left blank.
- Clarify when component information is genuinely not applicable.
- Track component completion rates over time.

---

## 2. Reduce Missing Work Order Information

Work-order recording is strong at **93.09%**, but 15 entries are still missing a work order.

Recommended actions:

- Require a work order wherever applicable.
- Review missing and unclear work-order entries regularly.
- Separate valid operational exceptions from incomplete records.
- Follow up when repeated recording issues occur.

---

## 3. Prioritise C02 and C03 for Data-Quality Review

Entries requiring investigation:

- C01: 7
- C02: 31
- C03: 35

C02 and C03 should receive greater attention during data-quality reviews.

Further analysis should compare issue counts against total withdrawal volume to determine whether the higher counts reflect greater usage or lower recording completeness.

---

## 4. Review Material Number Exceptions

The dashboard currently identifies:

- 12 consumable entries
- 6 entries with no SAP number
- 1 entry requiring review

These records should be checked to determine whether they represent:

- Valid consumables
- Valid non-stock items
- Items requiring SAP material master creation
- Incorrectly recorded material numbers

---

## 5. Investigate the Decline in Logbook Completeness

The completeness trend declines noticeably toward September and October.

Recommended actions:

- Review recent withdrawal entries for missing information.
- Check whether entries are being recorded before all details are available.
- Identify whether particular staff, storerooms, or processes contribute more frequently to incomplete records.
- Monitor the trend monthly.

---

## 6. Use Drill-Through for Item-Level Investigation

The Item Withdrawal Detail page can be used to investigate specific materials.

Users can review:

- Total quantity withdrawn
- Work-order recording
- Component recording
- Staff involved
- Withdrawal history
- Location bin
- Stock information
- Notes

This helps determine whether a data-quality issue is an isolated case or a recurring pattern.

---

## 7. Establish Regular Logbook Data-Quality Monitoring

The dashboard should be reviewed routinely rather than only when a problem is discovered.

Regular monitoring could focus on:

- Work-order completion
- TECO completion
- Component completion
- Material-number exceptions
- Storerooms with high investigation counts
- Recent changes in logbook completeness

This would allow issues to be corrected earlier and improve the reliability of withdrawal records used for inventory and maintenance analysis.

---

# Conclusion

The dashboard shows that spare-parts withdrawal records are generally well maintained, particularly for **work orders and TECO information**, but several areas still require improvement.

Work-order recording is relatively strong at **93.09%**, while **91.78%** of entries contain TECO information. Component recording is lower at **78.62%**, making it the main completeness issue identified in the dashboard.

Material-number quality is also generally strong, with **285 of 304** withdrawal entries recorded as registered materials. A smaller number are classified as consumables, have no SAP number, or require further review.

Data-quality issues are more concentrated in **C02 and C03**, which contain 31 and 35 entries requiring investigation respectively, compared with 7 in C01.

The dashboard therefore supports a more proactive approach to spare-parts logbook management by helping users:

- Improve component recording.
- Reduce missing work orders.
- Review material-number exceptions.
- Investigate incomplete entries by storeroom.
- Monitor completeness trends.
- Perform item-level investigation through drill-through analysis.

---

# Data and Analysis Limitations

- This is an internal company project, so the live dashboard, underlying company data, and detailed drill-through records cannot be publicly shared.
- The current dashboard contains 304 withdrawal entries and therefore represents the available logbook records rather than all spare-parts activity across the business.
- The dashboard covers storerooms C01, C02, and C03 only.
- The analysis focuses primarily on logbook completeness and data quality rather than overall inventory or maintenance effectiveness.
- Missing information may result from incomplete recording, unavailable work orders, non-stock items, consumables, or operational exceptions.
- Work-order and material-number categories are based on defined transformation rules, so unusual entries may still require manual review.
- Monthly completeness trends should be interpreted carefully because withdrawal volumes differ by month and recent records may still be incomplete.
- Higher investigation counts in C02 and C03 do not necessarily indicate poorer recording performance because these storerooms may also have higher withdrawal volumes.
- Component completeness measures whether component information was recorded; it does not validate whether the recorded component is technically correct.
- Detailed drill-through analysis cannot be publicly demonstrated using actual company records due to confidentiality.
- Dashboard results should be used as a monitoring and investigation tool, with unusual or incomplete records verified against SAP and the original operational records before action is taken.
- The dashboard is currently live, while further development is underway to integrate data more directly with SAP and reduce the need to manage information across multiple systems.

---

# Future Improvements

The following improvement opportunities are based on feedback from the **National Reliability Manager**.

## Inventory Visibility

- Add total inventory value.
- Track the number of items with zero stock.
- Identify zero-stock items without an active purchase order.
- Monitor generated purchase requisitions that do not yet have a corresponding purchase order.
- Identify non-moving or dead stock to support inventory optimisation and reduce unnecessary stock holding.

## Cycle Counting

- Track the number of items counted during cycle counting.
- Track the number of cycle count results posted in SAP.
- Monitor the quantity of items due for cycle counting.
- Include inventory variance details to identify differences between physical and system stock.
- Analyse stock adjustments using SAP movement types **711** and **712** to monitor inventory gains and losses identified during cycle counting.

---

# Tools and Skills Demonstrated

- Power Query & DAX
- Data cleaning and transformation
- Relational data modelling
- Dashboard design and data visualisation
- KPI development
- Inventory and spare-parts analysis
- Data-quality and logbook completeness analysis
- Work-order and component-record analysis
- Drill-through and item-level analysis
- Process improvement insights

---

# Repository Contents

```text
Refresco-Spare-Parts-Withdrawals-Dashboard/
├── Refresco Stock Level (Overview page).pbix(1).png
├── Refresco Stock Level (Item Details page).pbix(1).png
└── README.md
```

> The original Power BI file and underlying company dataset are not included in the public repository due to confidentiality.

---

# Author

**Bao Nghi Ngo**  
Business Analytics and Information Systems Graduate

Interested in:

- Business Analysis
- Data Analytics
- Process Improvement
- Dashboard Development
- Operational Analytics
