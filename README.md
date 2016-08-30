# Airbnb Spider: Airbnb advanced search using Scrapy 

## Intro

I tend to travel a lot, and for big cities Airbnb just doesn't seem to
cut it. I didn't want to have to write this script, but there is no way
to do any sort of advanced Airbnb search by default, and they limit you
to 300 results per search which means it is a chore to see their 
entire inventory. 

Luckily, they make it easy by storing most of their listing data in big 
javascript objects at the bottom of the page in some meta HTML tags. Of 
course, if this were to change, the script would have to be modified to 
work with their new DOM.


## Example Usage

```
crawl airbnb_spider -a city=Madrid -a country=Spain \
    -a check_in=9/27/2016 -a check_out=11/04/2016 \
    -a max_price=1800 \
    -a neighborhoods="Acacias,Almagro,Arganzuela,Argüelles,Centro,Cortes,Embajadores,Imperial,Jerónimos,La Latina,Malasaña,Moncloa,Palacio,Recoletos,Retiro,Salamanca,Sol" \ 
    -s CANNOT_HAVE="studio" \
    -s MUST_HAVE="(balcon|terra|patio)" \
    -s WEB_BROWSER="/usr/bin/chromium %s" \
    -o madrid.xlsx 
```

## Scraping Description

After running the above command, the scraper will start. It will first 
run the search query, then determine the quantity of result pages, and
finally iterate through each of those, scraping each of the property 
listings on each page.

Scraped items (listings) will be passed to the default item pipeline,
where, optionally, the `description`, `name`, `summary`, and `reviews` 
fields will be filtered using either or both of the `CANNOT_HAVE` and 
`MUST_HAVE` regexes. Filtered items will be dropped. Accepted items 
can be optionally opened in a given web browser, so that you can easily 
view your search results.

Finally, the output can be saved to an xlsx format file for additional
filtering, sorting, and inspection.


## Parameters

You can find the values for these by first doing a search manually on 
the Airbnb site. 

* `city`, `state`: City and State to search. **(required)** 
* `check_in`, `check_out`: Check-in and Check-out dates. **(required)** 
* `max_price`: Maximum price for the period. The Airbnb search algo seems
  to scale this based upon search length. So it will be either the
  daily, weekly, or monthly price depending on the length of the stay. 
  **(required)**
* `neighborhoods`: Comma-separated list of neighborhoods within the city
  to filter for. **(optional)**
* `output`: Name of output file. Only xlsx output is tested. 
  **(optional)**

         
## Settings

These settings can be edited in the `settings.py` file, or appended to 
the command line using the `-s` flag as in the example above.

* `CANNOT_HAVE="<cannot-have-regex>"`  
  Don't accept listings that match the given regex pattern. 
  **(optional)**
  
* `FIELDS_TO_EXPORT=['field1', 'field2', ...]`  
  Can be found in settings.py. Contains a list of all possible fields to 
  export, i.e. all fields of `AirbnbScraperItem`. Comment items to 
  remove undesired fields from output.
  
* `MUST_HAVE="(<must-have-regex>)"`  
  Only accept listings that match the given regex pattern. 
  **(optional)**

* `WEB_BROWSER="/path/to/browser %s"`  
  Web browser executable command. **(optional)**  
    
  *Examples*:
  - MacOS  
  `WEB_BROWSER="open -a /Applications/Google\ Chrome.app"`

  - Windows  
  `WEB_BROWSER="C:\Program Files (x86)\Google\Chrome\Application\chrome.exe"`
    
  - Linux  
  `WEB_BROWSER="/usr/bin/google-chrome"`


## Requirements

* [Scrapy 1.1.2](http://scrapy.org/)

* [openpyxl 2.3.5](https://openpyxl.readthedocs.io/en/default/#installation)


## Credits

This project was originally inspired by 
[this excellent blog post](http://www.verginer.eu/blog/web-scraping-airbnb/) 
by Luca Verginer.