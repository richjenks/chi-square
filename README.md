# Stats

Statistics library for non-statistical people

- [Introduction](#introduction)
- [Installation](#installation)
- [Quickstart](#quickstart)
- [Usage](#usage)
	- [Mean/Average](#meanaverage)
	- [Median](#median)
	- [Mode](#mode)
	- [Frequencies](#frequencies)
	- [Range](#range)
	- [Variance, Standard Deviation & Standard Error](#variance-standard-deviation--standard-error)
		- [Individual Deviations](#individual-deviations)
		- [Sample or Population](#sample-or-population)
	- [Quartiles, Interquartile Range & Outliers](#quartiles-interquartile-range--outliers)
		- [Quartiles](#quartiles)
		- [Interquartile Range](#interquartile-range)
		- [Outliers](#outliers)
	- [Percentiles](#percentiles)
		- [All Percentiles](#all-percentiles)
		- [Single Percentile](#single-percentile)
		- [In Percentile](#in-percentile)
- [CLI](#cli)
- [Unit Tests](#unit-tests)

## Introduction

If you're into statistics then PHP will not be your language of choice (try [R](https://www.r-project.org/) instead) but if for any reason you, a non-statistician, need to _do some stats_ then this library aims to provide a simple set of methods for common statistical functions.

By design, with the exception of statistical tests, functions generally accept a single series of data at a time. This is to keep the library simple to use

Many of the methods in this library are available from the [Statistics Extension](http://uk1.php.net/manual/en/book.stats.php), however this is not included in PHP by default. If possible, I'd recommend using this extension rather than my stats library.

## Installation

1. Install with Composer: `composer require richjenks/stats`
1. Include autoloader: `require 'vendor/autoload.php';`
1. All static methods are available from the `RichJenks\Stats` class

## Quickstart

```php
<?php
require 'vendor/autoload.php';
use RichJenks\Stats;
echo Stats::mean([1, 2, 3]);
// 2
```

> Stats will generally return either a `float` or an `array`, whichever is most appropriate for the function

## Usage

### Mean/Average

Calculates the mean/average of given data:

```php
Stats::mean([1, 2, 3]);
// 2

Stats::mean([15, 1000, 68.5, 9]);
// 273.125
```

> The `average` function aliases `mean`, e.g. `Stats::average([1, 2, 3]);` also returns `2`

### Median

Calculates the median (middle value) of given data:

```php
Stats::median([1, 2, 3, 4]);
// 2.5

Stats::median([3.141, 1.618, 1.234]);
// 1.618
```

### Mode

Calculates the mode(s) — most common value(s) — of given data:

```php
Stats::mode([1, 2, 2, 3]);
// [2]

`Stats::mode([1, 2, 2, 3, 3]);
// [2, 3]
```

> This function always return an array because it is able to handle multi-modal data and an empty array would mean there is no mode

### Frequencies

Constructs a sorted array of frequencies for each value in a series:

```php
Stats::frequencies([1, 2, 3]);
// [
//   1 => 1,
//   2 => 1,
//   3 => 1,
// ]

Stats::frequencies([10, 20, 20]);
// [
//   20 => 2,
//   10 => 1,
// ]
```

### Range

Determines the range (highest minus lowest) of given data:

```php
Stats::range([1, 9]);
// 8

Stats::range([-41, 1.61803]);
// 42.61803
```

### Variance & Standard Deviation

These functions calculate:

- **Variance**: square of average variation from the mean
- **Standard Deviation**: average variation from the mean (square root of Variance)

```php
$data = [1, 2, 3, 4, 5];

Stats::variance($data);
// 2.5

Stats::sd($data);
// 1.5811388301
```

#### Individual Deviations

The `deviations` function is also available if you require the deviations for each individual value, for example:

```php
Stats::deviations([1, 2, 3, 4, 5]);
// [
//   1 => 4,
//   2 => 1,
//   3 => 0,
//   4 => 1,
//   5 => 4,
// ]

Stats::deviations([42, 75, 101, 22.5, 18]);
// [
//   42   => 94.09,
//   75   => 542.89,
//   101  => 2430.49,
//   22.5 => 852.64,
//   18   => 1135.69,
// ]
```

#### Sample or Population

`Sample` is the default mode for Variance and Standard Deviation but if you're unsure of the effect this decision has on your data then you probably don't need it and can skip this section.

> **Definitions**
>
> *Population*
> Every subject applicable, e.g. people who wear glasses or non-extinct species of frog
>
> *Sample*
> The subset of subjects for which data is available, e.g. 100 glass-wearing subjects or a dozen species of frog

You can optionally pass the constants `Stats::Sample` or `Stats::POPULATION` as second parameters to determine whether your data is for a sample or a whole population:

```php
$data = [1, 2, 3, 4, 5];

Stats::variance($data, Stats::POPULATION);
// 2

Stats::sd($data, Stats::POPULATION);
// 1.4142135624
```

### Standard Error of the Mean

Estimates how well the sample mean approximates the population mean:

```php
Stats::sem([1, 2, 3, 4, 5]);
// 0.70710678118655
```

### Quartiles, Interquartile Range & Outliers

These functions calculate the data required to construct a [Box Plot](https://en.wikipedia.org/wiki/Box_plot) which, when you understand what each data point means, is a concise way of displaying and comparing data sets.

#### Quartiles

Calculates Quartiles 0—4, where:

- 0 is the lowest data point
- 1 is Q¹
- 2 is Q² (the median)
- 3 is Q³
- 4 is the highest data point

```php
Stats::quartiles([1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12]);
// [
//   0 => 1,
//   1 => 3.5,
//   2 => 6.5,
//   3 => 9.5,
//   4 => 12,
// ]

Stats::quartiles([839, 560, 607, 828, 875, 805, 646, 450, 930, 443])
// [
//   0 => 443,
//   1 => 560,
//   2 => 725.5,
//   3 => 839,
//   4 => 930,
// ]
```

#### Interquartile Range

Calculates the range between Q¹ and Q³ (the middle 50% of data):

```php
Stats::iqr([1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12]);
// 6

Stats::iqr([839, 560, 607, 828, 875, 805, 646, 450, 930, 443])
// 279
```

#### Outliers

Determines which values in a series are outliers (too far from the other values so sometimes omitted from the data set, possibly due to experimental error):

```php
Stats::outliers([1, 2, 3, 4, 5, 6, 7, 8, 9, 10]);
// []

Stats::outliers([1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 999])
// [999]
```

#### Inliers

Determines which values in a series are not outliers, i.e. removes outliers:

```php
Stats::inliers([1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 999])
// [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
```

#### Whiskers

Determines the lower and upper limit for identifying outliers:

```php
Stats::whiskers([1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 999])
// ['lower' => -6, 'upper' => 18]
```

### Percentiles

All percentile functions accept an optional additional parameter for rounding that works as follows:

- If omitted, percentages are rounded to the nearest whole
- If a positive integer, percentages are rounded to that many decimal places
- If a negative integer (e.g. `-1`), percentages are not rounded

#### All Percentiles

Determines the percentile of each value:

```php
// Closest Rank
Stats::percentiles([15, 20, 35, 40, 50]);
// [
//   15 => 0,
//   20 => 14,
//   35 => 57,
//   40 => 71,
//   50 => 100,
// ]
```

#### Single Percentile

Determines the value closest to the given percentile:

```php
Stats::percentile([15, 20, 35, 40, 50], 75);
// [
//   'value'      => 40,
//   'percentile' => 71,
// ]
```

#### Intra-Percentile

Determines the values that fall in the given percentile, _i.e._ the lowest _x_% of all values:

```php
Stats::intrapercentile([15, 20, 35, 40, 50], 60);
// [
//   15 => 0,
//   20 => 14,
//   35 => 57,
// ]
```

## CLI

CLI usage is supported via the included `scli` (Stats Command Line Interface) file and simply expects the name of the required method followed by its arguments:

```bash
./scli mean 1 2 3
# 2

./scli inliers 1 2 3 4 5 999
# 1,2,3,4,5
```

> In cases where the result is a set (i.e. an array) it is presented as comma-separated

## Unit Tests

```bash
phpunit --bootstrap Stats.php tests/StatsTest
```
