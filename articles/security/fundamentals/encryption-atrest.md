---
title: Datenverschlüsselung ruhender Azure-Daten – Azure Security
description: In diesem Artikel erhalten Sie einen Überblick über die Azure-Datenverschlüsselung ruhender Daten, die Hauptfunktionen und allgemeine Überlegungen.
services: security
documentationcenter: na
author: msmbaldwin
manager: rkarlin
ms.assetid: 9dcb190e-e534-4787-bf82-8ce73bf47dba
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/13/2020
ms.author: mbaldwin
ms.openlocfilehash: 092320db9b7fe2b1f3fe142f84ad201d40dc6e2e
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96492283"
---
# <a name="azure-data-encryption-at-rest"></a>Azure-Datenverschlüsselung ruhender Daten

Microsoft Azure bietet Tools, um Daten den Sicherheits- und Konformitätsanforderungen Ihres Unternehmens entsprechend zu schützen. In diesem Artikel geht es um folgende Themen:

- Schutz ruhender Daten in der gesamten Microsoft Azure-Umgebung
- Verschiedene Komponenten bei der Implementierung des Datenschutzes
- Vor- und Nachteile der verschiedenen Ansätze für den Schutz der Schlüsselverwaltung

Die Verschlüsselung ruhender Daten ist eine gängige Sicherheitsanforderung. In Azure können Organisationen ruhende Daten ohne Risiko und ohne die Kosten für eine benutzerdefinierte Lösung zur Schlüsselverwaltung verschlüsseln. Organisationen haben die Option, Azure die komplette Verschlüsselung ruhender Daten verwalten zu lassen. Zusätzlich haben Organisationen verschiedene Optionen, um die Verschlüsselung oder die Verschlüsselungsschlüssel genau zu verwalten.

## <a name="what-is-encryption-at-rest"></a>Was ist die Verschlüsselung ruhender Daten?

Die Verschlüsselung ruhender Daten ist die Codierung (Verschlüsselung) von Daten, sobald diese gespeichert werden. Die Entwürfe zur Verschlüsselung ruhender Daten in Azure verwenden die symmetrische Verschlüsselung zum schnellen Ver- und Entschlüsseln von großen Mengen an Daten anhand eines einfachen konzeptionellen Modells:

- Ein symmetrischer Verschlüsselungsschlüssel wird zur Verschlüsselung von Daten verwendet, wenn diese in Speicher geschrieben werden.
- Derselbe Verschlüsselungsschlüssel wird verwendet, um diese Daten zu entschlüsseln, wenn diese auf den Gebrauch im Arbeitsspeicher vorbereitet werden.
- Daten können partitioniert werden, und für jede Partition können unterschiedliche Schlüssel verwendet werden.
- Schlüssel müssen an einem sicheren Standort mit identitätsbasierter Zugriffssteuerung und Überwachungsrichtlinien gespeichert werden. Datenverschlüsselungsschlüssel werden häufig mit einem Schlüssel für die Schlüsselverschlüsselung in Azure Key Vault verschlüsselt, um den Zugriff weiter einzuschränken.

In der Praxis erfordern Szenarios der Schlüsselverwaltung und -steuerung sowie die Skalierungs- und Verfügbarkeitssicherstellung zusätzliche Konstrukte. Konzepte und Komponenten der Verschlüsselung ruhender Daten mit Microsoft Azure werden unten beschrieben.

## <a name="the-purpose-of-encryption-at-rest"></a>Welchen Zweck erfüllt die Verschlüsselung ruhender Daten?

Die Verschlüsselung ruhender Daten bietet Schutz für gespeicherte Daten (im Ruhezustand). Zu Angriffen auf ruhende Daten zählen Versuche, physischen Zugriff auf die Hardware zu erhalten, auf der die Daten gespeichert sind, und die enthaltenen Daten zu kompromittieren. Bei einem solchen Angriff wurde die Festplatte eines Servers während der Wartung möglicherweise unsachgemäß behandelt, sodass ein Angreifer die Festplatte entfernen konnte. Dann kann der Angreifer die Festplatte später in einen Computer einbauen, den er steuert, und erhält Zugriff auf die Daten.

Die Verschlüsselung ruhender Daten wurde entwickelt, um dem Angreifer den Zugriff auf die unverschlüsselten Daten zu verwehren, indem die Daten auf der Festplatte verschlüsselt sind. Wenn ein Angreifer sich Zugriff auf eine Festplatte mit verschlüsselten Daten verschafft, aber nicht über die Verschlüsselungsschlüssel verfügt, muss er die Verschlüsselung zum Lesen der Daten umgehen. Dieser Angriff ist weitaus komplexer und ressourcenintensiver als der Zugriff auf nicht verschlüsselte Daten auf einer Festplatte. Aus diesem Grund wird die Verschlüsselung ruhender Daten dringend empfohlen und stellt für viele Organisationen eine Anforderung höchster Priorität dar.

Die Verschlüsselung ruhender Daten ist ggf. auch erforderlich, um die Anforderungen einer Organisation an Datengovernance und Konformität zu erfüllen. Branchenspezifische und gesetzliche Vorschriften wie HIPAA, PCI und FedRAMP geben spezifische Sicherheitsmechanismen für Datenschutz- und Verschlüsselungsanforderungen vor. Die Verschlüsselung ruhender Daten ist eine verpflichtende Maßnahme, die für die Erfüllung dieser Vorschriften erforderlich ist. Weitere Informationen über den Ansatz von Microsoft zur FIPS 140-2-Validierung finden Sie in der [Publikation 140-2 des Federal Information Processing Standard (FIPS)](/microsoft-365/compliance/offering-fips-140-2).

Zusätzlich zur Erfüllung von Konformitäts- und rechtlichen Anforderungen bietet die Verschlüsselung im Ruhezustand einen umfassenden Schutz. Microsoft Azure stellt eine konforme Plattform für Dienste, Anwendungen und Daten bereit. Darüber hinaus bietet Azure umfassende Sicherheit für Einrichtungen, physische Sicherheit, Datenzugriffskontrolle und Überwachung. Es ist jedoch wichtig, für zusätzliche sich „überschneidende“ Sicherheitsmaßnahmen zu sorgen, falls eine der anderen Sicherheitsmaßnahmen fehlschlägt und die Verschlüsselung der ruhenden Daten eine solche Sicherheitsmaßnahme bereitstellt.

Microsoft hat es sich zum Ziel gesetzt, Optionen für die Verschlüsselung im Ruhezustand clouddienstübergreifend bereitzustellen, und gibt seinen Kunden die Kontrolle über Verschlüsselungsschlüssel und Protokolle der Schlüsselverwendung. Darüber hinaus arbeitet Microsoft daran, alle ruhenden Kundendaten standardmäßig zu verschlüsseln.

## <a name="azure-encryption-at-rest-components"></a>Komponenten der Verschlüsselung ruhender Daten von Azure

Wie bereits beschrieben, ist das Ziel der Verschlüsselung ruhender Daten die Verschlüsselung von Daten, die auf einer Festplatte gespeichert werden, mithilfe eines geheimen Verschlüsselungsschlüssels. Um das Ziel der Erstellung sicherer Schlüssel zu erreichen, müssen Sie die Speicherung, Zugriffssteuerung und Verwaltung der Verschlüsselungsschlüssel gewährleisten. Auch wenn sich die Details unterscheiden können, können Implementierungen der Verschlüsselung ruhender Daten von Azure-Diensten anhand der Konzepte beschrieben werden, die im folgenden Diagramm veranschaulicht werden.

![Komponenten](./media/encryption-atrest/azure-security-encryption-atrest-fig1.png)

### <a name="azure-key-vault"></a>Azure-Schlüsseltresor

Der Speicherort der Verschlüsselungsschlüssel und der Zugriffssteuerung dieser Schlüssel ist für das Modell der Verschlüsselung ruhender Daten wesentlich. Die Schlüssel müssen stark gesichert aber dennoch von angegebenen Benutzern verwaltbar sein. Darüber hinaus müssen spezifische Dienste darauf zugreifen können. Azure Key Vault ist die für Azure-Dienste empfohlene Schlüsselspeicherlösung. Es bietet eine dienstübergeifende Verwaltungserfahrung. Schlüssel werden in Schlüsseltresoren gespeichert und verwaltet. Der Zugriff auf diesen Schlüsseltresor kann Benutzern und Diensten gewährt werden. Azure Key Vault unterstützt das Erstellen von Schlüsseln durch Kunden und das Importieren von Kundenschlüsseln für den Gebrauch in von Kunden verwalteten Verschlüsselungsschlüsselszenarios.

### <a name="azure-active-directory"></a>Azure Active Directory

Berechtigungen zum Speichern der Schlüssel in Azure Key Vault, um sie zu verwalten oder um auf sie zur Ver- und Entschlüsselung ruhender Daten zuzugreifen, können Azure AD-Konten gewährt werden.

### <a name="key-hierarchy"></a>Schlüsselhierarchie

Bei der Implementierung einer Verschlüsselung ruhender Daten wird mehr als ein Schlüssel verwendet. Das Speichern eines Verschlüsselungsschlüssels in Azure Key Vault gewährleistet den sicheren Schlüsselzugriff und die zentrale Verwaltung von Schlüsseln. Allerdings ist die Wartung des lokalen Zugriffs auf Verschlüsselungsschlüssel für die Massenverschlüsselung und -entschlüsselung effizienter als die Interaktion mit Key Vault für jeden Datenvorgang und ermöglicht eine stärkere Verschlüsselung und bessere Leistung. Das Einschränken des Gebrauchs eines einzelnen Verschlüsselungsschlüssels mindert das Risiko, das die Sicherheit des Schlüssels gefährdet wird. Zudem werden die Kosten für die erneute Verschlüsselung gesenkt, wenn ein Schlüssel ersetzt werden muss. Azure-Modelle für die Verschlüsselung ruhender Daten arbeiten mit einer Schlüsselhierarchie, die sich aus den folgenden Typen von Schlüsseln zusammensetzt, um allen diesen Anforderungen gerecht zu werden:

- **Datenverschlüsselungsschlüssel (DEK)** : Ein symmetrischer AES256-Schlüssel zum Verschlüsseln einer Partition oder eines Datenblocks.  Eine einzelne Ressource kann aus viele Partitionen bestehen und deshalb auch viele DEKs haben. Das Verschlüsseln jedes Datenblocks mit einem anderen Schlüssel erschwert Kryptoanalyseangriffe. Der Ressourcenanbieter oder die Anwendungsinstanz, die einen spezifischen Block ver- oder entschlüsselt, muss Zugriff auf die DEKs gewähren. Wenn ein DEK durch einen neuen Schlüssel ersetzt wird, müssen nur die Daten im verknüpften Block erneut mit dem neuen Schlüssel verschlüsselt werden.
- **Schlüsselverschlüsselungsschlüssel (KEK)** : Ein Verschlüsselungsschlüssel zur Verschlüsselung der Datenverschlüsselungsschlüssel. Mit einem KEK, der Key Vault nie verlässt, können die DEKs selbst verschlüsselt und gesteuert werden. Die Entität, die auf den KEK zugreifen kann, kann sich von der Entität unterscheiden, die den DEK erfordert. Eine Entität kann den Zugriff auf die DEK aushandeln, um den Zugriff jedes DEK auf eine bestimmte Partition zu begrenzen. Da der KEK erforderlich ist, um DEKs zu entschlüsseln, ist der KEK ein Punkt, an dem DEKs gelöscht werden können, indem der KEK gelöscht wird.

Die DEKs, die mit KEKs verschlüsselt wurden, werden separat gespeichert. Nur eine Entität mit Zugriff auf den KEK kann diese DEKs entschlüsseln. Es werden verschiedene Schlüsselspeichermodelle unterstützt. Weitere Informationen finden Sie unter [Datenverschlüsselungsmodelle](encryption-models.md).

## <a name="encryption-at-rest-in-microsoft-cloud-services"></a>Verschlüsselung ruhender Daten in Microsoft-Clouddiensten

Microsoft Cloud-Dienste werden in drei Cloudmodellen verwendet: IaaS, PaaS und SaaS. Unten finden Sie Beispiele, wie diese in das jeweilige Modell passen:

- Softwaredienste, als Software-as-a-Service (SaaS) bezeichnet, mit denen Anwendungen über die Cloud bereitgestellt werden, z. B. Microsoft 365
- Plattformdienste, mit denen Kunden die Cloud in ihren Anwendungen nutzen, z.B. für Speicherung, Analysen und Service Bus-Funktionalität.
- IaaS-Dienste (Infrastructure-as-a-Service), in denen Kunden Betriebssysteme und Anwendungen bereitstellen, die in der Cloud gehostet werden. Zudem haben sie die Möglichkeit, auch andere Clouddienste zu nutzen.

### <a name="encryption-at-rest-for-saas-customers"></a>Verschlüsselung ruhender Daten für SaaS-Kunden

Software-as-a-Service-Kunden haben die Verschlüsselung ruhender Daten üblicherweise aktiviert oder sie steht zumindest in jedem Dienst zur Verfügung. Microsoft 365 bietet mehrere Optionen, mit denen Kunden die Verschlüsselung ruhender Daten verifizieren oder aktivieren können. Informationen zu Microsoft 365-Diensten finden Sie unter [Verschlüsselung](/microsoft-365/compliance/encryption).

### <a name="encryption-at-rest-for-paas-customers"></a>Verschlüsselung ruhender Daten for PaaS-Kunden

Die Daten von PaaS-Kunden (Platform-as-a-Service) befinden sich in der Regel in einem Speicherdienst wie Blob Storage, können aber auch zwischengespeichert oder in der Anwendungsausführungsumgebung gespeichert werden, beispielsweise auf einem virtuellen Computer. Um sich die für Sie verfügbaren Optionen für die Verschlüsselung ruhender Daten anzusehen, überprüfen Sie die nachstehende Tabelle auf die von Ihnen verwendeten Speicher- und Anwendungsplattformen.

### <a name="encryption-at-rest-for-iaas-customers"></a>Verschlüsselung ruhender Daten für IaaS-Kunden

IaaS-Kunden können mehrere Dienste und Anwendungen verwenden. IaaS-Dienste können die Verschlüsselung ruhender Daten auf ihrer in Azure gehosteten VM und ihren in Azure gehosteten VHDs mithilfe von Azure Disk Encryption aktivieren.

#### <a name="encrypted-storage"></a>Verschlüsselter Speicher

Genauso wie PaaS können auch IaaS-Lösungen andere Azure-Dienste nutzen, um ruhende verschlüsselte Daten zu speichern. In diesen drei Fällen können Sie die Unterstützung der Verschlüsselung ruhender Daten wie von jeden genutzten Azure-Dienst bereitgestellt ermöglichen. In der unten stehenden Tabelle sind die Hauptspeicher, -dienste und -anwendungsplattformen sowie das unterstützte Modell zur Verschlüsselung ruhender Daten aufgelistet.

#### <a name="encrypted-compute"></a>Verschlüsselte Berechnung

Alle verwalteten Datenträger, Momentaufnahmen und Images werden mithilfe von Speicherdienstverschlüsselung mit einem vom Dienst verwalteten Schlüssel verschlüsselt. Eine vollständigere Lösung zur Verschlüsselung ruhender Daten stellt sicher, dass die Daten niemals in unverschlüsselter Form gespeichert werden. Während der Verarbeitung der Daten auf einem virtuellen Computer können die Daten in der Windows- oder Linux-Auslagerungsdatei, in einem Speicherabbild oder in einem Anwendungsprotokoll persistent gespeichert werden. Um sicherzustellen, dass diese Daten im Ruhezustand verschlüsselt sind, können IaaS-Anwendungen Azure Disk Encryption für eine Azure-IaaS-VM (Windows oder Linux) und einen virtuellen Datenträger verwenden.

#### <a name="custom-encryption-at-rest"></a>Kundenspezifische Verschlüsselung ruhender Daten

Es wird empfohlen, dass IaaS-Anwendungen möglichst die Optionen für Azure Disk Encryption und die Verschlüsselung ruhender Daten nutzen, die von jedem verwendeten Azure-Dienst bereitgestellt werden. In einigen Fällen, wie z.B. bei unregelmäßigen Verschlüsselungsanforderungen oder einem nicht Azure-basierten Speicher, muss der Entwickler einer IaaS-Anwendung möglicherweise selbst die Verschlüsselung ruhender Daten implementieren. Entwickler von IaaS-Lösungen können besser auf die Erwartungen der Azure-Verwaltung und von Kunden eingehen, wenn sie gewisse Azure-Komponenten nutzen. Dies bedeutet, dass Entwickler den Azure Key Vault-Dienst verwenden sollten, um einen sicheren Schlüsselspeicher bereitzustellen sowie ihren Kunden Optionen zur Schlüsselverwaltung zur Verfügung stellen zu können, die denen von anderen Azure-Plattformdiensten entsprechen. Darüber hinaus sollten kundenspezifische Lösungen von Azure verwalteten Dienstidentitäten verwenden, um Dienstkonten den Zugriff auf Verschlüsselungsschlüssel zu ermöglichen. Entwicklerinformationen zu Azure Key Vault und verwalteten Dienstidentitäten finden Sie in den jeweiligen SDKs.

## <a name="azure-resource-providers-encryption-model-support"></a>Unterstützung für Verschlüsselungsmodelle von Azure-Ressourcenanbietern

Microsoft Azure-Dienste unterstützen alle mindestens ein Modell zur Verschlüsselung ruhender Daten. Für einige Dienste stehen jedoch nicht alle Verschlüsselungsmodelle zur Verfügung. Für Dienste, die vom Kunden verwaltete Schlüsselszenarien unterstützen, unterstützen sie ggf. nur eine Teilmenge der Schlüsseltypen, die Azure Key Vault für wichtige Verschlüsselungsschlüssel unterstützt. Zudem kann es sein, dass die Unterstützung dieser Szenarien und Schlüsseltypen anhand unterschiedlicher Zeitpläne in den Diensten hinzugefügt wird. In diesem Abschnitt wird die Unterstützung für die Verschlüsselung ruhender Daten für die Haupt-Datenspeicherdienste von Azure zum Zeitpunkt der Veröffentlichung dieses Artikels beschrieben.

### <a name="azure-disk-encryption"></a>Azure Disk Encryption

Jeder Kunde, der Funktionen von Azure-IaaS verwendet, kann die Verschlüsselung ruhender Daten für seine IaaS-VMs und -Datenträger mit Azure Disk Encryption erreichen. Weitere Informationen zu Azure Disk Encryption finden Sie in der [Dokumentation zu Azure Disk Enryption](./azure-disk-encryption-vms-vmss.md).

#### <a name="azure-storage"></a>Azure-Speicher

Alle Azure Storage-Dienste (Blob Storage, Queue Storage, Table Storage und Azure Files) unterstützen die serverseitige Verschlüsselung im Ruhezustand. Einige Dienste unterstützen außerdem vom Kunden verwaltete Schlüssel und clientseitige Verschlüsselung.

- Serverseitig: Alle Azure Storage-Dienste ermöglichen standardmäßig die serverseitige Verschlüsselung mithilfe von dienstverwalteten Schlüsseln – transparent für die Anwendung. Weitere Informationen finden Sie unter [Azure Storage Service Encryption für ruhende Daten](../../storage/common/storage-service-encryption.md). Azure Blob Storage und Azure Files unterstützen auch von Kunden verwaltete RSA-Schlüssel mit 2048 Bit in Azure Key Vault. Weitere Informationen finden Sie unter [Azure Storage Service Encryption mit von Kunden verwalteten Schlüsseln in Azure Key Vault](../../storage/common/customer-managed-keys-configure-key-vault.md).
- Clientseitig: Azure-Blobs, -Tabellen und -Warteschlangen unterstützen die clientseitige Verschlüsselung. Beim Verwenden der clientseitigen Verschlüsselung verschlüsseln Kunden die Daten und laden die Daten als verschlüsselte Blobs hoch. Der Kunde ist für die Schlüsselverwaltung verantwortlich. Weitere Informationen finden Sie unter [Clientseitige Verschlüsselung und Azure Key Vault für Microsoft Azure Storage](../../storage/common/storage-client-side-encryption.md).

#### <a name="azure-sql-database"></a>Azure SQL-Datenbank

Azure SQL-Datenbank unterstützt aktuell die Verschlüsselung ruhender Daten für von Microsoft verwaltete serverseitige und clientseitige Verschlüsselungsszenarien.

Die Unterstützung für die Serververschlüsselung wird aktuell mit dem SQL-Feature „Transparent Data Encryption“ bereitgestellt. Sobald ein Azure SQL-Datenbank-Kunde TDE aktiviert, werden Schlüssel automatisch für ihn erstellt und verwaltet. Die Verschlüsselung ruhender Daten kann auf Datenbank- und Serverebene aktiviert werden. Seit Juni 2017 ist [Transparent Data Encryption (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption) standardmäßig in neu erstellten Datenbanken aktiviert. Die Azure SQL-Datenbank unterstützt von Kunden verwaltete RSA-Schlüssel mit 2048 Bit in Azure Key Vault. Weitere Informationen finden Sie unter [Transparent Data Encryption mit BYOK-Unterstützung (Bring Your Own Key) für Azure SQL-Datenbank und Data Warehouse](/sql/relational-databases/security/encryption/transparent-data-encryption-byok-azure-sql).

Die clientseitige Verschlüsselung von Daten von Azure SQL-Datenbank wird durch die Funktion [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) unterstützt. Always Encrypted verwendet einen Schlüssel, der vom Client erstellt und gespeichert wird. Kunden können den Masterschlüssel in einem Windows-Zertifikatspeicher, Azure Key Vault oder einem lokalen Hardwaresicherheitsmodul speichern. Mit SQL Server Management Studio können SQL-Benutzer auswählen, welchen Schlüssel sie zum Verschlüsseln welcher Spalte verwenden möchten.

## <a name="conclusion"></a>Zusammenfassung

Der Schutz von Kundendaten, die in Azure-Diensten gespeichert sind, ist für Microsoft von größter Bedeutung. Alle in Azure gehosteten Dienste sollen Optionen für die Verschlüsselung ruhender Daten bereitstellen. Azure-Dienste unterstützen entweder dienstseitig verwaltete Schlüssel, kundenseitig verwaltete Schlüssel oder die clientseitige Verschlüsselung. Azure-Dienste verbessern die Verfügbarkeit der Verschlüsselung ruhender Daten deutlich. Zudem sind für die nächsten Monate bereits neue Optionen für die Vorschau und die allgemeinen Verfügbarkeit geplant.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu dienstseitig und kundenseitig verwalteten Schlüsseln finden Sie unter [Datenverschlüsselungsmodelle](encryption-models.md).
- Erfahren Sie, wie Azure [doppelte Verschlüsselung](double-encryption.md) verwendet, um Bedrohungen zu minimieren, die bei der Verschlüsselung von Daten auftreten.
- Erfahren Sie, was Microsoft unternimmt, um die [Plattformintegrität und -sicherheit](platform.md) von Hosts beim Durchlaufen von Hardware- und Firmwareentwicklungs-, Integration-s, Operationalisierungs- und Reparaturpipelines zu gewährleisten.
