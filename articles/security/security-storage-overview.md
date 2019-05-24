---
title: Sicherheitsfunktionen, die mit Azure Storage verwendet werden können | Microsoft-Dokumentation
description: Dieser Artikel bietet eine Übersicht über die wichtigsten Sicherheitsfunktionen von Azure, die mit Azure Storage verwendet werden können.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/28/2019
ms.author: terrylan
ms.openlocfilehash: 74c24e05d974c4b3bb15a242185b645231f74580
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65792781"
---
# <a name="azure-storage-security-overview"></a>Übersicht über die Sicherheit von Azure Storage

Dieser Artikel bietet eine Übersicht über die Sicherheitsfunktionen von Azure, die mit Azure Storage verwendet werden können. Azure Storage ist eine Cloudspeicherlösung für moderne Anwendungen, die eine Kombination aus Dauerhaftigkeit, Verfügbarkeit und Skalierbarkeit benötigen, um die Anforderungen ihrer Kunden zu erfüllen. Azure Storage bietet einen umfassenden Satz von Sicherheitsfunktionen. Ihre Möglichkeiten:

* Schützen Sie das Speicherkonto mit rollenbasierter Zugriffssteuerung (RBAC) und Azure Active Directory.
* Schützen Sie Daten während der Übertragung zwischen einer Anwendung und Azure mit clientseitiger Verschlüsselung, HTTPS oder SMB 3.0.
* Legen Sie fest, dass Daten automatisch mit der Speicherdienstverschlüsselung verschlüsselt werden, wenn sie in Azure Storage geschrieben werden.
* Legen Sie fest, dass Betriebssystemdatenträger und sonstige von virtuellen Computern (VMs) verwendete Datenträger mit Azure Disk Encryption verschlüsselt werden.
* Erteilen Sie delegierten Zugriff auf die Datenobjekte in Azure Storage mit Shared Access Signatures (SAS).
* Verwenden Sie Analysen zum Verfolgen der Authentifizierungsmethode, die ein Benutzer beim Zugriff auf Storage verwendet.

Eine ausführlichere Betrachtung der Sicherheit in Azure Storage finden Sie im [Azure Storage-Sicherheitsleitfaden](../storage/common/storage-security-guide.md). Dieser Leitfaden enthält fundierte Informationen zu den Sicherheitsfeatures von Azure Storage. Zu diesen Features gehören Speicherkontoschlüssel, Datenverschlüsselung während der Übertragung und Speicherung sowie Speicheranalysen.

## <a name="role-based-access-control"></a>Rollenbasierte Access Control

Sie können Ihr Speicherkonto mit rollenbasierter Zugriffssteuerung (RBAC) sichern. Das Einschränken des Zugriffs auf der Grundlage der Sicherheitsprinzipien [Need to know](https://en.wikipedia.org/wiki/Need_to_know) und [Least Privilege](https://en.wikipedia.org/wiki/Principle_of_least_privilege) ist für Organisationen unerlässlich, die Sicherheitsrichtlinien für den Datenzugriff erzwingen möchten. Diese Zugriffsrechte werden gewährt, indem Gruppen und Anwendungen die jeweils geeignete RBAC-Rolle für einen bestimmten Bereich zugewiesen wird. Sie können [integrierte RBAC-Rollen](../role-based-access-control/built-in-roles.md)(etwa „Speicherkontomitwirkender“) verwenden, um Benutzern Berechtigungen zuzuweisen.

Weitere Informationen:

* [Rollenbasierte Zugriffssteuerung in Azure Active Directory](../role-based-access-control/role-assignments-portal.md)

## <a name="delegated-access-to-storage-objects"></a>Delegierter Zugriff auf Speicherobjekte

Shared Access Signatures bieten delegierten Zugriff auf Ressourcen in Ihrem Speicherkonto. Eine SAS bietet die Möglichkeit, einem Client für einen bestimmten Zeitraum spezielle eingeschränkte Berechtigungen für Objekte in Ihrem Speicherkonto zu erteilen. Dazu müssen Sie nicht Ihre Kontozugriffsschlüssel freigeben.

Die SAS ist ein URI, dessen Abfrageparameter alle erforderlichen Informationen für den authentifizierten Zugriff auf eine Speicherressource enthalten. Für den Zugriff auf Speicherressourcen mit der SAS braucht der Client diese nur an den entsprechenden Konstruktor bzw. die entsprechende Methode zu übergeben.

Weitere Informationen:

* [Grundlagen zum SAS-Modell](../storage/common/storage-dotnet-shared-access-signature-part-1.md)
* [Erstellen und Verwenden einer SAS mit Blob Storage](../storage/common/storage-dotnet-shared-access-signature-part-1.md)

## <a name="encryption-in-transit"></a>Verschlüsselung während der Übertragung

Verschlüsselung während der Übertragung ist ein Mechanismus zum Schutz der Daten bei der Übertragung über Netzwerke hinweg. Mit Azure Storage können Sie Daten mit folgenden Features schützen:

* [Verschlüsselung auf Transportebene](../storage/common/storage-security-guide.md#encryption-in-transit) (etwa HTTPS), wenn Sie Daten in oder aus Azure Storage übertragen.
* [Wire-Verschlüsselung](../storage/common/storage-security-guide.md#using-encryption-during-transit-with-azure-file-shares) (etwa SMB 3.0-Verschlüsselung für Azure-Dateifreigaben).
* [Clientseitige Verschlüsselung](../storage/common/storage-security-guide.md#using-client-side-encryption-to-secure-data-that-you-send-to-storage), um die Daten vor ihrer Übertragung in Storage zu verschlüsseln und nach der Übertragung aus Storage zu entschlüsseln.

Weitere Informationen zur clientseitigen Verschlüsselung finden Sie hier:

* [Clientseitige Verschlüsselung für Microsoft Azure Storage](https://blogs.msdn.microsoft.com/windowsazurestorage/2015/04/28/client-side-encryption-for-microsoft-azure-storage-preview/)
* [Cloudsicherheitsteuerungs-Serien: Verschlüsseln von Daten während der Übertragung](https://cloudblogs.microsoft.com/microsoftsecure/2015/08/10/cloud-security-controls-series-encrypting-data-in-transit/)

## <a name="encryption-at-rest"></a>Verschlüsselung ruhender Daten

Für viele Organisationen ist die Verschlüsselung [ruhender Daten](https://cloudblogs.microsoft.com/microsoftsecure/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/) ein obligatorischer Schritt in Richtung Datenschutz, Konformität und Datenhoheit. Drei Azure-Features ermöglichen die Verschlüsselung ruhender Daten:

* Die [Speicherdienstverschlüsselung](../storage/common/storage-security-guide.md#encryption-at-rest) ist immer aktiviert und verschlüsselt beim Schreiben in Azure Storage automatisch Daten des Speicherdiensts.
* Die [clientseitige Verschlüsselung](../storage/common/storage-security-guide.md#client-side-encryption) ermöglicht ebenfalls eine Verschlüsselung ruhender Daten.
* Mit [Azure Disk Encryption](../storage/common/storage-security-guide.md#using-azure-disk-encryption-to-encrypt-disks-used-by-your-virtual-machines) können Sie die Betriebssystemdatenträger und andere Datenträger verschlüsseln, die von einem virtuellen IaaS-Computer verwendet werden.

Weitere Informationen zu Storage Service Encryption finden Sie hier:

* Die [Azure-Speicherdienstverschlüsselung](https://azure.microsoft.com/services/storage/) ist für [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/) verfügbar. Ausführliche Informationen zu anderen Typen von Azure-Speicher finden Sie unter [Azure Files](https://azure.microsoft.com/services/storage/files/), [Table Storage](https://azure.microsoft.com/services/storage/tables/) und [Queue Storage](https://azure.microsoft.com/services/storage/queues/).
* [Azure Storage Service Encryption für ruhende Daten](../storage/common/storage-service-encryption.md)

## <a name="azure-disk-encryption"></a>Azure Disk Encryption

Mit Azure Disk Encryption für virtuelle Computer können Sie die Sicherheits- und Konformitätsanforderungen Ihrer Organisation erfüllen. Dieses Feature verschlüsselt Ihre VM-Datenträger (einschließlich Start- und anderer Datenträger) mit Schlüsseln und Richtlinien, die Sie in [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) steuern.

Datenträgerverschlüsselung für VMs funktioniert für Linux- und Windows-Betriebssysteme. Es wird auch Key Vault verwendet, um Ihnen das Schützen, Verwalten und Überwachen der Nutzung Ihrer Datenträgerverschlüsselungs-Schlüssel zu erleichtern. Alle Daten auf den Datenträgern Ihrer virtuellen Computer werden im Ruhezustand mithilfe von Verschlüsselungstechnologien des Industriestandards in Ihren Azure-Speicherkonten verschlüsselt. Die Disk Encryption-Lösung für Windows basiert auf der [Microsoft BitLocker-Laufwerkverschlüsselung](https://technet.microsoft.com/library/cc732774.aspx), die Linux-Lösung auf [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt).

Weitere Informationen

* [Azure Disk Encryption for Windows and Linux IaaS Virtual Machines (Azure Disk Encryption für virtuelle Windows- und Linux-IaaS-Computer)](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0)

## <a name="firewalls-and-virtual-networks"></a>Firewalls und virtuelle Netzwerke

Azure Storage ermöglicht es Ihnen, Firewallregeln für Ihre Speicherkonten zu aktivieren. Sobald diese aktiviert sind, blockieren sie eingehende Datenanforderungen, einschließlich Anforderungen von anderen Azure-Diensten. Sie können Ausnahmen zum Zulassen von Datenverkehr konfigurieren. Firewallregeln können für vorhandene Speicherkonten oder während der Erstellung aktiviert werden.

Sie sollten diese Funktion verwenden, um Ihre Speicherkonten für eine bestimmte Gruppe zulässiger Netzwerke zu sichern.

Weitere Informationen zu Firewalls und virtuellen Netzwerken in Azure Storage finden Sie im Artikel [Konfigurieren von Firewalls und virtuellen Netzwerken in Azure Storage](../storage/common/storage-network-security.md).

## <a name="azure-data-box"></a>Azure Data Box

Wenn das Netzwerk keine Option ist, können Sie mit Data Box-, Data Box Disk- und Data Box Heavy-Geräten große Datenmengen nach Azure übertragen. Diese Geräte zur Offlinedatenübertragung werden zwischen Ihrer Organisation und dem Azure-Rechenzentrum verschickt. Die Geräte nutzen die AES-Verschlüsselung, um Ihre Daten während der Übertragung zu schützen, und werden nach dem Upload einem umfassenden Bereinigungsprozess unterzogen, um Ihre Daten sicher von den Geräten zu löschen.

Data Box Edge und Data Box Gateway sind Produkte zur Onlinedatenübertragung, die als Netzwerkspeichergateways für die Übertragung der Daten zwischen Ihrem Standort und Azure dienen. Data Box Edge, ein lokales Netzwerkgerät, überträgt Daten nach und aus Azure und verwendet KI-fähiges Edge Computing zur Datenverarbeitung. Data Box Gateway ist eine virtuelle Appliance mit Speichergatewayfunktionen.

Weitere Informationen:

* [Azure Data Box](https://azure.microsoft.com/services/storage/databox/)
* [Azure Data Box Edge](../databox-online/data-box-edge-overview.md)
* [Azure Data Box Gateway](..//databox-online/data-box-gateway-overview.md)

## <a name="advanced-threat-protection"></a>Erweiterter Schutz vor Bedrohungen

Azure Storage bietet Advanced Threat Protection, um intelligente Sicherheitsfunktionen zu nutzen, die ungewöhnliche und möglicherweise schädliche Versuche zu ermitteln, auf Ihr Speicherkonto zuzugreifen oder dieses unbefugt zu nutzen. Advanced Threat Protection überwacht die Azure Storage-Diagnoseprotokolle auf verdächtige Lese-, Schreib- oder Löschanforderungen in Blob Storage.

Warnungen von Advanced Threat Protection können im [Azure Security Center](https://azure.microsoft.com/services/security-center/) angezeigt werden. Das Azure Security Center zeigt Details zu allen ermittelten verdächtigen Aktivitäten an und empfiehlt Aktionen zur Untersuchung und Beseitigung der potenziellen Bedrohung.

Weitere Informationen:

* [Azure Storage Advanced Threat Protection – Übersicht](../storage/common/storage-advanced-threat-protection.md)

## <a name="azure-key-vault"></a>Azure Key Vault

Azure Disk Encryption verwendet [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), damit Sie die Verschlüsselungsschlüssel und Geheimnisse für die Datenträgerverschlüsselung in Ihrem Key Vault-Abonnement steuern und verwalten können. Außerdem wird sichergestellt, dass alle ruhenden Daten auf den Datenträgern der virtuellen Computer in Azure Storage verschlüsselt sind. Es empfiehlt sich, die Verwendung von Schlüsseln und Richtlinien mithilfe des Azure-Schlüsseltresors zu überwachen.

Weitere Informationen

* [Was ist der Azure-Schlüsseltresor?](../key-vault/key-vault-overview.md)
