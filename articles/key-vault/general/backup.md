---
title: Sichern eines in Azure Key Vault gespeicherten Geheimnisses, Schlüssels oder Zertifikats | Microsoft-Dokumentation
description: Verwenden Sie dieses Dokument, um ein Geheimnis, ein Zertifikat oder einen Schlüssel zu sichern, das bzw. der in Azure Key Vault gespeichert ist.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 10/22/2020
ms.author: sudbalas
ms.openlocfilehash: c55c87cd8ab1b2cd1dbaf2c877eb341744d822ef
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93287471"
---
# <a name="azure-key-vault-backup"></a>Sicherung in Azure Key Vault

In diesem Dokument erfahren Sie, wie Sie in Ihrem Schlüsseltresor gespeicherte Geheimnisse, Schlüssel und Zertifikate sichern. Eine Sicherung soll als Offlinekopie aller Geheimnisse in dem unwahrscheinlichen Fall dienen, dass Sie keinen Zugriff mehr auf Ihren Schlüsseltresor haben.

## <a name="overview"></a>Übersicht

Azure Key Vault stellt automatisch Features zur Gewährleistung der Verfügbarkeit und zur Vermeidung von Datenverlusten zur Verfügung. Sichern Sie Geheimnisse nur dann, wenn eine wichtige geschäftliche Begründung vorliegt. Das Sichern von Geheimnissen in Ihrem Schlüsseltresor kann betriebliche Herausforderungen mit sich bringen, etwa im Zusammenhang mit der Verwaltung von mehreren Protokoll-, Berechtigungs- und Sicherungssätzen beim Ablauf oder bei der Rotation von Geheimnissen.

Key Vault gewährleistet die Verfügbarkeit in Notfallszenarien und führt automatisch ein Failover von Anforderungen auf ein Regionspaar aus, ohne dass ein Eingreifen des Benutzers erforderlich ist. Weitere Informationen finden Sie unter [Azure Key Vault: Verfügbarkeit und Redundanz](./disaster-recovery-guidance.md).

Wenn Sie Ihre Geheimnisse vor versehentlichem oder böswilligem Löschen schützen möchten, konfigurieren Sie für Ihren Schlüsseltresor die Funktionen zum Schutz vor vorläufigem und endgültigem Löschen. Weitere Informationen finden Sie unter [Übersicht über die Azure Key Vault-Funktion für vorläufiges Löschen](./soft-delete-overview.md).

## <a name="limitations"></a>Einschränkungen

> [!IMPORTANT]
> Key Vault unterstützt nicht die Möglichkeit, mehr als 500 frühere Versionen eines Schlüssels, Geheimnisses oder Zertifikatobjekts zu sichern. Der Versuch, einen Schlüssel, ein Geheimnis oder ein Zertifikatobjekt zu sichern, kann zu einem Fehler führen. Es ist nicht möglich, frühere Versionen eines Schlüssels, Geheimnisses oder Zertifikats zu löschen.

Key Vault unterstützt derzeit keine Methode zum Sichern eines gesamten Schlüsseltresors in einem einzigen Vorgang. Versuche, die in diesem Dokument aufgeführten Befehle zum Ausführen einer automatisierten Sicherung eines Schlüsseltresors zu verwenden, können zu Fehlern führen und werden von Microsoft oder dem Azure Key Vault-Team nicht unterstützt. 

Berücksichtigen Sie außerdem folgende Auswirkungen:

* Das Sichern von Geheimnissen mit mehreren Versionen kann zu Timeoutfehlern führen.
* Bei der Sicherung wird eine Point-in-Time-Momentaufnahme erstellt. Geheimnisse werden während einer Sicherung unter Umständen erneuert, was zu einem Konflikt mit den Verschlüsselungsschlüsseln führt.
* Wenn die Grenzwerte des Schlüsseltresordiensts für Anforderungen pro Sekunde überschritten werden, wird der Schlüsseltresor gedrosselt. Dies führt zu einem Fehler bei der Sicherung.

## <a name="design-considerations"></a>Überlegungen zum Entwurf

Wenn Sie ein Schlüsseltresorobjekt (Geheimnis, Schlüssel oder Zertifikat) sichern, wird das Objekt beim Sicherungsvorgang als verschlüsseltes Blob heruntergeladen. Dieses Blob kann außerhalb von Azure nicht entschlüsselt werden. Um verwendbare Daten aus diesem Blob zu erhalten, müssen Sie das Blob in einem Schlüsseltresor innerhalb desselben Azure-Abonnements und derselben [Azure-Geografie](https://azure.microsoft.com/global-infrastructure/geographies/) wiederherstellen.

## <a name="prerequisites"></a>Voraussetzungen

Zum Sichern eines Schlüsseltresorobjekts benötigen Sie Folgendes: 

* Mindestens Berechtigungen auf der Ebene „Mitwirkender“ für ein Azure-Abonnement
* Einen primären Schlüsseltresor mit den zu sichernden Geheimnissen
* Sekundärer Schlüsseltresor, in dem Geheimnisse wiederhergestellt werden

## <a name="back-up-and-restore-from-the-azure-portal"></a>Sichern und Wiederherstellen über das Azure-Portal

Führen Sie die Schritte in diesem Abschnitt aus, um Objekte mithilfe des Azure-Portals zu sichern und wiederherzustellen.

### <a name="back-up"></a>Sichern

1. Öffnen Sie das Azure-Portal.
2. Wählen Sie Ihren Schlüsseltresor aus.
3. Navigieren Sie zu dem Objekt (Geheimnis, Schlüssel oder Zertifikat), das Sie sichern möchten.

    ![Screenshot: Auswählen der Schlüsseleinstellung und eines Objekts in einem Schlüsseltresor](../media/backup-1.png)

4. Wählen Sie das Objekt aus.
5. Wählen Sie **Sicherung herunterladen** aus.

    ![Screenshot: Auswählen der Schaltfläche „Sicherung herunterladen“ in einem Schlüsseltresor](../media/backup-2.png)
    
6. Wählen Sie **Herunterladen** aus.

    ![Screenshot: Auswählen der Schaltfläche „Herunterladen“ in einem Schlüsseltresor](../media/backup-3.png)
    
7. Speichern Sie das verschlüsselte Blob an einem sicheren Speicherort.

### <a name="restore"></a>Restore

1. Öffnen Sie das Azure-Portal.
2. Wählen Sie Ihren Schlüsseltresor aus.
3. Navigieren Sie zu dem Typ des Objekts (Geheimnis, Schlüssel oder Zertifikat), das Sie wiederherstellen möchten.
4. Wählen Sie **Sicherung wiederherstellen** aus.

    ![Screenshot: Auswählen von „Sicherung wiederherstellen“ in einem Schlüsseltresor](../media/backup-4.png)
    
5. Navigieren Sie zu dem Speicherort, an dem Sie das verschlüsselte Blob gespeichert haben.
6. Klicken Sie auf **OK**.

## <a name="back-up-and-restore-from-the-azure-cli"></a>Sichern und Wiederherstellen über die Azure CLI

```azurecli
## Log in to Azure
az login

## Set your subscription
az account set --subscription {AZURE SUBSCRIPTION ID}

## Register Key Vault as a provider
az provider register -n Microsoft.KeyVault

## Back up a certificate in Key Vault
az keyvault certificate backup --file {File Path} --name {Certificate Name} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Back up a key in Key Vault
az keyvault key backup --file {File Path} --name {Key Name} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Back up a secret in Key Vault
az keyvault secret backup --file {File Path} --name {Secret Name} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Restore a certificate in Key Vault
az keyvault certificate restore --file {File Path} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Restore a key in Key Vault
az keyvault key restore --file {File Path} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Restore a secret in Key Vault
az keyvault secret restore --file {File Path} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

```

## <a name="next-steps"></a>Nächste Schritte

Aktivieren Sie die [Protokollierung und Überwachung](./logging.md) für Ihre Key Vault-Instanz.