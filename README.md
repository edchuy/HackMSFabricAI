# HackMSFabricAI

My Hack Together: The Microsoft Fabric Global AI Hack Submission: Square Feet Forecasting

Introduction

I have always been intrigued by Time Series Forecasting and was wondering whether I could use Microsoft Fabric to develop predictive models, but also display the forecasted values in the same graph as the actual values for those dates. Power BI does have an analytics forecasting in its line visual, but it can only create forward-looking forecast and visualize the predicted value and confidence limits based on the data being displayed, so that actual values for the predicted dates on the same visual. This article provided with the information of what model Power BI uses for the forecasting: https://powerbi.microsoft.com/es-mx/blog/describing-the-forecasting-models-in-power-view/

Another visual that I discovered early on in my process of learning to use Power BI was a Time Series Decomposition published by Microsoft in Appsource. I found it is no longer available there, but I was able to get access to it in both my Power BI desktop and Service from Github: https://github.com/microsoft/powerbi-visuals-timeseriesdecomposition

Having it at hand I proceed to apply to a square feet of ceramic tiles sales real-world time series to see if it had any seasonality and would be a good candidate for a time series model that included seasonality. As you can see, it not only showed me that it had a 90 day seasonality but that a multiplicative model was recommended.

![image](https://github.com/edchuy/HackMSFabricAI/assets/9073204/7343fd5d-7b1d-4ab7-999d-9c76971948dd)
[Uploading Time_Series_Predictive_Analytics .ipynb…]()

Project Design Flow

![image](https://github.com/edchuy/HackMSFabricAI/assets/9073204/7f36c2cc-1da0-494f-9130-34d7ad67cfe9)

Steps Taken and Results

- Initially, using Power Automate online I created an automation that refreshed with certain frequency the raw sales dataset from its Dropbox origin to OneDrive. That file was then ingested and transformed using DataflowGen 2, as well of item data from a SQL Server and a calendar table developed using M Power Query based on Avi Singh's ultimate PowerBI calendar (https://web.learnpowerbi.com/download/).
- The resulting tables were then published to the project's lakhouse as parquet tables. Using a notebook, I was able to use SparkSQL to query the sales and item tables to produce aggregates by date of square feet as well as other sales data (profit, revenue, % profit) in the salesdatagold dataframe that was then written back to the lakehouse as a parquet table.
- I found a Github project (https://github.com/SooyeonWon/time_series_analytics/blob/main/Time_Series_Predictive_Analytics%20.ipynb) that includes data decomposition and ETS model Python code in a notebook. I tried to use the Python code as a base to write the code in the notebook but ran into a runtime error in the step I was to use the training dataset in the ETS model ("Error com.fasterxml.jackson.databind.exc.MismatchedInputException: No content to map due to end-of-input") that prevented me from using the notebook further. I guess it probably has to do with Pyspark not being exactly Python.
- I decided to do the time series decomposition and ETS modelling as a Jupyter notebook and ... it worked as expected after some tinkering.
- The last step of the Jupyter involved exporting the forecasted square feet for 22 days in a .csv that I loaded to the lakehouse.
- I created the following semantic model that uses the calendar table as the bridge between the salesdatagold and sqftforecast tables (with a caveat that somehow 7 days of the original data somehow disappeared from the original datase after it was processed before it could be used for forecasting):
  
![image](https://github.com/edchuy/HackMSFabricAI/assets/9073204/8bf7e46f-2232-4663-9955-ab20220eb31e)

- I created the following PowerBI visualization that accomplished my objective (I haven't gotten the raw data for any date in March yet, but I will update the image as soon as it is updated):

![image](https://github.com/edchuy/HackMSFabricAI/assets/9073204/3152ff1d-5bc4-4e0f-abc3-e7a2444f500a)

One thing I found is that I can only download a PBI version of the file that has a live connection to the datasources which won't be helpful to publish here.

Conclusion

My project didn't end up being a 100% Microsoft Fabric as I would have wanted originally. But if there is one thing I learned is that you have a sure, backup way to get things done. I have already accepted being a speaker on AI on Pi Day (https://aidausergroup.org/2024/02/02/ai-on-pi-day-2024/), so I will definitely update this. In addition to resolving the Pyspark issue, I had the other sales data that I didn't manage to include this time.
