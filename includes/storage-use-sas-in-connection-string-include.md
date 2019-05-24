---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 2f27c50b1d016265c20102521a137bcbb0646115
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66115503"
---
Wenn Sie über eine URL für Shared Access Signature (SAS) verfügen, die den Zugriff auf Ressourcen in einem Speicherkonto ermöglicht, können Sie die SAS in einer Verbindungszeichenfolge verwenden. Da die SAS die zum Authentifizieren der Anforderung erforderlichen Informationen enthält, werden über eine Verbindungszeichenfolge mit einer SAS das Protokoll, der Dienstendpunkt und die erforderlichen Anmeldeinformationen zum Zugreifen auf die Ressource bereitgestellt.

Um eine Verbindungszeichenfolge zu erstellen, die eine SAS enthält, geben Sie die Zeichenfolge im folgenden Format ein:

```
BlobEndpoint=myBlobEndpoint;
QueueEndpoint=myQueueEndpoint;
TableEndpoint=myTableEndpoint;
FileEndpoint=myFileEndpoint;
SharedAccessSignature=sasToken
```

Jeder Dienstendpunkt ist optional, obwohl die Verbindungszeichenfolge mindestens einen enthalten muss.

> [!NOTE]
> Die Verwendung von HTTPS mit einem SAS wird als bewährte Methode empfohlen.
>
> Wenn Sie eine SAS in einer Verbindungszeichenfolge in einer Konfigurationsdatei angeben, müssen Sie womöglich Sonderzeichen in der URL codieren.
>
>

### <a name="service-sas-example"></a>Beispiel für Dienst-SAS
Hier ist ein Beispiel einer Verbindungszeichenfolge, die eine Dienst-SAS für Blobspeicher enthält:

```
BlobEndpoint=https://storagesample.blob.core.windows.net;
SharedAccessSignature=sv=2015-04-05&sr=b&si=tutorial-policy-635959936145100803&sig=9aCzs76n0E7y5BpEi2GvsSv433BZa22leDOZXX%2BXXIU%3D
```

Und hier ist ein Beispiel für die gleiche Verbindungszeichenfolge mit codierten Sonderzeichen:

```
BlobEndpoint=https://storagesample.blob.core.windows.net;
SharedAccessSignature=sv=2015-04-05&amp;sr=b&amp;si=tutorial-policy-635959936145100803&amp;sig=9aCzs76n0E7y5BpEi2GvsSv433BZa22leDOZXX%2BXXIU%3D
```

### <a name="account-sas-example"></a>Beispiel für Konto-SAS
Hier ist ein Beispiel einer Verbindungszeichenfolge, die eine Dienst-SAS für Blob-und Dateispeicher enthält: Beachten Sie, dass Endpunkte für beide Dienste angegeben werden:

```
BlobEndpoint=https://storagesample.blob.core.windows.net;
FileEndpoint=https://storagesample.file.core.windows.net;
SharedAccessSignature=sv=2015-07-08&sig=iCvQmdZngZNW%2F4vw43j6%2BVz6fndHF5LI639QJba4r8o%3D&spr=https&st=2016-04-12T03%3A24%3A31Z&se=2016-04-13T03%3A29%3A31Z&srt=s&ss=bf&sp=rwl
```

Und hier ist ein Beispiel für die gleiche Verbindungszeichenfolge mit URL-Codierung:

```
BlobEndpoint=https://storagesample.blob.core.windows.net;
FileEndpoint=https://storagesample.file.core.windows.net;
SharedAccessSignature=sv=2015-07-08&amp;sig=iCvQmdZngZNW%2F4vw43j6%2BVz6fndHF5LI639QJba4r8o%3D&amp;spr=https&amp;st=2016-04-12T03%3A24%3A31Z&amp;se=2016-04-13T03%3A29%3A31Z&amp;srt=s&amp;ss=bf&amp;sp=rwl
```

