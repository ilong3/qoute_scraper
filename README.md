# Quote Scraper

This project is a web scraper built with Puppeteer that extracts quotes from [Quotes to Scrape](http://quotes.toscrape.com). It navigates through the first five pages of the website, extracting quotes, authors, and tags, and saves them into a CSV file. The scraper respects the `robots.txt` rules of the website.

## Features

- Scrapes quotes, authors, and tags from the first five pages of Quotes to Scrape.
- Checks `robots.txt` to ensure scraping is allowed.
- Saves the scraped data into a CSV file.

## Requirements

- Node.js
- npm (Node Package Manager)

## Installation

1. Clone the repository:

```sh
git clone https://github.com/yourusername/quote-scraper.git
cd quote-scraper


```

```

import puppeteer from "puppeteer"; // Import the puppeteer module for browser automation
import robotsParser from "robots-parser"; // Import the robots-parser module to handle robots.txt parsing
import fetch from "node-fetch"; // Import the node-fetch module to make HTTP requests
import { writeFileSync } from "fs";
import { parse } from "json2csv";

// Function to check if scraping the given URL is allowed by robots.txt
async function canScrape(url) {
  const robotsTxtUrl = new URL("/robots.txt", url).href; // Construct the URL to the robots.txt file
  const robotsTxt = await (await fetch(robotsTxtUrl)).text(); // Fetch the robots.txt file content
  const robots = robotsParser(robotsTxtUrl, robotsTxt); // Parse the robots.txt content
  return robots.isAllowed(url, "*"); // Check if scraping is allowed for the given URL
}

(async () => {
  const start_url = "https://quotes.toscrape.com"; // The URL to start scraping from
  const isAllowed = await canScrape(start_url); // Check if scraping the start URL is allowed

  if (!isAllowed) {
    console.log(`Scraping not allowed on ${start_url} according to robots.txt`);
    return; // Exit if scraping is not allowed
  }

  const browser = await puppeteer.launch({ headless: false }); // Launch the browser in non-headless mode
  const page = await browser.newPage(); // Open a new page/tab in the browser
  await page.goto(start_url); // Navigate to the quotes website

  const results = []; // Initialize an array to store the scraped data
  let pageCounter = 1; // Initialize a counter to keep track of the current page number

  while (pageCounter <= 5) {
    // Loop to scrape data until page 5
    const allText = await page.$$(".quote"); // Select all elements with the class 'quote' on the page

    for (const text of allText) {
      // Iterate over each selected quote element
      const conta = await page.evaluate((text) => {
        // Extract and return the text content, author, and tags from each quote element
        const title = text.querySelector(".text").textContent.trim(); // Get the quote text
        const author = text.querySelector(".author").textContent.trim(); // Get the quote author
        const tags = Array.from(text.querySelectorAll(".tag")).map((el) =>
          el.textContent.trim()
        ); // Get the quote tags and convert them to an array of trimmed strings

        return {
          title, // Return the title (quote text)
          author, // Return the author
          tags, // Return the tags
        };
      }, text);
      results.push(conta); // Add the extracted data to the result array
    }

    // Check if there is a next page link
    const nextPageLink = await page.$(".next > a"); // Select the next page link element
    if (nextPageLink && pageCounter < 5) {
      // If the next page link exists and the page counter is less than 5
      await Promise.all([
        page.waitForNavigation({ waitUntil: "load" }), // Wait for the navigation to complete
        nextPageLink.click({ delay: 4000 }), // Click the next page link & Delay to mimic human browsing
      ]);
      pageCounter++; // Increment the page counter
    } else {
      break; // No more pages or reached page 5, exit the loop
    }
  }

  // console.log(res); // Log the scraped data to the console
  const csv = parse(res);
  writeFileSync("quotes.csv", csv);
  await browser.close(); // Close the browser
})();

```
