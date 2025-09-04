
## LOAN ANALYTICS DASHBOARD - COMPLETE DAX MEASURES COLLECTION

### **Author:** Nayan Mandal 
### **Project:** Advanced Loan Analytics Dashboard
### **Version:** 1.0
### **Description:** Comprehensive DAX measures for loan default prediction and analysis

## DASHBOARD 1: LOAN DEFAULT & OVERVIEW ANALYTICS

### 1. Average Income by Employment Type 
```
AVG Income by Employment Type = 
CALCULATE(
    AVERAGE('Loan_default'[Income]),
    ALLEXCEPT('Loan_default', 'Loan_default'[EmploymentType])
)
```
### 2. Average Loan Amount by Age Group 
```
AVG Loan by Age Group = 
AVERAGEX(
    VALUES('Loan_default'[Age Group]),
    AVERAGE('Loan_default'[LoanAmount])
)
```
### 3. Default Rate by Employment Type 
```
Default Rate by Employment Type = 
VAR TotalRecords = COUNTROWS(ALL('Loan_default'))
VAR DefaultCases = COUNTROWS(FILTER('Loan_default', 'Loan_default'[Default] = TRUE()))
RETURN
CALCULATE(
    DIVIDE(DefaultCases, TotalRecords),
    ALLEXCEPT('Loan_default', 'Loan_default'[EmploymentType])
) * 100
```
### 4. Default Rate by Year 
```
Default Rate by Year = 
VAR TotalLoan = 
    CALCULATE(
        COUNTROWS('Loan_default'),
        ALLEXCEPT('Loan_default', 'Loan_default'[Year])
    )
VAR DefaultCount = 
    CALCULATE(
        COUNTROWS(FILTER('Loan_default', 'Loan_default'[Default] = TRUE())),
        ALLEXCEPT('Loan_default', 'Loan_default'[Year])
    )
RETURN
DIVIDE(DefaultCount, TotalLoan) * 100
```
### 5. Total Loan Amount by Purpose 
```
Loan Amount by Purpose = 
SUMX(
    FILTER('Loan_default', NOT(ISBLANK('Loan_default'[LoanAmount]))),
    'Loan_default'[LoanAmount]
)
```

## DASHBOARD 2: APPLICANT DEMOGRAPHICS & FINANCIAL PROFILE

### 1. Average Loan Amount for High Credit Score 
```
AVG Loan Amount (High Credit Score) = 
AVERAGEX(
    FILTER('Loan_default', 'Loan_default'[Credit score Bins] = "High"),
    'Loan_default'[LoanAmount]
)
```
### 2. Count of Loans by Education Type
```
Loan Count by Education Type = 
COUNTROWS(
    FILTER('Loan_default', NOT(ISBLANK('Loan_default'[LoanID])))
)
```
### 3. Median Loan Amount by Credit Score Bins 
```
Median by Credit Score Bins = 
MEDIANX('Loan_default', 'Loan_default'[LoanAmount])
```
### 4. Total Loan Amount for Middle Age Adults 
```
Total Loan (Middle Age Adults) = 
SUMX(
    FILTER('Loan_default', 'Loan_default'[Age Group] = "Middle Age Adults"),
    'Loan_default'[LoanAmount]
)
```
### 5. Total Loan Amount by Credit Bins (Adults Only)
```
Total Loan (Credit Bins - Adults) = 
CALCULATE(
    SUM('Loan_default'[LoanAmount]),
    'Loan_default'[Age Group] = "Adults",
    ALLEXCEPT(
        'Loan_default',
        'Loan_default'[Age],
        'Loan_default'[Age Group],
        'Loan_default'[CreditScore],
        'Loan_default'[Credit score Bins]
    )
)
```

## DASHBOARD 3: FINANCIAL RISK METRICS

### 1. Year-over-Year Default Loan Change 
```
YOY Default Loan Change = 
VAR CurrentYearDefaults = 
    CALCULATE(
        COUNTROWS(FILTER('Loan_default', 'Loan_default'[Default] = TRUE())),
        'Loan_default'[Year] = YEAR(MAX('Loan_default'[Loan_Date_DD_MM_YYYY]))
    )
VAR PreviousYearDefaults = 
    CALCULATE(
        COUNTROWS(FILTER('Loan_default', 'Loan_default'[Default] = TRUE())),
        'Loan_default'[Year] = YEAR(MAX('Loan_default'[Loan_Date_DD_MM_YYYY])) - 1
    )
RETURN
DIVIDE(
    CurrentYearDefaults - PreviousYearDefaults,
    PreviousYearDefaults,
    0
) * 100
```
### 2. Year-over-Year Loan Amount Change 
```
YOY Loan Amount Change = 
VAR CurrentYearAmount = 
    CALCULATE(
        SUM('Loan_default'[LoanAmount]),
        'Loan_default'[Year] = YEAR(MAX('Loan_default'[Loan_Date_DD_MM_YYYY]))
    )
VAR PreviousYearAmount = 
    CALCULATE(
        SUM('Loan_default'[LoanAmount]),
        'Loan_default'[Year] = YEAR(MAX('Loan_default'[Loan_Date_DD_MM_YYYY])) - 1
    )
RETURN
DIVIDE(
    CurrentYearAmount - PreviousYearAmount,
    PreviousYearAmount,
    0
) * 100
```
### 3. Year-to-Date Loan Amount 
```
YTD Loan Amount = 
CALCULATE(
    SUM('Loan_default'[LoanAmount]),
    DATESYTD('Loan_default'[Loan_Date_DD_MM_YYYY]),
    ALLEXCEPT(
        'Loan_default',
        'Loan_default'[Credit score Bins],
        'Loan_default'[MaritalStatus]
    )
)
```

## ADDITIONAL SUPPORTING MEASURES

### Total Loans Count 
```
Total Loans = 
COUNTROWS('Loan_default')
```
### Total Default Loans 
```
Total Default Loans = 
COUNTROWS(FILTER('Loan_default', 'Loan_default'[Default] = TRUE()))
```
### Overall Default Rate 
```
Overall Default Rate = 
DIVIDE([Total Default Loans], [Total Loans]) * 100
```
### Average Interest Rate 
```
Average Interest Rate = 
AVERAGE('Loan_default'[InterestRate])
```
### Average DTI Ratio 
```
Average DTI Ratio = 
AVERAGE('Loan_default'[DTIRatio])
```
### Total Loan Portfolio Value 
```
Total Portfolio Value = 
SUM('Loan_default'[LoanAmount])
```
### Average Loan Amount 
```
Average Loan Amount = 
AVERAGE('Loan_default'[LoanAmount])
```
### Loan Amount Rank by Purpose 
```
Loan Amount Rank by Purpose = 
RANKX(
    ALL('Loan_default'[LoanPurpose]),
    [Loan Amount by Purpose],
    ,
    DESC
)
```

## TIME INTELLIGENCE MEASURES

### Previous Year Total Loan Amount 
```
Previous Year Loan Amount = 
CALCULATE(
    SUM('Loan_default'[LoanAmount]),
    PREVIOUSYEAR('Loan_default'[Loan_Date_DD_MM_YYYY])
)
```
### Month-over-Month Growth 
```
MOM Loan Growth = 
VAR CurrentMonth = SUM('Loan_default'[LoanAmount])
VAR PreviousMonth = 
    CALCULATE(
        SUM('Loan_default'[LoanAmount]),
        PREVIOUSMONTH('Loan_default'[Loan_Date_DD_MM_YYYY])
    )
RETURN
DIVIDE(CurrentMonth - PreviousMonth, PreviousMonth) * 100
```
### Rolling 12 Month Average 
```
Rolling 12M Average = 
AVERAGEX(
    DATESINPERIOD(
        'Loan_default'[Loan_Date_DD_MM_YYYY],
        MAX('Loan_default'[Loan_Date_DD_MM_YYYY]),
        -12,
        MONTH
    ),
    [Total Portfolio Value]
)
```

 ## PERFORMANCE OPTIMIZATION NOTES


### OPTIMIZATION TIPS:
- 1. Use CALCULATE instead of FILTER when possible for better performance
- 2. Store frequently used variables at the beginning of measures
- 3. Use ALLEXCEPT instead of ALL with multiple columns for cleaner code
- 4. Consider creating calculated columns for complex groupings
- 5. Use DIVIDE function to handle division by zero scenarios
- 6. Test measures with large datasets to ensure optimal performance

### BEST PRACTICES:
- 1. Always handle blank/null values appropriately
- 2. Use descriptive variable names for better readability
- 3. Comment complex logic for future maintenance
- 4. Consider creating measure tables for organization
- 5. Use proper data types for optimal storage and performance
