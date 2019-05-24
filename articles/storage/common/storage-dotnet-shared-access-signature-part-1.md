---
title: Verwenden von Shared Access Signatures (SAS) in Azure Storage | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Shared Access Signatures (SAS) verwenden, um Zugriff auf Azure Storage-Ressourcen, einschließlich Blobs, Warteschlangen, Tabellen und Dateien, zu delegieren.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/18/2017
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 8bee0426f171b0fdb7793d18c352649928fdb2e8
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65907179"
---
# <a name="using-shared-access-signatures-sas"></a>Verwenden von Shared Access Signatures (SAS)

Eine Shared Access Signature (SAS) bietet Ihnen die Möglichkeit, anderen Clients eingeschränkten Zugriff auf Objekte in Ihrem Speicherkonto zu gewähren, ohne dass Sie Ihren Kontoschlüssel verfügbar machen. Dieser Artikel enthält eine Übersicht über das SAS-Modell, eine Besprechung der bewährten Methoden für SAS und einige Beispiele samt Erläuterungen.

Weitere Codebeispiele als die hier angegebenen, in denen SAS verwendet wird, finden Sie unter [Getting Started with Azure Blob Storage in .NET](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/) (Erste Schritte mit Azure Blob Storage in .NET). Andere Beispiele sind in der Bibliothek [Azure-Codebeispiele](https://azure.microsoft.com/documentation/samples/?service=storage) verfügbar. Sie können die Beispielanwendungen herunterladen und ausführen oder den Code auf GitHub durchsuchen.

## <a name="what-is-a-shared-access-signature"></a>Was ist eine Shared Access Signature?
Shared Access Signatures bieten delegierten Zugriff auf Ressourcen in Ihrem Speicherkonto. Mit einer SAS können Sie Clients Zugriff auf Ressourcen unter Ihrem Speicherkonto gewähren, ohne dafür Kontoschlüssel weitergeben zu müssen. Der wichtigste Aspekt bei der Verwendung von Shared Access Signatures in Ihren Anwendungen: Bei einer SAS handelt es sich um eine sichere Methode zur Freigabe Ihrer Speicherressurcen, ohne dass Sie Kompromisse bei der Sicherheit der Kontoschlüssel eingehen müssen.

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

Mit einer SAS können Sie genauestens steuern, welche Art von Zugriff Sie Clients gewähren, die über die SAS verfügen. Dazu gehören:

* Intervallzeitraum der SAS-Gültigkeit, einschließlich Startzeit und Ablaufzeit.
* Berechtigungen, die von der SAS gewährt werden. Beispielsweise könnte eine SAS für ein Blob Lese- und Schreibberechtigungen für dieses Blob gewähren, aber keine Löschberechtigungen.
* Eine optionale IP-Adresse oder ein IP-Adressbereich, für den Azure Storage die SAS akzeptiert. Beispielsweise können Sie einen Bereich mit IP-Adressen angeben, der von Ihrer Organisation genutzt wird.
* Das Protokoll, über das Azure Storage die SAS akzeptiert. Sie können diesen optionalen Parameter verwenden, um den Zugriff auf Clients per HTTPS einzuschränken.

## <a name="when-should-you-use-a-shared-access-signature"></a>Wann sollten Sie eine Shared Access Signature verwenden?
Sie können eine SAS verwenden, wenn Sie einem Client, der nicht im Besitz der Kontoschlüssel Ihres Speicherkontos ist, Zugriff auf Ressourcen in Ihrem Speicherkonto gewähren möchten. Ihr Speicherkonto enthält sowohl einen primären als auch einen sekundären Zugriffsschlüssel, die beide Administratorzugriff auf Ihr Konto und alle in ihm enthaltenen Ressourcen gewähren. Wenn Sie einen dieser Schlüssel verfügbar machen, setzen Sie Ihr Konto der Gefahr von böswilliger oder fahrlässiger Nutzung aus. Shared Access Signatures bieten eine sichere Alternative, die Clients das Lesen, Schreiben und Löschen von Daten in Ihrem Speicherkonto entsprechend den Berechtigungen ermöglichen, die Sie explizit erteilt haben, und dies, ohne dass ein Kontoschlüssel erforderlich ist.

SAS sind zum Beispiel dann hilfreich, wenn Benutzer ihre eigenen Daten in Ihrem Speicherkonto ablegen und von dort abrufen. Für den Fall, dass ein Speicherkonto Benutzerdaten enthält, existieren zwei typische Designmuster:

1. Clients laden Daten über einen Front-End-Proxydienst hoch und herunter, der die Authentifizierung übernimmt. Dieser Front-End-Proxydienst hat den Vorteil, dass auch Geschäftsregeln validiert werden können. Allerdings kann die Erstellung eines skalierbaren Dienstes für große Datenmengen oder Transaktionen mit großem Volumen teuer und aufwändig sein.

   ![Szenariodiagramm: Front-End-Proxydienst](./media/storage-dotnet-shared-access-signature-part-1/sas-storage-fe-proxy-service.png)   

1. Ein einfacher Dienst authentifiziert den Client nach Bedarf und generiert anschließend die SAS. Sobald der Client die SAS erhält, kann er direkt gemäß der in der SAS definierten Berechtigungen für den definierten Zeitraum auf die Speicherkonto-Ressourcen zugreifen. Dank der SAS müssen nicht mehr alle Daten durch einen Front-End-Proxydienst geleitet werden.

   ![Szenariodiagramm: SAS-Anbieterdienst](./media/storage-dotnet-shared-access-signature-part-1/sas-storage-provider-service.png)   

Für viele tatsächlich arbeitende Dienste wird möglicherweise eine Mischung dieser beiden Ansätze verwendet. Beispielsweise kann es sein, dass einige Daten über den Front-End-Proxy verarbeitet und überprüft werden, während andere Daten direkt mit SAS gespeichert und/oder gelesen werden.

Darüber hinaus muss in bestimmten Szenarien eine SAS verwendet werden, um in einem Kopiervorgang den Zugriff auf das Quellobjekt zu autorisieren:

* Wenn Sie ein Blob in ein anderes Blob kopieren, das sich in einem anderen Speicherkonto befindet, müssen Sie eine SAS verwenden, um den Zugriff auf das Quellblob zu autorisieren. Sie können optional eine SAS verwenden, um auch den Zugriff auf das Zielblob zu autorisieren.
* Wenn Sie eine Datei in eine andere Datei kopieren, das sich in einem anderen Speicherkonto befindet, müssen Sie eine SAS verwenden, um den Zugriff auf die Quelldatei zu autorisieren. Sie können optional eine SAS verwenden, um auch den Zugriff auf die Zieldatei zu autorisieren.
* Wenn Sie ein Blob in eine Datei oder eine Datei in ein Blob kopieren, müssen Sie eine SAS verwenden, um den Zugriff auf das Quellobjekt zu autorisieren. Das gilt auch, wenn sich Quell- und Zielobjekt im gleichen Speicherkonto befinden.

## <a name="types-of-shared-access-signatures"></a>Arten von Shared Access Signatures
Sie können zwei Arten von Shared Access Signatures erstellen:

* **Dienst-SAS:** Die Dienst-SAS delegiert den Zugriff auf eine Ressource in nur einem der folgenden Speicherdienste: Blob Storage, Queue Storage, Table Storage oder File Storage. Ausführliche Informationen zur Erstellung des Dienst-SAS-Tokens finden Sie unter [Erstellen einer Dienst-SAS](https://msdn.microsoft.com/library/dn140255.aspx) und [Dienst-SAS-Beispiele](https://msdn.microsoft.com/library/dn140256.aspx).
* **Konto-SAS:** Die Konto-SAS delegiert den Zugriff auf Ressourcen in einem oder mehreren Speicherdiensten. Alle Vorgänge, die über eine Dienst-SAS verfügbar sind, sind auch über eine Konto-SAS verfügbar. Mit der Konto-SAS können Sie auch den Zugriff auf Vorgänge delegieren, die für einen bestimmten Dienst gelten, z.B. **Get/Set Service Properties** und **Get Service Stats**. Sie können auch den Zugriff auf Lese-, Schreib- und Löschvorgänge in Blob-Containern, Tabellen, Warteschlangen und Dateifreigaben delegieren, die mit einer Dienst-SAS nicht zulässig sind. Ausführliche Informationen zur Erstellung des Konto-SAS-Tokens finden Sie unter [Erstellen einer Konto-SAS](https://msdn.microsoft.com/library/mt584140.aspx).

## <a name="how-a-shared-access-signature-works"></a>Funktionsweise von Shared Access Signatures
Eine Shared Access Signature ist ein signierter URI, der auf eine oder mehrere Speicherressourcen verweist und über ein Token verfügt, das einen speziellen Satz von Abfrageparametern enthält. Das Token gibt an, wie der Client auf die Ressourcen zugreifen kann. Einer der Abfrageparameter ist die Signatur. Sie besteht aus den SAS-Parametern und wird mit dem Kontoschlüssel signiert. Diese Signatur wird von Azure Storage verwendet, um den Zugriff auf die Speicherressource zu autorisieren.

Hier ist ein Beispiel für einen SAS-URI mit dem Ressourcen-URI und dem SAS-Token angegeben:

![Komponenten eines SAS-URIs](./media/storage-dotnet-shared-access-signature-part-1/sas-storage-uri.png)   

Das SAS-Token ist eine Zeichenfolge, die Sie auf der *Client*-Seite generieren (im Abschnitt [SAS-Beispiele](#sas-examples) finden Sie Codebeispiele). Ein SAS-Token, das Sie beispielsweise mit der Speicherclientbibliothek generieren, wird von Azure Storage nicht nachverfolgt. Sie können auf der Clientseite eine unbegrenzte Anzahl von SAS-Token erstellen.

Wenn ein Client im Rahmen einer Anforderung einen SAS-URI für Azure Storage bereitstellt, überprüft der Dienst die SAS-Parameter und die Signatur, um sicherzustellen, dass diese Angaben für die Authentifizierung der Anforderung gültig sind. Wenn der Dienst bestätigt, dass die Signatur gültig ist, wird die Anforderung autorisiert. Andernfalls wird die Anforderung mit Fehlercode 403 (Unzulässig) abgelehnt.

## <a name="shared-access-signature-parameters"></a>Shared Access Signature-Parameter
Die Konto-SAS- und Dienst-SAS-Token enthalten einige gemeinsame Parameter sowie einige Parameter, die unterschiedlich sind.

### <a name="parameters-common-to-account-sas-and-service-sas-tokens"></a>Gemeinsame Parameter von Konto-SAS- und Dienst-SAS-Token
* **API-Version:** Ein optionaler Parameter zum Angeben der Speicherdienstversion, die zum Ausführen der Anforderung verwendet wird.
* **Dienstversion:** Ein erforderlicher Parameter zum Angeben der Speicherdienstversion, die zum Autorisieren der Anforderung verwendet werden soll.
* **Startzeit:** Dies ist der Zeitpunkt, ab dem die SAS gültig ist. Die Startzeit für eine Shared Access Signature ist optional. Wird keine Startzeit angegeben, wird die SAS sofort wirksam. Die Startzeit muss in UTC (Coordinated Universal Time) mit einem speziellen UTC-Kennzeichner („Z“) angegeben werden. Beispiel: `1994-11-05T13:15:30Z`.
* **Ablaufzeit:** Dies ist der Zeitpunkt, ab dem die SAS nicht mehr gültig ist. Sie sollten nach Möglichkeit entweder eine Ablaufzeit für die SAS angeben oder diese mit einer gespeicherten Zugriffsrichtlinie verknüpfen. Die Ablaufzeit muss in UTC (Coordinated Universal Time) mit einem speziellen UTC-Kennzeichner („Z“) angegeben werden. Beispiel: `1994-11-05T13:15:30Z` (weitere Informationen finden Sie weiter unten).
* **Berechtigungen.**  Die Berechtigungen der SAS geben an, welche Operationen der Client mit der SAS auf der Speicherressource ausführen darf. Die verfügbaren Berechtigungen unterscheiden sich für eine Konto-SAS und eine Dienst-SAS.
* **IP:** Ein optionaler Parameter, der eine IP-Adresse oder einen Bereich von IP-Adressen außerhalb von Azure angibt (siehe Abschnitt [Konfigurationszustand der Routingsitzung](../../expressroute/expressroute-workflows.md#routing-session-configuration-state) für Express Route), aus dem Anforderungen angenommen werden.
* **Protokoll:**  Ein optionaler Parameter zum Angeben des Protokolls, das für eine Anforderung zulässig ist. Mögliche Werte sind HTTPS und HTTP (`https,http`), was der Standardwert ist, oder nur HTTPS (`https`). Beachten Sie, dass HTTP allein kein zulässiger Wert ist.
* **Signatur:**  Die Signatur wird aus den anderen Parametern erstellt, die als Teiltoken angegeben sind, und dann verschlüsselt. Die Signatur wird verwendet, um den Zugriff auf die angegebenen Speicherressourcen zu autorisieren.

### <a name="parameters-for-a-service-sas-token"></a>Parameter für ein Dienst-SAS-Token
* **Speicherressource:**  Speicherressourcen, für die Sie den Zugriff mit einer Dienst-SAS delegieren können, umfassen:
  * Container und Blobs
  * Dateifreigaben und Dateien
  * Warteschlangen
  * Tabellen und Bereiche von Tabellenentitäten.

### <a name="parameters-for-an-account-sas-token"></a>Parameter für ein Konto-SAS-Token
* **Dienst oder Dienste:**  Eine Konto-SAS kann den Zugriff auf einen oder mehrere Speicherdienste delegieren. Sie können beispielsweise eine Konto-SAS erstellen, mit der der Zugriff auf den Blob- und Dateidienst delegiert wird. Oder Sie können eine SAS erstellen, mit der der Zugriff auf alle vier Dienste delegiert wird (Blob, Warteschlange, Tabelle und Datei).
* **Speicherressourcenarten:**  Eine Konto-SAS gilt für eine oder mehrere Klassen von Speicherressourcen und nicht für eine bestimmte Ressource. Sie können eine Konto-SAS erstellen, um den Zugriff für Folgendes zu delegieren:
  * APIs auf Dienstebene, die für die Speicherkontoressource aufgerufen werden. Beispiele hierfür sind **Get/Set Service Properties**, **Get Service Stats** und **List Containers/Queues/Tables/Shares**.
  * APIs auf Containerebene, die für die Containerobjekte für jeden Dienst aufgerufen werden: Blob-Container, Warteschlangen, Tabellen und Dateifreigaben. Beispiele hierfür sind **Create/Delete Container**, **Create/Delete Queue**, **Create/Delete Table**, **Create/Delete Share** und **List Blobs/Files and Directories**.
  * APIs auf Objektebene, die für Blobs, Warteschlangennachrichten, Tabellenentitäten und Dateien aufgerufen werden. Beispiele hierfür sind **Put Blob**, **Query Entity**, **Get Messages** und **Create File**.

## <a name="examples-of-sas-uris"></a>Beispiele für SAS-URIs

### <a name="service-sas-uri-example"></a>Beispiel für Dienst-SAS-URI

Dies ist ein Beispiel für einen Dienst-SAS-URI, der Lese- und Schreibzugriff für einen Blob gewährt. Die Tabelle analysiert die einzelnen Teile des URI, um dessen Zusammenhang mit der SAS zu erläutern:

```
https://myaccount.blob.core.windows.net/sascontainer/sasblob.txt?sv=2015-04-05&st=2015-04-29T22%3A18%3A26Z&se=2015-04-30T02%3A23%3A26Z&sr=b&sp=rw&sip=168.1.5.60-168.1.5.70&spr=https&sig=Z%2FRHIX5Xcg0Mq2rqI3OlWTjEg2tYkboXr1P9ZUXDtkk%3D
```

| NAME | SAS-Teil | BESCHREIBUNG |
| --- | --- | --- |
| Blob-URI |`https://myaccount.blob.core.windows.net/sascontainer/sasblob.txt` |Die Blob-Adresse. Sie sollten unbedingt HTTPS verwenden. |
| Version des Speicherdienstes |`sv=2015-04-05` |Für Speicherdienste der Version 2012-02-12 und später gibt dieser Parameter die zu verwendende Version an. |
| Startzeit |`st=2015-04-29T22%3A18%3A26Z` |Angegeben im UTC-Zeitformat. Lassen Sie diesen Parameter aus, wenn die SAS sofort gültig sein soll. |
| Ablaufzeit |`se=2015-04-30T02%3A23%3A26Z` |Angegeben im UTC-Zeitformat. |
| Resource |`sr=b` |Die Ressource ist ein Blob. |
| Berechtigungen |`sp=rw` |Die SAS verleiht die Berechtigungen zum Lesen (r) und Schreiben (w). |
| IP-Bereich |`sip=168.1.5.60-168.1.5.70` |Der Bereich der IP-Adressen, von denen eine Anforderung akzeptiert wird. |
| Protocol |`spr=https` |Nur Anforderungen per HTTPS sind zulässig. |
| Signatur |`sig=Z%2FRHIX5Xcg0Mq2rqI3OlWTjEg2tYkboXr1P9ZUXDtkk%3D` |Wird verwendet, um den Zugriff auf das Blob zu autorisieren. Die Signatur ist ein HMAC, der mithilfe des SHA256-Algorithmus über StringToSign-Zeichenfolge und Schlüssel erstellt und anschließend mit Base64 codiert wird. |

### <a name="account-sas-uri-example"></a>Beispiel für Konto-SAS-URI

Hier ist ein Beispiel für eine Konto-SAS angegeben, bei der dieselben allgemeinen Parameter im Token verwendet werden. Da diese Parameter oben beschrieben sind, werden Sie hier nicht noch einmal beschrieben. Lediglich die Parameter, die speziell für Konto-SAS gelten, werden unten in der Tabelle erläutert.

```
https://myaccount.blob.core.windows.net/?restype=service&comp=properties&sv=2015-04-05&ss=bf&srt=s&st=2015-04-29T22%3A18%3A26Z&se=2015-04-30T02%3A23%3A26Z&sr=b&sp=rw&sip=168.1.5.60-168.1.5.70&spr=https&sig=F%6GRVAZ5Cdj2Pw4tgU7IlSTkWgn7bUkkAg8P6HESXwmf%4B
```

| NAME | SAS-Teil | BESCHREIBUNG |
| --- | --- | --- |
| Ressourcen-URI |`https://myaccount.blob.core.windows.net/?restype=service&comp=properties` |Der Blob-Dienstendpunkt mit Parametern zum Abrufen von Diensteigenschaften (bei Aufruf mit GET) oder Festlegen von Diensteigenschaften (bei Aufruf mit SET). |
| Dienste |`ss=bf` |Die SAS gilt für die Blob- und Dateidienste. |
| Ressourcentypen |`srt=s` |Die SAS gilt für Vorgänge auf Dienstebene. |
| Berechtigungen |`sp=rw` |Die Berechtigungen gewähren Zugriff auf Lese- und Schreibvorgänge. |

Da die Berechtigungen auf die Dienstebene beschränkt sind, sind mit dieser SAS die Vorgänge **Get Blob Service Properties** (Lesen) und **Set Blob Service Properties** (Schreiben) zugänglich. Mit einem anderen Ressourcen-URI kann das gleiche SAS-Token aber auch verwendet werden, um den Zugriff auf **Get Blob Service Stats** (Lesen) zu delegieren.

## <a name="controlling-a-sas-with-a-stored-access-policy"></a>Steuern einer SAS mit einer gespeicherten Zugriffsrichtlinie
Shared Access Signatures können zwei unterschiedliche Formen haben:

* **Ad-hoc-SAS:** Beim Erstellen von Ad-Hoc-SAS werden Startzeit, Ablaufzeit und Berechtigungen für die SAS direkt im SAS-URI angegeben (bzw. impliziert, falls die Startzeit ausgelassen wird). Diese Art von SAS kann als Konto-SAS oder Dienst-SAS erstellt werden.
* **SAS mit gespeicherter Zugriffsrichtlinie:** Gespeicherte Zugriffsrichtlinien werden für Ressourcencontainer (Blobcontainer, Tabellen, Warteschlangen oder Dateifreigaben) definiert und dienen zur Verwaltung von Einschränkungen für eine oder mehrere Shared Access Signatures. Wenn Sie eine SAS mit einer gespeicherten Zugriffsrichtlinie verknüpfen, erbt die SAS die Einschränkungen (Startzeit, Ablaufzeit und Berechtigungen), die für diese gespeicherte Zugriffsrichtlinie definiert sind.

> [!NOTE]
> Derzeit muss eine Konto-SAS eine Ad-hoc-SAS sein. Gespeicherte Zugriffsrichtlinien werden für Konto-SAS noch nicht unterstützt.

Der Unterschied zwischen diesen beiden Formen ist wichtig für ein Schlüsselszenario: Widerruf. Da ein SAS-URI eine URL ist, kann er von jeder Person verwendet werden, die die SAS erhalten hat, unabhängig davon, wer ihn ursprünglich erstellt hat. Wenn eine SAS veröffentlicht wird, kann diese von beliebiger Stelle weltweit verwendet werden. Eine SAS gewährt jeder Person, die im Besitz der SAS ist, Zugriff auf Ressourcen, bis eine von vier Bedingungen zutrifft:

1. Die Ablaufzeit der SAS wird erreicht.
2. Die Ablaufzeit der von der SAS referenzierten gespeicherten Zugriffsrichtlinie wird erreicht (falls eine gespeicherte Zugriffsrichtlinie referenziert wurde und diese eine Ablaufzeit definiert). Dies geschieht entweder, weil das Zeitintervall abläuft, oder weil Sie die Ablaufzeit in der gespeicherten Zugriffsrichtlinie auf einen Zeitpunkt in der Vergangenheit festgelegt haben, um die SAS zu widerrufen.
3. Die von der SAS referenzierte gespeicherte Zugriffsrichtlinie wird gelöscht, wodurch die SAS ebenfalls widerrufen wird. Wenn Sie die gespeicherte Zugriffsrichtlinie mit demselben Namen erneut erstellen, sind alle existierenden SAS-Tokens gemäß der Berechtigungen der gespeicherten Zugriffsrichtlinie wieder gültig (sofern die Ablaufzeit der SAS noch nicht erreicht wurde). Falls Sie die SAS widerrufen möchten, müssen Sie einen anderen Namen verwenden, wenn Sie die gespeicherte Zugriffsrichtlinie mit einer Ablaufzeit in der Zukunft erneut erstellen.
4. Der Kontoschlüssel, mit dem die SAS erstellt wurde, wird erneut generiert. Nach der erneuten Generierung eines Kontoschlüssels ist die Autorisierung aller Anwendungskomponenten, die diesen Schlüssel verwenden, erst wieder erfolgreich, wenn diese entweder mit dem anderen gültigen Kontoschlüssel oder mit dem neu generierten Kontoschlüssel aktualisiert werden.

> [!IMPORTANT]
> Ein SAS-URI wird dem Kontoschlüssel, mit dem die Signatur erstellt wurde, und der zugehörigen gespeicherten Zugriffsrichtlinie (sofern vorhanden) zugeordnet. Wenn keine gespeicherte Zugriffsrichtlinie angegeben wird, kann eine SAS nur durch Änderung des Kontoschlüssels aufgehoben werden.

## <a name="authenticating-from-a-client-application-with-a-sas"></a>Authentifizieren über eine Clientanwendung mit einer SAS
Ein Client mit einer SAS kann diese verwenden, um eine Anforderung für ein Speicherkonto zu autorisieren, für das dem Client die Kontoschlüssel nicht bekannt sind. Eine SAS kann in eine Verbindungszeichenfolge eingebunden oder direkt über den passenden Konstruktor oder die Methode verwendet werden.

### <a name="using-a-sas-in-a-connection-string"></a>Verwenden einer SAS in einer Verbindungszeichenfolge
[!INCLUDE [storage-use-sas-in-connection-string-include](../../../includes/storage-use-sas-in-connection-string-include.md)]

### <a name="using-a-sas-in-a-constructor-or-method"></a>Verwenden einer SAS in einem Konstruktor oder einer Methode
Für viele Azure Storage-Clientbibliothekkonstruktoren und -Methodenüberladungen steht ein SAS-Parameter zur Verfügung, sodass Sie eine Anforderung an den Dienst mit einer SAS autorisieren können.

Hier wird beispielsweise ein SAS-URI verwendet, um einen Verweis auf einen Blockblob zu erstellen. Mit der SAS werden die einzigen Anmeldeinformationen bereitgestellt, die für die Anforderung benötigt werden. Der Blockblobverweis wird dann für einen Schreibvorgang verwendet:

```csharp
string sasUri = "https://storagesample.blob.core.windows.net/sample-container/" +
    "sampleBlob.txt?sv=2015-07-08&sr=b&sig=39Up9JzHkxhUIhFEjEH9594DJxe7w6cIRCg0V6lCGSo%3D" +
    "&se=2016-10-18T21%3A51%3A37Z&sp=rcw";

CloudBlockBlob blob = new CloudBlockBlob(new Uri(sasUri));

// Create operation: Upload a blob with the specified name to the container.
// If the blob does not exist, it will be created. If it does exist, it will be overwritten.
try
{
    MemoryStream msWrite = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
    msWrite.Position = 0;
    using (msWrite)
    {
        await blob.UploadFromStreamAsync(msWrite);
    }

    Console.WriteLine("Create operation succeeded for SAS {0}", sasUri);
    Console.WriteLine();
}
catch (StorageException e)
{
    if (e.RequestInformation.HttpStatusCode == 403)
    {
        Console.WriteLine("Create operation failed for SAS {0}", sasUri);
        Console.WriteLine("Additional error information: " + e.Message);
        Console.WriteLine();
    }
    else
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}

```

## <a name="best-practices-when-using-sas"></a>Bewährte Methoden bei Verwendung von SAS
Wenn Sie Shared Access Signatures in Ihren Anwendungen verwenden, müssen Sie sich der potenziellen Risiken bewusst sein:

* Weitergegebene SAS können von beliebiger Stelle verwendet werden, was die Sicherheit Ihres Speicherkontos gefährden kann.
* Wenn die SAS einer Clientanwendung abläuft und die Anwendung keine neue SAS von Ihrem Dienst abrufen kann, wird die Funktionsweise der Anwendung möglicherweise eingeschränkt.

Mit den folgenden Empfehlungen für die Verwendung von Shared Access Signatures können Sie diese Risiken verringern:

1. **Verwenden Sie immer HTTPS** für die Erstellung oder Verteilung einer SAS. Wenn eine SAS über HTTP weitergegeben und abgefangen wird, kann ein Angreifer mit einem Man-in-the-Middle-Angriff diese auslesen, anschließend im Namen des Benutzers verwenden und somit möglicherweise sensible Daten gefährden oder böswillig beschädigen.
2. **Verweisen Sie nach Möglichkeit auf gespeicherte Zugriffsrichtlinien.**  Gespeicherte Zugriffsrichtlinien bieten Ihnen die Möglichkeit, Berechtigungen zu widerrufen, ohne dafür die Speicherkontoschlüssel erneut generieren zu müssen. Legen Sie das Ablaufdatum für diese sehr weit in die Zukunft (oder unendlich) fest, und stellen Sie sicher, dass das Datum regelmäßig aktualisiert wird, um es weiter in die Zukunft zu verschieben.
3. **Verwenden Sie Ablaufdaten in naher Zukunft für Ad-Hoc-SAS.** Auf diese Weise ist eine SAS, selbst wenn sie gefährdet ist, nur für kurze Zeit gültig. Dies ist besonders dann wichtig, wenn Sie nicht auf eine gespeicherte Zugriffsrichtlinie verweisen können. Kurzfristige Ablaufzeiten beschränken auch die Datenmenge, die in einen Blob geschrieben werden kann, indem sie die Zeit verkürzen, die ein Blob für Uploads verfügbar ist.
4. **Sorgen Sie dafür, dass die Clients die SAS bei Bedarf automatisch erneuern müssen.** Die Clients sollten ihre SAS rechtzeitig vor der Ablaufzeit erneuern, um Zeit für Wiederholungsversuche zu bieten, falls der entsprechende Dienst nicht verfügbar sein sollte. Falls Ihre SAS für eine kleine Anzahl sofortiger und kurzfristiger Operationen gilt, die normalerweise innerhalb des Ablaufzeitraums abgeschlossen werden, ist dies möglicherweise nicht notwendig, da die SAS nicht erneuert werden müssen. Wenn Ihre Clients jedoch immer wieder Anfragen über die SAS stellen, müssen Sie sich mit dem Ablaufmechanismus auseinander setzen. Dabei müssen Sie einen Ausgleich zwischen der Notwendigkeit einer kurzlebigen SAS (wie zuvor beschrieben) und dem Bedarf des Clients schaffen, diese rechtzeitig zu erneuern, um zu verhindern, dass die SAS vor der erfolgreichen Erneuerung abläuft.
5. **Seien Sie vorsichtig mit der SAS-Startzeit.** Wenn Sie die Startzeit einer SAS auf **jetzt** setzen, können aufgrund von Zeitunterschieden zwischen unterschiedlichen Computern in den ersten Minuten Probleme auftreten. Üblicherweise sollten Sie als Startzeit eine Uhrzeit angeben, die mindestens 15 Minuten in der Vergangenheit liegt. Alternativ legen Sie gar keine Startzeit fest, wodurch sie in allen Fällen sofort wirksam wird. Dasselbe gilt für die Ablaufzeit. Rechnen Sie immer mit Zeitunterschieden von bis zu 15 Minuten in beide Richtungen bei jeder Anforderung. Für Clients mit einer REST-Version vor 2012-02-12 ist die maximale Dauer für eine SAS, in der nicht auf eine gespeicherte Zugriffsrichtlinie verwiesen wird, eine Stunde. Alle Richtlinien, die längere Zeiträume verwenden, schlagen fehl.
6. **Geben Sie die freigegebene Ressource exakt an.** Aus Sicherheitsgründen sollten Benutzer nur die minimal erforderlichen Berechtigungen erhalten. Wenn ein Benutzer nur Lesezugriff auf eine einzige Entität benötigt, dann geben Sie auch nur Lesezugriff auf diese Entität, und nicht Lese-/Schreib-/Löschzugriff auf alle Entitäten. So lässt sich auch der Schaden verringern, wenn eine SAS kompromittiert wurde, denn die SAS bietet dem Angreifer weniger Angriffsfläche.
7. **Beachten Sie, dass Ihnen jegliche Nutzung Ihres Kontos berechnet wird, inklusive der Nutzung über SAS.**  Wenn Sie Schreibzugriff für einen Blob vergeben, können Benutzer Blobs mit bis zu 200GB hochladen. Falls Sie außerdem noch Lesezugriff vergeben, können die Benutzer die Daten bis zu zehnmal herunterladen und Gebühren für den Datenausgang von bis zu 2 TB verursachen. Vergeben Sie also auch hierbei eingeschränkte Berechtigungen, um die möglichen Aktionen böswilliger Benutzer abzuschwächen. Verwenden Sie kurzlebige SAS, um diese Bedrohung zu mindern (beachten Sie jedoch mögliche Zeitunterschiede bei der Ablaufzeit).
8. **Überprüfen Sie Daten, die per SAS geschrieben wurden.**  Wenn Clientanwendungen Daten in Ihr Speicherkonto schreiben, müssen Sie stets beachten, dass diese Daten problembehaftet sein können. Wenn Ihre Anwendung diese Daten vor der Verwendung überprüfen oder autorisieren muss, sollten Sie diese Überprüfung durchführen, nachdem die Daten geschrieben und bevor sie von Ihrer Anwendung verwendet werden. Auf diese Weise schützen Sie Ihr Konto auch vor beschädigten oder bösartigen Daten, sowohl von tatsächlich berechtigten SAS-Benutzern als auch von Angreifern, die eine abgefangene SAS verwenden.
9. **Verwenden Sie SAS nicht immer.** Manchmal überwiegen die Risiken einer bestimmten Operation für Ihr Speicherkonto gegenüber den Vorzügen von SAS. Für solche Operation sollten Sie einen Dienst auf der mittleren Ebene erstellen, der zunächst Geschäftsregeln validiert sowie Authentifizierung und Überwachung durchführt und die Daten anschließend in Ihr Speicherkonto schreibt. Manchmal gibt es auch einfachere Möglichkeiten der Zugriffsverwaltung. Wenn Sie beispielsweise alle Blobs in einem Container öffentlich lesbar machen möchten, können Sie auch den Container öffentlich machen, anstatt jedem Client für den Zugriff eine SAS zu geben.
10. **Überwachen Sie Ihrer Anwendung mithilfe der Speicheranalyse.**  Sie können Häufungen von Authentifizierungsfehlern aufgrund von Ausfällen Ihres SAS-Anbieterdiensts oder einer unbeabsichtigt gelöschten gespeicherten Zugriffsrichtlinie mithilfe von Protokollierung und Metriken beobachten. Weitere Informationen finden Sie im [Blog des Azure-Speicherteams](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/08/03/windows-azure-storage-logging-using-logs-to-track-storage-requests.aspx) .

## <a name="sas-examples"></a>SAS-Beispiele
Unten sind einige Beispiele für beide Arten von Shared Access Signatures aufgeführt: Konto-SAS und Dienst-SAS.

Um diese C#-Beispiele auszuführen, müssen Sie in Ihrem Projekt auf die folgenden NuGet-Pakete verweisen:

* [Azure Storage-Clientbibliothek für .NET](https://www.nuget.org/packages/WindowsAzure.Storage), Version 6.x oder höher (zur Verwendung von Konto-SAS).
* [Azure-Konfigurations-Manager](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager)

Weitere Beispiele, die das Erstellen und Testen einer SAS veranschaulichen, finden Sie unter [Azure-Codebeispiele](https://azure.microsoft.com/documentation/samples/?service=storage).

### <a name="example-create-and-use-an-account-sas"></a>Beispiel: Erstellen und Verwenden einer Konto-SAS
Im folgenden Codebeispiel wird eine Konto-SAS erstellt, die für die Blob- und Dateidienste gültig ist und dem Client die Berechtigungen Lesen, Schreiben und Auflisten für den Zugriff auf APIs auf Dienstebene gewährt. Bei der Konto-SAS ist das Protokoll auf HTTPS beschränkt, sodass die Anforderung mit HTTPS durchgeführt werden muss.

```csharp
static string GetAccountSASToken()
{
    // To create the account SAS, you need to use your shared key credentials. Modify for your account.
    const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);

    // Create a new access policy for the account.
    SharedAccessAccountPolicy policy = new SharedAccessAccountPolicy()
        {
            Permissions = SharedAccessAccountPermissions.Read | SharedAccessAccountPermissions.Write | SharedAccessAccountPermissions.List,
            Services = SharedAccessAccountServices.Blob | SharedAccessAccountServices.File,
            ResourceTypes = SharedAccessAccountResourceTypes.Service,
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Protocols = SharedAccessProtocol.HttpsOnly
        };

    // Return the SAS token.
    return storageAccount.GetSharedAccessSignature(policy);
}
```

Um die Konto-SAS zum Zugreifen auf APIs auf Dienstebene für den Blob-Dienst zu verwenden, erstellen Sie ein Blob-Clientobjekt, indem Sie die SAS und den Blob-Speicherendpunkt für Ihr Speicherkonto verwenden.

```csharp
static void UseAccountSAS(string sasToken)
{
    // Create new storage credentials using the SAS token.
    StorageCredentials accountSAS = new StorageCredentials(sasToken);
    // Use these credentials and the account name to create a Blob service client.
    CloudStorageAccount accountWithSAS = new CloudStorageAccount(accountSAS, "account-name", endpointSuffix: null, useHttps: true);
    CloudBlobClient blobClientWithSAS = accountWithSAS.CreateCloudBlobClient();

    // Now set the service properties for the Blob client created with the SAS.
    blobClientWithSAS.SetServiceProperties(new ServiceProperties()
    {
        HourMetrics = new MetricsProperties()
        {
            MetricsLevel = MetricsLevel.ServiceAndApi,
            RetentionDays = 7,
            Version = "1.0"
        },
        MinuteMetrics = new MetricsProperties()
        {
            MetricsLevel = MetricsLevel.ServiceAndApi,
            RetentionDays = 7,
            Version = "1.0"
        },
        Logging = new LoggingProperties()
        {
            LoggingOperations = LoggingOperations.All,
            RetentionDays = 14,
            Version = "1.0"
        }
    });

    // The permissions granted by the account SAS also permit you to retrieve service properties.
    ServiceProperties serviceProperties = blobClientWithSAS.GetServiceProperties();
    Console.WriteLine(serviceProperties.HourMetrics.MetricsLevel);
    Console.WriteLine(serviceProperties.HourMetrics.RetentionDays);
    Console.WriteLine(serviceProperties.HourMetrics.Version);
}
```

### <a name="example-create-a-stored-access-policy"></a>Beispiel: Erstellen einer gespeicherten Zugriffsrichtlinie
Mit dem folgenden Code wird eine gespeicherte Zugriffsrichtlinie für einen Container erstellt. Sie können mit der Zugriffsrichtlinie Einschränkungen für eine Dienst-SAS für den Container oder seine Blobs festlegen.

```csharp
private static async Task CreateSharedAccessPolicyAsync(CloudBlobContainer container, string policyName)
{
    // Create a new shared access policy and define its constraints.
    // The access policy provides create, write, read, list, and delete permissions.
    SharedAccessBlobPolicy sharedPolicy = new SharedAccessBlobPolicy()
    {
        // When the start time for the SAS is omitted, the start time is assumed to be the time when the storage service receives the request.
        // Omitting the start time for a SAS that is effective immediately helps to avoid clock skew.
        SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
        Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.List |
            SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Create | SharedAccessBlobPermissions.Delete
    };

    // Get the container's existing permissions.
    BlobContainerPermissions permissions = await container.GetPermissionsAsync();

    // Add the new policy to the container's permissions, and set the container's permissions.
    permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
    await container.SetPermissionsAsync(permissions);
}
```

### <a name="example-create-a-service-sas-on-a-container"></a>Beispiel: Erstellen einer Dienst-SAS für einen Container
Mit dem folgenden Code wird eine SAS für einen Container erstellt. Wenn der Name einer vorhandenen gespeicherten Zugriffsrichtlinie angegeben wird, wird diese Richtlinie der SAS zugewiesen. Wenn keine gespeicherte Zugriffsrichtlinie angegeben wird, erstellt der Code eine Ad-hoc-SAS für den Container.

```csharp
private static string GetContainerSasUri(CloudBlobContainer container, string storedPolicyName = null)
{
    string sasContainerToken;

    // If no stored policy is specified, create a new access policy and define its constraints.
    if (storedPolicyName == null)
    {
        // Note that the SharedAccessBlobPolicy class is used both to define the parameters of an ad hoc SAS, and
        // to construct a shared access policy that is saved to the container's shared access policies.
        SharedAccessBlobPolicy adHocPolicy = new SharedAccessBlobPolicy()
        {
            // When the start time for the SAS is omitted, the start time is assumed to be the time when the storage service receives the request.
            // Omitting the start time for a SAS that is effective immediately helps to avoid clock skew.
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List
        };

        // Generate the shared access signature on the container, setting the constraints directly on the signature.
        sasContainerToken = container.GetSharedAccessSignature(adHocPolicy, null);

        Console.WriteLine("SAS for blob container (ad hoc): {0}", sasContainerToken);
        Console.WriteLine();
    }
    else
    {
        // Generate the shared access signature on the container. In this case, all of the constraints for the
        // shared access signature are specified on the stored access policy, which is provided by name.
        // It is also possible to specify some constraints on an ad hoc SAS and others on the stored access policy.
        sasContainerToken = container.GetSharedAccessSignature(null, storedPolicyName);

        Console.WriteLine("SAS for blob container (stored access policy): {0}", sasContainerToken);
        Console.WriteLine();
    }

    // Return the URI string for the container, including the SAS token.
    return container.Uri + sasContainerToken;
}
```

### <a name="example-create-a-service-sas-on-a-blob"></a>Beispiel: Erstellen einer Dienst-SAS für ein Blob
Mit dem folgenden Code wird eine SAS für ein Blob erstellt. Wenn der Name einer vorhandenen gespeicherten Zugriffsrichtlinie angegeben wird, wird diese Richtlinie der SAS zugewiesen. Wenn keine gespeicherte Zugriffsrichtlinie angegeben wird, erstellt der Code eine Ad-hoc-SAS für das Blob.

```csharp
private static string GetBlobSasUri(CloudBlobContainer container, string blobName, string policyName = null)
{
    string sasBlobToken;

    // Get a reference to a blob within the container.
    // Note that the blob may not exist yet, but a SAS can still be created for it.
    CloudBlockBlob blob = container.GetBlockBlobReference(blobName);

    if (policyName == null)
    {
        // Create a new access policy and define its constraints.
        // Note that the SharedAccessBlobPolicy class is used both to define the parameters of an ad hoc SAS, and
        // to construct a shared access policy that is saved to the container's shared access policies.
        SharedAccessBlobPolicy adHocSAS = new SharedAccessBlobPolicy()
        {
            // When the start time for the SAS is omitted, the start time is assumed to be the time when the storage service receives the request.
            // Omitting the start time for a SAS that is effective immediately helps to avoid clock skew.
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Create
        };

        // Generate the shared access signature on the blob, setting the constraints directly on the signature.
        sasBlobToken = blob.GetSharedAccessSignature(adHocSAS);

        Console.WriteLine("SAS for blob (ad hoc): {0}", sasBlobToken);
        Console.WriteLine();
    }
    else
    {
        // Generate the shared access signature on the blob. In this case, all of the constraints for the
        // shared access signature are specified on the container's stored access policy.
        sasBlobToken = blob.GetSharedAccessSignature(null, policyName);

        Console.WriteLine("SAS for blob (stored access policy): {0}", sasBlobToken);
        Console.WriteLine();
    }

    // Return the URI string for the container, including the SAS token.
    return blob.Uri + sasBlobToken;
}
```

## <a name="conclusion"></a>Zusammenfassung
Shared Access Signatures sind nützlich für die Vergabe eingeschränkter Berechtigungen für Ihr Speicherkonto an Clients, die keinen Zugriff auf Ihren Kontoschlüssel haben dürfen. Somit sind sie ein wichtiger Teil des Sicherheitsmodells für alle Anwendungen, die den Azure-Speicher verwenden. Wenn Sie den hier genannten Empfehlungen folgen, können Sie mit SAS eine größere Flexibilität für den Zugriff auf Ressourcen in Ihrem Speicherkonto erreichen, ohne die Sicherheit Ihrer Anwendung zu gefährden.

## <a name="next-steps"></a>Nächste Schritte
* [Verwalten des anonymen Lesezugriffs auf Container und Blobs](../blobs/storage-manage-access-to-resources.md)
* [Delegieren des Zugriffs mit einer Shared Access Signature](https://msdn.microsoft.com/library/azure/ee395415.aspx)
* [Introducing Table and Queue SAS (Einführung in Tabellen- und Warteschlangen-SAS; in englischer Sprache)](https://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx)
