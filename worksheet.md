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
2. Launch Mathematica from the desktop icon or by clicking on the Main Menu, then Education, then Mathematica. It will take a little while to load; you can see its progress from messages displayed over the image. Mathematica is a huge and complex system, but it has excellent documentation which we can use to see how to access weather information. We’ll use a function called `WeatherData`. More information can be found [in the official documentation](http://reference.wolfram.com/documentation-search.html?query=weatherdata).
    
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

This shows the temperatures as Mathematica `Quantity` values; these have both a *size* and a *unit* part. We can now use Sonic Pi to ‘play’ the temperatures we’ve collected by extracting the temperature value from each `Quantity`.

## Hear the sound (finally!)

1. Quit Mathematica.
2. Open the file `workspace.txt` in the Leafpad editor.
3. Select all the text with `Ctrl + A`, and copy it with `Ctrl + C`.
4. Open Sonic Pi from the Programming menu, select an unused workspace from the tabs at the bottom of the window then paste in the text you just copied with `Ctrl + V`.

Have a look at the code:
    
```ruby
# Listen to the weather

# define some utility functions
# quantity_to_temp(quantity)
# return the temperature value from a Quantity string 
def quantity_to_temp(quantity)
  return quantity.sub('Quantity[', '').split('.')[0]
end

# scale(t)
# multiply t by a scaling factor to get a playable note
def scale(t)
  scaling_factor = 3
  return t.to_i * scaling_factor
end

# now play the weather!
with_synth "fm"
File.open('/home/pi/temperature.txt') do |file|
  file.each_line {
    |line|
    puts ("Temp = #{line}")    
    play scale(quantity_to_temp(line))
    sleep 0.5
  }
end
```

At the top there are definitions of two utility functions; these turn the output from Mathematica into notes that Sonic Pi can play. 

Then `quantity_to_temp(quantity)` takes one of the lines from the `temperature.txt` file and does some string manipulation to return a string with just the temperature number in it. It does this in two stages; removing all the text up to the temperature, and then discarding everything after it. Look at Ruby’s [documentation](http://www.ruby-doc.org/core-2.1.3/String.html) to see the String functions used.

Next `scale(t)` is important, so that we can actually hear the note Sonic Pi plays; good values for `play` are between 30 and 120. You need to multiply the temperature by a scaling factor, whose value depends on the place where your temperatures came from. The range in this particular day in Kathmandu was 15-26 °C. So, a good value in this case for the variable `scaling_factor` is _3_, giving us notes from 45 to 78. You may have to adjust `scaling_factor` for your choice of location.

The last part of the workspace does the actual playing. Because Sonic Pi has many facilities available from its underlying Ruby language, we can use them to open our temperature file, iterate over each line, show the `Quantity` text in the Sonic Pi’s log window, and then play the scaled note.

If you’d like to read an entertaining online guide to Ruby, try [Why's (Poignant) Guide to Ruby](http://mislav.uniqpath.com/poignant-guide/). Or for a more conventional alternative, try [Programming Ruby: The Pragmatic Programmer's Guide](http://ruby-doc.com/docs/ProgrammingRuby/).

Finally, press Sonic Pi’s *Run* button and listen to the results of your efforts. At the risk of annoying your neighbours/pets, you might want to experiment with different values of `scaling_factor` and `sleep`. 

## Next steps

This is just a brief introduction to sonification with Sonic Pi and Mathematica. There are all sorts of data sources suitable for treatment in this way. With Sonic Pi’s Ruby programmability, you could continuously query external sources and play sounds to interpret them. 

