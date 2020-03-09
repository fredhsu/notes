# Meaningful Availability
[https://www.usenix.org/system/files/nsdi20spring_hauer_prepub.pdf](nsdi20spring)
# Abstract
## Windowed User-uptime
* Directly models user-perceived availability and avoids bias in commonly used availability metrics
* by simultaneously calculating the availability metric over many windows it can readily distinguish between many short periods of unavailability and fewer but longer peroids of unavailability

# Introduction
* Good availability metric should be:
    * Meaningful
    * proportional
    * actionable
## Common approaches for quantifying availability
### success-ratio
* successful requests / total requests over a peroid of time (month/quarter)
* biased towards more active users
* assumes user behavoid does not change during outage
### incident ratio 
* up minutes / total minutes
* up minutes based on duration of known incidents
* inappropraite for large-scale distributed systems since they are almost never completely down or up

## Windowed user-uptime
### User-uptime
* Analyzed fine grained user request logs to determine up/down minutes for each user and aggregates these into overall metric
* Meaningful and proportional
### Windowed user-uptime
* Quantifies availability at all time windows
* Can distinguish many short outages from fewer longer ones
* Makes metric actionable

# Related Work
* MTTF and MTTR work for binary up/down systems, but not accurate for complex distributed systems
* success-ratio 
* confidence bounds for future performance based on collected past performance data (12)
* error budgets connected to an availability goal (29)
* issues when trying to achieve high availability (19)
* MSFT Cloud reports uptime : overall service up / total time 
    * immediately Meaningful
* % of successful interactive user requests
* GCP : Downtime : consecutive minutes of Downtime
* Slack : uptime is average derived from # of affected users
* AWS : Error rate : % requests with errors in 5 minute interval
    * Average is reported to customers as monthly uptime percentage

# Motivation
availability = good service / total demanded service
* helps prioritize work to improve the systems
* propoprtional - quantify the benefit of incremental improvement
* actionable - zero in on episodes of worst availability and find problems
* availabilty metrics are either time or count based
## Time based availabiltiy metrics
* MTTF/(MTTF+MTTR)
Mean time to failure - Mean time to recovery
Time between failures = uptime
Time to recover = Downtime
availability = uptime / (uptime + downtime)
* is failure when outage affects all users or one user?  neither is adequate since distributed systems avoid single points of failure 
* Commonly used time based availability metrics have the following issues:
    * not proportional to the severity (10% failure == 100% failure)
    * not proportional to # of affected users (peak vs slow)
    * not actionable - no guidance to the source of failure
    * not meaningful - rely on arbitrary thresholds or manual judgements

## Count based availability metrics
Success-ratio
* Easy to implement
* approximates usere perception better than internal instrumentation
* prone to bias, most active users are often 1000x more active than least active users and are overrepresented
* Fails to proportionally capture changes in availability
    * service down 3 hrs then up 3 hrs
    * While up many successful requests made
    * While down, periodic probing, lower than active use
    * or the reverse where client service floods trying to reconnect
* count based metric flaws:
    * not meaningful since not based on time
    * biased by highly active users
    * biased based on behavior during outages

## Probes
