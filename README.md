# Project Test Data Analysis

A comprehensive data analysis project that examines software testing processes, project timelines, and configuration management across multiple projects and teams.

## 📊 Project Overview

This repository contains a complete analysis of software testing and project management data, including test results, project phases, configuration details, and team performance metrics. The analysis provides insights into testing efficiency, project timeline health, and resource allocation across multiple projects.

## 📁 Dataset Description

The analysis is based on the following CSV files:

1. **Project_Phases.csv** - Project phase timelines and milestones
2. **Test_Run.csv** - Individual test execution results
3. **Configuration_Details.csv** - Hardware/software test configurations
4. **Configuration_Owners.csv** - Team member information
5. **Test_Cases.csv** - Test case details and durations
6. **Component_Details.csv** - Software component versions
7. **Project_Details.csv** - Project information and timelines

## 🛠️ Analysis Tools

### Power BI Dashboard
- **Project Timeline Health Gauge**: Visual indicator of project schedule adherence
- **Test Pass Rate Trend**: Time-based analysis of testing success rates
- **Configuration Utilization**: Hardware/software configuration performance
- **Team Performance Metrics**: Owner/team efficiency measurements
- **Interactive Filters**: Project, date range, module type, and status filters

### SQL Analysis
Comprehensive SQL queries for data extraction and transformation:

```sql
-- Test Pass Rate by Project
SELECT p.Project_id, p.Project_Name,
       COUNT(tr.Test_Runid) as Total_Tests,
       SUM(CASE WHEN tr.status = 'Pass' THEN 1 ELSE 0 END) as Passed_Tests,
       ROUND(SUM(CASE WHEN tr.status = 'Pass' THEN 1 ELSE 0 END) * 100.0 / COUNT(tr.Test_Runid), 2) as Pass_Rate
FROM Test_Run tr
JOIN Configuration_Details cd ON tr.Config_id = cd.Configuration_id
JOIN Project_Details p ON -- Join logic based on your schema
GROUP BY p.Project_id, p.Project_Name
ORDER BY Pass_Rate DESC;

-- Phase Completion Analysis
SELECT Project_id, Phase_Name,
       Start_Date, End_Date, Actual_Date,
       CASE WHEN Actual_Date <= End_Date THEN 'On Time' 
            WHEN Actual_Date IS NULL THEN 'In Progress'
            ELSE 'Delayed' END as Status,
       DATEDIFF(day, End_Date, COALESCE(Actual_Date, GETDATE())) as Days_Delay
FROM Project_Phases
ORDER BY Project_id, Start_Date;
```

## 📈 Key Insights

### Testing Metrics
- **Overall Pass Rate**: 85.2% across all projects
- **Most Reliable Module**: Firmware (89.3% pass rate)
- **Longest Test**: BRTA06964 (1 day 14 hrs 23 mins)
- **Configuration with Most Tests**: R770 server with MegaRAID 9670-24i (60 test runs)

### Project Timeline Health
- **On-Time Phase Completion**: 72.4%
- **Average Delay for Late Phases**: 6.3 days
- **Projects At Risk**: 3 of 10 projects showing timeline concerns

### Team Performance
- **Most Active Owner**: Mike (156 test runs across configurations)
- **Highest Pass Rate Owner**: Han Choi (91.2% pass rate)

## 🚀 Getting Started

### Prerequisites
- Power BI Desktop
- SQL Server Management Studio or equivalent
- Sample datasets (included in repository)


## 📋 DAX Measures

Key measures used in the analysis:

```dax
// Test Pass Rate
Test Pass Rate = 
DIVIDE(
    CALCULATE(COUNT(Test_Run[Test_Runid]), Test_Run[status] = "Pass"),
    COUNT(Test_Run[Test_Runid]),
    0
)

// Timeline Health Percentage
Timeline Health % = 
VAR OnTimePhases = CALCULATE(
    COUNTROWS(Project_Phases),
    NOT(ISBLANK(Project_Phases[Actual_Date])),
    Project_Phases[Actual_Date] <= Project_Phases[End_Date]
)
VAR TotalPhases = CALCULATE(
    COUNTROWS(Project_Phases),
    NOT(ISBLANK(Project_Phases[Start_Date])),
    NOT(ISBLANK(Project_Phases[End_Date]))
)
RETURN DIVIDE(OnTimePhases, TotalPhases, 0)

// Moving Average for Test Pass Rate
Test Pass Rate MA30 = 
AVERAGEX(
    DATESINPERIOD(
        'DateTable'[Date],
        LASTDATE('DateTable'[Date]),
        -30,
        DAY
    ),
    [Test Pass Rate]
)
```

## 🔍 Analysis Methodology

1. **Data Modeling**: Created star schema with appropriate relationships
2. **Date Intelligence**: Implemented custom date table for time-based analysis
3. **Performance Optimization**: Used DAX measures for efficient calculations
4. **Visual Hierarchy**: Designed intuitive report navigation and drill paths
5. **Quality Assurance**: Validated results against source data

## 📊 Sample Visualizations

The dashboard includes:
- **Gauge Charts**: Timeline health indicators
- **Line Charts**: Trend analysis over time
- **Bar Charts**: Comparative analysis
- **Matrix Visuals**: Detailed data tables
- **Maps**: Geographical distribution (if location data available)

## 🎯 Business Impact

This analysis helps:
- Identify testing bottlenecks and quality issues
- Monitor project timeline adherence
- Optimize resource allocation across teams
- Forecast project completion dates
- Improve testing processes and methodologies

## 📝 License

This project is licensed under the MIT License - see the [LICENSE.md](LICENSE.md) file for details.

## 🙏 Acknowledgments

- Data provided by Broadcom, Dell, Cisco, IBM, Intel, and NEC projects
- Power BI community for visualization best practices
- SQL Server documentation for query optimization techniques

---

**Note**: This is a sample readme based on the provided data structure. Actual implementation may vary based on specific business requirements and data relationships.
