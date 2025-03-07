---
title: Create an external table using the Web UI Wizard in Azure Data Explorer
description: Use the one-click experience to create an external table.
author: orspod
ms.author: orspodek
ms.reviewer: ohbitton
ms.service: data-explorer
ms.topic: how-to
ms.date: 07/01/2021
---

# Create an external table using the Web UI wizard

An external table is a schema entity that references data stored outside the Azure Data Explorer database. Azure Data Explorer Web UI can create external tables by taking sample files from a storage container and creating schema based on these samples. You can then analyze and query data in external tables without ingestion into Azure Data Explorer. For information about different ways to create external tables, see [create and alter Azure Storage external tables](kusto/management/external-tables-azurestorage-azuredatalake.md).

This article shows you how to create an external table using the one-click experience.

## Prerequisites

* An Azure subscription. Create a [free Azure account](https://azure.microsoft.com/free/).
* Create [a cluster and database](create-cluster-database-portal.md).
* Sign in to the [Azure Data Explorer Web UI](https://dataexplorer.azure.com/) and [add a connection to your cluster](web-query-data.md#add-clusters).

## Create an external table

There are two ways to access the creation wizard:

* In the left menu of the Web UI, select **Data**. In the **Create external table** card, select **Create**.

     :::image type="content" source="media/external-table/data-blade.png" alt-text="Screenshot of data blade in Azure Data Explorer Web UI with create external table highlighted. .":::

* In the left blade of the Web UI, select **Query**. In the left menu, right-click on your database name and select **Create external table**.

    :::image type="content" source="media/external-table/access-wizard.png" alt-text="Screenshot of how to access the wizard to create an external table in the Azure Data Explorer WebUI.":::

The **Create external table** window opens with the **Destination** tab selected.

### Destination tab

1. The **Cluster** and **Database** fields are auto-populated. You may select a different destination from the drop-down menu.
1. In **Table name**, enter a name for your table.
    > [!TIP]
    >  Table names can be up to 1024 characters including alphanumeric, hyphens, and underscores. Special characters aren't supported.
1. Select **Next: Source**

:::image type="content" source="media/external-table/destination-tab.png" alt-text="Screen capture of the Destination tab with Cluster, Database, and Table name fields.":::

### Source tab

1. In **Link to source**, enter an [account key or SAS URL](kusto/api/connection-strings/storage.md#generate-a-sas-for-azure-storage-blob-container) to your source container with read and list permissions. You can add up to 10 sources. 
    The first source container will display files below the **File filters**. In a later step, you will use one of these files to generate the table schema.

    :::image type="content" source="media/external-table/source-tab.png" alt-text="Screen shot of create external table source tab in Azure Data Explorer.":::

1. Use **File filters** to filter the files that the table should include. Files can be filtered according to folder path, file begins with, or file extension.

    :::image type="content" source="media/external-table/schema-defining.png" alt-text="Screenshot of selecting schema-defining file.":::

1. Choose the schema-defining file by selecting the circle to the left of the file. This file will be used to generate the table schema.
1. Select **Next: schema**. The **Schema** tab opens.

### Schema tab

In the right-hand side of the tab, you can preview your data. On the left-hand side, you can add [partitions](kusto/management/partitioningpolicy.md) to your table definitions to access the source data more quickly and achieve better performance.

> [!NOTE] 
> Mappings are not part of the definition of an external table, and are not supported in this wizard. Mappings can be [configured later](kusto/management/external-tables-azurestorage-azuredatalake.md#create-external-table-mapping) if necessary. Some functionalities, such as deleting the last column in CSV files or changing column names in JSON files, require mappings in order to work correctly.

1. Select **Add partition**.

    :::image type="content" source="media/external-table/view-file.png" alt-text="Screen shot of view file for external table in Azure Data Explorer.":::

1. The partition window opens. A partition is defined over a subpath of the file, which can be altered using the **Path prefix** field. For each partition you wish to apply, fill out the fields as follows:

    Field | Description | Required/Optional
    ---|---|---
    Partition name | Used for identification of the partition. The name can be arbitrary. | Required
    Type | The data type of the data partitioning column. | Required
    Data column | The column used for partitioning. Virtual columns partition according to URL path. | Required
    Function | The function applied to the data column used for partitioning. | Optional
    Function argument | Argument to be used in the partition function. | Required if function is used.
    Path prefix | The subpath of the file on which the partitioning is defined. This prefix changes the URL of the external table, as seen in the **Uri preview** box, and should match the schema-defining file URI. | Optional
    Datetime pattern | Format of date that will be used to construct the table URI path. | Optional

    :::image type="content" source="media/external-table/add-partitions.png" alt-text="Screen shot add partitions to external table in Azure Data Explorer." lightbox="media/external-table/add-partitions.png":::

    For example, the partition name *CustomerName* suggests that the value to partition by is in the customer name part of the URL. The above example declared two partitions: one partition over the customer name and one partition over the date embedded in the URL. 

    > [!NOTE]
    > Virtual columns appear as part of the schema as the columns data extracted from the file path, and this data can be used later in queries.

1. Select **Add partition** to add another partition. 
1. Select **Save**. The partitions you added now appear in the list of **Partitions** in the left pane. Partitioned columns can't be changed in preview.
    
    :::image type="content" source="media/external-table/schema.png" alt-text="Screen shot of schema external table Azure Data Explorer.":::

1. Select **Next: Create table**. When the table is created, an **External table successfully created** window opens.
1. To view the command used to create the table, select **View command**.

    :::image type="content" source="media/external-table/successfully-created.png" alt-text="Screen shot of successful creation of external table in Azure Data Explorer.":::
1. To undo the creation of the external table, select **Tools** > **Undo**.

## Query the external table

The resulting table includes data from all the files that fit the criteria defined above. You can query this table using the `external_table()` function. For more information on how to query external tables, see [Querying an external table](data-lake-query-data.md#querying-an-external-table).

:::image type="content" source="media/external-table/view-table.png" alt-text="Screen shot of table output from querying external table in Azure Data Explorer.":::

## Next steps

* [Create and alter Azure Storage external tables](kusto/management/external-tables-azurestorage-azuredatalake.md)
* [External tables overview](kusto/query/schema-entities/externaltables.md)
* [external_table() function](kusto/query/externaltablefunction.md)
* [Write queries for Azure Data Explorer](write-queries.md)
