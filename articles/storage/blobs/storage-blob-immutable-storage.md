---
title: Unveränderlicher Blobspeicher – Azure Storage
description: Azure Storage bietet WORM-Unterstützung (Write Once, Read Many – Einmal schreiben, oft lesen) für Blobspeicher (Objektspeicher), der Benutzern das Speichern in einem nicht löschbaren und nicht änderbaren Zustand für einen angegebenen Zeitraum ermöglicht.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 11/13/2020
ms.author: tamram
ms.reviewer: hux
ms.subservice: blobs
ms.openlocfilehash: acb2ebb0d7ce70c6b5963a8a6c3e392091e4bb1e
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96010060"
---
# <a name="store-business-critical-blob-data-with-immutable-storage"></a>Speichern unternehmenskritischer Blobdaten mit unveränderlichem Speicher

Unveränderlicher Speicher für Azure-Blobspeicher ermöglicht es Benutzern, unternehmenskritische Datenobjekte im WORM-Zustand (Write Once, Read Many – Einmal schreiben, oft lesen) zu speichern. In diesem Zustand sind die Daten für einen vom Benutzer angegebenen Zeitraum nicht löschbar und nicht änderbar. Während des Aufbewahrungszeitraums können Blobs erstellt und gelesen, aber nicht geändert oder gelöscht werden. Unveränderlicher Speicher steht für Universell V1-, Universell V2-, BlobStorage- und BlockBlobStorage-Konten in allen Azure-Regionen zur Verfügung.

Informationen zum Festlegen und Aufheben einer Aufbewahrung für juristische Zwecke sowie zum Erstellen einer zeitbasierten Aufbewahrungsrichtlinie über das Azure-Portal, per PowerShell oder mithilfe der Azure-Befehlszeilenschnittstelle finden Sie unter [Festlegen und Verwalten von Unveränderlichkeitsrichtlinien für Blobspeicher](storage-blob-immutability-policies-manage.md).

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="about-immutable-blob-storage"></a>Informationen zu unveränderlichem Blobspeicher

Unveränderlicher Speicher unterstützt Organisationen im Gesundheitswesen sowie Finanzinstitute und ähnliche Branchen (insbesondere Broker-Dealer-Organisationen) bei der sicheren Speicherung von Daten. Darüber hinaus kann unveränderlicher Speicher in allen Szenarien verwendet werden, in denen kritische Daten vor Änderung oder Löschung geschützt werden sollen.

Beispiele für typische Anwendungen:

- **Einhaltung gesetzlicher Bestimmungen**: Unveränderlicher Speicher für Azure-Blobspeicher unterstützt Organisationen dabei, SEC 17a-4(f), CFTC 1.31(d), FINRA und weitere Bestimmungen einzuhalten. In einem technischen Whitepaper von Cohasset Associates ist ausführlich beschrieben, wie diese gesetzlichen Anforderungen mit unveränderlichem Speicher erfüllt werden können. Sie können das Whitepaper über das [Microsoft-Vertrauensstellungsportal](https://aka.ms/AzureWormStorage) herunterladen. Das [Azure Trust Center](https://www.microsoft.com/trustcenter/compliance/compliance-overview) enthält ausführliche Informationen zu den Compliancezertifizierungen.

- **Sichere Dokumentaufbewahrung**: Mit unveränderlichem Speicher für Azure-Blobspeicher wird sichergestellt, dass Daten von Benutzern auch dann nicht geändert oder gelöscht werden können, wenn diese über Berechtigungen für die Kontoverwaltung verfügen.

- **Gesetzliche Aufbewahrungspflicht**: Mit unveränderlichem Speicher für Azure-Blobspeicher können Benutzer sensible Informationen, die für Rechtsstreitigkeiten oder die geschäftliche Nutzung wichtig sind, für den gewünschten Zeitraum in einem manipulationsgeschütztem Zustand speichern, bis die Aufbewahrungspflicht nicht mehr gilt. Dieses Feature ist nicht allein auf rechtliche Anwendungsfälle beschränkt, sondern kann auch als ereignisbasierter Aufbewahrungsvorgang oder Unternehmenssperre angesehen werden, wenn Daten basierend auf Ereignisauslösern oder Unternehmensrichtlinien geschützt werden müssen.

Unveränderlicher Speicher unterstützt folgende Features:

- **[Unterstützung einer zeitbasierten Aufbewahrungsrichtlinie](#time-based-retention-policies)** : Benutzer können Richtlinien für die Speicherung von Daten für einen bestimmten Zeitraum festlegen. Wenn eine zeitbasierte Aufbewahrungsrichtlinie festgelegt wird, können Blobs erstellt und gelesen, aber nicht geändert oder gelöscht werden. Nach Ablauf des Aufbewahrungszeitraums können Blobs gelöscht, aber nicht überschrieben werden.

- **[Unterstützung einer Richtlinie zur gesetzlichen Aufbewahrungspflicht](#legal-holds)** : Ist der Aufbewahrungszeitraum nicht bekannt, können Benutzer eine Aufbewahrung für juristische Zwecke festlegen, um unveränderliche Daten zu speichern, bis die Aufbewahrung für juristische Zwecke aufgehoben wird.  Wenn eine Richtlinie für die gesetzliche Aufbewahrungspflicht festgelegt wird, können Blobs erstellt und gelesen, aber nicht geändert oder gelöscht werden. Jeder gesetzlichen Aufbewahrungspflicht ist ein benutzerdefiniertes alphanumerisches Tag zugeordnet (z. B. Fall-ID, Ereignisname usw.), das als Bezeichnerzeichenfolge verwendet wird. 

- **Unterstützung für alle Blobebenen**: WORM-Richtlinien sind unabhängig von der Azure-Blobspeicherebene und gelten für alle Ebenen: „Heiß“, „Kalt“ und „Archiv“. Benutzer können Daten in die Ebene überführen, für die die Workloadkosten bestmöglich optimiert sind, während gleichzeitig die Unveränderlichkeit der Daten sichergestellt ist.

- **Konfiguration auf Containerebene**: Benutzer können zeitbasierte Aufbewahrungsrichtlinien und Tags für gesetzliche Aufbewahrungspflichten auf Containerebene konfigurieren. Mit den Einstellungen auf Containerebene können Benutzer zeitbasierte Aufbewahrungsrichtlinien erstellen und sperren, Aufbewahrungszeiträume verlängern, Zeiträume für gesetzliche Aufbewahrungspflichten festlegen und aufheben usw. Diese Richtlinien gelten für alle Blobs im Container (vorhandene und neue).

- **Unterstützung der Überwachungsprotokollierung**: Jeder Container enthält ein Richtlinien-Überwachungsprotokoll. Es enthält bis zu sieben zeitbasierte Aufbewahrungsbefehle für gesperrte zeitbasierte Aufbewahrungsrichtlinien sowie die Benutzer-ID, den Befehlstyp, Zeitstempel und den Aufbewahrungszeitraum. Für Zeiträume zur gesetzlichen Aufbewahrungspflicht enthält das Protokoll Benutzer-ID, Befehlstyp, Zeitstempel und die entsprechenden Tags. Dieses Protokoll wird für die Lebensdauer der Richtlinie gemäß den SEC 17a-4(f)-Bestimmungsrichtlinien aufbewahrt. Im [Azure-Aktivitätsprotokoll](../../azure-monitor/platform/platform-logs-overview.md) werden umfassendere Protokolldaten mit allen Aktivitäten auf Steuerungsebene angezeigt. Wenn Sie [Azure-Ressourcenprotokolle](../../azure-monitor/platform/platform-logs-overview.md) aktivieren, werden dagegen nur Vorgänge auf Datenebene aufbewahrt und angezeigt. Der Benutzer ist für die dauerhafte Speicherung dieser Protokolle verantwortlich, die aus gesetzlichen oder anderen Gründen ggf. erforderlich ist.

## <a name="how-it-works"></a>Funktionsweise

Unveränderlicher Speicher für Azure-Blobspeicher unterstützt zwei Arten von WORM-Richtlinien bzw. Richtlinien für die unveränderliche Speicherung: zeitbasierte Aufbewahrung und gesetzliche Aufbewahrungspflicht. Wenn eine zeitbasierte Aufbewahrungsrichtlinie oder ein Zeitraum für die gesetzliche Aufbewahrungspflicht auf einen Container angewendet wird, werden alle vorhandenen Blobs innerhalb von weniger als 30 Sekunden in einen unveränderlichen WORM-Zustand versetzt. Alle neuen Blobs, die in den durch die Richtlinie geschützten Container hochgeladen werden, werden ebenfalls in einen unveränderlichen Zustand versetzt. Wenn sich alle Blobs in einem unveränderlichen Zustand befinden, wird die Unveränderlichkeitsrichtlinie bestätigt, und alle Überschreibungs- oder Löschvorgänge im unveränderlichen Container sind unzulässig.

Container und Speicherkonten können außerdem nicht gelöscht werden, wenn der Container Blobs enthält, die durch eine Richtlinie für die Aufbewahrung für juristische Zwecke oder eine gesperrte zeitbasierte Richtlinie geschützt sind. Eine Richtlinie für die Aufbewahrung für juristische Zwecke schützt vor der Löschung von Blobs, Containern und Speicherkonten. Entsperrte sowie gesperrte zeitbasierte Richtlinien schützen vor der Löschung von Blobs im angegebenen Zeitraum. Entsperrte und gesperrte zeitbasierte Richtlinien schützen nur vor der Löschung von Containern, wenn im entsprechenden Container mindestens ein Blob vorhanden ist. Nur bei einem Container mit einer *gesperrten* zeitbasierten Richtlinie sind Speicherkonten vor der Löschung geschützt. Container mit entsperrten zeitbasierten Richtlinien bieten weder Schutz vor der Löschung von Speicherkonten noch Compliance.

Weitere Informationen zum Festlegen und Sperren zeitbasierter Aufbewahrungsrichtlinien finden Sie unter [Festlegen und Verwalten von Unveränderlichkeitsrichtlinien für Blobspeicher](storage-blob-immutability-policies-manage.md).

## <a name="time-based-retention-policies"></a>Zeitbasierte Aufbewahrungsrichtlinien

> [!IMPORTANT]
> Eine zeitbasierte Aufbewahrungsrichtlinie muss *gesperrt* sein, damit das Blob zur Erzielung von Konformität mit SEC 17a-4(f) und anderen gesetzlichen Bestimmungen in einem konformen unveränderlichen Zustand ist (Schreib- und Löschschutz). Sie sollten die Richtlinie in einem angemessenen Zeitraum sperren – in der Regel weniger als 24 Stunden. Der ursprüngliche Zustand einer angewendeten zeitbasierten Aufbewahrungsrichtlinie lautet *Entsperrt*, damit Sie das Feature testen und Änderungen an der Richtlinie vornehmen können, bevor Sie das Sperren durchführen. Der Zustand *Entsperrt* bietet zwar Unveränderlichkeitsschutz, aber wir empfehlen Ihnen, *Entsperrt* nicht für andere Zwecke als für kurzfristige Featuretests zu verwenden. 

Wenn eine zeitbasierte Aufbewahrungsrichtlinie auf einen Container angewendet wird, bleiben alle Blobs im Container so lange im unveränderlichen Zustand, wie der Aufbewahrungszeitraum *gilt*. Die Gültigkeit des Aufbewahrungszeitraums für Blobs entspricht der Differenz zwischen der **Erstellungszeit** des Blobs und dem vom Benutzer angegebenen Aufbewahrungszeitraum. Da Benutzer den Aufbewahrungszeitraum verlängern können, nutzt unveränderlicher Speicher den letzten Wert des vom Benutzer angegebenen Aufbewahrungszeitraums, um den effektiven Aufbewahrungszeitraum zu berechnen.

Ein Beispiel: Angenommen, der Benutzer erstellt eine zeitbasierte Aufbewahrungsrichtlinie mit einem Aufbewahrungszeitraum von fünf Jahren. Ein in diesem Container vorhandenes Blob (_testblob1_) wurde vor einem Jahr erstellt. Der effektive Aufbewahrungszeitraum für _testblob1_ beträgt also vier Jahre. Wenn ein neues Blob (_testblob2_) in den Container hochgeladen wird, beträgt der effektive Aufbewahrungszeitraum für _testblob2_ fünf Jahre ab dem Zeitpunkt seiner Erstellung.

Eine zeitbasierte Aufbewahrungsrichtlinie im Zustand „Entsperrt“ wird nur für Featuretests empfohlen, und eine Richtlinie muss gesperrt sein, um mit SEC 17a-4(f) konform zu sein und andere gesetzliche Bestimmungen zu erfüllen. Nachdem eine zeitbasierte Aufbewahrungsrichtlinie gesperrt wurde, kann die Richtlinie nicht mehr entfernt werden, und es sind maximal fünf Erhöhungen des effektiven Aufbewahrungszeitraums zulässig.

Für Aufbewahrungsrichtlinien gelten folgende Grenzwerte:

- Ein Speicherkonto kann über maximal 10.000 Container mit gesperrten zeitbasierten Unveränderlichkeitsrichtlinien verfügen.
- Der Mindestwert für den Aufbewahrungszeitraum beträgt einen Tag. Der Höchstwert beträgt 146.000 Tage (400 Jahre).
- Für einen Container sind höchstens fünf Bearbeitungen zur Verlängerung eines Aufbewahrungszeitraums gesperrte zeitbasierte unveränderliche Richtlinien möglich.
- Für einen Container werden maximal sieben Überwachungsprotokolle der zeitbasierten Aufbewahrungsrichtlinie für eine gesperrte Richtlinie aufbewahrt.

### <a name="allow-protected-append-blobs-writes"></a>Zulassen von Schreibvorgängen in geschützten Anfügeblobs

Anfügeblobs bestehen aus Datenblöcken und sind für Vorgänge zum Anfügen von Daten optimiert, die in Überprüfungs- und Protokollierungsszenarien erforderlich sind. Bei Anfügeblobs können neue Blöcke entwurfsbedingt nur am Ende des Blobs hinzugefügt werden. Unabhängig von der Unveränderlichkeit ist das Ändern oder Löschen vorhandener Blöcke in einem Anfügeblob grundsätzlich nicht zulässig. Weitere Informationen zu Anfügeblobs finden Sie unter [Informationen zu Anfügeblobs](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs).

Nur zeitbasierte Aufbewahrungsrichtlinien enthalten eine `allowProtectedAppendWrites`-Einstellung, über die das Schreiben neuer Blöcke in ein Anfügeblob ermöglicht wird und gleichzeitig der Unveränderlichkeitsschutz und die Konformität aufrechterhalten werden. Wenn diese Einstellung aktiviert ist, können Sie ein Anfügeblob direkt in dem durch Richtlinien geschützten Container erstellen und über die *AppendBlock*-API weitere neue Datenblöcke am Ende von vorhandenen Anfügeblobs hinzufügen. Es können nur neue Blöcke hinzugefügt werden. Vorhandene Blöcke können nicht geändert oder gelöscht werden. Der Unveränderlichkeitsschutz für die Aufbewahrungszeit gilt weiterhin und verhindert die Löschung des Anfügeblobs bis der geltende Aufbewahrungszeitraum abgelaufen ist. Die Aktivierung dieser Einstellung wirkt sich nicht auf das Unveränderlichkeitsverhalten von Blockblobs oder Seitenblobs aus.

Da diese Einstellung zu einer zeitbasierten Aufbewahrungsrichtlinie gehört, bleiben die Anfügeblobs so lange im unveränderlichen Zustand, wie der Aufbewahrungszeitraum *gilt*. Da neue Daten auch nach der anfänglichen Erstellung des Anfügeblobs angefügt werden können, gibt es bei der Festlegung des Aufbewahrungszeitraums einen geringfügigen Unterschied. Die Gültigkeit des Aufbewahrungszeitraums entspricht der Differenz zwischen dem **Zeitpunkt der letzten Änderung** des Blobs und dem vom Benutzer angegebenen Aufbewahrungszeitraum. Entsprechend wird beim Verlängern des Aufbewahrungszeitraums für den unveränderlichen Speicher der letzte Wert des vom Benutzer angegebenen Aufbewahrungszeitraums verwendet, um den effektiven Aufbewahrungszeitraum zu berechnen.

Ein Beispiel: Angenommen, ein Benutzer erstellt eine zeitbasierte Aufbewahrungsrichtlinie mit aktivierter `allowProtectedAppendWrites`-Einstellung und einem Aufbewahrungszeitraum von 90 Tagen. Ein Anfügeblob (_logblob1_) wird am aktuellen Datum im Container erstellt. Dem Anfügeblob werden während der nächsten 10 Tage weiterhin neue Protokolle hinzugefügt. Der effektive Aufbewahrungszeitraum für _logblob1_ beträgt also 100 Tage ab dem aktuellen Datum (Zeitpunkt der letzten Anfügung + 90 Tage).

Bei entsperrten zeitbasierten Aufbewahrungsrichtlinien kann die `allowProtectedAppendWrites`-Einstellung zu jedem Zeitpunkt aktiviert und deaktiviert werden. Nachdem die zeitbasierte Aufbewahrungsrichtlinie gesperrt wurde, kann die `allowProtectedAppendWrites`-Einstellung nicht mehr geändert werden.

Gesetzliche Aufbewahrungspflichten können `allowProtectedAppendWrites` nicht aktivieren und heben die Eigenschaft allowProtectedAppendWrites auf. Wenn eine Aufbewahrung für juristische Zwecke auf eine zeitbasierte Aufbewahrungsrichtlinie angewandt wird, bei der `allowProtectedAppendWrites` aktiviert ist, kann die *AppendBlock*-API nicht ausgeführt werden, bevor die Aufbewahrung für juristische Zwecke entfernt wird.

## <a name="legal-holds"></a>Gesetzliche Aufbewahrungspflichten

Zeiträume zur Aufbewahrung für juristische Zwecke sind Zeiträume zur temporären Aufbewahrung, die für rechtliche Untersuchungen oder allgemeine Schutzrichtlinien verwendet werden können. Jede Richtlinie für die Aufbewahrung für juristische Zwecke muss einem oder mehreren Tags zugeordnet sein. Tags werden als benannte Bezeichner verwendet, z. B. Fall-ID oder Ereignis, um den Zweck der Aufbewahrung zu kategorisieren und zu beschreiben.

Für einen Container können gleichzeitig eine gesetzliche Aufbewahrungspflicht und eine zeitbasierte Aufbewahrungsrichtlinie gelten. Alle Blobs in diesem Container verbleiben so lange im unveränderlichen Zustand, bis alle gesetzlichen Aufbewahrungspflichten aufgehoben wurden. Dies gilt auch, wenn die effektive Aufbewahrungsdauer bereits abgelaufen ist. Dagegen verbleibt ein Blob auch dann so lange im unveränderlichen Zustand, bis der effektive Aufbewahrungszeitraum abgelaufen ist, wenn alle Zeiträume für die gesetzliche Aufbewahrungspflicht aufgehoben wurden.

Im Zusammenhang mit der Aufbewahrung für juristische Zwecke gelten folgende Grenzwerte:

- Ein Speicherkonto kann über maximal 10.000 Container mit einer Einstellung zur Aufbewahrung für juristische Zwecke verfügen.
- Ein Container kann über maximal 10 Tags für die gesetzliche Aufbewahrungspflicht verfügen.
- Ein Tag für die Aufbewahrung für juristische Zwecke muss mindestens drei alphanumerische Zeichen lang sein. Die Höchstlänge beträgt 23 alphanumerische Zeichen.
- Für einen Container werden höchstens zehn Richtlinien-Überwachungsprotokolle für die gesetzliche Aufbewahrungspflicht für die Dauer der Richtlinie aufbewahrt.

## <a name="scenarios"></a>Szenarien

Die folgende Tabelle gibt Aufschluss über die Arten von Blobspeichervorgängen, die für die verschiedenen Unveränderlichkeitsszenarien deaktiviert sind. Weitere Informationen finden Sie in der Dokumentation [Blob-Dienst-REST-API](/rest/api/storageservices/blob-service-rest-api).

| Szenario | Blobzustand | Verweigerte Blobvorgänge | Container- und Kontoschutz |
|--|--|--|--|
| Effektiver Aufbewahrungszeitraum für das Blob ist noch nicht abgelaufen bzw. ein Zeitraum für die gesetzliche Aufbewahrungspflicht wurde festgelegt | Unveränderlich: Lösch- und Schreibschutz | Put Blob<sup>1</sup>, Put Block<sup>1</sup>, Put Block List<sup>1</sup>, Delete Container, Delete Blob, Set Blob Metadata, Put Page, Set Blob Properties, Snapshot Blob, Incremental Copy Blob, Append Block<sup>2</sup> | Containerlöschung verweigert, Speicherkontenlöschung verweigert |
| Effektiver Aufbewahrungszeitraum für das Blob ist abgelaufen, und kein Zeitraum zur Aufbewahrung für juristische Zwecke ist festgelegt | Nur Schreibschutz (Löschvorgänge sind zulässig) | Put Blob<sup>1</sup>, Put Block<sup>1</sup>, Put Block List<sup>1</sup>, Set Blob Metadata, Put Page, Set Blob Properties, Snapshot Blob, Incremental Copy Blob, Append Block<sup>2</sup> | Die Löschung von Containern wird verweigert, wenn mindestens ein Blob im geschützten Container vorhanden ist. Die Löschung von Speicherkonten wird nur bei *gesperrten* zeitbasierten Richtlinien verweigert. |
| Keine WORM-Richtlinie angewandt (keine zeitbasierte Aufbewahrung und kein Tag für die Aufbewahrung für juristische Zwecke) | Veränderlich | Keine | Keine |

<sup>1</sup> Der Blob-Dienst lässt diese Vorgänge zu, um einmalig ein neues Blob zu erstellen. Alle nachfolgenden Überschreibungsvorgänge in einem vorhandenen Blobpfad eines unveränderlichen Containers sind nicht zulässig.

<sup>2</sup> „Append Block“ ist nur bei zeitbasierten Aufbewahrungsrichtlinien mit aktivierter `allowProtectedAppendWrites`-Eigenschaft zulässig. Weitere Informationen finden Sie im Abschnitt [Zulassen von Schreibvorgängen in geschützten Anfügeblobs](#allow-protected-append-blobs-writes).

> [!IMPORTANT]
> Einige Workloads, z. B. [SQL Server-Sicherung über URLs](/sql/relational-databases/backup-restore/sql-server-backup-to-url), erstellen ein Blob und fügen es dann hinzu. Wenn für den Container eine aktive zeitbasierte Aufbewahrungsrichtlinie oder eine gesetzliche Aufbewahrungspflicht gilt, wird dieses Muster nicht erfolgreich ausgeführt.

## <a name="pricing"></a>Preise

Für die Nutzung dieses Features fallen keine zusätzlichen Gebühren an. Unveränderliche Daten werden auf die gleiche Weise abgerechnet wie änderbare Daten. Ausführliche Informationen zu Preisen für Azure Blob Storage finden Sie auf der [Seite mit den Preisen für Azure Storage](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="faq"></a>Häufig gestellte Fragen

**Kann Dokumentation zum Thema WORM-Konformität bereitgestellt werden?**

Ja. Für die Dokumentation der Konformität hat Microsoft ein führendes Unternehmen für unabhängige Bewertungen (Cohasset Associates) beauftragt, das auf Datensatzverwaltung und Information Governance spezialisiert ist. Die Aufgabe bestand darin, unveränderlichen Blobspeicher und dessen Konformität mit den spezifischen Anforderungen der Finanzdienstleistungsbranche zu bewerten. Cohasset hat bestätigt, dass unveränderlicher Blobspeicher bei Verwendung zur Aufbewahrung von zeitbasierten Blobs im WORM-Zustand die relevanten Speicheranforderungen der CFTC-Regel 1.31(c)-(d), der FINRA-Regel 4511 und der SEC-Regel 17a-4 erfüllt. Microsoft hat die Einhaltung dieser Regeln angestrebt, da sie die weltweit strikteste Anleitung zur Aufbewahrung von Datensätzen für Finanzinstitute darstellen. Der Cohasset-Bericht ist im [Microsoft Service Trust Center](https://aka.ms/AzureWormStorage) verfügbar. Wenden Sie sich an den Azure-Support, um von Microsoft einen Nachweis der WORM-Unveränderlichkeitskonformität anzufordern.

**Gilt die Funktion nur für Blockblobs und Anfügeblobs oder auch für Seitenblobs?**

Unveränderlicher Speicher kann mit einem beliebigen Blobtyp verwendet werden, da er auf Containerebene festgelegt wird. Für Container, in denen in erster Linie Blockblobs und Anfügeblobs gespeichert werden, empfehlen wir jedoch die Verwendung von WORM. Vorhandene Blobs in einem Container werden durch eine neu festgelegte WORM-Richtlinie geschützt. Alle neuen Seitenblobs müssen jedoch außerhalb des WORM-Containers erstellt und dann in diesen kopiert werden. Nach dem Kopieren in einen WORM-Container sind keine weiteren Änderungen eines Seitenblobs zulässig. Es wird davon abgeraten, eine WORM-Richtlinie für einen Container festzulegen, in dem VHDs (Seitenblobs) für aktive virtuelle Computer gespeichert sind, da dadurch der VM-Datenträger gesperrt wird. Es empfiehlt sich, die Dokumentation gründlich durchzugehen und Ihre Szenarien zu testen, bevor Sie zeitbasierte Richtlinien sperren.

**Muss ich ein neues Speicherkonto erstellen, um dieses Feature zu nutzen?**

Nein. Sie können unveränderlichen Speicher mit allen vorhandenen oder neu erstellten Universell V1-, Universell V2-, BlobStorage- und BlockBlobStorage-Konten verwenden. Universell V1-Speicherkonten werden zwar unterstützt, es wird aber empfohlen, ein Upgrade auf „Universell V2“ durchzuführen, damit Sie weitere Funktionen nutzen können. Informationen zum Upgraden eines vorhandenen Kontos vom Typ „Universell v1“ finden Sie unter [Durchführen eines Upgrades auf ein Speicherkonto vom Typ „Allgemein v2“](../common/storage-account-upgrade.md).

**Kann ich sowohl eine Richtlinie für eine gesetzliche Aufbewahrungspflicht als auch eine Richtlinie für die zeitbasierte Aufbewahrung anwenden?**

Ja, für einen Container kann gleichzeitig sowohl eine gesetzliche Aufbewahrungspflicht als auch eine zeitbasierte Aufbewahrungsrichtlinie gelten. Die Einstellung allowProtectedAppendWrites gilt jedoch erst, nachdem die gesetzliche Aufbewahrungspflicht aufgehoben wurde. Alle Blobs in diesem Container verbleiben so lange im unveränderlichen Zustand, bis alle gesetzlichen Aufbewahrungspflichten aufgehoben wurden. Dies gilt auch, wenn die effektive Aufbewahrungsdauer bereits abgelaufen ist. Dagegen verbleibt ein Blob auch dann so lange im unveränderlichen Zustand, bis der effektive Aufbewahrungszeitraum abgelaufen ist, wenn alle Zeiträume für die gesetzliche Aufbewahrungspflicht aufgehoben wurden. 

**Gelten Richtlinien für die gesetzliche Aufbewahrungspflicht nur für rechtliche Abläufe, oder gibt es auch andere Nutzungsszenarien?**

Nein. „Aufbewahrung für juristische Zwecke“ ist nur der allgemeine Ausdruck, der für eine nicht zeitbasierte Aufbewahrungsrichtlinie verwendet wird. Er bezieht sich nicht ausschließlich auf Vorgänge im Zusammenhang mit Rechtsstreitigkeiten. Richtlinien zur gesetzlichen Aufbewahrungspflicht sind nützlich, um Überschreibungen und Löschungen zum Schützen von wichtigen WORM-Unternehmensdaten zu deaktivieren, wenn der Aufbewahrungszeitraum unbekannt ist. Sie können sie als Unternehmensrichtlinie zum Schützen Ihrer unternehmenskritischen WORM-Workloads oder als Stagingrichtlinie verwenden, bevor für einen benutzerdefinierten Ereignisauslöser eine zeitbasierte Aufbewahrungsrichtlinie erforderlich ist. 

**Kann ich eine zeitbasierte Aufbewahrungsrichtlinie im Zustand _Gesperrt_ oder eine gesetzliche Aufbewahrungspflicht entfernen?**

Nur entsperrte zeitbasierte Aufbewahrungsrichtlinien können aus einem Container entfernt werden. Nachdem eine zeitbasierte Aufbewahrungsrichtlinie gesperrt wurde, kann sie nicht mehr entfernt werden. Es sind nur Verlängerungen des geltenden Aufbewahrungszeitraums zulässig. Tags für die gesetzliche Aufbewahrungspflicht können gelöscht werden. Wenn alle Tags für die gesetzliche Aufbewahrungspflicht gelöscht werden, kann die gesetzliche Aufbewahrungspflicht entfernt werden.

**Was passiert, wenn ich versuche, einen Container mit einer zeitbasierten Aufbewahrungsrichtlinie oder einem Zeitraum zur Aufbewahrung für juristische Zwecke zu löschen?**

Beim Vorgang „Delete Container“ tritt ein Fehler auf, wenn im Container mindestens ein Blob mit einer gesperrten oder entsperrten zeitbasierten Aufbewahrungsrichtlinie vorhanden ist oder für den Container ein Zeitraum zur Aufbewahrung für juristische Zwecke gilt. Der Vorgang „Delete Container“ wird nur erfolgreich ausgeführt, wenn kein Blob im Container enthalten ist und keine Zeiträume zur Aufbewahrung für juristische Zwecke gelten. 

**Was passiert, wenn ich versuche, ein Speicherkonto mit einem Container zu löschen, der eine zeitbasierte Aufbewahrungsrichtlinie oder einen Zeitraum zur Aufbewahrung für juristische Zwecke enthält?**

Beim Löschen des Speicherkontos tritt ein Fehler auf, wenn mindestens ein Container mit einem festgelegten Zeitraum zur Aufbewahrung für juristische Zwecke oder einer **gesperrten** zeitbasierten Richtlinie vorhanden ist. Bei einem Container mit einer entsperrten zeitbasierten Richtlinie besteht kein Schutz vor der Löschung des Speicherkontos. Sie müssen alle Zeiträume zur Aufbewahrung für juristische Zwecke entfernen und alle **gesperrten** Container löschen, damit Sie das Speicherkonto löschen können. Informationen zum Löschen des Containers finden Sie unter der vorherigen Frage. Außerdem können Sie mit [Azure Resource Manager-Sperren](../../azure-resource-manager/management/lock-resources.md) zusätzlichen Schutz vor der Löschung des Speicherkontos anwenden.

**Kann ich die Daten zwischen unterschiedlichen Blobebenen („Hot“, „Cool“, „Archiv“) verschieben, wenn sich das Blob im unveränderlichen Zustand befindet?**

Ja. Sie können den Befehl „Set Blob Tier“ nutzen, um Daten zwischen den Blobebenen zu verschieben, während die Daten im konformen, unveränderlichen Zustand verbleiben. Unveränderlicher Speicher wird für die Blobebenen „Hot“, „Cool“ und „Archiv“ unterstützt.

**Was passiert, wenn ich nicht bezahle und mein Aufbewahrungszeitraum abgelaufen ist?**

Bei einer fehlenden Zahlung gelten die üblichen Richtlinien der Datenaufbewahrung gemäß den Bedingungen Ihres Vertrags mit Microsoft. Weitere Informationen finden Sie unter [Datenverwaltung bei Microsoft](https://www.microsoft.com/en-us/trust-center/privacy/data-management). 

**Bieten Sie einen Test- oder Karenzzeitraum an, in dem die Funktion ausprobiert werden kann?**

Ja. Wenn eine zeitbasierte Aufbewahrungsrichtlinie erstellt wird, befindet sie sich zunächst im *entsperrten* Zustand. In diesem Zustand können Sie alle gewünschten Änderungen am Aufbewahrungszeitraum vornehmen, z.B. diesen verlängern oder verkürzen und auch die Richtlinie löschen. Nach dem Sperren der Richtlinie bleibt diese gesperrt, bis der Aufbewahrungszeitraums abgelaufen ist. Mit dieser Sperrung der Richtlinie wird verhindert, dass der Aufbewahrungszeitraum gelöscht und geändert wird. Es wird dringend empfohlen, den *entsperrten* Zustand nur für Testzwecke zu nutzen und die Richtlinie innerhalb eines Zeitraums von 24 Stunden wieder zu sperren. Dieses Vorgehen erleichtert Ihnen, SEC 17a-4(f) und andere Bestimmungen einzuhalten.

**Kann ich neben Richtlinien für unveränderliche Blobs das vorläufige Löschen verwenden?**

Ja, wenn das vorläufige Löschen in Ihren Konformitätsanforderungen aktiviert werden kann. Das [vorläufige Löschen für Azure-Blobspeicher](./soft-delete-blob-overview.md) gilt für alle Container eines Speicherkontos, unabhängig von einer Richtlinie für die gesetzliche Aufbewahrungspflicht oder eine zeitbasierte Aufbewahrungsrichtlinie. Wir empfehlen Ihnen, das vorläufige Löschen als zusätzlichen Schutz zu verwenden, bevor Richtlinien für den unveränderlichen WORM-Zustand angewendet und bestätigt werden.

## <a name="next-steps"></a>Nächste Schritte

- [Festlegen und Verwalten von Unveränderlichkeitsrichtlinien für Blobspeicher](storage-blob-immutability-policies-manage.md)
- [Festlegen von Regeln zum automatischen Einstufen und Löschen von Blobdaten über die Lebenszyklusverwaltung](storage-lifecycle-management-concepts.md)
- [Vorläufiges Löschen für Azure Storage-Blobs](./soft-delete-blob-overview.md)
- [Schützen von Abonnements, Ressourcengruppen und Ressourcen mit Azure Resource Manager-Sperren](../../azure-resource-manager/management/lock-resources.md).