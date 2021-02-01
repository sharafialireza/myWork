##Author: Ali R. Sharafi 01/30/2021

# Temperature API lookup by IP Address and Creating its Histogram 

This is a Python application that can take an input file containing IP location of cities around the world and will lookup to 
find its weather forecasts. This program will plot a histogram of the temperatures found and save the plot as .png along with a 
TSV text format file of the results. The TSV file will contain two columns: The count and the histogram edges for the 
number of bins. 

The use can choose the input file (containing the IP address as tsv format), the output file for the 
results of TSV format file and the number of buckets as follows:

    $ python3 main.py --input IN_FILENAME.TSV --output OUT_FILENAME.TSV --bucket-count X

The histogram plot as .png will always be created as "hist_graph_temp.png". There is also another file generated for 
all types of logs generated (success or failure) during the application running including errors encountered. The log file is 
called "log-error.txt". The total of errors and its percent is also printed on the screen at the end.

The application needs an account to the sites supplying the weather forecasts and usually there is a limit 
to the number of API calls made to those web sites such as https://openweathermap.org/ where this application 
makes those API calls for free of charge. I have my API token hard coded and there is a limit of the 
number of API calls made for free. This limit is 60 API calls per minute. 

Hence to avoid this application being blocked by those sites there is a time delay inserted when the number of API calls reaches to 59. 
The time delay is 60seconds between each 59 API calls batch. At the moment the total number of API calls 
are set to max 500 calls but that can be changed to higher number (or less for testing purposes) if required by updating variable 
"number_of_rows" in the python application.

Note: It is assumed the IP location info in the input file is at column 23.

### Choosing Fahrenheit or Celsius

The default temperature unit used in the application is Fahrenheit. However that can be changed to use 
Celsius by updating the python file. Find variable "temperature_unit" in main.py and put metric 
instead of imperial, like:

    temperature_unit = "metric"

## Requirements

The Python application was created using Pyhton3.7 so it is recommended to use this version (or above) to avoid any possible issue
with older version of Python. There are several module required for this application to work properly which are given in the 
requirements.txt file.

The Python application can be run from a Docker container. To successfully run this python application, you need
to install the following tools/modules:

* docker - installed from [docker.io](https://www.docker.com/)
* python >= 3.7 - the python code is written and in python3, make sure you have proper python interpreter installed on your system
* python requests library - this is required for submitting multiple requests to the api server. You can run `pip install requests`
* python pandas - open source data analysis and manipulation tool, built on top of the Python programming language.
* python matplotlib - is a multi-platform data visualization library built on NumPy arrays for plotting 2D plots of arrays
* python numpy - NumPy is a library for the Python programming language, adding support for large, multi-dimensional arrays
* python argparse - The argparse module makes it easy to write user-friendly command-line interfaces

## Running the application as a docker container

The application can also be run as docker container. The container image needs to be created using the 
Dockerfile and requirements.txt included.

1- Create a directory on your computer, save the python application file along with input file (IP 
location as tsv) and the Dockerfile supplied with application in that directory.

2- The container is created by using the following command. The python application file name supplied is 
named "main.py" and its Dockerfile is using that name to create the docker image. The Dockerfile needs to 
be updated for the name if main.py file name is changed.

    $ docker build -t city-forecasts .

3- Make sure you can access the internet from your host computer since the application make API calls 
to https://ipapi.co and https://api.openweathermap.org to fetch lat/long/city information and 
subsequently to fetch the temperature forecasts. If there is any failure, the log file will have an 
entry for it. The information about the fetched data will be also printed on screen for each IP selected 
from the input file.

4- To run the container image, run:

    $ docker run --mount -type=bind,source=/path/to/inputfile,target=/app city-forecasts --input in_filename.tsv --output out_filename.tsv --bucket-count 10

The printed information for a successful retrieval for an IP would be like:

    IP Value : 97.68.237.19
    Location:Melbourne, Florida, US
    today date: 2021-01-30
    forecasted temperature:
    31-01-2021: 70.56

### to check the list of image created after the container is created
 
    $ docker image list
    REPOSITORY                              TAG          IMAGE ID       CREATED         SIZE
    city-forecasts                          latest       3712dadaef78   12 hours ago    597MB

### to check the status of the running container

    $ docker ps -a
    CONTAINER ID   IMAGE            COMMAND                  CREATED         STATUS                      PORTS     NAMES
    b203e07645ec   city-forecasts   "python3 /app/main.p…"   9 hours ago     Exited (0) 9 hours ago                gifted_bouman

### to stop a container

    $ docker stop my_container

