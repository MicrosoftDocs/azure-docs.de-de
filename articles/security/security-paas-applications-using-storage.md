---
title: Schützen von PaaS-Anwendungen mit Azure Storage | Microsoft-Dokumentation
description: Informationen zu den Best Practices in Azure Storage zum Schutz Ihrer webbasierten und mobilen PaaS-Anwendungen.
services: security
documentationcenter: na
author: TomShinder
manager: barbkess
editor: ''
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/28/2018
ms.author: TomShinder
ms.openlocfilehash: 3ad97c7adb5901c1da1d174d12d5d6a91831cc74
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60445416"
---
# <a name="best-practices-for-securing-paas-web-and-mobile-applications-using-azure-storage"></a>Bewährte Methoden zum Schützen webbasierter und mobiler PaaS-Anwendungen mit Azure Storage
In diesem Artikel wird eine Sammlung empfohlener Vorgehensweisen in Azure Storage zum Schutz Ihrer webbasierten und mobilen PaaS-Anwendungen (Platform-as-a-Service) erläutert. Diese empfohlenen Vorgehensweisen sind aus unseren Erfahrungen mit Azure und den Erfahrungen von Kunden wie Ihnen abgeleitet.

Azure bietet Methoden zum Bereitstellen und Verwenden von Speicher, die lokal nicht möglich sind. Sie können in Azure Storage mit relativ geringem Aufwand ein hohes Maß an Skalierbarkeit und Verfügbarkeit erzielen. Azure Storage ist nicht nur die Grundlage für virtuelle Windows- und Linux-Computer in Azure, sondern unterstützt auch große verteilte Anwendungen.

Azure Storage bietet die folgenden vier Dienste: Blobspeicher, Tabellenspeicher, Warteschlangenspeicher und Dateispeicher. Weitere Informationen finden Sie unter [Introduction to Microsoft Azure Storage](../storage/storage-introduction.md) (Einführung in Microsoft Azure, in englischer Sprache).

Der [Azure Storage security guide](../storage/common/storage-security-guide.md) (Azure Storage-Sicherheitsleitfaden, in englischer Sprache) ist eine hervorragende Quelle für ausführliche Informationen zu Azure Storage und Sicherheit. In diesem Artikel zu bewährten Methoden werden einige der Konzepte im Sicherheitsleitfaden in allgemeiner Form erläutert. Er enthält zudem Links zum Sicherheitsleitfaden und anderen Quellen, die weitere Informationen bieten.

Dieser Artikel behandelt die folgenden Best Practices:

- Shared Access Signatures (SAS)
- Rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC)
- Clientseitige Verschlüsselung wertvoller Daten
- Storage Service Encryption


## <a name="use-a-shared-access-signature-instead-of-a-storage-account-key"></a>Verwenden der Shared Access Signature (SAS) anstelle eines Speicherkontoschlüssels
Die Zugriffssteuerung ist von wesentlicher Bedeutung. Zum Steuern des Zugriffs auf Azure Storage erzeugt Azure 512-Bit-Speicherkontoschlüssel (Storage Account Keys, SAKs), wenn Sie ein Speicherkonto erstellen. Der Grad der Schlüsselredundanz ermöglicht Ihnen die Vermeidung von Dienstunterbrechungen während der regelmäßigen Schlüsselrotation. 

Speicherzugriffsschlüssel sind Geheimnisse von hoher Priorität, und nur die Personen, die für die Speicherzugriffssteuerung verantwortlich sind, sollten Zugriff auf sie haben. Falls die falschen Personen Zugriff auf diese Schlüssel erlangen, haben sie umfassende Kontrolle über den Speicher und können Dateien im Speicher ersetzen, löschen oder hinzufügen. Hierzu zählen auch Malware und andere Typen von Inhalten, die Ihre Organisation oder Ihre Kunden potenziell gefährden.

Dennoch benötigen Sie ein Verfahren, um Zugriff auf Objekte im Speicher zu gewähren. Sie können die SAS für einen genauer abgestimmten Zugriff verwenden. Die SAS ermöglicht es Ihnen, bestimmte Objekte im Speicher für ein vordefiniertes Zeitintervall und mit bestimmten Berechtigungen freizugeben. Mit einer SAS können Sie Folgendes definieren:

- Intervallzeitraum der SAS-Gültigkeit, einschließlich Startzeit und Ablaufzeit.
- Berechtigungen, die von der SAS gewährt werden. Mit einer SAS auf einem Blob können einem Benutzer beispielsweise Lese- und Schreibberechtigungen für das Blob gewährt werden, aber keine Löschberechtigungen.
- Eine optionale IP-Adresse oder ein optionaler IP-Adressbereich, für den Azure Storage die SAS akzeptiert. Beispielsweise können Sie einen Bereich mit IP-Adressen angeben, der von Ihrer Organisation genutzt wird. Dies ist eine weitere Sicherheitsmaßnahme für Ihre SAS.
- Das Protokoll, über das Azure Storage die SAS akzeptiert. Sie können diesen optionalen Parameter verwenden, um den Zugriff auf Clients per HTTPS einzuschränken.

Mit SAS können Sie Inhalte auf die gewünschte Weise freigeben, ohne die Speicherkontoschlüssel mitzuteilen. Wenn Sie in Ihrer Anwendung immer SAS verwenden, verfügen Sie über eine sichere Methode zur Freigabe Ihrer Speicherressourcen, ohne dass Sie Kompromisse bei der Sicherheit der Speicherkontoschlüssel eingehen müssen.

Weitere Informationen zu Shared Access Signatures finden Sie unter [Verwenden von Shared Access Signatures (SAS)](../storage/common/storage-dotnet-shared-access-signature-part-1.md). 

## <a name="use-role-based-access-control"></a>Verwenden der rollenbasierten Zugriffssteuerung
Die Zugriffsverwaltung kann auch mit der [rollenbasierter Zugriffssteuerung](../role-based-access-control/overview.md) (role-based access control, RBAC) erfolgen. Mit RBAC gewähren Sie Mitarbeitern auf Need-to-Know-Basis und auf Grundlage des Sicherheitsprinzips der geringsten Rechte genau die Berechtigungen, die sie benötigen. Zu viele Berechtigungen können ein Konto zum leichten Angriffsziel machen. Wenn die Berechtigungen nicht ausreichen, können Mitarbeiter nicht effizient arbeiten. RBAC begegnet diesem Problem dadurch, dass eine präzise Zugriffsverwaltung für Azure ermöglicht wird. Dies ist für Organisationen zwingend erforderlich, die Sicherheitsrichtlinien für den Datenzugriff durchsetzen möchten.

Sie können integrierte RBAC-Rollen in Azure verwenden, um Benutzern Berechtigungen zuzuweisen. Verwenden Sie Beispielsweise die Rolle „Speicherkontomitwirkender“ für Cloudoperatoren, die Speicherkonten verwalten müssen, und nutzen Sie die Rolle „Klassischer Speicherkontomitwirkender“, um klassische Speicherkonten zu verwalten. Cloudoperatoren, die virtuelle Computer, jedoch nicht das virtuelle Netzwerk oder das Speicherkonto, mit dem sie verbunden sind, verwalten müssen, können Sie der Rolle „Mitwirkender für virtuelle Computer“ zuweisen.

Organisationen, die keine Datenzugriffssteuerung mithilfe von Funktionen wie RBAC erzwingen, erteilen Ihren Benutzern möglicherweise mehr Berechtigungen als erforderlich. Dies kann zur Kompromittierung der Daten führen, da einigen Benutzern Zugriff auf Daten gewährt wird, auf die sie von vorneherein keinen Zugriff haben sollten.

Weitere Informationen über RBAC:

- [Verwalten des Zugriffs mithilfe der RBAC und des Azure-Portals](../role-based-access-control/role-assignments-portal.md)
- [Integrierte Rollen für die rollenbasierte Zugriffssteuerung in Azure](../role-based-access-control/built-in-roles.md)
- [Azure Storage-Sicherheitsleitfaden](../storage/common/storage-security-guide.md) 

## <a name="use-client-side-encryption-for-high-value-data"></a>Verwenden von clientseitiger Verschlüsselung für wertvolle Daten
Die clientseitige Verschlüsselung ermöglicht Ihnen das programmgesteuerte Verschlüsseln von Daten während der Übertragung, bevor sie in Azure Storage hochgeladen werden, und das programmgesteuerte Entschlüsseln von Daten beim Abrufen. So können Daten während der Übertragung, jedoch auch ruhende Daten verschlüsselt werden. Die clientseitige Verschlüsselung ist die sicherste Methode zum Verschlüsseln der Daten, sie erfordert jedoch programmgesteuerte Änderungen an Ihrer Anwendung und die Platzierung von Schlüsselverwaltungsprozessen.

Die clientseitige Verschlüsselung bietet Ihnen außerdem die alleinige Kontrolle über die Verschlüsselungsschlüssel. Sie können eigene Verschlüsselungsschlüssel generieren und verwalten. Bei der clientseitigen Verschlüsselung wird ein Umschlagverfahren verwendet. Dabei generiert die Azure Storage-Clientbibliothek einen Inhaltsverschlüsselungsschlüssel (Content Encryption Key, CEK), der dann mit dem Schlüsselverschlüsselungsschlüssel (Key Encryption Key, KEK) umschlossen wird. Der KEK wird anhand eines Schlüsselbezeichners identifiziert und kann ein asymmetrisches Schlüsselpaar oder ein symmetrischer Schlüssel sein. Er kann lokal verwaltet oder im [Azure Key Vault](../key-vault/key-vault-whatis.md) gespeichert werden.

Die clientseitige Verschlüsselung ist in den Java- und .NET-Speicherclientbibliotheken integriert. Informationen zum Verschlüsseln von Daten in Clientanwendungen sowie zum Generieren und Verwalten eigener Verschlüsselungsschlüssel finden Sie unter [Clientseitige Verschlüsselung und Azure Key Vault für Microsoft Azure Storage](../storage/storage-client-side-encryption.md).

## <a name="enable-storage-service-encryption-for-data-at-rest"></a>Aktivieren der Speicherdienstverschlüsselung für ruhende Daten
Wenn [Speicherdienstverschlüsselung](../storage/storage-service-encryption.md) für File Storage aktiviert ist, werden die Daten automatisch mit AES-256-Verschlüsselung verschlüsselt. Microsoft behandelt die gesamte Verschlüsselung, Entschlüsselung und Schlüsselverwaltung. Dieses Feature ist für die Redundanztypen LRS und GRS verfügbar.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel wurde Ihnen eine Sammlung empfohlener Vorgehensweisen in Azure Storage zum Schutz Ihrer webbasierten und mobilen PaaS-Anwendungen vorgestellt. Weitere Informationen zum Schutz Ihrer PaaS-Bereitstellungen finden Sie unter:

- [Schützen von PaaS-Bereitstellungen](security-paas-deployments.md)
- [Securing PaaS web and mobile applications using Azure App Services](security-paas-applications-using-app-services.md) (Schützen webbasierter und mobiler PaaS-Anwendungen mit Azure App Services)
- [Schützen von PaaS-Datenbanken in Azure](security-paas-applications-using-sql.md)
