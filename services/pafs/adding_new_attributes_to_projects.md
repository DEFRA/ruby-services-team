# Adding new attributes to the POL integration

The PAFS-user application sends project data to a downstream system POL (Project OnLine) via a JSON REST API. The endpoint and credentials for this API are held in the following environment variables:
- AZURE_CLIENT_ID
- AZURE_CLIENT_SECRET
- AZURE_OAUTH_URL
- AZURE_VAULT_AUTH_TOKEN_KEY_NAME_RECEIVE
- AZURE_VAULT_AUTH_TOKEN_KEY_NAME_SUBMISSION (note that this needs to be updated when the version of the Azure app changes)
- AZURE_VAULT_HOST
- POL_ARCHIVE_URL
- POL_SUBMISSION_URL

If a new attribute is added to the PafsCore::Project model that is needed for POL:
1. Add the attribute to the `attributes` method of the [CAMC3 presenter](https://github.com/DEFRA/pafs_core/blob/develop/app/presenters/pafs_core/camc3_presenter.rb#L73) and update the associated spec.
2. Update the [JSON schema for the POL feed](https://github.com/DEFRA/pafs_core/blob/develop/doc/json_schemas/camc3.json).
