---
title: Asynchrones Aktualisieren von Azure Analysis Services-Modellen | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie die asynchrone Aktualisierung mit der REST-API programmieren.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 05/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 63b64df457af5b7d3d2bd5901f73d89ccd3c913a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65506975"
---
# <a name="asynchronous-refresh-with-the-rest-api"></a>Asynchrones Aktualisieren mit der REST-API

Durch Verwendung einer Programmiersprache, die REST-Aufrufe unterstützt, können Sie asynchrone Datenaktualisierungsvorgänge in Ihren tabellarischen Azure Analysis Services-Modellen durchführen. Dies schließt die Synchronisierung von schreibgeschützten Replikaten für die horizontale Skalierung von Abfragen ein. 

Datenaktualisierungsvorgänge können abhängig von verschiedenen Faktoren, z.B. dem Datenvolumen oder der Optimierungsebene mit Partitionen, einige Zeit in Anspruch nehmen. Diese Vorgänge werden üblicherweise mit vorhandenen Methoden aufgerufen, z.B. unter Verwendung von [TOM](https://docs.microsoft.com/sql/analysis-services/tabular-model-programming-compatibility-level-1200/introduction-to-the-tabular-object-model-tom-in-analysis-services-amo) (Tabellenobjektmodell), [PowerShell](https://docs.microsoft.com/sql/analysis-services/powershell/analysis-services-powershell-reference)-Cmdlets oder [TMSL](https://docs.microsoft.com/sql/analysis-services/tabular-model-scripting-language-tmsl-reference) (Tabular Model Scripting Language). Diese Methoden können jedoch häufig unzuverlässige HTTP-Verbindungen mit langer Ausführungszeit erfordern.

Mithilfe der REST-API für Azure Analysis Services können Datenaktualisierungsvorgänge asynchron ausgeführt werden. Durch Verwendung der REST-API sind keine über Clientanwendungen hergestellten HTTP-Verbindungen mit langer Ausführungszeit erforderlich. Es gibt auch andere integrierte Features für die Zuverlässigkeit, z.B. automatische Wiederholungsversuche und in Batches verarbeitete Commits.

## <a name="base-url"></a>Basis-URL

Die Basis-URL weist das folgende Format auf:

```
https://<rollout>.asazure.windows.net/servers/<serverName>/models/<resource>/
```

Angenommen, Sie verwenden ein Modell mit dem Namen „AdventureWorks“ auf dem Server „myserver“ in der Azure-Region „USA, Westen“. Der Servername lautet:

```
asazure://westus.asazure.windows.net/myserver 
```

Die Basis-URL für diesen Servernamen lautet:

```
https://westus.asazure.windows.net/servers/myserver/models/AdventureWorks/ 
```

Durch Verwendung der Basis-URL können Ressourcen und Vorgänge mit den folgenden Parametern angefügt werden: 

![Asynchrone Aktualisierung](./media/analysis-services-async-refresh/aas-async-refresh-flow.png)

- Alle Elemente, die mit **s** enden, sind als Sammlung definiert.
- Alle Elemente, die mit **()** enden, sind als Funktion definiert.
- Bei allen anderen Elementen handelt es sich um Ressourcen oder Objekte.

Sie können beispielsweise das POST-Verb für die Refreshes-Sammlung verwenden, um einen Aktualisierungsvorgang durchzuführen:

```
https://westus.asazure.windows.net/servers/myserver/models/AdventureWorks/refreshes
```

## <a name="authentication"></a>Authentication

Alle Aufrufe müssen mit einem gültigen Azure Active Directory-Token (OAuth 2) im Autorisierungsheader authentifiziert werden und die folgenden Anforderungen erfüllen:

- Das Token muss ein Benutzertoken oder ein Anwendungsdienstprinzipal sein.
- Für das Token muss die richtige Zielgruppe auf `https://*.asazure.windows.net` festgelegt sein.
- Der Benutzer oder die Anwendung muss für den Server oder das Modell über ausreichende Berechtigungen zum Ausführen des angeforderten Aufrufs verfügen. Die Berechtigungsebene wird durch Rollen innerhalb des Modells oder der Administratorengruppe auf dem Server bestimmt.

    > [!IMPORTANT]
    > Derzeit sind Berechtigungen der Rolle **Serveradministrator** erforderlich.

## <a name="post-refreshes"></a>POST /refreshes

Verwenden Sie zum Ausführen eines Aktualisierungsvorgangs das POST-Verb für die /refreshes-Sammlung, um der Sammlung ein neues Aktualisierungselement hinzuzufügen. Der Location-Header in der Antwort enthält die Aktualisierungs-ID. Die Clientanwendung kann die Verbindung trennen und den Status gegebenenfalls später überprüfen, da die Aktualisierung asynchron ist.

Für ein Modell kann nur jeweils ein Aktualisierungsvorgang ausgeführt werden. Wenn bei einem aktuell ausgeführten Aktualisierungsvorgang ein weiterer Aktualisierungsvorgang übertragen wird, wird der HTTP-Statuscode „409 Conflict“ zurückgegeben.

Die Text kann wie folgt aussehen:

```
{
    "Type": "Full",
    "CommitMode": "transactional",
    "MaxParallelism": 2,
    "RetryCount": 2,
    "Objects": [
        {
            "table": "DimCustomer",
            "partition": "DimCustomer"
        },
        {
            "table": "DimDate"
        }
    ]
}
```

### <a name="parameters"></a>Parameter

Es müssen keine Parameter angegeben werden. Es wird jeweils der Standard angewendet.

| NAME             | Type  | BESCHREIBUNG  |Standard  |
|------------------|-------|--------------|---------|
| `Type`           | Enum  | Der auszuführende Verarbeitungstyp. Die Typen werden an die TMSL-Typen des [refresh-Befehls](https://docs.microsoft.com/sql/analysis-services/tabular-models-scripting-language-commands/refresh-command-tmsl) angepasst: full, clearValues, calculate, dataOnly, automatic und defragment. Der add-Typ wird nicht unterstützt.      |   automatic      |
| `CommitMode`     | Enum  | Legt fest, ob für Objekte ein Commit in Batches oder erst nach Abschluss ausgeführt wird. Folgende Modi sind verfügbar: default, transactional, partialBatch.  |  transactional       |
| `MaxParallelism` | Int   | Dieser Wert legt die maximale Anzahl der Threads fest, für die Verarbeitungsbefehle parallel ausgeführt werden. Dieser Wert wird an die MaxParallelism-Eigenschaft angepasst, die im [Sequence-Befehl](https://docs.microsoft.com/sql/analysis-services/tabular-models-scripting-language-commands/sequence-command-tmsl) (TMSL) oder mit anderen Methoden festgelegt werden kann.       | 10        |
| `RetryCount`     | Int   | Gibt an, wie oft der Vorgang wiederholt wird, bevor ein Fehler auftritt.      |     0    |
| `Objects`        | Array | Ein Array von zu verarbeitenden Objekten. Jedes Objekt umfasst Folgendes: „table“ bei Verarbeitung der gesamten Tabelle oder „table“ und „partition“ bei Verarbeitung einer Partition. Wenn keine Objekte angegeben sind, wird das gesamte Modell aktualisiert. |   Verarbeiten des gesamten Modells      |

CommitMode entspricht partialBatch und wird beim anfänglichen Laden umfangreicher DataSets verwendet, das Stunden dauern kann. Wenn beim Aktualisierungsvorgang nach dem erfolgreichen Committen für einen oder mehrere Batches Fehler auftreten, bleiben die erfolgreich committeten Batches committet (erfolgreich committete Batches werden nicht zurückgesetzt).

> [!NOTE]
> Zum Zeitpunkt der Erstellung dieser Dokumentation entspricht die Batchgröße dem MaxParallelism-Wert – dieser Wert kann sich jedoch ändern.

## <a name="get-refreshesrefreshid"></a>GET /refreshes/\<refreshId>

Verwenden Sie zum Überprüfen des Status eines Aktualisierungsvorgangs das GET-Verb für die Aktualisierungs-ID. Es folgt ein Beispiel für den Antworttext. Wenn sich der Vorgang in Bearbeitung befindet, wird als Status **inProgress** zurückgegeben.

```
{
    "startTime": "2017-12-07T02:06:57.1838734Z",
    "endTime": "2017-12-07T02:07:00.4929675Z",
    "type": "full",
    "status": "succeeded",
    "currentRefreshType": "full",
    "objects": [
        {
            "table": "DimCustomer",
            "partition": "DimCustomer",
            "status": "succeeded"
        },
        {
            "table": "DimDate",
            "partition": "DimDate",
            "status": "succeeded"
        }
    ]
}
```

## <a name="get-refreshes"></a>GET /refreshes

Verwenden Sie zum Abrufen einer Liste der Aktualisierungsverlaufsdaten für ein Modell das GET-Verb für die /refreshes-Sammlung. Es folgt ein Beispiel für den Antworttext. 

> [!NOTE]
> Zum Zeitpunkt der Erstellung dieser Dokumentation werden die Aktualisierungsvorgänge der letzten 30 Tage gespeichert und zurückgegeben – diese Anzahl kann sich jedoch ändern.

```
[
    {
        "refreshId": "1344a272-7893-4afa-a4b3-3fb87222fdac",
        "startTime": "2017-12-09T01:58:04.76",
        "endTime": "2017-12-09T01:58:12.607",
        "status": "succeeded"
    },
    {
        "refreshId": "474fc5a0-3d69-4c5d-adb4-8a846fa5580b",
        "startTime": "2017-12-07T02:05:48.32",
        "endTime": "2017-12-07T02:05:54.913",
        "status": "succeeded"
    }
]
```

## <a name="delete-refreshesrefreshid"></a>DELETE /refreshes/\<refreshId>

Verwenden Sie zum Abbrechen eines ausgeführten Aktualisierungsvorgangs das DELETE-Verb für die Aktualisierungs-ID.

## <a name="post-sync"></a>POST /sync

Nach dem Ausführen von Aktualisierungsvorgängen müssen die neuen Daten möglicherweise mit den Replikaten für die horizontale Skalierung von Abfragen synchronisiert werden. Verwenden Sie zum Ausführen eines Synchronisierungsvorgangs für ein Modell das POST-Verb für die /sync-Funktion. Der Location-Header in der Antwort enthält die ID des Synchronisierungsvorgangs.

## <a name="get-sync-status"></a>GET /sync status

Verwenden Sie zum Überprüfen des Status eines Synchronisierungsvorgangs das GET-Verb, mit dem die Vorgangs-ID als Parameter übergeben wird. Es folgt ein Beispiel für den Antworttext:

```
{
    "operationId": "cd5e16c6-6d4e-4347-86a0-762bdf5b4875",
    "database": "AdventureWorks2",
    "UpdatedAt": "2017-12-09T02:44:26.18",
    "StartedAt": "2017-12-09T02:44:20.743",
    "syncstate": 2,
    "details": null
}
```

Werte für `syncstate`:

- 0: Replikation. Datenbankdateien werden in einen Zielordner repliziert.
- 1: Aktivierung. Die Datenbank wird für schreibgeschützte Serverinstanzen aktiviert.
- 2: Abgeschlossen. Der Synchronisierungsvorgang wurde erfolgreich abgeschlossen.
- 3: Fehler. Beim Synchronisierungsvorgang sind Fehler aufgetreten.
- 4: Ausführung wird abgeschlossen. Der Synchronisierungsvorgang wurde abgeschlossen, es werden jedoch noch Bereinigungsschritte ausgeführt.

## <a name="code-sample"></a>Codebeispiel

C#-Codebeispiel für Ihren Einstieg: [RestApiSample auf GitHub](https://github.com/Microsoft/Analysis-Services/tree/master/RestApiSample).

### <a name="to-use-the-code-sample"></a>So verwenden Sie das Codebeispiel

1.  Klonen Sie das Repository, oder laden Sie es herunter. Öffnen Sie die RestApiSample-Lösung.
2.  Suchen Sie die Zeile **client.BaseAddress = …** , und geben Sie Ihre [Basis-URL](#base-url) an.

Das Codebeispiel verwendet die [Dienstprinzipal](#service-principal)-Authentifizierung.

### <a name="service-principal"></a>Dienstprinzipal

Weitere Informationen zum Einrichten eines Dienstprinzipals und Zuweisen der erforderlichen Berechtigungen in Azure finden Sie unter [Erstellen eines Dienstprinzipals – Azure-Portals](../active-directory/develop/howto-create-service-principal-portal.md) und [Hinzufügen eines Dienstprinzipals zur Serveradministratorrolle](analysis-services-addservprinc-admins.md). Führen Sie nach Abschluss dieser Schritte die folgenden zusätzlichen Schritte aus:

1.  Suchen Sie im Codebeispiel **string authority = …** , und ersetzen Sie **common** durch die Mandanten-ID Ihrer Organisation.
2.  Fügen Sie eine Auskommentierung ein, bzw. heben Sie die Auskommentierung auf, damit die ClientCredential-Klasse zum Instanziieren des cred-Objekts verwendet wird. Stellen Sie sicher, dass auf die Werte \<App ID> und \<App Key> auf sichere Weise zugegriffen wird, oder verwenden Sie die zertifikatbasierte Authentifizierung für Dienstprinzipale.
3.  Führen Sie das Beispiel aus.


## <a name="see-also"></a>Weitere Informationen

[Beispiele](analysis-services-samples.md)   
[REST-API](https://docs.microsoft.com/rest/api/analysisservices/servers)   


