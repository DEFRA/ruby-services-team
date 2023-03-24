# Area administration

Current setup for new environments is documented here:
<https://github.com/DEFRA/pafs-user/blob/main/lib/fixtures/areas/areas.csv>

Currently there is no way to administer area information in the back office, so rake tasks have been created to do this.

## Adding New areas

To add new areas for the application, add a csv file to the `lib/fixtures/areas` folder in `pafs-user`

It must have the following headers

> name, parent area, type, sub type

Areas in PAFS can be one of four types:

1. Country
2. EA Area
3. PSO
4. RMA

All area types other than Country have a parent area. The hierarchy is:
* EA Areas belong to Countries
* PSOs belong to EA Areas
* RMAs belong to PSOs

RMAs have a subtype which is one of:
* Highways England (HE) 
* Internal Drainage Board (IDB)
* Lead Local Flood Authority (LLFA)
* Local authority (LA)
* Water company (WC) .

When the file is added, you can run a rake task `rails areas:import_new_areas` to import the areas into the database e.g.
```
areas:import_additional_areas[2023-02-13.csv]
```

Note: There are Jenkins jobs in each enviroment for running Rake tasks. Specify the Rake task to run by setting `RAKE_TASK_NAME` to the fully qualified Rake task name in the "Properties Content" section, e.g. 
```
RAKE_TASK_NAME=areas:import_additional_areas[2023-02-13.csv]
```

## Renaming areas

Use the `rename_area` task to rename an exising area, e.g. 
```
areas:rename_area[RMA,Milton Keynes Council,Milton Keynes City Council]"
```

## Changing areas

Risk management authorities (RMA) sometimes change the Partnerships and Strategic Overview team (PSO).

To add new areas for the application, add a csv file to the `lib/fixtures` folder in `pafs-user`, then a rake task can be run to update the RMA's PSO e.g.
```
areas:change_areas[lib/fixtures/cumbria_rma_changes.csv]
```