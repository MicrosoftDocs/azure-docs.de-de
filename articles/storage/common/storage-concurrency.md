---
title: Verwalten von Nebenläufigkeit Microsoft Azure Storage
description: Verwalten von Nebenläufigkeit für die Blob-, Warteschlangen-, Tabellen- und Dateidienste
services: storage
author: jasontang501
ms.service: storage
ms.devlang: dotnet
ms.topic: article
ms.date: 05/11/2017
ms.author: jasontang501
ms.subservice: common
ms.openlocfilehash: 9e786aed031d528b8ae574444b71753ac538cf47
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64728306"
---
# <a name="managing-concurrency-in-microsoft-azure-storage"></a>Verwalten von Nebenläufigkeit Microsoft Azure Storage
## <a name="overview"></a>Übersicht
Moderne Internet-basierte Anwendungen haben in der Regel mehrere Benutzer, die Daten gleichzeitig anzeigen und aktualisieren. Dies zwingt Anwendungsentwickler dazu, sorgfältig zu überlegen, wie sie ihren Endbenutzern ein vorhersagbares Erlebnis gewährleisten können, insbesondere in Situationen, in denen mehrere Benutzer die gleichen Daten aktualisieren können. Von Entwicklern werden für gewöhnlich die drei folgenden Hauptstrategien für die Datenparallelität in Betracht gezogen:  

1. Optimistische Nebenläufigkeit – Eine Anwendung, die eine Aktualisierung ausführt, prüft während dieses Vorgangs, ob die Daten sich geändert haben, seitdem sie das letzte Mal gelesen wurden. Wenn z. B. zwei Benutzer eine Wiki-Seite aufrufen und dieselbe Seite aktualisieren, muss die Wiki-Plattform sicherstellen, dass die erste Aktualisierung nicht von der zweiten Aktualisierung überschrieben wird und dass beide Benutzer wissen, ob ihre Aktualisierung erfolgreich war oder nicht. Diese Strategie wird in Webanwendung sehr häufig verwendet.
2. Pessimistische Nebenläufigkeit – Eine Anwendung, die eine Aktualisierung ausführt, sperrt ein Objekt, sodass andere Benutzer die Daten erst aktualisieren können, wenn die Sperre aufgehoben wird. Bei einer Datenreplikation zwischen Master und untergeordnetem Gerät beispielsweise, bei der nur das Master Aktualisierungen vornimmt, richtet dieser in der Regel für einen bestimmten Zeitraum eine exklusive Sperre der Daten ein, damit sie von keinem anderen Benutzer aktualisiert werden können.
3. Letzter Schreiber gewinnt – Ein Verfahren, bei dem Aktualisierungsoperationen ausgeführt werden können, ohne dass geprüft wird, ob eine andere Anwendung die Daten aktualisiert hat, nachdem sie von der Anwendung erstmals gelesen wurden. Diese Strategie (oder das Fehlen einer formellen Strategie) wird in der Regel verwendet, wenn die Daten so partitioniert sind, dass der gleichzeitige Zugriff mehrerer Benutzer auf dieselben Daten unwahrscheinlich ist. Diese Strategie kann auch bei der Verarbeitung kurzlebiger Datenströme sinnvoll sein.  

Dieser Artikel gibt eine Übersicht darüber, wie die Azure Storage-Plattform Entwicklungsprozesse durch die ausgezeichnete Unterstützung aller drei Nebenläufigkeitsstrategien vereinfacht.  

## <a name="azure-storage--simplifies-cloud-development"></a>Azure Storage – Vereinfachen der Cloudentwicklung
Der Azure-Speicherdienst unterstützt alle drei Strategien, vor allem jedoch die optimistische und pessimistische Nebenläufigkeit. Denn der Dienst umfasst ein strenges Konsistenzmodell, das garantiert, dass nach Einfügungen oder Aktualisierungen von Daten durch den Speicherdienst stets auf die letzte Aktualisierung dieser Daten zugegriffen wird. Bei Speicherplattformen, die ein Modell der letztendlichen Konsistenz verwenden, liegt zwischen dem Schreiben der Daten durch einen Benutzer und der Anzeige dieser Daten durch andere Benutzer eine bestimmte Zeitspanne, damit bei Endbenutzern keine Inkonsistenzen auftreten. Die Entwicklung von Clientanwendungen wird dadurch kompliziert.  

Entwickler müssen nicht nur die entsprechende Nebenläufigkeitsstrategie auswählen, sondern auch wissen, wie eine Speicherplattform Änderungen isoliert, insbesondere Änderungen, die in mehreren Transaktionen an demselben Objekt vorgenommen wurden. Der Azure-Speicherdienst verwendet die Momentaufnahmeisolation, damit Lesevorgänge innerhalb einer einzelnen Partition gleichzeitig mit Schreibvorgängen ausgeführt werden können. Im Unterschied zu anderen Isolationsstufen garantiert die Momentaufnahmeisolation, dass bei allen Lesevorgängen eine konsistente Momentaufnahme der Daten vorliegt, auch wenn Aktualisierungen stattfinden. Dazu werden während der Verarbeitung einer Aktualisierungstransaktion im Wesentlichen die zuletzt übergebenen Werte zurückgegeben.  

## <a name="managing-concurrency-in-blob-storage"></a>Verwalten der Parallelität im Blobspeicher
Für die Verwaltung des Zugriffs auf Blobs und Container im Blob-Dienst können Sie entweder das optimistische oder das pessimistische Nebenläufigkeitsmodell verwenden. Wenn Sie die Strategie nicht explizit festlegen, wird standardmäßig "Letzter Schreiber gewinnt" verwendet.  

### <a name="optimistic-concurrency-for-blobs-and-containers"></a>Optimistische Nebenläufigkeit für Blobs und Container
Der Speicherdienst weist jedem gespeicherten Objekt einen Bezeichner zu. Dieser Bezeichner wird jedes Mal aktualisiert, wenn ein Objekt aktualisiert wird. Der Bezeichner wird dem Client zusammen mit einer HTTP GET-Antwort zurückgegeben. Dabei wird ETag-Header (Entitätstag) verwendet, der im HTTP-Protokoll definiert ist. Ein Benutzer, der eine Aktualisierung eines Objekts ausführt, kann das ursprüngliche ETag zusammen mit einem bedingten Header senden, um sicherzustellen, dass eine Aktualisierung nur stattfindet, wenn eine bestimmte Bedingung erfüllt ist. In diesem Fall handelt es sich bei der Bedingung um einen „If-Match“-Header, der den Speicherdienst zwingt zu prüfen, ob der Wert des in der Aktualisierungsanforderung angegebenen ETags mit dem im Speicherdienst hinterlegten Wert identisch ist.  

Dieser Prozess ist folgendermaßen gegliedert:  

1. Es wird ein Blob aus dem Speicherdienst abgerufen. Die Antwort enthält einen HTTP-ETag-Headerwert, der die aktuelle Version des Objekts im Speicherdienst bezeichnet.
2. Wenn Sie das Blob aktualisieren, fügen Sie den in Schritt 1 erhaltenen ETag-Wert in den bedingten **If-Match** -Header der Anforderung ein, die Sie an den Dienst senden.
3. Der Dienst vergleicht den ETag-Wert in der Anforderung mit dem aktuellen ETag-Wert des Blob.
4. Wenn der aktuelle ETag-Wert des Blob eine andere Version hat als das ETag im bedingten **If-Match** -Header in der Anforderung, gibt der Dienst einen 412-Fehler an den Client zurück. Dadurch wird dem Client mitgeteilt, dass das Blob von einem anderen Prozess aktualisiert wurde, seitdem es vom Client abgerufen wurde.
5. Wenn der aktuelle ETag-Wert des Blob dieselbe Version hat wie der des ETag im bedingten **If-Match** -Header in der Anforderung, führt der Dienst die angeforderte Operation aus und aktualisiert den aktuellen ETag-Wert des Blobs, um anzuzeigen, dass eine neue Version erstellt wurde.  

Der folgende C#-Codeausschnitt (der Client Storage Library 4.2.0 verwendet) zeigt ein einfaches Beispiel zur Konstruktion eines **If-Match AccessCondition** -Objekts, das auf dem ETag-Wert beruht, auf den über die Eigenschaften eines Blob zugegriffen wird, das zuvor abgerufen oder eingefügt wurde. Beim Aktualisieren des Blobs wird dann das **AccessCondition**-Objekt verwendet: Das **AccessCondition**-Objekt fügt der Anforderung den **If-Match**-Header hinzu. Falls das Blob von einem anderen Prozess aktualisiert wurde, gibt der Blob-Dienst eine HTTP 412-Statusmeldung (Vorbedingungsfehler) zurück. Sie können das vollständige Beispiel hier herunterladen: [Managing concurrency using Azure Storage (Verwalten von Parallelität mit Azure Storage)](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).  

```csharp
// Retrieve the ETag from the newly created blob
// Etag is already populated as UploadText should cause a PUT Blob call
// to storage blob service which returns the ETag in response.
string originalETag = blockBlob.Properties.ETag;

// This code simulates an update by a third party.
string helloText = "Blob updated by a third party.";

// No ETag provided so original blob is overwritten (thus generating a new ETag)
blockBlob.UploadText(helloText);
Console.WriteLine("Blob updated. Updated ETag = {0}",
blockBlob.Properties.ETag);

// Now try to update the blob using the original ETag provided when the blob was created
try
{
    Console.WriteLine("Trying to update blob using original ETag to generate if-match access condition");
    blockBlob.UploadText(helloText,accessCondition:
    AccessCondition.GenerateIfMatchCondition(originalETag));
}
catch (StorageException ex)
{
    if (ex.RequestInformation.HttpStatusCode == (int)HttpStatusCode.PreconditionFailed)
    {
        Console.WriteLine("Precondition failure as expected. Blob's original ETag no longer matches");
        // TODO: client can decide on how it wants to handle the 3rd party updated content.
    }
    else
        throw;
}  
```

Der Speicherdienst unterstützt auch weitere bedingte Header wie **If-Modified-Since**, **If-Unmodified-Since** und **If-None-Match** sowie Kombinationen dieser Header. Weitere Informationen finden Sie unter [Specifying Conditional Headers for Blob Service Operations](https://msdn.microsoft.com/library/azure/dd179371.aspx) (Angeben von bedingten Headern für Vorgänge des Blob-Diensts) auf MSDN.  

Die folgende Tabelle gibt eine Übersicht über die Containervorgänge, die bedingte Header wie **If-Match** in der Anforderung akzeptieren und in der Antwort einen ETag-Wert zurückgeben.  

| Vorgang | Gibt Container-ETag-Wert zurück | Akzeptiert bedingte Header |
|:--- |:--- |:--- |
| Create Container |Ja |Nein |
| Get Container Properties |Ja |Nein |
| Get Container Metadata |Ja |Nein |
| Set Container Metadata |Ja |Ja |
| Get Container ACL |Ja |Nein |
| Set Container ACL |Ja |Ja (*) |
| Delete Container |Nein |Ja |
| Lease Container |Ja |Ja |
| List Blobs |Nein |Nein |

(*) Die von SetContainerACL definierten Berechtigungen werden zwischengespeichert, und die Verteilung der Aktualisierungen dieser Berechtigungen dauert 30 Sekunden. Während dieser Zeitspanne kann die Konsistenz der Aktualisierungen nicht garantiert werden.  

Die folgende Tabelle gibt einen Überblick über die Blob-Vorgänge, die bedingte Header wie **If-Match** in der Anforderung akzeptieren und in der Antwort einen ETag-Wert zurückgeben.

| Vorgang | Gibt ETag-Wert zurück | Akzeptiert bedingte Header |
|:--- |:--- |:--- |
| Put Blob |Ja |Ja |
| Get Blob |Ja |Ja |
| Get Blob Properties |Ja |Ja |
| Set Blob Properties |Ja |Ja |
| Get Blob Metadata |Ja |Ja |
| Set Blob Metadata |Ja |Ja |
| Lease Blob (*) |Ja |Ja |
| Snapshot Blob |Ja |Ja |
| Kopieren von Blobs |Ja |Ja (für Quell- und Ziel-Blob) |
| Abort Copy Blob |Nein |Nein |
| Delete Blob |Nein |Ja |
| Put Block |Nein |Nein |
| Put Block List |Ja |Ja |
| Get Block List |Ja |Nein |
| Put Page |Ja |Ja |
| Get Page Ranges |Ja |Ja |

(*) "Lease Blob" ändert das ETag eines Blob nicht.  

### <a name="pessimistic-concurrency-for-blobs"></a>Pessimistische Nebenläufigkeit für Blobs
Um ein Blob für die exklusive Verwendung zu sperren, können Sie dafür eine [Lease](https://msdn.microsoft.com/library/azure/ee691972.aspx) abrufen. Wenn Sie eine Lease erwerben, geben Sie an, wie lange Sie die Lease benötigen: Das kann zwischen 15 und 60 Sekunden oder unendlich sein, was zu einer exklusiven Sperre führt. Sie können eine begrenzte Lease erneuern, um sie zu verlängern, und Sie können eine Lease freigeben, wenn sie nicht mehr benötigt wird. Der Blob-Dienst gibt begrenzte Leases automatisch frei, wenn sie abgelaufen sind.  

Leases ermöglichen die Unterstützung verschiedener Synchronisierungsstrategien, einschließlich exklusiver Schreib-/gemeinsamer Lesezugriff, exklusiver Schreib-/exklusiver Lesezugriff und gemeinsamer Schreib-/exklusiver Lesezugriff. Ist eine Lease vorhanden, erzwingt der Speicherdienst exklusive Schreibzugriffe (put-, set- und delete-Vorgänge). Um Exklusivität von Lesevorgängen sicherzustellen, muss der Entwickler jedoch dafür sorgen, dass alle Clientanwendungen eine Lease-ID verwenden und dass jeweils nur ein Client über eine gültige Lease-ID verfügt. Lesevorgänge, die keine Lease-ID enthalten, führen zu gemeinsamen Lesevorgängen.  

Der folgende C#-Codeausschnitt zeigt an einem Beispiel, wie eine exklusive Lease von 30 Sekunden für ein Blob abgerufen, der Inhalt des Blob aktualisiert und die Lease anschließend freigegeben wird. Wenn beim Abrufen einer neuen Lease für das Blob bereits eine Lease wirksam ist, gibt der Blob-Dienst die Statusmeldung „HTTP (409) Conflict“ zurück. Im folgenden Codeausschnitt wird ein **AccessCondition**-Objekt verwendet, um die Lease-Informationen zu kapseln, wenn die Anforderung zum Aktualisieren des Blob im Speicherdienst ausgeführt wird.  Sie können das vollständige Beispiel hier herunterladen: [Managing concurrency using Azure Storage (Verwalten von Parallelität mit Azure Storage)](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).

```csharp
// Acquire lease for 15 seconds
string lease = blockBlob.AcquireLease(TimeSpan.FromSeconds(15), null);
Console.WriteLine("Blob lease acquired. Lease = {0}", lease);

// Update blob using lease. This operation will succeed
const string helloText = "Blob updated";
var accessCondition = AccessCondition.GenerateLeaseCondition(lease);
blockBlob.UploadText(helloText, accessCondition: accessCondition);
Console.WriteLine("Blob updated using an exclusive lease");

//Simulate third party update to blob without lease
try
{
    // Below operation will fail as no valid lease provided
    Console.WriteLine("Trying to update blob without valid lease");
    blockBlob.UploadText("Update without lease, will fail");
}
catch (StorageException ex)
{
    if (ex.RequestInformation.HttpStatusCode == (int)HttpStatusCode.PreconditionFailed)
        Console.WriteLine("Precondition failure as expected. Blob's lease does not match");
    else
        throw;
}  
```

Wenn Sie an einem Blob mit Lease einen Schreibvorgang versuchen, ohne die Lease-ID zu übergeben, erzeugt die Anforderung einen 412-Fehler. Wenn die Lease abläuft, bevor die **UploadText**-Methode aufgerufen wird, die Lease-ID aber dennoch übergeben wird, erzeugt die Anforderung ebenfalls einen **412**-Fehler. Weitere Informationen zum Verwalten von Lease-Ablaufzeiten und Lease-IDs finden Sie in der REST-Dokumentation zum [Leasen eines Blob](https://msdn.microsoft.com/library/azure/ee691972.aspx).  

Zur Verwaltung der pessimistischen Nebenläufigkeit können die folgenden Blob-Vorgänge Leases verwenden:  

* Put Blob
* Get Blob
* Get Blob Properties
* Set Blob Properties
* Get Blob Metadata
* Set Blob Metadata
* Delete Blob
* Put Block
* Put Block List
* Get Block List
* Put Page
* Get Page Ranges
* Snapshot Blob - Lease-ID optional, falls eine Lease vorhanden ist
* Copy Blob - Lease-ID erforderlich, falls im Ziel-Blob eine Lease vorhanden ist
* Abort Copy Blob - Lease-ID erforderlich, falls im Ziel-Blob eine unendliche Lease vorhanden ist
* Leasen eines Blob  

### <a name="pessimistic-concurrency-for-containers"></a>Pessimistische Nebenläufigkeit für Container
Leases für Container unterstützen die gleichen Synchronisierungsstrategien wie für Blobs (exklusiver Schreib-/gemeinsamer Lesezugriff, exklusiver Schreib-/exklusiver Lesezugriff und gemeinsamer Schreib-/exklusiver Lesezugriff). Im Unterschied zu Blobs erzwingt der Speicherdienst Exklusivität jedoch nur bei Löschvorgängen. Um einen Container mit einer aktiven Lease zu löschen, muss ein Client die ID der aktiven Lease bei der Löschanforderung angeben. Alle anderen Containervorgänge können bei einem Container mit Lease ohne Angabe der Lease-ID erfolgreich ausgeführt werden. In diesem Fall handelt sich dann um gemeinsame Vorgänge. Wenn Aktualisierungs- (put oder set) bzw. Lesevorgänge exklusiv sein sollen, müssen die Entwickler sicherstellen, dass alle Clients eine Lease-ID verwenden und dass jeweils nur ein Client über eine gültige Lease-ID verfügt.  

Zur Verwaltung der pessimistischen Nebenläufigkeit können die folgenden Containervorgänge Leases verwenden:  

* Delete Container
* Get Container Properties
* Get Container Metadata
* Set Container Metadata
* Get Container ACL
* Set Container ACL
* Lease Container  

Weitere Informationen finden Sie unter  

* [Angeben von bedingten Headern für Vorgänge des Blob-Diensts](https://msdn.microsoft.com/library/azure/dd179371.aspx)
* [Lease Container](https://msdn.microsoft.com/library/azure/jj159103.aspx)
* [Lease Blob](https://msdn.microsoft.com/library/azure/ee691972.aspx)

## <a name="managing-concurrency-in-the-table-service"></a>Verwalten der Nebenläufigkeit im Tabellendienst
Wenn Sie mit Entitäten arbeiten, verwendet der Tabellendienst standardmäßig optimistische Nebenläufigkeitsprüfungen. Darin unterscheidet er sich vom Blob-Dienst, wo die Durchführung optimistischer Nebenläufigkeitsprüfungen explizit gewählt werden muss. Ein weiterer Unterschied zwischen Tabellendienst und Blob-Dienst besteht darin, dass Sie mit dem Tabellendienst nur das Nebenläufigkeitsverhalten von Entitäten verwalten können, während Sie mit dem Blob-Dienst die Nebenläufigkeit sowohl von Containern als auch von Blobs verwalten können.  

Um die optimistische Nebenläufigkeit zu verwenden und zu prüfen, ob eine Entität von einem anderen Prozess geändert wurde, nachdem Sie sie aus dem Tabellenspeicherdienst abgerufen haben, können Sie den ETag-Wert verwenden, den Sie erhalten, wenn der Tabellendienst eine Entität zurückgibt. Dieser Prozess ist folgendermaßen gegliedert:  

1. Es wird eine Entität aus dem Tabellenspeicherdienst abgerufen. Die Antwort enthält einen ETag-Wert, der den aktuellen Bezeichner angibt, der mit der betreffenden Entität im Speicherdienst verknüpft ist.
2. Wenn Sie die Entität aktualisieren, fügen Sie den in Schritt 1 erhaltenen ETag-Wert in den obligatorischen **If-Match** -Header der Anforderung ein, die Sie an den Dienst senden.
3. Der Dienst vergleicht den ETag-Wert in der Anforderung mit dem aktuellen ETag-Wert der Entität.
4. Wenn der aktuelle ETag-Wert der Entität vom ETag im obligatorischen **If-Match** -Header in der Anforderung abweicht, gibt der Dienst einen 412-Fehler an den Client zurück. Dadurch wird dem Client mitgeteilt, dass die Entität von einem anderen Prozess aktualisiert wurde, seitdem sie vom Client abgerufen wurde.
5. Wenn der aktuelle ETag-Wert der Entität identisch ist mit dem ETag-Wert im obligatorischen **If-Match**-Header in der Anforderung oder wenn der **If-Match**-Header das Platzhalterzeichen (*) enthält, führt der Dienst den angeforderten Vorgang aus und aktualisiert den aktuellen ETag-Wert der Entität, um anzuzeigen, dass die Entität aktualisiert wurde.  

Im Unterschied zum Blob-Dienst verlangt der Tabellendienst vom Client, in Aktualisierungsanforderungen einen **If-Match** -Header hinzuzufügen. Es ist jedoch möglich, eine unbedingte Aktualisierung zu erzwingen (Strategie "Letzter Schreiber gewinnt") und Nebenläufigkeitsprüfungen zu umgehen, wenn der Client den **If-Match** -Header in der Anforderung auf das Platzhalterzeichen (*) festlegt.  

Der folgende C#-Codeausschnitt zeigt, wie für eine Kundenentität, die zuvor erstellt oder abgerufen wurde, die E-Mail-Adresse aktualisiert wird. Mit dem anfänglichen Einfüge- oder Abrufvorgang wird der ETag-Wert im Kundenobjekt gespeichert. Da im Beispiel beim Ausführen des Ersetzenvorgangs dieselbe Objektinstanz verwendet wird, wird der ETag-Wert automatisch an den Tabellendienst zurückgesendet, sodass der Dienst auf Verletzungen der Nebenläufigkeit prüfen kann. Falls die Entität im Tabellenspeicher von einem anderen Prozess aktualisiert wurde, gibt der Dienst eine HTTP 412-Statusmeldung (Vorbedingungsfehler) zurück.  Sie können das vollständige Beispiel hier herunterladen: [Managing concurrency using Azure Storage (Verwalten von Parallelität mit Azure Storage)](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).

```csharp
try
{
    customer.Email = "updatedEmail@contoso.org";
    TableOperation replaceCustomer = TableOperation.Replace(customer);
    customerTable.Execute(replaceCustomer);
    Console.WriteLine("Replace operation succeeded.");
}
catch (StorageException ex)
{
    if (ex.RequestInformation.HttpStatusCode == 412)
        Console.WriteLine("Optimistic concurrency violation – entity has changed since it was retrieved.");
    else
        throw;
}  
```

Um die Parallelitätsprüfung explizit zu deaktivieren, müssen Sie die **ETag**-Eigenschaft des Objekts **employee** auf „*“ festlegen, bevor der Ersetzungsvorgang ausgeführt wird.  

```csharp
customer.ETag = "*";  
```

Die folgende Tabelle gibt eine Übersicht darüber, wie ETag-Werte in den Tabellen-Entitätsvorgängen verwendet werden:

| Vorgang | Gibt ETag-Wert zurück | Erfordert If-Match-Anforderungsheader |
|:--- |:--- |:--- |
| Entitäten abfragen |Ja |Nein |
| Entität einfügen |Ja |Nein |
| Entität aktualisieren |Ja |Ja |
| Entität zusammenführen |Ja |Ja |
| Entität löschen |Nein |Ja |
| Entität einfügen oder ersetzen |Ja |Nein |
| Entität einfügen oder zusammenführen |Ja |Nein |

Bei den Vorgängen **Entität einfügen oder ersetzen** und **Entität einfügen oder zusammenführen** werden *keine* Nebenläufigkeitsprüfungen durchgeführt, da hierbei kein ETag-Wert an den Tabellenspeicherdienst gesendet wird.  

Bei der Entwicklung skalierbarer Anwendung sollten Entwickler grundsätzlich auf die optimistische Nebenläufigkeit zurückgreifen. Wenn pessimistische Sperren erforderlich sind, können Entwickler beim Zugriff auf Tabelle u. a. folgendes Verfahren anwenden: Jeder Tabelle wird ein bestimmtes Blob zugewiesen, und es kann versucht werden, eine Lease für das Blob zu erhalten, bevor die Tabelle bearbeitet wird. Bei diesem Verfahren muss die Anwendung nicht sicherstellen, dass Datenzugriffspfade die Lease vor der Bearbeitung der Tabelle abrufen. Beachten Sie außerdem, dass die Mindestzeit für Leases 15 Sekunden beträgt, was bei der Skalierbarkeit sorgfältig zu beachten ist.  

Weitere Informationen finden Sie unter  

* [Vorgänge für Entitäten](https://msdn.microsoft.com/library/azure/dd179375.aspx)  

## <a name="managing-concurrency-in-the-queue-service"></a>Verwalten der Nebenläufigkeit im Warteschlangendienst
Ein Szenario, in dem Nebenläufigkeit ein Problem im Warteschlangendienst darstellt, ist der Abruf von Nachrichten aus einer Warteschlange durch mehrere Clients. Wenn eine Nachricht aus der Warteschlange abgerufen wird, enthält die Antwort die Nachricht und einen Pop-Belegwert, der zum Löschen der Nachricht erforderlich ist. Die Nachricht wird nicht automatisch aus der Warteschlange gelöscht, sondern erst, nachdem sie abgerufen wurde. Für das mit dem Parameter visibilitytimeout angegebene Zeitintervall ist die Nachricht für andere Clients nicht sichtbar. Es wird erwartet, dass die Nachricht vom abrufenden Client gelöscht wird, nachdem sie verarbeitet wurde und bevor die vom Element TimeNextVisible der Antwort angegebene Zeit verstrichen ist. Diese Zeit wird anhand des Werts des Parameters visibilitytimeout berechnet. Der Wert von visibilitytimeout wird zu der Zeit addiert, zu der die Nachricht abgerufen wird, um den Wert von TimeNextVisible zu ermitteln.  

Der Warteschlangendienst unterstützt weder die optimistische noch die pessimistische Nebenläufigkeit. Aus diesem Grund müssen Clients, die aus einer Warteschlange abgerufene Nachrichten verarbeiten, sicherstellen, dass die Nachrichten auf eine idempotente Weise verarbeitet werden. Für Aktualisierungsvorgänge wie SetQueueServiceProperties, SetQueueMetaData, SetQueueACL und UpdateMessage wird die Strategie "Letzter Schreiber gewinnt" verwendet.  

Weitere Informationen finden Sie unter  

* [REST-API des Warteschlangendiensts](https://msdn.microsoft.com/library/azure/dd179363.aspx)
* [Get Messages](https://msdn.microsoft.com/library/azure/dd179474.aspx)  

## <a name="managing-concurrency-in-the-file-service"></a>Verwalten der Nebenläufigkeit im Dateidienst
Der Zugriff auf den Dateidienst kann unter Verwendung zweier unterschiedlicher Protokollendpunkte – SMB und REST – erfolgen. Der REST-Dienst unterstützt weder die optimistische noch die pessimistische Sperrung, und alle Aktualisierungen folgen der Strategie "Letzter Schreiber gewinnt". SMB-Clients, die Dateifreigaben einbinden, können Dateisystem-Sperrmechanismen verwenden, um den Zugriff auf freigegebene Dateien zu verwalten – einschließlich der Möglichkeit, eine pessimistische Sperrung durchzuführen. Wenn ein SMB-Client eine Datei öffnet, gibt er sowohl den Dateizugriffs- als auch den Freigabemodus an. Wird für den Dateizugriff die Option "Schreiben" oder "Lesen/Schreiben" zusammen mit dem Dateifreigabemodus "Kein" festgelegt, wird die Datei von einem SMB-Client gesperrt, bis die Datei geschlossen wird. Wenn ein REST-Vorgang an einer Datei versucht wird, die von einem SMB-Client gesperrt wurde, gibt der REST-Dienst den Statuscode 409 (Konflikt) mit dem Fehlercode SharingViolation zurück.  

Wenn ein SMB-Client eine Datei zum Löschen öffnet, markiert er die Datei als zum Löschen ausstehend, bis alle übrigen offenen SMB-Client-Handles für die betreffende Datei geschlossen sind. Wenn eine Datei als zum Löschen ausstehend markiert ist, geben alle REST-Vorgänge für diese Datei den Statuscode 409 (Konflikt) mit dem Fehlercode SMBDeletePending zurück. Der Statuscode 404 (Nicht gefunden) wird nicht zurückgegeben, da der SMB-Client das Kennzeichen für einen ausstehenden Löschvorgang entfernen kann, bevor die Datei geschlossen wird. Mit anderen Worten, der Statuscode 404 (Nicht gefunden) ist nur zu erwarten, wenn die Datei entfernt wurde. Wenn sich eine Datei im Status eines ausstehenden SMB-Löschvorgangs befindet, ist sie nicht in den Ergebnissen von Dateiauflistungen enthalten. Beachten Sie außerdem, dass die REST-Vorgänge „Datei löschen“ und „Verzeichnis löschen“ atomisch committet werden und nicht zum Status eines ausstehenden Löschvorgangs führen.  

Weitere Informationen finden Sie unter  

* [Verwalten von Dateisperren](https://msdn.microsoft.com/library/azure/dn194265.aspx)  

## <a name="summary-and-next-steps"></a>Zusammenfassung und nächste Schritte
Der Microsoft Azure-Speicherdienst ist auf die Anforderungen äußerst komplexer Online-Anwendungen zugeschnitten, zwingt Entwickler jedoch nicht zur Einschränkung oder Neukonzeption wichtiger Entwurfsvoraussetzungen wie Nebenläufigkeit und Datenkonsistenz, die sie inzwischen für selbstverständlich halten.  

Informationen zur vollständigen Beispielanwendung, auf die in diesem Blog verwiesen wird:  

* [Verwalten von Nebenläufigkeit mit Azure Storage - Beispielanwendung](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114)  

Weitere Informationen zu Azure Storage finden Sie unter:  

* [Microsoft Azure Storage-Startseite](https://azure.microsoft.com/services/storage/)
* [Einführung in Azure Storage](storage-introduction.md)
* Storage – erste Schritte mit [Blobs](../blobs/storage-dotnet-how-to-use-blobs.md), [Tabellen](../../cosmos-db/table-storage-how-to-use-dotnet.md), [Warteschlangen](../storage-dotnet-how-to-use-queues.md) und [Dateien](../storage-dotnet-how-to-use-files.md)
* Storage Architecture – [Azure Storage: A Highly Available Cloud Storage Service with Strong Consistency](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx) (Hochverfügbarer Cloudspeicherdienst mit starker Konsistenz)

