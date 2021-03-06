# Grademark

Toolkit for algo trading and backtesting in JavaScript and TypeScript.

This API builds on [Data-Forge](http://data-forge-js.com/) and is best used from [Data-Forge Notebook](http://www.data-forge-notebook.com/) (making it easy to plot charts and visualize).

Check out the [release notes](release-notes.md) to see updates and breaking changes.

[Follow the developer on Twitter](https://twitter.com/ashleydavis75)

<a href="#support-the-developer">Skip to the end to see how you can **support the developer.**</a>

Please see what this looks like in [the Grademark first example](https://github.com/grademark/grademark-first-example) and the unit tests in this repo.

Love this? Please star this repo!

## First example

From the [Grademark first example](https://github.com/grademark/grademark-first-example) here's some example output. [Click to see the first example as a notebook.](https://grademark.github.io/grademark-first-example/)

Analysis of a sequence of trades looks like this:

![Analysis of trades screenshot](https://raw.githubusercontent.com/grademark/grademark-first-example/master/output/analysis-screenshot.png)

Here's a chart that visualizes the equity curve for the example strategy:

![Equity curve](https://raw.githubusercontent.com/grademark/grademark-first-example/master/output/my-equity-curve-pct.png)

Here's another chart, this one is a visualization of the drawdown for the example strategy:

![Drawdown](https://raw.githubusercontent.com/grademark/grademark-first-example/master/output/my-drawdown-pct.png)

## Pre-requisites

- Make sure your data is sorted in forward chronological order. 

## Features

- Define a trading strategy with entry and exit rules.
- Backtest a trading strategy on a single financial instrument.
- Apply custom indicators to your input data series.
- Specify lookback period.
- Built-in intrabar stop loss.
- Compute and plot equity curve and drawdown charts.
- Throughly covered by automated tests.

## Latest features

Not fully documented yet, sorry. [Blog posts](http://www.the-data-wrangler.com/), examples and [videos](https://www.youtube.com/channel/UCOxw0jy384_wFRwspgq7qMQ) coming soon!

- Calculation of risk and rmultiples.
- Intrabar profit target.
- Intrabar trailing stop loss.
- Conditional buy on price level (intrabar).
- Monte carlo simulation.
- Multiple parameter optimization based on permutations of parameters (using grid search and hill-climb algorithms).
- Walk forward optimization and backtesting.
- Plot a chart of trailing stop loss.

If you need help with new features please reach out!

## Coming soon

- Plot a chart of risk over time.

## Maybe coming later

- Support for precise decimal numbers.
- Fees.
- Slippage.
- Position sizing.
- Testing multiple instruments / portfolio simulation / ranking instruments.
- Short selling.
- Market filters.

## Complete examples

For a ready to go example please see the repo [grademark-first-example](https://github.com/grademark/grademark-first-example).

## Usage

Instructions here are for JavaScript, but this library is written in TypeScript and so it can also be used from TypeScript.

### Installation

    npm install --save grademark

### Import modules

```javascript
const dataForge = require('data-forge');
require('data-forge-fs'); // For file loading capability.
require('data-forge-indicators'); // For the moving average indicator.
require('data-forge-plot'); // For rendering charts.
const { backtest, analyze, computeEquityCurve, computeDrawdown } = require('grademark');
```

### Load your data

Use [Data-Forge](http://data-forge-js.com/) to load and prep your data, make sure your data is sorted in forward chronological order.

This example loads a CSV file, but feel free to load your data from REST API, database or wherever you want!

```javascript
let inputSeries = dataForge.readFileSync("STW.csv")
    .parseCSV()
    .parseDates("date", "D/MM/YYYY")
    .parseFloats(["open", "high", "low", "close", "volume"])
    .setIndex("date") // Index so we can later merge on date.
    .renameSeries({ date: "time" });
```
The example data file is available in [the example repo](https://github.com/grademark/grademark-first-example).

### Add indicators

Add whatever indicators and signals you want to your data.

```javascript
const movingAverage = inputSeries
    .deflate(bar => bar.close)          // Extract closing price series.
    .sma(30);                           // 30 day moving average.

inputSeries = inputSeries
    .withSeries("sma", movingAverage)   // Integrate moving average into data, indexed on date.
    .skip(30)                           // Skip blank sma entries.
```

### Create a strategy

This is a very simple and very naive mean reversion strategy:

```javascript
const strategy = {
    entryRule: (enterPosition, args) => {
        if (args.bar.close < args.bar.sma) { // Buy when price is below average.
            enterPosition();
        }
    },

    exitRule: (exitPosition, args) => {
        if (args.bar.close > args.bar.sma) {
            exitPosition(); // Sell when price is above average.
        }
    },

    stopLoss: args => { // Optional intrabar stop loss.
        return args.entryPrice * (5/100); // Stop out on 5% loss from entry price.
    },
};
```

### Running a backtest

Backtest your strategy, then compute and print metrics:

```javascript
const trades = backtest(strategy, inputSeries)
console.log("Made " + trades.count() + " trades!");

const startingCapital = 10000;
const analysis = analyze(startingCapital, trades);
console.log(analysis);
```

### Visualizing the results

Use [Data-Forge Plot](https://github.com/data-forge/data-forge-plot) to visualize the equity curve and drawdown chart from your trading strategy:

```javascript
computeEquityCurve(trades)
    .plot()
    .renderImage("output/my-equity-curve.png");

computeDrawdown(trades)
    .plot()
    .renderImage("output/my-drawdown.png");
```

## Advanced backtesting

We are only just getting started in this example to learn more please follow my [blog](http://www.the-data-wrangler.com/) and [YouTube channel](https://www.youtube.com/channel/UCOxw0jy384_wFRwspgq7qMQ).

## Resources

- [Data-Forge](http://data-forge-js.com/)
- [Data-Forge Notebook](http://www.data-forge-notebook.com/)

## Support the developer 

You can help support my work in any of the following ways:

- [Follow the developer on Twitter](https://twitter.com/ashleydavis75)
- Star this repository (and my other repos [here](https://github.com/data-forge), [here](https://github.com/grademark) and [here](https://github.com/data-forge-notebook)).
- Share links to my work on Twitter and other social media.
- [Sponsor me on GitHub](https://github.com/sponsors/ashleydavis).
- Purchase [Data-Forge Notebook](https://www.data-forge-notebook.com/).
- Purchase either of my books [Data Wrangling with JavaScript](http://bit.ly/2t2cJu2) or [Bootsrapping Microservices](http://bit.ly/2o0aDsP).

<a target="_blank" href="https://www.data-forge-notebook.com/"><img src="images/support1.png"></a>

<a target="_blank" href="https://www.data-forge-notebook.com/"><img src="images/support2.png"></a>

