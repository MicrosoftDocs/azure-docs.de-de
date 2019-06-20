---
title: Azure CLI-Skriptbeispiel – Binden eines benutzerdefinierten SSL-Zertifikats an eine Funktionen-App | Microsoft-Dokumentation
description: Azure CLI-Skriptbeispiel – Binden eines benutzerdefinierten SSL-Zertifikats an eine Funktionen-App in Azure
services: functions
documentationcenter: ''
author: ggailey777
manager: jeconnoc
ms.assetid: eb95d350-81ea-4145-a1e2-6eea3b7469b2
ms.service: azure-functions
ms.devlang: azurecli
ms.topic: sample
ms.date: 07/03/2013
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: ee655dc39fbe7d0e3eb5cb41b091aea24d8dbea3
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66131287"
---
# <a name="bind-a-custom-ssl-certificate-to-a-function-app"></a>Binden eines benutzerdefinierten SSL-Zertifikats an eine Funktionen-App

Dieses Skriptbeispiel erstellt eine Funktionen-App und die zugehörigen Ressourcen in App Service und bindet dann das SSL-Zertifikat eines benutzerdefinierten Domänennamens an die App. Für dieses Beispiel benötigen Sie Folgendes:

* Zugriff auf die Seite „DNS-Konfiguration“ der Domänenregistrierungsstelle.
* Eine gültige PFX-Datei und das zugehörige Kennwort für das SSL-Zertifikat, das Sie hochladen und binden möchten.
* Stelllen Sie sicher, dass Sie einen A-Eintrag in Ihrer benutzerdefinierten Domäne konfiguriert haben, die auf den Standarddomänennamen Ihrer Web-App verweist. Weitere Informationen finden Sie in den [Anweisungen zum Zuordnen von benutzerdefinierten Domäne für Azure App Service](https://aka.ms/appservicecustomdns).

Um ein SSL-Zertifikat zu binden, muss Ihre Funktions-App in einem Premium- oder einem App Service-Plan und nicht in einem Verbrauchsplan erstellt werden.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Wenn Sie die CLI lokal installieren und verwenden möchten, müssen Sie mindestens die Version 2.0 der Azure CLI ausführen. Führen Sie `az --version` aus, um die Version zu finden. Installations- und Upgradeinformationen finden Sie bei Bedarf unter [Installieren von Azure CLI]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Beispielskript

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom SSL certificate to a web app")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Erläuterung des Skripts

Das Skript verwendet die folgenden Befehle. Jeder Befehl in der Tabelle ist mit der zugehörigen Dokumentation verknüpft.

| Get-Help | Notizen |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az-group-create) | Erstellt eine Ressourcengruppe, in der alle Ressourcen gespeichert sind. |
| [az storage account create](https://docs.microsoft.com/cli/azure/storage/account#az-storage-account-create) | Erstellt ein Speicherkonto, das für die Funktionen-App erforderlich ist. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#az-appservice-plan-create) | Erstellt einen App Service-Plan, der zum Binden von SSL-Zertifikaten erforderlich ist. |
| [az functionapp create](https://docs.microsoft.com/cli/azure/functionapp#az-functionapp-create) | Erstellt eine Funktions-App im App Service-Plan. |
| [az functionapp config hostname add](https://docs.microsoft.com/cli/azure/functionapp/config/hostname#az-functionapp-config-hostname-add) | Ordnet eine benutzerdefinierte Domäne zu einer Funktionen-App zu. |
| [az functionapp config ssl upload](https://docs.microsoft.com/cli/azure/functionapp/config/ssl#az-functionapp-config-ssl-upload) | Lädt ein benutzerdefiniertes SSL-Zertifikat in eine Funktionen-App hoch. |
| [az functionapp config ssl bind](https://docs.microsoft.com/cli/azure/functionapp/config/ssl#az-functionapp-config-ssl-bind) | Bindet ein hochgeladenes SSL-Zertifikat an eine Funktionen-App. |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Azure CLI finden Sie in der [Azure CLI-Dokumentation](https://docs.microsoft.com/cli/azure).

Zusätzliche App Service-CLI-Skriptbeispiele finden Sie in der [Dokumentation zu Azure App Service](../functions-cli-samples.md).
