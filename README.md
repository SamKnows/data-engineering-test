<div align="center">
  <img src="samknows.png" align="center" width="60">
  <h1 align="center">SamKnows Data Engineering Test</h1>
</div>

## Outline

In this scenario the provided download measurement data is being ingested into the SamKnows big data platform, your task is to identify any anomalies in the data. 

The end result of this test should be a report outlining anomalies found in the data to be investigated. 

Please provide us with a public github (or similar) link to your solution.

We recommend that you spend no longer than 2 hours on this test

You can find a gzipped CSV file containing the data <a href="https://files.samknows.com/~techtests/data_engineering.csv.gz">here</a>

## Data Formats

| Column            | Type     | Description                                                 |
| ------------------|:--------:| :----------------------------------------------------------:|
| dtime             | datetime | The datetime the test was run                               |
| device_identifier | string   |   UUID representing the device                              |
| bytes_total       | int      |    The total number of bytes transferred during the test    |
| bytes_per_second  | int      |    The bytes per second calculated for the test             |
| successes         | int      |    Number of successful downloads (in this context 1 or 0)  |
| failures          | int      |    number of failed attempts (in this context 1 or 0)       |

## Approach

You can use any method you wish to identify the anomalies as long as it is demonstrable and we are capable of running the solution during the review process.

## What we are looking for

1. A well defined solution: what assumptions did you make, why did you choose this approach and how do we run it?
2. A clear report outlining the anomalies found in the data
3. The effieciency of the approach to finding the anomalies
4. Given more time, what would you do to improve your solution?

Good luck!
