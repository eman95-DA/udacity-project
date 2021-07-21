# udacity-project1-Bikeshare
import time
import pandas as pd
import numpy as np

CITY_DATA = { 'chicago': 'chicago.csv',
              'new york city': 'new_york_city.csv',
              'washington': 'washington.csv' }

def get_filters():
    """
    Asks user to specify a city, month, and day to analyze.

    Returns:
        (str) city - name of the city to analyze
        (str) month - name of the month to filter by, or "all" to apply no month filter
        (str) day - name of the day of week to filter by, or "all" to apply no day filter
    """
    print('Hello! Let\'s explore some US bikeshare data!')
    
    
        
       
    # TO DO: get user input for city (chicago, new york city, washington). HINT: Use a while loop to handle invalid inputs
    while True:   
      city= input("enter the city from chicago, new york city, washington :  ").lower()
      if city not in CITY_DATA :
        print("choose a city from chicago, new york city, washington")
      else:
          break 
     
    # TO DO: get user input for month (all, january, february, ... , june)
    while True:
        month=input('please enter the month from january till june or   "all" ').lower()
        months=['january','february','march','april','may','june']
        if month != 'all' and month not in months:
          print('please enter a valid month or type "all"')
        else:
            break
              

    # TO DO: get user input for day of week (all, monday, tuesday, ... sunday)
    while True:
        day=input('please enter the day :  or type "all"').lower()
        days=['saturday','sunday','monday','tuesday','wednesday','thursday','friday']
        if day != 'all' and day not in days:
           print("please enter a valid day")
        else: 
             break
           
    

    print('-'*40)
    return city, month, day


def load_data(city, month, day):
    """
    Loads data for the specified city and filters by month and day if applicable.

    Args:
        (str) city - name of the city to analyze
        (str) month - name of the month to filter by, or "all" to apply no month filter
        (str) day - name of the day of week to filter by, or "all" to apply no day filter
    Returns:
        df - Pandas DataFrame containing city data filtered by month and day
    """
         # load data file into a dataframe       
    df = pd.read_csv(CITY_DATA[city]) 
       # convert the Start Time column to datetime
    df['Start Time'] =df['Start Time'].apply(pd.to_datetime)
         # extract month and day of week from Start Time to create new columns
    df['month'] = df['Start Time'].dt.month
    df['day_of_week'] = df['Start Time'].dt.weekday_name
          # filter by month if applicable
    if month != 'all':
        # use the index of the months list to get the corresponding int
        months = ['january', 'february', 'march', 'april', 'may', 'june']
        month = months.index(month) + 1

        # filter by month to create the new dataframe
        df = df[df['month'] == month]

    # filter by day of week if applicable
    if day != 'all':
        # filter by day of week to create the new dataframe
        df = df[df['day_of_week'] == day.title()]      

    return df


def time_stats(df):
    """Displays statistics on the most frequent times of travel."""

    print('\nCalculating The Most Frequent Times of Travel...\n')
    start_time = time.time()

    # TO DO: display the most common month

    popular_month = df['month'].mode()[0]
    print('Most Popular month:', popular_month)
    # TO DO: display the most common day of week
    popular_day = df['day_of_week'].mode()[0]
    print('Most Popular day:', popular_day)
    # TO DO: display the most common start hour
    df['hour'] = df['Start Time'].dt.hour  # extract hour from the Start Time column to create an hour column
    popular_hour = df['hour'].mode()[0]# find the most popular hour

    print('Most Popular Start Hour:', popular_hour)

    print("\nThis took %s seconds." % round(time.time() - start_time,2))
    print('-'*40)


def station_stats(df):
    """Displays statistics on the most popular stations and trip."""

    print('\nCalculating The Most Popular Stations and Trip...\n')
    start_time = time.time()

    # TO DO: display most commonly used start station
    common_start = df['Start Station'].mode()[0]
    print('Most Popular Start station:',common_start )
    # TO DO: display most commonly used end station
    common_end = df['End Station'].mode()[0]
    print('Most Popular End station:',common_end )

    # TO DO: display most frequent combination of start station and end station trip
    most_common_start_end_station = df.groupby(['Start Station','End Station']).count().idxmax()
    print("The most commonly used start station and end station : ",most_common_start_end_station) 
          

    print("\nThis took %s seconds." % round(time.time() - start_time,2))
    print('-'*40)


def trip_duration_stats(df):
    """Displays statistics on the total and average trip duration."""
    
    print('\nCalculating Trip Duration...\n')
    start_time = time.time()

    # TO DO: display total travel time
    total_travel_time = df['Trip Duration'].sum()
    t=time.gmtime(total_travel_time)
    res = time.strftime("%H:%M:%S",t)
    print("The total travel time in Hours:min:sec " , res)

    # TO DO: display mean travel time
    mean_travel_time = df['Trip Duration'].mean()
    print("The mean travel time from the given fitered data is: ", mean_travel_time)

    print("\nThis took %s seconds." % round(time.time() - start_time,2))
    print('-'*40)
def display_rawdata(df):
      """ display data on user request """
      print(df.head())
      count=0
      while True: 
         view_data=input(" would you like to see 5 more rows : yes\no").lower()
         if view_data == 'no':
             return
         
         count = count + 5
         print(df.iloc[count:count+5])
        

def user_stats(df):
    """Displays statistics on bikeshare users."""

    print('\nCalculating User Stats...\n')
    start_time = time.time()
    
    # TO DO: Display counts of user types
    user_types = df['User Type'].value_counts()

    print('Number of users types:',user_types)

    # TO DO: Display counts of gender
    #if city == 'chicago' or city == 'new_york_city':
    if 'Gender' in df:
        print('count of gender :',df['Gender'].value_counts())
                

    # TO DO: Display earliest, most recent, and most common year of birth
   # if city == 'chicago' or city == 'new_york_city':
    if 'Birth Year' in df:
         print('the earliest year of birth is:',int(df['Birth Year'].min()))
         print('the most recent year of birth is :',int(df['Birth Year'].max()))
         print('the most common year of birth is:',int(df['Birth Year'].mode()[0]))

         print("\nThis took %s seconds." % round(time.time() - start_time,2))
         print('-'*40)
                      
                         
def main():
   while True:
        city, month, day = get_filters()
        df = load_data(city, month, day)
        display_rawdata(df)
        time_stats(df)
        station_stats(df)
        trip_duration_stats(df)
        user_stats(df)
       
        restart = input('\nWould you like to restart? Enter yes or no.\n')
        
        if restart.lower() =='yes' :
               main()
        elif restart.lower() =='no':
                break
        else :
            print("invalid enter")
            restart = input('\nWould you like to restart? Enter yes or no.\n')
if _name_ == "_main_":
	 main()
