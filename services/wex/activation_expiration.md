# Activation and Expiration

The wording and timings around when exactly an exemption is **ACTIVE** and when it is **EXPIRED** can be complex, and there may also be scenarios where what the record reflects and what the actual position is differs.

This page aims to provide clarification and guidance.

It's important to understand the time notation around [midnight](https://en.wikipedia.org/wiki/Midnight). This page uses [24 hour time](https://en.wikipedia.org/wiki/24-hour_clock) notation to try and be clear. Essentially 00:00 denotes the start of the specified date. 24:00 denotes the end of the specified date.

## Activation

For exemptions we ignore the time element of when an exemption is registered. So an exemption that is marked active on 22 Jan 2017 at 14:35 is officially active from 00:00 22 Jan 2017.

When we create the record we set

- `registered_on` as `current date`
- `expires_on` as `current date - (3.years - 1.days)`

So in our example

- `registered_on` will be 2017-01-22
- `expires_on` will be 2020-01-21

Users do not create a login when they registration an exemption, so confirmation of an email address is not required. Nor is there any payment. No additional background checks are carried out so as soon as the registration journey is complete, the registered exemptions are set as **ACTIVE**.

## Expiration

An exemption expires 3 years from the date of activation. So an exemption that is marked **ACTIVE** on 22 Jan 2015 at 14:35 is officially

- active from 00:00 22 Jan 2017
- expires at 24:00 21 Jan 2020
- is expired at 00:00 22 Jan 2020

We rely on a [background job](background_jobs.md) to actually mark exemptions as expired.

So based on current timings of the jobs you can track the state of an exemption using this example.

| Date        | Time  | Official status | Actual status | Activity                                |
|-------------|-------|-----------------|---------------|-----------------------------------------|
| 22 Jan 2017 | 14:35 | Active          | Active        | Complete the registration               |
| 21 Jan 2020 | 24:00 | Expired         | Active        |                                         |
| 22 Jan 2020 | 00:05 | Expired         | Expired       | Expire registrations background job run |
