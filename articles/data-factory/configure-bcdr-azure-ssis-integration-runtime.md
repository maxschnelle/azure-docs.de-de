---
title: Konfigurieren der Azure-SSIS Integration Runtime für Failover der SQL-Datenbank
description: In diesem Artikel wird beschrieben, wie die Azure-SSIS Integration Runtime mit Georeplikation für die Azure SQL-Datenbank und Failover für die SSISDB-Datenbank konfiguriert wird.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.devlang: powershell
author: swinarko
ms.author: sawinark
manager: mflasko
ms.reviewer: douglasl
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/09/2020
ms.openlocfilehash: 761841c1f2146a33b35cdddc4adc4d3eb1a4b139
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92635285"
---
# <a name="configure-the-azure-ssis-integration-runtime-with-sql-database-geo-replication-and-failover"></a>Konfigurieren der Azure-SSIS Integration Runtime mit Georeplikation und Failover der Azure SQL-Datenbank

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

In diesem Artikel wird beschrieben, wie die Azure-SSIS Integration Runtime (IR) mit Georeplikation für die Azure SQL-Datenbank für die SSISDB-Datenbank konfiguriert wird. Wenn ein Failover auftritt, können Sie sicherstellen, dass Azure-SSIS IR mit der sekundären Datenbank weiterhin funktioniert.

Weitere Informationen zu Georeplikation und Failover für die SQL-Datenbank finden Sie unter [Übersicht: Aktive Georeplikation und Gruppen für automatisches Failover](../azure-sql/database/auto-failover-group-overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-ssis-ir-failover-with-a-sql-managed-instance"></a>Azure-SSIS IR-Failover bei einer verwalteten SQL-Instanz

### <a name="prerequisites"></a>Voraussetzungen

Eine verwaltete Azure SQL-Instanz verwendet einen *Datenbankhauptschlüssel (Database Master Key, DMK)* zum Sichern von Daten, Anmeldeinformationen und Verbindungsinformationen, die in einer Datenbank gespeichert sind. Zur Aktivierung der automatischen Entschlüsselung des DMK wird eine Kopie des Schlüssels mit dem *Diensthauptschlüssel (Server Master Key, SMK)* verschlüsselt. 

Der SMK wird in einer Failovergruppe nicht repliziert. Sie müssen nach einem Failover ein Kennwort sowohl für die primäre als auch die sekundäre Instanz zur DMK-Entschlüsselung hinzufügen.

1. Führen Sie den folgenden Befehl für SSISDB auf der primären Instanz aus. In diesem Schritt wird ein neues Verschlüsselungskennwort hinzugefügt.

    ```sql
    ALTER MASTER KEY ADD ENCRYPTION BY PASSWORD = 'password'
    ```

2. Erstellen Sie eine Failovergruppe in einer verwaltete SQL-Instanz.

3. Führen Sie **sp_control_dbmasterkey_password** auf der sekundären Instanz aus, und verwenden Sie dazu das neue Verschlüsselungskennwort.

    ```sql
    EXEC sp_control_dbmasterkey_password @db_name = N'SSISDB',   
        @password = N'<password>', @action = N'add';  
    GO
    ```

### <a name="scenario-1-azure-ssis-ir-is-pointing-to-a-readwrite-listener-endpoint"></a>Szenario 1: Azure-SSIS IR verweist auf einen Listenerendpunkt mit Lese-/Schreibzugriff

Wenn die Azure-SSIS IR auf den Listenerendpunkt mit Lese-/Schreibzugriff verweisen soll, müssen Sie zuerst auf den primären Serverendpunkt verweisen. Nachdem Sie SSISDB in eine Failovergruppe eingefügt haben, können Sie zum Listenerendpunkt mit Lese-/Schreibzugriff wechseln und die Azure-SSIS IR neu starten.

#### <a name="solution"></a>Lösung

Wenn ein Failover auftritt, führen Sie die folgenden Schritte aus:

1. Beenden Sie die Azure-SSIS IR in der primären Region.

2. Bearbeiten Sie die Azure-SSIS IR mit URI-Informationen zu der neuen Region, dem virtuellen Netzwerk und Shared Access Signature (SAS) für benutzerdefiniertes Setup auf der sekundären Instanz. Weil die Azure-SSIS IR auf einen Listener mit Lese-/Schreibzugriff verweist und der Endpunkt für die Azure-SSIS IR transparent ist, müssen Sie den Endpunkt nicht bearbeiten.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                -VNetId "new VNet" `
                -Subnet "new subnet" `
                -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

3. Starten Sie die Azure-SSIS IR neu.

### <a name="scenario-2-azure-ssis-ir-is-pointing-to-a-primary-server-endpoint"></a>Szenario 2: Azure-SSIS IR verweist auf einen primären Serverendpunkt

Dieses Szenario ist geeignet, wenn die Azure-SSIS IR auf einen primären Serverendpunkt verweist.

#### <a name="solution"></a>Lösung

Wenn ein Failover auftritt, führen Sie die folgenden Schritte aus:

1. Beenden Sie die Azure-SSIS IR in der primären Region.

2. Bearbeiten Sie die Azure-SSIS IR mit Informationen zu der neuen Region, dem Endpunkt und dem virtuellen Netzwerk für die sekundäre Instanz.

    ```powershell
      Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                    -CatalogServerEndpoint "Azure SQL Database endpoint" `
                    -CatalogAdminCredential "Azure SQL Database admin credentials" `
                    -VNetId "new VNet" `
                    -Subnet "new subnet" `
                    -SetupScriptContainerSasUri "new custom setup SAS URI"
        ```

3. Restart the Azure-SSIS IR.

### Scenario 3: Azure-SSIS IR is pointing to a public endpoint of a SQL Managed Instance

This scenario is suitable if the Azure-SSIS IR is pointing to a public endpoint of a Azure SQL Managed Instance and it doesn't join to a virtual network. The only difference from scenario 2 is that you don't need to edit virtual network information for the Azure-SSIS IR after failover.

#### Solution

When failover occurs, take the following steps:

1. Stop the Azure-SSIS IR in the primary region.

2. Edit the Azure-SSIS IR with the new region and endpoint information for the secondary instance.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                -CatalogServerEndpoint "Azure SQL Database server endpoint" `
                -CatalogAdminCredential "Azure SQL Database server admin credentials" `
                -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

3. Starten Sie die Azure-SSIS IR neu.

### <a name="scenario-4-attach-an-existing-ssisdb-instance-ssis-catalog-to-a-new-azure-ssis-ir"></a>Szenario 4: Anfügen einer vorhandenen SSISDB-Instanz (SSIS-Katalog) an eine neue Azure-SSIS IR-Instanz

Dieses Szenario ist geeignet, wenn SSISDB bei einem Azure Data Factory- oder Azure-SSIS IR-Notfall in der aktuellen Region mit einer neuen Azure-SSIS IR in einer neuen Region arbeiten soll.

#### <a name="solution"></a>Lösung

Wenn ein Failover auftritt, führen Sie die folgenden Schritte aus.

> [!NOTE]
> Verwenden Sie PowerShell für Schritt 4 (Erstellung der IR). Andernfalls informiert das Azure-Portal in einer Fehlermeldung, dass SSISDB bereits vorhanden ist.

1. Beenden Sie die Azure-SSIS IR in der primären Region.

2. Führen Sie eine gespeicherte Prozedur aus, um Metadaten in der SSISDB so zu aktualisieren, dass Verbindungen von **\<new_data_factory_name\>** und **\<new_integration_runtime_name\>** akzeptiert werden.
   
    ```sql
    EXEC [catalog].[failover_integration_runtime] @data_factory_name='<new_data_factory_name>', @integration_runtime_name='<new_integration_runtime_name>'
    ```

3. Erstellen Sie in der neuen Region eine neue Data Factory mit dem Namen **\<new_data_factory_name\>** .

    ```powershell
    Set-AzDataFactoryV2 -ResourceGroupName "new resource group name" `
                      -Location "new region"`
                      -Name "<new_data_factory_name>"
    ```
    
    Weitere Informationen zu diesem PowerShell-Befehl finden Sie unter [Erstellen einer Azure Data Factory mithilfe von PowerShell](quickstart-create-data-factory-powershell.md).

4. Erstellen Sie in der neuen Region mit Azure PowerShell eine neue Azure-SSIS IR mit dem Namen **\<new_integration_runtime_name\>** .

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName "new resource group name" `
                                           -DataFactoryName "new data factory name" `
                                           -Name "<new_integration_runtime_name>" `
                                           -Description $AzureSSISDescription `
                                           -Type Managed `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                           -VnetId "new vnet" `
                                           -Subnet "new subnet" `
                                           -CatalogServerEndpoint $SSISDBServerEndpoint `
                                           -CatalogPricingTier $SSISDBPricingTier
    ```

    Weitere Informationen zu diesem PowerShell-Befehl finden Sie unter [Erstellen der Azure-SSIS Integration Runtime in Azure Data Factory](create-azure-ssis-integration-runtime.md).



## <a name="azure-ssis-ir-failover-with-sql-database"></a>Azure-SSIS IR-Failover bei SQL-Datenbank

### <a name="scenario-1-azure-ssis-ir-is-pointing-to-a-readwrite-listener-endpoint"></a>Szenario 1: Azure-SSIS IR verweist auf einen Listenerendpunkt mit Lese-/Schreibzugriff

Dies eignet sich für folgende Szenarien:

- Die Azure-SSIS IR verweist auf den Listenerendpunkt mit Lese-/Schreibzugriff der Failovergruppe.
- Der SQL-Datenbank-Server ist *nicht* mit der Regel für den VNET-Dienstendpunkt konfiguriert.

Wenn die Azure-SSIS IR auf den Listenerendpunkt mit Lese-/Schreibzugriff verweisen soll, müssen Sie zuerst auf den primären Serverendpunkt verweisen. Nachdem Sie SSISDB in eine Failovergruppe eingefügt haben, können Sie zu einem Listenerendpunkt mit Lese-/Schreibzugriff wechseln und die Azure-SSIS IR neu starten.

#### <a name="solution"></a>Lösung

Wenn ein Failover auftritt, ist dies für Azure-SSIS IR transparent. Azure-SSIS IR stellt automatisch eine Verbindung mit dem neuen primären Endpunkt der Failovergruppe her. 

Wenn Sie die Region oder andere Informationen in der Azure-SSIS IR-Instanz aktualisieren möchten, können Sie sie anhalten, bearbeiten und neu starten.


### <a name="scenario-2-azure-ssis-ir-is-pointing-to-a-primary-server-endpoint"></a>Szenario 2: Azure-SSIS IR verweist auf einen primären Serverendpunkt

Dieses Szenario ist geeignet, wenn die Azure-SSIS IR auf einen primären Serverendpunkt verweist.

#### <a name="solution"></a>Lösung

Wenn ein Failover auftritt, führen Sie die folgenden Schritte aus:

1. Beenden Sie die Azure-SSIS IR in der primären Region.

2. Bearbeiten Sie die Azure-SSIS IR mit Informationen zu der neuen Region, dem Endpunkt und dem virtuellen Netzwerk für die sekundäre Instanz.

    ```powershell
      Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                        -CatalogServerEndpoint "Azure SQL Database endpoint" `
                        -CatalogAdminCredential "Azure SQL Database admin credentials" `
                        -VNetId "new VNet" `
                        -Subnet "new subnet" `
                        -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

3. Starten Sie die Azure-SSIS IR neu.

### <a name="scenario-3-attach-an-existing-ssisdb-ssis-catalog-to-a-new-azure-ssis-ir"></a>Szenario 3: Anfügen einer vorhandenen SSISDB-Instanz (SSIS-Katalog) an eine neue Azure-SSIS IR-Instanz

Dieses Szenario ist geeignet, wenn Sie eine neue Azure-SSIS IR in einer sekundären Region bereitstellen möchten. Es ist auch geeignet, wenn Ihre SSISDB bei einem Azure Data Factory- oder Azure-SSIS IR-Notfall in der aktuellen Region mit einer neuen Azure-SSIS IR in einer neuen Region die Arbeit fortsetzen soll.

#### <a name="solution"></a>Lösung

Wenn ein Failover auftritt, führen Sie die folgenden Schritte aus.

> [!NOTE]
> Verwenden Sie PowerShell für Schritt 4 (Erstellung der IR). Andernfalls informiert das Azure-Portal in einer Fehlermeldung, dass SSISDB bereits vorhanden ist.

1. Beenden Sie die Azure-SSIS IR in der primären Region.

2. Führen Sie eine gespeicherte Prozedur aus, um Metadaten in der SSISDB so zu aktualisieren, dass Verbindungen von **\<new_data_factory_name\>** und **\<new_integration_runtime_name\>** akzeptiert werden.
   
    ```sql
    EXEC [catalog].[failover_integration_runtime] @data_factory_name='<new_data_factory_name>', @integration_runtime_name='<new_integration_runtime_name>'
    ```

3. Erstellen Sie in der neuen Region eine neue Data Factory mit dem Namen **\<new_data_factory_name\>** .

    ```powershell
    Set-AzDataFactoryV2 -ResourceGroupName "new resource group name" `
                         -Location "new region"`
                         -Name "<new_data_factory_name>"
    ```
    
    Weitere Informationen zu diesem PowerShell-Befehl finden Sie unter [Erstellen einer Azure Data Factory mithilfe von PowerShell](quickstart-create-data-factory-powershell.md).

4. Erstellen Sie in der neuen Region mit Azure PowerShell eine neue Azure-SSIS IR mit dem Namen **\<new_integration_runtime_name\>** .

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName "new resource group name" `
                                           -DataFactoryName "new data factory name" `
                                           -Name "<new_integration_runtime_name>" `
                                           -Description $AzureSSISDescription `
                                           -Type Managed `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                           -VnetId "new vnet" `
                                           -Subnet "new subnet" `
                                           -CatalogServerEndpoint $SSISDBServerEndpoint `
                                           -CatalogPricingTier $SSISDBPricingTier
    ```

    Weitere Informationen zu diesem PowerShell-Befehl finden Sie unter [Erstellen der Azure-SSIS Integration Runtime in Azure Data Factory](create-azure-ssis-integration-runtime.md).


## <a name="next-steps"></a>Nächste Schritte

Ziehen Sie diese weiteren Konfigurationsoptionen für die Azure-SSIS-IR in Betracht:

- [Konfigurieren der Azure-SSIS Integration Runtime für hohe Leistung](configure-azure-ssis-integration-runtime-performance.md)

- [Anpassen des Setups für Azure-SSIS-Integration Runtime](how-to-configure-azure-ssis-ir-custom-setup.md)

- [Bereitstellen der Enterprise-Edition für die Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-enterprise-edition.md)