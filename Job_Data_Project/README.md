# Introduction

This is my analysis of the data job market, focusing specifically on data analyst roles. This project was created out of a desire to navigate and understand the job market more effectively. It delves into the top-paying and in-demand skills to help find optimal job opportunities for data analysts.

The data sourced from [Luke Barousse's Python Course](https://lukebarousse.com/python) which provides a foundation for my analysis, containing detailed information on job titles, salaries, locations, and essential skills. Through a series of Python scripts, I explore key questions such as the most demanded skills, salary trends, and the intersection of demand and salary in data analytics.

# The Questions

Below are the questions I want to answer in my project:

1. What are the skills most in demand for the top 3 most popular data roles?
2. How are in-demand skills trending for Data Analysts?
3. How well do jobs and skills pay for Data Analysts?
4. What are the optimal skills for data analysts to learn? (High Demand AND High Paying) 

# Tools I Used

For my deep dive into the data analyst job market, I utilized the capabilities of several key tools:

- **Python:** The backbone of my analysis, allowing me to analyze the data and find critical insights. I also used the following Python libraries:
    - **Pandas Library:** Pandas was used for the analysis of the data.
    - **Matplotlib Library:** Matplotlib was used for visualization of the data.
    - **Seaborn Library:** Seaborn helped create more advanced and relevant visualizations.
- **Jupyter Notebooks:** The tool I used to run my Python scripts which let me easily include my notes and analysis.
- **Visual Studio Code:** The IDE for this project.
- **Git & GitHub:** Used for version control and sharing my Python code and analysis.

# Data Preparation and Cleanup

This section outlines the steps taken to prepare the data for analysis, ensuring accuracy and usability.

## Import & Clean Up Data

I started by uploaded the relevant python libraries for this project. Then, I loaded the dataset I used. Finally, I did some simple data cleaning to ensure that it was usable.

```python
# Importing the Libraries
import ast
import pandas as pd
import seaborn as sns
from datasets import load_dataset
import matplotlib.pyplot as plt  

# Loading the Data
dataset = load_dataset('lukebarousse/data_jobs')
df = dataset['train'].to_pandas()

# Data Cleaning
df['job_posted_date'] = pd.to_datetime(df['job_posted_date'])
df['job_skills'] = df['job_skills'].apply(lambda x: ast.literal_eval(x) if pd.notna(x) else x)
```

## Filter for US Jobs

Since I live in the US, I filtered my data to remove all other counties to make sure insights produced by this analysis would be applicable.

```python
df_US = df[df['job_country'] == 'United States']

```

# The Analysis

Each of the 5 Jupyter notebooks for this project had the goal of investigating specific aspects of the data job market as introduced in the questions section. Here’s how I approached each question:

## 1. What are the most demanded skills for the top 3 most popular data roles?

To find the skills that were the most in demand for the top 3 data roles with the most job postings, I first specified those positions by which ones were the most popular, and then found the top 5 skills for these top 3 roles. This highlights the most popular job titles and their top skills, which provides me with the skills I should pay attention to depending on the role I am targeting. Below I have selected a relevant section of the code on visualization.

View my notebook with all of the steps here: [2_Skill_Demand](2_Skill_Demand.ipynb).

### Visualize Data

```python
fig, ax = plt.subplots(len(job_titles), 1)

sns.set_theme(style='whitegrid')

for i, job_title in enumerate(job_titles):
    df_plot = df_skills_perc[df_skills_perc['job_title_short'] == job_title].head(5)
    sns.barplot(data=df_plot, x='skill_percent', y='job_skills', ax=ax[i], hue='skill_count', palette='dark:r_r')
    ax[i].set_title(job_title)
    ax[i].set_ylabel('')
    ax[i].set_xlabel('')
    ax[i].get_legend().remove()
    ax[i].set_xlim(0, 78)
    if i != len(job_titles) - 1:
        ax[i].set_xticks([])
    for n, v in enumerate(df_plot['skill_percent']):
        ax[i].text(v + 1, n, f'{v:.0f}%', va='center')

fig.suptitle('Likelihood of Skills Requested in US Job Postings', fontsize=15)
fig.tight_layout(h_pad=.8)
plt.show()
```

### Results

![Likelihood of Skills Requested in the US Job Postings](images/Likelihood_of_Skills_Requested_in_US_Job_Postings.png)

*Bar graph visualizing the salary for the top 3 data roles and their top 5 skills associated with each.*

### Insights:

- SQL is in the largest amount of job postings for both Data Analysts and Data Engineers while being number 2 for Data Scientists.
- Python is another highly sought after skill for all 3 roles, being number 1 for Data Scientists, number 2 for Data Engineers, and number 4 for Data Analysts.
- While Data Analysts and Data Scientists share 4 of the same skills in their top 5, Data Engineers only share the aforementioned python and sql. Data Engineers, instead, need more unique skills like aws, azure, and spark.

## 2. How are in-demand skills trending for Data Analysts?

To investigate this question, I first filtered for only data analyst job postings and grouped the exploded job skills by month of the posting. Then I graph the top 5 most demanded skills by month, showing how the popular skills were trending throughout 2023. Below is the graph and some selected code.

View my notebook with detailed steps here: [3_Skills_Trend](3_Skills_Trend.ipynb).

### Visualize Data

```python
from matplotlib.ticker import PercentFormatter

df_plot = df_DA_US_percent.iloc[:, :5]
sns.lineplot(data=df_plot, dashes=False, legend='full', palette='tab10')
sns.set_theme(style='whitegrid')
sns.despine()

plt.title('Trending Top Skills for Data Analysts in the US')
plt.ylabel('Likelihood in Job Posting')
plt.xlabel('2023')
plt.legend().remove()
plt.gca().yaxis.set_major_formatter(PercentFormatter(decimals=0))

for i in range(5):
    plt.text(11.2, df_plot.iloc[-1, i], df_plot.columns[i], color='black')

plt.show()
```

### Results

![Trending Top Skills for Data Analysts in the US](images/Trending_Top_Skills_for_Data_Analysts_in_the_US.png)  
*Bar graph visualizing the trending top skills for data analysts in the US in 2023.*

### Insights:
- Consistent with the previous insights, SQL is the most in demand skill for every month, although it does slowly decline in prevalence over the year.
- Excel was the second most requested skill throughout the year, but experienced a significant decrease and increase in the second half of the year.
- Python and Tableau were similarly sought after for the duration of the year, switching positions a couple times.

## 3. How well do jobs and skills pay for Data Analysts?

To pinpoint the highest-paying jobs and skills, I first filtered for jobs in the United States and their respective median salary. Then, I looked at the salary distributions of common data jobs like Data Scientist, Data Engineer, and Data Analyst, and their senior roles, to get an idea of which jobs are paid the most. 

View my notebook with detailed steps here: [4_Salary_Analysis](4_Salary_Analysis.ipynb).

#### Visualize Data 

```python
sns.boxplot(data=df_US_top6, x='salary_year_avg', y='job_title_short', order=job_order)
sns.set_theme(style='whitegrid')
sns.despine()

plt.title('Salary Distributions of Data Jobs in the US')
plt.xlabel('Yearly Salary (USD)')
plt.ylabel('')
plt.xlim(0, 600000) 
ticks_x = plt.FuncFormatter(lambda y, pos: f'${int(y/1000)}K')
plt.gca().xaxis.set_major_formatter(ticks_x)
plt.show()
```

#### Results

![Salary Distributions of Data Jobs in the US](images/Salary_Distributions_of_Data_Jobs_in_the_US.png)  
*Box plot visualizing the salary distributions for the top 6 data job titles.*

#### Insights

- The difference in salaries among the selected positions is very significant. Senior Data Scientist positions have the highest median salaray and salary potential, which indicates the value placed in advanced and varied data skills and experience.

- All of these roles show a considerable number of outliers above the box plot maximum, which suggests that exceptional skills or circumstances can cause there to be high pay.

- The median salaries increase with the seniority and specialization of the roles. The data shows a consistent increase in salary from data analyst roles to data engineer roles to data scientist roles. This increase holds true for the senior positions as well.

### Highest Paid & Most Demanded Skills for Data Analysts

Next, I narrowed my analysis by focusing on only data analyst roles. I looked at both the highest-paid skills and the most in-demand skills. I used two horizontal bar charts to showcase these. Below is selected code and the charts.

#### Visualize Data

```python
fig, ax = plt.subplots(2, 1)  

# Top 10 Highest Paid Skills for Data Analysts
sns.barplot(data=df_DA_top_pay, x='median', y=df_DA_top_pay.index, hue='median', ax=ax[0], palette='dark:r_r')
ax[0].legend().remove()
ax[0].set_title('Highest Paid Skills for Data Analysts in the US')
ax[0].set_ylabel('')
ax[0].set_xlabel('')
ax[0].xaxis.set_major_formatter(plt.FuncFormatter(lambda x, _: f'${int(x/1000)}K'))


# Top 10 Most In-Demand Skills for Data Analysts
sns.barplot(data=df_DA_skills, x='median', y=df_DA_skills.index, hue='median', ax=ax[1], palette='light:r')
ax[1].legend().remove()
ax[1].set_title('Most In-Demand Skills for Data Analysts in the US')
ax[1].set_ylabel('')
ax[1].set_xlabel('Median Salary (USD)')
ax[1].set_xlim(ax[0].get_xlim())  # Set the same x-axis limits as the first plot
ax[1].xaxis.set_major_formatter(plt.FuncFormatter(lambda x, _: f'${int(x/1000)}K'))

sns.set_theme(style='whitegrid')
plt.tight_layout()
plt.show()
```

#### Results
Here's the breakdown of the highest-paid & most in-demand skills for data analysts in the US:

![The Highest Paid & Most In-Demand Skills for Data Analysts in the US](images/Highest_Paid_and_Most_In_Demand_Skills_for_Data_Analysts_in_the_US.png)

*Two separate bar graphs visualizing the highest paid skills and most in-demand skills for data analysts in the US.*

#### Insights:

- The top graph shows that more specialized skills like dplyr, bitbucket, and gitlab are correlated to higher salaries, almost reaching $200k.

- The bottom graph displays how the most common skills do not have as high of a median salary as the aforementioned more advanced skills. Despite this they are still highly important due to them being highly demanded and usualy being foundational to the higher paying skills.

- There's a clear distinction between the skills that are highest paid and those that are most in-demand. Data analysts aiming to maximize their career potential should consider developing a diverse skill set that includes both high-paying specialized skills and widely demanded skills.

## 4. What are the most optimal skills to learn for Data Analysts?

To identify the most optimal skills to learn and master, the ones that are the both highest paid and highest in demand, I calculated the percent of skill demand and the median salary of these skills and compared them to easily identify which are the most optimal skills to learn. I also color coded the skills based on what type of technology they are associated with. Below is selected code and the resulting graph.

View my notebook with detailed steps here: [5_Optimal_Skills](5_Optimal_Skills.ipynb).

#### Visualize Data

```python
sns.scatterplot(
    data=df_DA_skills_tech_high_demand,
    x='skill_percent',
    y='median_salary',
    hue='technology'
)

sns.despine()
sns.set_theme(style='ticks')

# Prepare texts for adjustText
texts = []
for i, txt in enumerate(df_DA_skills_high_demand.index):
    texts.append(plt.text(df_DA_skills_high_demand['skill_percent'].iloc[i], df_DA_skills_high_demand['median_salary'].iloc[i], txt))

# Adjust text to avoid overlap
adjust_text(texts, arrowprops=dict(arrowstyle='->', color='gray'))

# Set axis labels, title, and legend
plt.xlabel('Percent of Data Analyst Jobs')
plt.ylabel('Median Yearly Salary')
plt.title('Most Optimal Skills for Data Analysts in the US')
plt.legend(title='Technology')

from matplotlib.ticker import PercentFormatter
ax = plt.gca()
ax.yaxis.set_major_formatter(plt.FuncFormatter(lambda y, pos: f'${int(y/1000)}K'))
ax.xaxis.set_major_formatter(PercentFormatter(decimals=0))

# Adjust layout and display plot 
plt.tight_layout()
plt.show()
```

#### Results

![Most Optimal Skills for Data Analysts in the US](images/Most_Optimal_Skills_for_Data_Analysts_in_the_US.png)    
*A scatter plot visualizing the most optimal skills (high paying & high demand) for data analysts in the US.*

#### Insights:

- The skill `Oracle` has the highest median pay among regularly requested job skills. This highlights an importance of cloud databases in some data analyst positions.

- The two most commonly referenced skills in job postings, `SQL` and `Excel`, do not have a high median salary as compared to the next two most commonly referenced, `Python` and `Tableau`.

- Programming skills and analyst tools are the most common among the top 12 skills. Although, programming skills such as `Python` and `R` are consistently higher paid than analyst tools such as `Excel` and `Power Bi`.

- Two of the top 5 highest median salary jobs in this graph as database related, highlighting the importance of databases to the functions of a data analyst position.

# What I Learned

Throughout this project, I furthered my understanding of the data analyst job market and enhanced my technical skills in Python, especially in data manipulation and visualization. Here are a few specific things I learned:

- **Advanced Python Usage**: Utilizing libraries such as Pandas for data manipulation, Seaborn and Matplotlib for data visualization, and other libraries helped me perform complex data analysis tasks more efficiently.
- **Data Cleaning Importance**: I learned that thorough data cleaning and preparation are crucial before any analysis can be conducted, ensuring the accuracy of insights derived from the data.
- **Strategic Skill Analysis**: The project emphasized the importance of aligning one's skills with market demand. Understanding the relationship between skill demand, salary, and job availability allows for more strategic career planning in the tech industry.


# Insights

This project provided several general insights into the data job market for analysts:

- **Skill Demand and Salary Correlation**: There is a clear correlation between the demand for specific skills and the salaries these skills command. Advanced and specialized skills like Python and Oracle often lead to higher salaries.
- **Market Trends**: There are changing trends in skill demand, highlighting the dynamic nature of the data job market. Keeping up with these trends is essential for career growth in data analytics.
- **Economic Value of Skills**: Understanding which skills are both in-demand and well-compensated can guide data analysts in prioritizing learning to maximize their economic returns.


# Challenges I Faced

This project was not without its challenges, but it provided good learning opportunities:

- **Data Inconsistencies**: Handling missing or inconsistent data entries requires careful consideration and thorough data-cleaning techniques to ensure the integrity of the analysis.
- **Complex Data Visualization**: Designing effective visual representations of complex datasets was challenging but critical for conveying insights clearly and compellingly.
- **Balancing Breadth and Depth**: Deciding how deeply to dive into each analysis while maintaining a broad overview of the data landscape required constant balancing to ensure comprehensive coverage without getting lost in details.


# Conclusion

This analysis game me far more knowledge of the overall data roles market as well as, more specifically, the data analyst job market. The insights I got enhance my understanding and provide actionable guidance for anyone looking to advance their career in data analytics. As the market continues to change, ongoing analysis will be essential to stay ahead in data analytics. This project is a good foundation for future explorations and underscores the importance of continuous learning and adaptation in the data field.


