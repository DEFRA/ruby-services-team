# Renewal window

The registration for a waste exemption lasts 3 years and then expires. Before it expires users have a 1 month window in which they can renew.

> An establishment or undertaking can renew their registration at any time in the month prior to the renewal date (3 years from date of initial registration) up to the date that the existing registration becomes invalid.

<sub>[Environmental Permitting Guidance - Exempt Waste Operations (page 23 paragraph 6.24)](https://assets.publishing.service.gov.uk/government/uploads/system/uploads/attachment_data/file/69317/pb13631-ep2010exemptwaste.pdf)</sub>

Before the release of our renewal feature the service did not support 'renewing'. Users had to re-register using the same details. With the release users can create a new registration populated from the previous one. They can only do this if it's within the **renewal window**.

## Before expiry window

The guidance states users have 1 month to renew a registration before it expires. The service interprets this as 28 days. The team and business agreed on this decision because

- it simplifies the code
- it makes it consistent and fair for all users

That last point refers to the fact that a month can be anything between 28 and 31 days long. So when you register could determine if you have more or less time than other users to renew.

## After expiry window

This is the period of time after a registration expires that a user can still renew it. It is often referred to as the **grace** window. There is nothing in the guidance or regulations that states we should or will provide it. But research with users and experience with renewals on [Waste Carriers](/services/wcr/grace_window.md) has shown it's required.

The service interprets this as 30 days. The team and the business agreed this period.

## Example

An exemption that is made **ACTIVE** on 01 Aug 2016 at 14:35 is officially

- active from `00:00 01 Aug 2016`
- expires at `24:00 31 Jul 2019`
- is expired at `00:00 01 Aug 2020`

The renewal windows are

- can be renewed from `00:00 03 Jul 2019`
- can no longer be renewed from `00:00 31 Aug 2019`
