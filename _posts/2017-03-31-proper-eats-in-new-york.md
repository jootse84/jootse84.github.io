Selecting a place to eat in New York on the fly is safe with the city's restaurant inspection letter rating. Each restaurant is inspected at least once per year by the [Health Department](https://www1.nyc.gov/site/doh/services/restaurant-grades.page). The inspectors check the food quality and temperature, in addition of space and personal hygiene. 

After the inspection, restaurants get assigned a grade represented by a letter from A to C being the first one the best rating - *A: super clean*; while the last one the worst rate - *C: let's say dirty*. Restaurants are required to post a placard advertising the letter grade, which assures consumers can decide if they want to take the risk and eat in a restaurant with a low rate. Then, of course, tricky owners manage solutions to cover bad inspections:

![alt a nice restaurant]({{ site.url }}/assets/images/proper_eats_post.jpg)

# Tuning data

[NYC OpenData](https://opendata.cityofnewyork.us/) contains a long list of interesting datasets from the city of New York and accessible to everyone. The [DOHMH New York City Restaurant Inspection Results](https://data.cityofnewyork.us/Health/DOHMH-New-York-City-Restaurant-Inspection-Results/xx67-kt59) is a good source where curious people like me can play around analyzing restaurant grades. In this case I decided to create a simple map visualization of the city including restaurants labeled by their grades.

We are going to use Python and pandas to play with the data. Let's dive into the code!

```python
import pandas as pd
rests = pd.read_csv("NYC_Restaurants.csv")

mRests = rests[rests['BORO']=="MANHATTAN"]
mRests = rests[rests['GRADE']!="Not Yet Graded"]
mRests = mRests[pd.notnull(mRests["GRADE"])]
mRests = mRests[pd.notnull(mRests["SCORE"])]
mRests["GRADE"] = mRests["GRADE"].astype("category", categories = ["A","B","C","P","Z"], ordered = True)

```

The previous code slices our data and discards any restaurant not located in Manhattan and already graded by the Health Department. Also, we need to tell pandas that the feature *GRADE* from the dataset is categorical, which means that exists a logical order in the values - *"A" being the best grade while "Z" being the worst*.

In order to plot the points on the maps, we will need to convert the addresses to a geolocation. The addresses provided by the dataset are very inconsistent with their labeling so we have to go through each one and normalize them. The next code basically converts numbers to words, and original words to numbers.

We use the Python package [inflect](https://pypi.python.org/pypi/inflect) to convert numbers of addresses to words and [re](https://docs.python.org/2/library/re.html) for matching regular expressions:

```python
import inflect
import re

p = inflect.engine()
word_to_number_mapping = {}

for i in range(1, 200):
    word_form = p.number_to_words(i)  # 1 -> 'one'
    ordinal_word = p.ordinal(word_form)  # 'one' -> 'first'
    ordinal_number = p.ordinal(i)  # 1 -> '1st'
    word_to_number_mapping[ordinal_word] = ordinal_number  # 'first': '1st'

for i in range(len(mRests)):

    street= mRests['STREET'][i].split()

    for j in range(len(street)):
        if street[j].lower() in word_to_number_mapping:
            street[j]=  word_to_number_mapping[street[j].lower()]

    for j in range(len(street)):
        if re.findall(r'([0-9]+(st|rd|th|nd)+)', street[j].lower())==[]:
            if street[j].isdigit():
                val=(street[j])
                street[j]=street[j].replace(str(val), str(p.ordinal(val)))    
        streetFull = ' '.join(street)
        mRests.set_value(i,'STREET',streetFull)

mRests["Address"] = mRests['BUILDING'].map(str)+ " " + mRests['STREET'].map(str)+ ", " + mRests['ZIPCODE'].map(str)
```

Awesome! If we type the command *mRests["Address"].head()* we should get in return the following:

```
0               374 5TH AVE, 11215
1          3713 13th AVENUE, 11218
2    16121 CROCHERON AVENUE, 11358
3          153 ESSEX STREET, 10002
4       230 EAST 9th STREET, 10003
Name: Address, dtype: object
```

# Folium

In order to build the map, we need to get the geocode from the previous addresses to match them with the latitude/longitude points. Let's suppose we already coded these addresses using a Python package like [geopy](https://pypi.python.org/pypi/geopy) and saved the results in our dataset with the features "long" and "lat".

Now we can use [Folium](http://python-visualization.github.io/folium/) to create the final map from our code. If you have no knowledge of JavaScript nor leaflet, with Folium you can plot in an awesome interactive map your data just by writing a few lines of Python.

For this example we are going to plot the restaurants with markers (green for *A-restaurants*, blue for *B-restaurants* and red for the rest). In addition the interactive map will popup a window with the name, score and grade of the restaurant when the user place the cursor on the marker.

```python
import folium

mCluster = folium.Map(location=[40.7831, -73.9712], zoom_start =12)
marker_cluster = folium.MarkerCluster().add_to(mCluster)
for i in range(len(samp)):
    if mRests['GRADE'][i] =="A":
        folium.Marker([mRests['lat'][i],mRests['long'][i]], popup= "Name: " + str(mRests['DBA'][i])+ '\n' + "Score: " + str(mRests["SCORE"][i]) + '\n'+'Grade: '+ str(mRests["GRADE"][i]),
                      icon=folium.Icon(color="green", icon='no-sign')).add_to(marker_cluster)
    elif mRests['GRADE'][i]=="B":
         folium.Marker([mRests['lat'][i],mRests['long'][i]], popup= "Name: " + str(mRests['DBA'][i])+ '\n' + "Score: " + str(mRests["SCORE"][i]) + '\n'+'Grade: '+ str(mRests["GRADE"][i]),
                      icon=folium.Icon(color='blue',icon='no-sign')).add_to(marker_cluster)
    else:
         folium.Marker([mRests['lat'][i],mRests['long'][i]], popup= "Name: " + str(mRests['DBA'][i])+ '\n' + "Score: " + str(mRests["SCORE"][i]) + '\n'+'Grade: '+ str(mRests["GRADE"][i]),
                      icon=folium.Icon(color='red',icon='no-sign')).add_to(marker_cluster)

mCluster.save('restaurants.html')
```

Voilà, we have a map! Click [here]({{ site.url }}/assets/restaurants_post.html) to check out the result.

