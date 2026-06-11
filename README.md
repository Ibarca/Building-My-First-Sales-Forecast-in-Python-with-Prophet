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



<table align="center"> <tr> <td  style="border:1px solid #d0d7de; padding:15px; border-radius:6px;"> ⚠️ **An accurate demand forecast is the foundation of any planning process.** By forecasting demand first, we can directly estimate future sales volumes and revenue, which then serve as the basis for a wide range of operational forecasts. These include inventory projections, order quantity calculations, workforce planning, and capacity requirements. 
While demand is the primary driver, these downstream calculations also require additional variables such as current inventory levels, incoming purchase orders, supplier lead times, minimum order quantities (MOQs), and productivity assumptions. However, the purpose of this article is to focus on demand forecasting itself, as it is the critical first step upon which all subsequent planning activities depend. </td> </tr> </table>


## The Dataset

For this project, I worked with a historical monthly sales dataset containing several years of sales observations. The data was structured as a time series, with each record representing the total sales achieved during a specific month. This monthly granularity made the dataset well-suited for forecasting long-term demand trends and seasonality patterns while reducing the noise often found in daily sales data.

<p align="center">
  <img src="https://github.com/Ibarca/Building-My-First-Sales-Forecast-in-Python-with-Prophet/blob/1914bae290e26a24780e51809c98260e4f2d3387/Images/Image%2011.06.26%20at%2011.55.jpeg" width="30%" />
  &nbsp;&nbsp;
  <img src="https://github.com/Ibarca/Building-My-First-Sales-Forecast-in-Python-with-Prophet/blob/5f0f7635bbc5fbb0b56e71e25492eaa8e09aaba5/Images/Image%2011.06.26%20at%2011.44.jpeg" width="45%" />
</p>



Before using Prophet, the data needs to follow a specific structure:

| Column | Description |
|----------|-------------|
| ds | Date column |
| y | Value to forecast |

Example:

| ds | y |
|------------|--------|
| 2021-01-01 | 1200 |
| 2021-02-01 | 1350 |
| 2021-03-01 | 1280 |

Prophet requires very little preprocessing compared to many machine learning models, which makes it beginner-friendly.

## Preparing the Data

Using Pandas, I converted the date column into a datetime format and renamed the columns to Prophet's required naming convention.

```python
df['ds'] = pd.to_datetime(df['Month'])
df = df.rename(columns={'Sales': 'y'})
```

At this point, the dataset was ready for forecasting.

## Training the Model

Creating a Prophet model is surprisingly simple.

```python
from prophet import Prophet

model = Prophet()
model.fit(df)
```

The model automatically identifies:

- Trend
- Seasonality
- Holiday effects (if provided)
- Changes in growth patterns

Unlike traditional regression models, Prophet handles much of this complexity behind the scenes.

## Creating Future Predictions

After training the model, I generated future dates and asked Prophet to make predictions.

```python
future = model.make_future_dataframe(periods=12, freq='M')

forecast = model.predict(future)
```

This produced predictions for the next 12 months.

The forecast dataset includes:

- Predicted values (`yhat`)
- Lower confidence interval (`yhat_lower`)
- Upper confidence interval (`yhat_upper`)

This is valuable because forecasts should never be treated as exact numbers—they always contain uncertainty.

## Visualizing the Forecast

One of the things I enjoyed most about Prophet is how quickly it generates professional-looking visualizations.

```python
fig = model.plot(forecast)
```

The chart shows:

- Historical sales
- Forecasted sales
- Confidence intervals

This makes it easy to communicate results to non-technical stakeholders.

For interactive visualizations, Plotly can also be used:

```python
from prophet.plot import plot_plotly

plot_plotly(model, forecast)
```

Interactive charts allow users to zoom, inspect values, and explore forecast behavior in more detail.

## Understanding Trend and Seasonality

A particularly useful feature of Prophet is the ability to decompose forecasts into their components.

```python
model.plot_components(forecast)
```

This visualization helps answer questions such as:

- Is demand generally increasing or decreasing?
- Are there recurring seasonal patterns?
- Which periods generate the highest demand?

For someone with a demand-planning background, this is often where the real business value starts to emerge.

## What I Learned

Building this project taught me several important lessons.

### 1. Forecasting Is More Than Generating Numbers

A model can produce predictions easily, but understanding whether those predictions make business sense is much harder.

Domain knowledge remains extremely important.

### 2. Data Quality Matters

Even the best forecasting model cannot compensate for poor data quality.

Missing values, incorrect dates, and inconsistent reporting can significantly impact results.

### 3. Validation Is Essential

A forecast should never be trusted simply because a model generated it.

The next step in my learning journey is to evaluate forecast accuracy using validation techniques and metrics such as:

- MAE (Mean Absolute Error)
- RMSE (Root Mean Squared Error)
- MAPE (Mean Absolute Percentage Error)

### 4. Python Makes Forecasting Accessible

Before learning Python, forecasting models felt intimidating.

Prophet demonstrated that modern forecasting can be approachable even for analysts who are just beginning their data science journey.

## Final Thoughts

This project was an important milestone in my transition from Category Management and Demand Planning into Data Analytics.

What I find particularly exciting is that forecasting sits at the intersection of business knowledge and data science. My previous experience working with inventory planning, stock availability, supplier lead times, and commercial performance gives me context for interpreting forecasts beyond the technical output.

This is only the beginning of my forecasting journey, but it has already shown me how powerful Python can be for turning historical data into actionable business insights.

Have you used Prophet or another forecasting tool in your projects? I'd love to hear about your experience.

---

## Repository Structure

```text
sales-forecasting-prophet/
│
├── data/
├── notebooks/
│   └── introduction_to_prophet.ipynb
├── images/
│   ├── forecast.png
│   └── components.png
├── README.md
└── requirements.txt
```

## Skills Demonstrated

- Python
- Pandas
- Time Series Analysis
- Forecasting
- Prophet
- Data Visualization
- Business Analytics
- Demand Planning

## Next Steps

In future articles, I plan to explore:

1. Forecast validation and backtesting
2. Forecast accuracy metrics (MAE, RMSE, MAPE)
3. Inventory planning applications
4. Demand forecasting with external variables
5. Interactive forecasting dashboards with Plotly
