---
title: Datenverschlüsselungsmodelle in Microsoft Azure
description: Dieser Artikel bietet eine Übersicht über die Datenverschlüsselungsmodelle in Microsoft Azure.
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
ms.date: 09/09/2020
ms.author: mbaldwin
ms.openlocfilehash: 141daa485fae5aba2db23647fada30ba5b621cd0
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96854613"
---
# <a name="data-encryption-models"></a>Datenverschlüsselungsmodelle

Damit Sie verstehen, wie die verschiedenen Ressourcenanbieter in Azure die ruhende Verschlüsselung implementieren, ist es wichtig, dass Sie die unterschiedlichen Verschlüsselungsmodelle und deren Vor- und Nachteile kennen. Diese Definitionen sind in allen Ressourcenanbietern in Azure gleich, um die gleiche Sprache und Taxonomie zu gewährleisten.

Es gibt drei Szenarios für die serverseitige Verschlüsselung:

- Serverseitige Verschlüsselung mit dienstseitig verwalteten Schlüsseln
  - Azure-Ressourcenanbieter führen die Verschlüsselungs- und Entschlüsselungsvorgänge durch
  - Microsoft verwaltet die Schlüssel
  - Vollständige Cloud-Funktionen

- Serverseitige Verschlüsselung mit vom Kunden verwalteten Schlüsseln in Azure Key Vault
  - Azure-Ressourcenanbieter führen die Verschlüsselungs- und Entschlüsselungsvorgänge durch
  - Der Kunde steuert Schlüssel per Azure Key Vault
  - Vollständige Cloud-Funktionen

- Serverseitige Verschlüsselung mit vom Kunden verwalteten Schlüsseln auf kundengesteuerter Hardware
  - Azure-Ressourcenanbieter führen die Verschlüsselungs- und Entschlüsselungsvorgänge durch
  - Der Kunde steuert die Schlüssel auf kundengesteuerter Hardware
  - Vollständige Cloud-Funktionen

Als Modelle für die serverseitige Verschlüsselung werden Verschlüsselungen bezeichnet, die vom Azure-Dienst durchgeführt werden. In diesem Modell führt der Ressourcenanbieter den Ver- und Entschlüsselungsvorgang durch. Azure Storage erhält z.B. Daten in Nur-Text-Vorgängen und führt die Ver- und Entschlüsselung intern durch. Der Ressourcenanbieter verwendet möglicherweise Verschlüsselungsschlüssel, die je nach Konfiguration von Microsoft oder dem Kunden verwaltet werden.

![Server](./media/encryption-models/azure-security-encryption-atrest-fig3.png)

Jedes Modell zur serverseitigen Verschlüsselung impliziert eindeutige Merkmale für die Schlüsselverwaltung. Dazu zählt u.a., wo und wie Verschlüsselungsschlüssel erstellt und gespeichert werden, sowie die Zugriffsmodelle und die Schlüsselrotationsprozeduren.  

Ziehen Sie für die clientseitige Verschlüsselung folgendes in Betracht:

- Azure-Dienste können entschlüsselte Daten nicht erkennen
- Die Kunden bewahren die Schlüssel lokal auf (oder in anderen sicheren Speichern). Schlüssel sind für Azure-Dienste nicht verfügbar
- Reduzierte Cloud-Funktionen

Die unterstützten Verschlüsselungsmodelle in Azure können in zwei Hauptgruppen unterteilt werden: „Clientverschlüsselung“ und „serverseitige Verschlüsselung“ (wie bereits erwähnt). Unabhängig vom Verschlüsselungsmodell für ruhende Daten wird für Azure-Dienste immer der Einsatz eines sicheren Datentransports wie TLS oder HTTPS empfohlen. Deshalb sollte das Datentransportprotokoll die Verschlüsselung während des Datentransport behandeln und sollte kein ausschlaggebender Faktor bei der Entscheidung für oder gegen ein Verschlüsselungsmodell für ruhende Daten sein.

## <a name="client-encryption-model"></a>Clientverschlüsselungsmodell

Als Clientverschlüsselungsmodell wird eine Verschlüsselung bezeichnet, die außerhalb des Ressourcenanbieters oder außerhalb von Azure von der Dienst- oder der aufrufenden Anwendung durchgeführt wird. Die Verschlüsselung kann von der Dienstanwendung in Azure durchgeführt werden oder von einer Anwendung, die im Rechenzentrum des Kunden ausgeführt wird. In beiden Fällen erhält der Azure-Ressourcenanbieter einen verschlüsselten Datenblob, ohne die Daten entschlüsseln oder auf die Verschlüsselungsschlüssel zugreifen zu können, wenn Sie dieses Verschlüsselungsmodell einsetzen. In diesem Modell erfolgt die Schlüsselverwaltung über den aufrufenden Dienst bzw. die aufrufende Anwendung und ist für den Azure-Dienst nicht transparent.

![Client](./media/encryption-models/azure-security-encryption-atrest-fig2.png)

## <a name="server-side-encryption-using-service-managed-keys"></a>Serverseitige Verschlüsselung mit vom Dienst verwalteten Schlüsseln

Für viele Kunden ist die wichtigste Anforderung, sicherzustellen, dass die Daten immer dann verschlüsselt sind, wenn sie ruhen. Dieses Modell ist durch die serverseitige Verschlüsselung mit vom Dienst verwalteten Schlüsseln möglich, da diese es Kunden ermöglicht, die spezifische Ressource (Storage-Konto, SQL-Datenbank usw.) für die Verschlüsselung zu kennzeichnen und alle Schlüsselverwaltungsaspekte wie die Schlüsselausstellung, -rotation und -sicherung Microsoft zu überlassen. Die meisten Azure-Dienste, die die Verschlüsselung ruhender Daten unterstützen, unterstützen üblicherweise auch dieses Modell, bei dem die Verwaltung der Verschlüsselungsschlüssel an Azure übergeben wird. Der Azure-Ressourcenanbieter erstellt die Schlüssel, speichert sie an einem sicheren Speicherort und ruft sie bei Bedarf ab. Dies bedeutet, dass der Dienst Vollzugriff auf die Schlüssel hat, und dass er die Verwaltung der Lebensdauer der Anmeldeinformationen vollständig steuert.

![verwaltet](./media/encryption-models/azure-security-encryption-atrest-fig4.png)

Durch die serverseitige Verschlüsselung mit vom Dienst verwalteten Schlüsseln wurde deshalb schnell auf die Anforderung reagiert, eine Verschlüsselung ruhender Daten mit geringem Mehraufwand für den Kunden bereitzustellen. Wenn sie zur Verfügung steht, kann der Kunde das Azure-Portal für das Zielabonnement und den Zielressourcenanbieter öffnen und ein Kontrollkästchen aktivieren, das angibt, dass die Daten verschlüsselt werden sollen. Bei einigen Ressourcen-Managern ist die serverseitige Verschlüsselungen mit vom Dienst verwalteten Schlüssel standardmäßig aktiviert.

Die serverseitige Verschlüsselung mit von Microsoft verwalteten Schlüsseln impliziert nicht, dass der Dienst über Vollzugriff zum Speichern und Verwalten der Schlüssel verfügt. Während einige Kunden möglicherweise die Schlüssel verwalten möchten, weil sie das Gefühl haben, dass sie so eine höhere Sicherheit erreichen können, sollten die Kosten und das Risiko, die mit einer kundenspezifischen Schlüsselspeicherlösung verbunden sind, bei der Bewertung eines Modells berücksichtigt werden. In vielen Fällen kann eine Organisation zu dem Schluss kommen, dass Ressourceneinschränkungen oder die Risiken einer lokalen Lösung höher sind als das Risiko der Cloudverwaltung der Schlüssel für die Verschlüsselung ruhender Daten.  Dieses Modell ist jedoch möglicherweise nicht für Organisationen ausreichend, die die Erstellung oder die Lebensdauer der Verschlüsselungsschlüssel steuern müssen, oder in denen die Verschlüsselungsschlüssel eines Diensts nicht von denselben Mitarbeitern verwaltet werden, die den Dienst verwalten (d. h. in Fällen, in denen die Schlüsselverwaltung vom allgemeinen Verwaltungsmodell des Diensts getrennt erfolgt).

### <a name="key-access"></a>Schlüsselzugriff

Wenn die serverseitige Verschlüsselung mit vom Dienst verwalteten Schlüsseln verwendet wird, werden die Erstellung und Speicherung von Schlüsseln sowie der Dienstzugriff vollständig vom Dienst verwaltet. Üblicherweise speichert der grundlegende Azure-Ressourcenanbieter die DEKs in einem Speicher, der sich in der Nähe der Daten befindet und schnell verfügbar und zugreifbar ist, während die KEKs in einem sicheren internen Speicher gespeichert werden.

**Vorteile**

- Einfache Einrichtung
- Microsoft verwaltet die Rotation, Sicherung und Redundanz der Schlüssel.
- Der Kunde muss die Kosten nicht tragen, die mit einer Implementierung oder dem Risiko eines kundenspezifischen Schlüsselverwaltungsschema in Verbindung stehen.

**Nachteile**

- Keine Kundensteuerung der Verschlüsselungsschlüssel (Schlüsselspezifizierung, Lebensdauern, Widerruf usw.)
- Die Schlüsselverwaltung kann nicht vom allgemeinen Verwaltungsmodell für den Dienst getrennt werden

## <a name="server-side-encryption-using-customer-managed-keys-in-azure-key-vault"></a>Serverseitige Verschlüsselung mit vom Kunden verwalteten Schlüsseln in Azure Key Vault

In Szenarien, in denen es erforderlich ist, die ruhenden Daten zu verschlüsseln und die Verschlüsselungsschlüssel zu steuern, können Kunden die serverseitige Verschlüsselung mit vom Kunden verwalteten Schlüsseln in Key Vault verwenden. Einige Diensten speichern möglicherweise nur den Stamm-KEK in Azure Key Vault und speichern den verschlüsselten DEK in einem internen Speicherort, der sich näher an den Daten befindet. In diesem Szenario können Kunden Ihre eigenen Schlüssel in Key Vault mitbringen (BYOK: Bring Your Own Key) oder neue Schlüssel generieren und diese verwenden, um die gewünschten Ressourcen zu verschlüsseln. Während der Ressourcenanbieter die Ver- und Entschlüsselungsvorgänge durchführt, verwendet er den konfigurierten KEK als Stammschlüssel für alle Verschlüsselungsvorgänge.

Der Verlust von KEKs bedeutet, dass Daten verloren gehen. Aus diesem Grund sollten Schlüssel nicht gelöscht werden. Schlüssel sollten immer dann gesichert werden, wenn sie erstellt oder gedreht werden. [Vorläufiges Löschen](../../key-vault/general/soft-delete-overview.md) sollte in jedem Tresor aktiviert werden, in dem KEKs gespeichert werden. Anstatt einen Schlüssel zu löschen, legen Sie die Aktivierungsoption auf „False“ fest, oder legen Sie das Ablaufdatum fest.

### <a name="key-access"></a>Schlüsselzugriff

Beim Modell der serverseitigen Verschlüsselung mit vom Kunden verwalteten Schlüsseln in Azure Key Vault greift der Dienst auf die Schlüssel zu, um nach Bedarf Ver- und Entschlüsselungen durchzuführen. Schlüssel für die Verschlüsselung ruhender Daten werden einem Dienst über eine Richtlinie zur Zugriffssteuerung verfügbar gemacht. Diese Richtlinie gewährt dem Dienst Identitätszugriff, um Schlüssel zu erhalten. Ein Azure-Dienst, der für ein verknüpftes Abonnement ausgeführt wird, kann mit einer Identität in diesem Abonnement konfiguriert werden. Der Dienst kann die Authentifizierung mit Azure AD durchführen und ein Authentifizierungstoken erhalten, dass ihn als diesen Dienst identifiziert, der im Namen des Abonnements handelt. Dieses Token kann dann Key Vault gezeigt werden, um einen Schlüssel zu erhalten, auf das es Zugriff hat.

Für Vorgänge mit Verschlüsselungsschlüsseln kann einer Dienstidentität Zugriff auf jeden der folgenden Vorgänge gewährt werden: decrypt, encrypt, unwrapKey, wrapKey, verify, sign, get, list, update, create, import, delete, backup, and restore.

Um einen Schlüssel zum Ver- oder Entschlüsseln von ruhenden Daten abzurufen, muss die Dienstidentität, als die die Dienstinstanz des Ressourcen-Managers ausgeführt wird, „UnwrapKey“ (zum Abrufen den Schlüssels für die Entschlüsselung) und „WrapKey“ (um einen Schlüssel beim Erstellen eines neuen Schlüssels in den Schlüsseltresor einzufügen) aufweisen.

>[!NOTE]
>Weitere Informationen zur Authentifizierung bei Key Vault finden Sie auf der Seite „Sichern Ihres Schlüsseltresors“ in der [Dokumentation zu Azure Key Vault](../../key-vault/general/secure-your-key-vault.md).

**Vorteile**

- Vollständige Steuerung der verwendeten Schlüssel: Verschlüsselungsschlüssel werden im Key Vault des Kunden unter dessen Kontrolle verwaltet.
- Die Fähigkeit, mehrere Dienste für einen Master zu verschlüsseln
- Die Schlüsselverwaltung kann vom allgemeinen Verwaltungsmodell für den Dienst getrennt werden.
- Der Dienst- und Schlüsselspeicherort kann regionsübergreifend definiert werden.

**Nachteile**

- Der Kunde trägt die volle Verantwortung für die Schlüsselzugriffsverwaltung
- Der Kunde trägt die volle Verantwortung für die Lebensdauerverwaltung des Schlüssels
- Zusätzlicher Aufwand für die Einrichtung und die Konfiguration

## <a name="server-side-encryption-using-customer-managed-keys-in-customer-controlled-hardware"></a>Serverseitige Verschlüsselung mit vom Kunden verwalteten Schlüsseln auf kundengesteuerter Hardware

Einige Azure-Dienste ermöglichen das Schlüsselverwaltungsmodell HYOK (Host Your Own Key, Eigenen Schlüssel hosten). Dieser Verwaltungsmodus ist in Szenarios nützlich, in denen es erforderlich ist, die ruhenden Daten zu verschlüsseln und die Schlüssel in einem geschützten Repository außerhalb der Kontrolle von Microsoft zu verwalten. In diesem Modell muss der Dienst den Schlüssel von einer externen Website abrufen. Leistungs- und Verfügbarkeitsgarantien werden beeinträchtigt, und die Konfiguration ist komplexer. Zusätzlich ähneln die allgemeinen Sicherheitsgarantien für dieses Modell denen für eine Umgebung, in der die Schlüssel vom Kunden in Azure Key Vault verwaltet werden, weil der Dienst bei Verschlüsselungs- und Entschlüsselungsvorgängen Zugriff auf den DEK hat.  Daraus folgt, dass das Modell für die meisten Organisationen ungeeignet ist, es sei denn, sie haben besondere Schlüsselverwaltungsanforderungen. Aufgrund dieser Einschränkungen unterstützen die meisten Azure-Dienste nicht die serverseitige Verschlüsselung mit vom Server verwalteten Schlüsseln auf von Kunden verwalteter Hardware.

### <a name="key-access"></a>Schlüsselzugriff

Wenn die serverseitige Verschlüsselung mit vom Server verwalteten Schlüsseln auf von Kunden verwalteter Hardware verwendet wird, werden die Schlüssel in einem vom Kunden konfigurierten System verwaltet. Azure-Dienste, die dieses Modell unterstützen, bieten eine Möglichkeit, eine sichere Verbindung mit einem von Kunden bereitgestellten Schlüsselspeicher herzustellen.

**Vorteile**

- Volle Steuerung des verwendeten Stammschlüssels: Verschlüsselungsschlüssel werden von einem vom Kunden bereitgestellten Speicher verwaltet.
- Die Fähigkeit, mehrere Dienste für einen Master zu verschlüsseln
- Die Schlüsselverwaltung kann vom allgemeinen Verwaltungsmodell für den Dienst getrennt werden.
- Der Dienst- und Schlüsselspeicherort kann regionsübergreifend definiert werden.

**Nachteile**

- Volle Verantwortung für den Speicher, die Sicherheit, Leistung und Verfügbarkeit der Schlüssel
- Volle Verantwortung für die Schlüsselzugriffsverwaltung
- Volle Verantwortung für die Lebensdauerverwaltung des Schlüssels
- Erhebliche Kosten für die Einrichtung, Konfiguration und fortlaufende Wartung
- Verstärkte Abhängigkeit von der Netzwerkverfügbarkeit zwischen den Rechenzentrum des Kunden und den Azure-Rechenzentren

## <a name="supporting-services"></a>Unterstützende Dienste
Die Azure-Dienste, die die einzelnen Verschlüsselungsmodelle unterstützen:

| Produkt, Feature oder Dienst | Serverseitig mit vom Dienst verwaltetem Schlüssel   | Serverseitig mit kundenseitig verwaltetem Schlüssel | Clientseitig mit vom Client verwaltetem Schlüssel  |
|----------------------------------|--------------------|-----------------------------------------|--------------------|
| **KI und Machine Learning**      |                    |                    |                    |
| Azure Cognitive Search           | Ja                | Ja                | -                  |
| Azure Cognitive Services         | Ja                | Ja                | -                  |
| Azure Machine Learning           | Ja                | Ja                | -                  |
| Azure Machine Learning Studio (klassisch) | Ja         | RSA 2048 Bit, Vorschauversion | -               |
| Content Moderator                | Ja                | Ja                | -                  |
| Gesicht                             | Ja                | Ja                | -                  |
| Language Understanding           | Ja                | Ja                | -                  |
| Personalisierung                     | Ja                | Ja                | -                  |
| QnA Maker                        | Ja                | Ja                | -                  |
| Spracherkennungsdienste                  | Ja                | Ja                | -                  |
| Textübersetzung                  | Ja                | Ja                | -                  |
| Power BI                         | Ja                | Ja, RSA 4.096 Bit  | -                  |
| **Analyse**                    |                    |                    |                    |
| Azure Stream Analytics           | Ja                | Ja\*\*            | -                  |
| Event Hubs                       | Ja                | Ja                | -                  |
| Functions                        | Ja                | Ja                | -                  |
| Azure Analysis Services          | Ja                | -                  | -                  |
| Azure Data Catalog               | Ja                | -                  | -                  |
| Azure HDInsight                  | Ja                | All                | -                  |
| Azure Monitor Application Insights | Ja                | Ja                | -                  |
| Azure Monitor Log Analytics      | Ja                | Ja                | -                  |
| Azure-Daten-Explorer              | Ja                | Ja                | -                  |
| Azure Data Factory               | Ja                | Ja                | -                  |
| Azure Data Lake Store            | Ja                | Ja, RSA 2048 Bit  | -                  |
| **Container**                   |                    |                    |                    |
| Azure Kubernetes Service         | Ja                | Ja                | -                  |
| Container Instances              | Ja                | Ja                | -                  |
| Containerregistrierung               | Ja                | Ja                | -                  |
| **Compute**                      |                    |                    |                    |
| Virtual Machines                 | Ja                | Ja                | -                  |
| VM-Skalierungsgruppe        | Ja                | Ja                | -                  |
| SAP HANA                         | Ja                | Ja                | -                  |
| App Service                      | Ja                | Ja\*\*            | -                  |
| Automation                       | Ja                | Ja\*\*            | -                  |
| Azure-Funktionen                  | Ja                | Ja\*\*            | -                  |
| Azure-Portal                     | Ja                | Ja\*\*            | -                  |
| Logic Apps                       | Ja                | Ja                | -                  |
| Verwaltete Azure-Anwendungen       | Ja                | Ja\*\*            | -                  |
| Service Bus                      | Ja                | Ja                | -                  |
| Site Recovery                    | Ja                | Ja                | -                  |
| **Datenbanken**                    |                    |                    |                    |
| SQL Server auf virtuellen Computern   | Ja                | Ja                | Ja                |
| Azure SQL-Datenbank               | Ja                | Ja, RSA 3072 Bit  | Ja                |
| Azure SQL-Datenbank for MariaDB   | Ja                | -                  | -                  |
| Azure SQL-Datenbank for MySQL     | Ja                | Ja                | -                  |
| Azure SQL-Datenbank for PostgreSQL | Ja               | Ja                | -                  |
| Azure Synapse Analytics          | Ja                | Ja, RSA 3072 Bit  | -                  |
| SQL Server Stretch Database      | Ja                | Ja, RSA 3072 Bit  | Ja                |
| Table Storage                    | Ja                | Ja                | Ja                |
| Azure Cosmos DB                  | Ja                | Ja                | -                  |
| Azure Databricks                 | Ja                | Ja                | -                  |
| Azure Database Migration Service | Ja                | N/V\*              | -                  |
| **DevOps**                       |                    |                    |                    |
| Azure DevOps Services            | Ja                | -                  | Ja                |
| Azure Repos                      | Ja                | -                  | Ja                |
| **Identität**                     |                    |                    |                    |
| Azure Active Directory           | Ja                | -                  | -                  |
| Azure Active Directory Domain Services | Ja          | Ja                | -                  |
| **Integration**                  |                    |                    |                    |
| Service Bus                      | Ja                | Ja                | Ja                |
| Event Grid                       | Ja                | -                  | -                  |
| API Management                   | Ja                | -                  | -                  |
| **IoT-Dienste**                 |                    |                    |                    |
| IoT Hub                          | Ja                | Ja                | Ja                |
| IoT Hub Device Provisioning      | Ja                | Ja                | -                  |
| **Verwaltung und Governance**    |                    |                    |                    |
| Azure Site Recovery              | Ja                | -                  | -                  |
| Azure Migrate                    | Ja                | Ja                | -                  |
| **Medien**                        |                    |                    |                    |
| Media Services                   | Ja                | Ja                | Ja                |
| **Security**                     |                    |                    |                    |
| Azure Security Center für IoT    | Ja                | Ja                | -                  |
| Azure Sentinel                   | Ja                | Ja                | -                  |
| **Storage**                      |                    |                    |                    |
| Blob Storage                     | Ja                | Ja                | Ja                |
| Blob Storage Premium             | Ja                | Ja                | Ja                |
| Disk Storage                     | Ja                | Ja                | -                  |
| Disk Storage Ultra               | Ja                | Ja                | -                  |
| Speicher für verwaltete Datenträger             | Ja                | Ja                | -                  |
| File Storage                     | Ja                | Ja                | -                  |
| Storage Premium für Dateien             | Ja                | Ja                | -                  |
| Dateisynchronisierung                        | Ja                | Ja                | -                  |
| Queue Storage                    | Ja                | Ja                | Ja                |
| Avere vFXT                       | Ja                | -                  | -                  |
| Azure Cache for Redis            | Ja                | N/V\*              | -                  |
| Azure NetApp Files               | Ja                | Ja                | -                  |
| Archivspeicher                  | Ja                | Ja                | -                  |
| StorSimple                       | Ja                | Ja                | Ja                |
| Azure Backup                     | Ja                | Ja                | Ja                |
| Data Box                         | Ja                | -                  | Ja                |
| Data Box Edge                    | Ja                | Ja                | -                  |

\* Dieser Dienst speichert keine Daten. Vorübergehende Caches werden ggf. mit einem Microsoft-Schlüssel verschlüsselt.

\*\* Dieser Dienst unterstützt das Speichern von Daten in Ihrem eigenen Schlüsseltresor, Speicherkonto oder einem anderen Dienst zur persistenten Datenspeicherung, der bereits die serverseitige Verschlüsselung mit vom Kunden verwaltetem Schlüssel unterstützt.

## <a name="next-steps"></a>Nächste Schritte

- Informieren Sie sich über die [Verwendung der Verschlüsselung in Azure](encryption-overview.md).
- Lesen Sie mehr dazu, wie mithilfe der [doppelten Verschlüsselung](double-encryption.md) Bedrohungen in Azure minimiert werden, die mit der Datenverschlüsselung einhergehen.
