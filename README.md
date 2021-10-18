# Data Parser

## Table of Contents

- [Intro](#intro)
- [What is data parsing?](#what-is-data-parsing)
- [The Structure of a Data Parser](#the-structure-of-a-data-parser)
- [Common Use Cases of a Data Parser](#common-use-cases-of-a-data-parser) 
- [Data Scraping and Parsing Example](#data-scraping-and-parsing-example)
  - [Installation](#installation) 
  - [Packages and Arguments](#packages-and-arguments) 
  - [Access Google Books and Google News Through URL Links](#access-google-books-and-google-news-through-url-links)
  - [Parse Google Books](#parse-google-books)
  - [Parse Google News](#parse-google-news)
  - [Saving the Data From Google](#saving-the-data-from-google)
  - [Saving the Data From Google](#saving-the-data-from-google)
  - [Results](#results)
  - [Complete Code Implementation](#complete-code-implementation)

## Intro

As big data becomes more and more crucial to companies across all industries, the need to extract, analyze, and process data will only increase. How else can you make sense of the ubiquitous data without parsing it? A solid understanding of data and its interpretation is critical to the success of any enterprise.

Data parsing is a term you’ll have come across a lot in the IT industry. In many cases, you’ll find yourself needing to process a particular data from an API or webpage and changing it into a more understandable format like JSON or CSV.

Data parsing is the process of extracting raw data from one source and transforming it into a more readable format. Web scraping relies heavily on data parsers since you won’t always have time to process HTML forms from raw data. In that regard, data parsing occurs after you extract data from a web page, making it easier to read and analyze so your team can use the results efficiently.

In this article, you’ll learn about data parsing, its benefits, and how you can extract data from the web and parse it into a readable format.

## What Is Data Parsing?
 
Within IT, data parsing involves analyzing and interpreting source data (usually a string) as some kind of a structured dataset. In simpler terms, data parsing is all about transforming one type of data into another, for example, an unstructured data into CSV, JSON, and other formats.

Parsing data from the Web is very useful in converting a raw HTML into a JSON or a CSV in order for the data to be more easily analyzed down the process chain. In addition, the parsed data could also be used for creating a table to display the most relevant information or generating reports.

Regardless of your position within a company, learning to transform and make sense of data is a prerequisite to long-term business strategy.

## The Structure of a Data Parser

Now that you have a cursory understanding of what a data parser is, let’s take a look at the structure of a data parser and how to carry it out. 

During parsing, not everything in the input string is useful; only the relevant data is retained after the parsing. With proper rules in the parser implementation, the parser distinguishes between the relevant and irrelevant information with an HTML string. The required data can then be converted into any format supported by the parser.

## Common Use Cases of a Data Parser

Data parsing has seen several use cases and applications across many industries.  

- Streamline business processes: By using data parsing, businesses can optimize their workflows to include data from the data extraction. It has seen widespread usage in marketing, customer data management, investment analysis, and more.

- Analysis for accounting and finance: By parsing data, banks and finance companies can extract information from the enormous amounts of data provided to them by the customers. Its uses in this sector include income verification, identity verification, investment portfolios, and credit reports.
 
- Logistics and shipping sector: Companies can collect billing and shipping information from online shoppers using data parsers.  

- Real estate industry: Businesses can extract leads from emails sent in by property owners and builders. You can also extract data for processing on CRM applications.


## Data Scraping and Parsing Example
 
With a web scraper, you can extract raw data (HTML) from web pages. That data can then be parsed and outputted to a more suitable format.

To do that, you’ll need web scraper software like Puppeteer. 

This section will focus on parsing information from a Google web scraper into a JSON format. We’ll scrape Google for books and news and parse relevant data into JSON.

### Installation
 
Before installation, you should set up a new project folder in your local machine. In your CLI, access the project folder and initialize npm with:

```npm init``` 

Now you’re ready to install the Puppeteer package. You can find the documentation for it [here.](https://www.npmjs.com/package/puppeteer)

```npm i puppeteer```

Once the installation is complete, create a main access file and name it `index.js` within the folder.  

### Packages and Arguments

The Google scraper and data parser implementation will be inside the `index.js` file. First, you’ll need to import two packages: the Puppeteer for scraping and the fs package for writing the data into a file.

```
const fs = require('fs');
const puppeteer = require('puppeteer');
``` 

You need to configure the application to receive arguments directly from the CLI. In this tutorial, we’ll use two arguments for our application: 
 
- Keyword for search
- Google search medium (either books or news)

```
const keyword = process.argv[2];
const medium = process.argv[3] || 'book';

const mediums = ['book', 'news'];

if (!keyword) {
   throw "Please provide a keyword to search for as the first argument";
}
if (!mediums.includes(medium)) {
   throw "Specify whether you're searching for books or news in the second argument"
}

if (medium === 'news') {
   getNews();
} else if (medium === 'book') {
   getBooks();
}
``` 

In the code block, the scraper will throw an error if there’s no keyword or medium argument supplied during execution. 

This implementation will contain two functions: ```getBooks()``` for book searches, ```getNews()``` for news searches. Despite their similarity, there still are a few differences.

To start scraping, you need to launch an instance of Puppeteer. You can do that with the following line do code:

```
const browser = await puppeteer.launch();
const page = await browser.newPage();
``` 

Access Google Books and Google News Through URL Links 

Once the browser is launched, you can provide it the URL link for the search medium. To search through Google Books pages, use the following code:
 
```
page.goto(`https://www.google.com/search?tbm=bks&q=${keyword}`);
``` 

The URL for accessing Google News is slightly different from that of Google Books:

```
page.goto(`https://www.google.com/search?tbm=nws&q=${keyword}`);
``` 
### Parse Google Books

Using the ```evaluate()``` method, you can use the query selector to pick out the headers and links on the first-page results on Google. The following is for the ```getBooks()``` function:

```
let urls = await page.evaluate(() => {
   let results = [];
   let items = document.querySelectorAll('div.Yr5TG');
   items.forEach((item) => {
       // Extract author details.
       const bookDetails = item.children[1];
       // Get Author About page.
       const link = bookDetails.querySelector('a').getAttribute('href');
       // Get book title.
       const title = bookDetails.querySelector('a>h3').innerText;
       // Get book author.
       const author = bookDetails.querySelector('div.N96wpd span').innerText;
       results.push({
           link,
           title,
           author
       });
   });
   return results;
})
``` 

This block of code will scrape Google for information—author name, book title, URL link— on the supplied keyword using the querySelector() method.

## Parse Google News 

In the getNews() method, the following block extracts the news post's link, channel, and title. 

```
let urls = await page.evaluate(() => {
   let results = [];
   let items = document.querySelector('div.v7W49e').children;
   [...items].forEach((item) => {
       // Get news link.
       const link = item.querySelector('a').getAttribute('href');
       // Get news channel.
       const channel = item.querySelector('div.CEMjEf span').innerText;
       // Get news heading.
       const title = item.querySelector('a div[role="heading"]').innerText;
       results.push({
           link,
           channel,
           title
       });
   });
   return results;
})
```

## Saving the Data From Google

With the implementation complete, we can run the script to query Google for information on a particular book keyword. For this example, we’ll be using the keyword “To kill a Mockingbird”. Run this application with:
 
```
node index.js "To kill a Mockingbird" book
```

On the CLI, you'll see the results displayed. 

Being able to see the information in the terminal is fine, but it is not really useful when that data has to be processed by other scripts as well. 

As a format to transfer the data in we will use JSON.
Inside the getBooks() and getNews() method, you can extract and convert the data to JSON with the following. 

```
saveToFile(JSON.stringify(urls, null, 4), keyword);
```

The saveToFile() function saves the parsed data using the keyword as the name of the file.
 
```
function saveToFile(dataString, keyword) {
   const fileSlug = keyword.replace(/\s+/gm, '-').toLowerCase();
   fs.writeFile(`${fileSlug}.json`, dataString, function (err) {
       if (err) {
           console.log(err);
       }
   });
}
``` 

### Results

Below are the results when you parse books' data from Google.

```
[
   {
       "link": "https://www.theverge.com/2021/10/15/22711372/nasa-lucy-mission-jupiter-trojan-asteroids-12-years-launch",
       "channel": "The Verge",
       "title": "NASA is sending a spacecraft on a 12-year journey to explore swarms of asteroids around Jupiter"
   },
   {
       "link": "https://www.theguardian.com/science/2021/oct/15/nasa-launch-lucy-mission-distant-asteroids",
       "channel": "The Guardian",
       "title": "Nasa prepares to launch Lucy mission to distant asteroids"
   },
   {
       "link": "https://www.yahoo.com/news/nasa-advisor-quits-agency-keeps-100633698.html",
       "channel": "Yahoo",
       "title": "NASA advisor quits after the agency keeps a $10 billion telescope named after James Webb, who was a senior State Department official during the persecution of gay and lesbian government employees"
   },
]
``` 

### Complete Code Implementation


``` 
const fs = require('fs');
const puppeteer = require('puppeteer');

const keyword = process.argv[2];
const medium = process.argv[3] || 'book';

const mediums = ['book', 'news'];

if (!keyword) {
   throw "Please provide a keyword to search for as the first argument";
}
if (!mediums.includes(medium)) {
   throw "Specify you're searching for books or news in the second argument"
}


async function getBooks() {
   const browser = await puppeteer.launch();
   const page = await browser.newPage();
   try {
       await page.goto(`https://www.google.com/search?tbm=bks&q=${keyword}`);
       let urls = await page.evaluate(() => {
           let results = [];
           let items = document.querySelectorAll('div.Yr5TG');
           items.forEach((item) => {
               // Extract author details.
               const bookDetails = item.children[1];
               // Get Author About page.
               const link = bookDetails.querySelector('a').getAttribute('href');
               // Get book title.
               const title = bookDetails.querySelector('a>h3').innerText;
               // Get book author.
               const author = bookDetails.querySelector('div.N96wpd span').innerText;
               results.push({
                   link,
                   title,
                   author
               });
           });
           return results;
       })

       console.log({urls});
       saveToFile(JSON.stringify(urls, null, 4), keyword);
       await browser.close();
   } catch (error) {
       console.error(error)
   }
}

async function getNews() {
   const browser = await puppeteer.launch();
   const page = await browser.newPage();
   try {
       await page.goto(`https://www.google.com/search?tbm=nws&q=${keyword}`);
       let urls = await page.evaluate(() => {
           let results = [];
           let items = document.querySelector('div.v7W49e').children;
           [...items].forEach((item) => {
               // Get news link.
               const link = item.querySelector('a').getAttribute('href');
               // Get news channel.
               const channel = item.querySelector('div.CEMjEf span').innerText;
               // Get news heading.
               const title = item.querySelector('a div[role="heading"]').innerText;
               results.push({
                   link,
                   channel,
                   title
               });
           });
           return results;
       })

       console.log({urls});

       saveToFile(JSON.stringify(urls, null, 4), keyword);
       await browser.close();
   } catch (error) {
       console.error(error)
   }
}

function saveToFile(dataString, keyword) {
   const fileSlug = keyword.replace(/\s+/gm, '-').toLowerCase();
   fs.writeFile(`${fileSlug}.json`, dataString, function (err) {
       if (err) {
           console.log(err);
       }
   });
}

if (medium === 'news') {
   getNews();
} else if (medium === 'book') {
   getBooks();
}
``` 










 



