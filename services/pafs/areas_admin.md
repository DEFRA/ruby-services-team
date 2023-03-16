# Area administration

Current setup for new environments is documented here:
<https://github.com/DEFRA/pafs-user/blob/main/lib/fixtures/areas/areas.csv>

Currently there is no way to administer area information in the back office, so rake tasks have been created to do this.

## Adding New areas

To add new areas for the application, add a csv file to the `lib/fixtures/areas` folder in `pafs-user`

It must have the following headers

> name, parent area, type, sub type

Areas in PAFS can be one of four types:

1. RMA
2. PSO
3. EA Area
4. Country

RMAs will have a subtype of either LA or WC.

Unless the Area type is `Country`, the new areas should have a parent area that comes directly above them in the hierarachy, ie RMAs belong to PSOs, PSOs belong to EA Areas and EA Areas belong to Countries.

When the file is added, you can run a rake task `rails areas:import_new_areas` to import the areas into the database e.g.`areas:import_additional_areas[2023-02-13.csv]`

Note: there are jenkins jobs setup to run rake tasks in each environment. Specify the Rake task to run by setting RAKE_TASK_NAME to the fully qualified Rake task name in the Properties Content section.
e.g. `RAKE_TASK_NAME=areas:import_additional_areas[2023-02-13.csv]`

## Renaming areas

Again can be run by using a rake task e.g. `areas:rename_area[RMA,Milton Keynes Council,Milton Keynes City Council]"`

## Changing areas

Risk management authorities (RMA) sometimes change the Partnerships and Strategic Overview team (PSO).

To add new areas for the application, add a csv file to the `lib/fixtures` folder in `pafs-user, then a rake task can be run to update the RMA's PSO e.g.`areas:change_areas[lib/fixtures/cumbria_rma_changes.csv]`