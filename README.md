## Summary

I wrote this script to practise my web scraping skills, and to conduct an analysis about the characteristics of the demand of analytical positions. 

###**Strcuture**
This package has three components: one configure file (config.json), one module (linkedin.py) and several executable scripts. All constant variables that are be used by multiple scripts are store in the config file; all functions/classes that are used by multiple are stored in the module; each executable script is used to performance a specific task. 

###**How to Run the Script**

1. Install CouchDB
2. Install Python Library listed in the Dependency Section
3. Modify the config.json files.
  - Database
  - Linkedin Account Info
4. Modified the search keyword in jobs.py
5. Execute jobs.py, company.py and location.py in order
  - jobs.py is used to scrape the job info;
  - company.py is used to scrape company info if the company appear in the job post
  - location is used to standard the location info using Google Geocoded service.


## Dependency

- **BeautifulSoup**
  - Install: http://www.crummy.com/software/BeautifulSoup/
  - Documentation: http://www.crummy.com/software/BeautifulSoup/bs4/doc/
- **mechanize**
  - Install: https://pypi.python.org/pypi/mechanize/
  - Documentation: http://wwwsearch.sourceforge.net/mechanize/
- **couchdb**
  - Install: https://pypi.python.org/pypi/CouchDB/0.9
  - Documentation: https://pythonhosted.org/CouchDB/
- **GeoPy**
  - Install: https://pypi.python.org/pypi/geopy
  - Documentation: http://geopy.readthedocs.org/en/latest/index.html#

## Database

This set of scripts work with CouchDB NoSQL databases. All data are stored seperately in the following four types of database:
- **Job**: Stores the job information scraped from the source website;
- **Company**: Store the company information scraped from the source website;
- **Log**: Store the metadata of invidual scraped job, like the started datetime and the ending datetime;
- **Mapping**: Store the mapping relation to standard the raw scraped data. For example, "Chicago City" and "Greater Chicago Area" will be standardized to "Chicago, IL", and this mapping relationship is stored in the **mapping** database. 

### Database: Job
**Data Strucutre**
``` 
{
   "_id": "5571a44cbcc9b9b10d5fb1031b0012ad",
   "_rev": "1-8bb9faa3f15f1d5bb60e3a8dc1cb06ab",
   "description": "... Work with Finance Department ...",
   "title": "Nonprofit Quality & Compliance Manager",
   "jobid": 16178050,
   "companyid": "412913",
   "source": "Linkedin",
   "location": "Cincinnati, Ohio",
   "date": {
       "month": 6,
       "day": 8,
       "year": 2014
   },
   "type": "job",
   "company": "Cincinnati Union Bethel"
}
```
### Database: Company
**Data Strucutre**
```
{
   "_id": "ba25d89e51e2e89c3f4d455edb707ace",
   "_rev": "2-131dcd331da95277f228e069baf1ada4",
   "name": "Microsoft",
   "companyid": "1035",
   "url": "https://www.linkedin.com/company/1035",
   "ctype": "Public Company",
   "founded": "1975",
   "specialties": [
       "Business Software",
       "Design Tools",
       "Developer Tools",
       "Entertainment Products",
       "Hardware",
       "Home & Educational Software",
       "Tablets",
       "Search",
       "Advertising",
       "Servers",
       "Windows Operating System",
       "Windows Applications & Platforms",
       "Smartphones",
       "Cloud Computing"
   ],
   "industry": "Computer Software",
   "size": "10,001+ employees"
}
```

### Database: Mapping
**Location Mapping: Data Structure**

### Database: Log

**Data Strucutre**
```
{
    doctype: "scrape",
    scrape_id: "00000001",
    start_datetime: '2014-07-04 17:12:50.226416' # str(datetime.today())
    end_datetime: '2014-07-04 17:12:50.226416' # str(datetime.today())
    queue = {
                "data analyst": {
                                    "15213":[job1_id,job2_id],
                                    "15214":[job1_id,job2_id],
                                }
                "data science": {"00000":[job1_id,job2_id]}
            }
    finish = [job1_id, jobd2_id, jobd3_id ...]
}
```

## Module/Script

### Module: Linkedin Package

#### getCompanyInfo(companyid, verbose=False)

Linkedin assign an unique id for company who open public page on its website. For example, the following webpage is BlackStone Group's public page: https://www.linkedin.com/company/7834, and its company id is 7834. 

**getCompanyInfo** takes companyid as argument and return the company basic information, including type, founed, specialties, in dictionary. verbose can be set to True if the user want to print debuging information. 

#### getJobInfo(jobid, verbose=False)

Linkedin assign an unique id for every available job post. For example, the following webpage is one job post on Linkedin https://www.linkedin.com/jobs2/view/11004170 and the corresponding jobid is 11004170

**getJobInfo** takes jobid as argument and return the job basic information, including job title, company, posted date and job descrption, in dictionary. verbose have the same functions as the getCompanyInfo function. 

#### searchJobLink(keyword, pageNum=1, sort="R", zipcode=None, radius=None)

**searchLink** generate the query url for search a job based on the given keyword and other argument. Here is an example generated link: https://www.linkedin.com/vsearch/j?type=jobs&keywords=data+analyst&sortBy=R&countryCode=us&page_num=1

Every job search link starts with "https://www.linkedin.com/vsearch/j?type=jobs". When you type this link in the browser, it return all available job post in Linkedin. The symbol "&" is used to concatenated different criteria. 
- "keywords=" represents the keyword. 
- "sortBy=" represents "sort by" and can take three values - R for revelent, DA for earliest and DD for most recent posts. 
- "zipcode=" can be used to specify the searched regions
- "radius=" can be used to specify the radius of the searched region when zipcode is specified
- "pageNum=" can be used to the specify the page num of the search result. Note that Linkedin only make the first 40 pages avaialble to normal user. 

### Script: company.py

company.py scrapes the information of those companies that occur in the job database but not yet stored in the company database. 

 
### Script: location.py

Location information scraped from the source website are usually not in a standard format. For example, **"Chicago, IL"** or **"The Greater Chicago Area"** refer to the same area but are typed in different format. The purpose of the location.py is to standardize the location into (State, County, City) format by using Google geocoded service. 
