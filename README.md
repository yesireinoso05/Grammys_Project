# Grammys_Project

#The Purpose of this project is to work on real data from both websites owned by The Recording Academy, the non-profit organization behind the famous Grammy Awards. Ray Starck, the VP of Digital Strategy, decided to split the websites into grammy.com and recordingacademy.com to better serve the Recording Academy's various audience needs. The task is to examine the impact of splitting up the two websites, and analyzing the data for a better understanding of trends and audience behavior on both sites.

#The two files being used will be grammys_live_web_analytics.csv and ra_live_web_analytics.csv

# Import libraries
import pandas as pd
import plotly.express as px
import numpy as np

# this formats numbers to two decimal places when shown in pandas
pd.set_option('display.float_format', lambda x: '%.2f' % x)

# Read in dataframes
full_df = pd.read_csv("datasets/grammy_live_web_analytics.csv")
rec_academy = pd.read_csv("datasets/ra_live_web_analytics.csv")

# preview full_df dataframe
full_df.head()

date	visitors	pageviews	sessions	bounced_sessions	avg_session_duration_secs	awards_week	awards_night
0	2017-01-01	9611	21407	10196	6490	86	0	0
1	2017-01-02	10752	25658	11350	7055	100	0	0
2	2017-01-03	11425	27062	12215	7569	92	0	0
3	2017-01-04	13098	29189	13852	8929	90	0	0
4	2017-01-05	12234	28288	12990	8105	95	0	0

# preview rec_academy dataframe
rec_academy.head()

date	visitors	pageviews	sessions	bounced_sessions	avg_session_duration_secs	awards_week	awards_night
0	2022-02-01	928	2856	1092	591	148	0	0
1	2022-02-02	1329	3233	1490	923	90	0	0
2	2022-02-03	1138	3340	1322	754	127	0	0
3	2022-02-04	811	2552	963	534	142	0	0
4	2022-02-05	541	1530	602	326	111	0	0

#let's create a line chart of the number of users on the site for every day in the full_df to see what days the Grammys awards are hosted.

# Plot a line chart of the visitors on the site.
px.line(full_df,x='date',y='visitors')

# What does an "average" day looks like when the awards show is being hosted versus the other 364 days out of the year?

average = full_df.groupby('awards_night').agg({'visitors': 'sum'}).reset_index()

average

awards_night	visitors
0	0	75432297
1	1	18064673

# Create two new dataframes for the split companies: combined_site for all dates before 2022-02-01 and grammys for all dates after (and including) 2022-02-01

# Split the data to separate the full_df into two new dataframes.

combined_site = full_df[full_df['date'] < '2022-02-01']

grammys = full_df[full_df['date'] >= '2022-02-01']

# .copy() to prevent pandas from printing a scary-looking warning message
combined_site = combined_site.copy()
grammys = grammys.copy()

# print the shape of the combined_site dataframe
combined_site.shape

(1857, 8)

# create the list of dataframes
frames = [combined_site, rec_academy, grammys]

# create the `pages_per_session` column for all 3 dataframes.
for frame in frames:
    frame['pages_per_session'] = frame['pageviews'] / frame['sessions']

# combined_site graph
px.line(combined_site,x='date',y='pages_per_session')

# rec_academy graph
px.line(rec_academy,x='date',y='pages_per_session')

# now we have to find bounce Rate, a measure of the percentage of visitors who come to the site and never interact with the website and leave.

def bounce_rate(dataframe):
    '''
    Calculates the bounce rate for visitors on the website.
    input: dataframe with bounced_sessions and sessions columns
    output: numeric value from bounce rate
    '''
    # WRITE YOUR CODE BELOW
    # Remember, the input for the function is called `dataframe`
    # So all calculations should reference that variable.



    sum_bounced = dataframe['bounced_sessions'].sum()
    sum_sessions = dataframe['sessions'].sum()
    rate = (sum_bounced / sum_sessions) * 100
    return rate

# Calculate the Bounce Rate for each site
for frame in frames:
    my_value = bounce_rate(frame)
    print(f'my value is: {my_value:0.2f}')
    
    #I have mine ordered as combined_site, rec_academy, grammys

my value is: 41.58
my value is: 33.67
my value is: 40.16

# Calculate the average of the avg_session_duration_secs. Use the frames list you created in Task 6.
mean_v = grammys['avg_session_duration_secs'].mean()
mean_vi = rec_academy['avg_session_duration_secs'].mean()
mean_vii = combined_site['avg_session_duration_secs'].mean()

#the order is the mean for the grammys, rec_academy, and combined_site
print(f"The mean session duration is {mean_v}")
print(f"The mean session duration is {mean_vi}")
print(f"The mean session duration is {mean_vii}")

The mean session duration is 83.01446280991736
The mean session duration is 128.50309278350517
The mean session duration is 102.85352719439958

# Now to read in the grammys_age_demographics.csv and tra_age_demograhics.csv files and store them into dataframes named age_grammys and age_tra, respectively.

# read in the files
age_grammys = pd.read_csv("datasets/grammys_age_demographics.csv")
age_tra = pd.read_csv("datasets/tra_age_demographics.csv")

# preview the age_grammys file. the age_tra will look very similar.
age_grammys.head()

age_group	pct_visitors
0	18-24	27.37
1	25-34	24.13
2	35-44	18.72
3	45-54	13.57
4	55-64	9.82

# create the website column

column1 = age_grammys['website'] = 'grammys'

website2 = age_tra['website'] = 'Recording Academy'

# use pd.concat to join the two datasets
age_df = pd.concat([age_grammys, age_tra])
age_df.shape
(12, 3)

# Create a bar chart of the age_group and pct_visitors. This chart should have, for each age group, one color for the Recording Academy and a different color for the Grammys.
#create bar chart
px.bar(age_df, x='age_group', y='pct_visitors', color='website', barmode='group')

#Load in the two files. The desktop_users.csv and mobile_users.csv files contain the users coming from desktop users and mobile users respectively.
Store them in variables named desktop_users and mobile_users

# Load in the data
desktop_users = pd.read_csv("datasets/desktop_users.csv")
mobile_users = pd.read_csv("datasets/mobile_users.csv")

# preview the desktop_users file
desktop_users.head()

date	segment	visitors
0	2022-02-01	Desktop Traffic	10195
1	2022-02-02	Desktop Traffic	10560
2	2022-02-03	Desktop Traffic	9935
3	2022-02-04	Desktop Traffic	8501
4	2022-02-05	Desktop Traffic	5424

# preview mobile_users file
mobile_users.head()

date	segment	visitors
0	2022-02-01	Mobile Traffic	23494
1	2022-02-02	Mobile Traffic	20234
2	2022-02-03	Mobile Traffic	22816
3	2022-02-04	Mobile Traffic	18592
4	2022-02-05	Mobile Traffic	13298

#For each dataframe, let's change the name of the visitors column so that it says which category they come from. For example, the desktop_users dataframe should have a column named desktop_visitors instead of visitors.

# change name of the visitors column to indicate which category it comes from

desktop_users = desktop_users.rename(columns={'visitors': 'desktop_visitors'})

mobile_users = mobile_users.rename(columns={'visitors': 'mobile_visitors'})


# drop the segment column from each dataframe since it is not needed

mobile_users = mobile_users.drop(columns=['segment'])

desktop_users = desktop_users.drop(columns=['segment'])

date	visitors
0	2022-02-01	10195
1	2022-02-02	10560
2	2022-02-03	9935
3	2022-02-04	8501
4	2022-02-05	5424
...	...	...
510	2023-06-26	4302
511	2023-06-27	5528
512	2023-06-28	4928
513	2023-06-29	5554
514	2023-06-30	6336
515 rows × 2 columns

# join the two dataframes together in a new variable called segment_df
# join the two dataframes and preview the dataframe
segment_df = pd.concat([mobile_users, desktop_users])
segment_df.sample

<bound method NDFrame.sample of            date          segment  visitors
0    2022-02-01   Mobile Traffic     23494
1    2022-02-02   Mobile Traffic     20234
2    2022-02-03   Mobile Traffic     22816
3    2022-02-04   Mobile Traffic     18592
4    2022-02-05   Mobile Traffic     13298
..          ...              ...       ...
510  2023-06-26  Desktop Traffic      4302
511  2023-06-27  Desktop Traffic      5528
512  2023-06-28  Desktop Traffic      4928
513  2023-06-29  Desktop Traffic      5554
514  2023-06-30  Desktop Traffic      6336

[1030 rows x 3 columns]>

