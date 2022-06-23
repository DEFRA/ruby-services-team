#Adding New areas

To add new areas for the application, add a csv file to the ` lib/fixtures/new_areas` folder in `pafs-user`

It must have the following headers

> name, parent area, type, sub type

Areas in PAFS can be one of four types:

1. RMA
2. PSO
3. EA Area
4. Country

RMAs will have a subtype of either LA or WC.

Unless the Area type is `Country`, the new areas should have a parent area that comes directly above them in the hierarachy, ie RMAs belong to PSOs, PSOs belong to EA Areas and EA Areas belong to Countries.

When the file is added, you can run `rails areas:import_new_areas` to import the areas locally.

To import the new areas on the development, training or production environments there is a Jenkins task called `ADD_NEW_AREAS`
