# Astrotools

Astrotools is a general purpose PHP library for astronomy.

## Table of Contents

<!-- MarkdownTOC depth=0 autolink=true bracket=round -->

- [Requirements](#requirements)
  - [PHP 5](#php-5)
- [Installation](#installation)
  - [PHP 5](#php-5-1)
- [Features](#features)
- [Why not use PHP's calendar extension?](#why-not-use-phps-calendar-extension)
- [Usage Examples](#usage-examples)
  - [Calculation of the Julian day from Date/Time](#calculation-of-the-julian-day-from-datetime)
  - [Calculation of Date/Time from Julian day](#calculation-of-datetime-from-julian-day)
  - [Calculation of sidereal time for a given Date/Time](#calculation-of-sidereal-time-for-a-given-datetime)
    - [Greenwich sidereal time](#greenwich-sidereal-time)
    - [Local sidereal time](#local-sidereal-time)
  - [Time helper](#time-helper)
    - [Convert hours, minutes, and seconds to decimal time](#convert-hours-minutes-and-seconds-to-decimal-time)
    - [Convert decimal time to hours, minutes, and seconds](#convert-decimal-time-to-hours-minutes-and-seconds)
    - [Convert between time and hour angle](#convert-between-time-and-hour-angle)
  - [Date of Easter Calculation](#date-of-easter-calculation)
  - [Calculation of ΔT](#calculation-of-δt)
- [Todo](#todo)
- [References](#references)

<!-- /MarkdownTOC -->

## Requirements

* PHP >= 7.0
* *bcmath* extension

### PHP 5

It's possible to use the 0.0.x branch of Astrotools with PHP 5.4 - 5.6. For details see the next section.

## Installation

```shell
composer require mjaschen/astrotools
```

### PHP 5

If you need to use Astrotools with PHP 5.4, 5.5, or 5.6, just require version 0.0.2:

```shell
composer require mjaschen/astrotools:0.0.2
```

Please keep in mind, that the 0.0.x branch is no longer maintained and won't get any updates.

## Features

* Julian day calculation (forward/reverse)
* Sidereal time calculation (GMST, local sidereal time)
* Date of Easter calculation
* Time helper

## Why not use PHP's calendar extension?

Some of the implemented features (e.g. calculation of Julian day or Date of Easter) are already provided by PHP's [calendar](http://php.net/manual/en/ref.calendar.php) extension.

These functions come with some problems, e. g. `easter_date()` can only calculate the Date of Easter for the timerange of unix timestamps (1970 January 1 to somewhere around 2037/2038).

## Usage Examples

### Calculation of the Julian day from Date/Time

```php
use Astrotools\Time\JulianDay;

$timestamp = new \DateTime('2015-02-25 12:01:36', new \DateTimeZone('Europe/Berlin'));
$jd = new JulianDay($timestamp);

echo $jd->getValue();
```

The code above produces the output shown below:

```
2457078.9594444
```

### Calculation of Date/Time from Julian day

```php
use Astrotools\Time\JulianDay;

$jd = new JulianDay();
$jd->setValue(2451545.0);

var_dump($jd->getDateTime());
```

The code above produces the output shown below:

```
class DateTime#10 (3) {
  public $date =>
  string(26) "2000-01-01 12:00:00.000000"
  public $timezone_type =>
  int(3)
  public $timezone =>
  string(3) "UTC"
}
```

### Calculation of sidereal time for a given Date/Time

#### Greenwich sidereal time

```php
use Astrotools\Time\SiderealTime;

$dt = new \DateTime('2007-12-25 00:00:00', new \DateTimeZone('UTC'));
$st = new SiderealTime($dt);

echo $st->getSiderealTime();
```

The code above produces the output shown below:

```
6.2086583333
```

#### Local sidereal time

Local sidereal time for Berlin, Germany (longitude = 13.5 degrees east):

```php
use Astrotools\Time\SiderealTime;

$dt = new \DateTime('2007-12-25 20:00:00', new \DateTimeZone('UTC'));
$st = new SiderealTime($dt);

echo $st->getLocalSiderealTime(13.5);
```

The code above produces the output shown below:

```
3.1634161794
```

### Time helper

#### Convert hours, minutes, and seconds to decimal time

```php
use Astrotools\Helper\Time;

$time = new Time(6, 42, 23.1337);

echo $time->getValue();
```

The code above produces the output shown below:

```
6.7064260278
```

#### Convert decimal time to hours, minutes, and seconds

```php
use Astrotools\Helper\Time;

$time = new Time(6.7064260278);

printf('%02d:%02d:%02.4f', $time->getHour(), $time->getMinute(), $time->getSecond());
```

The code above produces the output shown below:

```
06:42:23.1337
```

#### Convert between time and hour angle

```php
use Astrotools\Helper\Time;

$time = new Time(6, 42, 23.1337);

echo $time->getHourAngle() . PHP_EOL;

$time->setHourAngle(275);

echo $time->getValue();
```

The code above produces the output shown below:

```
100.596390417
18.333333333333
```

### Date of Easter Calculation

```php
use Astrotools\Time\DateOfEaster;

$doe = new DateOfEaster(2000);

echo $doe->getDate()->format('Y-m-d');
```

The code above produces the output shown below:

```
2000-04-23
```

### Calculation of ΔT

There exist multiple methods to get the value of ΔT for a given (decimal) year. 
One can lookup the value in [tables](http://maia.usno.navy.mil/) and interpolate 
it for the wanted date or calculate it using 
[polynomial expressions](http://eclipse.gsfc.nasa.gov/SEcat5/deltatpoly.html).

*Astrotools* currently provides the calculation of ΔT with polynomial 
expressions. Reasonably accurate values are provided for the timespan between 
the years -500 and 2150.

```php
<?php

use Astrotools\Time\DeltaT\PolynomialExpression;

$deltaT = new PolynomialExpression();

echo $deltaT->getDeltaT(2016.125);
```

The code above produces the output shown below:

```
69.568218578125
```

## Todo

* Support for microseconds in Julian day calculation
* Implement ΔT determination by table lookups

## References

* [Julian day formula - Astronomical Applications Department of the U.S. Naval Observatory](http://aa.usno.navy.mil/faq/docs/JD_Formula.php)
* [Julian Date Converter - Astronomical Applications Department of the U.S. Naval Observatory](http://aa.usno.navy.mil/data/docs/JulianDate.php)
* [Julian day - Wikipedia](http://en.wikipedia.org/wiki/Julian_day)
* [ΔT](https://en.wikipedia.org/wiki/%CE%94T)
* Astronomical Algorithms, Jean Meuss, 1998, ISBN 978-0943396613
* [Polynomial Expressions For Delta T (ΔT)](http://eclipse.gsfc.nasa.gov/SEcat5/deltatpoly.html)
