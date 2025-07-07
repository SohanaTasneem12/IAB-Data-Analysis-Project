### **Data Analysis & Interactive Dashboard Documentation**

### **📝 Documentation Log Entry**

### **🛠️ Issue Log: Duplicate Records Handling**

During the data cleaning stage, it was observed that **two entries had identical company names and related details** but **different IAB Accreditation IDs**. Upon closer inspection, these records were treated as **distinct entities**, considering the possibility that they represent:

* Different branches, or  
* Separate legal registrations of the same company.

✅ **Action Taken:** Both records were retained in the dataset to maintain the integrity of officially registered firms and to avoid potential data loss.

⚠️ **Note:** These entries were flagged in the cleaning log for transparency, but no further deduplication was applied since Accreditation ID is assumed to uniquely identify registered firms.

---

### **🛠️ Issue Log: Age Column Calculation**

**Issue Title :** Incorrect output in "Age" column (shows 1900--)

**Stage :** Data Cleaning / Calculated Columns

**Problem Description :** While calculating the age of companies based on the "Establish Year" column, the formula output showed incorrect values like 1900-- instead of a number. This happened even though the "Establish Year" values appeared numeric.

**Root Cause :** Google Sheets was interpreting the result as a **date** instead of a **number** because the "Age" column was mistakenly formatted as a **Date** type. When a number is treated as a date, small values (like 16 for years of experience) are converted into a date offset from January 1, 1900\.

**Solution Applied:**

1. Changed the **column format** of the "Age" column from **Date** to **Number**:  
    Format \> Number \> Number

Used the corrected formula:  
 \=IFERROR(YEAR(TODAY()) \- VALUE(H2), "")

2.  This also ensures that any non-numeric or empty values in "Establish Year" don’t break the calculation.

**Outcome :** The column now correctly shows the company's age (e.g., 16, 20, etc.), and the calculation works across all rows.

**Learning/Takeaway :** Always verify the **data format of output columns** when writing formulas involving numeric or date calculations. Output formatting can affect how formulas are rendered or interpreted in Google Sheets.

---

### **🛠️ Issue Log: Professionalism Score – Missing Values**

**Issue Title :** Incomplete records resulting in blank professionalism scores

**Stage :** Data Analysis / Scoring

**Problem Description :** During the calculation of the Professionalism Score (0–100 scale), it was observed that a number of records returned blank results. This was due to missing data in one or more of the key input columns:

* Google Rating  
* Website Status  
* Email Type  
* Company Age

**Root Cause :** The scoring formula requires complete data for all four input variables. If any of the inputs were blank (e.g., missing rating or website info), the formula returned an empty cell to avoid inaccurate or misleading scoring.

**Solution Applied :** Choose a conservative and accurate approach by **leaving the Professionalism Score blank** for records with incomplete data. The scoring logic is as follows:

* Ratings were multiplied by 10 and weighted at 40%  
* Website presence contributed 20% (Yes \= 20, No \= 0\)  
* Professional email contributed 20% (Professional \= 20, Personal \= 0\)  
* Age was capped at 20 years \= 20%

**Formula Used:**

\=IF(OR(K2="", L2="", M2="", E2=""), "",   
  MIN(100,   
    (K2 \* 10 \* 0.4) \+   
    (IF(L2="Yes", 20, 0)) \+   
    (IF(M2="Professional", 20, 0)) \+   
    MIN(E2, 20\)  
  )  
)

**Outcome :** Only records with complete and reliable data received a professionalism score. This approach ensures that the metric remains meaningful and avoids artificially inflated or deflated values.

**Learning/Takeaway :** In predictive scoring or metric construction, it’s critical to prioritize data integrity. Skipping incomplete records protects the quality of analysis, especially when the score influences business insights or rankings.

---

### **🛠️ Issue Log: Estimated Projects Completed Calculation**

**Objective Title :** Estimate Total Projects Completed

**Stage :** Data Analysis / Predictive Estimation

**Problem Addressed :** There is no actual data on the number of projects completed by each company. A logical estimation method was required to provide insight into company productivity over time.

**Solution Applied :** A **basic assumption model** was developed based on two factors:

* **Company Age** (in years)

* **Google Rating** (scale of 0–5)

Assumed average project completion rate \= **3 projects per year**, adjusted by a performance multiplier derived from Google Ratings.

**Formula Used:**

\=IF(AND(E2\<\>"", K2\<\>""), ROUND(E2 \* 3 \* (K2 / 5), 0), "")

**Logic Summary:**

* Multiplies years in business by a base rate (3 projects/year)  
* Scales by Google Rating (as a proxy for productivity)  
* Leaves cell blank if either Age or Rating is missing

**Outcome :** Estimated values give a reasonable picture of project experience. This helps in comparative analysis, rankings, and dashboard summaries.

**Learning/Takeaway :** Even without exact project counts, we can create useful, data-driven estimates using structured assumptions and available quality indicators.

### ---

#### **🛠️ Issue Log: Estimating Companies That Charge Higher or Lower Fees**

**Objective Title :** Estimate Pricing Category Based on Location

**Stage :** Predictive Classification

**Problem Description :** There is no actual fee or cost data in the dataset. The instructor recommended using company **location** (Thana) as a proxy for pricing tendencies.

**Solution Applied :** Locations were categorized into three tiers based on business area prestige and socioeconomic indicators:

* **High Fee Areas:** Gulshan, Banani, Baridhara, Dhanmondi  
* **Medium Fee Areas:** Uttara, Mirpur, Mohammadpur, Motijheel, Badda, Tejgaon  
* **Low Fee Areas:** Outside Dhaka, Gazipur, Savar, Narayanganj, and other remote regions

A new column was added using a lookup logic (IFS function in Google Sheets) to assign each company a likely **Fee Tier**: High, Medium, or Low.

**Formula Used:**

\=IFS(  
  OR(J2="Gulshan", J2="Banani", J2="Baridhara", J2="Dhanmondi"), "High",  
  OR(J2="Uttara", J2="Mirpur", J2="Mohammadpur", J2="Motijheel", J2="Tejgaon", J2="Badda"), "Medium",  
  ISBLANK(J2), "",  
  TRUE, "Low"  
)

**Learning/Takeaway :** Location-based assumptions are a powerful and acceptable method when exact financial data is missing. Categorizing pricing based on geographic status allows for realistic market segmentation insights.

---

### **📝 Dashboard Log Entry**

### **✅ What Was Done in Looker Studio**

1. **Connected Cleaned Dataset**

   * Integrated the final analyzed Google Sheets data with Looker Studio for dynamic visual reporting.

2. **Set Canvas to Widescreen**

   * Changed the canvas to widescreen format for optimal layout and presentation space.

3. **Created Scorecards for Key Metrics**

   * Total IAB-registered companies.  
   * Total companies inside Dhaka.  
   * Companies with professional emails and active websites.

4. **Categorical Bar & Pie Charts**

   * Age group distribution of companies (Experience Buckets).  
   * Top 5 Police Stations with highest firm concentration.  
   * Project types (High-End Commercial, Aesthetic Apartment, Regular Construction).  
   * Estimated Fee Tiers by location (High, Medium, Low).

5. **Geographic Mapping with Bubble Map**

   * Displayed company locations using extracted Latitude and Longitude from the “Google Map Number” column.

6. **Interactive Filters / Controls**

   * Added drop-down filters for:

     * Police Station

     * Company Age Group

     * Fee Tier

     * Project Type

   * Enabled users to dynamically slice data for deeper insights.

7. **Clean Visual Design**

   * Used consistent colors, titles, and layouts for a cohesive dashboard experience.  
   * Made smart use of limited space by integrating filters instead of additional charts.

8. **Real-Time Sync with Google Sheets**

   * Any update in the source Google Sheet reflects instantly in the dashboard.

---

