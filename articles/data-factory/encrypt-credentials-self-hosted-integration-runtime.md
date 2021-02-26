---
title: Verschlüsseln von Anmeldeinformationen in Azure Data Factory
description: Erhalten Sie Informationen zum Verschlüsseln und Speichern von Anmeldeinformationen für Ihre lokalen Datenspeicher auf einem Computer mit selbstgehosteter Integrationslaufzeit.
author: nabhishek
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/15/2018
ms.author: abnarain
ms.openlocfilehash: 59d177aa3baf25f185201f1b6c4738cfce9c25a3
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100392645"
---
# <a name="encrypt-credentials-for-on-premises-data-stores-in-azure-data-factory"></a>Verschlüsseln von Anmeldeinformationen für lokale Datenspeicher in Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Sie können Anmeldeinformationen für Ihre lokalen Datenspeicher (verknüpfte Dienste mit vertraulichen Informationen) auf einem Computer mit selbstgehosteter Integrationslaufzeit verschlüsseln und speichern. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Sie übergeben eine JSON-Definitionsdatei mit Anmeldeinformationen an das <br/>[**New-AzDataFactoryV2LinkedServiceEncryptedCredential**](/powershell/module/az.datafactory/New-AzDataFactoryV2LinkedServiceEncryptedCredential)-Cmdlet, um eine JSON-Ausgabedefinitionsdatei mit den verschlüsselten Anmeldeinformationen zu erstellen. Verwenden Sie dann die aktualisierte JSON-Definition, um die verknüpften Dienste zu erstellen.

## <a name="author-sql-server-linked-service"></a>Verfassen eines mit SQL Server verknüpften Diensts
Erstellen Sie in einem beliebigen Ordner eine JSON-Datei namens **SqlServerLinkedService.json** mit dem folgenden Inhalt:  

Ersetzen Sie `<servername>`, `<databasename>`, `<username>` und `<password>` durch Werte für Ihren SQL Server, bevor die Datei gespeichert wird. Ersetzen Sie zudem `<integration runtime name>` durch den Namen Ihrer Integrationslaufzeit. 

```json
{
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": "Server=<servername>;Database=<databasename>;User ID=<username>;Password=<password>;Timeout=60"
        },
        "connectVia": {
            "type": "integrationRuntimeReference",
            "referenceName": "<integration runtime name>"
        },
        "name": "SqlServerLinkedService"
    }
}
```

## <a name="encrypt-credentials"></a>Verschlüsseln von Anmeldeinformationen
Um vertrauliche Daten aus der JSON-Nutzlast in einer lokalen selbstgehosteten Integrationslaufzeit zu verschlüsseln, führen Sie **New-AzDataFactoryV2LinkedServiceEncryptedCredential** aus, und übergeben Sie die JSON-Nutzlast. Dieses Cmdlet stellt sicher, dass die Anmeldeinformationen per DPAPI verschlüsselt und lokal auf dem Knoten der selbstgehosteten Integrationslaufzeit gespeichert werden. Die Ausgabenutzlast, die den verschlüsselten Verweis auf die Anmeldeinformationen enthält, kann an eine weitere JSON-Datei umgeleitet werden (in diesem Fall „encryptedLinkedService.json“).

```powershell
New-AzDataFactoryV2LinkedServiceEncryptedCredential -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "SqlServerLinkedService" -DefinitionFile ".\SQLServerLinkedService.json" > encryptedSQLServerLinkedService.json
```

## <a name="use-the-json-with-encrypted-credentials"></a>Verwenden der JSON-Datei mit verschlüsselten Anmeldeinformationen
Jetzt verwenden Sie die JSON-Ausgabedatei aus dem vorherigen Befehl, der die verschlüsselten Anmeldeinformationen enthält, um **SqlServerLinkedService** einzurichten.

```powershell
Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "EncryptedSqlServerLinkedService" -DefinitionFile ".\encryptedSqlServerLinkedService.json" 
```

## <a name="next-steps"></a>Nächste Schritte
Informationen zu Sicherheitsüberlegungen für das Verschieben von Daten finden Sie unter [Sicherheitsüberlegungen zur Datenverschiebung](data-movement-security-considerations.md).

