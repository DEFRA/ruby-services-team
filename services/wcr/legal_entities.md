# Legal entity names and Business names

The users of the service - carriers, brokers and dealers, hereafter referred to as "entities" - can have either or both a legal entity name or an informal business/trading name. At least one of these must be present in all cases. The combinations allowed depend on the business type of the entity and the tier of the registration. In some cases, fields in the database may be populated or nil depending on whether the registration was created before or after the deployment of new legal entities logic (ca. April 2022).

## Business name vs Company name
The term "Business name" was chosen by the wider Ruby Services team after considering alternatives such as "Trading name". Strictly speaking this should be reflected in the code by renaming the existing `company_name` field to `business_name`. However such a change would have significant and potentially unknown downstream impacts. In particular, there are downstream reporting systems and processes which use the `company_name` column and the impact on these of changing the column name are not known. As a result, it was decided to retain the existing name and accept the anomaly between "Business name" and `company_name`.

## legal_entity_name
The `legal_entity_name` of an entity is the formally registered name of the entity, if any. The business logic for `legal_entity_name` varies according to the business type, tier and the date of the registration.
- Limited Companies and Limited Liability Partnerships:
  - Upper-tier: `legal_entity_name` is the registered company name retrieved from Companies House during the registration, e.g. "Applied Waste Solutions Limited". Note that this will not be populated for registrations created before the deployment of the legal entities changes.
  - Lower-tier: Currently nil, as companies house details are not retrieved for lower-tier registrations.
- Sole Traders:
  - Upper-tier: `legal_entity_name` is the first name and last name of the key person for the registration, e.g. "Jim Smith". This is always populated.
  - Lower-tier: Currently nil, as the key person name is not stored for lower-tier sole traders.
- Other:
  - `legal_entity_name` is nil in all cases.

## company_name
The `company_name` of an entity is an informal trading name provided by the user creating the registration and not subject to validation. Again the business logic varies according to the business type, tier and date of the registration:
- Limited Companies and Limited Liability Partnerships:
  - Upper-tier: The user may add an optional `company_name` during registration, e.g. "We Are Waste". Prior to the deployment of the legal entities changes this field was mandatory.
  - Lower-tier: `company_name` is mandatory, as the companies house name is not stored for lower-tier companies.
- Sole Traders:
  - Upper-tier: The user may add an optional `company_name` during registration, e.g. "Jim's Skips". Prior to the deployment of the legal entities changes this field was mandatory.
  - Lower-tier: `company_name` is mandatory, as the key person name is not stored for lower-tier sole traders.
- Other:
  - `company_name` is mandatory in all cases.

## entity_display_name
An entity's `entity_display_name` is a combination of `legal_entity_name` and `company_name`. If only one of `legal_entity_name` or `company_name` is populated, the `entity_display_name` will consist of the value of that field. If both are populated, the `entity_display_name` will comprise the value of `legal_entity_name` plus "trading as" plus the value of `company_name`.
Examples:

| Business Type | `registered_company_name` | `company_name` | Key Person Name | `entity_display_name`                 |
|---------------|---------------------------|----------------|-----------------|---------------------------------------|
| LTD or LLP    | ABC Waste Ltd             |                |                 | ABC Waste Ltd                         |
| LTD or LLP    |                           | We are Waste   |                 | We are Waste                          |
| LTD or LLP    | ABC Waste Ltd             | We are Waste   |                 | ABC Waste Ltd trading as We are Waste |
| Sole Trader   |                           |                | Jim Smith       | Jim Smith                             |
| Sole Trader   |                           | Jim's Skips    |                 | Jim's Skips                           |
| Sole Trader   |                           |                | Jim Smith       | Jim Smith                             |
| Sole Trader   |                           |                | Jim Smith       | Jim Smith trading as Jim's Skips      |
| Other         |                           | Ace Waste      |                 | Ace Waste                             |

#### De-duplication of "trading as" detail
Prior to the deployment of the legal entities logic, some entities implemented similar presentation logic manually be entering a `company_name` including "trading as" or "t/a" between two names. Based on the logic above, this could result in anomalous entity_display_names such as:
- "ABC Waste Ltd trading as ABC Waste Ltd trading as We are Waste"
- "XYZ Limited trading as Global Waste Displosal Limited trading as Global Disposal"
In order to avoid these anomalies, and to enforce the use of the correct `legal_entity_name` in the `entity_display_name`, everything up to and including "trading as " or "t/a " in the `company_name` is omitted when forming the `entity_display_name`. The above examples will therefore appear as:
- "ABC Waste Ltd trading as We are Waste"
- "XYZ Limited trading as Global Disposal"

## Code considerations
The logic above is contained in two model concerns:
1. can_have_registration_attributes.rb
  This defines the `legal_entity_name` method, implementing the logic described above in [legal_entity_name](#Legal-entity-name).
  The unit tests for this are in a shared_example "Can have registration attributes". This is included in the relevant model spec files, `registration_spec.rb` and `transient_registration_spec.rb`.
1. can_present_entity_display_name.rb
  This defines the `entity_display_name` method, implementing the logic described above in [entity_display_name](#Presentation-name).
  The unit tests for this are in a shared_example "Can present entity display name". This is included in the relevant model spec files, `registration_spec.rb` and `transient_registration_spec.rb`.
