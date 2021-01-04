---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: cedfd719a5f0aeed6fc2e932d3aa5189b83c9796
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96028251"
---
Die [Microsoft Azure Configuration Manager-Bibliothek für .NET](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/) stellt eine Klasse für die Analyse einer Verbindungszeichenfolge aus einer Konfigurationsdatei bereit. Die Klasse [CloudConfigurationManager](/previous-versions/azure/reference/mt634650(v=azure.100)) analysiert Konfigurationseinstellungen. Sie analysiert Einstellungen für Clientanwendungen, die auf dem Desktop, auf einem mobilen Gerät, auf einem virtuellen Azure-Computer oder in einem Azure-Clouddienst ausgeführt werden.

Fügen Sie die folgenden `using`-Anweisungen hinzu, um auf das Paket `CloudConfigurationManager` zu verweisen:

```csharp
using Microsoft.Azure; //Namespace for CloudConfigurationManager
using Microsoft.Azure.Storage;
```

Das folgende Beispiel zeigt, wie Sie eine Verbindungszeichenfolge aus einer Konfigurationsdatei abrufen:

```csharp
// Parse the connection string and return a reference to the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));
```

Die Verwendung von Azure Configuration Manager ist optional. Sie können auch eine API wie die [Klasse „ConfigurationManager“](/dotnet/api/system.configuration.configurationmanager) von .NET Framework verwenden.