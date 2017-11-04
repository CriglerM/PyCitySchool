# Pandas
Homework Week 4 - Py City School

This assignment uses python pandas to read and analyze csv files.

* Observations
    * Charter schools outperfom District schools in both the average scores and the percent of students passing
    * Higher budget cost per student does not appear to improve student outcomes
    * Small and Midsized schools outperform larger schools 


## Python Pandas Markdown
```python
# Dependancies
import pandas as pd
```


```python
# Set file paths
csv_filepath1 = "raw_data/schools_complete.csv"
csv_filepath2 = "raw_data/students_complete.csv"
```


```python
# Read the csv files into new dataframes
# pd.read_csv(csv_filepath2, encoding="iso-8859-1", low_memory=False,)
school_df = pd.read_csv(csv_filepath1, encoding="iso-8859-1", low_memory=False)
student_df = pd.read_csv(csv_filepath2, encoding="iso-8859-1", low_memory=False)

```


```python
# Inspect the columns in each spreadhseet
    #school_df.columns
    #student_df.columns
```


```python
# Rename the school name column in both tables so the match and can be used later to join the tables
school_df = school_df.rename(columns={"name":"SchoolName"})
student_df = student_df.rename(columns={"school":"SchoolName"})
```


```python
# Inspect sample of table data
    #school_df.head(10)
    #student_df.head(10)
```


```python
# Total number of schools
numberSchools = len(school_df["SchoolName"].unique())
    #print(f"The number of schools is: {numberSchools}")
```


```python
# Total number of students
numberStudents = school_df["size"].sum()
    #print(f"The number of students is: {numberStudents}")
```


```python
# Total budget of all schools
totalBudget = school_df["budget"].sum()
    #print(f"The total of all budgets is: {totalBudget}")
```


```python
# Average math score
avgMathScore = student_df["math_score"].mean()
    #print(f"The average math score is: {avgMathScore}")
```


```python
# Average reading score
avgReadingScore = student_df["reading_score"].mean()
    #print(f"The average reading score is: {avgReadingScore}")
```


```python
# Percent of passing math scores
percentMathPass = ((student_df["math_score"] > 70).sum()/student_df["math_score"].count())*100
    #print(f"The percent of students with passing math scores: {percentMathPass}")
```


```python
# Percent of passing reading scores
percentReadingPass = (((student_df["reading_score"] > 70).sum()/student_df["reading_score"].count())*100)
    #print(f"The percent of students with passing reading scores: {percentReadingPass}")
```


```python
# Overall passing scores
overallPass = ((percentMathPass + percentReadingPass)/2)
    #print(f"The overall pass percentage is: {overallPass}")
```


```python
# District Summary table
district_summary = pd.DataFrame({"Total Schools":[numberSchools],
                               "Total Students":[numberStudents],
                               "Total Budget" : [totalBudget],
                               "Average Math Score":[avgMathScore],
                               "Average Reading Score":[avgReadingScore],
                               "% Passing Math":[percentMathPass],
                               "% Passing Reading": [percentReadingPass],
                               "% Overall Passing Rate":[overallPass]})

# Force column order
district_summary = district_summary[["Total Schools", "Total Students","Total Budget", "Average Math Score", "Average Reading Score", "% Passing Math", "% Passing Reading", "% Overall Passing Rate"]]
```


```python
# Apply formatting to columns
district_summary["Total Students"] = district_summary["Total Students"].map("{:,}".format)
district_summary["Total Budget"] = district_summary["Total Budget"].map("${:,}".format)
district_summary["Average Math Score"] = district_summary["Average Math Score"].map("{:.2f}%".format)
district_summary["Average Reading Score"] = district_summary["Average Reading Score"].map("{:.2f}%".format)
district_summary["% Passing Math"] = district_summary["% Passing Math"].map("{:.2f}%".format)
district_summary["% Passing Reading"] = district_summary["% Passing Reading"].map("{:.2f}%".format)
district_summary["% Overall Passing Rate"] = district_summary["% Overall Passing Rate"].map("{:.2f}%".format)

district_summary.head()
```
## District Summary



<div>

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Total Schools</th>
      <th>Total Students</th>
      <th>Total Budget</th>
      <th>Average Math Score</th>
      <th>Average Reading Score</th>
      <th>% Passing Math</th>
      <th>% Passing Reading</th>
      <th>% Overall Passing Rate</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>15</td>
      <td>39,170</td>
      <td>$24,649,428</td>
      <td>78.99%</td>
      <td>81.88%</td>
      <td>72.39%</td>
      <td>82.97%</td>
      <td>77.68%</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Group student data by school
groupedStudentSchool_df = student_df.groupby(["SchoolName"], as_index=False)
    #groupedStudentSchool_df.head(5)
```


```python
# average Math score per school
sch_avgMathScore_df = pd.DataFrame(groupedStudentSchool_df["math_score"].mean())
    #sch_avgMathScore_df.head()
```


```python
# average Reading score per school
sch_avgReadingScore_df = pd.DataFrame(groupedStudentSchool_df["reading_score"].mean())
    #sch_avgReadingScore_df.head()
```


```python
#Group math scores better than 70
group_mathPass = student_df[student_df["math_score"]>70].groupby(["SchoolName"], as_index=False)
    #group_mathPass.head()
```


```python
# total math pass scores as a prep for the percent pass
sch_mathPass_df = pd.DataFrame(group_mathPass["math_score"].count())
sch_mathPass_df.columns= ["SchoolName", "mathPassCnt"]
    #sch_mathPass_df.head()
```


```python
#Group reading scores better than 70
group_readPass = student_df[student_df["reading_score"]>70].groupby(["SchoolName"], as_index=False)
    #group_readPass.head()
```


```python
# total reading pass scores as a prep for the percent pass
sch_readPass_df = pd.DataFrame(group_readPass["reading_score"].count())
sch_readPass_df.columns= ["SchoolName", "readPassCnt"]
    #sch_readPass_df.head()
```


```python
# Merge school table with grouped data
mergedStudentSchool_df = pd.merge(school_df, sch_avgMathScore_df, on="SchoolName" )
mergedStudentSchool_df = pd.merge(mergedStudentSchool_df, sch_mathPass_df, on="SchoolName" )
mergedStudentSchool_df = pd.merge(mergedStudentSchool_df, sch_avgReadingScore_df, on="SchoolName" )
mergedStudentSchool_df = pd.merge(mergedStudentSchool_df, sch_readPass_df, on="SchoolName" )
    #mergedStudentSchool_df.head()
```


```python
# Add calculated columns for math and reading percent pass
mergedStudentSchool_df["mathPassPct"] = (mergedStudentSchool_df["mathPassCnt"]/mergedStudentSchool_df["size"])*100
mergedStudentSchool_df["readPassPct"] = (mergedStudentSchool_df["readPassCnt"]/mergedStudentSchool_df["size"])*100
    #mergedStudentSchool_df.head()
```


```python
# Add calculated column for overall pass pct
mergedStudentSchool_df["overallPassPct"] = (mergedStudentSchool_df["mathPassPct"]+mergedStudentSchool_df["readPassPct"])/2
    #mergedStudentSchool_df.head()
```


```python
# Add calculated Per Student Budget column
mergedStudentSchool_df["perStudentBudget"] = mergedStudentSchool_df["budget"]/mergedStudentSchool_df["size"]
    #mergedStudentSchool_df.head()
```


```python
# Save data unformatted for later use
school_summary_raw = pd.DataFrame(mergedStudentSchool_df[["SchoolName", "type", "size", "budget", "perStudentBudget", "math_score", "reading_score", "mathPassPct", "readPassPct", "overallPassPct"]])
school_summary_raw.columns = ["SchoolName", "Type", "Num Students", "Budget", "Budget/Student", "Avg Math", "Avg Reading", "% Passing Math", "% Passing Reading", "% Overall Pass Rate"]
    #school_summary_raw.head()    
```


```python
# Select and rename columns
school_summary = pd.DataFrame(mergedStudentSchool_df[["SchoolName", "type", "size", "budget", "perStudentBudget", "math_score", "reading_score", "mathPassPct", "readPassPct", "overallPassPct"]])
school_summary.columns = ["SchoolName", "Type", "Num Students", "Budget", "Budget/Student", "Avg Math", "Avg Reading", "% Passing Math", "% Passing Reading", "% Overall Pass Rate"]
    #school_summary.head()                                        
```


```python
# Apply formatting to columns
school_summary["Num Students"] = school_summary["Num Students"].map("{:,}".format)
school_summary["Budget"] = school_summary["Budget"].map("${:,}".format)
school_summary["Budget/Student"] = school_summary["Budget/Student"].map("${:,}".format)
school_summary["Avg Math"] = school_summary["Avg Math"].map("{:.2f}%".format)
school_summary["Avg Reading"] = school_summary["Avg Reading"].map("{:.2f}%".format)
school_summary["% Passing Math"] = school_summary["% Passing Math"].map("{:.2f}%".format)
school_summary["% Passing Reading"] = school_summary["% Passing Reading"].map("{:.2f}%".format)
school_summary["% Overall Pass Rate"] = school_summary["% Overall Pass Rate"].map("{:.2f}%".format)

school_summary.head(15)
```


## School Summary

<div>

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>SchoolName</th>
      <th>Type</th>
      <th>Num Students</th>
      <th>Budget</th>
      <th>Budget/Student</th>
      <th>Avg Math</th>
      <th>Avg Reading</th>
      <th>% Passing Math</th>
      <th>% Passing Reading</th>
      <th>% Overall Pass Rate</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Huang High School</td>
      <td>District</td>
      <td>2,917</td>
      <td>$1,910,635</td>
      <td>$655.0</td>
      <td>76.63%</td>
      <td>81.18%</td>
      <td>63.32%</td>
      <td>78.81%</td>
      <td>71.07%</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Figueroa High School</td>
      <td>District</td>
      <td>2,949</td>
      <td>$1,884,411</td>
      <td>$639.0</td>
      <td>76.71%</td>
      <td>81.16%</td>
      <td>63.75%</td>
      <td>78.43%</td>
      <td>71.09%</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Shelton High School</td>
      <td>Charter</td>
      <td>1,761</td>
      <td>$1,056,600</td>
      <td>$600.0</td>
      <td>83.36%</td>
      <td>83.73%</td>
      <td>89.89%</td>
      <td>92.62%</td>
      <td>91.25%</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Hernandez High School</td>
      <td>District</td>
      <td>4,635</td>
      <td>$3,022,020</td>
      <td>$652.0</td>
      <td>77.29%</td>
      <td>80.93%</td>
      <td>64.75%</td>
      <td>78.19%</td>
      <td>71.47%</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Griffin High School</td>
      <td>Charter</td>
      <td>1,468</td>
      <td>$917,500</td>
      <td>$625.0</td>
      <td>83.35%</td>
      <td>83.82%</td>
      <td>89.71%</td>
      <td>93.39%</td>
      <td>91.55%</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Wilson High School</td>
      <td>Charter</td>
      <td>2,283</td>
      <td>$1,319,574</td>
      <td>$578.0</td>
      <td>83.27%</td>
      <td>83.99%</td>
      <td>90.93%</td>
      <td>93.25%</td>
      <td>92.09%</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Cabrera High School</td>
      <td>Charter</td>
      <td>1,858</td>
      <td>$1,081,356</td>
      <td>$582.0</td>
      <td>83.06%</td>
      <td>83.98%</td>
      <td>89.56%</td>
      <td>93.86%</td>
      <td>91.71%</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Bailey High School</td>
      <td>District</td>
      <td>4,976</td>
      <td>$3,124,928</td>
      <td>$628.0</td>
      <td>77.05%</td>
      <td>81.03%</td>
      <td>64.63%</td>
      <td>79.30%</td>
      <td>71.97%</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Holden High School</td>
      <td>Charter</td>
      <td>427</td>
      <td>$248,087</td>
      <td>$581.0</td>
      <td>83.80%</td>
      <td>83.81%</td>
      <td>90.63%</td>
      <td>92.74%</td>
      <td>91.69%</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Pena High School</td>
      <td>Charter</td>
      <td>962</td>
      <td>$585,858</td>
      <td>$609.0</td>
      <td>83.84%</td>
      <td>84.04%</td>
      <td>91.68%</td>
      <td>92.20%</td>
      <td>91.94%</td>
    </tr>
    <tr>
      <th>10</th>
      <td>Wright High School</td>
      <td>Charter</td>
      <td>1,800</td>
      <td>$1,049,400</td>
      <td>$583.0</td>
      <td>83.68%</td>
      <td>83.95%</td>
      <td>90.28%</td>
      <td>93.44%</td>
      <td>91.86%</td>
    </tr>
    <tr>
      <th>11</th>
      <td>Rodriguez High School</td>
      <td>District</td>
      <td>3,999</td>
      <td>$2,547,363</td>
      <td>$637.0</td>
      <td>76.84%</td>
      <td>80.74%</td>
      <td>64.07%</td>
      <td>77.74%</td>
      <td>70.91%</td>
    </tr>
    <tr>
      <th>12</th>
      <td>Johnson High School</td>
      <td>District</td>
      <td>4,761</td>
      <td>$3,094,650</td>
      <td>$650.0</td>
      <td>77.07%</td>
      <td>80.97%</td>
      <td>63.85%</td>
      <td>78.28%</td>
      <td>71.07%</td>
    </tr>
    <tr>
      <th>13</th>
      <td>Ford High School</td>
      <td>District</td>
      <td>2,739</td>
      <td>$1,763,916</td>
      <td>$644.0</td>
      <td>77.10%</td>
      <td>80.75%</td>
      <td>65.75%</td>
      <td>77.51%</td>
      <td>71.63%</td>
    </tr>
    <tr>
      <th>14</th>
      <td>Thomas High School</td>
      <td>Charter</td>
      <td>1,635</td>
      <td>$1,043,130</td>
      <td>$638.0</td>
      <td>83.42%</td>
      <td>83.85%</td>
      <td>90.21%</td>
      <td>92.91%</td>
      <td>91.56%</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Create sorted list of top performing schools
sort_school_summary = school_summary.sort_values("% Overall Pass Rate", ascending=False)[:5]
sort_school_summary.head(15)
```

## Top Performing Schools


<div>

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>SchoolName</th>
      <th>Type</th>
      <th>Num Students</th>
      <th>Budget</th>
      <th>Budget/Student</th>
      <th>Avg Math</th>
      <th>Avg Reading</th>
      <th>% Passing Math</th>
      <th>% Passing Reading</th>
      <th>% Overall Pass Rate</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>5</th>
      <td>Wilson High School</td>
      <td>Charter</td>
      <td>2,283</td>
      <td>$1,319,574</td>
      <td>$578.0</td>
      <td>83.27%</td>
      <td>83.99%</td>
      <td>90.93%</td>
      <td>93.25%</td>
      <td>92.09%</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Pena High School</td>
      <td>Charter</td>
      <td>962</td>
      <td>$585,858</td>
      <td>$609.0</td>
      <td>83.84%</td>
      <td>84.04%</td>
      <td>91.68%</td>
      <td>92.20%</td>
      <td>91.94%</td>
    </tr>
    <tr>
      <th>10</th>
      <td>Wright High School</td>
      <td>Charter</td>
      <td>1,800</td>
      <td>$1,049,400</td>
      <td>$583.0</td>
      <td>83.68%</td>
      <td>83.95%</td>
      <td>90.28%</td>
      <td>93.44%</td>
      <td>91.86%</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Cabrera High School</td>
      <td>Charter</td>
      <td>1,858</td>
      <td>$1,081,356</td>
      <td>$582.0</td>
      <td>83.06%</td>
      <td>83.98%</td>
      <td>89.56%</td>
      <td>93.86%</td>
      <td>91.71%</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Holden High School</td>
      <td>Charter</td>
      <td>427</td>
      <td>$248,087</td>
      <td>$581.0</td>
      <td>83.80%</td>
      <td>83.81%</td>
      <td>90.63%</td>
      <td>92.74%</td>
      <td>91.69%</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Create sorted list of bottom performing schools
sort_school_summary = school_summary.sort_values("% Overall Pass Rate")[:5]
sort_school_summary.head(15)
```

## Bottom Performing Schools


<div>

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>SchoolName</th>
      <th>Type</th>
      <th>Num Students</th>
      <th>Budget</th>
      <th>Budget/Student</th>
      <th>Avg Math</th>
      <th>Avg Reading</th>
      <th>% Passing Math</th>
      <th>% Passing Reading</th>
      <th>% Overall Pass Rate</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>11</th>
      <td>Rodriguez High School</td>
      <td>District</td>
      <td>3,999</td>
      <td>$2,547,363</td>
      <td>$637.0</td>
      <td>76.84%</td>
      <td>80.74%</td>
      <td>64.07%</td>
      <td>77.74%</td>
      <td>70.91%</td>
    </tr>
    <tr>
      <th>0</th>
      <td>Huang High School</td>
      <td>District</td>
      <td>2,917</td>
      <td>$1,910,635</td>
      <td>$655.0</td>
      <td>76.63%</td>
      <td>81.18%</td>
      <td>63.32%</td>
      <td>78.81%</td>
      <td>71.07%</td>
    </tr>
    <tr>
      <th>12</th>
      <td>Johnson High School</td>
      <td>District</td>
      <td>4,761</td>
      <td>$3,094,650</td>
      <td>$650.0</td>
      <td>77.07%</td>
      <td>80.97%</td>
      <td>63.85%</td>
      <td>78.28%</td>
      <td>71.07%</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Figueroa High School</td>
      <td>District</td>
      <td>2,949</td>
      <td>$1,884,411</td>
      <td>$639.0</td>
      <td>76.71%</td>
      <td>81.16%</td>
      <td>63.75%</td>
      <td>78.43%</td>
      <td>71.09%</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Hernandez High School</td>
      <td>District</td>
      <td>4,635</td>
      <td>$3,022,020</td>
      <td>$652.0</td>
      <td>77.29%</td>
      <td>80.93%</td>
      <td>64.75%</td>
      <td>78.19%</td>
      <td>71.47%</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Get data by grades
ninth_df = student_df.loc[student_df["grade"] == "9th"].groupby("SchoolName", as_index=False)
tenth_df = student_df.loc[student_df["grade"] == "10th"].groupby("SchoolName", as_index=False)
eleventh_df = student_df.loc[student_df["grade"] == "11th"].groupby("SchoolName", as_index=False)
twelfth_df = student_df.loc[student_df["grade"] == "12th"].groupby("SchoolName", as_index=False)
```


```python
#Group and get average math scores
ninthM_Avg = pd.DataFrame(ninth_df["math_score"].mean())
tenthM_Avg = pd.DataFrame(tenth_df["math_score"].mean())
eleventhM_Avg = pd.DataFrame(eleventh_df["math_score"].mean())
twelfthM_Avg = pd.DataFrame(twelfth_df["math_score"].mean())
```


```python
#Merge to Math score summary table
mathByGrade = pd.merge(ninthM_Avg, tenthM_Avg, on="SchoolName")
mathByGrade = pd.merge(mathByGrade, eleventhM_Avg, on="SchoolName")
mathByGrade = pd.merge(mathByGrade, twelfthM_Avg, on="SchoolName")
mathByGrade.columns = ["SchoolName","9th","10th","11th","12th"]
mathByGrade.head(15)
```


## Math Scores by Grade

<div>

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>SchoolName</th>
      <th>9th</th>
      <th>10th</th>
      <th>11th</th>
      <th>12th</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Bailey High School</td>
      <td>77.083676</td>
      <td>76.996772</td>
      <td>77.515588</td>
      <td>76.492218</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Cabrera High School</td>
      <td>83.094697</td>
      <td>83.154506</td>
      <td>82.765560</td>
      <td>83.277487</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Figueroa High School</td>
      <td>76.403037</td>
      <td>76.539974</td>
      <td>76.884344</td>
      <td>77.151369</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Ford High School</td>
      <td>77.361345</td>
      <td>77.672316</td>
      <td>76.918058</td>
      <td>76.179963</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Griffin High School</td>
      <td>82.044010</td>
      <td>84.229064</td>
      <td>83.842105</td>
      <td>83.356164</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Hernandez High School</td>
      <td>77.438495</td>
      <td>77.337408</td>
      <td>77.136029</td>
      <td>77.186567</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Holden High School</td>
      <td>83.787402</td>
      <td>83.429825</td>
      <td>85.000000</td>
      <td>82.855422</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Huang High School</td>
      <td>77.027251</td>
      <td>75.908735</td>
      <td>76.446602</td>
      <td>77.225641</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Johnson High School</td>
      <td>77.187857</td>
      <td>76.691117</td>
      <td>77.491653</td>
      <td>76.863248</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Pena High School</td>
      <td>83.625455</td>
      <td>83.372000</td>
      <td>84.328125</td>
      <td>84.121547</td>
    </tr>
    <tr>
      <th>10</th>
      <td>Rodriguez High School</td>
      <td>76.859966</td>
      <td>76.612500</td>
      <td>76.395626</td>
      <td>77.690748</td>
    </tr>
    <tr>
      <th>11</th>
      <td>Shelton High School</td>
      <td>83.420755</td>
      <td>82.917411</td>
      <td>83.383495</td>
      <td>83.778976</td>
    </tr>
    <tr>
      <th>12</th>
      <td>Thomas High School</td>
      <td>83.590022</td>
      <td>83.087886</td>
      <td>83.498795</td>
      <td>83.497041</td>
    </tr>
    <tr>
      <th>13</th>
      <td>Wilson High School</td>
      <td>83.085578</td>
      <td>83.724422</td>
      <td>83.195326</td>
      <td>83.035794</td>
    </tr>
    <tr>
      <th>14</th>
      <td>Wright High School</td>
      <td>83.264706</td>
      <td>84.010288</td>
      <td>83.836782</td>
      <td>83.644986</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Group and get average reading scores
ninthR_Avg = pd.DataFrame(ninth_df["reading_score"].mean())
tenthR_Avg = pd.DataFrame(tenth_df["reading_score"].mean())
eleventhR_Avg = pd.DataFrame(eleventh_df["reading_score"].mean())
twelfthR_Avg = pd.DataFrame(twelfth_df["reading_score"].mean())
```


```python
#Merge to Reading score summary table
readByGrade = pd.merge(ninthR_Avg, tenthR_Avg, on="SchoolName")
readByGrade = pd.merge(readByGrade, eleventhR_Avg, on="SchoolName")
readByGrade = pd.merge(readByGrade, twelfthR_Avg, on="SchoolName")
readByGrade.columns = ["SchoolName","9th","10th","11th","12th"]
readByGrade.head(15)
```


## Reading Scores by Grade

<div>

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>SchoolName</th>
      <th>9th</th>
      <th>10th</th>
      <th>11th</th>
      <th>12th</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Bailey High School</td>
      <td>81.303155</td>
      <td>80.907183</td>
      <td>80.945643</td>
      <td>80.912451</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Cabrera High School</td>
      <td>83.676136</td>
      <td>84.253219</td>
      <td>83.788382</td>
      <td>84.287958</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Figueroa High School</td>
      <td>81.198598</td>
      <td>81.408912</td>
      <td>80.640339</td>
      <td>81.384863</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Ford High School</td>
      <td>80.632653</td>
      <td>81.262712</td>
      <td>80.403642</td>
      <td>80.662338</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Griffin High School</td>
      <td>83.369193</td>
      <td>83.706897</td>
      <td>84.288089</td>
      <td>84.013699</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Hernandez High School</td>
      <td>80.866860</td>
      <td>80.660147</td>
      <td>81.396140</td>
      <td>80.857143</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Holden High School</td>
      <td>83.677165</td>
      <td>83.324561</td>
      <td>83.815534</td>
      <td>84.698795</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Huang High School</td>
      <td>81.290284</td>
      <td>81.512386</td>
      <td>81.417476</td>
      <td>80.305983</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Johnson High School</td>
      <td>81.260714</td>
      <td>80.773431</td>
      <td>80.616027</td>
      <td>81.227564</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Pena High School</td>
      <td>83.807273</td>
      <td>83.612000</td>
      <td>84.335938</td>
      <td>84.591160</td>
    </tr>
    <tr>
      <th>10</th>
      <td>Rodriguez High School</td>
      <td>80.993127</td>
      <td>80.629808</td>
      <td>80.864811</td>
      <td>80.376426</td>
    </tr>
    <tr>
      <th>11</th>
      <td>Shelton High School</td>
      <td>84.122642</td>
      <td>83.441964</td>
      <td>84.373786</td>
      <td>82.781671</td>
    </tr>
    <tr>
      <th>12</th>
      <td>Thomas High School</td>
      <td>83.728850</td>
      <td>84.254157</td>
      <td>83.585542</td>
      <td>83.831361</td>
    </tr>
    <tr>
      <th>13</th>
      <td>Wilson High School</td>
      <td>83.939778</td>
      <td>84.021452</td>
      <td>83.764608</td>
      <td>84.317673</td>
    </tr>
    <tr>
      <th>14</th>
      <td>Wright High School</td>
      <td>83.833333</td>
      <td>83.812757</td>
      <td>84.156322</td>
      <td>84.073171</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Scores by School Spending
bins = [0, 585, 615, 645, 675]
group_names = ["0 to 585", "585 to 615", "615 to 645", "645 to 675"]
scoreByBudget = school_summary_raw[["Avg Math", "Avg Reading", "% Passing Math", "% Passing Reading", "% Overall Pass Rate"]].groupby(pd.cut(school_summary_raw["Budget/Student"], bins=bins, labels=group_names )).mean()
scoreByBudget.head()
```


## Scores by School Spending per Student

<div>

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Avg Math</th>
      <th>Avg Reading</th>
      <th>% Passing Math</th>
      <th>% Passing Reading</th>
      <th>% Overall Pass Rate</th>
    </tr>
    <tr>
      <th>Budget/Student</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0 to 585</th>
      <td>83.455399</td>
      <td>83.933814</td>
      <td>90.350436</td>
      <td>93.325838</td>
      <td>91.838137</td>
    </tr>
    <tr>
      <th>585 to 615</th>
      <td>83.599686</td>
      <td>83.885211</td>
      <td>90.788049</td>
      <td>92.410786</td>
      <td>91.599418</td>
    </tr>
    <tr>
      <th>615 to 645</th>
      <td>79.079225</td>
      <td>81.891436</td>
      <td>73.021426</td>
      <td>83.214343</td>
      <td>78.117884</td>
    </tr>
    <tr>
      <th>645 to 675</th>
      <td>76.997210</td>
      <td>81.027843</td>
      <td>63.972368</td>
      <td>78.427809</td>
      <td>71.200088</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Scores by School Size
bins = [0, 1000, 2500, 50000]
group_names = ["Small < 1000", "Medium 1000 to 2500", "Large 2500 to 5000"]
scoreBySize = school_summary_raw[["Avg Math", "Avg Reading", "% Passing Math", "% Passing Reading", "% Overall Pass Rate"]].groupby(pd.cut(school_summary_raw["Num Students"], bins=bins, labels=group_names)).mean()
scoreBySize.head()
```


## Scores by School Size

<div>

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Avg Math</th>
      <th>Avg Reading</th>
      <th>% Passing Math</th>
      <th>% Passing Reading</th>
      <th>% Overall Pass Rate</th>
    </tr>
    <tr>
      <th>Num Students</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Large 2500 to 5000</th>
      <td>76.956733</td>
      <td>80.966636</td>
      <td>64.302528</td>
      <td>78.324559</td>
      <td>71.313543</td>
    </tr>
    <tr>
      <th>Medium 1000 to 2500</th>
      <td>83.357937</td>
      <td>83.885280</td>
      <td>90.098249</td>
      <td>93.246451</td>
      <td>91.672350</td>
    </tr>
    <tr>
      <th>Small &lt; 1000</th>
      <td>83.821598</td>
      <td>83.929843</td>
      <td>91.158155</td>
      <td>92.471895</td>
      <td>91.815025</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Scores by School Type
school_summary_type = school_summary_raw
school_summary_type["Type"] = school_summary_type["Type"].replace({"Charter": 1, "District":2})

bins = [0, 1, 2]
group_names = ["Charter", "District"]
scoreByType = school_summary_type[["Avg Math", "Avg Reading", "% Passing Math", "% Passing Reading", "% Overall Pass Rate"]].groupby(pd.cut(school_summary_type["Type"], bins=bins,labels=group_names)).mean()
scoreByType.head()
```


## Scores by School Type

<div>

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Avg Math</th>
      <th>Avg Reading</th>
      <th>% Passing Math</th>
      <th>% Passing Reading</th>
      <th>% Overall Pass Rate</th>
    </tr>
    <tr>
      <th>Type</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Charter</th>
      <td>83.473852</td>
      <td>83.896421</td>
      <td>90.363226</td>
      <td>93.052812</td>
      <td>91.708019</td>
    </tr>
    <tr>
      <th>District</th>
      <td>76.956733</td>
      <td>80.966636</td>
      <td>64.302528</td>
      <td>78.324559</td>
      <td>71.313543</td>
    </tr>
  </tbody>
</table>
</div>




```python

```
