---
title: 'Azure-Schnellstart: Erstellen eines verwalteten Hardwaresicherheitsmoduls (HSM) mithilfe einer Azure Resource Manager-Vorlage'
description: Schnellstart zum Erstellen eines verwalteten Azure Key Vault-HSM mithilfe einer Resource Manager-Vorlage
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: quickstart
ms.custom: mvc, devx-track-azurecli
ms.date: 09/15/2020
ms.author: mbaldwin
ms.openlocfilehash: d47935f76347b2d5272b386942a85643a732e643
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94831751"
---
# <a name="quickstart-create-an-key-vault-managed-hsm-using-an-azure-resource-manager-template"></a>Schnellstart: Erstellen eines verwalteten Key Vault-Hardwaresicherheitsmoduls (HSM) mithilfe einer Azure Resource Manager-Vorlage

Verwaltetes HSM ist ein vollständig verwalteter, hochverfügbarer, standardkonformer Einzelinstanz-Clouddienst, der es Ihnen ermöglicht, kryptografische Schlüssel für Ihre Cloudanwendungen über HSMs zu schützen, die mit **FIPS 140-2 Level 3** validiert sind.  

In dieser Schnellstartanleitung geht es um die Bereitstellung einer Resource Manager-Vorlage zum Erstellen eines verwalteten HSM.  Die [Resource Manager-Vorlage](../../azure-resource-manager/templates/overview.md) ist eine JSON-Datei (JavaScript Object Notation), in der die Infrastruktur und die Konfiguration für Ihr Projekt definiert sind. Für die Vorlage wird deklarative Syntax verwendet. Hiermit können Sie angeben, was Sie bereitstellen möchten, ohne dass Sie die Folge der Programmierbefehle für die Erstellung schreiben müssen. Weitere Informationen zur Entwicklung von Resource Manager-Vorlagen finden Sie in der [Resource Manager-Dokumentation](../../azure-resource-manager/index.yml) und der [Vorlagenreferenz](/azure/templates/microsoft.keyvault/allversions).

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Damit Sie die in diesem Artikel aufgeführten Schritte ausführen können, benötigen Sie Folgendes:

- Ein Abonnement für Microsoft Azure. Falls Sie über kein Azure-Abonnement verfügen, können Sie sich für eine [kostenlose Testversion](https://azure.microsoft.com/pricing/free-trial) registrieren.
- Azure-Befehlszeilenschnittstelle ab Version 2.12.0. Führen Sie `az --version` aus, um die Version zu ermitteln. Installations- und Upgradeinformationen finden Sie bei Bedarf unter [Installieren der Azure CLI]( /cli/azure/install-azure-cli).


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Geben Sie Folgendes ein, um sich über die Befehlszeilenschnittstelle bei Azure anzumelden:

```azurecli
az login
```

Weitere Informationen zu den Anmeldeoptionen für die Befehlszeilenschnittstelle finden Sie unter [Anmelden mit der Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest&preserve-view=true).

## <a name="create-a-manage-hsm"></a>Erstellen eines verwalteten HSM

Die in dieser Schnellstartanleitung verwendete Vorlage stammt von der Seite mit den [Azure-Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/101-managed-hsm-create/).

In der Vorlage definierte Azure-Ressource:

* **Microsoft.KeyVault/managedHSMs**: Erstellen eines verwalteten Azure Key Vault-HSM

Weitere Beispiele für Azure Key Vault-Vorlagen finden Sie [hier](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Keyvault).

Die Vorlage erfordert die Ihrem Konto zugeordnete Objekt-ID. Ermitteln Sie sie mithilfe des Azure CLI-Befehls [az ad user show](/cli/azure/ad/user?view=azure-cli-latest&preserve-view=true#az_ad_user_show). Übergeben Sie dabei Ihre E-Mail-Adresse an den Parameter `--id`. Die Ausgabe kann mit dem Parameter `--query` auf die Objekt-ID beschränkt werden.

```azurecli-interactive
az ad user show --id <your-email-address> --query "objectId"
```

Möglicherweise benötigen Sie auch die Mandanten-ID. Ermitteln Sie sie mithilfe des Azure CLI-Befehls [az ad user show](/cli/azure/account?view=azure-cli-latest&preserve-view=true#az_account_show). Die Ausgabe kann mit dem Parameter `--query` auf die Mandanten-ID beschränkt werden.

 ```azurecli-interactive
 az account show --query "tenantId"
 ```

1. Klicken Sie auf das folgende Bild, um sich bei Azure anzumelden und eine Vorlage zu öffnen. Die Vorlage erstellt einen Schlüsseltresor und ein Geheimnis.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-managed-hsm-create%2Fazuredeploy.json"><img src="../media/deploy-to-azure.svg" alt="deploy to azure"/></a>

2. Wählen Sie die folgenden Werte aus, bzw. geben Sie sie ein.

    Verwenden Sie den Standardwert, um den Schlüsseltresor und ein Geheimnis zu erstellen, sofern kein anderer Wert angegeben ist.

    - **Abonnement**: Wählen Sie ein Azure-Abonnement aus.
    - **Ressourcengruppe**: Wählen Sie die Option **Neu erstellen** aus, geben Sie einen eindeutigen Namen für die Ressourcengruppe ein, und klicken Sie dann auf **OK**.
    - **Standort**: Wählen Sie einen Standort aus. Beispiel: **USA, Süden-Mitte**
    - **managedHSMName**: Geben Sie einen Namen für das verwaltete HSM ein.
    - **Mandanten-ID**: Die Vorlagenfunktion ruft automatisch Ihre Mandanten-ID ab. Ändern Sie den Standardwert nicht.  Ist kein Wert vorhanden, geben Sie die Mandanten-ID ein, die Sie unter [Voraussetzungen](#prerequisites) abgerufen haben.
    * **initialAdminObjectIds**: Geben Sie die Objekt-ID ein, die Sie unter [Voraussetzungen](#prerequisites) abgerufen haben.

3. Wählen Sie die Option **Kaufen**. Nach der erfolgreichen Bereitstellung des Schlüsseltresors erhalten Sie eine Benachrichtigung:

Zum Bereitstellen der Vorlage wird das Azure-Portal verwendet. Neben dem Azure-Portal können Sie auch Azure PowerShell, die Azure-Befehlszeilenschnittstelle (Azure CLI) und die REST-API verwenden. Informationen zu anderen Bereitstellungsmethoden finden Sie unter [Bereitstellen von Vorlagen](../../azure-resource-manager/templates/deploy-powershell.md).

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie ein verwaltetes HSM erstellt. Dieses verwaltete HSM ist erst dann voll funktionsfähig, wenn es aktiviert wurde. Informationen zum Aktivieren des HSM finden Sie unter [Aktivieren des verwalteten HSM](quick-create-cli.md#activate-your-managed-hsm).

- Erhalten Sie einen [Überblick über verwaltetes HSM](overview.md).
- Erfahren Sie mehr über das [Verwalten von Schlüsseln in einem verwalteten HSM](key-management.md).
- Sehen Sie sich die [bewährten Methoden für verwaltetes HSM](best-practices.md) an.
