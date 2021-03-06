# MB7621_modem_scraper
Python project to scrape Motorola MB7621 webpage info, save to sqlite db and do some charting. 

I have been having problems with my cable internet service, and one of the troubleshooting steps I took was to purchase a new modem. Still had the same problems. In order to convince my cable company that there were issues that had nothing to do with modem or house, I decided to collect some data. To that end I embarked on a project to learn python coding/charting, jupyter lab, web scraping and github. Before you are the fruits of my labor. 

Update: Tech came to my house on Sunday and replaced the cable between street and house. 24 hours has gone by and not one drop :-)

Please keep in mind that web scraping can be fragile and that firmware changes can and probably will break it. The version of the code here was developed on a modem with hardware version "V1.0" and software version "7621-5.7.1.5". You can find these by logging into your modem and looking on the Advanced>Software page. 

## What's Here?

- environment.yml is a conda environment file.
- config.cfg is where the user configuration information is stored. Currently it contains username and password for the modem and the interval used by collect_data.py.
- MB7621 is a python module that contains all the webpage scrapping code. 
- example.ipynb provides simple example of how to use the module
- collect_data.py is a more complicated example that collects the web scrappings into a sqlite database at a user specified interval. 
- reboot_modem.py does just that...reboots your modem. Be careful there are no checks...if you run this it will reboot your modem.
- charts.ipynb will generate up and downstream charts assuming you've collected some data into a sqlite database using collect_data.py.

## Modem Password

You need to figure out what your modem password really is. It is not the string that you type at the modem login page. There is a bit of javascript on the login page that scrambles your password before sending to the modem, and you need to discover what that scrambled password is. The easiest way is use tcpdump or wireshark or similar to capture a modem login session to a file and then search for the encoded password. On my mac that might look like the following:

```sudo tcpdump -lA host 192.168.100.1  > session.txt```

Now search for "loginPassword=" (make sure you include the equals sign "="). There should only be one entry if you logged in just once. It might look something like 

```loginUsername=admin&loginPassword=aAbBcCdDeEfF%3D%3D```

Note that some characters that start with % and followed by two characters are encoded. Go [here](https://www.w3schools.com/tags/ref_urlencode.ASP) to decode them. In my example, %3D is equal sign "=". So my password would be ```aAbBcCdDeEfF==```.

## Config.cfg

Edit the config.cfg file and enter your newly discovered password there. Note that quotes are not required. Here is what the config might look like:

```
[MB7621]
username=admin
password=aAbBcCdDeEfF==
interval=15
```

## Python Environment

First you need an environment to run in. I've provided an environment.yml file that you can use with conda like so:

```conda env create --file environment.yml```

That will create an environment called ```modem``` which contains the necessary modules. Activate it via ```conda activate modem```.

You should then be able to run ```jupyter lab``` and open the example.ipynb or run either of the two python scripts: ```python collect_data.py``` or ```python reboot_modem.py```

If you are going to run the collect_data.py script, it collects at 15 second intervals. If you want to collect faster or slower, edit config.cfg and change the interval. Interval defines the number of seconds in the future that next data collect should happen.

## Charts

Make sure you are in the MB7621_modem_scraper directory and then run ```jupyter lab```. Open the charts.ipynb file. Assuming you have run collect_data.py successfully and you have a sqlite3 database called MB7621.db, run all the cells in the charts.ipynb notebook. The cell containing "hours" defines how many hours back worth of data to display, starting from now. So hours=24 display the past 24 hours worth of data. If all goes well, you should end up with something like the following:

<img width="921" alt="charts" src="https://user-images.githubusercontent.com/3979338/89111961-5a230b00-d411-11ea-8926-f9b98e913198.png">
