# Seismic Events Analyzer

## Context

There are actually quite few sources from which we can fetch seismic data (RESIF, IRIS or even local data from NAS server). So the main issue is to be able to manipulate data from different sources and different formats. Indeed, we may want to use data from RESIF in parallel to data from local server.

So, a possible solution is to develop algorithms and interfaces able to manage data from different sources and to fetch signals data and sensor type for each desired station in the world.

To develop this project, I'll use several existing APIs from different sources, but, before to use them, I have to establish a kind of simple documentation presenting essentials useful data and their real-world signification.

## APIs Documentation

IPGP's datacenter provides us European and International structures like:

- https://www.resif.fr/
- http://www.orfeus-eu.org/data/eida/
- https://www.epos-eu.org/
- http://www.fdsn.org/
- http://ds.iris.edu/ds/nodes/dmc/
- https://pds.nasa.gov/

1. RESIF

RESIF makes available url builder tools for different kind of data.

* Dataselect url builder (<code>https://seismology.resif.fr/dataselect/#/</code>) provides miniseed file with all corresponding data. These results can be found by sending the following GET HTTP request:

```HTTP
https://ws.resif.fr/fdsnws/dataselect/1/query?network=GL&station=TDBSM&location=00&quality=B&channel=EH1,EH2,EHZ&nodata=204
```

This mseed file can be opened in Octave or Matlab thanks to existing mseed dedicated files, <code>rdmseed.m</code> and <code>mkmseed.m</code>. To read mseed file we just have to run the following command <code>X = rdmseed(filename)</code>. This operation can take a while according to the file size.

* Station url builder (<code>https://seismology.resif.fr/dataselect/#/</code>) provides station informations. The corresponding response will provide the following informations:

```
- Network
- Station
- Location
- Channel
- Latitude
- Longitude
- Elevation
- Depth
- Azimuth
- Dip
- SensorDescription
- Scale
- ScaleFreq
- ScaleUnits
- SampleRate
- StartTime
- EndTime
```

By sending the following GET HTTP request:

```HTTP
https://ws.resif.fr/fdsnws/station/1/query?network=GL&station=TDBSM&location=00&channel=EH1,EH2,EHZ&includerestricted=true&level=channel&format=text&nodata=404
```

2. Orfeus

Orfeus allows us to send requests like the one on the next line, knowing that the result will be in <code>mseed</code> format: 

```HTTP
http://www.orfeus-eu.org/fdsnws/dataselect/1/query?network=NL&station=HGN&start=2017-01-01T00:00:00&end=2017-01-01T00:01:00
```

All the possible parameters are listed on this webpage: <code>http://www.orfeus-eu.org/data/eida/webservices/dataselect/</code>

But we also can send station request like

```HTTP
http://www.orfeus-eu.org/fdsnws/station/1/query?network=NL&station=HGN&level=response
```
3. EPOS

do it later

4. FDSN

This provider lets us send different types of requests. I'll list them here without to give any details. I'll focus on this provider later.

```HTTP
https://www.fdsn.org/ws/datacenters/1/query
```
```HTTP
https://www.fdsn.org/ws/datacenters/1/query?name=IRISDMC
```
```HTTP
https://www.fdsn.org/ws/datacenters/1/query?services=%2A-dataselect-1
```

5. IRIS

Thanks to IRIS provider, we can get networks code informations

```
- Network
- Description
- StartTime
- EndTime
- TotalStations
```

thanks to this request <code>http://service.iris.edu/fdsnws/station/1/query?level=network&nodata=404&format=text</code>

I think that it could be interested to develop the features given by this link <code>http://ds.iris.edu/ds/nodes/dmc/data/</code>

6. NASA

To do later

## First main steps

1. Get signals data and stations informations from different sources

Actual seismic network provides us different data from different sources or networks. The data's format can change according to given provenance. So the main goal here is to fetch correctly this data and to arrange them to make them uniform.

For that, I've planned to develop the following architecture:
[![Untitled-Diagram-3.png](https://i.postimg.cc/zXr8xzwD/Untitled-Diagram-3.png)](https://postimg.cc/vD3JBdSC)

I'll give here some text explanations.

First of all, the entire architecture will be hosted on my personal Raspberry Pi for the development phasis and will be hosted on my school server for the long term. On this Pi/Server, multiple packages: <code>influxdb</code>, <code>grafana</code> and some other ones that I'll list later.

I'll create a Tkinter interface with Python to choose the desired data sources and import local data from NAS server by example.
Once all data are fetched and/or imported, they're sent to data script for execute data processing. When the data are processed, they're sent to a time-series database (InfluxDB) to be easily displayed on Grafana interface. This Grafana interface is optional for this first step. But it is the techno I'll use for the further tasks so I think it's beneficial to use it right now.

<br/>

<code>I'll explane more precisely the two following steps later, when I got further in the project.</code>

<br/>
2. Export data with .mat format

All those data coming from different sources should be exportable in given format, ideally .mat. This format is frequently used by the research team, so it is a recommended format.
3. Use poles and zeros of sensor to plot signals in m/s

Actually, data are plotted in a two dimensions graph. Abscissa axis represents the date where the samples were taken. The ordinate axis unit is counts but it is strongly recommended to plot data on the ordinate axis using m/s unit. To compute this conversion, we have to use poles and zeros of used sensor.

## Second main step

Superimpose signals representation on a map to improve seismic evenenments analysis. We have to develop this part later when the first main step will be usable.