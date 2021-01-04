---
title: 'Über die CLI: Hochladen und Binden eines TLS-/SSL-Zertifikats an eine App'
description: Hier erfahren Sie, wie Sie die Azure CLI zum Automatisieren der Bereitstellung und Verwaltung Ihrer App Service-App verwenden. In diesem Beispiel wird gezeigt, wie Sie ein benutzerdefiniertes TLS-/SSL-Zertifikat an eine App binden.
tags: azure-service-management
ms.assetid: eb95d350-81ea-4145-a1e2-6eea3b7469b2
ms.devlang: azurecli
ms.topic: sample
ms.date: 12/11/2017
ms.custom: mvc, seodec18, devx-track-azurecli
ms.openlocfilehash: 1d03018cca9757c8a6ddddbff96738a407bba7d4
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97006394"
---
# <a name="bind-a-custom-tlsssl-certificate-to-an-app-service-app-using-cli"></a>Binden eines benutzerdefinierten TLS-/SSL-Zertifikats an eine App Service-App mit der CLI

Dieses Skriptbeispiel erstellt eine App und die zugehörigen Ressourcen in App Service und bindet dann das TLS-/SSL-Zertifikat eines benutzerdefinierten Domänennamens an die App. Für dieses Beispiel benötigen Sie Folgendes:

* Zugriff auf die Seite „DNS-Konfiguration“ der Domänenregistrierungsstelle.
* Eine gültige PFX-Datei und das zugehörige Kennwort für das TLS-/SSL-Zertifikat, das Sie hochladen und binden möchten.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Wenn Sie die CLI lokal installieren und verwenden möchten, benötigen Sie die Azure CLI-Version 2.0 oder höher. Führen Sie `az --version` aus, um die Version zu finden. Installations- und Upgradeinformationen finden Sie bei Bedarf unter [Installieren von Azure CLI]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Beispielskript

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom TLS/SSL certificate to an app")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Erläuterung des Skripts

Das Skript verwendet die folgenden Befehle. Jeder Befehl in der Tabelle ist mit der zugehörigen Dokumentation verknüpft.

| Get-Help | Notizen |
|---|---|
| [`az group create`](/cli/azure/group#az-group-create) | Erstellt eine Ressourcengruppe, in der alle Ressourcen gespeichert sind. |
| [`az appservice plan create`](/cli/azure/appservice/plan#az-appservice-plan-create) | Erstellt einen App Service-Plan. |
| [`az webapp create`](/cli/azure/webapp#az-webapp-create) | Erstellt eine App Service-App. |
| [`az webapp config hostname add`](/cli/azure/webapp/config/hostname#az-webapp-config-hostname-add) | Ordnet eine benutzerdefinierte Domäne einer App Service-App zu. |
| [`az webapp config ssl upload`](/cli/azure/webapp/config/ssl#az-webapp-config-ssl-upload) | Lädt ein TLS-/SSL-Zertifikat in eine App Service-App hoch. |
| [`az webapp config ssl bind`](/cli/azure/webapp/config/ssl#az-webapp-config-ssl-bind) | Bindet ein hochgeladenes TLS-/SSL-Zertifikat an eine App Service-App. |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Azure CLI finden Sie in der [Azure CLI-Dokumentation](/cli/azure).

Zusätzliche App Service-CLI-Skriptbeispiele finden Sie in der [Dokumentation zu Azure App Service](../samples-cli.md).
