# Adding new attributes to the POL integration

The PAFS-user application sends data via a message that sends a JSON document to POL.

If a new attribute is added to the PafsCore::Project model that is needed for POL, the [CAMC3 presenter](https://github.com/DEFRA/pafs_core/blob/develop/app/presenters/pafs_core/camc3_presenter.rb#L73) will need to be updated by adding the new attribute to the `attributes` method.

The associated spec should also be updated, along with the [JSON schema for the specs](https://github.com/DEFRA/pafs_core/blob/develop/doc/json_schemas/camc3.json).
