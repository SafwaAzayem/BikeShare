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
    # get user input for city (chicago, new york city, washington). HINT: Use a while loop to handle invalid inputs
    city= ' '
    while city not in CITY_DATA.keys(): 
        city = input ("Please enter the city you want to explore its bike share data, chicago, new york city or washington. (full city name, capital or small letters) ").lower()
        if city not in CITY_DATA.keys():
            print("Invalid input. Please check your input, it is not in an accepted input formats.")
            print("Restarting...")

    
    # get user input for month (all, january, february, ... , june)
    months = {'january': 1, 'february': 2, 'march': 3, 'april': 4, 'may': 5, 'june': 6, 'all': 7}
    month =' '
    while month not in months.keys(): 
        month = input('please enter the month you want to explore its data from january to june. (full month name, capital or small letters, if u want all months type all)').lower()
        if month not in months.keys():
            print("Invalid input. Please check your input, it is not in an accepted input formats.")
            print("Restarting...")
 

    # get user input for day of week (all, monday, tuesday, ... sunday)
    days = ['all', 'monday', 'tuesday', 'wednesday', 'thursday', 'friday', 'saturday', 'sunday' ]
    day=' '
    while day not in days:      
        day = input('Please type the day you want to explore its data (full day name, capital or small letters, if you want all days type all) ').lower()
        if day not in days:
            print("Invalid input. Please check your input, it is not in an accepted input formats.")
            print("Restarting...")
            
    print(f"\nYou have chosen to view data for {city.upper()} city, in {month.upper()}  month/s and in {day.upper()} day/s.")
  
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
    df['Start Time'] = pd.to_datetime(df['Start Time'])

    # extract month and day of week from Start Time to create new columns
    df['month'] = df['Start Time'].dt.month
    df['day_of_week'] = df['Start Time'].dt.weekday_name
    df['hour'] = df['Start Time'].dt.time
    
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

    # display the most common month
    popular_month = df['month'].mode()[0]
    print('Most common month is:', popular_month)
    
    # display the most common day of week
    popular_week_day = df['day_of_week'].mode()[0]
    print('Most common week day is:', popular_week_day)

    
    # display the most common start hour
    popular_start_hour = df['hour'].mode()[0]
    print('Most common start hour is:', popular_start_hour)


    print("\nThis took %s seconds." % (time.time() - start_time))
    print('-'*40)
    
    return df

def station_stats(df):
    """Displays statistics on the most popular stations and trip."""

    print('\nCalculating The Most Popular Stations and Trip...\n')
    start_time = time.time()

    # display most commonly used start station

    # find the most popular start station
    popular_start_station = df['Start Station'].mode()[0]
    print('Most Popular Start station:', popular_start_station)


    # display most commonly used end station
    popular_end_station = df['End Station'].mode()[0]

    print('Most Popular end station:', popular_end_station)


    # display most frequent combination of start station and end station trip
    popular_start_end_station = df[['Start Station', 'End Station']].mode().loc[0]

    print('Most Popular combination of start station and end station trip:', popular_start_end_station)


    print("\nThis took %s seconds." % (time.time() - start_time))
    print('-'*40)


def trip_duration_stats(df):
    """Displays statistics on the total and average trip duration."""

    print('\nCalculating Trip Duration...\n')
    start_time = time.time()

    # display total travel time
    
    travel_time_tot = df["Trip Duration"].sum()
    print(f"Total travel time is {round(travel_time_tot/60)} min") 


    # display mean travel time
    travel_time_av = (df["Trip Duration"].mean())
    print(f" Average travel time is {round(travel_time_av/60)} min") 



    print("\nThis took %s seconds." % (time.time() - start_time))
    print('-'*40)


def user_stats(df):
    """Displays statistics on bikeshare users."""

    print('\nCalculating User Stats...\n')
    start_time = time.time()

    # Display counts of user types
    user_types = df['User Type'].value_counts()
    print("counts for each user type:", user_types)
    

    # Display counts of gender
    
       # Chicago only has gender data, so will use try & except to avoid errors in new york & whashington
    try:
        user_gender = df['Gender'].value_counts()
        print("Counts for each user gender:", user_gender)
    except:
        print("There is no gender data in this file.")

    # Display earliest, most recent, and most common year of birth
    
       # Chicago only has birthyear data, so will use try & except to avoid errors in new york & whashington
    try:
        user_birth_earliest = int(df['Birth Year'].min())
        print("Earliest year of birth is", user_birth_earliest)
        user_birth_most_recent = int(df['Birth Year'].max())
        print("Most recent year of birth is", user_birth_most_recent)
        user_birth_ =  int(df["Birth Year"].mode()[0])
        print("Most common year of birth is", user_birth_)
    except:
        print("There is no birth year data in this file.")

        
    print("\nThis took %s seconds." % (time.time() - start_time))
    print('-'*40)
    
  
 #---------------------
 #checing raw data
    counter = 0
    response = input("Do you want to see the raw data? please answer with yes or no. ").lower()
    while True:
        if response == 'no':
            break
        if response == 'yes':
            print(df.iloc[counter: counter + 5])
            counter = counter + 5
            response = input("\n Would you like to see five more rows of the data used to compute the stats? Please write 'yes' or 'no' \n").lower()


def main():
    while True:
        city, month, day = get_filters()
        df = load_data(city, month, day)

        time_stats(df)
        station_stats(df)
        trip_duration_stats(df)
        user_stats(df)

        restart = input('\nWould you like to restart? Enter yes or no.\n')
        if restart.lower() != 'yes':
            break


if __name__ == "__main__":
	main()
#-------------------------------------------------------------------------------------------------------------
#===================================================================================
#--------------------------------------------------------------------
