# Storage

## Blob storage

### Access control

Azure portal uses "Access key" authentication method by default. This means storage account key is used for authentication. Authentication method can be switched to Azure AD user account from blob container overview page.

### Syncing objects from AWS S3 bucket to blob container

Objects can be synced from AWS S3 bucket to Azure blob container using AzCopy. For example:

```bash
azcopy cp "https://$BUCKET_NAME.s3.$AWS_REGION.amazonaws.com" "https://$STORAGE_ACCOUNT_NAME.blob.core.windows.net/$CONTAINER_NAME" --recursive
```

It seems that AzCopy can only read AWS credentials from `AWS_ACCESS_KEY_ID` and `AWS_SECRET_ACCESS_KEY` environment variables. So, those need to be exported. If session credentials are used, `AWS_SESSION_TOKEN` needs to be also exported.

On the Azure side, the data layer has in a sense its own access policies. Even though, an user has Owner role in subscription, blob data level roles might need to be added.
