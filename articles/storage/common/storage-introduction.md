---
title: Einführung in Azure Storage – Cloudspeicher in Azure | Microsoft-Dokumentation
description: Die Azure Storage-Kernplattform ist die Cloudspeicherlösung von Microsoft. Azure Storage stellt Speicher für Datenobjekte bereit, die hoch verfügbar, sicher, langlebig, stark skalierbar und redundant sind.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 04/08/2020
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: b49e780714bd5e0662c6cb7ef208c473a8b45ccc
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96498148"
---
# <a name="introduction-to-the-core-azure-storage-services"></a>Einführung in die zentralen Azure Storage-Dienste

Die Azure Storage-Plattform ist die Cloudspeicherlösung von Microsoft für moderne Datenspeicherszenarien. Die zentralen Speicherdienste verfügen über einen hochgradig skalierbaren Objektspeicher für Datenobjekte, Datenträgerspeicher für virtuelle Azure-Computer (VMs), einen Dateisystemdienst für die Cloud, einen Nachrichtenspeicher für zuverlässiges Messaging und einen NoSQL-Speicher. Die Dienste haben die folgenden Eigenschaften:

- **Robust und hoch verfügbar:** Mit Redundanz wird sichergestellt, dass Ihre Daten sicher sind, falls es zu vorübergehenden Hardwareausfällen kommt. Sie können sich auch für das Replizieren von Daten über Rechenzentren oder geografische Regionen hinweg entscheiden, um eine weitere Schutzebene vor lokalen Notfällen oder Naturkatastrophen zu schaffen. Daten, die auf diese Weise repliziert werden, sind bei einem unerwarteten Ausfall weiterhin hoch verfügbar.
- **Sicher:** Alle Daten, die in ein Azure-Speicherkonto geschrieben werden, werden vom Dienst verschlüsselt. Bei Azure Storage können Sie genau steuern, wer Zugriff auf Ihre Daten hat.
- **Skalierbar:** Azure Storage ist auf hohe Skalierbarkeit ausgelegt, um die Datenspeicherungs- und Leistungsanforderungen heutiger Anwendungen zu erfüllen.
- **Verwaltet:** In Azure werden Hardwarewartung, Updates und die Behandlung kritischer Probleme für Sie übernommen.
- **Zugänglich:** Auf Daten in Azure Storage kann weltweit per HTTP oder HTTPS zugegriffen werden. Microsoft stellt Clientbibliotheken für Azure Storage in verschiedenen Sprachen (z. B. .NET, Java, Node.js, Python, PHP, Ruby und Go) sowie eine ausgereifte REST-API bereit. Azure Storage unterstützt die Skripterstellung in Azure PowerShell oder der Azure CLI. Darüber hinaus werden im Azure-Portal und über Azure Storage-Explorer einfache visuelle Lösungen für die Arbeit mit Ihren Daten bereitgestellt.  

## <a name="core-storage-services"></a>Zentrale Speicherdienste

Die Azure Storage-Plattform umfasst die folgenden Datendienste:

- [Azure-Blobs](../blobs/storage-blobs-introduction.md): Ein überaus skalierbarer Objektspeicher für Text- und Binärdaten. Er verfügt auch über Unterstützung für Big Data-Analysen mit Data Lake Storage Gen2.
- [Azure Files](../files/storage-files-introduction.md): Verwaltete Dateifreigaben für Bereitstellungen lokal oder in der Cloud.
- [Azure-Warteschlangen](../queues/storage-queues-introduction.md): Ein Messagingspeicher für zuverlässiges Messaging zwischen Anwendungskomponenten.
- [Azure-Tabellen](../tables/table-storage-overview.md): Ein NoSQL-Speicher für die schemalose Speicherung von strukturierten Daten.
- [Azure-Datenträger](../../virtual-machines/managed-disks-overview.md): Speichervolumes auf Blockebene für virtuelle Azure-Computer.

Auf jeden Dienst wird über ein Speicherkonto zugegriffen. Informationen zu den ersten Schritten finden Sie unter [Speicherkonto erstellen](storage-account-create.md).

## <a name="example-scenarios"></a>Beispielszenarien

In der folgenden Tabelle werden die Dienste für Dateien, Blobs, Datenträger, Warteschlangen und Tabellen miteinander verglichen. Darüber hinaus sind jeweils passende Beispielszenarien angegeben.

| Funktion | BESCHREIBUNG | Verwendung |
|--------------|-------------|-------------|
| **Azure Files** |Verfügt über vollständig verwaltete Clouddateifreigaben, auf die Sie von überall aus mit dem branchenüblichen SMB-Protokoll (Server Message Block) zugreifen können.<br><br>Sie können Azure-Dateifreigaben über Cloud- und lokale Bereitstellungen von Windows, Linux und macOS einbinden. | Sie möchten eine Anwendung mit der „Lift & Shift“-Methode in die Cloud verschieben, die bereits die nativen Dateisystem-APIs verwendet, um Daten für andere in Azure ausgeführte Anwendungen freizugeben.<br/><br/>Sie möchten lokale Dateiserver oder NAS-Geräte austauschen oder ergänzen.<br><br> Wenn Sie Tools zum Entwickeln und Debuggen speichern möchten, auf die von vielen virtuellen Computern zugegriffen werden muss. |
| **Azure-Blobs** | Ermöglicht für unstrukturierte Daten in großem Umfang die Speicherung und den Zugriff in Blockblobs.<br/><br/>Blob Storage unterstützt darüber hinaus [Azure Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md) für Big Data-Analyselösungen auf Unternehmensniveau. | Wenn Sie möchten, dass Ihre Anwendung Szenarios für das Streaming und den zufälligen Zugriff unterstützt.<br/><br/>Wenn Sie die Möglichkeit haben möchten, von überall auf Anwendungsdaten zugreifen zu können.<br/><br/>Sie möchten einen Unternehmens-Data Lake in Azure aufbauen und Big Data-Analysen durchführen. |
| **Azure-Datenträger** | Ermöglicht die dauerhafte Speicherung und den Zugriff auf Daten über eine angefügte virtuelle Festplatte. | Sie möchten mit der „Lift & Shift“-Methode Anwendungen verschieben, die native Dateisystem-APIs verwenden, um für Daten auf beständigen Datenträgern Lese- und Schreibvorgänge durchzuführen.<br/><br/>Wenn Sie Daten speichern möchten, auf die nicht von außerhalb des virtuellen Computers zugegriffen werden muss, an den der Datenträger angefügt ist. |
| **Azure-Warteschlangen** | Ermöglicht die Nutzung von asynchronen Nachrichtenwarteschlangen zwischen Anwendungskomponenten. | Sie möchten Anwendungskomponenten entkoppeln und asynchrones Messaging für die Kommunikation zwischen den Komponenten verwenden.<br><br>Informationen dazu, ob Warteschlangenspeicher oder Service Bus-Warteschlangen genutzt werden sollten, finden Sie unter [Storage-Warteschlangen und Service Bus-Warteschlangen – Vergleich und Gegenüberstellung](../../service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted.md). |
| **Azure-Tabellen** | Ermöglicht das Speichern von strukturierten NoSQL-Daten in der Cloud mit einem Schlüssel-/Attributspeicher mit schemalosem Design. | Sie möchten flexible Datasets wie Benutzerdaten für Webanwendungen, Adressbücher, Geräteinformationen und andere Arten von Metadaten speichern, die für Ihren Dienst benötigt werden. <br/><br/>Informationen dazu, ob Tabellenspeicher oder die Azure Cosmos DB-Tabellen-API verwendet werden soll, finden Sie unter [Entwickeln mit der Azure Cosmos DB-Tabellen-API und Azure Table Storage](../../cosmos-db/table-support.md). |

## <a name="blob-storage"></a>Blob Storage

Azure Blob Storage ist die Objektspeicherlösung von Microsoft für die Cloud. Blobspeicher ist für die Speicherung großer Mengen von unstrukturierten Daten, z.B. Text oder Binärdaten, optimiert.

Blobspeicher ist für folgende Zwecke ideal geeignet:

- Speichern von Bildern oder Dokumenten direkt für einen Browser
- Speichern von Dateien für verteilten Zugriff
- Video- und Audio-Streaming
- Speichern von Daten für Sicherung und Wiederherstellung, Notfallwiederherstellung und Archivierung
- Speichern von Daten für Analysen durch einen lokalen oder von Azure gehosteten Dienst

Über HTTP oder HTTPS kann von allen Orten weltweit auf Objekte zugegriffen werden, die sich in Blobspeicher befinden. Benutzer oder Clientanwendungen können über URLs, die [Azure Storage-REST-API](/rest/api/storageservices/blob-service-rest-api), [Azure PowerShell](/powershell/module/azure.storage), die [Azure CLI](/cli/azure/storage) oder eine Azure Storage-Clientbibliothek auf Blobs zugreifen. Die Speicherclientbibliotheken sind für mehrere Sprachen verfügbar, z.B. [.NET](/dotnet/api/overview/azure/storage), [Java](/java/api/overview/azure/storage), [Node.js](https://azure.github.io/azure-storage-node), [Python](https://azure-storage.readthedocs.io/), [PHP](https://azure.github.io/azure-storage-php/) und [Ruby](https://azure.github.io/azure-storage-ruby).

Weitere Informationen zu Blob Storage finden Sie unter [Einführung in Blob Storage](../blobs/storage-blobs-introduction.md).

## <a name="azure-files"></a>Azure Files

[Azure Files](../files/storage-files-introduction.md) ermöglicht die Einrichtung hochverfügbarer Netzwerkdateifreigaben, auf die über das standardmäßige SMB-Protokoll (Server Message Block) zugegriffen werden kann. Dadurch können mehrere virtuelle Computer gemeinsam die gleichen Dateien mit Lese- und Schreibzugriff nutzen. Die Dateien können auch mithilfe der REST-Schnittstelle oder mithilfe der Speicherclientbibliotheken gelesen werden.

Azure Files unterscheidet sich in einem Punkt von Dateien auf einer Dateifreigabe eines Unternehmens: Über eine URL, die auf die gewünschte Datei verweist und ein SAS-Token (Shared Access Signature) enthält, kann von jedem Ort der Welt auf die Datei zugegriffen werden. Sie können SAS-Token generieren, um für einen bestimmten Zeitraum spezifischen Zugriff auf eine private Ressource zu ermöglichen.

Dateifreigaben können in zahlreichen Szenarien verwendet werden:

- Viele lokale Anwendungen verwenden Dateifreigaben. Dieses Feature erleichtert die Migration dieser Anwendungen mit gemeinsamen Daten zu Azure. Wenn Sie die Dateifreigabe unter dem gleichen Laufwerksbuchstaben einbinden, den auch die lokale Anwendung verwendet, müsste der Teil Ihrer Anwendung, der auf die Dateifreigabe zugreift, mit minimalen (oder sogar ganz ohne) Änderungen funktionieren.

- Konfigurationsdateien können auf einer Dateifreigabe gespeichert und von mehreren virtuellen Computern genutzt werden. Tools und Hilfsprogramme, die von mehreren Entwicklern in einer Gruppe verwendet werden, können auf einer Dateifreigabe gespeichert werden, um sicherzustellen, dass sie von allen gefunden werden und dass alle die gleiche Version verwenden.

- Ressourcenprotokolle, Metriken und Absturzabbilder sind nur drei Beispiele für Daten, die zur späteren Verarbeitung oder Analyse auf eine Dateifreigabe geschrieben werden können.

Weitere Informationen zu Azure Files finden Sie unter [Einführung in Azure Files](../files/storage-files-introduction.md).

Einige SMB-Funktion sind nicht in der Cloud verfügbar. Weitere Informationen finden Sie unter [Features not supported by the Azure File service (Funktionen, die vom Azure-Dateidienst nicht unterstützt werden)](/rest/api/storageservices/features-not-supported-by-the-azure-file-service).

## <a name="queue-storage"></a>Queue Storage

Der Azure Queue-Dienst wird zum Speichern und Abrufen von Nachrichten verwendet. Warteschlangennachrichten können eine Größe von bis zu 64 KB haben, und eine Warteschlange kann Millionen von Nachrichten enthalten. Warteschlangen dienen im Allgemeinen zum Speichern von Listen mit Nachrichten, die asynchron verarbeitet werden sollen.

Beispiel: Ihre Kunden sollen Bilder hochladen können, und Sie möchten Miniaturbilder für jedes Bild erstellen. Sie können Kunden warten lassen, bis die Miniaturbilder beim Hochladen der Bilder erstellt werden. Alternativ können Sie eine Warteschlange verwenden. Wenn der Kunde seinen Upload abgeschlossen hat, schreiben Sie eine Nachricht an die Warteschlange. Legen Sie dann eine Azure-Funktion fest, die die Nachricht aus der Warteschlange abruft und die Miniaturbilder erstellt. Alle Schritte dieses Verarbeitungsvorgangs können separat skaliert werden, sodass Sie ihn für Ihre Zwecke optimieren können.

Weitere Informationen zu Azure-Warteschlangen finden Sie unter [Einführung in Warteschlangen](../queues/storage-queues-introduction.md).

## <a name="table-storage"></a>Table Storage

Azure Table Storage ist jetzt Teil von Azure Cosmos DB. Diese Azure Table Storage-Dokumentation finden Sie unter [Übersicht über Azure Table Storage](../tables/table-storage-overview.md). Zusätzlich zum vorhandenen Azure Table Storage-Dienst ist das neue Angebot „Azure Cosmos DB-Tabellen-API“ mit durchsatzoptimierten Tabellen, globaler Verteilung und automatischen sekundären Indizes verfügbar. Weitere Informationen sowie eine Möglichkeit zum Testen des neuen Premiumangebots finden Sie unter [Einführung in die Tabellen-API von Azure Cosmos DB](../../cosmos-db/table-introduction.md).

Weitere Informationen zu Table Storage finden Sie unter [Übersicht über Azure Table Storage](../tables/table-storage-overview.md).

## <a name="disk-storage"></a>Datenträgerspeicher

Ein verwalteter Azure-Datenträger ist eine virtuelle Festplatte (Virtual Hard Disk, VHD). Sie können sich dies wie einen physischen Datenträger in einem lokalen Server vorstellen, aber der Unterschied besteht darin, dass er virtualisiert ist. Verwaltete Azure-Datenträger werden als Seitenblobs gespeichert, bei denen es sich in Azure um ein zufälliges E/A-Speicherobjekt handelt. Wir bezeichnen einen verwalteten Datenträger als „verwaltet“, weil es eine Abstraktion ist, die sich über Seitenblobs, Blobcontainer und Azure-Speicherkonten erstreckt. Bei verwalteten Datenträgern müssen Sie nicht mehr tun, als den Datenträger bereitzustellen. Azure übernimmt dann den Rest.

Weitere Informationen zu verwalteten Datenträgern finden Sie in der [Einführung in Azure Managed Disks](../../virtual-machines/managed-disks-overview.md).

## <a name="types-of-storage-accounts"></a>Speicherkontentypen

Azure Storage bietet mehrere Arten von Speicherkonten. Jeder Typ unterstützt unterschiedliche Features und verfügt über ein eigenes Preismodell. Weitere Informationen zu Arten von Speicherkontotypen finden Sie unter [Übersicht über Azure Storage-Konten](storage-account-overview.md).

## <a name="secure-access-to-storage-accounts"></a>Schützen des Zugriffs auf Speicherkonten

Jede Anforderung an Azure Storage muss autorisiert sein. Azure Storage unterstützt die folgenden Autorisierungsmethoden:

- **Integration von Azure Active Directory (Azure AD) für Blob- und Warteschlangendaten.** Azure Storage unterstützt die Authentifizierung und Autorisierung mit Azure AD für Blob- und Warteschlangendienste über die rollenbasierte Zugriffssteuerung in Azure (Azure RBAC). Für mehr Sicherheit und Benutzerfreundlichkeit wird die Autorisierung von Anforderungen mit Azure AD empfohlen. Weitere Informationen finden Sie unter [Authorize access to Azure blobs and queues using Azure Active Directory (Autorisieren des Zugriffs auf Azure-Blobs und -Warteschlangen mit Azure Active Directory)](storage-auth-aad.md).
- **Azure AD-Autorisierung über SMB für Azure Files.** Azure Files unterstützt die identitätsbasierte Autorisierung per SMB (Server Message Block), indem entweder Azure Active Directory Domain Services (Azure AD DS) oder eine lokale Instanz von Active Directory Domain Services (Vorschauversion) verwendet wird. Ihre in die Domäne eingebundenen Windows-VMs können mit Azure AD-Anmeldeinformationen auf Azure-Dateifreigaben zugreifen. Weitere Informationen finden Sie unter [Übersicht über die Unterstützung der identitätsbasierten Authentifizierung mit Azure Files für den SMB-Zugriff](../files/storage-files-active-directory-overview.md) und [Planung für eine Azure Files-Bereitstellung](../files/storage-files-planning.md#identity).
- **Autorisierung mit gemeinsam verwendetem Schlüssel.** Die Dienste Azure Storage Blob, Files, Queue und Table unterstützen die Autorisierung per gemeinsam verwendetem Schlüssel. Ein Client mit Autorisierung per gemeinsam verwendetem Schlüssel übergibt mit jeder Anforderung einen Header, der mit dem Speicherkonto-Zugriffsschlüssel signiert wird. Weitere Informationen finden Sie unter [Authentifizieren mit gemeinsam verwendetem Schlüssel](/rest/api/storageservices/authorize-with-shared-key).
- **Autorisierung mit Shared Access Signatures (SAS).** Eine Shared Access Signature (SAS) ist eine Zeichenfolge mit einem Sicherheitstoken, das an den URI für eine Speicherressource angefügt werden kann. Im Sicherheitstoken sind Einschränkungen wie Berechtigungen und das Zugriffsintervall gekapselt. Weitere Informationen finden Sie unter [Verwenden von Shared Access Signatures (SAS)](storage-sas-overview.md).
- **Anonymer Zugriff auf Container und Blobs.** Ein Container und seine Blobs können öffentlich verfügbar sein. Wenn Sie einen Container oder ein Blob als öffentliche Ressource festlegen, kann jeder Benutzer anonym und ohne Authentifizierung darauf zugreifen. Weitere Informationen finden Sie unter [Verwalten des anonymen Lesezugriffs auf Container und Blobs](../blobs/anonymous-read-access-configure.md).

## <a name="encryption"></a>Verschlüsselung

Für die wichtigsten Speicherdienste stehen zwei grundlegende Verschlüsselungsarten zur Verfügung. Ausführlichere Informationen zu Sicherheit und Verschlüsselung finden Sie im [Azure Storage-Sicherheitsleitfaden](../blobs/security-recommendations.md).

### <a name="encryption-at-rest"></a>Verschlüsselung ruhender Daten

Durch die Azure Storage-Verschlüsselung werden Ihre Daten ausreichend geschützt, um den Sicherheits- und Complianceanforderungen Ihrer Organisation gerecht zu werden. Vor der dauerhaften Speicherung im Speicherkonto werden alle Daten automatisch durch Azure Storage verschlüsselt. Beim Abrufen werden die Daten entschlüsselt. Die Verschlüsselungs-, Entschlüsselungs- und Schlüsselverwaltungsprozesse sind für Benutzer transparent. Die Kunden können ihre eigenen Schlüssel mithilfe von Azure Key Vault verwalten. Weitere Informationen finden Sie unter [Azure Storage-Verschlüsselung für ruhende Daten](storage-service-encryption.md).

### <a name="client-side-encryption"></a>Clientseitige Verschlüsselung

Die Clientbibliotheken für Azure Storage umfassen verschiedene Methoden zum Verschlüsseln von Daten aus der Clientbibliothek, bevor sie gesendet werden und die Antwort entschlüsselt wird. Daten, die über die clientseitige Verschlüsselung verschlüsselt werden, werden auch im ruhenden Zustand von Azure Storage verschlüsselt. Weitere Informationen zur clientseitigen Verschlüsselung finden Sie unter [Client-side encryption with .NET for Azure Storage (Clientseitige Verschlüsselung mit .NET für Azure Storage)](storage-client-side-encryption.md).

## <a name="redundancy"></a>Redundanz

Um die Dauerhaftigkeit Ihrer Daten zu gewährleisten, speichert Azure Storage mehrere Kopien Ihrer Daten. Beim Einrichten Ihres Speicherkontos wählen Sie eine Redundanzoption aus. Weitere Informationen finden Sie unter [Azure Storage-Redundanz](./storage-redundancy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="transfer-data-to-and-from-azure-storage"></a>Übertragen von Daten in und aus Azure Storage

Sie haben mehrere Optionen zum Verschieben von Daten in oder aus Azure Storage. Welche Option Sie auswählen, hängt von der Größe Ihres Datasets und der verfügbaren Netzwerkbandbreite ab. Weitere Informationen finden Sie unter [Auswählen einer Azure-Lösung für die Datenübertragung](storage-choose-data-transfer-solution.md).

## <a name="pricing"></a>Preise

Bei der Entscheidung für die Art und Weise, wie Sie Ihre Daten speichern und auf diese zugreifen möchten, sollten Sie auch die Kosten berücksichtigen. Weitere Informationen finden Sie unter [Preise für Azure Storage](https://azure.microsoft.com/pricing/details/storage/).

## <a name="storage-apis-libraries-and-tools"></a>Storage-APIs, -Bibliotheken und -Tools

Sie können auf die Ressourcen in einem Speicherkonto mit allen Sprachen zugreifen, mit denen HTTP/HTTPS-Anforderungen gesendet werden können. Darüber hinaus verfügen die Azure Storage-Kerndienste über Programmierbibliotheken für mehrere gängige Sprachen. Diese Bibliotheken vereinfachen viele Aspekte der Arbeit mit Azure Storage und behandeln bestimmte Details wie synchrone und asynchrone Aufrufe, die Zusammenfassung von Vorgängen, die Ausnahmeverwaltung, automatische Wiederholungen, das Betriebsverhalten und Ähnliches. Bibliotheken sind aktuell für die folgenden Sprachen und Plattformen erhältlich. Weitere sind in Planung.

### <a name="azure-storage-data-api-and-library-references"></a>Verweise auf die Azure Storage-Daten-API und Bibliotheken

- [Azure Storage-REST-API](/rest/api/storageservices/)
- [Azure Storage-Clientbibliothek für .NET](/dotnet/api/overview/azure/storage)
- [Azure Storage-Clientbibliothek für Java/Android](/java/api/overview/azure/storage)
- [Azure Storage-Clientbibliothek für Node.js](/javascript/api/overview/azure/storage-overview)
- [Azure Storage-Clientbibliothek für Python](https://github.com/Azure/azure-storage-python)
- [Azure Storage-Clientbibliothek für PHP](https://github.com/Azure/azure-storage-php)
- [Azure Storage-Clientbibliothek für Ruby](https://github.com/Azure/azure-storage-ruby)
- [Azure Storage-Clientbibliothek für C++](https://github.com/Azure/azure-storage-cpp)

### <a name="azure-storage-management-api-and-library-references"></a>Verweise auf die Azure Storage-Verwaltungs-API und Bibliotheken

- [Speicherressourcenanbieter – REST-API](/rest/api/storagerp/)
- [Speicherressourcenanbieter – Clientbibliothek für .NET](/dotnet/api/overview/azure/storage/management)
- [Storage-Dienstverwaltung – REST-API (klassisch)](/previous-versions/azure/reference/ee460790(v=azure.100))

### <a name="azure-storage-data-movement-api-and-library-references"></a>Verweise auf die Azure Storage-Datenverschiebungs-API und Bibliotheken

- [Import/Export-Storage-Dienst – REST-API](/rest/api/storageimportexport/)
- [Storage-Datenverschiebung – Clientbibliothek für .NET](/dotnet/api/microsoft.azure.storage.datamovement)

### <a name="tools-and-utilities"></a>Tools und Hilfsprogramme

- [Azure PowerShell-Cmdlets für Storage](/powershell/module/az.storage)
- [Azure CLI-Cmdlets für Storage](/cli/azure/storage)
- [Befehlszeilenprogramm AzCopy](https://aka.ms/downloadazcopy)
- Bei [Azure Storage-Explorer](https://azure.microsoft.com/features/storage-explorer/) handelt es sich um eine kostenlose eigenständige App von Microsoft, über die Sie ganz einfach visuell mit Azure Storage-Daten arbeiten können – unter Windows, MacOS und Linux.
- [Azure Resource Manager-Vorlagen für Azure Storage](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Storage)

## <a name="next-steps"></a>Nächste Schritte

Informationen zur Herstellung der Betriebsbereitschaft für die Kerndienste von Azure Storage finden Sie unter [Erstellen eines Speicherkontos](storage-account-create.md).