#CloudHealth Reporting API: Getting Started
CloudHealth provides API services to allow you to programmatically retrieve data from our platform. This guide will provide you a brief overview to get you started using the API, with a focus on retrieving reporting data.

##Disclaimer
The current API detailed in this document is version 0.0.1. This early API is subject to change. Future changes to this API may impact code written to previous versions of the API. We will do our best to notify users of the API of pending changes, in order to ease the migration to the new version.

##Terminology
We use specific terminology to in our API that is important to understand. These include:

- Report name - the name of the report (e.g. Instance Usage).
- Dimensions - the available members for the X-axis and categorization (e.g. by Days categorized by Reservation Type).
- Intervals - the time granularity of the report (e.g. monthly).
- Measures - the selected measures (e.g. # Instances).
- Filters - user defined filters for the report.
- Data - the data for the reports.

The below shows an example of the different elements of a report on the application:

![Report Elements](https://github.com/CloudHealth/cht_api_guide/blob/master/images/report_components.png)

##Getting an API Key
In order to make authenticated requests to the CloudHealth API service, it is necessary to get an API key. An API key is a globally unique identifier (GUID) automatically generated by CloudHealth on behalf of a user. This GUID uniquely identifies requests to a specific user. 

To request an API key, click on My Profile in your user settings.

![Report Elements](https://github.com/CloudHealth/cht_api_guide/blob/master/images/my_profile.png)

In your profile settings is a section named API Key. Click on the Generate API Key button to create your API key. The generated GUID will be saved with your user upon clicking the Update button. This GUID will need to be supplied with all future API requests.

![Report Elements](https://github.com/CloudHealth/cht_api_guide/blob/master/images/api_key.png)

To periodically rotate your API key for security reasons, just return to your profile settings and click Generate API Key to create a new key. Upon clicking update the previous key will cease working, and the new one will be in effect.

##Making Your First API Request
The CloudHealth reporting API is a REST-based interface that returns JSON. It allows you to retrieve both default reports provided with the platform (“standard reports”), as well as reports you save (“custom reports”). In order to explore the API, we are going to use the standard UNIX utility curl.

###Retrieve List of Available Standard Reports
To retrieve a list of standard reports available through the API, run the below from a command:

```
$ curl "https://apps.cloudhealthtech.com/olap_reports?api_key=<your api key>"
```

If you have Python installed on your system, you can pretty print the results with this command:

```
$ curl "https://apps.cloudhealthtech.com/olap_reports?api_key=<your api key>" | python -m json.tool
```

Notice the list of available reports have a hierarchical structure. For example, under the topic “cost” are reports such as “history”, “current” and “instance”. These refer to the standard reports in the CloudHealth platform named Cost History, Current Cost and Instance Cost respectively. Each report also provides a URL to retrieve the report.

###Retrieve List of Standard Reports for a Topic
To retrieve the available reports for a specific topic, you can specify the topic in the URL. For example, the below will return a list of all standard reports available for the cost topic: 

```
$ curl "https://apps.cloudhealthtech.com/olap_reports/cost?api_key=<your api key>"
```

###Retrieve Data For a Standard Report
To retrieve data for a report, you specify its URL, which is provided with the previous requests. For example, the below will retrieve the data for the Cost History report:

```
$ curl "https://apps.cloudhealthtech.com/olap_reports/cost/history?api_key=<your api key>"
```

For details on how to interpret the data returned, see the below section on Response For Report Request.

###Retrieve a List of Custom Reports
To retrieve a list of custom reports, you can make the below request:

```
$ curl "https://apps.cloudhealthtech.com/olap_reports/custom?api_key=<your api key>"
```

A custom report is any report that you or your users have customized and saved.

###Retrieve Data For a Custom Report
You can also retrieve data for a user saved report. To do this, you need to have the unique identifier for the report. To get this identifier, go to Customer Reports, and click preview for the report you wish to retrieve.

You can identify the unique identifier from the URL:

![Report Elements](https://github.com/CloudHealth/cht_api_guide/blob/master/images/report_id.png)

By adding the report id in the below request, you can retrieve the data for this report:

```
$ curl "https://apps.cloudhealthtech.com/olap_reports/custom/<report id>?api_key=<your api key>"
```

###Response Format For Report Requests
When you retrieve data for a report, the resulting json has the following major elements, each corresponding to the report terminology provided in Terminology section:

- “report” - the name of the report (string).
- “dimensions” - the available dimensions (x-axis and category) for the report (array).
- “filters” - the selected filters for the report (array).
- “interval” - the selected interval for the report (string).
- “measures” - the selected measures for the report (array).
- “data” - the data for the report (array).

The data is reported in an array, with each row in the array providing data for a specific member in the dimension. For example if the dimensions returned:

```
“dimensions”: [ 
{“Months”: [“total”, “January”, “February”]}, 
{“Service Items”: [“total”, “ec2_compute”, “ec2_transfer]}
]
```

The supporting data might look like this:

```
“data”: [ 
[[100, 40, 60], [60, 20, 40], [40, 20, 20]],
[[30, 20, 10], [20, 10, 10], [10, 0, nil]], 
[[70, 40, 30], [40, 20, 20], [30, 20, 10]], 
]
```

The first row in the data represents the data for the “Month” called “total”; the second row is data for the “Month” called “January”, and so on. Within each row is the specific data that corresponds to the dimension called “Service Items”. For example, the values [60, 20, 40] represent the “total”, “ec_compute”, and “ec2_transfer” for the month of “January”.

Note: A zero denotes that there was data for the specified dimension member in the underlying data analyzed, but its aggregate total was zero. A null however denotes that there was no data for this dimension member in the underlying data analyzed.

###Ruby Examples
Below are some examples written in the Ruby programming language to get you started:

- [Export Cost History data in CSV format](https://github.com/CloudHealth/cht_api_guide/blob/master/examples/ruby/export_csv.rb)
- [Finding the Month Over Month Cost Increase](https://github.com/CloudHealth/cht_api_guide/blob/master/examples/ruby/report_monthly_increase.rb)

###Support
If you have questions or issues using the reporting API, please contact us at support@cloudhealthtech.com.




