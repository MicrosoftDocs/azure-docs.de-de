---
title: Verschieben eines Schlüsseltresors in eine andere Region – Azure Key Vault| Microsoft-Dokumentation
description: Dieser Artikel bietet eine Anleitung zum Verschieben eines Schlüsseltresors in eine andere Region.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 04/24/2020
ms.author: sudbalas
Customer intent: As a key vault administrator, I want to move my vault to another region.
ms.openlocfilehash: 54a1894c88d12cfac1a2d8062425de9549c4292e
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93285212"
---
# <a name="move-an-azure-key-vault-across-regions"></a>Regionsübergreifendes Verschieben eines Azure Key Vault

Azure Key Vault unterstützt keinen Vorgang zum Verschieben von Ressourcen, der das Verschieben eines Schlüsseltresors aus einer Region in eine andere zulässt. In diesem Artikel werden Problemumgehungen für Organisationen behandelt, bei denen eine Geschäftsanforderung besteht, einen Schlüsseltresor in eine andere Region zu verschieben. Jede Problemumgehungsoption unterliegt Einschränkungen. Es ist wichtig, die Auswirkungen dieser Problemumgehungen zu verstehen, bevor Sie versuchen, sie in einer Produktionsumgebung anzuwenden.

Um einen Schlüsseltresor in eine andere Region zu verschieben, erstellen Sie einen Schlüsseltresor in der anderen Region und kopieren dann manuell jeden geheimen Schlüssel aus dem vorhandenen Schlüsseltresor in den neuen Schlüsseltresor. Hierfür können Sie eine der beiden folgenden Methoden verwenden.

## <a name="design-considerations"></a>Überlegungen zum Entwurf

Bevor Sie beginnen, bedenken Sie folgende Konzepte:

* Schlüsseltresornamen sind global eindeutig. Sie können einen Tresornamen nicht wiederverwenden.
* Sie müssen Ihre Zugriffsrichtlinien und Netzwerkkonfigurationseinstellungen in dem neuen Schlüsseltresor erneut konfigurieren.
* Sie müssen vorläufiges Löschen und Bereinigungsschutz in dem neuen Schlüsseltresor erneut konfigurieren.
* Der Sicherungs- und Wiederherstellungsvorgang erhält Ihre Einstellungen für die automatische Rotation nicht. Sie müssen die Einstellungen eventuell neu konfigurieren.

## <a name="option-1-use-the-key-vault-backup-and-restore-commands"></a>Option 1: Verwenden der Key Vault-Befehle zum Sichern und Wiederherstellen

Mit dem Sicherungsbefehl können Sie die einzelnen Geheimnisse, Schlüssel und Zertifikate in Ihrem Tresor sichern. Ihre Geheimnisse werden als verschlüsseltes Blob heruntergeladen. Anschließend können Sie das Blob in Ihrem neuen Schlüsseltresor wiederherstellen. Eine Liste der Befehle finden Sie unter [Azure Key Vault-Befehle](/powershell/module/azurerm.keyvault/?view=azurermps-6.13.0#key_vault).

Die Verwendung der Befehle Sichern und Wiederherstellen weist zwei Einschränkungen auf:

* Es ist nicht möglich, einen Schlüsseltresor in einer geografischen Region zu sichern und ihn in einer anderen geografischen Region wiederherzustellen. Weitere Informationen finden Sie unter [Azure-Geografien](https://azure.microsoft.com/global-infrastructure/geographies/).

* Der Sicherungsbefehl sichert alle Versionen der einzelnen Geheimnisse. Wenn Sie ein Geheimnis mit einer großen Anzahl von Vorgängerversionen (mehr als 10) verwenden, kann die Anforderung die maximal zulässige Anforderungsgröße überschreiten, und der Vorgang schlägt eventuell fehl.

## <a name="option-2-manually-download-and-upload-the-key-vault-secrets"></a>Option 2: Manuelles Herunterladen und Hochladen der Schlüsseltresorgeheimnisse

Sie können bestimmte Geheimnistypen manuell herunterladen. Beispielsweise können Sie Zertifikate als PFX-Datei herunterladen. Diese Option beseitigt die geografischen Einschränkungen für einige Geheimnistypen wie Zertifikate. Sie können die PFX-Dateien in beliebige Schlüsseltresore in einer beliebigen Region hochladen. Die Geheimnisse werden in einem nicht kennwortgeschützten Format heruntergeladen. Sie sind für den Schutz Ihrer Geheimnisse während des Verschiebens verantwortlich.