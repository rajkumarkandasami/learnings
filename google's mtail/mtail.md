# Google's Mtail https://github.com/google/mtail

## Introduction:
Mtail is used to extract whitebox monitoring data from application logs for collection in a timeseries database.
    
## Implementation in Sample app:
I instrumented mtail in a sample ruby application with following configurations, and make it echo metrics in prometheus format.

## app.mtail

------------------------------------------------------------------
```
counter ruby_http_requests by controller, action

gauge ruby_response_time by controller, action

/^(?P<date>\d{4}-\d{2}-\d{2} \d{2}:\d{2}:\d{2}) id=.*, e=, ip=(?P<ip_address>\d{1,3}\.\d{1,3}\.\d{1,3}\.\d{1,3}), u=(?P<userid>\d+), d=(?P<dmain>.*), url=(?P<url>.*), p=(?P<path>.*), c=(?P<controller>.*), acn=(?P<action>.*), h=(?P<host>.*), sts=(?P<status>.*), f=(?P<format>.*), db=(?P<dbduration>.*), vw=(?P<viewtime>.*), rc=(?P<rediscalls>.*), r=(?P<redisduration>.*), mc=(?P<memcachecalls>.*), mdr=(?P<mcduration>.*), m=.(?P<m>.*), oa=(?P<objalloc>.*), qt=(?P<qt>.*), tdur=(?P<totaldur>.*), dur=(?P<duration>\d+)\.\d+.*$/ {
strptime($date, "2006-01-02 15:04:05")

ruby_http_requests[$controller][$action]++

ruby_response_time[$controller][$action] = $duration

}
```
-------------------------------------------------------------------
where as the application.log file having a format like,

## sample application.log

-------------------------------------------------------------------
```
2019-12-18 06:27:06 id=3d66cb0b-0bf5-4c91-9ff8-xxxxxxxxxxxx, e=, ip=xxx.xxx.xxx.xxx, u=17, d=raj.example.com, url=https://raj.example.com/api/marketplace_apps, p=/api/_/marketplace_apps, c=Ember::AppstoreController, acn=index, h=xxx.xxx.xxx.xxx, sts=503, f=json, db=1.86, vw=0.75, rc=7, r=8.13, mc=8, mdr=1, m=3.33, oa=16588, qt=1.894, tdur=408.22, dur=406.33

2019-12-18 06:27:06 id=97787d6a-fb29-4145-83bf-xxxxxxxxxxxx, e=, ip=xxx.xxx.xxx.xxx, u=17, d=raj.example.com, url=https://raj.example.com/api/installed_applications, p=/api/_/installed_applications, c=Ember::InstalledAppsController, acn=index, h=xxx.xxx.xxx.xxx, sts=200, f=json, db=2.53, vw=0.73, rc=7, r=7.9, mc=6, mdr=1, m=2.3, oa=15911, qt=1.734, tdur=49.567, dur=47.83
```
--------------------------------------------------------------------

## to collect sample metrics from mtail,
### curl localhost:3903/metrics

---------------------------------------------------------------------
```
\#HELP helpkit_http_requests defined at app.mtail:1:9-29
\#TYPE helpkit_http_requests counter

helpkit_http_requests{action="index",controller="Ember::MarketplaceApps",prog="app.mtail"} 1
helpkit_http_requests{action="index",controller="Ember::InstalledApplications",prog="app.mtail"} 9

```
----------------------------------------------------------------------------------

##
