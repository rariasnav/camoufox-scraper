Camoufox Scraper
Camoufox Scraper is a ready-made solution for crawling websites using Playwright with Camoufox browser. It provides page to your defined function, which you can use to extract any data from the page.

Usage
To get started with Camoufox Scraper, you only need two things. First, tell the scraper which web pages it should load. Second, tell it how to extract data from each page.

The scraper starts by loading the pages specified in the Start URLs field. You can make the scraper follow page links on the fly by setting a Link selector, and Link patterns to tell the scraper which links it should add to the crawling queue. This is useful for the recursive crawling of entire websites, e.g. to find all products in an online store.

To tell the scraper how to extract data from web pages, you need to provide a Page function. This is Python code that is executed for every web page loaded.

In summary, Camoufox Scraper works as follows:

Adds each Start URL to the crawling queue.
Fetches the first URL from the queue and constructs a DOM from the fetched HTML string.
Executes the Page function on the loaded page and saves its results.
Optionally, finds all links from the page using the Link selector. If a link matches any of the Link selector and has not yet been visited, add it to the queue.
If there are more items in the queue, repeats step 2, otherwise finish.
Input configuration
As input, the Beautifulsoup Scraper Actor accepts a number of configurations. These can be entered either manually in the user interface in Apify Console, or programmatically in a JSON object using the Apify API. For a complete list of input fields and their types, please visit the Input tab.

Page function
The Page function (pageFunction) field contains a Python script with a single function that enables the user to extract data from the web page, access its DOM, add new URLs to the request queue, and otherwise control Beautifulsoup Scraper's operation.

Example:

from typing import Any
from crawlee.crawlers import PlaywrightCrawlingContext

async def page_function(context: PlaywrightCrawlingContext) -> Any:
    url = context.request["url"]
    title = await context.page.locator("title").first.inner_text()
    return {"url": url, "title": title}
Context
The code runs in Python 3.12 and the page_function accepts a single argument context of type PlaywrightCrawlingContext. See documentation link for further details

Proxy configuration
The Proxy configuration (proxyConfiguration) option enables you to set proxies that will be used by the scraper in order to prevent its detection by target web pages. You can use both the Apify Proxy and custom HTTP or SOCKS5 proxy servers.

Proxy is required to run the scraper. The following table lists the available options for the proxy configuration setting:

Apify Proxy (automatic)	The scraper will load all web pages using the Apify Proxy in automatic mode. In this mode, the proxy uses all proxy groups that are available to the user. For each new web page, it automatically selects the proxy that hasn't been used in the longest time for the specific hostname in order to reduce the chance of detection by the web page. You can view the list of available proxy groups on the Proxy page in Apify Console.
Apify Proxy (selected groups)	The scraper will load all web pages using the Apify Proxy with specific groups of target proxy servers.
Custom proxies	
The scraper will use a custom list of proxy servers. The proxies must be specified in the scheme://user:password@host:port format. Multiple proxies should be separated by a space or new line. The URL scheme can be either http or socks5. The user and password might be omitted, but the port must always be present.

Example:

http://bob:password@proxy1.example.com:8000http://bob:password@proxy2.example.com:8000
The proxy configuration can be set programmatically when calling the Actor using the API by setting the proxyConfiguration field. It accepts a JSON object with the following structure:

{
    // Indicates whether to use the Apify Proxy or not.
    "useApifyProxy": Boolean,

    // Array of Apify Proxy groups, only used if "useApifyProxy" is true.
    // If missing or null, the Apify Proxy will use automatic mode.
    "apifyProxyGroups": String[],

    // Array of custom proxy URLs, in "scheme://user:password@host:port" format.
    // If missing or null, custom proxies are not used.
    "proxyUrls": String[],
}
Results
The scraping results returned by Page function are stored in the default dataset associated with the Actor run, from where you can export them to formats such as JSON, XML, CSV, or Excel.

To download the results, call the Get dataset items API endpoint:

https://api.apify.com/v2/datasets/[DATASET_ID]/items?format=json
where [DATASET_ID] is the ID of the Actor's run dataset, in which you can find the Run object returned when starting the Actor. Alternatively, you'll find the download links for the results in Apify Console.

To skip the #error and #debug metadata fields from the results and not include empty result records, simply add the clean=true query parameter to the API URL, or select the Clean items option when downloading the dataset in Apify Console.

To get the results in other formats, set the format query parameter to xml, xlsx, csv, html, etc. For more information, see Datasets in documentation or the Get dataset items endpoint in Apify API reference.
