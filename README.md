

```python
# Import dependencies
import pandas as pd
import matplotlib.pyplot as plt
import numpy as np
from pprint import pprint
import openweathermapy.core as owm
from citipy import citipy
import requests

# Import API key
from config import api_key
```

## Generate list of N random cities


```python
# This n determines how many cities we generate
# We also initialize our lists to track the cities
n = 50
cities = []
country_codes = []

for x in range(n):
    lat = np.random.uniform(-90, 90)
    lng = np.random.uniform(-180, 180)
    city = citipy.nearest_city(lat, lng)
    
    # Make sure that the randomly generated city is not already in our series
    # If it is, regenerate the lat and lng until we get one that isn't
    while city.city_name in cities:
        lat = np.random.uniform(-90, 90)
        lng = np.random.uniform(-180, 180)
        city = citipy.nearest_city(lat, lng)
    
    # Add the data to our lists
    cities.append(city.city_name)
    country_codes.append(city.country_code)
    
country_codes = [x.upper() for x in country_codes]
```

## Perform Weather Check Using API Calls


```python
# Create base URL and indicate imperial units
url = "http://api.openweathermap.org/data/2.5/weather?"
units = "imperial"
```


```python
# Build partial query URL
query = f"{url}appid={api_key}&units={units}&q="
```


```python
# Create an "extracts" object to get the temperature, humidity, cloudiness and wind speed
latitude = []
longitude = []
temperature = []
humidity = []
cloudiness = []
wind_speed = []
dates = []

for x in range(len(cities)):
    response = requests.get(f"{query}{cities[x]},{country_codes[x].upper()}").json()
    try:
        temperature.append(response['main']['temp_max'])
    except KeyError:
        temperature.append("NA")
    try:
        latitude.append(response['coord']['lat'])
    except KeyError:
        latitude.append("NA")
    try:
        longitude.append(response['coord']['lon'])
    except KeyError:
        longitude.append("NA")
    try:
        humidity.append(response['main']['humidity'])
    except KeyError:
        humidity.append("NA")
    try:
        cloudiness.append(response['clouds']['all'])
    except KeyError:
        cloudiness.append(0)
    try:
        wind_speed.append(response['wind']['speed'])
    except KeyError:
        wind_speed.append("NA")
    try:
        dates.append(response['dt'])
    except KeyError:
        dates.append("NA")


# Assemble everything into a data frame
weather_df = pd.DataFrame({"City": cities,
                           "Latitude": latitude,
                           "Longitude": longitude,
                           "Humidity": humidity,
                           "Max Temp": temperature,
                           "Cloudiness": cloudiness,
                           "Wind Speed": wind_speed,
                           "Date": dates,
                           "Country": country_codes
                          })

weather_df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>City</th>
      <th>Cloudiness</th>
      <th>Country</th>
      <th>Date</th>
      <th>Humidity</th>
      <th>Latitude</th>
      <th>Longitude</th>
      <th>Max Temp</th>
      <th>Wind Speed</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>hermanus</td>
      <td>44</td>
      <td>ZA</td>
      <td>1526873601</td>
      <td>86</td>
      <td>-34.42</td>
      <td>19.24</td>
      <td>14.88</td>
      <td>5.02</td>
    </tr>
    <tr>
      <th>1</th>
      <td>fortuna</td>
      <td>90</td>
      <td>US</td>
      <td>1526871300</td>
      <td>93</td>
      <td>40.6</td>
      <td>-124.16</td>
      <td>12</td>
      <td>5.1</td>
    </tr>
    <tr>
      <th>2</th>
      <td>ambon</td>
      <td>68</td>
      <td>ID</td>
      <td>1526873601</td>
      <td>99</td>
      <td>-3.7</td>
      <td>128.18</td>
      <td>27.93</td>
      <td>1.32</td>
    </tr>
    <tr>
      <th>3</th>
      <td>taolanaro</td>
      <td>0</td>
      <td>MG</td>
      <td>NA</td>
      <td>NA</td>
      <td>NA</td>
      <td>NA</td>
      <td>NA</td>
      <td>NA</td>
    </tr>
    <tr>
      <th>4</th>
      <td>punta arenas</td>
      <td>0</td>
      <td>CL</td>
      <td>1526871600</td>
      <td>80</td>
      <td>-53.16</td>
      <td>-70.91</td>
      <td>4</td>
      <td>7.2</td>
    </tr>
  </tbody>
</table>
</div>




```python
weather_df
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>City</th>
      <th>Cloudiness</th>
      <th>Country</th>
      <th>Date</th>
      <th>Humidity</th>
      <th>Latitude</th>
      <th>Longitude</th>
      <th>Max Temp</th>
      <th>Wind Speed</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>hermanus</td>
      <td>44</td>
      <td>ZA</td>
      <td>1526873601</td>
      <td>86</td>
      <td>-34.42</td>
      <td>19.24</td>
      <td>14.88</td>
      <td>5.02</td>
    </tr>
    <tr>
      <th>1</th>
      <td>fortuna</td>
      <td>90</td>
      <td>US</td>
      <td>1526871300</td>
      <td>93</td>
      <td>40.6</td>
      <td>-124.16</td>
      <td>12</td>
      <td>5.1</td>
    </tr>
    <tr>
      <th>2</th>
      <td>ambon</td>
      <td>68</td>
      <td>ID</td>
      <td>1526873601</td>
      <td>99</td>
      <td>-3.7</td>
      <td>128.18</td>
      <td>27.93</td>
      <td>1.32</td>
    </tr>
    <tr>
      <th>3</th>
      <td>taolanaro</td>
      <td>0</td>
      <td>MG</td>
      <td>NA</td>
      <td>NA</td>
      <td>NA</td>
      <td>NA</td>
      <td>NA</td>
      <td>NA</td>
    </tr>
    <tr>
      <th>4</th>
      <td>punta arenas</td>
      <td>0</td>
      <td>CL</td>
      <td>1526871600</td>
      <td>80</td>
      <td>-53.16</td>
      <td>-70.91</td>
      <td>4</td>
      <td>7.2</td>
    </tr>
    <tr>
      <th>5</th>
      <td>shache</td>
      <td>92</td>
      <td>CN</td>
      <td>1526873601</td>
      <td>100</td>
      <td>38.42</td>
      <td>77.24</td>
      <td>9.83</td>
      <td>3.77</td>
    </tr>
    <tr>
      <th>6</th>
      <td>rikitea</td>
      <td>32</td>
      <td>PF</td>
      <td>1526873602</td>
      <td>100</td>
      <td>-23.12</td>
      <td>-134.97</td>
      <td>24.98</td>
      <td>5.97</td>
    </tr>
    <tr>
      <th>7</th>
      <td>kapaa</td>
      <td>75</td>
      <td>US</td>
      <td>1526871840</td>
      <td>78</td>
      <td>22.08</td>
      <td>-159.32</td>
      <td>26</td>
      <td>8.2</td>
    </tr>
    <tr>
      <th>8</th>
      <td>tateyama</td>
      <td>75</td>
      <td>JP</td>
      <td>1526868000</td>
      <td>68</td>
      <td>36.66</td>
      <td>137.31</td>
      <td>20</td>
      <td>3.1</td>
    </tr>
    <tr>
      <th>9</th>
      <td>bluff</td>
      <td>88</td>
      <td>NZ</td>
      <td>1526873602</td>
      <td>100</td>
      <td>-46.6</td>
      <td>168.33</td>
      <td>9.48</td>
      <td>11.67</td>
    </tr>
    <tr>
      <th>10</th>
      <td>honiara</td>
      <td>40</td>
      <td>SB</td>
      <td>1526871600</td>
      <td>83</td>
      <td>-9.43</td>
      <td>159.96</td>
      <td>29</td>
      <td>4.1</td>
    </tr>
    <tr>
      <th>11</th>
      <td>mount isa</td>
      <td>0</td>
      <td>AU</td>
      <td>1526869800</td>
      <td>23</td>
      <td>-20.73</td>
      <td>139.49</td>
      <td>23</td>
      <td>6.7</td>
    </tr>
    <tr>
      <th>12</th>
      <td>avarua</td>
      <td>75</td>
      <td>CK</td>
      <td>1526871600</td>
      <td>78</td>
      <td>-21.21</td>
      <td>-159.78</td>
      <td>27</td>
      <td>4.1</td>
    </tr>
    <tr>
      <th>13</th>
      <td>ushuaia</td>
      <td>75</td>
      <td>AR</td>
      <td>1526868000</td>
      <td>80</td>
      <td>-54.81</td>
      <td>-68.31</td>
      <td>4</td>
      <td>6.07</td>
    </tr>
    <tr>
      <th>14</th>
      <td>hilo</td>
      <td>90</td>
      <td>US</td>
      <td>1526871180</td>
      <td>94</td>
      <td>19.71</td>
      <td>-155.08</td>
      <td>23</td>
      <td>4.1</td>
    </tr>
    <tr>
      <th>15</th>
      <td>illoqqortoormiut</td>
      <td>0</td>
      <td>GL</td>
      <td>NA</td>
      <td>NA</td>
      <td>NA</td>
      <td>NA</td>
      <td>NA</td>
      <td>NA</td>
    </tr>
    <tr>
      <th>16</th>
      <td>busselton</td>
      <td>0</td>
      <td>AU</td>
      <td>1526873603</td>
      <td>99</td>
      <td>-33.64</td>
      <td>115.35</td>
      <td>19.48</td>
      <td>7.77</td>
    </tr>
    <tr>
      <th>17</th>
      <td>mahebourg</td>
      <td>75</td>
      <td>MU</td>
      <td>1526871600</td>
      <td>88</td>
      <td>-20.41</td>
      <td>57.7</td>
      <td>23</td>
      <td>2.1</td>
    </tr>
    <tr>
      <th>18</th>
      <td>new norfolk</td>
      <td>36</td>
      <td>AU</td>
      <td>1526873604</td>
      <td>79</td>
      <td>-42.78</td>
      <td>147.06</td>
      <td>10.08</td>
      <td>8.67</td>
    </tr>
    <tr>
      <th>19</th>
      <td>provideniya</td>
      <td>40</td>
      <td>RU</td>
      <td>1526871600</td>
      <td>56</td>
      <td>64.42</td>
      <td>-173.23</td>
      <td>6</td>
      <td>4</td>
    </tr>
    <tr>
      <th>20</th>
      <td>cape town</td>
      <td>75</td>
      <td>ZA</td>
      <td>1526871600</td>
      <td>88</td>
      <td>-33.93</td>
      <td>18.42</td>
      <td>17</td>
      <td>7.7</td>
    </tr>
    <tr>
      <th>21</th>
      <td>vila franca do campo</td>
      <td>40</td>
      <td>PT</td>
      <td>1526871600</td>
      <td>72</td>
      <td>37.72</td>
      <td>-25.43</td>
      <td>16</td>
      <td>7.2</td>
    </tr>
    <tr>
      <th>22</th>
      <td>dawei</td>
      <td>36</td>
      <td>MM</td>
      <td>1526873604</td>
      <td>73</td>
      <td>14.07</td>
      <td>98.19</td>
      <td>30.18</td>
      <td>1.17</td>
    </tr>
    <tr>
      <th>23</th>
      <td>kitimat</td>
      <td>90</td>
      <td>CA</td>
      <td>1526871600</td>
      <td>58</td>
      <td>54.02</td>
      <td>-128.69</td>
      <td>13</td>
      <td>9.3</td>
    </tr>
    <tr>
      <th>24</th>
      <td>copiapo</td>
      <td>32</td>
      <td>CL</td>
      <td>1526871600</td>
      <td>76</td>
      <td>-27.37</td>
      <td>-70.33</td>
      <td>14</td>
      <td>2.1</td>
    </tr>
    <tr>
      <th>25</th>
      <td>khandyga</td>
      <td>64</td>
      <td>RU</td>
      <td>1526873605</td>
      <td>58</td>
      <td>62.65</td>
      <td>135.58</td>
      <td>9.53</td>
      <td>3.52</td>
    </tr>
    <tr>
      <th>26</th>
      <td>carahue</td>
      <td>0</td>
      <td>CL</td>
      <td>1526873605</td>
      <td>100</td>
      <td>-38.71</td>
      <td>-73.16</td>
      <td>6.63</td>
      <td>2.97</td>
    </tr>
    <tr>
      <th>27</th>
      <td>lebu</td>
      <td>0</td>
      <td>CL</td>
      <td>1526873605</td>
      <td>100</td>
      <td>-37.62</td>
      <td>-73.65</td>
      <td>11.93</td>
      <td>7.62</td>
    </tr>
    <tr>
      <th>28</th>
      <td>seymchan</td>
      <td>68</td>
      <td>RU</td>
      <td>1526873605</td>
      <td>44</td>
      <td>62.93</td>
      <td>152.39</td>
      <td>11.63</td>
      <td>3.02</td>
    </tr>
    <tr>
      <th>29</th>
      <td>ilulissat</td>
      <td>24</td>
      <td>GL</td>
      <td>1526871000</td>
      <td>74</td>
      <td>69.22</td>
      <td>-51.1</td>
      <td>-2</td>
      <td>1.5</td>
    </tr>
    <tr>
      <th>30</th>
      <td>ballina</td>
      <td>0</td>
      <td>AU</td>
      <td>1526873606</td>
      <td>100</td>
      <td>-28.87</td>
      <td>153.57</td>
      <td>19.73</td>
      <td>3.72</td>
    </tr>
    <tr>
      <th>31</th>
      <td>kutum</td>
      <td>0</td>
      <td>SD</td>
      <td>1526873606</td>
      <td>54</td>
      <td>14.2</td>
      <td>24.66</td>
      <td>15.73</td>
      <td>1.22</td>
    </tr>
    <tr>
      <th>32</th>
      <td>apple valley</td>
      <td>1</td>
      <td>US</td>
      <td>1526871300</td>
      <td>34</td>
      <td>34.5</td>
      <td>-117.19</td>
      <td>25</td>
      <td>2.6</td>
    </tr>
    <tr>
      <th>33</th>
      <td>pampas</td>
      <td>8</td>
      <td>PE</td>
      <td>1526873606</td>
      <td>100</td>
      <td>-12.4</td>
      <td>-74.87</td>
      <td>-0.93</td>
      <td>1.22</td>
    </tr>
    <tr>
      <th>34</th>
      <td>arraial do cabo</td>
      <td>0</td>
      <td>BR</td>
      <td>1526873606</td>
      <td>93</td>
      <td>-22.97</td>
      <td>-42.02</td>
      <td>20.48</td>
      <td>6.47</td>
    </tr>
    <tr>
      <th>35</th>
      <td>hofn</td>
      <td>92</td>
      <td>IS</td>
      <td>1526873606</td>
      <td>100</td>
      <td>64.25</td>
      <td>-15.21</td>
      <td>6.08</td>
      <td>13.47</td>
    </tr>
    <tr>
      <th>36</th>
      <td>albany</td>
      <td>0</td>
      <td>AU</td>
      <td>1526873607</td>
      <td>46</td>
      <td>-35.02</td>
      <td>117.88</td>
      <td>19.83</td>
      <td>4.82</td>
    </tr>
    <tr>
      <th>37</th>
      <td>port elizabeth</td>
      <td>0</td>
      <td>ZA</td>
      <td>1526871600</td>
      <td>87</td>
      <td>-33.92</td>
      <td>25.57</td>
      <td>12</td>
      <td>1</td>
    </tr>
    <tr>
      <th>38</th>
      <td>jamestown</td>
      <td>100</td>
      <td>SH</td>
      <td>1526873530</td>
      <td>100</td>
      <td>-15.94</td>
      <td>-5.72</td>
      <td>21.63</td>
      <td>6.72</td>
    </tr>
    <tr>
      <th>39</th>
      <td>arman</td>
      <td>90</td>
      <td>RU</td>
      <td>1526871600</td>
      <td>93</td>
      <td>59.7</td>
      <td>150.17</td>
      <td>5</td>
      <td>2</td>
    </tr>
    <tr>
      <th>40</th>
      <td>ramos arizpe</td>
      <td>24</td>
      <td>MX</td>
      <td>1526873607</td>
      <td>81</td>
      <td>25.54</td>
      <td>-100.95</td>
      <td>20.58</td>
      <td>1.77</td>
    </tr>
    <tr>
      <th>41</th>
      <td>mys shmidta</td>
      <td>0</td>
      <td>RU</td>
      <td>NA</td>
      <td>NA</td>
      <td>NA</td>
      <td>NA</td>
      <td>NA</td>
      <td>NA</td>
    </tr>
    <tr>
      <th>42</th>
      <td>cockburn town</td>
      <td>56</td>
      <td>BS</td>
      <td>1526873607</td>
      <td>92</td>
      <td>24.03</td>
      <td>-74.52</td>
      <td>26.18</td>
      <td>9.67</td>
    </tr>
    <tr>
      <th>43</th>
      <td>qaanaaq</td>
      <td>0</td>
      <td>GL</td>
      <td>1526873608</td>
      <td>83</td>
      <td>77.48</td>
      <td>-69.36</td>
      <td>-7.73</td>
      <td>2.77</td>
    </tr>
    <tr>
      <th>44</th>
      <td>kadoma</td>
      <td>12</td>
      <td>ZW</td>
      <td>1526873608</td>
      <td>96</td>
      <td>-18.34</td>
      <td>29.91</td>
      <td>11.98</td>
      <td>3.17</td>
    </tr>
    <tr>
      <th>45</th>
      <td>bengkulu</td>
      <td>0</td>
      <td>ID</td>
      <td>NA</td>
      <td>NA</td>
      <td>NA</td>
      <td>NA</td>
      <td>NA</td>
      <td>NA</td>
    </tr>
    <tr>
      <th>46</th>
      <td>castro</td>
      <td>0</td>
      <td>CL</td>
      <td>1526873608</td>
      <td>98</td>
      <td>-42.48</td>
      <td>-73.76</td>
      <td>2.23</td>
      <td>1.07</td>
    </tr>
    <tr>
      <th>47</th>
      <td>pitimbu</td>
      <td>40</td>
      <td>BR</td>
      <td>1526871600</td>
      <td>88</td>
      <td>-7.47</td>
      <td>-34.81</td>
      <td>25</td>
      <td>1.5</td>
    </tr>
    <tr>
      <th>48</th>
      <td>mocuba</td>
      <td>8</td>
      <td>MZ</td>
      <td>1526873608</td>
      <td>94</td>
      <td>-16.84</td>
      <td>36.99</td>
      <td>16.53</td>
      <td>1.17</td>
    </tr>
    <tr>
      <th>49</th>
      <td>hobart</td>
      <td>32</td>
      <td>AU</td>
      <td>1526873405</td>
      <td>69</td>
      <td>-42.88</td>
      <td>147.33</td>
      <td>12.18</td>
      <td>6.67</td>
    </tr>
  </tbody>
</table>
</div>


