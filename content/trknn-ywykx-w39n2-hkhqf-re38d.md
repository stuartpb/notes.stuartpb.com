# Geocoding and Location

Geocoding is the process of turning an address into a location.

https://github.com/GNOME/geocode-glib

## Where Am I?

In the browser, you can use the Location API.

Out of the browser, you can use [Geoclue](https://gitlab.freedesktop.org/geoclue/geoclue/wikis/home) as an abstraction around all the means to determine location you may have:

- Wifi triangulation (ie. Mozilla Location Service)
- An actual GPS in the computer

You can also use a device uploading triangulation data to "anonymously" associate to the location data from a proper GPS to a wifi triangulation point.

side thought about how someone might create a place that confuses location data [on this side page](ddzft-5zvkj-87azb-cr3zd-72er1)

## Plus Codes

This is just a little rabbit hole collecting the stuff Google published around their Plus Codes, which is really good across a lot of dimensions

Plus Codes provide a clean and simple alternative for serverless (reverse) geocoding "addresses".

https://plus.codes/

https://github.com/google/open-location-code/wiki/Evaluation-of-Location-Encoding-Systems#mls

https://github.com/google/open-location-code/blob/master/docs/olc_definition.adoc
