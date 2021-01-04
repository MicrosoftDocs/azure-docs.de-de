---
title: Bewährte Methoden zum Verwenden von Key Vault – Azure Key Vault | Microsoft-Dokumentation
description: Hier erfahren Sie mehr über bewährte Methoden für Azure Key Vault, einschließlich der Steuerung des Zugriffs, der Verwendung separater Schlüsseltresore sowie Sicherungs-, Protokollierungs- und Wiederherstellungsoptionen.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-key-vault
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 03/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 3d959cccd4fbce24e36b1ed62bc6bf417af23c82
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94445505"
---
# <a name="best-practices-to-use-key-vault"></a>Bewährte Methoden zum Verwenden von Key Vault

## <a name="control-access-to-your-vault"></a>Steuern des Zugriffs auf Ihren Schlüsseltresor

Der Azure Key Vault-Clouddienst schützt Verschlüsselungsschlüssel und Geheimnisse (wie Zertifikate, Verbindungszeichenfolgen und Kennwörter). Da es sich hierbei um vertrauliche und geschäftskritische Daten handelt, müssen Sie den Zugriff auf Schlüsseltresore schützen, sodass nur autorisierte Anwendungen und Benutzer zugelassen sind. In diesem [Artikel](secure-your-key-vault.md) finden Sie eine Übersicht des Modells für den Key Vault-Zugriff. Er erläutert Authentifizierung und Autorisierung und beschreibt, wie Sie den Zugriff auf Ihre Schlüsseltresore schützen.

Vorschläge für die Zugriffssteuerung auf Ihren Schlüsseltresor sehen wie folgt aus:
1. Sperren Sie den Zugriff auf Ihr Abonnement, Ihre Ressourcengruppe und Ihre Schlüsseltresore (Azure RBAC).
2. Erstellen Sie Zugriffsrichtlinien für jeden Schlüsseltresor.
3. Verwenden Sie den Prinzipal mit den geringsten Zugriffsrechten, um den Zugriff zu gewähren.
4. Aktivieren Sie die Firewall und [VNET-Dienstendpunkte](overview-vnet-service-endpoints.md).

## <a name="use-separate-key-vault"></a>Verwenden Sie separate Key Vaults.

Es wird empfohlen, einen Schlüsseltresor pro Anwendung und Umgebung (Entwicklung, Präproduktion und Produktion) zu verwenden. Dadurch können Sie die Geheimnisse nicht umgebungsübergreifend freigegeben und es verringert auch die Bedrohung im Falle einer Sicherheitsverletzung.

## <a name="backup"></a>Backup

Stellen Sie sicher, dass Sie regelmäßig Sicherungskopien Ihres Schlüsseltresors beim Aktualisieren/Löschen/Erstellen von Objekten in einem Schlüsseltresor erstellen.

### <a name="azure-powershell-backup-commands"></a>Sicherungsbefehle für Azure PowerShell

* [Sichern eines Zertifikats](/powershell/module/azurerm.keyvault/Backup-AzureKeyVaultCertificate?view=azurermps-6.13.0)
* [Sichern eines Schlüssels](/powershell/module/azurerm.keyvault/Backup-AzureKeyVaultKey?view=azurermps-6.13.0)
* [Sichern eines Geheimnisses](/powershell/module/azurerm.keyvault/Backup-AzureKeyVaultSecret?view=azurermps-6.13.0)

### <a name="azure-cli-backup-commands"></a>Sicherungsbefehle der Azure-Befehlszeilenschnittstelle

* [Sichern eines Zertifikats](/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-backup)
* [Sichern eines Schlüssels](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-backup)
* [Sichern eines Geheimnisses](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-backup)


## <a name="turn-on-logging"></a>Aktivieren der Protokollierung

[Aktivieren Sie die Protokollierung](logging.md) für Ihren Schlüsseltresor. Richten sie auch Warnungen ein.

## <a name="turn-on-recovery-options"></a>Aktivieren von Wiederherstellungsoptionen

1. Aktivieren Sie [Vorläufiges Löschen](soft-delete-overview.md).
2. Aktivieren Sie den Löschschutz, wenn Sie sich auch nach dem Aktivieren des vorläufigen Löschens vor dem erzwungenen Löschen des Geheimnis/Schlüsseltresors schützen möchten.