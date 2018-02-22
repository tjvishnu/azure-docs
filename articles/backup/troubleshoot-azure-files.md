---
title: Troubleshoot Azure Files Backup 
description: This article is troubleshooting information about issues occurring when protecting your Azure Files (file shares) in Azure.
services: backup
ms.service: backup
keywords: Don’t add or edit keywords without consulting your SEO champ.
author: markgalioto
ms.author: markgal
ms.date: 2/21/2018
ms.topic: tutorial
ms.workload: storage-backup-recovery
manager: carmonm

---

# Troubleshoot problems backing up Azure Files

You can troubleshoot issues and errors encountered while using Azure Files backup with information listed in the following tables.

## Preview boundaries
Azure Files backup is in Preview. The following backup scenarios are not supported for Azure File shares:
- Protecting File shares in Storage Accounts with [zone-redundant storage](../storage/common/storage-redundancy.md#zone-redundant-storage) (ZRS) or [read-access geo-redundant storage](../storage/common/storage-redundancy.md#read-access-geo-redundant-storage) (RA-GRS) replication.
- Protecting File shares in Storage Accounts that have Virtual Networks enabled.
- Backing up Azure Files using PowerShell or CLI.

### Limits
- Maximum #Scheduled Backups per File Share in a day is 1.
- Maximum #On-Demand Backups per File Share in a day is 4.
-	Do not delete snapshots created by Azure Backup. This will lead to loss of Recovery Points and/or Restore failures.
-	Use resource locks on the Storage Account to prevent accidental deletion of Backups in your Recovery Services vault

## Configuring Backup
The following table is for configuring the backup.

| Configuring Backup | Workaround or Resolution tips |
| ------------------ | ----------------------------- |
| Could not find my Storage Account to configure backup for Azure File share | <ul><li>Wait until discovery is complete. <li>Check if any File share from the storage account is already protected with another Recovery Services vault. **Note**: All file shares in a Storage Account can be protected only under one Recovery Services vault. <li>Be sure the File share is not present in any of the unsupported Storage Accounts.|
| Error in portal states discovery of storage accounts failed. | If your subscription is partner (CSP-enabled), ignore the error. If your subscription is not CSP-enabled, and your storage accounts can't be discovered, contact support.|
| Selected Storage Account validation or registration failed.| Retry the operation, if the problem persists contact support.|
| Could not list or find File shares in the selected Storage Account. | <ul><li> Make sure the Storage Account exists in the Resource Group (and has not been deleted or moved after the last validation/registration in vault).<li>Make sure the File share you are looking to protect has not been deleted. <li>Make sure the Storage Account is a supported storage account for File share backup.<li>Check if the File share is already protected in the same Recovery Services vault.|
| Backup File share configuration (or the protection policy configuration) is failing. | <ul><li>Retry the operation to see if the issue persists. <li> Make sure the File share you want to protect has not been deleted. <li> If you are trying to protect multiple File shares at once, and some of the file shares are failing, retry configuring the backup for the failed File shares again. |
| Unable to delete the Recovery Services vault after unprotecting a File share. | In the Azure portal, open **Backup Infrastructure** > **Storage accounts** and click **Unregister** to remove the storage account from the Recovery Services vault.|


## Error messages for Backup or Restore Job failures

| Error messages | Workaround or Resolution tips |
| -------------- | ----------------------------- |
| Operation failed as the File share is not found. | Make sure the File share you are looking to protect has not been deleted.|
| Storage account not found or not supported. | <ul><li>Make sure the storage account exists in the Resource Group, and was not deleted or removed from the Resource Group after the last validation. <li> Make sure the Storage account is a supported Storage account for File share backup.|
| You have reached the max limit of snapshots for this file share, you will be able to take more once the older ones expire. | <ul><li> This error can occur when you create multiple on-demand backups for a File. <li> There is a limit of 200 snapshots per File share including the ones taken by Azure Backup. Older scheduled backups (or snapshots) are cleaned up automatically. On-demand backups (or snapshots) must be deleted if the maximum limit is reached.<li> Delete the on-demand backups (Azure File share snapshots) from the Azure Files portal. **Note**: You will lose the recovery points if you delete snapshots created by Azure Backup. |
| File share backup or restore failed due to storage service throttling. This may be because the storage service is busy processing other requests for the given storage account.| Retry the operation after some time. |
| Restore failed with Target File Share Not Found. | <ul><li>Make sure the selected Storage Account exists and the Target File share is not deleted. <li> Make sure the Storge Account is a supported storage account for File share backup. |
| Azure Backup is currently not supported for Azure Files in Storage Accounts with Virtual Networks enabled. | Disable Virtual Networks on your Storage Account to ensure successful backups or restore operations. |
| Backup or Restore jobs failed due to storage account being in Locked state. | Remove the lock on Storage Account or use delete lock insted of read lock and retry the operation. |
| Recovery failed because number of failed files are more than the threshold. | <ul><li> Recovery failure reasons are listed in a file (path provided in Job details). Please address the failures and retry the restore operation for the failed files only. <li> Common reasons for File restore failures: <br/> - make sure the files that failed are currently not in use, <br/> - a directory with the same name as the failed file exists in the parent directory. |
| Recovery failed as no file could be recovered. | <ul><li> Recovery failure reasons are listed in a file (path provided in Job details). Address the failures and retry the restore operations for the failed files only. <li> Common reasons for file restore failure: <br/> - Make sure the files that have failed are currently not in use. <br/> - A directory with the same name as the failed file exists in the parent directory. |
| Restore fails because one of the files in the source does not exist. | <ul><li> The selected items are not present in the recovery point data. To recover the files, provide the correct file list. <li> The file share snapshot that corresponds to the recovery point is manually deleted. Select a different recovery point and retry the restore operation. |
| A Recovery job is in process to the same destination. | <ul><li>File share backup does not support parallel recovery to the same target File share. <li>Wait for the existing recovery to finish and then try again. If you don't find a recovery job in the Recovery Services vault, check other Recovery Services vaults in the same subscription. |
| Restore operation failed as target file share is full. | Increase the target file share size quota to accomodate the restore data and retry the operation. |
| One or more files could not be recovered successfully. For more information, check the failed file list in the path given above. | <ul> <li> Recovery failure reasons are listed in the file (path provided in the Job details), address the reasons and retry the restore operation for the failed files only. <li> Common reasons for file restore failures are : <br/> - Make sure the failed files are not currently in use. <br/> - A directory with the same name as the failed files exists in the parent directory. |

## See Also
For additional information about backing up Azure File shares see:
- [Back up Azure File shares](backup-azure-files.md)
- [Back up Azure File share FAQ](backup-azure-files-faq.md)
