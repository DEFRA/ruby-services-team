# Grace window

Upper tier waste carrier registrations incur a charge to register them, and expire every 3 years. However before they expire users have the opportunity to renew them for a lesser charge.

What this page describes is the 'grace window', the period during which a registration is 'expired' but users can still start the renewal process.

## Current window

The current window is *5 days* after the registration has expired (see [Activation and Expiration](activation_expiration.md) for specifics on when a registration expires).

For example if the a registration expires on 1 Oct 2018, then the registration

- was ACTIVE on Sept 30, and EXPIRED on Oct 1
- can still be renewed from Oct 1 to Oct 5 at the latest

There could be some confusion because 1 + 5 = 6, but it's important to remember that a registration is expired on its expiry date. Hence the 5 day grace window includes Oct 1.

## Configuration

The value is managed via an environment variable, so should it need to change it should just be a case of updating the env var `WCRS_REGISTRATION_GRACE_WINDOW`

## COVID-19 extension

In April 2020, grace window rules were temporarily modified to deal with disruption from COVID-19.

Instead of 5 days, users were given 90 days to renew. In July, this was extended to 180 days.

The extension was also applied to the public register – registrations which would otherwise have expired had this grace period added on to their displayed expiry date.

In October 2020, this extension was rolled back. Any registrations which had previously received it would still have it, but any registrations expiring after 27 October would return to the 5-day grace window, with no extension to their displayed expiry date on the public register.
