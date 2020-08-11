# Congressional Voting EDA
## Motivation

The motivation behind this project comes from a desire to learn why members of Congress miss roll-call votes, the most important part of their job. In researching this project I learned that it is fairly common for member of Congress to miss 1-2% of votes during each session. However, there are notable outliers that miss a significantly higher percentage of votes. My goal was to understand what was going on this situation.

## Data Collection

The data for this project was collected using ProPublica's free API. The data consists of voting records for the past 15 Congressional sessions for the House, Senate, and individual members. The data is relatively clean, however there may be some inconsistencies between the individual `member` data and the data present in the `house` and `senate` voting records. This project primarily used the data in the `house` and `senate` directories.

```
congress-data
  |
  +-- house
  |
  +-- senate
  |
  +-- member (files named according to each member's unique ID)
  |
  +-- shapefiles
```

## Findings

### Something to keep in mind
This project focused purely on the quantity of bills that members missed votes for. There was no qualitative analysis conducted to determine which bills were more significant than others. If a member is missing a significant number of bills, it may very well be that they are missing bills they determine to be less important, such as commemorative or procedural bills.

### Approach
My approach to this project was to first conduct some exploratory data analysis to get a deeper understanding of the data. From there, I conducted hypothesis tests on any samples that I found to be peculiar. I ended up conducting hypothesis testing for the following groupings:
    - Gender
    - Party
    - Age
    - Seniority
    - Distance From DC
    
### 0. EDA
During my initial exploration of the data I identified 2 trends that were present causing specific members of Congress to miss roll-call votes.

![Senator Candidates](/images/Senate_pres.png)

```
      100+ Missed Votes - Senate
========================================
Spark Matsunaga    | Albert Gore
Tom Harkin         | Jesse Helms
J. Robert Kerrey   | David Pryor
John McCain        | Jesse Helms
John Edwards       | Bob Graham
John Kerry         | Joseph Lieberman
Zell Miller        | Jon Corzine
John Rockefeller   | Joseph Biden
Sam Brownback      | Hillary Clinton
Christopher Dodd   | Tim Johnson
Edward Kennedy     | John McCain
Barack Obama       | Robert Byrd
Edward Kennedy     | Mark Kirk
Tom Coburn         | Thad Cochran
Frank Lautenberg   | Ted Cruz
Lindsey Graham     | Marco Rubio
Bernard Sanders    | John McCain
Lamar Alexander    | Michael Bennet
Cory Booker        | Kirsten Gillibrand
Kamala Harris      | Johnny Isakson
Amy Klobuchar      | Bernard Sanders
Elizabeth Warren   | 
```
I found that Senators who declare a Presidential campaign will almost always miss signficantly more votes than their counterparts who do not run for President. This is true regardless of party, gender, and age.

![Speakers of the House](/images/Speakers.png)

```
     500+ Missed Votes - House
=======================================
Thomas Foley       | Thomas Foley
Craig Washington   | Newt Gingrich
Newt Gingrich      | Henry Gonzalez
Steven Schiff      | J. Dennis Hastert
J. Dennis Hastert  | Richard Gephardt
J. Dennis Hastert  | Lane Evans
J. Dennis Hastert  | Barbara Cubin
Jo Ann Davis       | Nancy Pelosi
Bobby Rush         | J. Barrett
Nancy Pelosi       | John Boehner
Gabrielle Giffords | Ron Paul
John Campbell      | Carolyn McCarthy
John Boehner       | Paul Ryan
Paul Ryan          | Nancy Pelosi
```
I also discovered that Representatives in the House will almost always miss significantly more votes if they are Speaker of the House. I suspect that this has something to with a tradition or rule present in the House, although I have not been able to figure out what it is.

**Note**: Because both of these trends are obvious and the number of missed votes for these members is so great, I made sure to remove these individual members from my analysis later on.

### 1. Gender
The first factor I decided to examine was gender. I wanted to see if the gender of members of Congress might be a factor in why they miss votes. I compared the House and the Senate separately, since they are two independent bodies.

#### House
![House Gender Distribution](/images/house_gen_dist.png)

Even when accounting for Speaker of the House, female Representatives miss more votes on average than male Representatives. I ran a hypothesis test to determine if this trend was statistically significant:

$H_0:%20\mu_f%20=%20\mu_m$
$H_a:%20\mu_f%20\neq%20\mu_m$

Because of the seriousness of this factor, I decided to establish a threshold of 0.02 necessary to reject the null hypothesis.

![House Gender P-Value](/images/house_gen_p_val.png)

After running a two-tailed t-test, I found a p-value of 0.006, meaning that I can reject the null hypothesis. This means that the fact that female Representatives miss more votes than male Representatives is statistically significant.

It is here that I would like to reiterate the point I made earlier: that this focuses purely on quantity of votes missed and pays no attention to the content of the votes that were missed.

#### Senate
![Senate Gender Distribution](/images/senate_gen_dist.png)

When accounting for sessions when Senators are running for President, we see that male Senators miss more votes on average than female Senators. I set up a hypothesis test to determine if this trend was statistically significant:

$H_0:%20\mu_m%20=%20\mu_f$
$H_a:%20\mu_m%20\neq%20\mu_f$

Because of the seriousness of this factor, I again decided to use a threshold of 0.02 necessary to reject the null hypothesis.

![Senate Gender P-Value](/images/senate_gen_p_val.png)

After running a two-tailed t-test, I found a p-value of 0.025, meaning that I cannot reject the null hypothesis, meaning that the fact that male Senators miss more votes than female Senators is not statistically significant. 

However, the difference in sample sizes in this case made me uneasy, so I decided to run a Mann-Whitney U-test to confirm the results of my t-test, using the same threshold.

![Senate Gender Mann_Whitney](/images/senate_gen_mann_whitnney.png)

In this case the p-value is below the threshold for rejection, meaning that the difference in average votes missed is statistically significant.


Ultimately, it is hard to make a conclusion about whether or not male Senators missing more votes than female Senators is statistically significant. More research will need to be done on the subject.

### 2. Party
I also decided to examine if members of a specific political party tend to miss more votes. Again, I compared the House and Senate separately because they are two independent bodies.

#### House
![House Party Distribution](/images/house_party_dist.png)

When accounting for Speaker of the House, Democrats miss more votes than Republicans. I established a hypothesis test to determine if this is statistically significant.

$H_0:%20\mu_d%20=%20\mu_r$
$H_a:%20\mu_d%20\neq%20\mu_r$

The insinutation that a member misses votes due to party carries much less weight than the same due to gender. Because of this, I decided to use the standard rejection threshold of 0.05.

![House Party P-Value](/images/house_party_p_val.png)

After running a two-tailed t-test, I found a p-value of 0.000. This incredibly small value allows me to reject the null hypothesis and determine that Democrats missing more votes than Republicans is statistically significant.

#### Senate
![Senate Party Distribution](/images/senate_party_dist)

After accounting for Senators who run for President, I found that Republicans miss more votes than Democrats on average. I ran a hypothesis test to determine if this is statistically significant, using a rejection threshold of 0.05.

$H_0:%20\mu_r%20=%20\mu_d$
$H_a:%20\mu_r%20\neq%20\mu_d$

![Senate Party P-Value](/images/senate_party_p_val.png)

After running a two-tailed t-test, I found a p-value of 0.102, meaning that I cannot reject the null hypothesis. This means that while Republicans miss more votes on average, this is not statistically significant.

### 3. Age
### 4. Seniority
### 5. Distance From DC