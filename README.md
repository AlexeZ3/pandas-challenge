# pandas-challenge

District Summary
# Dependencies and Setup
import pandas as pd
from pathlib import Path
# File to Load (Remember to Change These)
school_data_to_load = Path("Resources/schools_complete.csv")
student_data_to_load = Path("Resources/students_complete.csv")

# Read School and Student Data File and store into Pandas DataFrames
school_data = pd.read_csv(school_data_to_load)
student_data = pd.read_csv(student_data_to_load)

# Combine the data into a single dataset.  
school_data_complete = pd.merge(student_data, school_data, how="left", on=["school_name", "school_name"])
school_data_complete.head()
Student ID	student_name	gender	grade	school_name	reading_score	math_score	School ID	type	size	budget
0	0	Paul Bradley	M	9th	Huang High School	66	79	0	District	2917	1910635
1	1	Victor Smith	M	12th	Huang High School	94	61	0	District	2917	1910635
2	2	Kevin Rodriguez	M	12th	Huang High School	90	60	0	District	2917	1910635
3	3	Dr. Richard Scott	M	12th	Huang High School	67	58	0	District	2917	1910635
4	4	Bonnie Ray	F	9th	Huang High School	97	84	0	District	2917	1910635
# Calculate the total number of unique schools
school_count = school_data_complete["school_name"].unique()
school_count = len(school_count)
school_count
15
# Calculate the total number of students
student_count = school_data_complete["student_name"].count()
student_count
39170
# Calculate the total budget
total_budget = school_data["budget"].sum()
total_budget
24649428
# Calculate the average math score.
average_math_score = school_data_complete["math_score"].mean()
average_math_score
78.98537145774827
# Calculate the average reading score
average_reading_score = school_data_complete["reading_score"].mean()
average_reading_score
81.87784018381414
# Use the following to calculate the percentage of students who passed math (math scores greather than or equal to 70)
passing_math_count = school_data_complete[(school_data_complete["math_score"] >= 70)].count()["student_name"]
passing_math_percentage = passing_math_count / float(student_count) * 100
passing_math_percentage
74.9808526933878
# Calculate the percentage of students who passeed reading (hint: look at how the math percentage was calculated)
passing_reading_count = school_data_complete[(school_data_complete["reading_score"] >= 70)].count()["student_name"]
passing_reading_percentage = passing_reading_count / float(student_count) * 100
passing_reading_percentage
85.80546336482001
# Use the following to calculate the percentage of students that passed math and reading
passing_math_reading_count = school_data_complete[(school_data_complete["math_score"] >= 70) & (school_data_complete["reading_score"] >= 70)
].count()["student_name"]
overall_passing_rate = passing_math_reading_count /  float(school_count) * 100
overall_passing_rate
170186.66666666666
# Create a high-level snapshot of the district's key metrics in a DataFrame
district_summary = pd.DataFrame({
    "Total Schools": school_count,
    "Total Students": f"{student_count:,}",
    "Total Budget": f"${total_budget:,.2f}",
    "Average Math Score": f"{average_math_score:.6f}",
    "Average Reading Score": f"{average_reading_score:.5f}",
    "% Passing Math": f"{passing_math_percentage:.6f}",
    "% Passing Reading": f"{passing_reading_percentage:.6f}",
    "% Overall Passing": f"{passing_math_reading_count: .6f}"}, index=[0])




# Display the DataFrame
district_summary.head()
Total Schools	Total Students	Total Budget	Average Math Score	Average Reading Score	% Passing Math	% Passing Reading	% Overall Passing
0	15	39,170	$24,649,428.00	78.985371	81.87784	74.980853	85.805463	25528.000000
School Summary
school_name = school_data_complete.set_index("school_name").groupby(["school_name"])
# Use the code provided to select the school type
school_types = school_data.set_index(["school_name"])["type"]
per_school_counts = school_data_complete["school_name"].value_counts()
# Calculate the total school budget and per capita spending
per_school_budget = school_data_complete.groupby(["school_name"]).mean()["budget"]
per_school_capita = per_school_budget / per_school_counts
# Calculate the average test scores
per_school_math = school_name["math_score"].mean()
per_school_reading = school_name["reading_score"].mean()
# Calculate the number of schools with math scores of 70 or higher
school_passing_math = school_data_complete[school_data_complete["math_score"] >= 70]
# Calculate the number of schools with reading scores of 70 or higher
school_passing_reading = school_data_complete[school_data_complete["reading_score"] >= 70]
# Use the provided code to calculate the schools that passed both math and reading with scores of 70 or higher
passing_math_and_reading = school_data_complete[
    (school_data_complete["reading_score"] >= 70) & (school_data_complete["math_score"] >= 70)]
# Use the provided code to calculate the passing rates
per_school_passing_math = school_passing_math.groupby(["school_name"]).count()["student_name"] / per_school_counts * 100
per_school_passing_reading = school_passing_reading.groupby(["school_name"]).count()["student_name"] / per_school_counts * 100
overall_passing_rate = passing_math_and_reading.groupby(["school_name"]).count()["student_name"] / per_school_counts * 100
# Create a DataFrame called `per_school_summary` with columns for the calculations above.
per_school_summary = school_summary = pd.DataFrame({
    "School Type": school_types,
    "Total Students": per_school_counts,
    "Per Student Budget": per_school_capita,
    "Total School Budget": per_school_budget,
    "Average Math Score": per_school_math,
    "Average Reading Score": per_school_reading,
    "% Passing Math": per_school_passing_math,
    "% Passing Reading": per_school_passing_reading,
    "% Overall Passing": overall_passing_rate})

# Formatting
per_school_summary["Total School Budget"] = per_school_summary["Total School Budget"].map("${:,.2f}".format)
per_school_summary["Per Student Budget"] = per_school_summary["Per Student Budget"].map("${:,.2f}".format)

# Display the DataFrame
per_school_summary
School Type	Total Students	Per Student Budget	Total School Budget	Average Math Score	Average Reading Score	% Passing Math	% Passing Reading	% Overall Passing
Bailey High School	District	4976	$628.00	$3,124,928.00	77.048432	81.033963	66.680064	81.933280	54.642283
Cabrera High School	Charter	1858	$582.00	$1,081,356.00	83.061895	83.975780	94.133477	97.039828	91.334769
Figueroa High School	District	2949	$639.00	$1,884,411.00	76.711767	81.158020	65.988471	80.739234	53.204476
Ford High School	District	2739	$644.00	$1,763,916.00	77.102592	80.746258	68.309602	79.299014	54.289887
Griffin High School	Charter	1468	$625.00	$917,500.00	83.351499	83.816757	93.392371	97.138965	90.599455
Hernandez High School	District	4635	$652.00	$3,022,020.00	77.289752	80.934412	66.752967	80.862999	53.527508
Holden High School	Charter	427	$581.00	$248,087.00	83.803279	83.814988	92.505855	96.252927	89.227166
Huang High School	District	2917	$655.00	$1,910,635.00	76.629414	81.182722	65.683922	81.316421	53.513884
Johnson High School	District	4761	$650.00	$3,094,650.00	77.072464	80.966394	66.057551	81.222432	53.539172
Pena High School	Charter	962	$609.00	$585,858.00	83.839917	84.044699	94.594595	95.945946	90.540541
Rodriguez High School	District	3999	$637.00	$2,547,363.00	76.842711	80.744686	66.366592	80.220055	52.988247
Shelton High School	Charter	1761	$600.00	$1,056,600.00	83.359455	83.725724	93.867121	95.854628	89.892107
Thomas High School	Charter	1635	$638.00	$1,043,130.00	83.418349	83.848930	93.272171	97.308869	90.948012
Wilson High School	Charter	2283	$578.00	$1,319,574.00	83.274201	83.989488	93.867718	96.539641	90.582567
Wright High School	Charter	1800	$583.00	$1,049,400.00	83.682222	83.955000	93.333333	96.611111	90.333333
Highest-Performing Schools (by % Overall Passing)
# Sort the schools by `% Overall Passing` in descending order and display the top 5 rows.
top_schools = per_school_summary.sort_values("% Overall Passing", ascending = False)
top_schools.head().style.format({'Total Students': '{:}',
                           "Total School Budget": "${:,.2f}", 
                           "Per Student Budget": "${:.2f}", 
                           "% Passing Math": "{:6f}", 
                           "% Passing Reading": "{:6f}", 
                           "% Overall Passing": "{:6f}"})
top_schools.head(5)
School Type	Total Students	Per Student Budget	Total School Budget	Average Math Score	Average Reading Score	% Passing Math	% Passing Reading	% Overall Passing
Cabrera High School	Charter	1858	$582.00	$1,081,356.00	83.061895	83.975780	94.133477	97.039828	91.334769
Thomas High School	Charter	1635	$638.00	$1,043,130.00	83.418349	83.848930	93.272171	97.308869	90.948012
Griffin High School	Charter	1468	$625.00	$917,500.00	83.351499	83.816757	93.392371	97.138965	90.599455
Wilson High School	Charter	2283	$578.00	$1,319,574.00	83.274201	83.989488	93.867718	96.539641	90.582567
Pena High School	Charter	962	$609.00	$585,858.00	83.839917	84.044699	94.594595	95.945946	90.540541
Lowest-Performing Schools (by % Overall Passing)
# Sort the schools by `% Overall Passing` in ascending order and display the top 5 rows.
bottom_schools = top_schools.tail()
bottom_schools.sort_values('% Overall Passing', ascending = False)
bottom_schools.style.format({'Total Students': '{: }', 
                       "Total School Budget": "${:,.2f}", 
                       "Per Student Budget": "${:.2f}", 
                       "% Passing Math": "{:6f}", 
                       "% Passing Reading": "{:6f}", 
                       "% Overall Passing": "{:6f}"})
bottom_schools.head(5)
School Type	Total Students	Per Student Budget	Total School Budget	Average Math Score	Average Reading Score	% Passing Math	% Passing Reading	% Overall Passing
Johnson High School	District	4761	$650.00	$3,094,650.00	77.072464	80.966394	66.057551	81.222432	53.539172
Hernandez High School	District	4635	$652.00	$3,022,020.00	77.289752	80.934412	66.752967	80.862999	53.527508
Huang High School	District	2917	$655.00	$1,910,635.00	76.629414	81.182722	65.683922	81.316421	53.513884
Figueroa High School	District	2949	$639.00	$1,884,411.00	76.711767	81.158020	65.988471	80.739234	53.204476
Rodriguez High School	District	3999	$637.00	$2,547,363.00	76.842711	80.744686	66.366592	80.220055	52.988247
Math Scores by Grade
# Use the code provided to separate the data by grade
ninth_math = school_data_complete.loc[school_data_complete['grade'] == '9th'].groupby('school_name')["math_score"].mean()
tenth_math = school_data_complete.loc[school_data_complete['grade'] == '10th'].groupby('school_name')["math_score"].mean()
eleventh_math = school_data_complete.loc[school_data_complete['grade'] == '11th'].groupby('school_name')["math_score"].mean()
twelfth_math = school_data_complete.loc[school_data_complete['grade'] == '12th'].groupby('school_name')["math_score"].mean()


math_scores = pd.DataFrame({
        "9th": ninth_math,
        "10th": tenth_math,
        "11th": eleventh_math,
        "12th": twelfth_math
})
# Minor data wrangling
math_scores = math_scores[['9th', '10th', '11th', '12th']]
math_scores.index.name = "School Name"

# Display the DataFrame
math_scores
9th	10th	11th	12th
School Name				
Bailey High School	77.083676	76.996772	77.515588	76.492218
Cabrera High School	83.094697	83.154506	82.765560	83.277487
Figueroa High School	76.403037	76.539974	76.884344	77.151369
Ford High School	77.361345	77.672316	76.918058	76.179963
Griffin High School	82.044010	84.229064	83.842105	83.356164
Hernandez High School	77.438495	77.337408	77.136029	77.186567
Holden High School	83.787402	83.429825	85.000000	82.855422
Huang High School	77.027251	75.908735	76.446602	77.225641
Johnson High School	77.187857	76.691117	77.491653	76.863248
Pena High School	83.625455	83.372000	84.328125	84.121547
Rodriguez High School	76.859966	76.612500	76.395626	77.690748
Shelton High School	83.420755	82.917411	83.383495	83.778976
Thomas High School	83.590022	83.087886	83.498795	83.497041
Wilson High School	83.085578	83.724422	83.195326	83.035794
Wright High School	83.264706	84.010288	83.836782	83.644986
Reading Scores by Grade
ninth_reading = school_data_complete.loc[school_data_complete['grade'] == '9th'].groupby('school_name')["reading_score"].mean()
tenth_reading = school_data_complete.loc[school_data_complete['grade'] == '10th'].groupby('school_name')["reading_score"].mean()
eleventh_reading = school_data_complete.loc[school_data_complete['grade'] == '11th'].groupby('school_name')["reading_score"].mean()
twelfth_reading = school_data_complete.loc[school_data_complete['grade'] == '12th'].groupby('school_name')["reading_score"].mean()

reading_scores = pd.DataFrame({
        "9th": ninth_reading,
        "10th": tenth_reading,
        "11th": eleventh_reading,
        "12th": twelfth_reading
})

reading_scores = reading_scores[['9th', '10th', '11th', '12th']]
reading_scores.index.name = "School Name"

reading_scores
9th	10th	11th	12th
School Name				
Bailey High School	81.303155	80.907183	80.945643	80.912451
Cabrera High School	83.676136	84.253219	83.788382	84.287958
Figueroa High School	81.198598	81.408912	80.640339	81.384863
Ford High School	80.632653	81.262712	80.403642	80.662338
Griffin High School	83.369193	83.706897	84.288089	84.013699
Hernandez High School	80.866860	80.660147	81.396140	80.857143
Holden High School	83.677165	83.324561	83.815534	84.698795
Huang High School	81.290284	81.512386	81.417476	80.305983
Johnson High School	81.260714	80.773431	80.616027	81.227564
Pena High School	83.807273	83.612000	84.335938	84.591160
Rodriguez High School	80.993127	80.629808	80.864811	80.376426
Shelton High School	84.122642	83.441964	84.373786	82.781671
Thomas High School	83.728850	84.254157	83.585542	83.831361
Wilson High School	83.939778	84.021452	83.764608	84.317673
Wright High School	83.833333	83.812757	84.156322	84.073171
Scores by School Spending
# Establish the bins
spending_bins = [0, 585, 630, 645, 680]
labels = ["<$585", "$585-630", "$630-645", "$645-680"]
# Create a copy of the school summary since it has the "Per Student Budget"
school_spending_df = per_school_summary.copy()
# Use `pd.cut` to categorize spending based on the bins.
per_school_summary['Per Student Budget'] = per_school_summary['Per Student Budget'].astype(float)

school_spending_df["Spending Ranges (Per Student)"] = pd.cut(per_school_summary["Per Student Budget"], 
                                                                  spending_bins, labels=labels, include_lowest=True)
school_spending_df
School Type	Total Students	Per Student Budget	Total School Budget	Average Math Score	Average Reading Score	% Passing Math	% Passing Reading	% Overall Passing	School Size	Spending Ranges (Per Student)
Bailey High School	District	4976	628.0	$3,124,928.00	77.048432	81.033963	66.680064	81.933280	54.642283	Large (2000-5000)	$585-630
Cabrera High School	Charter	1858	582.0	$1,081,356.00	83.061895	83.975780	94.133477	97.039828	91.334769	Medium (1000-2000)	<$585
Figueroa High School	District	2949	639.0	$1,884,411.00	76.711767	81.158020	65.988471	80.739234	53.204476	Large (2000-5000)	$630-645
Ford High School	District	2739	644.0	$1,763,916.00	77.102592	80.746258	68.309602	79.299014	54.289887	Large (2000-5000)	$630-645
Griffin High School	Charter	1468	625.0	$917,500.00	83.351499	83.816757	93.392371	97.138965	90.599455	Medium (1000-2000)	$585-630
Hernandez High School	District	4635	652.0	$3,022,020.00	77.289752	80.934412	66.752967	80.862999	53.527508	Large (2000-5000)	$645-680
Holden High School	Charter	427	581.0	$248,087.00	83.803279	83.814988	92.505855	96.252927	89.227166	Small (<1000)	<$585
Huang High School	District	2917	655.0	$1,910,635.00	76.629414	81.182722	65.683922	81.316421	53.513884	Large (2000-5000)	$645-680
Johnson High School	District	4761	650.0	$3,094,650.00	77.072464	80.966394	66.057551	81.222432	53.539172	Large (2000-5000)	$645-680
Pena High School	Charter	962	609.0	$585,858.00	83.839917	84.044699	94.594595	95.945946	90.540541	Small (<1000)	$585-630
Rodriguez High School	District	3999	637.0	$2,547,363.00	76.842711	80.744686	66.366592	80.220055	52.988247	Large (2000-5000)	$630-645
Shelton High School	Charter	1761	600.0	$1,056,600.00	83.359455	83.725724	93.867121	95.854628	89.892107	Medium (1000-2000)	$585-630
Thomas High School	Charter	1635	638.0	$1,043,130.00	83.418349	83.848930	93.272171	97.308869	90.948012	Medium (1000-2000)	$630-645
Wilson High School	Charter	2283	578.0	$1,319,574.00	83.274201	83.989488	93.867718	96.539641	90.582567	Large (2000-5000)	<$585
Wright High School	Charter	1800	583.0	$1,049,400.00	83.682222	83.955000	93.333333	96.611111	90.333333	Medium (1000-2000)	<$585
#  Calculate averages for the desired columns. 
spending_math_scores = school_spending_df.groupby(["Spending Ranges (Per Student)"]).mean()["Average Math Score"]
spending_reading_scores = school_spending_df.groupby(["Spending Ranges (Per Student)"]).mean()["Average Reading Score"]
spending_passing_math = school_spending_df.groupby(["Spending Ranges (Per Student)"]).mean()["% Passing Math"]
spending_passing_reading = school_spending_df.groupby(["Spending Ranges (Per Student)"]).mean()["% Passing Reading"]
overall_passing_spending = school_spending_df.groupby(["Spending Ranges (Per Student)"]).mean()["% Overall Passing"]
# Assemble into DataFrame
spending_summary = pd.DataFrame({
    "Average Math Score": spending_math_scores,
    "Average Reading Score": spending_reading_scores,
    "% Passing Math": spending_passing_math,
    "% Passing Reading": spending_passing_reading,
    "% Overall Passing": overall_passing_spending})

# Display results
spending_summary
Average Math Score	Average Reading Score	% Passing Math	% Passing Reading	% Overall Passing
Spending Ranges (Per Student)					
<$585	83.455399	83.933814	93.460096	96.610877	90.369459
$585-630	81.899826	83.155286	87.133538	92.718205	81.418596
$630-645	78.518855	81.624473	73.484209	84.391793	62.857656
$645-680	76.997210	81.027843	66.164813	81.133951	53.526855
Scores by School Size
# Establish the bins.
size_bins = [0, 1000, 2000, 5000]
labels = ["Small (<1000)", "Medium (1000-2000)", "Large (2000-5000)"]
# Categorize the spending based on the bins
# Use `pd.cut` on the "Total Students" column of the `per_school_summary` DataFrame.

per_school_summary["School Size"] =pd.cut(per_school_summary["Total Students"], size_bins, labels = labels)
per_school_summary
School Type	Total Students	Per Student Budget	Total School Budget	Average Math Score	Average Reading Score	% Passing Math	% Passing Reading	% Overall Passing	School Size
Bailey High School	District	4976	$628.00	$3,124,928.00	77.048432	81.033963	66.680064	81.933280	54.642283	Large (2000-5000)
Cabrera High School	Charter	1858	$582.00	$1,081,356.00	83.061895	83.975780	94.133477	97.039828	91.334769	Medium (1000-2000)
Figueroa High School	District	2949	$639.00	$1,884,411.00	76.711767	81.158020	65.988471	80.739234	53.204476	Large (2000-5000)
Ford High School	District	2739	$644.00	$1,763,916.00	77.102592	80.746258	68.309602	79.299014	54.289887	Large (2000-5000)
Griffin High School	Charter	1468	$625.00	$917,500.00	83.351499	83.816757	93.392371	97.138965	90.599455	Medium (1000-2000)
Hernandez High School	District	4635	$652.00	$3,022,020.00	77.289752	80.934412	66.752967	80.862999	53.527508	Large (2000-5000)
Holden High School	Charter	427	$581.00	$248,087.00	83.803279	83.814988	92.505855	96.252927	89.227166	Small (<1000)
Huang High School	District	2917	$655.00	$1,910,635.00	76.629414	81.182722	65.683922	81.316421	53.513884	Large (2000-5000)
Johnson High School	District	4761	$650.00	$3,094,650.00	77.072464	80.966394	66.057551	81.222432	53.539172	Large (2000-5000)
Pena High School	Charter	962	$609.00	$585,858.00	83.839917	84.044699	94.594595	95.945946	90.540541	Small (<1000)
Rodriguez High School	District	3999	$637.00	$2,547,363.00	76.842711	80.744686	66.366592	80.220055	52.988247	Large (2000-5000)
Shelton High School	Charter	1761	$600.00	$1,056,600.00	83.359455	83.725724	93.867121	95.854628	89.892107	Medium (1000-2000)
Thomas High School	Charter	1635	$638.00	$1,043,130.00	83.418349	83.848930	93.272171	97.308869	90.948012	Medium (1000-2000)
Wilson High School	Charter	2283	$578.00	$1,319,574.00	83.274201	83.989488	93.867718	96.539641	90.582567	Large (2000-5000)
Wright High School	Charter	1800	$583.00	$1,049,400.00	83.682222	83.955000	93.333333	96.611111	90.333333	Medium (1000-2000)
# Calculate averages for the desired columns. 
size_math_scores = per_school_summary.groupby(["School Size"]).mean()["Average Math Score"]
size_reading_scores = per_school_summary.groupby(["School Size"]).mean()["Average Reading Score"]
size_passing_math = per_school_summary.groupby(["School Size"]).mean()["% Passing Math"]
size_passing_reading = per_school_summary.groupby(["School Size"]).mean()["% Passing Reading"]
size_overall_passing = per_school_summary.groupby(["School Size"]).mean()["% Overall Passing"]
# Create a DataFrame called `size_summary` that breaks down school performance based on school size (small, medium, or large).
# Use the scores above to create a new DataFrame called `size_summary`
size_summary = pd.DataFrame({
    "Average Math Score": size_math_scores,
    "Average Reading Score": size_reading_scores,
    "% Passing Math": size_passing_math,
    "% Passing Reading": size_passing_reading,
    "% Overall Passing": size_overall_passing})

# Display results
size_summary
Average Math Score	Average Reading Score	% Passing Math	% Passing Reading	% Overall Passing
School Size					
Small (<1000)	83.821598	83.929843	93.550225	96.099437	89.883853
Medium (1000-2000)	83.374684	83.864438	93.599695	96.790680	90.621535
Large (2000-5000)	77.746417	81.344493	69.963361	82.766634	58.286003
Scores by School Type
# Group the per_school_summary DataFrame by "School Type" and average the results.
type_math_scores = per_school_summary.groupby(["School Type"]).mean()["Average Math Score"]
type_reading_scores = per_school_summary.groupby(["School Type"]).mean()["Average Reading Score"]
type_passing_math = per_school_summary.groupby(["School Type"]).mean()["% Passing Math"]
type_passing_reading = per_school_summary.groupby(["School Type"]).mean()["% Passing Reading"]
type_overall_passing = per_school_summary.groupby(["School Type"]).mean()["% Overall Passing"]

# Use the code provided to select new column data
average_math_score_by_type = type_math_scores
average_reading_score_by_type = type_reading_scores
average_percent_passing_math_by_type = type_passing_math
average_percent_passing_reading_by_type = type_passing_reading
average_percent_overall_passing_by_type = type_overall_passing
# Assemble the new data by type into a DataFrame called `type_summary`
type_summary = pd.DataFrame({
    "Average Math Score": average_math_score_by_type,
    "Average Reading Score": average_reading_score_by_type,
    "% Passing Math": average_percent_passing_math_by_type,
    "% Passing Reading": average_percent_passing_reading_by_type,
    "% Overall Passing": average_percent_overall_passing_by_type})

# Display results
type_summary
Average Math Score	Average Reading Score	% Passing Math	% Passing Reading	% Overall Passing
School Type					
Charter	83.473852	83.896421	93.620830	96.586489	90.432244
District	76.956733	80.966636	66.548453	80.799062	53.672208