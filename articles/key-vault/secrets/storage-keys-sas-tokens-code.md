---
title: Abrufen von Shared Access Signature-Token im Code | Azure Key Vault
description: Das Feature für verwaltete Speicherkonten bietet eine nahtlose Integration zwischen Azure Key Vault und einem Azure-Speicherkonto. In diesem Beispiel wird das Azure SDK für .NET zum Verwalten von SAS-Token verwendet.
ms.topic: tutorial
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/10/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 2a0202c5259ccebedf03ade217f57b6305b9fa1b
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94444927"
---
# <a name="create-sas-definition-and-fetch-shared-access-signature-tokens-in-code"></a>Erstellen einer SAS-Definition und Abrufen von Shared Access Signature-Token im Code

Sie können Ihr Speicherkonto mit den SAS-Token (Shared Access Signature) verwalten, die in Ihrem Schlüsseltresor gespeichert sind. Weitere Informationen finden Sie unter [Gewähren von eingeschränktem Zugriff auf Azure Storage-Ressourcen mithilfe von SAS](../../storage/common/storage-sas-overview.md).

> [!NOTE]
> Es wird empfohlen, die [rollenbasierte Zugriffssteuerung in Azure (Azure RBAC](../../storage/common/storage-auth-aad.md)) zum Schutz Ihres Speicherkontos zu verwenden, um eine höhere Sicherheit und Benutzerfreundlichkeit als bei der Autorisierung mit gemeinsam verwendeten Schlüsseln zu erzielen.

Dieser Artikel enthält Beispiele für .NET-Code, der eine SAS-Definition erstellt und SAS-Token abruft. Ausführliche Informationen, u. a. zum generierten Client für per Key Vault verwaltete Storage-Konten, finden Sie in unserem [ShareLink](/samples/azure/azure-sdk-for-net/share-link/)-Beispiel. Weitere Informationen zum Erstellen und Speichern von SAS-Token finden Sie unter [Azure-Befehlszeilenschnittstelle](overview-storage-keys.md) oder [Verwalten von Speicherkontoschlüsseln mit Azure Key Vault und Azure PowerShell](overview-storage-keys-powershell.md).

## <a name="code-samples"></a>Codebeispiele

Im folgenden Beispiel wird eine SAS-Vorlage erstellt:

:::code language="csharp" source="~/azure-sdk-for-net/sdk/keyvault/samples/sharelink/Program.cs" range="91-97":::

Mithilfe dieser Vorlage können Sie eine SAS-Definition erstellen: 

:::code language="csharp" source="~/azure-sdk-for-net/sdk/keyvault/samples/sharelink/Program.cs" range="137-156":::

Nach der Erstellung der SAS-Definition können Sie SAS-Token wie Geheimnisse mithilfe von `SecretClient` abrufen. Sie müssen dem Namen des Geheimnisses den Namen des Speicherkontos gefolgt von einem Bindestrich voranstellen:

:::code language="csharp" source="~/azure-sdk-for-net/sdk/keyvault/samples/sharelink/Program.cs" range="52-58":::

Wenn Ihr Shared Access Signature-Token in Kürze abläuft, können Sie das gleiche Geheimnis erneut abrufen, um ein neues Token zu generieren.

Eine Anleitung zur Verwendung von aus Key Vault abgerufenen SAS-Token für den Zugriff auf Azure Storage-Dienste finden Sie unter [Verwenden einer Konto-SAS von einem Client](../../storage/common/storage-account-sas-create-dotnet.md#use-an-account-sas-from-a-client).

> [!NOTE]
> Die App muss das SAS-Token aktualisieren können, wenn sie von Azure Storage den Statuscode 403 empfängt, damit Sie im Falle eines kompromittierten Schlüssels die Schlüsselrotation beschleunigen können. 

## <a name="next-steps"></a>Nächste Schritte
- Erfahren Sie, wie Sie [mithilfe von SAS eingeschränkten Zugriff auf Azure Storage-Ressourcen gewähren](../../storage/common/storage-sas-overview.md).
- Lernen Sie Folgendes: [Verwalten von Speicherkontoschlüsseln mit Azure Key Vault und der Azure-Befehlszeilenschnittstelle](overview-storage-keys.md) oder [Azure PowerShell](overview-storage-keys-powershell.md).
- Weitere Informationen finden Sie unter [Beispiele für verwaltete Speicherkontoschlüssel](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=).