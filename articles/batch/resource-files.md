---
title: Erstellen und Verwenden von Ressourcendateien – Azure Batch | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Azure Batch-Ressourcendateien aus verschiedenen Eingabequellen erstellen.
services: batch
author: laurenhughes
manager: jeconnoc
ms.service: batch
ms.topic: article
ms.date: 03/14/2019
ms.author: lahugh
ms.openlocfilehash: 113faffb0ebac50a67c96ce21e0ee2c1564bb4fc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65405641"
---
# <a name="creating-and-using-resource-files"></a>Erstellen und Verwenden von Ressourcendateien

Eine Azure Batch-Aufgabe erfordert oft eine Form von Daten zur Verarbeitung. Sie können diese Daten Ihrem virtuellen Batch-Computer über eine Aufgabe mithilfe von Ressourcendateien bereitstellen. Ressourcendateien werden von allen Arten von Aufgaben unterstützt: Aufgaben, Startaufgaben, Aufgaben für die Arbeitsvorbereitung, Aufgaben für die Freigabe von Aufträgen, etc. Dieser Artikel behandelt einige gängige Methoden, wie man Ressourcendateien erstellt und auf einer VM platziert.  

Ressourcendateien sind ein Mechanismus, um Daten auf eine VM im Batch zu übertragen, aber die Art der Daten und wie sie verwendet werden, ist flexibel. Es gibt jedoch einige gängige Anwendungsfälle:

1. Bereitstellung allgemeiner Dateien auf jeder VM mithilfe von Ressourcendateien in einer Startaufgabe
1. Bereitstellung von Eingabedaten für die Verarbeitung durch Aufgaben

Allgemeine Dateien können z.B. Dateien einer Startaufgabe sein, die zur Installation von Anwendungen verwendet werden, die Ihre Aufgabe ausführt. Die Eingabedaten können unbearbeitete Bild- oder Videodaten oder beliebige Informationen sein, die von Batch verarbeitet werden sollen.

## <a name="types-of-resource-files"></a>Ressourcendateitypen

Es gibt einige verschiedene Optionen, um Ressourcendateien zu generieren. Der Erstellungsprozess für Ressourcendateien variiert je nachdem, wo die Originaldaten gespeichert sind.

Optionen für die Erstellung einer Ressourcendatei:

- [Speichercontainer-URL](#storage-container-url): Generiert eine Ressourcendatei aus einem Speichercontainer in Azure
- [Speichercontainername](#storage-container-name): Generiert eine Ressourcendatei aus dem Namen eines Containers in einem Azure-Speicherkonto, das mit Batch verknüpft ist
- [Webendpunkt](#web-endpoint): Generiert eine Ressourcendatei aus einer beliebigen gültigen HTTP-URL

### <a name="storage-container-url"></a>Speichercontainer-URL

Die Verwendung einer Speichercontainer-URL bedeutet, dass Sie mit den richtigen Berechtigungen auf Dateien in jedem Speichercontainer in Azure zugreifen können.

In diesem C#-Beispiel wurden die Dateien bereits als Blobspeicher in einen Azure-Speichercontainer hochgeladen. Um auf die Daten zuzugreifen, die für die Erstellung einer Ressourcendatei benötigt werden, müssen wir zunächst Zugriff auf den Speichercontainer erhalten.

Erstellen Sie eine URI für Shared Access Signatures (SAS) mit den richtigen Berechtigungen für den Zugriff auf den Speichercontainer. Legen Sie die Ablaufzeit und die Berechtigungen für die SAS fest. In diesem Fall wird keine Startzeit angegeben, sodass die SAS sofort gültig wird und zwei Stunden nach ihrer Generierung abläuft.

```csharp
SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
{
    SharedAccessExpiryTime = DateTime.UtcNow.AddHours(2),
    Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.List
};
```

> [!NOTE]
> Für den Containerzugriff müssen Sie sowohl `Read`- als auch `List`-Berechtigungen haben, wohingegen Sie beim Blobzugriff nur `Read`-Berechtigungen benötigen.

Nachdem die Berechtigungen konfiguriert sind, erstellen Sie das SAS-Token und formatieren Sie die SAS-URL für den Zugriff auf den Speichercontainer. Generieren Sie unter Verwendung der formatierten SAS-URL für den Speichercontainer eine Ressourcendatei mit [`FromStorageContainerUrl`](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.resourcefile.fromstoragecontainerurl?view=azure-dotnet).

```csharp
CloudBlobContainer container = blobClient.GetContainerReference(containerName);

string sasToken = container.GetSharedAccessSignature(sasConstraints);
string containerSasUrl = String.Format("{0}{1}", container.Uri, sasToken);

ResourceFile inputFile = ResourceFile.FromStorageContainerUrl(containerSasUrl);
```

Eine Alternative zum Generieren einer SAS-URL ist die Aktivierung eines anonymen öffentlichen Lesezugriffs auf einen Container und dessen Blobs in Azure Blob Storage. Auf diese Weise können Sie schreibgeschützten Zugriff auf diese Ressourcen gewähren, ohne Ihren Kontoschlüssel freizugeben und eine SAS zu erfordern. Öffentlicher Lesezugriff wird in der Regel für Szenarien verwendet, in denen bestimmte Blobs stets für anonymen Lesezugriff zur Verfügung stehen sollen. Wenn dieses Szenario zu Ihrer Lösung passt, lesen Sie den Artikel [Anonymer Zugriff auf Blobs](../storage/blobs/storage-manage-access-to-resources.md), um mehr über die Verwaltung des Zugriffs auf Ihre Blobdaten zu erfahren.

### <a name="storage-container-name"></a>Speichercontainername

Anstatt eine SAS-URL zu konfigurieren und zu erstellen, können Sie den Namen Ihres Azure-Speichercontainers verwenden, um auf Ihre Blobdaten zuzugreifen. Der verwendete Speichercontainer muss sich im Azure-Speicherkonto befinden, das mit Ihrem Batch-Konto verknüpft ist, das als Autostorage-Konto bekannt ist. Mit dem Speichercontainernamen eines Autostorage-Kontos können Sie die Konfiguration und Erstellung einer SAS-URL für den Zugriff auf einen Speichercontainer umgehen.

In diesem Beispiel gehen wir davon aus, dass sich die Daten, die für die Erstellung von Ressourcendateien verwendet werden sollen, bereits in einem Azure-Speicherkonto befinden, das mit Ihrem Batch-Konto verknüpft ist. Wenn Sie kein Autostorage-Konto haben, lesen Sie die Schritte unter [Erstellen eines Batch-Kontos](batch-account-create-portal.md) für Details zur Erstellung und Verknüpfung eines Kontos.

Durch die Verwendung eines verknüpften Speicherkonto müssen Sie keine SAS-URL zu einem Speichercontainer erstellen und konfigurieren. Geben Sie stattdessen den Namen des Speichercontainers in Ihrem verknüpften Speicherkonto an.

```csharp
ResourceFile inputFile = ResourceFile.FromAutoStorageContainer(containerName);
```

### <a name="web-endpoint"></a>Webendpunkt

Daten, die nicht zu Azure Storage hochgeladen werden, können weiterhin zum Erstellen von Ressourcendateien verwendet werden. Sie können jede gültige HTTP-URL angeben, die Ihre Eingabedaten enthält. Die URL wird der Batch API bereitgestellt, und dann werden die Daten verwendet, um eine Ressourcendatei zu erstellen.

Im folgenden C#-Beispiel werden die Eingabedaten auf einem fiktiven GitHub-Endpunkt bereitgestellt. Die API ruft die Datei vom gültigen Webendpunkt ab und generiert eine Ressourcendatei, die von Ihrer Aufgabe verwendet werden soll. Für dieses Szenario sind keine Anmeldeinformationen erforderlich.

```csharp
ResourceFile inputFile = ResourceFile.FromUrl("https://github.com/foo/file.txt", filePath);
```

## <a name="tips-and-suggestions"></a>Tipps und Vorschläge

Jede Azure Batch-Aufgabe verwendet Dateien unterschiedlich, weshalb Batch über Optionen zur Verwaltung von Dateien auf Aufgaben verfügt. Die folgenden Szenarien sind nicht als vollständig zu verstehen, sondern decken einige gängige Situationen ab und geben Empfehlungen.

### <a name="many-resource-files"></a>Viele Ressourcendateien

Ihre Batch-Auftrag kann mehrere Aufgaben enthalten, die alle die gleichen allgemeinen Dateien verwenden. Wenn allgemeine Aufgabendateien für viele Tasks gemeinsam genutzt werden, kann die Verwendung eines Anwendungspakets zur Aufnahme der Dateien anstelle von Ressourcendateien eine bessere Option sein. Anwendungspakete bieten eine Optimierung der Downloadgeschwindigkeit. Außerdem werden Daten in Anwendungspaketen zwischen den Aufgaben zwischengespeichert, sodass Anwendungspakete eine gute Wahl für Ihre Lösung sein können, wenn sich Ihre Aufgabendateien nicht oft ändern. Mit Anwendungspaketen müssen Sie nicht mehrere Ressourcendateien manuell verwalten oder SAS-URLs generieren, um auf die Dateien in Azure Storage zuzugreifen. Batch funktioniert im Hintergrund mit Azure Storage zum Speichern von Anwendungspaketen und zum Bereitstellen der Pakete auf Serverknoten.

Wenn jede Aufgabe viele Dateien hat, die für diesen Task einzigartig sind, sind Ressourcendateien wahrscheinlich die beste Option. Aufgaben, die eindeutige Dateien verwenden, müssen oft aktualisiert oder ersetzt werden, was mit dem Inhalt der Anwendungspakete nicht so einfach ist. Ressourcendateien bieten zusätzliche Flexibilität beim Aktualisieren, Hinzufügen oder Bearbeiten einzelner Dateien.

### <a name="number-of-resource-files-per-task"></a>Anzahl von Ressourcendateien pro Aufgabe

Wenn mehrere hundert Ressourcendateien für eine Aufgabe angegeben sind, kann Batch die Aufgabe ablehnen, weil sie zu groß ist. Es ist am besten, Ihre Aufgaben klein zu halten, indem Sie die Anzahl der Ressourcendateien in der Aufgabe selbst minimieren.

Wenn es keine Möglichkeit gibt, die Anzahl der Dateien zu minimieren, die Ihre Aufgabe benötigt, können Sie die Aufgabe optimieren, indem Sie eine einzelne Ressourcendatei erstellen, die auf einen Speichercontainer mit Ressourcendateien verweist. Legen Sie dazu Ihre Ressourcendateien in einen Azure Storage-Container und verwenden Sie die verschiedenen „Container“-Modi von Ressourcendateien. Verwenden Sie die Optionen für das Blobpräfix, um Sammlungen von Dateien anzugeben, die für Ihre Aufgaben heruntergeladen werden sollen.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über [Anwendungspakete](batch-application-packages.md) als Alternative zu Ressourcendateien.
- Weitere Informationen zur Verwendung von Containern für Ressourcendateien finden Sie unter [Containerworkloads](batch-docker-container-workloads.md).
- Um zu erfahren, wie Sie die Ausgabedaten Ihrer Aufgaben sammeln und speichern können, lesen Sie [Persistente Aufträge und Aufgabenausgabe](batch-task-output.md).
- Informieren Sie sich über die [Batch-APIs und Tools](batch-apis-tools.md), die für die Erstellung von Batch-Lösungen verfügbar sind.
