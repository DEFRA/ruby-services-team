# Secure token

When you start a new registration, or start to renew an existing one, a token is created. We use this token to identify the in-progress `transient_registration`. From a design point of view, this delivers a number of benefits to the user (as long as they retain the link):

- they are not tied to their current session. They can close the browser and return to the journey at a later date
- we avoid needing to setup an account
- we are not adding an additional cookie to their session to track the registration

For example, once a user is past the first page of the new registration journey their browser will show something like this for the url

> https://wasteexemptions.service.gov.uk/location/3zaSBUDkBQzhu8s5xd7Phoow

## Security

Because we're identifying the in-progress registration from a url parameter and not something stored in the cookie, we needed to make sure it's sufficiently complex to guess, and unique enough to avoid duplication.

The token is generated randomly using [base58](https://api.rubyonrails.org/classes/SecureRandom.html). It is 24 characters from a `{0-9a-zA-Z}` character set (minus `0, O, I and l`). For example, `RA7pzGw7b52M1eaJGr7h2emR`. The total number of unique tokens we can generate is

> 58^24 = 2.1002541e+42

The chance of guessing a token is 1 over 58^24

> 1/58^24 = 4.7613286e-43

For comparison: there are approximately 10^80  atoms in the Universe!

## Uniqueness

Even though it is highly unlikely a collision will occur between tokens in the database, as an extra measure both `token` and `renew_token` have a unique index constraint in the database.

## Implementation

We use the [HasSecureToken gem](https://github.com/robertomiranda/has_secure_token) to generate the token.

This is a backport of `ActiveRecord::SecureToken` 5 to AR 3.x and 4.x, and we are aware it has been some time since any changes have been made to the repository.

We should be able to drop the gem and use built in ActiveRecord support once we [upgrade to Rails 5].(https://github.com/DEFRA/ruby-services-team/issues/31).
