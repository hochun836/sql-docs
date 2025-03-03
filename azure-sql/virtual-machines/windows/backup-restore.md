---
title: Backup and restore for SQL Server on Azure VMs
description: Describes backup and restore considerations for SQL Server databases running on Azure Virtual Machines.
author: adbadram
ms.author: adbadram
ms.reviewer: mathoma
ms.date: 04/18/2023
ms.service: virtual-machines-sql
ms.subservice: backup
ms.topic: conceptual
tags: azure-resource-management
---
# Backup and restore for SQL Server on Azure VMs
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

This article provides guidance on the backup and restore options available for SQL Server running on a Windows virtual machine (VM) in Azure. Azure Storage maintains three copies of every Azure VM disk to guarantee protection against data loss or physical data corruption. Thus, unlike SQL Server on-premises, you don't need to focus on hardware failures. However, you should still back up your SQL Server databases to protect against application or user errors, such as inadvertent data insertions or deletions. In this situation, it is important to be able to restore to a specific point in time.

The first part of this article provides an overview of the available backup and restore options. This is followed by sections that provide more information on each strategy.

## Backup and restore options

The following table provides information on various backup and restore options for SQL Server on Azure VMs:

| Strategy | SQL versions | Description |
|---|---|---|
| [Automated Backup](#automated) | 2014 and later | Automated Backup allows you to schedule regular backups for all databases on a SQL Server VM. Backups are stored in Azure storage for up to 30 days. Beginning with SQL Server 2016, Automated Backup v2 offers additional options such as configuring manual scheduling and the frequency of full and log backups. |
| [Azure Backup for SQL VMs](#azbackup) | 2008 and later | Azure Backup provides an Enterprise class backup capability for SQL Server on Azure VMs. With this service, you can centrally manage backups for multiple servers and thousands of databases. Databases can be restored to a specific point in time in the portal. It offers a customizable retention policy that can maintain backups for years. |
| [Manual backup](#manual) | All | Depending on your version of SQL Server, there are various techniques to manually backup and restore SQL Server on Azure VM. In this scenario, you are responsible for how your databases are backed up and the storage location and management of these backups. |

The following sections describe each option in more detail. The final section of this article provides a summary in the form of a feature matrix.

## <a id="automated"></a> Automated Backup

Automated Backup provides an automatic backup service for SQL Server Standard and Enterprise editions running on a Windows VM in Azure. This service is provided by the [SQL Server IaaS Agent Extension](sql-server-iaas-agent-extension-automate-management.md), which is automatically installed on SQL Server Windows virtual machine images in the Azure portal.

All databases are backed up to an Azure storage account that you configure. Backups can be encrypted and retained for up to 90 days.

SQL Server 2016 and higher VMs offer more customization options with Automated Backup v2. These improvements include:

- System database backups
- Manual backup schedule and time window
- Full and log file backup frequency

To restore a database, you must locate the required backup file(s) in the storage account and perform a restore on your SQL VM using SQL Server Management Studio (SSMS) or Transact-SQL commands.

For more information on how to configure Automated Backup for SQL VMs, see one of the following articles:

- **SQL Server 2016 and later**: [Automated Backup v2 for Azure Virtual Machines](automated-backup.md)
- **SQL Server 2014**: [Automated Backup for SQL Server 2014 Virtual Machines](automated-backup-sql-2014.md)

## <a id="azbackup"></a> Azure Backup for SQL VMs

[Azure Backup](/azure/backup/index) provides an Enterprise class backup capability for SQL Server on Azure VMs. All backups are stored and managed in a Recovery Services vault. There are several advantages that this solution provides, especially for Enterprises:

- **Zero-infrastructure backup**: You do not have to manage backup servers or storage locations.
- **Scale**: Protect many SQL VMs and thousands of databases.
- **Pay-As-You-Go**: This capability is a separate service provided by Azure Backup, but as with all Azure services, you only pay for what you use.
- **Central management and monitoring**: Centrally manage all of your backups, including other workloads that Azure Backup supports, from a single dashboard in Azure.
- **Policy driven backup and retention**: Create standard backup policies for regular backups. Establish retention policies to maintain backups for years.
- **Support for SQL Always On**: Detect and protect a SQL Server Always On configuration and honor the backup Availability Group backup preference.
- **15-minute Recovery Point Objective (RPO)**: Configure SQL transaction log backups up to every 15 minutes.
- **Point in time restore**: Use the portal to recover databases to a specific point in time without having to manually restore multiple full, differential, and log backups.
- **Consolidated email alerts for failures**: Configure consolidated email notifications for any failures.
- **Azure role-based access control**: Determine who can manage backup and restore operations through the portal.

This Azure Backup solution for SQL VMs is generally available. For more information, see [Back up SQL Server database to Azure](/azure/backup/backup-azure-sql-database).

## <a id="manual"></a> Manual backup

If you want to manually manage backup and restore operations on your SQL VMs, there are several options depending on the version of SQL Server you are using. For an overview of backup and restore, see one of the following articles based on your version of SQL Server:

- [Backup and restore for SQL Server 2016 and later](/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases)
- [Backup and restore for SQL Server 2014](/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases?viewFallbackFrom=sql-server-2014)
- [Backup and restore for SQL Server 2012](/previous-versions/sql/sql-server-2012/ms187048(v=sql.110))
- [Backup and restore for SQL Server SQL Server 2008 R2](/previous-versions/sql/sql-server-2008-r2/ms187048(v=sql.105))
- [Backup and restore for SQL Server 2008](/previous-versions/sql/sql-server-2008/ms187048(v=sql.100))

The following sections describe several manual backup and restore options in more detail.

### Backup to attached disks

For SQL Server on Azure VMs, you can use native backup and restore techniques using attached disks on the VM for the destination of the backup files. However, there is a limit to the number of disks you can attach to an Azure virtual machine, based on the [size of the virtual machine](/azure/virtual-machines/sizes?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). There is also the overhead of disk management to consider.

For an example of how to manually create a full database backup using SQL Server Management Studio (SSMS) or Transact-SQL, see [Create a Full Database Backup](/sql/relational-databases/backup-restore/create-a-full-database-backup-sql-server).

### Backup to URL

Beginning with SQL Server 2012 SP1 CU2, you can back up and restore directly to Microsoft Azure Blob storage, which is also known as backup to URL. SQL Server 2016 also introduced the following enhancements for this feature:

| 2016 enhancement | Details |
| --- | --- |
| **Striping** |When backing up to Microsoft Azure Blob Storage, SQL Server 2016 supports backing up to multiple blobs to enable backing up large databases, up to a maximum of 12.8 TB. |
| **Snapshot Backup** |Through the use of Azure snapshots, SQL Server File-Snapshot Backup provides nearly instantaneous backups and rapid restores for database files stored using Azure Blob Storage. This capability enables you to simplify your backup and restore policies. File-snapshot backup also supports point in time restore. For more information, see [Snapshot Backups for Database Files in Azure](/sql/relational-databases/backup-restore/file-snapshot-backups-for-database-files-in-azure). |

For more information, see the one of the following articles based on your version of SQL Server:

- **SQL Server 2016 and later**: [SQL Server Backup to URL](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service)
- **SQL Server 2014**: [SQL Server 2014 Backup to URL](/previous-versions/sql/2014/relational-databases/backup-restore/sql-server-backup-to-url)
- **SQL Server 2012**: [SQL Server 2012 Backup to URL](/previous-versions/sql/sql-server-2012/jj919148(v=sql.110))

### Managed Backup

Beginning with SQL Server 2014, Managed Backup automates the creation of backups to Azure storage. Behind the scenes, Managed Backup makes use of the Backup to URL feature described in the previous section of this article. Managed Backup is also the underlying feature that supports the SQL Server VM Automated Backup service.

Beginning in SQL Server 2016, Managed Backup got additional options for scheduling, system database backup, and full and log backup frequency.

For more information, see one of the following articles based on your version of SQL Server:

- [Managed Backup to Microsoft Azure for SQL Server 2016 and later](/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure)
- [Managed Backup to Microsoft Azure for SQL Server 2014](/previous-versions/sql/2014/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure)

## Decision matrix

The following table summarizes the capabilities of each backup and restore option for SQL Server virtual machines in Azure.

| Option | Automated Backup | Azure Backup for SQL | Manual backup |
|---|---|---|---|
| Requires additional Azure service |   | :::image type="content" source="../../media/applies-to/yes-icon.svg" alt-text="Azure Backup for SQL." border="false"::: |   |
| Configure backup policy in Azure portal | :::image type="content" source="../../media/applies-to/yes-icon.svg" alt-text="Automated Backup." border="false"::: | :::image type="content" source="../../media/applies-to/yes-icon.svg" alt-text="Azure Backup for SQL." border="false"::: |   |
| Restore databases in Azure portal |   | :::image type="content" source="../../media/applies-to/yes-icon.svg" alt-text="Azure Backup for SQL." border="false"::: |   |
| Manage multiple servers in one dashboard |   | :::image type="content" source="../../media/applies-to/yes-icon.svg" alt-text="Azure Backup for SQL." border="false"::: |   |
| Point-in-time restore | :::image type="content" source="../../media/applies-to/yes-icon.svg" alt-text="Automated Backup." border="false"::: | :::image type="content" source="../../media/applies-to/yes-icon.svg" alt-text="Azure Backup for SQL." border="false"::: | :::image type="content" source="../../media/applies-to/yes-icon.svg" alt-text="Manual backup." border="false"::: |
| 15-minute Recovery Point Objective (RPO) | :::image type="content" source="../../media/applies-to/yes-icon.svg" alt-text="Automated Backup." border="false"::: | :::image type="content" source="../../media/applies-to/yes-icon.svg" alt-text="Azure Backup for SQL." border="false"::: | :::image type="content" source="../../media/applies-to/yes-icon.svg" alt-text="Manual backup." border="false"::: |
| Short-term backup retention policy (days) | :::image type="content" source="../../media/applies-to/yes-icon.svg" alt-text="Automated Backup." border="false"::: | :::image type="content" source="../../media/applies-to/yes-icon.svg" alt-text="Azure Backup for SQL." border="false":::  |   |
| Long-term backup retention policy (months, years) |   | :::image type="content" source="../../media/applies-to/yes-icon.svg" alt-text="Azure Backup for SQL." border="false"::: |   |
| Built-in support for SQL Server Always On |   | :::image type="content" source="../../media/applies-to/yes-icon.svg" alt-text="Azure Backup for SQL." border="false"::: |   |
| Backup to Azure Storage account(s) | :::image type="content" source="../../media/applies-to/yes-icon.svg" alt-text="Automated Backup." border="false":::(automatic) | :::image type="content" source="../../media/applies-to/yes-icon.svg" alt-text="Azure Backup for SQL." border="false":::(automatic) | :::image type="content" source="../../media/applies-to/yes-icon.svg" alt-text="Manual backup." border="false"::: (customer managed) |
| Management of storage and backup files |   | :::image type="content" source="../../media/applies-to/yes-icon.svg" alt-text="Azure Backup for SQL." border="false"::: |   |
| Backup to attached disks on the VM |   |   | :::image type="content" source="../../media/applies-to/yes-icon.svg" alt-text="Manual backup." border="false"::: |
| Central customizable backup reports |   | :::image type="content" source="../../media/applies-to/yes-icon.svg" alt-text="Azure Backup for SQL." border="false"::: |   |
| Consolidated email alerts for failures |   | :::image type="content" source="../../media/applies-to/yes-icon.svg" alt-text="Azure Backup for SQL." border="false"::: |   |
| Customize monitoring based on Azure Monitor logs |   | :::image type="content" source="../../media/applies-to/yes-icon.svg" alt-text="Azure Backup for SQL." border="false"::: |   |
| Monitor backup jobs with SSMS or Transact-SQL scripts | :::image type="content" source="../../media/applies-to/yes-icon.svg" alt-text="Automated Backup." border="false"::: | :::image type="content" source="../../media/applies-to/yes-icon.svg" alt-text="Azure Backup for SQL." border="false"::: | :::image type="content" source="../../media/applies-to/yes-icon.svg" alt-text="Manual backup." border="false"::: |
| Restore databases with SSMS or Transact-SQL scripts | :::image type="content" source="../../media/applies-to/yes-icon.svg" alt-text="Automated Backup." border="false"::: |   | :::image type="content" source="../../media/applies-to/yes-icon.svg" alt-text="Manual backup." border="false"::: |

## Next steps

If you are planning your deployment of SQL Server on Azure VM, you can find provisioning guidance in the following guide: [How to provision a Windows SQL Server virtual machine in the Azure portal](create-sql-vm-portal.md).

Although backup and restore can be used to migrate your data, there are potentially easier data migration paths to SQL Server on VM. For a full discussion of migration options and recommendations, see [Migration guide: SQL Server to SQL Server on Azure Virtual Machines](../../migration-guides/virtual-machines/sql-server-to-sql-on-azure-vm-individual-databases-guide.md).