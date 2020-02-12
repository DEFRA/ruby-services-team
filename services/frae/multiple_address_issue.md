# Multiple organisation addresses

There is a [known issue](https://eaflood.atlassian.net/browse/RIP-359) with the service which causes multiple address records to be created, all linked to the same organisation and all listed as `primary`.

We can replicate the issue by completing an application as far as the **Check details** screen. Then if we use the back link to go back to a step prior to the organisation address page, make a change, and click next until we have finished we find we have 2 address records. In fact every time you do this it leads to another address record, so theoretically you could have loads (we went as far as 3)!

What should be happening is the existing address is updated, so that there is only one when you come to submit the application.

## Duplicate results in search

You can see this exposed when searching for an exemption which has been effected in the back office. For each extra primary organisation address that exists, you will see a separate search result. It gives the impression that the data is duplicated. In fact it's because the underyling query is expecting only one address record returned, so it's forced to duplicate the data in the results.

## Data

```sql
/*
  addressable_id is a polymorphic relation to the object linked to the address
  record. In this case it's the `flood_risk_engine_organisations.id` of an
  effected record
*/
SELECT * FROM flood_risk_engine_addresses WHERE addressable_id = 6419
```

When the `flood_risk_engine_addresses` is queried you'll see 2 results

|id|premises|street_address|locality|city|postcode|address_type|addressable_id|addressable_type|
|--|--------|--------------|--------|----|--------|------------|--------------|----------------|
|12585|`null`|`null`|`null`|`null`|`null`|0|6419|FloodRiskEngine::Organisation|
|12586|Turtle House|1 Acacia Avenue|Spitoon|Eastbury|BS1 5AH|0|6419|FloodRiskEngine::Organisation|

## Production VS Non-production

We seem to 'get away' with this issue in production because when we ask for the primary address on an `Organisation` instance it returns us the one that is populated (some Googling seems to suggest that the order something is inserted effects the order results are returned. But this is either refuted or understandably not advised to be relied upon).

If we take this data and restore it to a non-production environment however the ordering is effected. We believe it's because we use [pg_dump](https://www.postgresql.org/docs/9.6/app-pgdump.html) and [pg_restore](https://www.postgresql.org/docs/9.6/app-pgrestore.html). Again tracking down specifics is hard but [this stackoverflow post](https://stackoverflow.com/a/2179304) suggests its because `pg_dump` writes its statements dependent on how it reads the data based on disk order. You cannot alter this order, and it means the restored database ***will*** return results differently to production.

This has been observed where effected exemptions now appear to have missing organisation addresses.

## Why here?

Anyone trying to work with the data needs to be aware of this bug to ensure they are not making decisions based on bad data.

Mainly it is a reminder to ourselves (until the issue is resolved) to not panic if when attempting to compare something with what we see in production that there may well be differences.
