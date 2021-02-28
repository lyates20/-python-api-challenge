# -python-api-challenge

Trend 1 - Temperature vs Latitude Plot shows that the temperatures are higher the closer they are to the equator (latitude range of -20 to 20)
Trend 2 - City Latitude vs Windspeed Plot shows that Windspeeds are somewhat indifferent to Latitudes, but it is important to note that highest wind speed outliers all occur at latitudes furthest from the equator.
Trend 3 - Based on the data, it seems as though cloudiness and humidity are relatively consistent across all latitudes. Further analysis to include an additional variable may reveal further insights into those relationships
import json
import requests as req
import random
import seaborn as sns
import pandas as pd
import math as math
import time
import numpy as np
import matplotlib.pyplot as plt
from citipy import citipy
api_key = "ca948e37c7fc34fc07a1ece6ffdb31a0"
location_data = pd.DataFrame()
location_data['lat_values'] = [np.random.uniform(-90,90) for x in range(1500)]
location_data['lng_values'] = [np.random.uniform(-180, 180) for x in range(1500)]

location_data.head()
<style> .dataframe thead tr:only-child th { text-align: right; }
.dataframe thead th {
    text-align: left;
}

.dataframe tbody tr th {
    vertical-align: top;
}
</style>
lat_values	lng_values
0	63.004580	57.504731
1	-80.877433	-21.529340
2	-33.619646	69.244591
3	76.794412	-147.886088
4	29.843754	-140.093445
location_data['city'] = ""
location_data['country'] = ""

count = 0
for index, row in location_data.iterrows():
    near_city = citipy.nearest_city(row['lat_values'], row['lng_values']).city_name
    near_country = citipy.nearest_city(row['lat_values'], row['lng_values']).country_code
    location_data.set_value(index,"city",near_city)
    location_data.set_value(index,"country",near_country)
location_data.head()
<style> .dataframe thead tr:only-child th { text-align: right; }
.dataframe thead th {
    text-align: left;
}

.dataframe tbody tr th {
    vertical-align: top;
}
</style>
lat_values	lng_values	city	country
0	63.004580	57.504731	vuktyl	ru
1	-80.877433	-21.529340	ushuaia	ar
2	-33.619646	69.244591	mahebourg	mu
3	76.794412	-147.886088	barrow	us
4	29.843754	-140.093445	hilo	us
len(location_data)
1500
location_data=location_data.drop_duplicates(['city','country'],keep = "first")
location_data['Temp'] = ""
location_data['Humidity'] = ""
location_data['Cloudiness'] = ""
location_data['Wind Speed'] = ""
location_data["Lat"] =""
location_data["Longitude"] =""
sample_size = 500

target_url = 'http://api.openweathermap.org/data/2.5/weather?q='
units = 'imperial'
location_data.head()
<style> .dataframe thead tr:only-child th { text-align: right; }
.dataframe thead th {
    text-align: left;
}

.dataframe tbody tr th {
    vertical-align: top;
}
</style>
lat_values	lng_values	city	country	Temp	Humidity	Cloudiness	Wind Speed	Lat	Longitude
0	63.004580	57.504731	vuktyl	ru						
1	-80.877433	-21.529340	ushuaia	ar						
2	-33.619646	69.244591	mahebourg	mu						
3	76.794412	-147.886088	barrow	us						
4	29.843754	-140.093445	hilo	us						
record = 0
for index, row in location_data.iterrows():
    city_name = row['city']
    country_code = row['country']
    url = target_url + city_name + ',' + country_code + '&units=' + units + '&APPID=' + api_key
    print (url)
    try: 
        weather_response = req.get(url)
        weather_json = weather_response.json()
        latitude = weather_json["coord"]["lat"]
        longitude = weather_json["coord"]["lon"]
        temp = weather_json["main"]["temp"]
        humidity = weather_json["main"]["humidity"]
        cloud = weather_json["clouds"]["all"]
        wind = weather_json["wind"]["speed"]
        location_data.set_value(index,"Temp", temp)
        location_data.set_value(index,"Humidity",humidity)
        location_data.set_value(index,"Wind Speed", wind)
        location_data.set_value(index,"Cloudiness",cloud)
        location_data.set_value(index,"Lat", latitude)
        location_data.set_value(index,"Longitude",longitude)
        print("Retrieved data for %s, %s" % (city_name, country_code))
    except:
        print("No data for %s, %s" % (city_name,country_code))
    record += 1
    if record % 59 == 0:
        time.sleep(60)
http://api.openweathermap.org/data/2.5/weather?q=vuktyl,ru&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for vuktyl, ru
http://api.openweathermap.org/data/2.5/weather?q=ushuaia,ar&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for ushuaia, ar
http://api.openweathermap.org/data/2.5/weather?q=mahebourg,mu&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for mahebourg, mu
http://api.openweathermap.org/data/2.5/weather?q=barrow,us&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for barrow, us
http://api.openweathermap.org/data/2.5/weather?q=hilo,us&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for hilo, us
http://api.openweathermap.org/data/2.5/weather?q=saldanha,za&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for saldanha, za
http://api.openweathermap.org/data/2.5/weather?q=mys shmidta,ru&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
No data for mys shmidta, ru
http://api.openweathermap.org/data/2.5/weather?q=east london,za&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for east london, za
http://api.openweathermap.org/data/2.5/weather?q=tuktoyaktuk,ca&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for tuktoyaktuk, ca
http://api.openweathermap.org/data/2.5/weather?q=ribeira grande,pt&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for ribeira grande, pt
http://api.openweathermap.org/data/2.5/weather?q=qaanaaq,gl&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for qaanaaq, gl
http://api.openweathermap.org/data/2.5/weather?q=coahuayana,mx&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for coahuayana, mx
http://api.openweathermap.org/data/2.5/weather?q=punta arenas,cl&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for punta arenas, cl
http://api.openweathermap.org/data/2.5/weather?q=aberdeen,us&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for aberdeen, us
http://api.openweathermap.org/data/2.5/weather?q=avarua,ck&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for avarua, ck
http://api.openweathermap.org/data/2.5/weather?q=shieli,kz&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for shieli, kz
http://api.openweathermap.org/data/2.5/weather?q=amderma,ru&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
No data for amderma, ru
http://api.openweathermap.org/data/2.5/weather?q=hermanus,za&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for hermanus, za
http://api.openweathermap.org/data/2.5/weather?q=neuquen,ar&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for neuquen, ar
http://api.openweathermap.org/data/2.5/weather?q=saint-philippe,re&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for saint-philippe, re
http://api.openweathermap.org/data/2.5/weather?q=marcona,pe&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
No data for marcona, pe
http://api.openweathermap.org/data/2.5/weather?q=cape town,za&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for cape town, za
http://api.openweathermap.org/data/2.5/weather?q=new norfolk,au&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for new norfolk, au
http://api.openweathermap.org/data/2.5/weather?q=kaitangata,nz&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for kaitangata, nz
http://api.openweathermap.org/data/2.5/weather?q=los llanos de aridane,es&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for los llanos de aridane, es
http://api.openweathermap.org/data/2.5/weather?q=victoria,sc&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for victoria, sc
http://api.openweathermap.org/data/2.5/weather?q=carnarvon,au&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for carnarvon, au
http://api.openweathermap.org/data/2.5/weather?q=albany,au&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for albany, au
http://api.openweathermap.org/data/2.5/weather?q=kapaa,us&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for kapaa, us
http://api.openweathermap.org/data/2.5/weather?q=winneba,gh&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for winneba, gh
http://api.openweathermap.org/data/2.5/weather?q=bahjoi,in&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for bahjoi, in
http://api.openweathermap.org/data/2.5/weather?q=mataura,pf&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
No data for mataura, pf
http://api.openweathermap.org/data/2.5/weather?q=ningbo,cn&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for ningbo, cn
http://api.openweathermap.org/data/2.5/weather?q=hasaki,jp&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for hasaki, jp
http://api.openweathermap.org/data/2.5/weather?q=cayenne,gf&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for cayenne, gf
http://api.openweathermap.org/data/2.5/weather?q=nikolskoye,ru&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for nikolskoye, ru
http://api.openweathermap.org/data/2.5/weather?q=dingle,ie&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for dingle, ie
http://api.openweathermap.org/data/2.5/weather?q=rikitea,pf&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for rikitea, pf
http://api.openweathermap.org/data/2.5/weather?q=lorengau,pg&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for lorengau, pg
http://api.openweathermap.org/data/2.5/weather?q=panjakent,tj&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for panjakent, tj
http://api.openweathermap.org/data/2.5/weather?q=ancud,cl&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for ancud, cl
http://api.openweathermap.org/data/2.5/weather?q=busselton,au&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for busselton, au
http://api.openweathermap.org/data/2.5/weather?q=namatanai,pg&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for namatanai, pg
http://api.openweathermap.org/data/2.5/weather?q=bredasdorp,za&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for bredasdorp, za
http://api.openweathermap.org/data/2.5/weather?q=atuona,pf&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for atuona, pf
http://api.openweathermap.org/data/2.5/weather?q=tanggul,id&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for tanggul, id
http://api.openweathermap.org/data/2.5/weather?q=husavik,is&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for husavik, is
http://api.openweathermap.org/data/2.5/weather?q=bathsheba,bb&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for bathsheba, bb
http://api.openweathermap.org/data/2.5/weather?q=puerto ayora,ec&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for puerto ayora, ec
http://api.openweathermap.org/data/2.5/weather?q=makung,tw&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
No data for makung, tw
http://api.openweathermap.org/data/2.5/weather?q=black diamond,ca&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for black diamond, ca
http://api.openweathermap.org/data/2.5/weather?q=solnechnyy,ru&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for solnechnyy, ru
http://api.openweathermap.org/data/2.5/weather?q=inhambane,mz&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for inhambane, mz
http://api.openweathermap.org/data/2.5/weather?q=repnoye,ru&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for repnoye, ru
http://api.openweathermap.org/data/2.5/weather?q=kiunga,pg&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for kiunga, pg
http://api.openweathermap.org/data/2.5/weather?q=bluff,nz&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for bluff, nz
http://api.openweathermap.org/data/2.5/weather?q=fairbanks,us&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for fairbanks, us
http://api.openweathermap.org/data/2.5/weather?q=yellowknife,ca&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for yellowknife, ca
http://api.openweathermap.org/data/2.5/weather?q=upernavik,gl&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for upernavik, gl
http://api.openweathermap.org/data/2.5/weather?q=nizhneyansk,ru&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
No data for nizhneyansk, ru
http://api.openweathermap.org/data/2.5/weather?q=port alfred,za&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for port alfred, za
http://api.openweathermap.org/data/2.5/weather?q=pandan,ph&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for pandan, ph
http://api.openweathermap.org/data/2.5/weather?q=santa maria,cv&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for santa maria, cv
http://api.openweathermap.org/data/2.5/weather?q=salalah,om&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for salalah, om
http://api.openweathermap.org/data/2.5/weather?q=lahij,ye&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for lahij, ye
http://api.openweathermap.org/data/2.5/weather?q=beroroha,mg&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for beroroha, mg
http://api.openweathermap.org/data/2.5/weather?q=severo-kurilsk,ru&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for severo-kurilsk, ru
http://api.openweathermap.org/data/2.5/weather?q=gat,ly&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
No data for gat, ly
http://api.openweathermap.org/data/2.5/weather?q=seoul,kr&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for seoul, kr
http://api.openweathermap.org/data/2.5/weather?q=arraial do cabo,br&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for arraial do cabo, br
http://api.openweathermap.org/data/2.5/weather?q=vaini,to&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for vaini, to
http://api.openweathermap.org/data/2.5/weather?q=grand gaube,mu&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for grand gaube, mu
http://api.openweathermap.org/data/2.5/weather?q=dicabisagan,ph&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for dicabisagan, ph
http://api.openweathermap.org/data/2.5/weather?q=cherskiy,ru&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for cherskiy, ru
http://api.openweathermap.org/data/2.5/weather?q=gornopravdinsk,ru&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for gornopravdinsk, ru
http://api.openweathermap.org/data/2.5/weather?q=burnie,au&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for burnie, au
http://api.openweathermap.org/data/2.5/weather?q=luderitz,na&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for luderitz, na
http://api.openweathermap.org/data/2.5/weather?q=kurchum,kz&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for kurchum, kz
http://api.openweathermap.org/data/2.5/weather?q=namibe,ao&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for namibe, ao
http://api.openweathermap.org/data/2.5/weather?q=san cristobal,ec&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for san cristobal, ec
http://api.openweathermap.org/data/2.5/weather?q=belushya guba,ru&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
No data for belushya guba, ru
http://api.openweathermap.org/data/2.5/weather?q=rockland,us&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for rockland, us
http://api.openweathermap.org/data/2.5/weather?q=xucheng,cn&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for xucheng, cn
http://api.openweathermap.org/data/2.5/weather?q=tororo,ug&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for tororo, ug
http://api.openweathermap.org/data/2.5/weather?q=cordoba,ar&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for cordoba, ar
http://api.openweathermap.org/data/2.5/weather?q=aliwal north,za&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for aliwal north, za
http://api.openweathermap.org/data/2.5/weather?q=meulaboh,id&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for meulaboh, id
http://api.openweathermap.org/data/2.5/weather?q=san ramon,bo&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for san ramon, bo
http://api.openweathermap.org/data/2.5/weather?q=louisbourg,ca&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
No data for louisbourg, ca
http://api.openweathermap.org/data/2.5/weather?q=illoqqortoormiut,gl&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
No data for illoqqortoormiut, gl
http://api.openweathermap.org/data/2.5/weather?q=islamkot,pk&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for islamkot, pk
http://api.openweathermap.org/data/2.5/weather?q=oriximina,br&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for oriximina, br
http://api.openweathermap.org/data/2.5/weather?q=saint george,bm&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for saint george, bm
http://api.openweathermap.org/data/2.5/weather?q=mecca,sa&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for mecca, sa
http://api.openweathermap.org/data/2.5/weather?q=hithadhoo,mv&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for hithadhoo, mv
http://api.openweathermap.org/data/2.5/weather?q=moranbah,au&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for moranbah, au
http://api.openweathermap.org/data/2.5/weather?q=grand river south east,mu&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
No data for grand river south east, mu
http://api.openweathermap.org/data/2.5/weather?q=georgetown,sh&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for georgetown, sh
http://api.openweathermap.org/data/2.5/weather?q=kudahuvadhoo,mv&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for kudahuvadhoo, mv
http://api.openweathermap.org/data/2.5/weather?q=lyuban,by&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for lyuban, by
http://api.openweathermap.org/data/2.5/weather?q=sitka,us&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for sitka, us
http://api.openweathermap.org/data/2.5/weather?q=lixourion,gr&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for lixourion, gr
http://api.openweathermap.org/data/2.5/weather?q=xiaoshi,cn&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for xiaoshi, cn
http://api.openweathermap.org/data/2.5/weather?q=kulhudhuffushi,mv&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for kulhudhuffushi, mv
http://api.openweathermap.org/data/2.5/weather?q=srednekolymsk,ru&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for srednekolymsk, ru
http://api.openweathermap.org/data/2.5/weather?q=carutapera,br&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for carutapera, br
http://api.openweathermap.org/data/2.5/weather?q=taolanaro,mg&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
No data for taolanaro, mg
http://api.openweathermap.org/data/2.5/weather?q=chingirlau,kz&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for chingirlau, kz
http://api.openweathermap.org/data/2.5/weather?q=bouloupari,nc&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for bouloupari, nc
http://api.openweathermap.org/data/2.5/weather?q=katherine,au&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for katherine, au
http://api.openweathermap.org/data/2.5/weather?q=brewster,us&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for brewster, us
http://api.openweathermap.org/data/2.5/weather?q=sangin,af&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for sangin, af
http://api.openweathermap.org/data/2.5/weather?q=nouadhibou,mr&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for nouadhibou, mr
http://api.openweathermap.org/data/2.5/weather?q=birjand,ir&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for birjand, ir
http://api.openweathermap.org/data/2.5/weather?q=port keats,au&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for port keats, au
http://api.openweathermap.org/data/2.5/weather?q=bengkulu,id&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
No data for bengkulu, id
http://api.openweathermap.org/data/2.5/weather?q=olinda,br&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for olinda, br
http://api.openweathermap.org/data/2.5/weather?q=erenhot,cn&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for erenhot, cn
http://api.openweathermap.org/data/2.5/weather?q=severnoye,ru&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for severnoye, ru
http://api.openweathermap.org/data/2.5/weather?q=sechura,pe&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for sechura, pe
http://api.openweathermap.org/data/2.5/weather?q=nome,us&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for nome, us
http://api.openweathermap.org/data/2.5/weather?q=saleaula,ws&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
No data for saleaula, ws
http://api.openweathermap.org/data/2.5/weather?q=zhanatas,kz&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
No data for zhanatas, kz
http://api.openweathermap.org/data/2.5/weather?q=cidreira,br&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for cidreira, br
http://api.openweathermap.org/data/2.5/weather?q=port hardy,ca&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for port hardy, ca
http://api.openweathermap.org/data/2.5/weather?q=okha,ru&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for okha, ru
http://api.openweathermap.org/data/2.5/weather?q=vardo,no&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for vardo, no
http://api.openweathermap.org/data/2.5/weather?q=russell,nz&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for russell, nz
http://api.openweathermap.org/data/2.5/weather?q=barentsburg,sj&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
No data for barentsburg, sj
http://api.openweathermap.org/data/2.5/weather?q=awjilah,ly&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for awjilah, ly
http://api.openweathermap.org/data/2.5/weather?q=umzimvubu,za&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
No data for umzimvubu, za
http://api.openweathermap.org/data/2.5/weather?q=kundiawa,pg&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for kundiawa, pg
http://api.openweathermap.org/data/2.5/weather?q=san jeronimo,mx&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for san jeronimo, mx
http://api.openweathermap.org/data/2.5/weather?q=prizren,rs&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
No data for prizren, rs
http://api.openweathermap.org/data/2.5/weather?q=huilong,cn&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for huilong, cn
http://api.openweathermap.org/data/2.5/weather?q=bambous virieux,mu&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for bambous virieux, mu
http://api.openweathermap.org/data/2.5/weather?q=nuuk,gl&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for nuuk, gl
http://api.openweathermap.org/data/2.5/weather?q=port macquarie,au&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for port macquarie, au
http://api.openweathermap.org/data/2.5/weather?q=nanortalik,gl&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for nanortalik, gl
http://api.openweathermap.org/data/2.5/weather?q=lompoc,us&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for lompoc, us
http://api.openweathermap.org/data/2.5/weather?q=bartica,gy&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for bartica, gy
http://api.openweathermap.org/data/2.5/weather?q=xining,cn&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for xining, cn
http://api.openweathermap.org/data/2.5/weather?q=mobaye,cf&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for mobaye, cf
http://api.openweathermap.org/data/2.5/weather?q=kirovskiy,ru&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for kirovskiy, ru
http://api.openweathermap.org/data/2.5/weather?q=namtsy,ru&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for namtsy, ru
http://api.openweathermap.org/data/2.5/weather?q=kirakira,sb&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for kirakira, sb
http://api.openweathermap.org/data/2.5/weather?q=pitimbu,br&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for pitimbu, br
http://api.openweathermap.org/data/2.5/weather?q=kargasok,ru&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for kargasok, ru
http://api.openweathermap.org/data/2.5/weather?q=manggar,id&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for manggar, id
http://api.openweathermap.org/data/2.5/weather?q=sentyabrskiy,ru&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
No data for sentyabrskiy, ru
http://api.openweathermap.org/data/2.5/weather?q=vydrino,ru&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for vydrino, ru
http://api.openweathermap.org/data/2.5/weather?q=naze,jp&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for naze, jp
http://api.openweathermap.org/data/2.5/weather?q=castro,cl&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for castro, cl
http://api.openweathermap.org/data/2.5/weather?q=play cu,vn&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
No data for play cu, vn
http://api.openweathermap.org/data/2.5/weather?q=pacific grove,us&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for pacific grove, us
http://api.openweathermap.org/data/2.5/weather?q=minab,ir&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for minab, ir
http://api.openweathermap.org/data/2.5/weather?q=hovd,mn&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for hovd, mn
http://api.openweathermap.org/data/2.5/weather?q=kutum,sd&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for kutum, sd
http://api.openweathermap.org/data/2.5/weather?q=jamestown,sh&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for jamestown, sh
http://api.openweathermap.org/data/2.5/weather?q=butaritari,ki&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for butaritari, ki
http://api.openweathermap.org/data/2.5/weather?q=tiksi,ru&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for tiksi, ru
http://api.openweathermap.org/data/2.5/weather?q=culebra,us&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for culebra, us
http://api.openweathermap.org/data/2.5/weather?q=hobart,au&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for hobart, au
http://api.openweathermap.org/data/2.5/weather?q=havre-saint-pierre,ca&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for havre-saint-pierre, ca
http://api.openweathermap.org/data/2.5/weather?q=paracuru,br&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for paracuru, br
http://api.openweathermap.org/data/2.5/weather?q=guerrero negro,mx&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for guerrero negro, mx
http://api.openweathermap.org/data/2.5/weather?q=atbasar,kz&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for atbasar, kz
http://api.openweathermap.org/data/2.5/weather?q=saskylakh,ru&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for saskylakh, ru
http://api.openweathermap.org/data/2.5/weather?q=naftah,tn&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
No data for naftah, tn
http://api.openweathermap.org/data/2.5/weather?q=dakar,sn&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for dakar, sn
http://api.openweathermap.org/data/2.5/weather?q=mar del plata,ar&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for mar del plata, ar
http://api.openweathermap.org/data/2.5/weather?q=khatanga,ru&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for khatanga, ru
http://api.openweathermap.org/data/2.5/weather?q=klichka,ru&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for klichka, ru
http://api.openweathermap.org/data/2.5/weather?q=tiznit,ma&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for tiznit, ma
http://api.openweathermap.org/data/2.5/weather?q=mahibadhoo,mv&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for mahibadhoo, mv
http://api.openweathermap.org/data/2.5/weather?q=palabuhanratu,id&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
No data for palabuhanratu, id
http://api.openweathermap.org/data/2.5/weather?q=boguchany,ru&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for boguchany, ru
http://api.openweathermap.org/data/2.5/weather?q=pocone,br&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for pocone, br
http://api.openweathermap.org/data/2.5/weather?q=hamilton,bm&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for hamilton, bm
http://api.openweathermap.org/data/2.5/weather?q=port elizabeth,za&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for port elizabeth, za
http://api.openweathermap.org/data/2.5/weather?q=pangnirtung,ca&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for pangnirtung, ca
http://api.openweathermap.org/data/2.5/weather?q=goma,cd&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for goma, cd
http://api.openweathermap.org/data/2.5/weather?q=san patricio,mx&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for san patricio, mx
http://api.openweathermap.org/data/2.5/weather?q=kalanguy,ru&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for kalanguy, ru
http://api.openweathermap.org/data/2.5/weather?q=lagoa,pt&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for lagoa, pt
http://api.openweathermap.org/data/2.5/weather?q=oga,jp&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
No data for oga, jp
http://api.openweathermap.org/data/2.5/weather?q=smithers,ca&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for smithers, ca
http://api.openweathermap.org/data/2.5/weather?q=kapuskasing,ca&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for kapuskasing, ca
http://api.openweathermap.org/data/2.5/weather?q=grindavik,is&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for grindavik, is
http://api.openweathermap.org/data/2.5/weather?q=maniitsoq,gl&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for maniitsoq, gl
http://api.openweathermap.org/data/2.5/weather?q=marawi,sd&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for marawi, sd
http://api.openweathermap.org/data/2.5/weather?q=itarema,br&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for itarema, br
http://api.openweathermap.org/data/2.5/weather?q=thompson,ca&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for thompson, ca
http://api.openweathermap.org/data/2.5/weather?q=ponta do sol,cv&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for ponta do sol, cv
http://api.openweathermap.org/data/2.5/weather?q=sumkino,ru&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for sumkino, ru
http://api.openweathermap.org/data/2.5/weather?q=londiani,ke&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for londiani, ke
http://api.openweathermap.org/data/2.5/weather?q=cerritos,mx&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for cerritos, mx
http://api.openweathermap.org/data/2.5/weather?q=kishapu,tz&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for kishapu, tz
http://api.openweathermap.org/data/2.5/weather?q=tsihombe,mg&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
No data for tsihombe, mg
http://api.openweathermap.org/data/2.5/weather?q=olafsvik,is&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
No data for olafsvik, is
http://api.openweathermap.org/data/2.5/weather?q=yerbogachen,ru&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for yerbogachen, ru
http://api.openweathermap.org/data/2.5/weather?q=bud,no&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for bud, no
http://api.openweathermap.org/data/2.5/weather?q=baykit,ru&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for baykit, ru
http://api.openweathermap.org/data/2.5/weather?q=syamzha,ru&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for syamzha, ru
http://api.openweathermap.org/data/2.5/weather?q=waingapu,id&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for waingapu, id
http://api.openweathermap.org/data/2.5/weather?q=torbay,ca&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for torbay, ca
http://api.openweathermap.org/data/2.5/weather?q=angoche,mz&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for angoche, mz
http://api.openweathermap.org/data/2.5/weather?q=bogorodskoye,ru&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for bogorodskoye, ru
http://api.openweathermap.org/data/2.5/weather?q=chumikan,ru&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for chumikan, ru
http://api.openweathermap.org/data/2.5/weather?q=tubruq,ly&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
No data for tubruq, ly
http://api.openweathermap.org/data/2.5/weather?q=trairi,br&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for trairi, br
http://api.openweathermap.org/data/2.5/weather?q=bethel,us&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for bethel, us
http://api.openweathermap.org/data/2.5/weather?q=byron bay,au&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for byron bay, au
http://api.openweathermap.org/data/2.5/weather?q=yulara,au&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for yulara, au
http://api.openweathermap.org/data/2.5/weather?q=yoichi,jp&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for yoichi, jp
http://api.openweathermap.org/data/2.5/weather?q=danville,us&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for danville, us
http://api.openweathermap.org/data/2.5/weather?q=wanning,cn&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for wanning, cn
http://api.openweathermap.org/data/2.5/weather?q=aromashevo,ru&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for aromashevo, ru
http://api.openweathermap.org/data/2.5/weather?q=hofn,is&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for hofn, is
http://api.openweathermap.org/data/2.5/weather?q=llanes,es&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for llanes, es
http://api.openweathermap.org/data/2.5/weather?q=katsuura,jp&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for katsuura, jp
http://api.openweathermap.org/data/2.5/weather?q=pevek,ru&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for pevek, ru
http://api.openweathermap.org/data/2.5/weather?q=ponte de sor,pt&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for ponte de sor, pt
http://api.openweathermap.org/data/2.5/weather?q=pisco,pe&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for pisco, pe
http://api.openweathermap.org/data/2.5/weather?q=dikson,ru&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for dikson, ru
http://api.openweathermap.org/data/2.5/weather?q=panguna,pg&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for panguna, pg
http://api.openweathermap.org/data/2.5/weather?q=ilhabela,br&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for ilhabela, br
http://api.openweathermap.org/data/2.5/weather?q=leopold,au&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for leopold, au
http://api.openweathermap.org/data/2.5/weather?q=teknaf,bd&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for teknaf, bd
http://api.openweathermap.org/data/2.5/weather?q=mozarlandia,br&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for mozarlandia, br
http://api.openweathermap.org/data/2.5/weather?q=diego de almagro,cl&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for diego de almagro, cl
http://api.openweathermap.org/data/2.5/weather?q=asosa,et&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for asosa, et
http://api.openweathermap.org/data/2.5/weather?q=preobrazheniye,ru&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for preobrazheniye, ru
http://api.openweathermap.org/data/2.5/weather?q=honiara,sb&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for honiara, sb
http://api.openweathermap.org/data/2.5/weather?q=deputatskiy,ru&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for deputatskiy, ru
http://api.openweathermap.org/data/2.5/weather?q=hihifo,to&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
No data for hihifo, to
http://api.openweathermap.org/data/2.5/weather?q=nikolayevsk-na-amure,ru&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for nikolayevsk-na-amure, ru
http://api.openweathermap.org/data/2.5/weather?q=kahului,us&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for kahului, us
http://api.openweathermap.org/data/2.5/weather?q=poli,cm&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for poli, cm
http://api.openweathermap.org/data/2.5/weather?q=mamlyutka,kz&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for mamlyutka, kz
http://api.openweathermap.org/data/2.5/weather?q=vestmannaeyjar,is&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for vestmannaeyjar, is
http://api.openweathermap.org/data/2.5/weather?q=alice springs,au&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for alice springs, au
http://api.openweathermap.org/data/2.5/weather?q=high rock,bs&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for high rock, bs
http://api.openweathermap.org/data/2.5/weather?q=half moon bay,us&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for half moon bay, us
http://api.openweathermap.org/data/2.5/weather?q=souillac,mu&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for souillac, mu
http://api.openweathermap.org/data/2.5/weather?q=tongliao,cn&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for tongliao, cn
http://api.openweathermap.org/data/2.5/weather?q=talara,pe&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for talara, pe
http://api.openweathermap.org/data/2.5/weather?q=okhotsk,ru&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for okhotsk, ru
http://api.openweathermap.org/data/2.5/weather?q=fukuma,jp&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for fukuma, jp
http://api.openweathermap.org/data/2.5/weather?q=lavrentiya,ru&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for lavrentiya, ru
http://api.openweathermap.org/data/2.5/weather?q=cabo san lucas,mx&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for cabo san lucas, mx
http://api.openweathermap.org/data/2.5/weather?q=prigorodnyy,ru&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for prigorodnyy, ru
http://api.openweathermap.org/data/2.5/weather?q=northam,au&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for northam, au
http://api.openweathermap.org/data/2.5/weather?q=fare,pf&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for fare, pf
http://api.openweathermap.org/data/2.5/weather?q=geraldton,au&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for geraldton, au
http://api.openweathermap.org/data/2.5/weather?q=chokurdakh,ru&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for chokurdakh, ru
http://api.openweathermap.org/data/2.5/weather?q=vaitape,pf&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for vaitape, pf
http://api.openweathermap.org/data/2.5/weather?q=riyadh,sa&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for riyadh, sa
http://api.openweathermap.org/data/2.5/weather?q=wagar,sd&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for wagar, sd
http://api.openweathermap.org/data/2.5/weather?q=tevriz,ru&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for tevriz, ru
http://api.openweathermap.org/data/2.5/weather?q=eirunepe,br&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for eirunepe, br
http://api.openweathermap.org/data/2.5/weather?q=constitucion,mx&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for constitucion, mx
http://api.openweathermap.org/data/2.5/weather?q=hanzhong,cn&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for hanzhong, cn
http://api.openweathermap.org/data/2.5/weather?q=sibi,pk&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for sibi, pk
http://api.openweathermap.org/data/2.5/weather?q=kodiak,us&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for kodiak, us
http://api.openweathermap.org/data/2.5/weather?q=henties bay,na&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for henties bay, na
http://api.openweathermap.org/data/2.5/weather?q=rungata,ki&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
No data for rungata, ki
http://api.openweathermap.org/data/2.5/weather?q=verkhnevilyuysk,ru&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for verkhnevilyuysk, ru
http://api.openweathermap.org/data/2.5/weather?q=laguna,br&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
No data for laguna, br
http://api.openweathermap.org/data/2.5/weather?q=mae tha,th&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for mae tha, th
http://api.openweathermap.org/data/2.5/weather?q=vanimo,pg&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for vanimo, pg
http://api.openweathermap.org/data/2.5/weather?q=college,us&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for college, us
http://api.openweathermap.org/data/2.5/weather?q=derbent,ru&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for derbent, ru
http://api.openweathermap.org/data/2.5/weather?q=aktash,ru&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for aktash, ru
http://api.openweathermap.org/data/2.5/weather?q=saint-georges,gf&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
No data for saint-georges, gf
http://api.openweathermap.org/data/2.5/weather?q=tignere,cm&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for tignere, cm
http://api.openweathermap.org/data/2.5/weather?q=harper,lr&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for harper, lr
http://api.openweathermap.org/data/2.5/weather?q=kruisfontein,za&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for kruisfontein, za
http://api.openweathermap.org/data/2.5/weather?q=andenes,no&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for andenes, no
http://api.openweathermap.org/data/2.5/weather?q=tucuman,ar&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
No data for tucuman, ar
http://api.openweathermap.org/data/2.5/weather?q=arlit,ne&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for arlit, ne
http://api.openweathermap.org/data/2.5/weather?q=iquique,cl&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for iquique, cl
http://api.openweathermap.org/data/2.5/weather?q=lolua,tv&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
No data for lolua, tv
http://api.openweathermap.org/data/2.5/weather?q=codrington,ag&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
No data for codrington, ag
http://api.openweathermap.org/data/2.5/weather?q=gimli,ca&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for gimli, ca
http://api.openweathermap.org/data/2.5/weather?q=klaksvik,fo&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for klaksvik, fo
http://api.openweathermap.org/data/2.5/weather?q=can tho,vn&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for can tho, vn
http://api.openweathermap.org/data/2.5/weather?q=attawapiskat,ca&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
No data for attawapiskat, ca
http://api.openweathermap.org/data/2.5/weather?q=klyuchi,ru&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for klyuchi, ru
http://api.openweathermap.org/data/2.5/weather?q=linxia,cn&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for linxia, cn
http://api.openweathermap.org/data/2.5/weather?q=alihe,cn&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for alihe, cn
http://api.openweathermap.org/data/2.5/weather?q=port blair,in&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for port blair, in
http://api.openweathermap.org/data/2.5/weather?q=lebu,cl&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for lebu, cl
http://api.openweathermap.org/data/2.5/weather?q=tazmalt,dz&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for tazmalt, dz
http://api.openweathermap.org/data/2.5/weather?q=egvekinot,ru&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for egvekinot, ru
http://api.openweathermap.org/data/2.5/weather?q=mareeba,au&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for mareeba, au
http://api.openweathermap.org/data/2.5/weather?q=la tuque,ca&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for la tuque, ca
http://api.openweathermap.org/data/2.5/weather?q=ballina,ie&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for ballina, ie
http://api.openweathermap.org/data/2.5/weather?q=tokmak,kg&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
No data for tokmak, kg
http://api.openweathermap.org/data/2.5/weather?q=clyde river,ca&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for clyde river, ca
http://api.openweathermap.org/data/2.5/weather?q=senekal,za&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for senekal, za
http://api.openweathermap.org/data/2.5/weather?q=popondetta,pg&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for popondetta, pg
http://api.openweathermap.org/data/2.5/weather?q=joshimath,in&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for joshimath, in
http://api.openweathermap.org/data/2.5/weather?q=gangotri,in&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
No data for gangotri, in
http://api.openweathermap.org/data/2.5/weather?q=mongo,td&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for mongo, td
http://api.openweathermap.org/data/2.5/weather?q=faanui,pf&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for faanui, pf
http://api.openweathermap.org/data/2.5/weather?q=farmos,hu&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for farmos, hu
http://api.openweathermap.org/data/2.5/weather?q=palatka,ru&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for palatka, ru
http://api.openweathermap.org/data/2.5/weather?q=ewa beach,us&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for ewa beach, us
http://api.openweathermap.org/data/2.5/weather?q=nemuro,jp&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for nemuro, jp
http://api.openweathermap.org/data/2.5/weather?q=mgandu,tz&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for mgandu, tz
http://api.openweathermap.org/data/2.5/weather?q=coihaique,cl&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for coihaique, cl
http://api.openweathermap.org/data/2.5/weather?q=manica,mz&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for manica, mz
http://api.openweathermap.org/data/2.5/weather?q=norman wells,ca&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for norman wells, ca
http://api.openweathermap.org/data/2.5/weather?q=provideniya,ru&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for provideniya, ru
http://api.openweathermap.org/data/2.5/weather?q=tumannyy,ru&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
No data for tumannyy, ru
http://api.openweathermap.org/data/2.5/weather?q=saint-augustin,ca&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for saint-augustin, ca
http://api.openweathermap.org/data/2.5/weather?q=zonguldak,tr&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for zonguldak, tr
http://api.openweathermap.org/data/2.5/weather?q=hegang,cn&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for hegang, cn
http://api.openweathermap.org/data/2.5/weather?q=ballina,au&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for ballina, au
http://api.openweathermap.org/data/2.5/weather?q=manaia,nz&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for manaia, nz
http://api.openweathermap.org/data/2.5/weather?q=ola,ru&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for ola, ru
http://api.openweathermap.org/data/2.5/weather?q=duki,pk&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for duki, pk
http://api.openweathermap.org/data/2.5/weather?q=gunupur,in&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for gunupur, in
http://api.openweathermap.org/data/2.5/weather?q=matagami,ca&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for matagami, ca
http://api.openweathermap.org/data/2.5/weather?q=erzin,ru&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for erzin, ru
http://api.openweathermap.org/data/2.5/weather?q=samusu,ws&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
No data for samusu, ws
http://api.openweathermap.org/data/2.5/weather?q=susanville,us&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for susanville, us
http://api.openweathermap.org/data/2.5/weather?q=tanout,ne&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for tanout, ne
http://api.openweathermap.org/data/2.5/weather?q=sayaxche,gt&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for sayaxche, gt
http://api.openweathermap.org/data/2.5/weather?q=esperance,au&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for esperance, au
http://api.openweathermap.org/data/2.5/weather?q=severnyy,ru&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
No data for severnyy, ru
http://api.openweathermap.org/data/2.5/weather?q=riachao,br&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for riachao, br
http://api.openweathermap.org/data/2.5/weather?q=tam ky,vn&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for tam ky, vn
http://api.openweathermap.org/data/2.5/weather?q=awbari,ly&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for awbari, ly
http://api.openweathermap.org/data/2.5/weather?q=muzhi,ru&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for muzhi, ru
http://api.openweathermap.org/data/2.5/weather?q=tawkar,sd&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
No data for tawkar, sd
http://api.openweathermap.org/data/2.5/weather?q=tymovskoye,ru&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for tymovskoye, ru
http://api.openweathermap.org/data/2.5/weather?q=aloleng,ph&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for aloleng, ph
http://api.openweathermap.org/data/2.5/weather?q=shahrud,ir&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for shahrud, ir
http://api.openweathermap.org/data/2.5/weather?q=dargaville,nz&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for dargaville, nz
http://api.openweathermap.org/data/2.5/weather?q=harbin,cn&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for harbin, cn
http://api.openweathermap.org/data/2.5/weather?q=te anau,nz&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for te anau, nz
http://api.openweathermap.org/data/2.5/weather?q=san policarpo,ph&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for san policarpo, ph
http://api.openweathermap.org/data/2.5/weather?q=alexandria,eg&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for alexandria, eg
http://api.openweathermap.org/data/2.5/weather?q=tabou,ci&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for tabou, ci
http://api.openweathermap.org/data/2.5/weather?q=talnakh,ru&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for talnakh, ru
http://api.openweathermap.org/data/2.5/weather?q=caravelas,br&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for caravelas, br
http://api.openweathermap.org/data/2.5/weather?q=fortuna,us&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for fortuna, us
http://api.openweathermap.org/data/2.5/weather?q=wahiawa,us&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for wahiawa, us
http://api.openweathermap.org/data/2.5/weather?q=wiarton,ca&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for wiarton, ca
http://api.openweathermap.org/data/2.5/weather?q=krasnozatonskiy,ru&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for krasnozatonskiy, ru
http://api.openweathermap.org/data/2.5/weather?q=vila franca do campo,pt&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for vila franca do campo, pt
http://api.openweathermap.org/data/2.5/weather?q=washington,us&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for washington, us
http://api.openweathermap.org/data/2.5/weather?q=muros,es&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for muros, es
http://api.openweathermap.org/data/2.5/weather?q=kavaratti,in&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for kavaratti, in
http://api.openweathermap.org/data/2.5/weather?q=lusambo,cd&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for lusambo, cd
http://api.openweathermap.org/data/2.5/weather?q=contamana,pe&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for contamana, pe
http://api.openweathermap.org/data/2.5/weather?q=schattdorf,ch&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for schattdorf, ch
http://api.openweathermap.org/data/2.5/weather?q=poplar bluff,us&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for poplar bluff, us
http://api.openweathermap.org/data/2.5/weather?q=jatai,br&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for jatai, br
http://api.openweathermap.org/data/2.5/weather?q=hambantota,lk&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for hambantota, lk
http://api.openweathermap.org/data/2.5/weather?q=karamay,cn&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
No data for karamay, cn
http://api.openweathermap.org/data/2.5/weather?q=limoeiro do ajuru,br&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for limoeiro do ajuru, br
http://api.openweathermap.org/data/2.5/weather?q=cavalcante,br&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for cavalcante, br
http://api.openweathermap.org/data/2.5/weather?q=avera,pf&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
No data for avera, pf
http://api.openweathermap.org/data/2.5/weather?q=kennett,us&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for kennett, us
http://api.openweathermap.org/data/2.5/weather?q=puerto baquerizo moreno,ec&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for puerto baquerizo moreno, ec
http://api.openweathermap.org/data/2.5/weather?q=juifang,tw&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
No data for juifang, tw
http://api.openweathermap.org/data/2.5/weather?q=flin flon,ca&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for flin flon, ca
http://api.openweathermap.org/data/2.5/weather?q=camocim,br&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for camocim, br
http://api.openweathermap.org/data/2.5/weather?q=grand-lahou,ci&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for grand-lahou, ci
http://api.openweathermap.org/data/2.5/weather?q=george,za&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for george, za
http://api.openweathermap.org/data/2.5/weather?q=coihueco,cl&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for coihueco, cl
http://api.openweathermap.org/data/2.5/weather?q=dinsor,so&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
No data for dinsor, so
http://api.openweathermap.org/data/2.5/weather?q=san ramon de la nueva oran,ar&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for san ramon de la nueva oran, ar
http://api.openweathermap.org/data/2.5/weather?q=ruwi,om&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for ruwi, om
http://api.openweathermap.org/data/2.5/weather?q=faya,td&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
No data for faya, td
http://api.openweathermap.org/data/2.5/weather?q=shenjiamen,cn&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for shenjiamen, cn
http://api.openweathermap.org/data/2.5/weather?q=kununurra,au&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for kununurra, au
http://api.openweathermap.org/data/2.5/weather?q=kharian,pk&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for kharian, pk
http://api.openweathermap.org/data/2.5/weather?q=manono,cd&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for manono, cd
http://api.openweathermap.org/data/2.5/weather?q=tilichiki,ru&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for tilichiki, ru
http://api.openweathermap.org/data/2.5/weather?q=murray bridge,au&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for murray bridge, au
http://api.openweathermap.org/data/2.5/weather?q=labuhan,id&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for labuhan, id
http://api.openweathermap.org/data/2.5/weather?q=auki,sb&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for auki, sb
http://api.openweathermap.org/data/2.5/weather?q=karaul,ru&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
No data for karaul, ru
http://api.openweathermap.org/data/2.5/weather?q=ahipara,nz&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for ahipara, nz
http://api.openweathermap.org/data/2.5/weather?q=aldan,ru&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for aldan, ru
http://api.openweathermap.org/data/2.5/weather?q=bhaderwah,in&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for bhaderwah, in
http://api.openweathermap.org/data/2.5/weather?q=disna,by&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
No data for disna, by
http://api.openweathermap.org/data/2.5/weather?q=san quintin,mx&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
No data for san quintin, mx
http://api.openweathermap.org/data/2.5/weather?q=barentu,er&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for barentu, er
http://api.openweathermap.org/data/2.5/weather?q=maunabo,us&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
No data for maunabo, us
http://api.openweathermap.org/data/2.5/weather?q=farafangana,mg&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for farafangana, mg
http://api.openweathermap.org/data/2.5/weather?q=heihe,cn&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for heihe, cn
http://api.openweathermap.org/data/2.5/weather?q=port augusta,au&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for port augusta, au
http://api.openweathermap.org/data/2.5/weather?q=soe,id&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for soe, id
http://api.openweathermap.org/data/2.5/weather?q=tabuk,sa&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for tabuk, sa
http://api.openweathermap.org/data/2.5/weather?q=sao gabriel da cachoeira,br&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for sao gabriel da cachoeira, br
http://api.openweathermap.org/data/2.5/weather?q=sarana,ru&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for sarana, ru
http://api.openweathermap.org/data/2.5/weather?q=port moresby,pg&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for port moresby, pg
http://api.openweathermap.org/data/2.5/weather?q=hami,cn&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for hami, cn
http://api.openweathermap.org/data/2.5/weather?q=sao joao da barra,br&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for sao joao da barra, br
http://api.openweathermap.org/data/2.5/weather?q=tasiilaq,gl&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for tasiilaq, gl
http://api.openweathermap.org/data/2.5/weather?q=nizwa,om&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for nizwa, om
http://api.openweathermap.org/data/2.5/weather?q=sonqar,az&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
No data for sonqar, az
http://api.openweathermap.org/data/2.5/weather?q=haibowan,cn&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
No data for haibowan, cn
http://api.openweathermap.org/data/2.5/weather?q=chunskiy,ru&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for chunskiy, ru
http://api.openweathermap.org/data/2.5/weather?q=kitgum,ug&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for kitgum, ug
http://api.openweathermap.org/data/2.5/weather?q=durgapur,in&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for durgapur, in
http://api.openweathermap.org/data/2.5/weather?q=mogadishu,so&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for mogadishu, so
http://api.openweathermap.org/data/2.5/weather?q=tosya,tr&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for tosya, tr
http://api.openweathermap.org/data/2.5/weather?q=wilkie,ca&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for wilkie, ca
http://api.openweathermap.org/data/2.5/weather?q=plettenberg bay,za&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for plettenberg bay, za
http://api.openweathermap.org/data/2.5/weather?q=altay,cn&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for altay, cn
http://api.openweathermap.org/data/2.5/weather?q=baneh,ir&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for baneh, ir
http://api.openweathermap.org/data/2.5/weather?q=verkhoyansk,ru&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for verkhoyansk, ru
http://api.openweathermap.org/data/2.5/weather?q=poum,nc&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for poum, nc
http://api.openweathermap.org/data/2.5/weather?q=boa vista,br&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for boa vista, br
http://api.openweathermap.org/data/2.5/weather?q=boffa,gn&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for boffa, gn
http://api.openweathermap.org/data/2.5/weather?q=rabaul,pg&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for rabaul, pg
http://api.openweathermap.org/data/2.5/weather?q=muli,mv&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for muli, mv
http://api.openweathermap.org/data/2.5/weather?q=berdigestyakh,ru&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for berdigestyakh, ru
http://api.openweathermap.org/data/2.5/weather?q=athabasca,ca&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for athabasca, ca
http://api.openweathermap.org/data/2.5/weather?q=el castillo,mx&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for el castillo, mx
http://api.openweathermap.org/data/2.5/weather?q=zhigansk,ru&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for zhigansk, ru
http://api.openweathermap.org/data/2.5/weather?q=westport,ie&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for westport, ie
http://api.openweathermap.org/data/2.5/weather?q=teguldet,ru&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for teguldet, ru
http://api.openweathermap.org/data/2.5/weather?q=grandola,pt&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for grandola, pt
http://api.openweathermap.org/data/2.5/weather?q=osa,ru&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for osa, ru
http://api.openweathermap.org/data/2.5/weather?q=kavieng,pg&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for kavieng, pg
http://api.openweathermap.org/data/2.5/weather?q=bemidji,us&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for bemidji, us
http://api.openweathermap.org/data/2.5/weather?q=kunming,cn&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for kunming, cn
http://api.openweathermap.org/data/2.5/weather?q=qom,ir&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for qom, ir
http://api.openweathermap.org/data/2.5/weather?q=berlevag,no&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for berlevag, no
http://api.openweathermap.org/data/2.5/weather?q=comodoro rivadavia,ar&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for comodoro rivadavia, ar
http://api.openweathermap.org/data/2.5/weather?q=praia da vitoria,pt&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for praia da vitoria, pt
http://api.openweathermap.org/data/2.5/weather?q=sungaipenuh,id&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for sungaipenuh, id
http://api.openweathermap.org/data/2.5/weather?q=cambuquira,br&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for cambuquira, br
http://api.openweathermap.org/data/2.5/weather?q=ostrovnoy,ru&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for ostrovnoy, ru
http://api.openweathermap.org/data/2.5/weather?q=vad,ro&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for vad, ro
http://api.openweathermap.org/data/2.5/weather?q=constitucion,cl&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for constitucion, cl
http://api.openweathermap.org/data/2.5/weather?q=lata,sb&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
No data for lata, sb
http://api.openweathermap.org/data/2.5/weather?q=zemio,cf&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for zemio, cf
http://api.openweathermap.org/data/2.5/weather?q=longyearbyen,sj&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for longyearbyen, sj
http://api.openweathermap.org/data/2.5/weather?q=roebourne,au&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for roebourne, au
http://api.openweathermap.org/data/2.5/weather?q=vestbygda,no&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
No data for vestbygda, no
http://api.openweathermap.org/data/2.5/weather?q=kholmy,ua&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for kholmy, ua
http://api.openweathermap.org/data/2.5/weather?q=chuy,uy&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for chuy, uy
http://api.openweathermap.org/data/2.5/weather?q=aksu,cn&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for aksu, cn
http://api.openweathermap.org/data/2.5/weather?q=soure,br&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for soure, br
http://api.openweathermap.org/data/2.5/weather?q=ixtapa,mx&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for ixtapa, mx
http://api.openweathermap.org/data/2.5/weather?q=maposeni,tz&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for maposeni, tz
http://api.openweathermap.org/data/2.5/weather?q=narsaq,gl&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for narsaq, gl
http://api.openweathermap.org/data/2.5/weather?q=santa teresita,ph&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for santa teresita, ph
http://api.openweathermap.org/data/2.5/weather?q=terrace bay,ca&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for terrace bay, ca
http://api.openweathermap.org/data/2.5/weather?q=uruguaiana,br&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for uruguaiana, br
http://api.openweathermap.org/data/2.5/weather?q=atar,mr&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for atar, mr
http://api.openweathermap.org/data/2.5/weather?q=porto novo,cv&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for porto novo, cv
http://api.openweathermap.org/data/2.5/weather?q=garissa,ke&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for garissa, ke
http://api.openweathermap.org/data/2.5/weather?q=nirmal,in&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for nirmal, in
http://api.openweathermap.org/data/2.5/weather?q=sitrah,bh&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for sitrah, bh
http://api.openweathermap.org/data/2.5/weather?q=sobolevo,ru&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for sobolevo, ru
http://api.openweathermap.org/data/2.5/weather?q=komatipoort,za&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for komatipoort, za
http://api.openweathermap.org/data/2.5/weather?q=svetlaya,ru&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for svetlaya, ru
http://api.openweathermap.org/data/2.5/weather?q=port shepstone,za&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for port shepstone, za
http://api.openweathermap.org/data/2.5/weather?q=goderich,sl&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
No data for goderich, sl
http://api.openweathermap.org/data/2.5/weather?q=maningrida,au&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for maningrida, au
http://api.openweathermap.org/data/2.5/weather?q=bettiah,in&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for bettiah, in
http://api.openweathermap.org/data/2.5/weather?q=inirida,co&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for inirida, co
http://api.openweathermap.org/data/2.5/weather?q=halifax,ca&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for halifax, ca
http://api.openweathermap.org/data/2.5/weather?q=shawnee,us&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for shawnee, us
http://api.openweathermap.org/data/2.5/weather?q=santa maria del oro,mx&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for santa maria del oro, mx
http://api.openweathermap.org/data/2.5/weather?q=general roca,ar&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for general roca, ar
http://api.openweathermap.org/data/2.5/weather?q=solovetskiy,ru&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
No data for solovetskiy, ru
http://api.openweathermap.org/data/2.5/weather?q=bernau,de&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for bernau, de
http://api.openweathermap.org/data/2.5/weather?q=taksimo,ru&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for taksimo, ru
http://api.openweathermap.org/data/2.5/weather?q=lichtenburg,za&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for lichtenburg, za
http://api.openweathermap.org/data/2.5/weather?q=zenzeli,ru&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for zenzeli, ru
http://api.openweathermap.org/data/2.5/weather?q=linjiang,cn&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for linjiang, cn
http://api.openweathermap.org/data/2.5/weather?q=cooma,au&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for cooma, au
http://api.openweathermap.org/data/2.5/weather?q=tabiauea,ki&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
No data for tabiauea, ki
http://api.openweathermap.org/data/2.5/weather?q=broken hill,au&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for broken hill, au
http://api.openweathermap.org/data/2.5/weather?q=pishva,ir&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for pishva, ir
http://api.openweathermap.org/data/2.5/weather?q=nkhata bay,mw&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for nkhata bay, mw
http://api.openweathermap.org/data/2.5/weather?q=rocky mount,us&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for rocky mount, us
http://api.openweathermap.org/data/2.5/weather?q=oksfjord,no&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for oksfjord, no
http://api.openweathermap.org/data/2.5/weather?q=batticaloa,lk&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for batticaloa, lk
http://api.openweathermap.org/data/2.5/weather?q=alofi,nu&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for alofi, nu
http://api.openweathermap.org/data/2.5/weather?q=isangel,vu&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for isangel, vu
http://api.openweathermap.org/data/2.5/weather?q=kumluca,tr&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for kumluca, tr
http://api.openweathermap.org/data/2.5/weather?q=jinchang,cn&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for jinchang, cn
http://api.openweathermap.org/data/2.5/weather?q=palasa,in&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for palasa, in
http://api.openweathermap.org/data/2.5/weather?q=sur,om&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for sur, om
http://api.openweathermap.org/data/2.5/weather?q=beloha,mg&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for beloha, mg
http://api.openweathermap.org/data/2.5/weather?q=leninogorsk,ru&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for leninogorsk, ru
http://api.openweathermap.org/data/2.5/weather?q=port hawkesbury,ca&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for port hawkesbury, ca
http://api.openweathermap.org/data/2.5/weather?q=tougue,gn&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for tougue, gn
http://api.openweathermap.org/data/2.5/weather?q=juneau,us&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for juneau, us
http://api.openweathermap.org/data/2.5/weather?q=dolbeau,ca&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
No data for dolbeau, ca
http://api.openweathermap.org/data/2.5/weather?q=notse,tg&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for notse, tg
http://api.openweathermap.org/data/2.5/weather?q=baruun-urt,mn&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for baruun-urt, mn
http://api.openweathermap.org/data/2.5/weather?q=ambilobe,mg&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for ambilobe, mg
http://api.openweathermap.org/data/2.5/weather?q=caldas novas,br&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for caldas novas, br
http://api.openweathermap.org/data/2.5/weather?q=ust-kuyga,ru&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for ust-kuyga, ru
http://api.openweathermap.org/data/2.5/weather?q=baixa grande,br&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for baixa grande, br
http://api.openweathermap.org/data/2.5/weather?q=mount gambier,au&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for mount gambier, au
http://api.openweathermap.org/data/2.5/weather?q=yumen,cn&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for yumen, cn
http://api.openweathermap.org/data/2.5/weather?q=mumford,gh&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for mumford, gh
http://api.openweathermap.org/data/2.5/weather?q=springfield,us&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for springfield, us
http://api.openweathermap.org/data/2.5/weather?q=nuzvid,in&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for nuzvid, in
http://api.openweathermap.org/data/2.5/weather?q=shagamu,ng&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for shagamu, ng
http://api.openweathermap.org/data/2.5/weather?q=pingliang,cn&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for pingliang, cn
http://api.openweathermap.org/data/2.5/weather?q=ambon,id&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for ambon, id
http://api.openweathermap.org/data/2.5/weather?q=kaeo,nz&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for kaeo, nz
http://api.openweathermap.org/data/2.5/weather?q=portland,au&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for portland, au
http://api.openweathermap.org/data/2.5/weather?q=grand forks,ca&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for grand forks, ca
http://api.openweathermap.org/data/2.5/weather?q=kotovo,ru&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for kotovo, ru
http://api.openweathermap.org/data/2.5/weather?q=carnarvon,za&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for carnarvon, za
http://api.openweathermap.org/data/2.5/weather?q=beringovskiy,ru&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for beringovskiy, ru
http://api.openweathermap.org/data/2.5/weather?q=vikulovo,ru&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for vikulovo, ru
http://api.openweathermap.org/data/2.5/weather?q=richards bay,za&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for richards bay, za
http://api.openweathermap.org/data/2.5/weather?q=valparaiso,cl&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for valparaiso, cl
http://api.openweathermap.org/data/2.5/weather?q=wairoa,nz&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for wairoa, nz
http://api.openweathermap.org/data/2.5/weather?q=kamenskoye,ru&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
No data for kamenskoye, ru
http://api.openweathermap.org/data/2.5/weather?q=saint-michel-des-saints,ca&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for saint-michel-des-saints, ca
http://api.openweathermap.org/data/2.5/weather?q=lima,pe&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for lima, pe
http://api.openweathermap.org/data/2.5/weather?q=leningradskiy,ru&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for leningradskiy, ru
http://api.openweathermap.org/data/2.5/weather?q=belyy yar,ru&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for belyy yar, ru
http://api.openweathermap.org/data/2.5/weather?q=sinnamary,gf&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for sinnamary, gf
http://api.openweathermap.org/data/2.5/weather?q=benque viejo,bz&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
No data for benque viejo, bz
http://api.openweathermap.org/data/2.5/weather?q=martapura,id&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for martapura, id
http://api.openweathermap.org/data/2.5/weather?q=goulburn,au&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for goulburn, au
http://api.openweathermap.org/data/2.5/weather?q=acapulco,mx&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for acapulco, mx
http://api.openweathermap.org/data/2.5/weather?q=noumea,nc&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for noumea, nc
http://api.openweathermap.org/data/2.5/weather?q=onega,ru&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for onega, ru
http://api.openweathermap.org/data/2.5/weather?q=kuche,cn&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
No data for kuche, cn
http://api.openweathermap.org/data/2.5/weather?q=kramat,id&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for kramat, id
http://api.openweathermap.org/data/2.5/weather?q=stephenville,ca&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for stephenville, ca
http://api.openweathermap.org/data/2.5/weather?q=ilo,pe&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for ilo, pe
http://api.openweathermap.org/data/2.5/weather?q=bilasipara,in&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for bilasipara, in
http://api.openweathermap.org/data/2.5/weather?q=preeceville,ca&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for preeceville, ca
http://api.openweathermap.org/data/2.5/weather?q=riberalta,bo&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for riberalta, bo
http://api.openweathermap.org/data/2.5/weather?q=ilulissat,gl&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for ilulissat, gl
http://api.openweathermap.org/data/2.5/weather?q=komsomolskiy,ru&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for komsomolskiy, ru
http://api.openweathermap.org/data/2.5/weather?q=airai,pw&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
No data for airai, pw
http://api.openweathermap.org/data/2.5/weather?q=shimoda,jp&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for shimoda, jp
http://api.openweathermap.org/data/2.5/weather?q=kiili,ee&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for kiili, ee
http://api.openweathermap.org/data/2.5/weather?q=antalaha,mg&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for antalaha, mg
http://api.openweathermap.org/data/2.5/weather?q=anadyr,ru&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for anadyr, ru
http://api.openweathermap.org/data/2.5/weather?q=mollendo,pe&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for mollendo, pe
http://api.openweathermap.org/data/2.5/weather?q=brumunddal,no&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for brumunddal, no
http://api.openweathermap.org/data/2.5/weather?q=bambanglipuro,id&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for bambanglipuro, id
http://api.openweathermap.org/data/2.5/weather?q=sao filipe,cv&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for sao filipe, cv
http://api.openweathermap.org/data/2.5/weather?q=wloclawek,pl&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for wloclawek, pl
http://api.openweathermap.org/data/2.5/weather?q=maltahohe,na&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for maltahohe, na
http://api.openweathermap.org/data/2.5/weather?q=peniche,pt&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for peniche, pt
http://api.openweathermap.org/data/2.5/weather?q=pemangkat,id&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
No data for pemangkat, id
http://api.openweathermap.org/data/2.5/weather?q=opuwo,na&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for opuwo, na
http://api.openweathermap.org/data/2.5/weather?q=mount isa,au&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for mount isa, au
http://api.openweathermap.org/data/2.5/weather?q=broome,au&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for broome, au
http://api.openweathermap.org/data/2.5/weather?q=aklavik,ca&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for aklavik, ca
http://api.openweathermap.org/data/2.5/weather?q=eureka,us&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for eureka, us
http://api.openweathermap.org/data/2.5/weather?q=nampula,mz&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for nampula, mz
http://api.openweathermap.org/data/2.5/weather?q=ngukurr,au&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
No data for ngukurr, au
http://api.openweathermap.org/data/2.5/weather?q=jalu,ly&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for jalu, ly
http://api.openweathermap.org/data/2.5/weather?q=kemijarvi,fi&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
No data for kemijarvi, fi
http://api.openweathermap.org/data/2.5/weather?q=adre,td&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for adre, td
http://api.openweathermap.org/data/2.5/weather?q=samalaeulu,ws&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
No data for samalaeulu, ws
http://api.openweathermap.org/data/2.5/weather?q=pryluky,ua&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for pryluky, ua
http://api.openweathermap.org/data/2.5/weather?q=kibungo,rw&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for kibungo, rw
http://api.openweathermap.org/data/2.5/weather?q=male,mv&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for male, mv
http://api.openweathermap.org/data/2.5/weather?q=huainan,cn&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for huainan, cn
http://api.openweathermap.org/data/2.5/weather?q=palana,ru&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for palana, ru
http://api.openweathermap.org/data/2.5/weather?q=tukrah,ly&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
No data for tukrah, ly
http://api.openweathermap.org/data/2.5/weather?q=zheleznodorozhnyy,ru&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for zheleznodorozhnyy, ru
http://api.openweathermap.org/data/2.5/weather?q=betare oya,cm&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for betare oya, cm
http://api.openweathermap.org/data/2.5/weather?q=touros,br&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for touros, br
http://api.openweathermap.org/data/2.5/weather?q=evanston,us&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for evanston, us
http://api.openweathermap.org/data/2.5/weather?q=higuey,do&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
No data for higuey, do
http://api.openweathermap.org/data/2.5/weather?q=reitz,za&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for reitz, za
http://api.openweathermap.org/data/2.5/weather?q=pondicherry,in&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
No data for pondicherry, in
http://api.openweathermap.org/data/2.5/weather?q=iracoubo,gf&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for iracoubo, gf
http://api.openweathermap.org/data/2.5/weather?q=suicheng,cn&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for suicheng, cn
http://api.openweathermap.org/data/2.5/weather?q=allapalli,in&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for allapalli, in
http://api.openweathermap.org/data/2.5/weather?q=mpwapwa,tz&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for mpwapwa, tz
http://api.openweathermap.org/data/2.5/weather?q=wanaka,nz&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for wanaka, nz
http://api.openweathermap.org/data/2.5/weather?q=elat,il&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
No data for elat, il
http://api.openweathermap.org/data/2.5/weather?q=kudymkar,ru&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for kudymkar, ru
http://api.openweathermap.org/data/2.5/weather?q=chimbote,pe&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for chimbote, pe
http://api.openweathermap.org/data/2.5/weather?q=ilebo,cd&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for ilebo, cd
http://api.openweathermap.org/data/2.5/weather?q=vila,vu&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
No data for vila, vu
http://api.openweathermap.org/data/2.5/weather?q=kita,ml&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for kita, ml
http://api.openweathermap.org/data/2.5/weather?q=mitsamiouli,km&units=imperial&APPID=ca948e37c7fc34fc07a1ece6ffdb31a0
Retrieved data for mitsamiouli, km
location_data.head(20)
<style> .dataframe thead tr:only-child th { text-align: right; }
.dataframe thead th {
    text-align: left;
}

.dataframe tbody tr th {
    vertical-align: top;
}
</style>
lat_values	lng_values	city	country	Temp	Humidity	Cloudiness	Wind Speed	Lat	Longitude
0	63.004580	57.504731	vuktyl	ru	11.61	76	64	5.57	63.86	57.31
1	-80.877433	-21.529340	ushuaia	ar	48.2	81	20	16.11	-54.81	-68.31
2	-33.619646	69.244591	mahebourg	mu	78.8	88	40	6.42	-20.41	57.7
3	76.794412	-147.886088	barrow	us	27.37	62	1	5.75	39.51	-90.4
4	29.843754	-140.093445	hilo	us	68.43	94	90	3.36	19.71	-155.08
5	-40.174623	2.044285	saldanha	za	59	82	0	3.36	-33.01	17.94
6	88.709915	-171.769406	mys shmidta	ru						
7	-81.017596	58.192275	east london	za	69.79	100	88	6.08	-33.02	27.91
8	87.827188	-134.775348	tuktoyaktuk	ca	-6.54	83	75	18.34	69.44	-133.03
9	28.633250	-30.480538	ribeira grande	pt	57.96	100	92	7.92	38.52	-28.7
10	73.441959	-73.804482	qaanaaq	gl	-7.3	89	20	2.1	77.48	-69.36
11	1.621017	-111.561902	coahuayana	mx	64.57	67	0	2.06	18.62	-100.35
12	-70.589185	-117.157174	punta arenas	cl	44.6	65	0	10.29	-53.16	-70.91
13	45.482400	-99.738761	aberdeen	us	46.4	93	75	6.93	46.98	-123.82
15	-48.331502	-167.217154	avarua	ck	78.8	94	75	24.16	-21.21	-159.78
16	42.760941	65.358016	shieli	kz	4.32	72	0	8.88	44.18	66.74
19	78.157655	63.685742	amderma	ru						
20	-88.139615	-11.556958	hermanus	za	58.09	95	24	5.79	-34.42	19.24
21	-41.123730	-68.890685	neuquen	ar	80.6	44	0	9.78	-38.95	-68.06
22	-25.486569	58.282940	saint-philippe	re	76.03	69	0	6.93	-21.36	55.77
len(location_data)
595
location_data.to_csv("weatherpy_data.csv", encoding="utf-8", index=False)
location_data['Lat'] = pd.to_numeric(location_data['Lat'], errors = 'coerce')
location_data['Temp'] = pd.to_numeric(location_data['Temp'], errors = 'coerce')
location_data['Humidity'] = pd.to_numeric(location_data['Humidity'], errors = 'coerce')
location_data['Wind Speed'] = pd.to_numeric(location_data['Wind Speed'], errors = 'coerce')
location_data['Cloudiness'] = pd.to_numeric(location_data['Cloudiness'], errors = 'coerce')
location_data.head()
<style> .dataframe thead tr:only-child th { text-align: right; }
.dataframe thead th {
    text-align: left;
}

.dataframe tbody tr th {
    vertical-align: top;
}
</style>
lat_values	lng_values	city	country	Temp	Humidity	Cloudiness	Wind Speed	Lat	Longitude
0	63.004580	57.504731	vuktyl	ru	11.61	76.0	64.0	5.57	63.86	57.31
1	-80.877433	-21.529340	ushuaia	ar	48.20	81.0	20.0	16.11	-54.81	-68.31
2	-33.619646	69.244591	mahebourg	mu	78.80	88.0	40.0	6.42	-20.41	57.7
3	76.794412	-147.886088	barrow	us	27.37	62.0	1.0	5.75	39.51	-90.4
4	29.843754	-140.093445	hilo	us	68.43	94.0	90.0	3.36	19.71	-155.08
sns.set_context("notebook", font_scale=1.1)
sns.set_style("ticks")


sns.lmplot('Lat',
          'Temp',
          data=location_data,
          fit_reg=False,
          scatter_kws={"marker": "o",
                        "s": 20})
plt.grid(True)
plt.xlabel("Latitude")
plt.ylabel("Max Temperature (Celsius)")
plt.title("City Latitude vs Max Temperature")
plt.show()
weatherpy1

sns.set_context("notebook", font_scale=1.1)
sns.set_style("ticks")


sns.lmplot('Lat',
          'Humidity',
          data=location_data,
          fit_reg=False,
          scatter_kws={"marker": "o",
                        "s": 20})
plt.grid(True)
plt.xlabel("Latitude")
plt.ylabel("Humidity (%)")
plt.title("City Latitude vs Humidity")
plt.show()
weatherpy2

sns.set_context("notebook", font_scale=1.1)
sns.set_style("ticks")


sns.lmplot('Lat',
          'Cloudiness',
          data=location_data,
          fit_reg=False,
          scatter_kws={"marker": "o",
                        "s": 20})
plt.grid(True)
plt.xlabel("Latitude")
plt.ylabel("Cloudiness (%)")
plt.title("City Latitude vs Cloudiness")
plt.show()
weatherpy3

sns.set_context("notebook", font_scale=1.1)
sns.set_style("ticks")


sns.lmplot('Lat',
          'Wind Speed',
          data=location_data,
          fit_reg=False,
          scatter_kws={"marker": "o",
                        "s": 20})
plt.grid(True)
plt.xlabel("Latitude")
plt.ylabel("Wind Speed (kph)")
plt.title("City Latitude vs Wind Speed")
plt.show()
weatherpy4


