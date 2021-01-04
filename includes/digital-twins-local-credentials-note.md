---
author: baanders
description: Includedatei für DefaultAzureCredential in Azure Digital Twins-Beispielen – Hinweis
ms.service: digital-twins
ms.topic: include
ms.date: 10/22/2020
ms.author: baanders
ms.openlocfilehash: 35996595158994308a78f4d1197dcdc8f00af618
ms.sourcegitcommit: d6a739ff99b2ba9f7705993cf23d4c668235719f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2020
ms.locfileid: "92494499"
---
>[!NOTE]
> Dieses Beispiel verwendet [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential?preserve-view=true&view=azure-dotnet) (Teil der `Azure.Identity`-Bibliothek) zur Authentifizierung von Benutzern mit der Azure Digital Twins-Instanz, wenn Sie diese auf Ihrem lokalen Computer ausführen. Bei dieser Art der Authentifizierung sucht das Beispiel in Ihrer lokalen Umgebung nach Azure-Anmeldeinformationen, z. B. eine Anmeldung von einer lokalen [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) oder in Visual Studio/Visual Studio Code.
>
> Weitere Informationen zum Verwenden von `DefaultAzureCredential` und anderen Authentifizierungsoptionen finden Sie unter [*Vorgehensweise: Schreiben von App-Authentifizierungscode*](../articles/digital-twins/how-to-authenticate-client.md).