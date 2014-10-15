# Listening to the weather with Sonic Pi

## Find some weather data

We’re going to download some weather data and turn it into sound. There are many online sources of weather data, but there is one built into Raspbian: Mathematica. We can get an idea of the type of data available from Mathematica by querying the Wolfram Alpha website (this is the company which makes Mathematica).

1. Launch a web browser and navigate to [http://www.wolframalpha.com/](http://www.wolframalpha.com/). This allows you to type in queries and get data answers; it's like a search engine for live data. 
1. Try a query to find temperatures in a given location. You can substitute your favourite town/city here:

    `temperature Kathmandu`
    
    You will see a result such as this:
    
    ![](images/wolframalpha.png)

If we could get the temperature datapoints plotted on the graph into Sonic Pi, we could play notes corresponding to them. Mathematica has access to many datasets, including these weather ones.
    
## Look at weather data from Mathematica

1. Quit the web browser.
1. Launch Mathematica from the desktop icon or by clicking on the Main Menu, then Education, then Mathematica. It will take a little while to load; you can see its progress from messages displayed over the image. Mathematica is a huge and complex system, but it has excellent documentation which we can use to see how to access weather information. We’ll use a function called `WeatherData`. More information can be found [in the official documentation](http://reference.wolfram.com/documentation-search.html?query=weatherdata).
    
The format of the weather function is:

```
WeatherData["<name of place>", "<property to get>", {<year>,<month>,<day>}]
```

The parts in angle brackets should be replaced with the desired values. On starting, Mathematica will open a ‘notebook’ window. A useful way to check the data we will use is to plot it as a graph. Click in the notebook and type this command:
 
```
 	DateListPlot[WeatherData["Kathmandu", "Temperature", {2014, 10, 1}],  Joined -> False]
```
 	
Now hold Shift and press Enter with the cursor on the same line as your input; this tells Mathematica to process it. It will display your input as `In[1]:=…`. This will take a **long** time the first time you run a query, while Mathematica downloads data sources; it could be 10 minutes or so. Now is the time to go and make a cup of tea…

Finally, you should see a result like this:

![](images/temp-graph.png)

Each point on the graph corresponds to a temperature in °C during a day. 

## Save the weather data in a file

The easiest way to get the data into Sonic Pi is to write it into a text file, which Sonic Pi can then read for playing. To do this, execute the following command:

```
Export["temperature.txt", WeatherData["Kathmandu", "Temperature", {2014, 10, 1}]["Values"]]
```

This will create a file called `temperature.txt`. Take a look at it by opening Leafpad from the Accessories menu and viewing the `.txt` file. You’ll see the extracted data formatted like this:

![](images/temperatures.png)

This shows the temperatures as Mathematica `Quantity` values; these have both a *size* and a *unit* part. We can now use Sonic Pi to ‘play’ the temperatures we’ve collected by extracting the temperature value from each `Quantity`. Instructions on how to do this are in [the second worksheet](worksheet2.md).
