# The Benevolent

[Live Demo](https://the-benevolent.herokuapp.com/#/)

The Benevolent is a single-page Robinhood clone, where users can obtain real-time price data and additional information of stocks listed on US stock exchanges and perform buy/sell transactions on those stocks.

## Technologies

* Backend: Ruby on Rails, PostgreSQL, ActiveRecord
* Frontend: React, Redux
* IEX API
* News API
* Recharts
* Webpack, Babel, CSS3, HTML5

## Features

**Splash Page & User Authentication**

*BCrypt*

* Secure user authentication using BCrpyt

![Splash Page](/public/splash-page.gif)

**Portfolio Page**

*Recharts, Odometer*

* Uses Recharts and Odometer library to create dynamic and interactive price graphs, which displays prices according to parsed data on hover over graphs.
* Display stocks owned or watched by the user, along with its current market price and share ownership.
* Display recent news on home page.

![Portfolio Page](/public/portfolio-page.gif)


**Stock Show Page**

*IEX Stock API, News API*

* Parse and display current and historial stock prices and company information on specified stocks utilizing IEX Stock API. 
* Obtain and display recent news related to specified stock utilizing News API.

![Stock Page](/public/stock-page.gif)


The stock price graph displays current and historial prices based on the time period user chooses. This feature is completed with the least amount of API calls possible to improve efficiency of website in terms of memory and speed. 

* `5Y` prices are only fetched when user clicks on `5Y` label.
* `1Y` prices are fetched and stored to the local state when user clicks `1W`, `1M`, `3M`, or `1Y` labels. This data is then parsed to accordingly to display correct prices for the time period selected.
* `1D` prices are fetched in the `ComponentDidMount` lifecycle method, as it will always be the first graph on display when user is directed to the stock show page.

```javascript
handleTimeChange(e) {
        let time = e.currentTarget.innerText;
        if (time === '5Y') {
            this.props.fetch5YPrices(this.props.match.params.symbol).then(
                res => this.setState({ parsedData: res.prices, time: time })
            )
        } else if (time === '1Y') {
            this.setState({ parsedData: this.state.oneYear, time: time });
        } else if (time === '3M') {
            this.setState({ parsedData: this.state.oneYear.slice(185), time: time });
        } else if (time === '1M') {
            this.setState({ parsedData: this.state.oneYear.slice(227), time: time });
        } else if (time === '1W') {
            this.setState({ parsedData: this.state.oneYear.slice(243), time: time });
        } else {
            this.setState({ parsedData: this.props.prices.intraday, time: time });
        }
}
```

**Search Bar & Transaction**

* Allow users to search stocks by their ticker symbol and company name.
* Allow user to buy/sell shares of stock at the current market price and reflects share change on portfolio page after the transaction.

![Transaction](/public/transaction.gif)


Once an buy/sell transaction is placed (user clicks on submit button), the following function checks to see if it is a buy or sell transaction and adjusts share ownership accordingly. A new transaction is created with the updated information and saved to the database through the `createTransaction(transaction)` function passed in through the `props`. The function then resets the `local state` and opens up a `modal`, confirming that the order was successful.

```javascript
handleSubmit(e) {
        e.preventDefault();
        const { symbol, currentUser } = this.props;
        let shares = this.state.shares;
        if (!this.state.buy) {
           shares = shares * -1;
        } 
        let transaction = {
            user_id: currentUser,
            symbol: symbol, 
            shares: shares,
            cost: this.calculateCost()
        }
        this.props.createTransaction(transaction);
        this.setState({ submit: true, shares: 0 });
        let modal = document.getElementById("modal");
        modal.style.display = "block";
}
```
