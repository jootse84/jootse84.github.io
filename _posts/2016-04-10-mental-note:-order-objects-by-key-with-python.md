Today I had to deal with some unstructured data coming from a request of an external server API.

The keys of the response JSON object from that API were numbers, timestamps representing months. The containing values of those keys were some data that our system had to use to build and display a beautiful chart.

Obviously, the chart had to be in order ascendent of time (month by month). So the first issue I had to figure out, it was **how to get the value of an object in order ascending of its key values**.

And this is why I love Python:

```python
for key in sorted(result):
	print result[key]
```

Well, that was not exactly what I needed. Because the chart had to display only the last 6 months, but still Python can solved it very simple:

```python
for key in sorted(result)[-6:]:
	print result[key]
```

Actually, the reality is that we had to save the value in an Array of Tuples, to have well structured the data for the chart.
The first value of the Tuple was a String representing the month (i.e. 'March', 'April'), while the second value was the proper data.

```python
from datetime import datetime
import calendar

array = []
for key in sorted(result)[-6:]:
	array.append((calendar.month_name[datetime.fromtimestamp(float(key)).month], result[key]))
```

Please Jose, use list comprehensions:

```python
from datetime import datetime
import calendar

array = [(calendar.month_name[datetime.fromtimestamp(float(key)).month], result[key]) for key in sorted(result)[-6:]]
```


