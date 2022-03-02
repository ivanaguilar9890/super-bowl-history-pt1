# super-bowl-history
Primarily used the Wikipedia API alongside other Python modules to parse Super Bowl data into csv files.

In part 1, I attempt to get data from the main chart that details Super Bowl championships from the following Wikipedia page:
https://en.wikipedia.org/wiki/List_of_Super_Bowl_champions. While I could use beautifulsoup to scrape this data directly, Wikipedia has an API with community support which is better to use. In short, there is less fear that Wikipedia might impose a ban because of webscraping. 



```python
!pip install wikipedia
import wikipedia
from bs4 import BeautifulSoup
import pandas as pd
```

    Collecting wikipedia
      Downloading wikipedia-1.4.0.tar.gz (27 kB)
    Requirement already satisfied: beautifulsoup4 in /usr/local/lib/python3.7/dist-packages (from wikipedia) (4.6.3)
    Requirement already satisfied: requests<3.0.0,>=2.0.0 in /usr/local/lib/python3.7/dist-packages (from wikipedia) (2.23.0)
    Requirement already satisfied: chardet<4,>=3.0.2 in /usr/local/lib/python3.7/dist-packages (from requests<3.0.0,>=2.0.0->wikipedia) (3.0.4)
    Requirement already satisfied: idna<3,>=2.5 in /usr/local/lib/python3.7/dist-packages (from requests<3.0.0,>=2.0.0->wikipedia) (2.10)
    Requirement already satisfied: urllib3!=1.25.0,!=1.25.1,<1.26,>=1.21.1 in /usr/local/lib/python3.7/dist-packages (from requests<3.0.0,>=2.0.0->wikipedia) (1.24.3)
    Requirement already satisfied: certifi>=2017.4.17 in /usr/local/lib/python3.7/dist-packages (from requests<3.0.0,>=2.0.0->wikipedia) (2021.10.8)
    Building wheels for collected packages: wikipedia
      Building wheel for wikipedia (setup.py) ... [?25l[?25hdone
      Created wheel for wikipedia: filename=wikipedia-1.4.0-py3-none-any.whl size=11695 sha256=d90bd73632eb71a784e5fbab48c53620ee1cda89a13faa26d374f2b6ad65508c
      Stored in directory: /root/.cache/pip/wheels/15/93/6d/5b2c68b8a64c7a7a04947b4ed6d89fb557dcc6bc27d1d7f3ba
    Successfully built wikipedia
    Installing collected packages: wikipedia
    Successfully installed wikipedia-1.4.0
    

Using the Wikipedia module we can get the html content of a page which we may then convert to a BeautifulSoup object to filter through data we actually need.


```python
wikipedia.set_rate_limiting(True)
```


```python
parsed_html = BeautifulSoup(wikipedia.WikipediaPage("List of Super Bowl champions").html())
```


```python
superbowl_table = parsed_html.body.find('table', attrs={'class':'sortable'})
```


```python

data_dictionary = {}
# get the column headers of the Wikipedia table
for name in superbowl_table.findAll("th")[0:9]:
    data_dictionary[name.text.rstrip()] = []
# as shown below, all headers were filled approriately, just need to fill with
# data now
data_dictionary
```




    {'Attendance': [],
     'City': [],
     'Date/Season': [],
     'Game': [],
     'Losing team': [],
     'Referee': [],
     'Score': [],
     'Venue': [],
     'Winning team': []}




```python
# from the superbowl object we can get all the remaining data and pass it 
# directly into our earlier defined dictionary.
for row in superbowl_table.findAll("tr"):
  cells = row.findAll("td")
  if len(cells) == 10:
    for key in enumerate(data_dictionary):
      data_dictionary[key[1]].append(cells[key[0]].find(text=True).rstrip())
```


```python
superbowl_history_df = pd.DataFrame.from_dict(data_dictionary)
```


```python
# after creating the dataframe we have the following:
superbowl_history_df.head(5).append(superbowl_history_df.tail(5))
```

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Game</th>
      <th>Date/Season</th>
      <th>Winning team</th>
      <th>Score</th>
      <th>Losing team</th>
      <th>Venue</th>
      <th>City</th>
      <th>Attendance</th>
      <th>Referee</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>I</td>
      <td>January 15, 1967</td>
      <td>Green Bay Packers</td>
      <td>35–10</td>
      <td>Kansas City Chiefs</td>
      <td>Los Angeles Memorial Coliseum</td>
      <td>Los Angeles, California</td>
      <td>61,946</td>
      <td>Norm Schachter</td>
    </tr>
    <tr>
      <th>1</th>
      <td>II</td>
      <td>January 14, 1968</td>
      <td>Green Bay Packers</td>
      <td>33–14</td>
      <td>Oakland Raiders</td>
      <td>Miami Orange Bowl</td>
      <td>Miami, Florida</td>
      <td>75,546</td>
      <td>Jack Vest</td>
    </tr>
    <tr>
      <th>2</th>
      <td>III</td>
      <td>January 12, 1969</td>
      <td>New York Jets</td>
      <td>16–7</td>
      <td>Baltimore Colts</td>
      <td>Miami Orange Bowl</td>
      <td>Miami, Florida</td>
      <td>75,389</td>
      <td>Tom Bell</td>
    </tr>
    <tr>
      <th>3</th>
      <td>IV</td>
      <td>January 11, 1970</td>
      <td>Kansas City Chiefs</td>
      <td>23–7</td>
      <td>Minnesota Vikings</td>
      <td>Tulane Stadium</td>
      <td>New Orleans, Louisiana</td>
      <td>80,562</td>
      <td>John McDonough</td>
    </tr>
    <tr>
      <th>4</th>
      <td>V</td>
      <td>January 17, 1971</td>
      <td>Baltimore Colts</td>
      <td>16–13</td>
      <td>Dallas Cowboys</td>
      <td>Miami Orange Bowl</td>
      <td>Miami, Florida</td>
      <td>79,204</td>
      <td>Norm Schachter</td>
    </tr>
    <tr>
      <th>54</th>
      <td>LV</td>
      <td>February 7, 2021</td>
      <td>Tampa Bay Buccaneers</td>
      <td>31–9</td>
      <td>Kansas City Chiefs</td>
      <td>Raymond James Stadium</td>
      <td>Tampa, Florida</td>
      <td>24,835</td>
      <td>Carl Cheffers</td>
    </tr>
    <tr>
      <th>55</th>
      <td>LVI</td>
      <td>February 13, 2022</td>
      <td>Los Angeles Rams</td>
      <td>23–20</td>
      <td>Cincinnati Bengals</td>
      <td>SoFi Stadium</td>
      <td>Inglewood, California</td>
      <td>70,048</td>
      <td>Ron Torbert</td>
    </tr>
    <tr>
      <th>56</th>
      <td>LVII</td>
      <td>February 12, 2023</td>
      <td>X 2023</td>
      <td>—</td>
      <td>To be determined</td>
      <td>State Farm Stadium</td>
      <td>Glendale, Arizona</td>
      <td>TBD</td>
      <td></td>
    </tr>
    <tr>
      <th>57</th>
      <td>LVIII</td>
      <td>February 11, 2024</td>
      <td>X 2024</td>
      <td>—</td>
      <td>To be determined</td>
      <td>Allegiant Stadium</td>
      <td>Paradise, Nevada</td>
      <td>TBD</td>
      <td></td>
    </tr>
    <tr>
      <th>58</th>
      <td>LIX</td>
      <td>February 9, 2025</td>
      <td>X 2025</td>
      <td>—</td>
      <td>To be determined</td>
      <td>Caesars Superdome</td>
      <td>New Orleans, Louisiana</td>
      <td>TBD</td>
      <td></td>
    </tr>
  </tbody>
</table>
</div>

```python
superbowl_history_df.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 59 entries, 0 to 58
    Data columns (total 9 columns):
     #   Column        Non-Null Count  Dtype 
    ---  ------        --------------  ----- 
     0   Game          59 non-null     object
     1   Date/Season   59 non-null     object
     2   Winning team  59 non-null     object
     3   Score         59 non-null     object
     4   Losing team   59 non-null     object
     5   Venue         59 non-null     object
     6   City          59 non-null     object
     7   Attendance    59 non-null     object
     8   Referee       59 non-null     object
    dtypes: object(9)
    memory usage: 4.3+ KB
    

Changes to be made for first dataset:
*   Remove last 3 rows as they are Super Bowls that have yet to occur.
*   Change 'Date/Season' column to just 'Date'. Choice was made to not include 'Season' as Super Bowls are always played on the year after a season e.g. Super Bowl from 2022 had teams from the 2021 season. In short, very easy to know/infer so can do away with removing Season.
*   Convert new 'Date' Column to DateTime object.
*   Split [Score] into [Winner Score] and [Loser Score] -> after that convert new columns into integers
*   Split [City] into [City] and [State] columns
*   Convert values of [Attendance] to integers


```python
superbowl_history_df.head(5)
```

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Game</th>
      <th>Date/Season</th>
      <th>Winning team</th>
      <th>Score</th>
      <th>Losing team</th>
      <th>Venue</th>
      <th>City</th>
      <th>Attendance</th>
      <th>Referee</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>I</td>
      <td>January 15, 1967</td>
      <td>Green Bay Packers</td>
      <td>35–10</td>
      <td>Kansas City Chiefs</td>
      <td>Los Angeles Memorial Coliseum</td>
      <td>Los Angeles, California</td>
      <td>61,946</td>
      <td>Norm Schachter</td>
    </tr>
    <tr>
      <th>1</th>
      <td>II</td>
      <td>January 14, 1968</td>
      <td>Green Bay Packers</td>
      <td>33–14</td>
      <td>Oakland Raiders</td>
      <td>Miami Orange Bowl</td>
      <td>Miami, Florida</td>
      <td>75,546</td>
      <td>Jack Vest</td>
    </tr>
    <tr>
      <th>2</th>
      <td>III</td>
      <td>January 12, 1969</td>
      <td>New York Jets</td>
      <td>16–7</td>
      <td>Baltimore Colts</td>
      <td>Miami Orange Bowl</td>
      <td>Miami, Florida</td>
      <td>75,389</td>
      <td>Tom Bell</td>
    </tr>
    <tr>
      <th>3</th>
      <td>IV</td>
      <td>January 11, 1970</td>
      <td>Kansas City Chiefs</td>
      <td>23–7</td>
      <td>Minnesota Vikings</td>
      <td>Tulane Stadium</td>
      <td>New Orleans, Louisiana</td>
      <td>80,562</td>
      <td>John McDonough</td>
    </tr>
    <tr>
      <th>4</th>
      <td>V</td>
      <td>January 17, 1971</td>
      <td>Baltimore Colts</td>
      <td>16–13</td>
      <td>Dallas Cowboys</td>
      <td>Miami Orange Bowl</td>
      <td>Miami, Florida</td>
      <td>79,204</td>
      <td>Norm Schachter</td>
    </tr>
  </tbody>
</table>
</div>

```python
# remove last 3 rows by using indices
superbowl_history_df.drop([56, 57, 58], inplace=True)
```

Changes to be made for first dataset:
*   ~~Remove last 3 rows as they are Super Bowls that have yet to occur.~~
*   Change 'Date/Season' column to just 'Date'. Choice was made to not include 'Season' as Super Bowls are always played on the year after a season e.g. Super Bowl from 2022 had teams from the 2021 season. In short, very easy to know/infer so can do away with removing Season.
*   Convert new 'Date' Column to DateTime object.
*   Split [Score] into [Winner Score] and [Loser Score] -> after that convert new columns into integers
*   Split [City] into [City] and [State] columns
*   Convert values of [Attendance] to integers


```python
# rename column and change to date-time object
superbowl_history_df.rename(columns={"Date/Season": "Date"}, inplace=True)
superbowl_history_df["Date"] = pd.to_datetime(superbowl_history_df['Date'])
superbowl_history_df.head()
```

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Game</th>
      <th>Date</th>
      <th>Winning team</th>
      <th>Score</th>
      <th>Losing team</th>
      <th>Venue</th>
      <th>City</th>
      <th>Attendance</th>
      <th>Referee</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>I</td>
      <td>1967-01-15</td>
      <td>Green Bay Packers</td>
      <td>35–10</td>
      <td>Kansas City Chiefs</td>
      <td>Los Angeles Memorial Coliseum</td>
      <td>Los Angeles, California</td>
      <td>61,946</td>
      <td>Norm Schachter</td>
    </tr>
    <tr>
      <th>1</th>
      <td>II</td>
      <td>1968-01-14</td>
      <td>Green Bay Packers</td>
      <td>33–14</td>
      <td>Oakland Raiders</td>
      <td>Miami Orange Bowl</td>
      <td>Miami, Florida</td>
      <td>75,546</td>
      <td>Jack Vest</td>
    </tr>
    <tr>
      <th>2</th>
      <td>III</td>
      <td>1969-01-12</td>
      <td>New York Jets</td>
      <td>16–7</td>
      <td>Baltimore Colts</td>
      <td>Miami Orange Bowl</td>
      <td>Miami, Florida</td>
      <td>75,389</td>
      <td>Tom Bell</td>
    </tr>
    <tr>
      <th>3</th>
      <td>IV</td>
      <td>1970-01-11</td>
      <td>Kansas City Chiefs</td>
      <td>23–7</td>
      <td>Minnesota Vikings</td>
      <td>Tulane Stadium</td>
      <td>New Orleans, Louisiana</td>
      <td>80,562</td>
      <td>John McDonough</td>
    </tr>
    <tr>
      <th>4</th>
      <td>V</td>
      <td>1971-01-17</td>
      <td>Baltimore Colts</td>
      <td>16–13</td>
      <td>Dallas Cowboys</td>
      <td>Miami Orange Bowl</td>
      <td>Miami, Florida</td>
      <td>79,204</td>
      <td>Norm Schachter</td>
    </tr>
  </tbody>
</table>
</div>


Changes to be made for first dataset:
*   ~~Remove last 3 rows as they are Super Bowls that have yet to occur.~~
*   ~~Change 'Date/Season' column to just 'Date'. Choice was made to not include 'Season' as Super Bowls are always played on the year after a season e.g. Super Bowl from 2022 had teams from the 2021 season. In short, very easy to know/infer so can do away with removing Season.~~
*   ~~Convert new 'Date' Column to DateTime object.~~
*   Split [Score] into [Winner Score] and [Loser Score] -> after that convert new columns into integers
*   Split [City] into [City] and [State] columns
*   Convert values of [Attendance] to integers


```python
# Score is in XX-YY format -> strip and seperate
winner_score = []
loser_score = []
for score in superbowl_history_df["Score"]:
  scores = score.split("–")
  winner_score.append(scores[0])
  loser_score.append(scores[1])
# re-write column, rename it, and create new column
superbowl_history_df["Score"] = winner_score
superbowl_history_df.rename(columns={"Score": "Winner Score"}, inplace=True)
superbowl_history_df.insert(loc=5, column="Loser Score", value=loser_score)
```


```python
superbowl_history_df.head().append(superbowl_history_df.tail(5))
```
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Game</th>
      <th>Date</th>
      <th>Winning team</th>
      <th>Winner Score</th>
      <th>Losing team</th>
      <th>Loser Score</th>
      <th>Venue</th>
      <th>City</th>
      <th>Attendance</th>
      <th>Referee</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>I</td>
      <td>1967-01-15</td>
      <td>Green Bay Packers</td>
      <td>35</td>
      <td>Kansas City Chiefs</td>
      <td>10</td>
      <td>Los Angeles Memorial Coliseum</td>
      <td>Los Angeles, California</td>
      <td>61,946</td>
      <td>Norm Schachter</td>
    </tr>
    <tr>
      <th>1</th>
      <td>II</td>
      <td>1968-01-14</td>
      <td>Green Bay Packers</td>
      <td>33</td>
      <td>Oakland Raiders</td>
      <td>14</td>
      <td>Miami Orange Bowl</td>
      <td>Miami, Florida</td>
      <td>75,546</td>
      <td>Jack Vest</td>
    </tr>
    <tr>
      <th>2</th>
      <td>III</td>
      <td>1969-01-12</td>
      <td>New York Jets</td>
      <td>16</td>
      <td>Baltimore Colts</td>
      <td>7</td>
      <td>Miami Orange Bowl</td>
      <td>Miami, Florida</td>
      <td>75,389</td>
      <td>Tom Bell</td>
    </tr>
    <tr>
      <th>3</th>
      <td>IV</td>
      <td>1970-01-11</td>
      <td>Kansas City Chiefs</td>
      <td>23</td>
      <td>Minnesota Vikings</td>
      <td>7</td>
      <td>Tulane Stadium</td>
      <td>New Orleans, Louisiana</td>
      <td>80,562</td>
      <td>John McDonough</td>
    </tr>
    <tr>
      <th>4</th>
      <td>V</td>
      <td>1971-01-17</td>
      <td>Baltimore Colts</td>
      <td>16</td>
      <td>Dallas Cowboys</td>
      <td>13</td>
      <td>Miami Orange Bowl</td>
      <td>Miami, Florida</td>
      <td>79,204</td>
      <td>Norm Schachter</td>
    </tr>
    <tr>
      <th>51</th>
      <td>LII</td>
      <td>2018-02-04</td>
      <td>Philadelphia Eagles</td>
      <td>41</td>
      <td>New England Patriots</td>
      <td>33</td>
      <td>U.S. Bank Stadium</td>
      <td>Minneapolis, Minnesota</td>
      <td>67,612</td>
      <td>Gene Steratore</td>
    </tr>
    <tr>
      <th>52</th>
      <td>LIII</td>
      <td>2019-02-03</td>
      <td>New England Patriots</td>
      <td>13</td>
      <td>Los Angeles Rams</td>
      <td>3</td>
      <td>Mercedes-Benz Stadium</td>
      <td>Atlanta, Georgia</td>
      <td>70,081</td>
      <td>John Parry</td>
    </tr>
    <tr>
      <th>53</th>
      <td>LIV</td>
      <td>2020-02-02</td>
      <td>Kansas City Chiefs</td>
      <td>31</td>
      <td>San Francisco 49ers</td>
      <td>20</td>
      <td>Hard Rock Stadium</td>
      <td>Miami Gardens, Florida</td>
      <td>62,417</td>
      <td>Bill Vinovich</td>
    </tr>
    <tr>
      <th>54</th>
      <td>LV</td>
      <td>2021-02-07</td>
      <td>Tampa Bay Buccaneers</td>
      <td>31</td>
      <td>Kansas City Chiefs</td>
      <td>9</td>
      <td>Raymond James Stadium</td>
      <td>Tampa, Florida</td>
      <td>24,835</td>
      <td>Carl Cheffers</td>
    </tr>
    <tr>
      <th>55</th>
      <td>LVI</td>
      <td>2022-02-13</td>
      <td>Los Angeles Rams</td>
      <td>23</td>
      <td>Cincinnati Bengals</td>
      <td>20</td>
      <td>SoFi Stadium</td>
      <td>Inglewood, California</td>
      <td>70,048</td>
      <td>Ron Torbert</td>
    </tr>
  </tbody>
</table>
</div>

```python
# there are 56 rows so we want to check that all values are indeed numbers ~ we
# could do this by just looking at each row but better to do so programatically
display(superbowl_history_df["Winner Score"].str.isnumeric().value_counts())
display(superbowl_history_df["Loser Score"].str.isnumeric().value_counts())
# from this we see that the [Winner Score] column is fine but not for [Loser Score]
# i.e. we have one non integer value ~ use regex to remove said value (was just the
# "(" character)
print("After removing non-numeric")
# all of them are now numeric meaning we can convert the columns to number type
superbowl_history_df["Loser Score"] = superbowl_history_df["Loser Score"].str.extract('(\d+)', expand=False)
superbowl_history_df["Loser Score"].str.isnumeric().value_counts()
```


    True    56
    Name: Winner Score, dtype: int64



    True    56
    Name: Loser Score, dtype: int64


    
    




    True    56
    Name: Loser Score, dtype: int64




```python
superbowl_history_df["Winner Score"] = pd.to_numeric(superbowl_history_df["Winner Score"])
superbowl_history_df["Loser Score"] = pd.to_numeric(superbowl_history_df["Loser Score"])
```


```python
superbowl_history_df.head().append(superbowl_history_df.tail(5))
```

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Game</th>
      <th>Date</th>
      <th>Winning team</th>
      <th>Winner Score</th>
      <th>Losing team</th>
      <th>Loser Score</th>
      <th>Venue</th>
      <th>City</th>
      <th>Attendance</th>
      <th>Referee</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>I</td>
      <td>1967-01-15</td>
      <td>Green Bay Packers</td>
      <td>35</td>
      <td>Kansas City Chiefs</td>
      <td>10</td>
      <td>Los Angeles Memorial Coliseum</td>
      <td>Los Angeles, California</td>
      <td>61,946</td>
      <td>Norm Schachter</td>
    </tr>
    <tr>
      <th>1</th>
      <td>II</td>
      <td>1968-01-14</td>
      <td>Green Bay Packers</td>
      <td>33</td>
      <td>Oakland Raiders</td>
      <td>14</td>
      <td>Miami Orange Bowl</td>
      <td>Miami, Florida</td>
      <td>75,546</td>
      <td>Jack Vest</td>
    </tr>
    <tr>
      <th>2</th>
      <td>III</td>
      <td>1969-01-12</td>
      <td>New York Jets</td>
      <td>16</td>
      <td>Baltimore Colts</td>
      <td>7</td>
      <td>Miami Orange Bowl</td>
      <td>Miami, Florida</td>
      <td>75,389</td>
      <td>Tom Bell</td>
    </tr>
    <tr>
      <th>3</th>
      <td>IV</td>
      <td>1970-01-11</td>
      <td>Kansas City Chiefs</td>
      <td>23</td>
      <td>Minnesota Vikings</td>
      <td>7</td>
      <td>Tulane Stadium</td>
      <td>New Orleans, Louisiana</td>
      <td>80,562</td>
      <td>John McDonough</td>
    </tr>
    <tr>
      <th>4</th>
      <td>V</td>
      <td>1971-01-17</td>
      <td>Baltimore Colts</td>
      <td>16</td>
      <td>Dallas Cowboys</td>
      <td>13</td>
      <td>Miami Orange Bowl</td>
      <td>Miami, Florida</td>
      <td>79,204</td>
      <td>Norm Schachter</td>
    </tr>
    <tr>
      <th>51</th>
      <td>LII</td>
      <td>2018-02-04</td>
      <td>Philadelphia Eagles</td>
      <td>41</td>
      <td>New England Patriots</td>
      <td>33</td>
      <td>U.S. Bank Stadium</td>
      <td>Minneapolis, Minnesota</td>
      <td>67,612</td>
      <td>Gene Steratore</td>
    </tr>
    <tr>
      <th>52</th>
      <td>LIII</td>
      <td>2019-02-03</td>
      <td>New England Patriots</td>
      <td>13</td>
      <td>Los Angeles Rams</td>
      <td>3</td>
      <td>Mercedes-Benz Stadium</td>
      <td>Atlanta, Georgia</td>
      <td>70,081</td>
      <td>John Parry</td>
    </tr>
    <tr>
      <th>53</th>
      <td>LIV</td>
      <td>2020-02-02</td>
      <td>Kansas City Chiefs</td>
      <td>31</td>
      <td>San Francisco 49ers</td>
      <td>20</td>
      <td>Hard Rock Stadium</td>
      <td>Miami Gardens, Florida</td>
      <td>62,417</td>
      <td>Bill Vinovich</td>
    </tr>
    <tr>
      <th>54</th>
      <td>LV</td>
      <td>2021-02-07</td>
      <td>Tampa Bay Buccaneers</td>
      <td>31</td>
      <td>Kansas City Chiefs</td>
      <td>9</td>
      <td>Raymond James Stadium</td>
      <td>Tampa, Florida</td>
      <td>24,835</td>
      <td>Carl Cheffers</td>
    </tr>
    <tr>
      <th>55</th>
      <td>LVI</td>
      <td>2022-02-13</td>
      <td>Los Angeles Rams</td>
      <td>23</td>
      <td>Cincinnati Bengals</td>
      <td>20</td>
      <td>SoFi Stadium</td>
      <td>Inglewood, California</td>
      <td>70,048</td>
      <td>Ron Torbert</td>
    </tr>
  </tbody>
</table>
</div>

```python
superbowl_history_df.info()
```

    <class 'pandas.core.frame.DataFrame'>
    Int64Index: 56 entries, 0 to 55
    Data columns (total 10 columns):
     #   Column        Non-Null Count  Dtype         
    ---  ------        --------------  -----         
     0   Game          56 non-null     object        
     1   Date          56 non-null     datetime64[ns]
     2   Winning team  56 non-null     object        
     3   Winner Score  56 non-null     int64         
     4   Losing team   56 non-null     object        
     5   Loser Score   56 non-null     int64         
     6   Venue         56 non-null     object        
     7   City          56 non-null     object        
     8   Attendance    56 non-null     object        
     9   Referee       56 non-null     object        
    dtypes: datetime64[ns](1), int64(2), object(7)
    memory usage: 4.8+ KB
    

Changes to be made for first dataset:
*   ~~Remove last 3 rows as they are Super Bowls that have yet to occur.~~
*   ~~Change 'Date/Season' column to just 'Date'. Choice was made to not include 'Season' as Super Bowls are always played on the year after a season e.g. Super Bowl from 2022 had teams from the 2021 season. In short, very easy to know/infer so can do away with removing Season.~~
*   ~~Convert new 'Date' Column to DateTime object.~~
*   ~~Split [Score] into [Winner Score] and [Loser Score] -> after that convert new columns into integers~~
*   Split [City] into [City] and [State] columns
*   Convert values of [Attendance] to integers


```python
# city is in "CITY, STATE" format -> strip and seperate
cities = []
states = []
for location in superbowl_history_df["City"]:
  locations = location.split(", ")
  
  cities.append(locations[0])
  states.append(locations[1])

superbowl_history_df["City"] = cities
superbowl_history_df.insert(loc=8, column="State", value=states)
```


```python
superbowl_history_df.head().append(superbowl_history_df.tail(5))
```

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Game</th>
      <th>Date</th>
      <th>Winning team</th>
      <th>Winner Score</th>
      <th>Losing team</th>
      <th>Loser Score</th>
      <th>Venue</th>
      <th>City</th>
      <th>State</th>
      <th>Attendance</th>
      <th>Referee</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>I</td>
      <td>1967-01-15</td>
      <td>Green Bay Packers</td>
      <td>35</td>
      <td>Kansas City Chiefs</td>
      <td>10</td>
      <td>Los Angeles Memorial Coliseum</td>
      <td>Los Angeles</td>
      <td>California</td>
      <td>61,946</td>
      <td>Norm Schachter</td>
    </tr>
    <tr>
      <th>1</th>
      <td>II</td>
      <td>1968-01-14</td>
      <td>Green Bay Packers</td>
      <td>33</td>
      <td>Oakland Raiders</td>
      <td>14</td>
      <td>Miami Orange Bowl</td>
      <td>Miami</td>
      <td>Florida</td>
      <td>75,546</td>
      <td>Jack Vest</td>
    </tr>
    <tr>
      <th>2</th>
      <td>III</td>
      <td>1969-01-12</td>
      <td>New York Jets</td>
      <td>16</td>
      <td>Baltimore Colts</td>
      <td>7</td>
      <td>Miami Orange Bowl</td>
      <td>Miami</td>
      <td>Florida</td>
      <td>75,389</td>
      <td>Tom Bell</td>
    </tr>
    <tr>
      <th>3</th>
      <td>IV</td>
      <td>1970-01-11</td>
      <td>Kansas City Chiefs</td>
      <td>23</td>
      <td>Minnesota Vikings</td>
      <td>7</td>
      <td>Tulane Stadium</td>
      <td>New Orleans</td>
      <td>Louisiana</td>
      <td>80,562</td>
      <td>John McDonough</td>
    </tr>
    <tr>
      <th>4</th>
      <td>V</td>
      <td>1971-01-17</td>
      <td>Baltimore Colts</td>
      <td>16</td>
      <td>Dallas Cowboys</td>
      <td>13</td>
      <td>Miami Orange Bowl</td>
      <td>Miami</td>
      <td>Florida</td>
      <td>79,204</td>
      <td>Norm Schachter</td>
    </tr>
    <tr>
      <th>51</th>
      <td>LII</td>
      <td>2018-02-04</td>
      <td>Philadelphia Eagles</td>
      <td>41</td>
      <td>New England Patriots</td>
      <td>33</td>
      <td>U.S. Bank Stadium</td>
      <td>Minneapolis</td>
      <td>Minnesota</td>
      <td>67,612</td>
      <td>Gene Steratore</td>
    </tr>
    <tr>
      <th>52</th>
      <td>LIII</td>
      <td>2019-02-03</td>
      <td>New England Patriots</td>
      <td>13</td>
      <td>Los Angeles Rams</td>
      <td>3</td>
      <td>Mercedes-Benz Stadium</td>
      <td>Atlanta</td>
      <td>Georgia</td>
      <td>70,081</td>
      <td>John Parry</td>
    </tr>
    <tr>
      <th>53</th>
      <td>LIV</td>
      <td>2020-02-02</td>
      <td>Kansas City Chiefs</td>
      <td>31</td>
      <td>San Francisco 49ers</td>
      <td>20</td>
      <td>Hard Rock Stadium</td>
      <td>Miami Gardens</td>
      <td>Florida</td>
      <td>62,417</td>
      <td>Bill Vinovich</td>
    </tr>
    <tr>
      <th>54</th>
      <td>LV</td>
      <td>2021-02-07</td>
      <td>Tampa Bay Buccaneers</td>
      <td>31</td>
      <td>Kansas City Chiefs</td>
      <td>9</td>
      <td>Raymond James Stadium</td>
      <td>Tampa</td>
      <td>Florida</td>
      <td>24,835</td>
      <td>Carl Cheffers</td>
    </tr>
    <tr>
      <th>55</th>
      <td>LVI</td>
      <td>2022-02-13</td>
      <td>Los Angeles Rams</td>
      <td>23</td>
      <td>Cincinnati Bengals</td>
      <td>20</td>
      <td>SoFi Stadium</td>
      <td>Inglewood</td>
      <td>California</td>
      <td>70,048</td>
      <td>Ron Torbert</td>
    </tr>
  </tbody>
</table>
</div>


Changes to be made for first dataset:
*   ~~Remove last 3 rows as they are Super Bowls that have yet to occur.~~
*   ~~Change 'Date/Season' column to just 'Date'. Choice was made to not include 'Season' as Super Bowls are always played on the year after a season e.g. Super Bowl from 2022 had teams from the 2021 season. In short, very easy to know/infer so can do away with removing Season.~~
*   ~~Convert new 'Date' Column to DateTime object.~~
*   ~~Split [Score] into [Winner Score] and [Loser Score] -> after that convert new columns into integers~~
*   ~~Split [City] into [City] and [State] columns~~
*   Convert values of [Attendance] to integers


```python
# Before converting Attendance values into numbers, just need to get rid of commas
superbowl_history_df['Attendance']=superbowl_history_df['Attendance'].str.replace(',','')
```


```python
# check we can convert to numbers; all came up true so yes we can
display(superbowl_history_df["Attendance"].str.isnumeric().value_counts())
```


    True    56
    Name: Attendance, dtype: int64



```python
superbowl_history_df["Attendance"] = pd.to_numeric(superbowl_history_df["Attendance"])
```


```python
superbowl_history_df.info()
```

    <class 'pandas.core.frame.DataFrame'>
    Int64Index: 56 entries, 0 to 55
    Data columns (total 11 columns):
     #   Column        Non-Null Count  Dtype         
    ---  ------        --------------  -----         
     0   Game          56 non-null     object        
     1   Date          56 non-null     datetime64[ns]
     2   Winning team  56 non-null     object        
     3   Winner Score  56 non-null     int64         
     4   Losing team   56 non-null     object        
     5   Loser Score   56 non-null     int64         
     6   Venue         56 non-null     object        
     7   City          56 non-null     object        
     8   State         56 non-null     object        
     9   Attendance    56 non-null     int64         
     10  Referee       56 non-null     object        
    dtypes: datetime64[ns](1), int64(3), object(7)
    memory usage: 5.2+ KB
    


```python
superbowl_history_df.head()
```


<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Game</th>
      <th>Date</th>
      <th>Winning team</th>
      <th>Winner Score</th>
      <th>Losing team</th>
      <th>Loser Score</th>
      <th>Venue</th>
      <th>City</th>
      <th>State</th>
      <th>Attendance</th>
      <th>Referee</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>I</td>
      <td>1967-01-15</td>
      <td>Green Bay Packers</td>
      <td>35</td>
      <td>Kansas City Chiefs</td>
      <td>10</td>
      <td>Los Angeles Memorial Coliseum</td>
      <td>Los Angeles</td>
      <td>California</td>
      <td>61946</td>
      <td>Norm Schachter</td>
    </tr>
    <tr>
      <th>1</th>
      <td>II</td>
      <td>1968-01-14</td>
      <td>Green Bay Packers</td>
      <td>33</td>
      <td>Oakland Raiders</td>
      <td>14</td>
      <td>Miami Orange Bowl</td>
      <td>Miami</td>
      <td>Florida</td>
      <td>75546</td>
      <td>Jack Vest</td>
    </tr>
    <tr>
      <th>2</th>
      <td>III</td>
      <td>1969-01-12</td>
      <td>New York Jets</td>
      <td>16</td>
      <td>Baltimore Colts</td>
      <td>7</td>
      <td>Miami Orange Bowl</td>
      <td>Miami</td>
      <td>Florida</td>
      <td>75389</td>
      <td>Tom Bell</td>
    </tr>
    <tr>
      <th>3</th>
      <td>IV</td>
      <td>1970-01-11</td>
      <td>Kansas City Chiefs</td>
      <td>23</td>
      <td>Minnesota Vikings</td>
      <td>7</td>
      <td>Tulane Stadium</td>
      <td>New Orleans</td>
      <td>Louisiana</td>
      <td>80562</td>
      <td>John McDonough</td>
    </tr>
    <tr>
      <th>4</th>
      <td>V</td>
      <td>1971-01-17</td>
      <td>Baltimore Colts</td>
      <td>16</td>
      <td>Dallas Cowboys</td>
      <td>13</td>
      <td>Miami Orange Bowl</td>
      <td>Miami</td>
      <td>Florida</td>
      <td>79204</td>
      <td>Norm Schachter</td>
    </tr>
  </tbody>
</table>
</div>
     


```python
# can download the file uncode as necessary
#from google.colab import files
#superbowl_history_df.to_excel("superbowl.xlsx")
#files.download("superbowl.xlsx")
```


    <IPython.core.display.Javascript object>



    <IPython.core.display.Javascript object>



```python
superbowl_history_df
```

Done and data looks nice to work with now. For part 2 will attempt to get more specific in game data such

*   as points per quarter (and if applicable Overtime)
*   the MVP: player and position
*   Head Coach
*   Favorite Team Going into match
*   Cost of 30 second Commercial: Cost at time

