---
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/28/2020
ms.author: normesta
ms.openlocfilehash: 9b18687c0a6f3e48d94431e88be8ae8137c9dcdb
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96011159"
---
| Eigenschaft | BESCHREIBUNG |
|:--- |:---|
|**accountName** | Der Name des Speicherkontos. Beispiel: `mystorageaccount`.  |
|**requestUrl** | Hierbei handelt es sich um die angeforderte URL. |
|**userAgentHeader** | Hierbei handelt es sich um den Wert des **Benutzer-Agent-Headers** in Anführungszeichen. Beispiel: `WA-Storage/6.2.0 (.NET CLR 4.0.30319.42000; Win32NT 6.2.9200.0)`.|
|**referrerHeader** | Hierbei handelt es sich um den Wert des **Verweiserheaders**. Beispiel: `http://contoso.com/about.html`.|
|**clientRequestId** | Hierbei handelt es sich um den Wert des **x-ms-client-request-id**-Headers der Anforderung. Beispiel: `360b66a6-ad4f-4c4a-84a4-0ad7cb44f7a6`. |
|**etag** | Hierbei handelt es sich um den ETag-Bezeichner für das zurückgegebene Objekt in Anführungszeichen. Beispiel: `0x8D101F7E4B662C4`.  |
|**serverLatencyMs** | Hierbei handelt es sich um die zum Ausführen des angeforderten Vorgangs benötigte Gesamtzeit in Millisekunden. Dieser Wert umfasst nicht die Netzwerklatenzzeit (die Zeit, die für das Lesen der eingehenden Anforderung und das Senden der Antwort an den Anforderer benötigt wurde). Beispiel: `22`. |
|**serviceType** | Hierbei handelt es sich um den der Anforderung zugeordneten Dienst, z. B. `blob`, `table`, `files` oder `queue`. |
|**operationCount** | Hierbei handelt es sich um die Anzahl von an der Anforderung beteiligten protokollierten Vorgängen. Die Zählung beginnt mit einem Index von `0`. Für einige Anforderungen sind mehrere Vorgänge erforderlich. Die meisten Anforderungen führen nur einen Vorgang aus. Beispiel: `1`. |
|**requestHeaderSize** | Hierbei handelt es sich um die Größe des Anforderungsheaders in Byte. Beispiel: `578`. <br>Wenn bei einer Anforderung ein Fehler aufgetreten ist, ist dieser Wert möglicherweise leer. |
|**requestBodySize** | Hierbei handelt es sich um die Größe der vom Speicherdienst gelesenen Anforderungspakete in Byte. <br> Beispiel: `0`. <br>Wenn bei einer Anforderung ein Fehler aufgetreten ist, ist dieser Wert möglicherweise leer.  |
|**responseHeaderSize** | Hierbei handelt es sich um die Größe des Antwortheaders in Byte. Beispiel: `216`. <br>Wenn bei einer Anforderung ein Fehler aufgetreten ist, ist dieser Wert möglicherweise leer.  |
|**responseBodySize** | Hierbei handelt es sich um die Größe der vom Speicherdienst geschriebenen Antwortpakete in Byte. Wenn bei einer Anforderung ein Fehler aufgetreten ist, kann dieser Wert leer sein. Beispiel: `216`.  |
|**requestMd5** | Hierbei handelt es sich entweder um den Wert des **Content-MD5**-Headers oder den des **x-ms-content-md5**-Headers in der Anforderung. Der MD5-Hashwert, der in diesem Feld angegeben ist, stellt den Inhalt der Anforderung dar. Beispiel: `788815fd0198be0d275ad329cafd1830`. <br>Dieses Feld kann leer sein.  |
|**serverMd5** | Hierbei handelt es sich um den Wert des vom Speicherdienst berechneten MD5-Hashs. Beispiel: `3228b3cf1069a5489b298446321f8521`. <br>Dieses Feld kann leer sein.  |
|**lastModifiedTime** | Hierbei handelt es sich um die Uhrzeit der letzten Änderung für das zurückgegebene Objekt.  Beispiel: `Tuesday, 09-Aug-11 21:13:26 GMT`. <br>Dieses Feld ist für Vorgänge leer, die mehrere Objekte zurückgeben können. |
|**conditionsUsed** | Hierbei handelt es sich um eine Liste von Schlüssel-Wert-Paaren mit Semikolons als Trennzeichen, die eine Bedingung darstellen. Dabei kann es sich um folgende Bedingungen handeln: <li> If-Modified-Since <li> If-Unmodified-Since <li> If-Match <li> If-None-Match  <br> Beispiel: `If-Modified-Since=Friday, 05-Aug-11 19:11:54 GMT`. |
|**contentLengthHeader** | Hierbei handelt es sich um den Wert des Content-Length-Headers für die an den Speicherdienst gesendete Anforderung. Wenn die Anforderung erfolgreich war, entspricht dieser Wert dem von requestBodySize. Wenn bei einer Anforderung ein Fehler aufgetreten ist, entspricht dieser Wert möglicherweise nicht dem von requestBodySize, oder er ist leer. |
|**tlsVersion** | Hierbei handelt es sich um die bei der Anforderung für die Verbindung verwendete TLS-Version. Beispiel: `TLS 1.2`. |
|**smbTreeConnectID** | Hierbei handelt es sich um die beim Herstellen einer Strukturverbindung erstellte SMB-**treeConnectId** (Server Message Block). Beispiel: `0x3` |
|**smbPersistentHandleID** | Hierbei handelt es sich um eine persistente Handle-ID aus einer SMB2-CREATE-Anforderung, die das erneute Herstellen einer Verbindung mit dem Netzwerk übersteht.  Auf diese ID wird in [MS-SMB2](/openspecs/windows_protocols/ms-smb2/f1d9b40d-e335-45fc-9d0b-199a31ede4c3) 2.2.14.1 mit **SMB2_FILEID.Persistent** verwiesen. Beispiel: `0x6003f` |
|**smbVolatileHandleID** | Hierbei handelt es sich um eine flüchtige Handle-ID aus einer SMB2-CREATE-Anforderung, die beim erneuten Herstellen einer Verbindung mit dem Netzwerk wiederverwendet wird.  Auf diese ID wird in [MS-SMB2](/openspecs/windows_protocols/ms-smb2/f1d9b40d-e335-45fc-9d0b-199a31ede4c3) 2.2.14.1 mit **SMB2_FILEID.Volatile** verwiesen. Beispiel: `0xFFFFFFFF00000065` |
|**smbMessageID** | Hierbei handelt es sich um die **MessageId** für die Verbindung. Beispiel: `0x3b165` |
|**smbCreditsConsumed** | Hierbei handelt es sich um den von der Anforderung genutzten eingehenden oder ausgehenden Datenverkehr in Einheiten von je 64 KB. Beispiel: `0x3` |
|**smbCommandDetail** | Hierbei handelt es sich um weitere Informationen zur konkreten Anforderung statt zum allgemeinen Typ der Anforderung. Beispiel: `0x2000 bytes at offset 0xf2000` |
|**smbFileId** | Hierbei handelt es sich um die der Datei oder dem Verzeichnis zugeordnete **FileId**.  Diese entspricht ungefähr einer NTFS-FileId. Beispiel: `0x9223442405598953` |
|**smbSessionID** | Hierbei handelt es sich um die beim Einrichten der Sitzung erstellte SMB2-**SessionId**. Beispiel: `0x8530280128000049` |
|**smbCommandMajor  uint32** | Hierbei handelt es sich um einen Wert in **SMB2_HEADER.Command**. Derzeit ist dies eine Zahl im Bereich von 0 bis einschließlich 18. Beispiel: `0x6` |
|**smbCommandMinor** | Hierbei handelt es sich um eine etwaige Unterklasse von **SmbCommandMajor**. Beispiel: `DirectoryCloseAndDelete` |