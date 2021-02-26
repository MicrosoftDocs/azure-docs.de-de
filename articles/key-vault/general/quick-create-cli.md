---
title: Schnellstart – Erstellen eines Azure Key Vault über die Azure CLI
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie eine Azure Key Vault-Instanz über die Azure CLI erstellen.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: quickstart
ms.date: 01/27/2021
ms.author: mbaldwin
ms.openlocfilehash: f7f6f5d82c5fda7101e80ddcb8b17dc6bdef6532
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99070294"
---
# <a name="quickstart-create-a-key-vault-using-the-azure-cli"></a>Schnellstart: Erstellen eines Schlüsseltresors über die Azure CLI

Azure Key Vault ist ein Clouddienst, der einen sicheren Speicher für [Schlüssel](../keys/index.yml), [Geheimnisse](../secrets/index.yml) und [Zertifikate](../certificates/index.yml) bereitstellt. Weitere Informationen zu Key Vault finden Sie unter [Informationen zu Azure Key Vault](overview.md). Weitere Informationen zu den Elementen, die in einem Schlüsseltresor gespeichert werden können, finden Sie unter [Informationen zu Schlüsseln, Geheimnissen und Zertifikaten](about-keys-secrets-certificates.md).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Für diesen Schnellstart ist mindestens Version 2.0.4 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

[!INCLUDE [Create a resource group](../../../includes/key-vault-cli-rg-creation.md)]

## <a name="create-a-key-vault"></a>Erstellen eines Schlüsseltresors

[!INCLUDE [Create a key vault](../../../includes/key-vault-cli-kv-creation.md)]

## <a name="clean-up-resources"></a>Bereinigen der Ressourcen

[!INCLUDE [Create a key vault](../../../includes/key-vault-cli-delete-resources.md)]

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie einen Schlüsseltresor erstellt und diesen gelöscht. Weitere Informationen zu Key Vault und zur Integration in Ihre Anwendungen finden Sie in den folgenden Artikeln:

- [Was ist der Azure-Schlüsseltresor?](overview.md)
- [Azure Key Vault-Sicherheitsübersicht](security-overview.md)
- Sehen Sie sich die Referenz zu den [Azure CLI-Befehlen vom Typ „az keyvault“](/cli/azure/keyvault) an.

