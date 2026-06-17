# Building My First Sales Forecast in Python with Prophet

When I worked in Category Management and Demand Planning, forecasting was always a critical part of the job. Most of our forecasts were built using Excel-based methods, moving averages, market knowledge, and business assumptions.

As I transition into Data Analytics, I wanted to understand how modern forecasting tools work in Python. One of the first libraries I explored being a Le Wagon student was Prophet, an open-source forecasting library originally developed by Meta.

In this article, I'll walk through how I built my first sales forecast in Python using Prophet and what I learned along the way.

## Why Forecasting Matters

Accurate forecasts help companies reduce inventory costs, improve product availability, and make better business decisions answering questions such as:

- How much inventory should we order?
- How many employees will we need next month?
- What revenue can we expect next quarter?
- When might we experience stock shortages?



<table align="center">
<tr>
<td>

⚠️ **It is important to distinguish demand forecasting from other planning processes. Demand forecasting focuses solely on predicting future customer demand based on historical sales patterns and trends. Activities such as inventory planning, order quantity calculations, workforce planning, and capacity management come later. While these processes rely on the demand forecast as their starting point, they also require additional inputs such as current inventory levels, incoming purchase orders, supplier lead times, minimum order quantities (MOQs), and productivity assumptions.**

</td>
</tr>
</table>


## The Dataset

For this project, I worked with a historical monthly sales dataset containing several years of sales observations. The data was structured as a time series, with each record representing the total sales achieved during a specific month. This monthly granularity made the dataset well-suited for forecasting long-term demand trends and seasonality patterns while reducing the noise often found in daily sales data.

<p align="center">
  <img src="https://github.com/Ibarca/Building-My-First-Sales-Forecast-in-Python-with-Prophet/blob/1914bae290e26a24780e51809c98260e4f2d3387/Images/Image%2011.06.26%20at%2011.55.jpeg" width="30%" />
  &nbsp;&nbsp;
  <img src="https://github.com/Ibarca/Building-My-First-Sales-Forecast-in-Python-with-Prophet/blob/5f0f7635bbc5fbb0b56e71e25492eaa8e09aaba5/Images/Image%2011.06.26%20at%2011.44.jpeg" width="45%" />
</p>

Prophet requires very little preprocessing compared to many machine learning models, which makes it beginner-friendly. The data needs to follow a specific structure:

| Column | Description |
|----------|-------------|
| ds | Date column |
| y | Value to forecast |

The ds column tells Prophet when each observation occurred, while the y column tells it what happened at that point in time. Using these two fields, Prophet automatically identifies trends, seasonality patterns, and recurring cycles in the historical data. For example, if we are forecasting monthly sales, the ds column contains the month and the y column contains the sales volume for that month.

Example:

| ds | y |
|------------|--------|
| 2021-01-01 | 1200 |
| 2021-02-01 | 1350 |
| 2021-03-01 | 1280 |



## Preparing the Data

Using Pandas, I converted the date column into a datetime format and renamed the columns to Prophet's required naming convention.

In the original dataset, the dates were stored in a column called Month and sales values in a column called Sales. First, I converted the date column into a proper datetime format so that Prophet could recognize and process the time series correctly. Then, I renamed the columns to match Prophet's required naming convention.

```python
df['ds'] = pd.to_datetime(df['Month'])
df = df.rename(columns={'Sales': 'y'})
```

At this point, the dataset was ready for forecasting.

## Training the Model

Creating a Prophet model is surprisingly simple.
When the fit() method is executed, Prophet analyzes all historical observations and learns the underlying patterns present in the data. Rather than requiring the user to manually specify trend equations or seasonal factors, Prophet automatically decomposes the time series into several components.

```python
from prophet import Prophet

model = Prophet()
model.fit(df)
```

The model automatically identifies:

- **Trend** – The long-term direction of the data, showing whether demand or sales are generally increasing, decreasing, or remaining stable over time.
- **Seasonality** – Recurring patterns that repeat at regular intervals, such as weekly, monthly, or yearly fluctuations in demand.
- **Holiday Effects** *(optional)* – The impact of holidays, promotions, or special events on demand. When provided, Prophet can account for these events and adjust its forecasts accordingly.
- **Trend Changes (Changepoints)** – Points in time where the underlying growth rate changes. Prophet automatically detects these shifts, allowing the model to adapt to changes in market conditions, customer behavior, or business performance.

Unlike many traditional forecasting methods, where trend and seasonality often need to be modeled manually, Prophet handles much of this complexity automatically. This allows analysts to build robust forecasts quickly while still benefiting from sophisticated time-series modeling techniques under the hood.

At this point, the model has learned from the historical sales data and is ready to generate forecasts for future periods.

## Creating Future Predictions

After training the model, I generated future dates and asked Prophet to make predictions.

```python
future = model.make_future_dataframe(periods=12, freq='M')

forecast = model.predict(future)
```

The `make_future_dataframe()` function extends the existing timeline by creating additional future periods. In this example, I generated **12 future monthly observations**, allowing the model to forecast sales for the next year.

The `predict()` function then uses everything the model learned from the historical data—trend, seasonality, and growth patterns—to estimate future sales values.

As a result, Prophet returns a new DataFrame called `forecast` containing predictions for both historical and future dates.


## Key Forecast Outputs


The forecast dataset includes several useful columns:

![Forecast table](https://github.com/Ibarca/Building-My-First-Sales-Forecast-in-Python-with-Prophet/blob/5f8ab1cfdc5466df0100356b324e55b1a9ceac3f/Images/Image%2017.06.26%20at%2016.15.jpeg?raw=true)

| Column | Description |
|----------|----------|
| `yhat` | Predicted sales value |
| `yhat_lower` | Lower bound of the forecast interval |
| `yhat_upper` | Upper bound of the forecast interval |

The prediction interval is particularly important because forecasts should never be interpreted as exact numbers. Every forecast contains uncertainty, and the further we predict into the future, the greater that uncertainty tends to become. By default, Prophet uses an **80% uncertainty interval**, meaning there is roughly an **80% probability** that the future observation will fall within that range, according to the model.



Rather than saying *"sales next month will be exactly 2,500 units,"* the model provides a likely range of outcomes. This helps planners and decision-makers assess risk and prepare for different demand scenarios.

For example:

> Predicted sales: **2,500 units**  
> Expected range: **2,200 – 2,800 units**

![Prophet forecast uncertainty distribution](https://github.com/Ibarca/Building-My-First-Sales-Forecast-in-Python-with-Prophet/blob/1f55ff82474406b7ec9b22da38f399292a007d5d/Images/Prophet%20distribution%2017.%20Juni%202026%2C%2016_37_59.png?raw=true)

This range provides far more valuable business insight than a single number because it acknowledges the uncertainty inherent in forecasting.

## Visualizing the Forecast

One of the things I enjoyed most about Prophet is how quickly it generates professional-looking visualizations. With a single line of code, the model creates a chart that combines historical observations, future predictions, and the forecast uncertainty range.

```python
fig = model.plot(forecast)
```

![Forecast image](https://github.com/Ibarca/Building-My-First-Sales-Forecast-in-Python-with-Prophet/blob/9168947f728be380828b52ec79797511c11dc9ad/Images/Image.png?raw=true)

The resulting visualization provides a clear overview of the entire time series and helps validate whether the forecast appears reasonable. It also makes it easier to identify trends, seasonal patterns, and periods where uncertainty increases.

The chart includes:

- Historical sales data
- Forecasted sales values
- Confidence intervals around the forecast

These visualizations are particularly valuable when presenting results to business stakeholders, as they transform complex statistical outputs into an intuitive and easy-to-understand format.

For users who require a more dynamic experience, Prophet can also generate interactive visualizations using Plotly.

```python
from prophet.plot import plot_plotly

plot_plotly(model, forecast)
```

Interactive charts allow users to zoom into specific periods, hover over individual data points, inspect forecast values, and explore the model's behavior in greater detail. This can be especially useful when analyzing long time series or discussing forecasts with decision-makers who want to investigate particular periods more closely.

## Understanding Trend and Seasonality

One of Prophet's most valuable features is its ability to break a forecast down into the individual components that drive it. Rather than only providing future predictions, Prophet also helps explain *why* those predictions are being made.

This can be achieved with the following command:

```python
model.plot_components(forecast)
```

![Forecast uncertainty intervals](https://github.com/Ibarca/Building-My-First-Sales-Forecast-in-Python-with-Prophet/blob/5872640dae492e488ef03369e59995c3835e739d/Images/Image%2017.06.26%20at%2016.26.jpeg?raw=true)

The resulting visualization decomposes the forecast into separate charts, allowing us to inspect the underlying patterns learned from the historical data.

Depending on the data and model configuration, these components may include:

- **Trend** – the long-term growth or decline in demand over time.
- **Yearly seasonality** – recurring patterns that repeat each year.
- **Weekly seasonality (not included in this project)** – demand fluctuations that occur on specific days of the week.
- **Holiday effects (not included in this project)** – the impact of special events or holidays when provided to the model.

By separating these effects, Prophet transforms the forecasting process from a "black box" into an interpretable business tool.

For professionals working in demand planning, inventory management, or supply chain operations, this is often where the real business value begins to emerge. Understanding *what* the forecast predicts is important, but understanding *why* demand behaves in a certain way enables better decisions around inventory levels, purchasing, staffing, promotions, and capacity planning.

Instead of simply forecasting future sales, Prophet helps uncover the underlying drivers of demand, turning historical data into actionable business insights.


## Conclusion

Building my first sales forecasting model with Prophet showed me how accessible modern forecasting has become. With only a few lines of code, it is possible to generate forecasts, visualize future demand, and, most importantly, analyze the underlying trend and seasonality patterns that drive business performance.

What I particularly liked about Prophet is that it reduces much of the complexity typically associated with forecasting while still providing transparent and interpretable results. Features such as forecast decomposition make it easier to understand why demand behaves in a certain way, reducing the "black box" effect often associated with predictive models.

In future articles, I will explore how to evaluate forecast accuracy using metrics such as MAE and MAPE, as well as how to incorporate additional variables such as weather conditions, holidays, and marketing campaigns. These enhancements can help explain demand fluctuations more accurately while increasing the accountability and business value of forecasting models.

---



## Next Steps

In future articles, I plan to explore:

- How to validate forecasts using backtesting techniques
- Measuring forecast performance with MAE, RMSE, and MAPE
- Translating demand forecasts into inventory planning decisions
- Incorporating external variables such as weather, holidays, and marketing campaigns
- Building interactive forecasting dashboards with Plotly
