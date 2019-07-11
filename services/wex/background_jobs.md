# Background jobs

The [Waste Exemptions Back office](https://github.com/DEFRA/waste-exemptions-back-office-ta) contains 3 background jobs which are run on a nightly schedule. The schedules are set in [schedule.rb](https://github.com/DEFRA/waste-exemptions-back-office-ta/blob/master/config/schedule.rb).

The schedule is used to create and update [cron](https://en.wikipedia.org/wiki/Cron) jobs (the actual create and update occurs during deployment with [Capistrano](https://capistranorb.com/)). This is implemented using the [whenever gem](https://github.com/javan/whenever).

All background jobs are implemented as rake tasks, which are then called by the cron jobs we've created.

## Bulk export

The bulk export is essentially a data dump to a series of CSV files for every submitted registration in the service. Each file contains a months worth of registrations, based on their `submitted_at` date. The CSV files are uploaded to AWS S3 and users in the back office are presented with a series of links to them.

The job currently runs at 02:05 each day and takes approximately 25 minutes to complete.

## Electronic Public Register (EPR) export

The [EPR](https://environment.data.gov.uk/public-register/view/search-waste-exemptions) is an online search tool that users can use to find 'ACTIVE', non-expired waste exemptions, to validate they are registered.

It was maintained by a company called [Epimorphics](https://www.epimorphics.com/) but this was changed in Oct 2018 (we don't know who the new company is). The EPR export produces a CSV of all **ACTIVE** exemptions in a single CSV file. This is then uploaded to AWS S3.

The job currently runs at 22:05 each day and takes approximately 4 minutes to complete.

## Expired registrations

The expired registrations background job is used to identify and mark as EXPIRED those exemptions which have expired. It's filter is any exemptions with a status of **ACTIVE** and an expiry date less than the `CURRENT_DATE`.

The job currently runs at 00:05 each day.
