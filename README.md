# Notes on using the Stockflare API

## Converting a ticker symbol into a SIC

Before you can get historical data you will need the SIC code for the stock you are interested in.  You will need to call the search api with the ticker you are interested in.

Here is an example to get all fields available for `aapl`

```
curl -X PUT \
  https://api.stockflare.com/search/filter \
  -H 'cache-control: no-cache' \
  -H 'content-type: application/json' \
  -H 'postman-token: 1b362485-1d68-1efe-f65e-4f2bb7517e1b' \
  -d '{
  "conditions": {
  	"ticker": "aapl"
  },
  "select": "_all"
}'
```

The `select` parameter can be used to limit the fields returned, for instance:

```
curl -X PUT \
  https://api.stockflare.com/search/filter \
  -H 'cache-control: no-cache' \
  -H 'content-type: application/json' \
  -H 'postman-token: 6bccf5d1-d725-368f-2d9d-7b164ad53af0' \
  -d '{
  "conditions": {
  	"ticker": "aapl"
  },
  "select": ["sic", "ric", "rating", "market_value_usd"]
}'
```

## Getting the history for a stock

Once you have the SIC you can call the historical api like so:

```
curl -X PUT \
  https://api.stockflare.com/historical \
  -H 'cache-control: no-cache' \
  -H 'content-type: application/javascript' \
  -H 'postman-token: e380bba8-0300-82ef-a566-9778ccf811db' \
  -d '{
	"sic": "6c8227be-6855-11e4-98bf-294717b2347c",
	"after": 0,
	"select": "_all"
}'
```

An example response is
```
[
  {
      "sic": "6c8227be-6855-11e4-98bf-294717b2347c",
      "updated_at": 1497830400,
      "book_value": 25.76,
      "cash_flow_per_share": 10.46,
      "cheaper": true,
      "dividends": true,
      "dps": 2.28,
      "enterprise_value": 794414,
      "enterprise_value_to_operating_profit": 11.3952,
      "enterprise_value_to_sales": 3.6035,
      "invested_capital": null,
      "pre_tax_roic": null,
      "payout_ratio": 0.2667,
      "forecast_payout_ratio": 0.2629,
      "cash_conversion": 0.8174,
      "net_debt_to_operating_profit": 0.4507,
      "eps": 8.55,
      "eps_next_quarter": 1.57,
      "fifty_two_week_high": 156.65,
      "fifty_two_week_low": 91.5,
      "forecast_dividend_yield": 0.0161,
      "forecast_dps": 2.35,
      "forecast_eps": 8.94,
      "forecast_net_profit": 46978.41,
      "forecast_pe": 16.37,
      "forecast_sales": 226646.53,
      "free_cash_flow_yield": 0.0871,
      "gross_margin": 38.41,
      "growing": true,
      "high_growth": false,
      "latest_sales": 220457,
      "long_term_gain": 0.1235,
      "long_term_growth": 11.07,
      "market_value": 762993,
      "market_value_usd": 762993,
      "momentum": -2.805,
      "net_cash": false,
      "net_profit": 45730,
      "one_month_forecast_eps_change": 0,
      "one_month_price_change": -0.0497,
      "operating_profit": 69715,
      "pe_ratio": 17.12,
      "peer_average_forecast_pe": 17.1952,
      "peer_average_long_term_growth": 14.54,
      "per_year_gain": 0.1046,
      "price": 146.34,
      "price_change": 2.8608,
      "price_to_book_value": 5.68,
      "profitable": true,
      "rating": 4,
      "recommendation": 2.04,
      "recommendation_text": "buy",
      "return_on_equity": 34.57,
      "sales_next_quarter": 44877.75,
      "seven_year_gain": 1.0071,
      "shares_outstanding": 5213840000,
      "target_price": 156.77,
      "ten_day_average_volume": 38.68999,
      "three_month_forecast_eps_change": 0,
      "three_month_price_change": 0.0465,
      "upside": true,
      "abs_market_value_usd_change": 2182770.3744,
      "high_valuation_dividend_yield": false,
      "low_valuation_pe_ratio": false,
      "near_fifty_two_week_high": false,
      "near_fifty_two_week_low": false,
      "dividend_warning": false,
      "dps_payout_ratio": 0.2629,
      "sales_explosion": false,
      "percentage_up_downside": 0.0713,
      "mover_and_shaker": true,
      "indices": [
          "Dow Industry",
          "S&P 500",
          "NASDAQ 100 Index",
          "TR Equity United States Index"
      ],
      "employees": 116000,
      "historic_dividend_yield": 0.0156,
      "revenue_per_employee": 1.9005,
      "indices_change": false,
      "target_price_momentum": 5,
      "forecast_sales_momentum": -1,
      "forecast_eps_momentum": 3,
      "forecast_dps_momentum": 3,
      "positive_fundamentals": false,
      "negative_fundamentals": false
  }...
]
```

Again you can use the `select` parameter to limit the number of fields returned.

The `updated_at` field in the response in a Unix UTC time stamp representing the EOD date of the record.

This call is paginated, you can pass a `per_page` parameter to limit number of records returned in each call.  If there are additional record available the response headers contain an `x-cursor` header.  This header will not exist if you have already got all available records.

```
x-cursor => eyJzaWMiOiI2YzgyMjdiZS02ODU1LTExZTQtOThiZi0yOTQ3MTdiMjM0N2Mi%0ALCJ1cGRhdGVkX2F0IjoxNDk2NzA3MjAwfQ==%0A
```

In order to get the next page simple make another call with the `cursor` parameter as shown below.  Do not include an empty `cursor` parameter when making your call for the first page.

```
curl -X PUT \
  https://api.stockflare.com/historical \
  -H 'cache-control: no-cache' \
  -H 'content-type: application/javascript' \
  -H 'postman-token: c9c530a2-4268-2ee2-dcfd-0e4ee9c0ace7' \
  -d '{
	"sic": "6c8227be-6855-11e4-98bf-294717b2347c",
	"after": 0,
	"per_page": 10,
	"select": "_all",
	"cursor": "eyJzaWMiOiI2YzgyMjdiZS02ODU1LTExZTQtOThiZi0yOTQ3MTdiMjM0N2Mi%0ALCJ1cGRhdGVkX2F0IjoxNDk2NzA3MjAwfQ==%0A"
}'
```
