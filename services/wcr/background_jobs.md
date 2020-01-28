# Background jobs

The [Waste Carriers Back office](https://github.com/DEFRA/waste-carriers-back-office) contains 3 background jobs which are run on a nightly schedule. The schedules are set in [schedule.rb](https://github.com/DEFRA/waste-carriers-back-office/blob/master/config/schedule.rb).

The schedule is used to create and update [cron](https://en.wikipedia.org/wiki/Cron) jobs (the actual create and update occurs during deployment with [Capistrano](https://capistranorb.com/)). This is implemented using the [whenever gem](https://github.com/javan/whenever).

All background jobs are implemented as rake tasks, which are then called by the cron jobs we've created.

## Electronic Public Register (EPR) export

The [EPR](https://environment.data.gov.uk/public-register/view/search-waste-carriers-brokers) is an online search tool that users can use to find an 'ACTIVE' waste carrier, to validate they are registered.

It was originally maintained by a company called [Epimorphics](https://www.epimorphics.com/) but this changed to [Swirrl](https://www.swirrl.com/) in Oct 2018.

The EPR export produces a CSV file of all **ACTIVE** registrations. The job currently runs at 21:05 each day and takes approximately 5 minutes to complete.

### Transferring the data

The file is uploaded to shared `defra-reporting` AWS S3 bucket. That's as far as our responsibility goes. From there it's grabbed by **Swirrl** who handle updating the EPR with it.

## BOXI export

The BOXI export is used to export everything in the service. The export is made up of several files

- registrations.csv
- addresses.csv
- key_people.csv
- sign_offs.csv
- orders.csv
- order_items.csv
- payments.csv

The end result is that the data is loaded into a BOXI reporting universe to allow users to run reports. The job currently runs at 21:05 each day and takes approximately 5 minutes to complete.

### Transferring the data

The files are compressed into a single zip file, which is then uploaded to an AWS S3 bucket. That's as far as our responsibility goes. From there an ETL tool grabs the file and handles the import into the BOXI universe.

## Expired registrations

The expired registrations background job is used to identify and mark `EXPIRED` exemptions. Its filter is any exemptions with a status of **ACTIVE**, a tier of **UPPER**, and an expiry date less than or equal to `Time.now.in_time_zone("London").end_of_day`.

The job currently runs at 20:00 each day and takes less than a minute to complete.

### Early expiration

The team are aware that due to the timing and filter, this means we are expiring exemptions 4 hours earlier than they are due to expire. The current filter came from the old [waste-carriers-service](https://github.com/DEFRA/waste-carriers-service) project, and was beyond our scope to sort it as part of the tech debt work.
