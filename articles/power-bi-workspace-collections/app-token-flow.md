---
title: Authentifizieren und Autorisieren mit Power BI-Arbeitsbereichssammlungen | Microsoft-Dokumentation
description: Informationen zum Authentifizieren und Autorisieren mit Power BI-Arbeitsbereichssammlungen.
services: power-bi-workspace-collections
author: rkarlin
ms.author: rkarlin
ms.service: power-bi-workspace-collections
ms.topic: article
ms.workload: powerbi
ms.date: 09/20/2017
ms.openlocfilehash: 5fbe08bb9ebc355074a790f7732c9de10eaad859
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64707111"
---
# <a name="authenticating-and-authorizing-with-power-bi-workspace-collections"></a>Informationen zum Authentifizieren und Autorisieren mit Power BI-Arbeitsbereichssammlungen

Power BI-Arbeitsbereichssammlungen nutzen zur Authentifizierung und Autorisierung anstelle der expliziten Benutzerauthentifizierung **Schlüssel** und **App-Token**. Bei diesem Modell verwaltet Ihre Anwendung die Authentifizierung und Autorisierung Ihrer Endbenutzer. Bei Bedarf erstellt und sendet Ihre App die App-Token, die unseren Dienst anweisen, den angeforderten Bericht zu rendern. Bei diesem Konzept muss Ihre App nicht Azure Active Directory für die Benutzerauthentifizierung und -autorisierung verwenden, auch wenn dies natürlich möglich ist.

> [!IMPORTANT]
> Power BI-Arbeitsbereichssammlungen sind veraltet und nur noch bis Juni 2018 (oder bis zum Termin in Ihrem Vertrag) verfügbar. Es wird empfohlen, die Migration zu Power BI Embedded zu planen, um Unterbrechungen für Ihre Anwendung zu vermeiden. Informationen zur Migration Ihrer Daten zu Power BI Embedded finden Sie unter [Migrieren von Inhalten aus Power BI-Arbeitsbereichssammlungen zu Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

## <a name="two-ways-to-authenticate"></a>Zwei Authentifizierungsmöglichkeiten

**Schlüssel**: Schlüssel können für alle Aufrufe der REST-API von Power BI-Arbeitsbereichssammlungen verwendet werden. Sie finden die Schlüssel im **Microsoft Azure-Portal**, indem Sie auf **Alle Einstellungen** und dann auf **Zugriffsschlüssel** klicken. Behandeln Sie Ihren Schlüssel immer wie ein Kennwort. Die Schlüssel verfügen über Berechtigungen, die es ihnen ermöglichen, beliebige REST-API-Aufrufe für eine bestimmte Arbeitsbereichssammlung durchzuführen.

Wenn Sie einen Schlüssel für einen REST-Aufruf verwenden möchten, fügen Sie den folgenden Autorisierungsheader hinzu:

    Authorization: AppKey {your key}

**App-Token** : App-Token werden für alle Einbettungsanforderungen verwendet. Da seine clientseitige Ausführung vorgesehen ist, ist das Token auf einen einzelnen Bericht beschränkt, und es empfiehlt sich, eine Ablaufzeit festzulegen.

App-Token sind JWT (JSON Web Token), die mit einem Ihrer Schlüssel signiert sind.

Ihr App-Token kann folgende Ansprüche enthalten:

| Anspruch | BESCHREIBUNG |    
| --- | --- |
| **ver** |Die Version des App-Tokens. Die aktuelle Version ist 0.2.0. |
| **aud** |Der vorgesehene Empfänger des Tokens. Verwenden Sie für Power BI-Arbeitsbereichssammlungen: *https:\//analysis.windows.net/powerbi/api*. |
| **iss** |Eine Zeichenfolge, die die Anwendung angibt, die das Token ausgestellt hat. |
| **type** |Die Art des App-Tokens, das erstellt wird. Aktuell wird nur der Typ **embed**unterstützt. |
| **wcn** |Der Name der Arbeitsbereichssammlung, für die das Token ausgegeben wird. |
| **wid** |Die ID der Arbeitsbereichssammlung, für die das Token ausgegeben wird. |
| **rid** |Die Berichts-ID der Arbeitsbereichssammlung, für die das Token ausgegeben wird. |
| **username** (optional) |In Kombination mit RLS ermöglicht „username“ die Identifizierung des Benutzers, wenn RLS-Regeln angewendet werden. |
| **roles** (optional) |Mit den in dieser Zeichenfolge enthaltenen Rollen wird ausgewählt, wann die Regeln der Sicherheit auf Zeilenebene (Row Level Security, RLS) angewendet werden sollen. Mehrere Rollen sollten als Zeichenfolgenarray übergeben werden. |
| **scp** (optional) |Eine Zeichenfolge, die die Berechtigungsbereiche enthält. Mehrere Rollen sollten als Zeichenfolgenarray übergeben werden. |
| **exp** (optional) |Gibt die Ablaufzeit des Tokens an. Der Wert muss als Unix-Zeitstempel übergeben werden. |
| **nbf** (optional) |Gibt an, ab wann das Token gültig wird. Der Wert muss als Unix-Zeitstempel übergeben werden. |

Ein Beispiel-App-Token sieht wie folgt aus:

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ2ZXIiOiIwLjIuMCIsInR5cGUiOiJlbWJlZCIsIndjbiI6Ikd1eUluQUN1YmUiLCJ3aWQiOiJkNGZlMWViMS0yNzEwLTRhNDctODQ3Yy0xNzZhOTU0NWRhZDgiLCJyaWQiOiIyNWMwZDQwYi1kZTY1LTQxZDItOTMyYy0wZjE2ODc2ZTNiOWQiLCJzY3AiOiJSZXBvcnQuUmVhZCIsImlzcyI6IlBvd2VyQklTREsiLCJhdWQiOiJodHRwczovL2FuYWx5c2lzLndpbmRvd3MubmV0L3Bvd2VyYmkvYXBpIiwiZXhwIjoxNDg4NTAyNDM2LCJuYmYiOjE0ODg0OTg4MzZ9.v1znUaXMrD1AdMz6YjywhJQGY7MWjdCR3SmUSwWwIiI
```

Nach der Decodierung sieht es in etwa so aus:

```
Header

{
    typ: "JWT",
    alg: "HS256:
}

Body

{
  "ver": "0.2.0",
  "wcn": "SupportDemo",
  "wid": "ca675b19-6c3c-4003-8808-1c7ddc6bd809",
  "rid": "96241f0f-abae-4ea9-a065-93b428eddb17",
  "iss": "PowerBISDK",
  "aud": "https://analysis.windows.net/powerbi/api",
  "exp": 1360047056,
  "nbf": 1360043456
}

```

Innerhalb der SDKs stehen Methoden zur Verfügung, die die Erstellung von App-Token vereinfachen. Sehen Sie sich beispielsweise für .NET die Klasse [Microsoft.PowerBI.Security.PowerBIToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken) und die Methoden [CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN) an.

Informationen zum .NET SDK finden Sie unter [Scopes](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.scopes) (Bereiche).

## <a name="scopes"></a>Bereiche

Bei der Verwendung von Einbettungstoken möchten Sie vielleicht die Nutzung der Ressourcen beschränken, auf die Sie Zugriff gewähren. Aus diesem Grund können Sie ein Token mit bereichsbezogenen Berechtigungen generieren.

Im Folgenden sehen Sie die verfügbaren Bereiche für Power BI-Arbeitsbereichssammlungen.

|`Scope`|BESCHREIBUNG|
|---|---|
|Dataset.Read|Stellt die Berechtigung zum Lesen des angegebenen Datasets bereit.|
|Dataset.Write|Stellt die Berechtigung zum Schreiben in das angegebene Dataset bereit.|
|Dataset.ReadWrite|Stellt die Berechtigung zum Lesen des angegebenen Datasets sowie zum Schreiben bereit.|
|Report.Read|Stellt die Berechtigung zum Anzeigen des angegebenen Berichts bereit.|
|Report.ReadWrite|Stellt die Berechtigung zum Anzeigen und Bearbeiten des angegebenen Berichts bereit.|
|Workspace.Report.Create|Stellt die Berechtigung zum Erstellen eines neuen Berichts innerhalb des angegebenen Arbeitsbereichs bereit.|
|Workspace.Report.Copy|Stellt die Berechtigung zum Klonen eines vorhandenen Berichts innerhalb des angegebenen Arbeitsbereichs bereit.|

Sie können mehrere Bereiche angeben, indem Sie zwischen den Bereichen ein Leerzeichen einfügen (s.u.).

```csharp
string scopes = "Dataset.Read Workspace.Report.Create";
```

**Erforderliche Ansprüche – Bereiche**

scp: {scopesClaim} scopesClaim kann entweder eine Zeichenfolge oder ein Array von Zeichenfolgen sein, worin die zulässigen Berechtigungen für die Arbeitsbereichsressourcen (Bericht, Dataset usw.) erfasst werden.

Ein decodiertes Token mit definierten Bereichen sieht etwa wie folgt aus:

```
Header

{
    typ: "JWT",
    alg: "HS256:
}

Body

{
  "ver": "0.2.0",
  "wcn": "SupportDemo",
  "wid": "ca675b19-6c3c-4003-8808-1c7ddc6bd809",
  "rid": "96241f0f-abae-4ea9-a065-93b428eddb17",
  "scp": "Report.Read",
  "iss": "PowerBISDK",
  "aud": "https://analysis.windows.net/powerbi/api",
  "exp": 1360047056,
  "nbf": 1360043456
}

```

### <a name="operations-and-scopes"></a>Vorgänge und Bereiche

|Vorgang|Zielressource|Tokenberechtigungen|
|---|---|---|
|Erstellen Sie (In-Memory) einen neuen Bericht basierend auf einem Dataset.|Datensatz|Dataset.Read|
|Erstellen Sie (im Speicher) einen neuen Bericht basierend auf einem Dataset, und speichern Sie den Bericht.|Datensatz|* Dataset.Read<br>* Workspace.Report.Create|
|Zeigen Sie einen vorhandenen Bericht an (im Speicher), und untersuchen/bearbeiten Sie ihn. Report.Read impliziert Dataset.Read. „Report.Read“ lässt keine Speicherung von Änderungen zu.|Bericht|Report.Read|
|Bearbeiten und speichern Sie einen vorhandenen Bericht.|Bericht|Report.ReadWrite|
|Speichern Sie eine Kopie eines Berichts (Speichern unter).|Bericht|* Report.Read<br>* Workspace.Report.Copy|

## <a name="heres-how-the-flow-works"></a>Informationen zum Ablauf
1. Kopieren der API-Schlüssel zu Ihrer Anwendung Sie finden die Schlüssel im **Azure-Portal**.
   
    ![Wo die API-Schlüssel im Azure-Portal zu finden sind](media/get-started-sample/azure-portal.png)
1. Das Token setzt einen Anspruch durch und hat eine Ablaufzeit.
   
    ![App-Tokenfluss: Token bestätigt Anspruch.](media/get-started-sample/token-2.png)
1. Das Token wird mit einer API-Tastenkombination angemeldet.
   
    ![App-Tokenfluss: Token wird signiert.](media/get-started-sample/token-3.png)
1. Der Benutzer fordert die Ansicht des Berichts an.
   
    ![App-Tokenfluss: Benutzer fordert die Ansicht eines Berichts an.](media/get-started-sample/token-4.png)
1. Das Token wird mit einer API-Tastenkombination validiert.
   
   ![App-Tokenfluss: Token wird validiert.](media/get-started-sample/token-5.png)
1. Von Power BI-Arbeitsbereichssammlungen wird ein Bericht an den Benutzer gesendet.
   
   ![App-Tokenfluss: Dienst sendet Bericht an Benutzer.](media/get-started-sample/token-6.png)

Nachdem aus **Power BI-Arbeitsbereichssammlungen** ein Bericht an den Benutzer gesendet wurde, kann dieser den Bericht in Ihrer benutzerdefinierten App anzeigen. Wenn Sie also etwa das [Beispiel „Analyzing Sales Data PBIX“](https://download.microsoft.com/download/1/4/E/14EDED28-6C58-4055-A65C-23B4DA81C4DE/Analyzing_Sales_Data.pbix) importiert haben, sieht die Beispiel-Web-App wie folgt aus:

![Beispiel eines in eine Anwendung eingebetteten Berichts](media/get-started-sample/sample-web-app.png)

## <a name="see-also"></a>Siehe auch

[CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN)  
[Erste Schritte mit Microsoft Power BI-Arbeitsbereichssammlungen: Beispiel](get-started-sample.md)  
[Häufige Szenarien für Microsoft Power BI-Arbeitsbereichssammlungen](scenarios.md)  
[Erste Schritte mit Microsoft Power BI-Arbeitsbereichssammlungen](get-started.md)  
[PowerBI-CSharp Git-Repository](https://github.com/Microsoft/PowerBI-CSharp)

Weitere Fragen? [Power BI-Community ausprobieren](https://community.powerbi.com/)
