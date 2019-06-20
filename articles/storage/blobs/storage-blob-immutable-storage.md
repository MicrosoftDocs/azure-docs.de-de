---
title: Unveränderlicher Speicher für Azure Storage-Blobs | Microsoft-Dokumentation
description: Azure Storage bietet WORM-Unterstützung (Write Once, Read Many – Einmal schreiben, oft lesen) für Blobspeicher (Objektspeicher), der Benutzern das Speichern in einem nicht löschbaren und nicht änderbaren Zustand für einen angegebenen Zeitraum ermöglicht.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 06/01/2019
ms.author: tamram
ms.reviewer: hux
ms.subservice: blobs
ms.openlocfilehash: d58c596421cec2e69210dd39a5d4a9708c154b44
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66492752"
---
# <a name="store-business-critical-data-in-azure-blob-storage"></a>Speichern unternehmenskritischer Daten in Azure-Blobspeicher

Unveränderlicher Speicher für Azure-Blobspeicher ermöglicht es Benutzern, unternehmenskritische Datenobjekte im WORM-Zustand (Write Once, Read Many – Einmal schreiben, oft lesen) zu speichern. In diesem Zustand sind die Daten für einen vom Benutzer angegebenen Zeitraum nicht löschbar und nicht änderbar. Blobobjekte können während des Aufbewahrungszeitraums erstellt und gelesen werden, aber das Ändern oder Löschen ist nicht möglich. Unveränderlicher Speicher ist für Universell v2-Konten und Blobspeicherkonten in allen Azure-Regionen aktiviert.

## <a name="overview"></a>Übersicht

Unveränderlicher Speicher unterstützt Organisationen aus dem Gesundheitswesen, Finanzwesen und verwandten Branchen – insbesondere Broker-Organisationen – dabei, Daten sicher zu speichern. Er kann auch in allen Szenarien verwendet werden, in denen kritische Daten vor Änderungen oder Löschungen geschützt werden sollen. 

Beispiele für typische Anwendungen:

- **Einhaltung gesetzlicher Bestimmungen**: Unveränderlicher Speicher für Azure-Blobspeicher unterstützt Organisationen dabei, SEC 17a-4(f), CFTC 1.31(d), FINRA und weitere Bestimmungen einzuhalten. In einem technischen Whitepaper von Cohasset Associates ist ausführlich beschrieben, wie diese gesetzlichen Anforderungen mit unveränderlichem Speicher erfüllt werden können. Sie können das Whitepaper über das [Microsoft-Vertrauensstellungsportal](https://aka.ms/AzureWormStorage) herunterladen. Das [Azure Trust Center](https://www.microsoft.com/trustcenter/compliance/compliance-overview) enthält ausführliche Informationen zu den Compliancezertifizierungen.

- **Sichere Dokumentaufbewahrung**: Mit unveränderlichem Speicher für Azure-Blobspeicher wird sichergestellt, dass Daten von Benutzern auch dann nicht geändert oder gelöscht werden können, wenn diese über Berechtigungen für die Kontoverwaltung verfügen.

- **Gesetzliche Aufbewahrungspflicht**: Mit unveränderlichem Speicher für Azure-Blobspeicher können Benutzer sensible Informationen, die für Rechtsstreitigkeiten oder die geschäftliche Nutzung wichtig sind, für den gewünschten Zeitraum in einem manipulationsgeschütztem Zustand speichern, bis die Aufbewahrungspflicht nicht mehr gilt. Dieses Feature ist nicht allein auf rechtliche Anwendungsfälle beschränkt, sondern kann auch als ereignisbasierter Aufbewahrungsvorgang oder Unternehmenssperre angesehen werden, wenn Daten basierend auf Ereignisauslösern oder Unternehmensrichtlinien geschützt werden müssen.

Für unveränderlichen Speicher wird Folgendes unterstützt:

- **[Unterstützung einer zeitbasierten Aufbewahrungsrichtlinie](#time-based-retention)** : Benutzer können Richtlinien für die Speicherung von Daten für einen bestimmten Zeitraum festlegen. Wenn eine zeitbasierte Aufbewahrungsrichtlinie festgelegt wird, können Blobs erstellt und gelesen, aber nicht geändert oder gelöscht werden. Nach Ablauf des Aufbewahrungszeitraums können Blobs gelöscht, aber nicht überschrieben werden.

- **[Unterstützung einer Richtlinie zur gesetzlichen Aufbewahrungspflicht](#legal-holds)** : Wenn der Aufbewahrungszeitraum nicht bekannt ist, können Benutzer Zeiträume für die gesetzliche Aufbewahrungspflicht festlegen, um Daten auf unveränderliche Weise zu speichern, bis die Aufbewahrungspflicht nicht mehr gilt.  Wenn eine Richtlinie für die gesetzliche Aufbewahrungspflicht festgelegt wird, können Blobs erstellt und gelesen, aber nicht geändert oder gelöscht werden. Jeder gesetzlichen Aufbewahrungspflicht ist ein benutzerdefiniertes alphanumerisches Tag zugeordnet (z. B. Fall-ID, Ereignisname usw.), das als Bezeichnerzeichenfolge verwendet wird. 

- **Unterstützung für alle Blobebenen**: WORM-Richtlinien sind unabhängig von der Azure-Blobspeicherebene und gelten für alle Ebenen: „Heiß“, „Kalt“ und „Archiv“. Benutzer können Daten in die Ebene überführen, für die die Workloadkosten bestmöglich optimiert sind, während gleichzeitig die Unveränderlichkeit der Daten sichergestellt ist.

- **Konfiguration auf Containerebene**: Benutzer können zeitbasierte Aufbewahrungsrichtlinien und Tags für gesetzliche Aufbewahrungspflichten auf Containerebene konfigurieren. Mit den Einstellungen auf Containerebene können Benutzer zeitbasierte Aufbewahrungsrichtlinien erstellen und sperren, Aufbewahrungszeiträume verlängern, Zeiträume für gesetzliche Aufbewahrungspflichten festlegen und aufheben usw. Diese Richtlinien gelten für alle Blobs im Container (vorhandene und neue).

- **Unterstützung der Überwachungsprotokollierung**: Jeder Container enthält ein Richtlinien-Überwachungsprotokoll. Es enthält bis zu sieben zeitbasierte Aufbewahrungsbefehle für gesperrte zeitbasierte Aufbewahrungsrichtlinien sowie die Benutzer-ID, den Befehlstyp, Zeitstempel und den Aufbewahrungszeitraum. Für Zeiträume zur gesetzlichen Aufbewahrungspflicht enthält das Protokoll Benutzer-ID, Befehlstyp, Zeitstempel und die entsprechenden Tags. Dieses Protokoll wird für die Lebensdauer der Richtlinie gemäß den SEC 17a-4(f)-Bestimmungsrichtlinien aufbewahrt. Im [Azure-Aktivitätsprotokoll](../../azure-monitor/platform/activity-logs-overview.md) werden umfassendere Protokolldaten mit allen Aktivitäten auf Steuerungsebene angezeigt. Wenn Sie [Azure-Diagnoseprotokolle](../../azure-monitor/platform/diagnostic-logs-overview.md) aktivieren, werden dagegen nur Vorgänge auf Datenebene aufbewahrt und angezeigt. Der Benutzer ist für die dauerhafte Speicherung dieser Protokolle verantwortlich, die aus gesetzlichen oder anderen Gründen ggf. erforderlich ist.

## <a name="how-it-works"></a>So funktioniert's

Unveränderlicher Speicher für Azure-Blobspeicher unterstützt zwei Arten von WORM-Richtlinien bzw. Richtlinien für die unveränderliche Speicherung: zeitbasierte Aufbewahrung und gesetzliche Aufbewahrungspflicht. Wenn eine zeitbasierte Aufbewahrungsrichtlinie oder ein Zeitraum für die gesetzliche Aufbewahrungspflicht auf einen Container angewendet wird, werden alle vorhandenen Blobs innerhalb von weniger als 30 Sekunden in einen unveränderlichen WORM-Zustand versetzt. Alle neuen Blobs, die in diesen Container hochgeladen werden, werden ebenfalls in den unveränderlichen Zustand versetzt. Nachdem alle Blobs in den unveränderlichen Zustand versetzt wurden, wird die Unveränderlichkeitsrichtlinie bestätigt, und alle Überschreibungs- oder Löschvorgänge für vorhandene und neue Objekte im unveränderlichen Container sind unzulässig.

Das Löschen von Containern und Konten ist ebenfalls nicht zulässig, wenn Blobs vorhanden sind, die durch eine unveränderliche Richtlinie geschützt sind. Beim Vorgang „Delete Container“ tritt ein Fehler auf, wenn mindestens ein Blob mit einer gesperrten zeitbasierten Aufbewahrungsrichtlinie oder einer gesetzlichen Aufbewahrungspflicht vorhanden ist. Für das Löschen des Speicherkontos tritt ein Fehler auf, wenn mindestens ein WORM-Container mit einer gesetzlichen Aufbewahrungspflicht oder ein Blob mit einem aktiven Aufbewahrungszeitraum vorhanden ist. 

### <a name="time-based-retention"></a>Zeitbasierte Aufbewahrung

> [!IMPORTANT]
> Eine zeitbasierte Aufbewahrungsrichtlinie muss *gesperrt* sein, damit das Blob zur Erzielung von Konformität mit SEC 17a-4(f) und anderen gesetzlichen Bestimmungen in einem konformen unveränderlichen Zustand ist (Schreib- und Löschschutz). Sie sollten die Richtlinie in einem angemessenen Zeitraum sperren – in der Regel weniger als 24 Stunden. Der ursprüngliche Zustand einer angewendeten zeitbasierten Aufbewahrungsrichtlinie lautet *Entsperrt*, damit Sie das Feature testen und Änderungen an der Richtlinie vornehmen können, bevor Sie das Sperren durchführen. Der Zustand *Entsperrt* bietet zwar Unveränderlichkeitsschutz, aber wir empfehlen Ihnen, *Entsperrt* nicht für andere Zwecke als für kurzfristige Featuretests zu verwenden. 

Wenn eine zeitbasierte Aufbewahrungsrichtlinie auf einen Container angewendet wird, bleiben alle Blobs im Container so lange im unveränderlichen Zustand, wie der Aufbewahrungszeitraum *gilt*. Die Gültigkeit des Aufbewahrungszeitraums für vorhandene Blobs entspricht der Differenz zwischen dem Zeitpunkt der Blobänderung und dem vom Benutzer angegebenen Aufbewahrungszeitraum.

Für neue Blobs entspricht die Gültigkeit des Aufbewahrungszeitraums dem vom Benutzer angegebenen Aufbewahrungszeitraum. Da Benutzer den Aufbewahrungszeitraum verlängern können, nutzt unveränderlicher Speicher den letzten Wert des vom Benutzer angegebenen Aufbewahrungszeitraums, um den effektiven Aufbewahrungszeitraum zu berechnen.

> [!TIP]
> **Beispiel:** Ein Benutzer erstellt eine zeitbasierte Aufbewahrungsrichtlinie mit einem Aufbewahrungszeitraum von fünf Jahren.
>
> Das vorhandene Blob in diesem Container (_testblob1_) wurde vor einem Jahr erstellt. Für _testblob1_ gilt ein Aufbewahrungszeitraum von vier Jahren.
>
> Das neue Blob _testblob2_ wird jetzt in den Container hochgeladen. Der effektive Aufbewahrungszeitraum für dieses neue Blob beträgt fünf Jahre.

Eine zeitbasierte Aufbewahrungsrichtlinie im Zustand „Entsperrt“ wird nur für Featuretests empfohlen, und eine Richtlinie muss gesperrt sein, um mit SEC 17a-4(f) konform zu sein und andere gesetzliche Bestimmungen zu erfüllen. Nachdem eine zeitbasierte Aufbewahrungsrichtlinie gesperrt wurde, kann die Richtlinie nicht entfernt werden, und maximal fünf Erhöhungen des geltenden Aufbewahrungszeitraums sind zulässig. Weitere Informationen zum Festlegen und Sperren von zeitbasierten Aufbewahrungsrichtlinien finden Sie im Abschnitt [Erste Schritte](#getting-started).

### <a name="legal-holds"></a>Gesetzliche Aufbewahrungspflichten

Wenn Sie einen Zeitraum für die gesetzliche Aufbewahrungspflicht festlegen, verbleiben alle vorhandenen und neuen Blobs im unveränderlichen Zustand, bis die gesetzliche Aufbewahrungspflicht aufgehoben wird. Ausführliche Informationen zum Festlegen und Aufheben von Zeiträumen für die gesetzliche Aufbewahrungspflicht finden Sie im Abschnitt [Erste Schritte](#getting-started).

Für einen Container können gleichzeitig eine gesetzliche Aufbewahrungspflicht und eine zeitbasierte Aufbewahrungsrichtlinie gelten. Alle Blobs in diesem Container verbleiben so lange im unveränderlichen Zustand, bis alle gesetzlichen Aufbewahrungspflichten aufgehoben wurden. Dies gilt auch, wenn die effektive Aufbewahrungsdauer bereits abgelaufen ist. Dagegen verbleibt ein Blob auch dann so lange im unveränderlichen Zustand, bis der effektive Aufbewahrungszeitraum abgelaufen ist, wenn alle Zeiträume für die gesetzliche Aufbewahrungspflicht aufgehoben wurden.

In der folgenden Tabelle sind die Arten von Blobvorgängen angegeben, die für die unterschiedlichen Szenarien der unveränderlichen Speicherung deaktiviert sind. Weitere Informationen finden Sie in der Dokumentation [Blob-Dienst-REST-API](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api).

|Szenario  |Blobzustand  |Blobvorgänge sind nicht zulässig  |
|---------|---------|---------|
|Effektiver Aufbewahrungszeitraum für das Blob ist noch nicht abgelaufen bzw. ein Zeitraum für die gesetzliche Aufbewahrungspflicht wurde festgelegt     |Unveränderlich: Lösch- und Schreibschutz         | Put Blob<sup>1</sup>, Put Block<sup>1</sup>, Put Block List<sup>1</sup>, Delete Container, Delete Blob, Set Blob Metadata, Put Page, Set Blob Properties, Snapshot Blob, Incremental Copy Blob, Append Block         |
|Effektiver Aufbewahrungszeitraum für das Blob abgelaufen     |Nur Schreibschutz (Löschvorgänge sind zulässig)         |Put Blob<sup>1</sup>, Put Block<sup>1</sup>, Put Block List<sup>1</sup>, Set Blob Metadata, Put Page, Set Blob Properties, Snapshot Blob, Incremental Copy Blob, Append Block         |
|Alle Zeiträume zur gesetzlichen Aufbewahrungspflicht wurden aufgehoben, und für den Container wurde keine zeitbasierte Aufbewahrungsrichtlinie festgelegt.     |Veränderlich         |Keine         |
|Keine WORM-Richtlinie erstellt (zeitbasierte Aufbewahrung oder gesetzliche Aufbewahrungspflicht)     |Veränderlich         |Keine         |

<sup>1</sup> Die Anwendung lässt diese Vorgänge zu, um einmalig ein neues Blob zu erstellen. Alle nachfolgenden Überschreibungsvorgänge in einem vorhandenen Blobpfad eines unveränderlichen Containers sind nicht zulässig.

## <a name="supported-values"></a>Unterstützte Werte

### <a name="time-based-retention"></a>Zeitbasierte Aufbewahrung
- Ein Speicherkonto kann über maximal 1.000 Container mit gesperrten zeitbasierten Unveränderlichkeitsrichtlinien verfügen.
- Der Mindestwert für den Aufbewahrungszeitraum beträgt einen Tag. Der Höchstwert beträgt 146.000 Tage (400 Jahre).
- Für einen Container sind höchstens fünf Bearbeitungen zur Verlängerung eines Aufbewahrungszeitraums gesperrte zeitbasierte unveränderliche Richtlinien möglich.
- Für einen Container werden höchstens sieben Überwachungsprotokolle für die zeitbasierte Aufbewahrungsrichtlinie für die Dauer der Richtlinie aufbewahrt.

### <a name="legal-hold"></a>Gesetzliche Aufbewahrungspflicht
- Ein Speicherkonto kann über maximal 1.000 Container mit einer Einstellung der gesetzlichen Aufbewahrungspflicht verfügen.
- Ein Container kann über maximal 10 Tags für die gesetzliche Aufbewahrungspflicht verfügen.
- Die Mindestlänge eines Tags für die gesetzliche Aufbewahrungspflicht beträgt drei alphanumerische Zeichen. Die Höchstlänge beträgt 23 alphanumerische Zeichen.
- Für einen Container werden höchstens zehn Richtlinien-Überwachungsprotokolle für die gesetzliche Aufbewahrungspflicht für die Dauer der Richtlinie aufbewahrt.

## <a name="pricing"></a>Preise

Für die Nutzung dieses Features fallen keine zusätzlichen Gebühren an. Unveränderliche Daten werden auf die gleiche Weise wie reguläre, änderbare Daten abgerechnet. Ausführliche Informationen zu Preisen von Azure Blob Storage finden Sie auf der [Seite mit den Preisen für Azure Storage](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="getting-started"></a>Erste Schritte
Unveränderlicher Speicher steht nur für Universell v2-Konten und Blobspeicherkonten zur Verfügung. Diese Konten müssen über [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) verwaltet werden. Informationen zum Aktualisieren eines vorhandenen Kontos vom Typ „Universell v1“ finden Sie unter [Aktualisieren eines Speicherkontos](../common/storage-account-upgrade.md).

Die aktuellen Releases von [Azure-Portal](https://portal.azure.com), [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) und [Azure PowerShell](https://github.com/Azure/azure-powershell/releases) unterstützen unveränderlichen Speicher für Azure-Blobspeicher. [Unterstützung für Clientbibliotheken](#client-libraries) ist ebenfalls vorhanden.

### <a name="azure-portal"></a>Azure-Portal

1. Erstellen Sie einen neuen Container, oder wählen Sie einen vorhandenen Container aus, um die Blobs zu speichern, die im unveränderlichen Zustand aufbewahrt werden müssen.
 Der Container muss sich in einem GPv2- oder Blob-Speicherkonto befinden.
2. Wählen Sie in den Containereinstellungen den Befehl **Zugriffsrichtlinie** aus. Wählen Sie dann **+ Richtlinie hinzufügen** unter **Unveränderlicher Blobspeicher** aus.

    ![Containereinstellungen im Portal](media/storage-blob-immutable-storage/portal-image-1.png)

3. Wählen Sie im Dropdownmenü die Option **Zeitbasierte Aufbewahrung** aus, um die zeitbasierte Aufbewahrung zu aktivieren.

    ![„Zeitbasierte Aufbewahrung“ unter „Richtlinientyp“ ausgewählt](media/storage-blob-immutable-storage/portal-image-2.png)

4. Geben Sie den Aufbewahrungszeitraum in Tagen an (zulässige Werte: 1 bis 146.000 Tage).

    ![Feld „Aufbewahrungszeitraum ändern in:“](media/storage-blob-immutable-storage/portal-image-5-retention-interval.png)

    Der ursprüngliche Zustand der Richtlinie lautet „Entsperrt“, damit Sie das Feature testen und Änderungen an der Richtlinie vornehmen können, bevor Sie das Sperren durchführen. Das Sperren der Richtlinie ist entscheidend für die Einhaltung von Vorschriften wie SEC 17a-4.

5. Sperren Sie die Richtlinie. Wenn Sie mit der rechten Maustaste auf die Auslassungspunkte ( **...** ) klicken, wird das folgende Menü mit zusätzlichen Aktionen angezeigt:

    ![„Richtlinie sperren“ im Menü](media/storage-blob-immutable-storage/portal-image-4-lock-policy.png)

6. Wählen Sie **Richtlinie sperren** aus, und bestätigen Sie die Sperre. Die Richtlinie ist jetzt gesperrt und kann nicht gelöscht werden. Es sind lediglich Verlängerungen des Aufbewahrungszeitraums zulässig. Das Löschen und außer Kraft setzen von Blobs ist nicht erlaubt. 

    ![Bestätigen von „Richtlinie sperren“ im Menü](media/storage-blob-immutable-storage/portal-image-5-lock-policy.png)

7. Zum Aktivieren der gesetzlichen Aufbewahrungspflicht wählen Sie **+ Richtlinie hinzufügen**. Wählen Sie im Dropdownmenü die Option **Gesetzliche Aufbewahrungspflicht**.

    ![„Gesetzliche Aufbewahrungspflicht“ im Menü unter „Richtlinientyp“](media/storage-blob-immutable-storage/portal-image-legal-hold-selection-7.png)

8. Erstellen Sie einen Zeitraum für die gesetzliche Aufbewahrungspflicht mit mindestens einem Tag.

    ![Feld „Tagname“ unter dem Richtlinientyp](media/storage-blob-immutable-storage/portal-image-set-legal-hold-tags.png)

9. Entfernen Sie einfach das entsprechende angewendete ID-Tag, um eine gesetzliche Aufbewahrungspflicht zu löschen.

### <a name="azure-cli"></a>Azure-Befehlszeilenschnittstelle

Die Funktion ist in den folgenden Befehlsgruppen enthalten: `az storage container immutability-policy` und `az storage container legal-hold`. Führen Sie `-h` darauf aus, um die Befehle anzuzeigen.

### <a name="powershell"></a>PowerShell

Das Modul „Az.Storage“ unterstützt unveränderlichen Speicher.  Gehen Sie folgendermaßen vor, um diese Funktion zu aktivieren:

1. Vergewissern Sie sich, dass die aktuelle Version von PowerShellGet installiert ist: `Install-Module PowerShellGet –Repository PSGallery –Force`.
2. Entfernen Sie alle vorherigen Installationen von Azure PowerShell.
3. Installieren Sie Azure PowerShell: `Install-Module Az –Repository PSGallery –AllowClobber`.

Der Abschnitt [PowerShell-Beispielcode](#sample-powershell-code) weiter unten in diesem Artikel veranschaulicht die Verwendung der Funktion.

## <a name="client-libraries"></a>Clientbibliotheken

Die folgenden Clientbibliotheken unterstützen unveränderlichen Speicher für Azure-Blobspeicher:

- [.NET-Clientbibliothek Version 7.2.0-preview und höher](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/7.2.0-preview)
- [Node.js-Clientbibliothek Version 4.0.0 und höher](https://www.npmjs.com/package/azure-arm-storage)
- [Python-Clientbibliothek Version 2.0.0 Release Candidate 2 und höher](https://pypi.org/project/azure-mgmt-storage/2.0.0rc2/)
- [Java-Clientbibliothek](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/storage/resource-manager/Microsoft.Storage/preview/2018-03-01-preview)

## <a name="faq"></a>Häufig gestellte Fragen

**Kann Dokumentation zum Thema WORM-Konformität bereitgestellt werden?**

Ja. Für die Dokumentation der Konformität hat Microsoft ein führendes Unternehmen für unabhängige Bewertungen (Cohasset Associates) beauftragt, das auf Datensatzverwaltung und Information Governance spezialisiert ist. Der Auftrag bestand darin, für unveränderlichen Azure-Blobspeicher die Konformität mit den spezifischen Anforderungen der Finanzdienstleistungsbranche auszuwerten. Cohasset hat bestätigt, dass unveränderlicher Azure-Blobspeicher bei Verwendung zur Aufbewahrung von zeitbasierten Blobs im WORM-Zustand die relevanten Speicheranforderungen der CFTC-Regel 1.31(c)-(d), FINRA-Regel 4511 und SEC-Regel 17a-4 erfüllt. Microsoft hat die Einhaltung dieser Regeln angestrebt, da sie die weltweit strikteste Anleitung zur Aufbewahrung von Datensätzen für Finanzinstitute darstellen. Der Cohasset-Bericht ist im [Microsoft Service Trust Center](https://aka.ms/AzureWormStorage) verfügbar. Wenden Sie sich an den Azure-Support, um von Microsoft einen Nachweis der WORM-Konformität anzufordern.

**Gilt die Funktion nur für Blockblobs oder auch für Seiten- und Anfügeblobs?**

Unveränderlicher Speicher kann mit jedem Blobtyp verwendet werden, doch Sie sollten ihn vor allem für Blockblobs verwenden. Im Gegensatz zu Blockblobs müssen Seiten- und Anfügeblobs außerhalb eines WORM-Containers erstellt und dann hineinkopiert werden. Nach dem Kopieren dieser Blobs in einen WORM-Container sind keine weiteren *Anfügevorgänge* an ein Anfügeblob oder Änderungen eines Seitenblobs zulässig.

**Muss ich ein neues Speicherkonto erstellen, um dieses Feature zu nutzen?**

Nein. Sie können unveränderlichen Speicher mit allen vorhandenen oder neu erstellten Universell v2- oder Blobspeicherkonten verwenden. Dieses Feature ist für die Verwendung mit Blockblobs in Universell v2- und Blobspeicherkonten vorgesehen. Universell v1-Speicherkonten werden nicht unterstützt, aber es kann problemlos ein Upgrade auf Universell v2 durchgeführt werden. Informationen zum Aktualisieren eines vorhandenen Kontos vom Typ „Universell v1“ finden Sie unter [Aktualisieren eines Speicherkontos](../common/storage-account-upgrade.md).

**Kann ich sowohl eine Richtlinie für eine gesetzliche Aufbewahrungspflicht als auch eine Richtlinie für die zeitbasierte Aufbewahrung anwenden?**

Ja, für einen Container können gleichzeitig eine gesetzliche Aufbewahrungspflicht und eine zeitbasierte Aufbewahrungsrichtlinie gelten. Alle Blobs in diesem Container verbleiben so lange im unveränderlichen Zustand, bis alle gesetzlichen Aufbewahrungspflichten aufgehoben wurden. Dies gilt auch, wenn die effektive Aufbewahrungsdauer bereits abgelaufen ist. Dagegen verbleibt ein Blob auch dann so lange im unveränderlichen Zustand, bis der effektive Aufbewahrungszeitraum abgelaufen ist, wenn alle Zeiträume für die gesetzliche Aufbewahrungspflicht aufgehoben wurden.

**Gelten Richtlinien für die gesetzliche Aufbewahrungspflicht nur für rechtliche Abläufe, oder gibt es auch andere Nutzungsszenarien?**

Nein. „Gesetzliche Aufbewahrungspflicht“ ist nur der allgemeine Ausdruck, der für eine nicht zeitbasierte Aufbewahrungsrichtlinie verwendet wird. Er bezieht sich nicht ausschließlich auf Vorgänge im Zusammenhang mit Rechtsstreitigkeiten. Richtlinien zur gesetzlichen Aufbewahrungspflicht sind nützlich, um Überschreibungen und Löschungen zum Schützen von wichtigen WORM-Unternehmensdaten zu deaktivieren, wenn der Aufbewahrungszeitraum unbekannt ist. Sie können sie als Unternehmensrichtlinie zum Schützen Ihrer unternehmenskritischen WORM-Workloads oder als Stagingrichtlinie verwenden, bevor für einen benutzerdefinierten Ereignisauslöser eine zeitbasierte Aufbewahrungsrichtlinie erforderlich ist. 

**Kann ich eine zeitbasierte Aufbewahrungsrichtlinie im Zustand *Gesperrt* oder eine gesetzliche Aufbewahrungspflicht entfernen?**

Nur entsperrte zeitbasierte Aufbewahrungsrichtlinien können aus einem Container entfernt werden. Nachdem eine zeitbasierte Aufbewahrungsrichtlinie gesperrt wurde, kann sie nicht mehr entfernt werden. Es sind nur Verlängerungen des geltenden Aufbewahrungszeitraums zulässig. Tags für die gesetzliche Aufbewahrungspflicht können gelöscht werden. Wenn alle Tags für die gesetzliche Aufbewahrungspflicht gelöscht werden, kann die gesetzliche Aufbewahrungspflicht entfernt werden.

**Was passiert, wenn ich versuche, einen Container mit einer *gesperrten* zeitbasierten Aufbewahrungsrichtlinie oder gesetzlichen Aufbewahrungspflicht zu löschen?**

Beim Vorgang „Delete Container“ tritt ein Fehler auf, wenn mindestens ein Blob mit einer gesperrten zeitbasierten Aufbewahrungsrichtlinie oder einer gesetzlichen Aufbewahrungspflicht vorhanden ist. Der Vorgang „Delete Container“ ist erfolgreich, wenn kein Blob mit einem aktiven Aufbewahrungszeitraum vorhanden ist und keine gesetzlichen Aufbewahrungspflichten vorhanden sind. Sie müssen die Blobs löschen, bevor Sie den Container löschen können.

**Was passiert, wenn ich versuche, ein Speicherkonto mit einem WORM-Container zu löschen, das über eine *gesperrte* zeitbasierte Aufbewahrungsrichtlinie oder eine gesetzliche Aufbewahrungspflicht verfügt?**

Für das Löschen des Speicherkontos tritt ein Fehler auf, wenn mindestens ein WORM-Container mit einer gesetzlichen Aufbewahrungspflicht oder ein Blob mit einem aktiven Aufbewahrungszeitraum vorhanden ist. Sie müssen alle WORM-Container löschen, bevor Sie das Speicherkonto löschen können. Informationen zum Löschen des Containers finden Sie unter der vorherigen Frage.

**Kann ich die Daten zwischen unterschiedlichen Blobebenen („Hot“, „Cool“, „Cold“) verschieben, wenn sich das Blob im unveränderlichen Zustand befindet?**

Ja. Sie können den Befehl „Set Blob Tier“ nutzen, um Daten zwischen den Blobebenen zu verschieben, während die Daten im konformen, unveränderlichen Zustand verbleiben. Unveränderlicher Speicher wird für die Blobebenen „Hot“, „Cool“ und „Archiv“ unterstützt.

**Was passiert, wenn ich nicht bezahle und mein Aufbewahrungszeitraum abgelaufen ist?**

Bei einer fehlenden Zahlung gelten die üblichen Richtlinien der Datenaufbewahrung gemäß den Bedingungen Ihres Vertrags mit Microsoft.

**Bieten Sie einen Test- oder Karenzzeitraum an, in dem die Funktion ausprobiert werden kann?**

Ja. Wenn eine zeitbasierte Aufbewahrungsrichtlinie erstellt wird, befindet sie sich zunächst im *entsperrten* Zustand. In diesem Zustand können Sie alle gewünschten Änderungen am Aufbewahrungszeitraum vornehmen, z.B. diesen verlängern oder verkürzen und auch die Richtlinie löschen. Nach dem Sperren der Richtlinie bleibt diese gesperrt, bis der Aufbewahrungszeitraums abgelaufen ist. Mit dieser Sperrung der Richtlinie wird verhindert, dass der Aufbewahrungszeitraum gelöscht und geändert wird. Es wird dringend empfohlen, den *entsperrten* Zustand nur für Testzwecke zu nutzen und die Richtlinie innerhalb eines Zeitraums von 24 Stunden wieder zu sperren. Dieses Vorgehen erleichtert Ihnen, SEC 17a-4(f) und andere Bestimmungen einzuhalten.

**Kann ich neben Richtlinien für unveränderliche Blobs das vorläufige Löschen verwenden?**

Ja. Das [vorläufige Löschen für Azure-Blobspeicher](storage-blob-soft-delete.md) gilt für alle Container eines Speicherkontos, unabhängig von einer Richtlinie für die gesetzliche Aufbewahrungspflicht oder eine zeitbasierte Aufbewahrungsrichtlinie. Wir empfehlen Ihnen, das vorläufige Löschen als zusätzlichen Schutz zu verwenden, bevor Richtlinien für den unveränderlichen WORM-Zustand angewendet und bestätigt werden. 

**Wo ist die Funktion verfügbar?**

Unveränderliche Speicher sind in den Regionen Azure Public, China und Behörden verfügbar. Wenn unveränderlicher Speicher in Ihrer Region nicht verfügbar ist, können Sie sich an den Support wenden und die E-Mail-Adresse azurestoragefeedback@microsoft.com nutzen.

## <a name="sample-powershell-code"></a>PowerShell-Beispielcode

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Das folgende PowerShell-Beispielskript dient als Referenz. Dieses Skript erstellt ein neues Speicherkonto und einen Container. Anschließend wird veranschaulicht, wie Sie Zeiträume für gesetzliche Aufbewahrungspflichten festlegen und löschen und eine zeitbasierte Aufbewahrungsrichtlinie (auch als Unveränderlichkeitsrichtlinie bekannt) erstellen und sperren sowie den Aufbewahrungszeitraum verlängern.

Einrichten und Testen des Azure-Speicherkontos:

```powershell
$ResourceGroup = "<Enter your resource group>”
$StorageAccount = "<Enter your storage account name>"
$container = "<Enter your container name>"
$container2 = "<Enter another container name>”
$location = "<Enter the storage account location>"

# Log in to the Azure Resource Manager account
Login-AzAccount
Register-AzResourceProvider -ProviderNamespace "Microsoft.Storage"

# Create your Azure resource group
New-AzResourceGroup -Name $ResourceGroup -Location $location

# Create your Azure storage account
New-AzStorageAccount -ResourceGroupName $ResourceGroup -StorageAccountName `
    $StorageAccount -SkuName Standard_LRS -Location $location -Kind StorageV2

# Create a new container
New-AzStorageContainer -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -Name $container

# Create Container 2 with a storage account object
$accountObject = Get-AzStorageAccount -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount
New-AzStorageContainer -StorageAccount $accountObject -Name $container2

# Get a container
Get-AzStorageContainer -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -Name $container

# Get a container with an account object
$containerObject = Get-AzStorageContainer -StorageAccount $accountObject -Name $container

# List containers
Get-AzStorageContainer -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount

# Remove a container (add -Force to dismiss the prompt)
Remove-AzStorageContainer -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -Name $container2

# Remove a container with an account object
Remove-AzStorageContainer -StorageAccount $accountObject -Name $container2

# Remove a container with a container object
$containerObject2 = Get-AzStorageContainer -StorageAccount $accountObject -Name $container2
Remove-AzStorageContainer -InputObject $containerObject2
```

Festlegen und Löschen gesetzlicher Aufbewahrungspflichten:

```powershell
# Set a legal hold
Add-AzRmStorageContainerLegalHold -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -Name $container -Tag <tag1>,<tag2>,...

# with an account object
Add-AzRmStorageContainerLegalHold -StorageAccount $accountObject -Name $container -Tag <tag3>

# with a container object
Add-AzRmStorageContainerLegalHold -Container $containerObject -Tag <tag4>,<tag5>,...

# Clear a legal hold
Remove-AzRmStorageContainerLegalHold -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -Name $container -Tag <tag2>

# with an account object
Remove-AzRmStorageContainerLegalHold -StorageAccount $accountObject -Name $container -Tag <tag3>,<tag5>

# with a container object
Remove-AzRmStorageContainerLegalHold -Container $containerObject -Tag <tag4>
```

Erstellen oder Aktualisieren von Unveränderlichkeitsrichtlinien:
```powershell
# with an account name or container name
Set-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -ContainerName $container -ImmutabilityPeriod 10

# with an account object
Set-AzRmStorageContainerImmutabilityPolicy -StorageAccount $accountObject `
    -ContainerName $container -ImmutabilityPeriod 1 -Etag $policy.Etag

# with a container object
$policy = Set-AzRmStorageContainerImmutabilityPolicy -Container `
    $containerObject -ImmutabilityPeriod 7

# with an immutability policy object
Set-AzRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy $policy -ImmutabilityPeriod 5
```

Abrufen von Unveränderlichkeitsrichtlinien:
```powershell
# Get an immutability policy
Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -ContainerName $container

# with an account object
Get-AzRmStorageContainerImmutabilityPolicy -StorageAccount $accountObject `
    -ContainerName $container

# with a container object
Get-AzRmStorageContainerImmutabilityPolicy -Container $containerObject
```

Sperren von Unveränderlichkeitsrichtlinien („-Force“ hinzufügen, um die Aufforderung zu schließen):
```powershell
# with an immutability policy object
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container
$policy = Lock-AzRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy $policy -force

# with an account name or container name
$policy = Lock-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container `
    -Etag $policy.Etag

# with an account object
$policy = Lock-AzRmStorageContainerImmutabilityPolicy -StorageAccount `
    $accountObject -ContainerName $container -Etag $policy.Etag

# with a container object
$policy = Lock-AzRmStorageContainerImmutabilityPolicy -Container `
    $containerObject -Etag $policy.Etag -force
```

Erweitern von Unveränderlichkeitsrichtlinien:
```powershell

# with an immutability policy object
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container

$policy = Set-AzRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy `
    $policy -ImmutabilityPeriod 11 -ExtendPolicy

# with an account name or container name
$policy = Set-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container `
    -ImmutabilityPeriod 11 -Etag $policy.Etag -ExtendPolicy

# with an account object
$policy = Set-AzRmStorageContainerImmutabilityPolicy -StorageAccount `
    $accountObject -ContainerName $container -ImmutabilityPeriod 12 -Etag `
    $policy.Etag -ExtendPolicy

# with a container object
$policy = Set-AzRmStorageContainerImmutabilityPolicy -Container `
    $containerObject -ImmutabilityPeriod 13 -Etag $policy.Etag -ExtendPolicy
```

Entfernen einer entsperrten Unveränderlichkeitsrichtlinie („-Force“ hinzufügen, um die Aufforderung zu schließen):
```powershell
# with an immutability policy object
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container
Remove-AzRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy $policy

# with an account name or container name
Remove-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container `
    -Etag $policy.Etag

# with an account object
Remove-AzRmStorageContainerImmutabilityPolicy -StorageAccount $accountObject `
    -ContainerName $container -Etag $policy.Etag

# with a container object
Remove-AzRmStorageContainerImmutabilityPolicy -Container $containerObject `
    -Etag $policy.Etag

```
