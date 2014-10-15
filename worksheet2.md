## Hear the sound of the weather (finally!)

1. Quit Mathematica.
1. Open Sonic Pi from the Programming menu, select an unused workspace from the tabs at the bottom of the window then copy and paste in the text below with `Ctrl + C` and `Ctrl + V`.

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
# and limit to a lower minimum
def scale(t)
+ scaling_factor = 3
+ lower_limit = 30
+ scaled_note = t.to_i * scaling_factor
+ return (scaled_note < lower_limit) ? lower_limit : scaled_note
end

# now play the weather!
use_synth :tri
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
