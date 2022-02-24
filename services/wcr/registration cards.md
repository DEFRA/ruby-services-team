# Registration cards

Registration cards, also known as copy cards are an optional extra that can be ordered at the time of registration, renewal or on an ad-hoc basis. They are wallet-sized cards that can be used to show details of the registration.

## Registration card exports

All registration card orders are sent to a third party company for printing. This is done weekly and requires an export of the previous week's orders.

Only registrations and renewals that have registration card orders and are active are added to the order list. So outstanding payments or convictions checks will delay the order until they are processed.

Each export file contains the details of all card orders for which the associated registration or renewal became active during the relevant week, starting at 00:00:00 on Tuesday and finishing at 23:59:59 on the following Monday.

Export files are generated automatically at 02:15 on the Tuesday after the end of the relevant week.

Export files are written to a designated AWS S3 bucket.

Export files contain one row per card ordered.

Once the export has been generated, it's available in the back office in the menu as 'Copy card orders' for users with Agency with Refund, Agency super user and developer roles.
This can then be downloaded to be processed and sent to the printer.
