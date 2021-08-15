# Python primer VII, Data Science I, Fall 2020. 

## Topics to cover

- A few super useful Libraries
- More on dictionaries

## Useful materials

- slides from class
- https://www.scipy.org/ (ecosystem for many packages)
- https://www.w3schools.com/python/
- will add more

<p>&nbsp;</p>

## About libraries

While a majority of the course so far has been writing scripts on our own to process data, thousands of libraries have been written to do almost anything you can think of in python. The basics are important and necessary but a majority of real data science and analysis in real world contexts will use open-source libraries. Today we will go over a view common ones: 

- os
- NumPy
- pandas
- matplotlib

## Installing libraries

Python is extremely efficient and only has a view commands loaded and installed from the beginning. There are libraries we will have to install and import to be able to use. Everyone should have pip or pip3 available and this will be used to install any libraries we might need now and moving forward. 

Install libraries from the terminal

    $ pip3 install numpy
    $ pip3 install pandas
    $ pip3 install matplotlib
    
You might get a permissions error. If so, install like so:

    $ pip3 install --user matplotlib

## Importing and using libraries in python

We have already done this with os and re, so you shoudl be familiar. THere are some tricks though. Sometimes we might want to create aliases or only access paticular functions in libraries. 

Examples:

    import os,re  #import multiple libraries in single line
    import numpy as np #import package as alias
    from numpy import DataFrame #import only specific function

Common aliases you will see when searching issues:

    import numpy as np
    import pandas as pd
    import matplotlib.pyplot as plt

## os 

We have used `os` a bit in this course previously but we can extend it to pretty much do anything you can do in terminal (ls, cd, mkdir, find) but in python.

Make a directory called week12 and copy over the logfiles directory from week 11 assignment


    import os

    #create variable with logfile path
    week12_dir = '/Users/trevorfaske/Desktop/Classes/BIOL792/week12'
    log_dir = os.path.join(week12_dir,'logfiles') #joins paths

    #assert that the directory exists
    assert os.path.exists(log_dir), 'log_dir does not exist'

    #change directory to logfiles (same as cd)
    os.chdir(log_dir) 

    #get current working directory (same as pwd)
    print(os.getcwd())

    #use os.system() to count txt files in logfiles
    os.system('ls *txt | wc -l')

    #make a output directory (same as mkdir)
    output_dir = os.path.join(week12_dir,'output')
    os.mkdir(output_dir)

This may seem redundant but can be very useful for writing scripts that create that have many many steps and create multiple directories for things to be stored. Also, great for debugging code (hint: assert)

## NumPy (https://numpy.org/)

other good source (https://www.w3schools.com/python/numpy_intro)

Numpy is a popular array – processing package of Python. It also does a lot of mathmatical processes. Similar functions to those used in MATLAB. Everything is array/matrix based and works faster than a list. Great for anything involving linear algebra. There are many packages that does basic math but nice to use one package for everything so syntax is consistant! 

The most common type ndarray 

    import numpy as np 

    #create 1-D array
    d1 = np.array([1,3,5,2,4,6])
    type(d1) #numpy.ndarray

    #create 2-D array
    d2 = np.array([1,3,5],[2,4,6])
    print(d2)
    #[[1 3 5]
    #[2 4 6]]

    #get dimensions and total size
    d2.shape #(2,3)
    d2.size #6

Accessing and indexing arrays work very similar to lists but with added dimension. Very similar to R programming for those familar.  

array[row,column] #REMEMBER starts at 0
    # 2nd row, 1st column 
    print(d2[1,0]) #2

    # 1st row, 3rd column
    print(d2[0,2]) #5

Slicing works very similarly
    #extract first 2 elements of the 2nd row
    print(d2[1,:2]) # [2 4]


arange and reshape array format
    d1 = np.array([1,3,5,2,4,6])
    d1.reshape(2,3)
    #[[1 3 5]
    #[2 4 6]]

    d2.reshape(1,6)  
    #[[1, 3, 5, 2, 4, 6]]

    #create 1D array 1-10
    np.arange(10)
    #[0 1 2 3 4 5 6 7 8 9]

    #array 0 to 50 by 5 (start,stop,step)
    np.arange(0,51,5)
    #[ 0,  5, 10, 15, 20, 25, 30, 35, 40, 45, 50]

Random number generator
    from numpy import random
    
    #Generate a random float from 0 to 1
    random.rand()

    #generate 5 random float from 0 to 1
    random.rand(5)

    #generate random integer between 0-100
    random.randint(100)

    #generate 7 random integer between 0-100
    random.randint(100, size = 7)

    #generate 2D array random integer between 0-100 
    random.randint(100, size=(2,3))

Choose or randomly sample list/array
    #sample from list
    random.choice([3, 5, 7, 9])

    #sample 4 elements from list 
    random.choice([3, 5, 7, 9],size=4)

Math (https://numpy.org/doc/stable/reference/routines.math.html)
    #generate 100 random numbers from 1 to 1000
    x = random.randint(1000,size=100)

    #get max, min, mean
    print(x.max())
    print(x.min())
    print(x.mean())

## pandas

https://github.com/jvns/pandas-cookbook

pandas is a "must learn" for data science. It allows you to easily manipulate data and data structures of various formats for downstream processing. pandas data structures and syntax is going to look very similar to those who know and work in R. It is part of the SciPy ecosystem so works great for plotting and data analysis. 

The most common format in the pandas in DataFrame (same as in R). This consists of a matrix with rows and columns and will very similar to what you would expect an excel sheet or csv file to look like. 

**Make sure you move covid_states.csv**

pandas indexing works very similar to numpy or any matrix indices but make sure you use df.iloc[row,column]

Read, Subset, Write a csv file
    
    import pandas as pd

    state_covid_df = pd.read_csv('states_covid.csv') #read in csv

    state_covid_df.shape #(14111, 42) row and column length
    state_covid_df.head() #views the top 5 lines
    state_covid_df.columns #views the column names

    #### Select only columns date, state, death, negative, postive, totalTestResults #####
    new_covid_df = state_covid_df[['date','state','death','positive','negative','totalTestResults']]
    new_covid_df.head() 

    #### two ways to change column names totalTestResults to total ####
    new_covid_df.columns = ['date','state','death','positive','negative','total']
    new_covid_df.rename({'totalTestResults':'total'},axis=1,inplace=True)

    #### two ways to reorder columns ####
    new_covid_df = new_covid_df[['date','state','total','negative','positive','death']]
    new_covid_df = new_covid_df.iloc[:,[0,1,5,4,3,2]] #iloc is how you axis df like a matrix

    #### add a column for positivity rate ####
    new_covid_df['rate'] = new_covid_df.positive / new_covid_df.total
    new_covid_df['rate'] = new_covid_df['positive'] / new_covid_df['total']

    #### subsetting NV only ####
    new_covid_df.state == 'NV' #this is how you peak in
    NV_covid_df = new_covid_df[new_covid_df.state == 'NV']

    NV_covid_df.death.describe() # get summary stats

    #### Write out NV_covid_df to csv ####
    week12_dir = '/home/faske/g/BIOL792/week12'
    NV_out = os.path.join(week12_dir,'NV_covid.csv') #this just maks sure you write it to the correct place

    NV_covid_df.to_csv(path_or_buf=NV_out)
   

## matplotlib

Not going to go too much into this but just so you know it exists. matplotlib is the most common plotting function in python I think. There are many others though! matplotlib is nice cause it's in the SciPy family so works nicely with pandas and NumPy and other things. Also allows you to integrate ggplot and seaborn if you would like. 

Simple figures with Covid data

    import matplotlib.pyplot as plt

    #### read in covid data ####
    NV_covid_df = pd.read_csv('NV_covid.csv',parse_dates=['date'],index_col=None) #makes date a date object and row names

    # Make the graphs a bit prettier, and bigger
    plt.style.use('seaborn-whitegrid')
    plt.rcParams['figure.figsize'] = (15, 5)

    #### plot deaths over time ####
    NV_covid_df.death.plot()

    #### deaths by day of the week ####
    weekday_counts = NV_covid_df.groupby('weekday').aggregate(sum)
    weekday_counts.index = ['Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday', 'Sunday']
    weekday_counts

    weekday_counts.death.plot(kind='bar')

    weekday_counts[weekday_counts.death == weekday_counts.death.max()]
    #monday has most deaths total


Ideas... generate null distribution from bunch of values. Plot outliers and null! 

## Other useful libraries

- BioPython / SeqIO (molecular biology)
- scikit-learn (statistics and machine learning)
- TenserFlow (Deep Learning)
- BeautifulSoup (Scraping HTML)
- seaborn (extension of matplotlib)
- plotly (more ploting)
- iPython (notebooks / will go over in Julie's course)
- r2py (run R script within python)
- literally anything you can think of, a library exists for it