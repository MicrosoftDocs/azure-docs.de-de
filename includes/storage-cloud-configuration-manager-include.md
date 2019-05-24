---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 761c3a9aecadd9c1eabdb586f95c47e2988720d8
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66114627"
---
Die [Microsoft Azure Configuration Manager-Bibliothek für .NET](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/) stellt eine Klasse für die Analyse einer Verbindungszeichenfolge aus einer Konfigurationsdatei bereit. Die [CloudConfigurationManager](https://msdn.microsoft.com/library/azure/mt634650.aspx)-Klasse analysiert Konfigurationseinstellungen unabhängig davon, ob die Clientanwendung auf dem Desktop, auf einem mobilen Gerät, auf einem virtuellen Azure-Computer oder in einem Azure-Clouddienst ausgeführt wird.

Fügen Sie Ihrer Klasse die folgende `using`-Anweisung hinzu, um auf das CloudConfigurationManager-Paket zu verweisen:

```csharp
using Microsoft.Azure; //Namespace for CloudConfigurationManager
using Microsoft.WindowsAzure.Storage;
```

Das folgende Beispiel zeigt, wie Sie eine Verbindungszeichenfolge aus einer Konfigurationsdatei abrufen:

```csharp
// Parse the connection string and return a reference to the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));
```

Die Verwendung von Azure Configuration Manager ist optional. Sie können auch eine API wie die [ConfigurationManager](https://msdn.microsoft.com/library/system.configuration.configurationmanager.aspx)-Klasse von .NET Framework verwenden.

