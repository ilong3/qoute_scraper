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

## Scripts

```

import puppeteer from "puppeteer";

const sleep = (ms) => new Promise((resolve) => setTimeout(resolve, ms));

(async () => {
  const browser = await puppeteer.launch({
    headless: false,
    defaultViewport: null,
    userDataDir: "./tmp",
  });

  const page = await browser.newPage();
  await page.goto("https://auth.geeksforgeeks.org");

  await page.$('input[type="text"]');
  await page.waitForSelector('input[type="text"]');
  await page.type('input[type="text"]', "mammanilong@gmail.com");

  await page.$('input[type="password"]');
  await page.waitForSelector('input[type="password"]');
  await page.type('input[type="password"]', "M@thematics1");

  await page.$('button[type="submit"]');
  await page.waitForSelector('button[type="submit"]');
  await page.click('button[type="submit"]');

  await page.waitForNavigation();
  await page.waitForSelector(`#lower_section #lower_section_text`);
  await page.click(`#lower_section #lower_section_text`);

  await page.waitForNavigation();
  await page.screenshot({ path: "screenshot.png", fullPage: true });

  await sleep(50000);
  await browser.close()
})();
```
