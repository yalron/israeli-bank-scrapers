Israeli Bank Scrapers - Get closer to your own data
========
[![NPM](https://nodei.co/npm/israeli-bank-scrapers.png)](https://nodei.co/npm/israeli-bank-scrapers/)

[![npm version](https://badge.fury.io/js/israeli-bank-scrapers.svg)](https://badge.fury.io/js/israeli-bank-scrapers)
[![dependencies Status](https://david-dm.org/eshaham/israeli-bank-scrapers/status.svg)](https://david-dm.org/eshaham/israeli-bank-scrapers)
[![devDependencies Status](https://david-dm.org/eshaham/israeli-bank-scrapers/dev-status.svg)](https://david-dm.org/eshaham/israeli-bank-scrapers?type=dev)

# What's here?
What you can find here is scrapers for all major Israeli banks and credit card companies. That's the plan at least.
Currently only the following banks are supported:
- Discount Bank
- Leumi Card
- Isracard
- Amex (thanks @erezd)

# Prerequisites
To use this you will need to have [Node.js](https://nodejs.org) >= 6 installed.

# Getting started
To use these scrapers you'll need to install the package from npm:
```sh
npm install israeli-bank-scrapers --save
```
Then you can simply import and use it in your node module:
```node
import { createScraper } from 'israeli-bank-scrapers';

const credentials = {...}; // different for each bank
const options = {...};
const scraper = createScraper(options);
const scrapeResult = await scraper.scrape(credentials);

if (scrapeResult.success) {
  scrapeResult.accounts.forEach((account) => {
    console.log(`found ${account.txns.length} transactions for account number ${account.accountNumber}`);
  });
}
else {
  console.error(`scraping failed for the following reason: ${scrapeResult.errorType}`);
}
```
The definition of the `options` object is as follows:
```node
{
  companyId: string, // mandatory; one of 'discount', 'leumiCard', 'isracard', 'amex'
  startDate: Date, // the date to fetch transactions from (can't be before the minimum allowed time difference for the scraper)
  combineInstallments: boolean, // if set to true, all installment transactions will be combine into the first one
  verbose: boolean // include more debug info about in the output
}
```
The structure of the result object is as follows:
```node
{
  success: boolean,
  accounts: [{
    accountNumber: string,
    txns: [{
      type: string, // can be either 'normal' or 'installments'
      identifier: int, // only if exists
      date: Date,
      processedDate: Date,
      amount: double,
      description: string,
      installments: {
        number: int, // the current installment number
        total: int, // the total number of installments
      }
    }],  
  }],
  errorType: "invalidPassword"|"changePassword"|"timeout"|"generic", // only on success=false
  errorMessage: string, // only on success=false
}
```
You can also use the `SCRAPERS` list to get scraper metadata:
```node
import { SCRAPERS } from 'israeli-bank-scrapers';
```
The return value is a list of scraper metadata:
```node
{
  <companyId>: {
    name: string, // the name of the scraper
    loginFields: [ // a list of login field required by this scraper
      '<some field>' // the name of the field
    ]
  }
}
```

# Specific definitions per scraper
## Discount scraper
This scraper expects the following credentials object:
```node
const credentials = {
  id: <user identification number>,
  password: <user password>,
  num: <user identificaiton code>
};
```
This scraper supports fetching transaction from up to one year (minus 1 day).

## Leumi-Card scraper
This scraper expects the following credentials object:
```node
const credentials = {
  username: <user name>,
  password: <user password>
};
```
This scraper supports fetching transaction from up to one year.

## Isracard scraper
This scraper expects the following credentials object:
```node
const credentials = {
  id: <user identification number>,
  card6Digits: <6 last digits of card>
  password: <user password>
};
```
This scraper supports fetching transaction from up to one year.

## Amex scraper
This scraper expects the following credentials object:
```node
const credentials = {
  id: <user identification number>,
  card6Digits: <6 last digits of card>
  password: <user password>
};
```
This scraper supports fetching transaction from up to one year.

# License
The MIT License
