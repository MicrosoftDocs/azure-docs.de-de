---
title: Sicherheit von Azure Cognitive Services
titleSuffix: Azure Cognitive Services
description: Hier finden Sie Informationen zu den verschiedenen Sicherheitsaspekten im Zusammenhang mit der Verwendung von Cognitive Services.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: erhopf
ms.custom: devx-track-python, devx-track-js, devx-track-csharp
ms.openlocfilehash: f14ac72443dedc8e33e607a82b2145c7ebf95ad2
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2020
ms.locfileid: "94368779"
---
# <a name="azure-cognitive-services-security"></a>Sicherheit von Azure Cognitive Services

Sicherheit sollte bei der Anwendungsentwicklung stets einen hohen Stellenwert haben. Angesichts des Aufkommens KI-fähiger Anwendungen ist Sicherheit sogar noch wichtiger geworden. In diesem Artikel werden verschiedene Aspekte der Sicherheit von Azure Cognitive Services erläutert – etwa die Verwendung von Transport Layer Security, Authentifizierung, einer sicheren Konfiguration für sensible Daten und Kunden-Lockbox für den Zugriff auf Kundendaten.

## <a name="transport-layer-security-tls"></a>Transport Layer Security (TLS)

Bei allen Cognitive Services-Endpunkten, die über HTTP verfügbar gemacht werden, wird TLS 1.2 erzwungen. Aufgrund des erzwungenen Sicherheitsprotokolls gelten für Consumer, die einen Cognitive Services-Endpunkt aufrufen möchten, folgende Richtlinien:

* Das Clientbetriebssystem (Operating System, OS) muss TLS 1.2 unterstützen.
* Von der für den HTTP-Aufruf verwendeten Sprache (und Plattform) muss TLS 1.2 in der Anforderung angegeben werden.
  * Die Angabe von TLS ist je nach Sprache und Plattform entweder implizit oder explizit.

Informationen für .NET-Benutzer finden Sie unter <a href="https://docs.microsoft.com/dotnet/framework/network-programming/tls" target="_blank">Bewährte Methoden für Transport Layer Security (TLS) mit .NET Framework<span class="docon docon-navigate-external x-hidden-focus"></span></a>.

## <a name="authentication"></a>Authentifizierung

Bei der Authentifizierung gibt es einige weit verbreitete Irrtümer. Authentifizierung und Autorisierung werden häufig miteinander verwechselt. Die Identität ist ebenfalls eine wichtige Sicherheitskomponente. Bei einer Identität handelt es sich um eine Sammlung von Informationen zu einem <a href="https://en.wikipedia.org/wiki/Principal_(computer_security)" target="_blank">Prinzipal<span class="docon docon-navigate-external x-hidden-focus"></span></a>. Identitätsanbieter (Identity Providers, IdPs) stellen Identitäten für Authentifizierungsdienste bereit. Bei der Authentifizierung handelt es sich um die Überprüfung der Identität eines Benutzers. Autorisierung ist die Angabe von Zugriffsrechten und Berechtigungen für Ressourcen für eine bestimmte Identität. Viele der Cognitive Services-Angebote beinhalten die rollenbasierte Zugriffssteuerung von Azure (Azure Role-Based Access Control, Azure RBAC). Mit der rollenbasierten Zugriffssteuerung von Azure lassen sich einige der Abläufe im Zusammenhang mit der manuellen Verwaltung von Prinzipalen vereinfachen. Ausführlichere Informationen finden Sie unter [Was ist die rollenbasierte Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC)?](../role-based-access-control/overview.md).

Weitere Informationen zur Authentifizierung mit Abonnementschlüsseln, Zugriffstoken und Azure Active Directory (AAD) finden Sie unter <a href="https://docs.microsoft.com/azure/cognitive-services/authentication" target="_blank">Authentifizieren von Anforderungen an Azure Cognitive Services<span class="docon docon-navigate-external x-hidden-focus"></span></a>.

## <a name="environment-variables-and-application-configuration"></a>Umgebungsvariablen und Anwendungskonfiguration

Umgebungsvariablen sind in einer bestimmten Umgebung gespeicherte Name-Wert-Paare. Die Verwendung von Umgebungsvariablen ist eine sicherere Alternative zu hartcodierten Werten für sensible Daten. Hartcodierte Werte sind unsicher und sollten nicht verwendet werden.

> [!CAUTION]
> Verwenden Sie **keine** hartcodierten Werte für sensible Daten. Dies ist ein schwerwiegendes Sicherheitsrisiko.

> [!NOTE]
> Umgebungsvariablen werden zwar als Nur-Text gespeichert, sind aber in einer Umgebung isoliert. Im Falle einer Umgebungskompromittierung sind auch die Variablen in der Umgebung kompromittiert.

### <a name="set-environment-variable"></a>Festlegen von Umgebungsvariablen

Verwenden Sie zum Festlegen von Umgebungsvariablen einen der folgenden Befehle. `ENVIRONMENT_VARIABLE_KEY` ist hierbei der benannte Schlüssel, und `value` ist der in der Umgebungsvariablen gespeicherte Wert:

# <a name="command-line"></a>[Befehlszeile](#tab/command-line)

Erstellen Sie eine persistente Umgebungsvariable, und weisen Sie sie dem entsprechenden Wert zu.

```CMD
:: Assigns the env var to the value
setx ENVIRONMENT_VARIABLE_KEY="value"
```

Lesen Sie die Umgebungsvariable in einer neuen Instanz der **Eingabeaufforderung**.

```CMD
:: Prints the env var value
echo %ENVIRONMENT_VARIABLE_KEY%
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Erstellen Sie eine persistente Umgebungsvariable, und weisen Sie sie dem entsprechenden Wert zu.

```powershell
# Assigns the env var to the value
[System.Environment]::SetEnvironmentVariable('ENVIRONMENT_VARIABLE_KEY', 'value', 'User')
```

Lesen Sie die Umgebungsvariable von **Windows PowerShell**.

```powershell
# Prints the env var value
[System.Environment]::GetEnvironmentVariable('ENVIRONMENT_VARIABLE_KEY')
```

# <a name="bash"></a>[Bash](#tab/bash)

Erstellen Sie eine persistente Umgebungsvariable, und weisen Sie sie dem entsprechenden Wert zu.

```Bash
# Assigns the env var to the value
echo export ENVIRONMENT_VARIABLE_KEY="value" >> /etc/environment && source /etc/environment
```

Lesen Sie die Umgebungsvariable in einer neuen Instanz von **Bash**.

```Bash
# Prints the env var value
echo "${ENVIRONMENT_VARIABLE_KEY}"

# Or use printenv:
# printenv ENVIRONMENT_VARIABLE_KEY
```

---

> [!TIP]
> Starten Sie nach dem Festlegen einer Umgebungsvariablen Ihre integrierte Entwicklungsumgebung (Integrated Development Environment, IDE) neu, um sicherzustellen, dass neu hinzugefügte Umgebungsvariablen verfügbar sind.

### <a name="get-environment-variable"></a>Abrufen einer Umgebungsvariablen

Wenn Sie eine Umgebungsvariable abrufen möchten, muss sie in den Arbeitsspeicher gelesen werden. Hierzu finden Sie im Anschluss Codeausschnitte für verschiedene Sprachen. In diesen Codeausschnitten wird gezeigt, wie Sie eine Umgebungsvariable mit `ENVIRONMENT_VARIABLE_KEY` abrufen und einer Variablen namens `value` zuweisen:

# <a name="c"></a>[C#](#tab/csharp)

Weitere Informationen finden Sie unter <a href="https://docs.microsoft.com/dotnet/api/system.environment.getenvironmentvariable" target="_blank">`Environment.GetEnvironmentVariable`<span class="docon docon-navigate-external x-hidden-focus"></span></a>.

```csharp
using static System.Environment;

class Program
{
    static void Main()
    {
        // Get the named env var, and assign it to the value variable
        var value =
            GetEnvironmentVariable(
                "ENVIRONMENT_VARIABLE_KEY");
    }
}
```

# <a name="c"></a>[C++](#tab/cpp)

Weitere Informationen finden Sie unter <a href="https://docs.microsoft.com/cpp/c-runtime-library/reference/getenv-wgetenv" target="_blank">`getenv`<span class="docon docon-navigate-external x-hidden-focus"></span></a>.

```cpp
#include <stdlib.h>

int main()
{
    // Get the named env var, and assign it to the value variable
    auto value =
        getenv("ENVIRONMENT_VARIABLE_KEY");
}
```

# <a name="java"></a>[Java](#tab/java)

Weitere Informationen finden Sie unter <a href="https://docs.oracle.com/javase/7/docs/api/java/lang/System.html#getenv(java.lang.String)" target="_blank">`System.getenv`<span class="docon docon-navigate-external x-hidden-focus"></span></a>.

```java
import java.lang.*;

public class Program {
   public static void main(String[] args) throws Exception {
    // Get the named env var, and assign it to the value variable
    String value =
        System.getenv(
            "ENVIRONMENT_VARIABLE_KEY")
   }
}
```

# <a name="nodejs"></a>[Node.js](#tab/node-js)

Weitere Informationen finden Sie unter <a href="https://nodejs.org/api/process.html#process_process_env" target="_blank">`process.env`<span class="docon docon-navigate-external x-hidden-focus"></span></a>.

```javascript
// Get the named env var, and assign it to the value variable
const value =
    process.env.ENVIRONMENT_VARIABLE_KEY;
```

# <a name="python"></a>[Python](#tab/python)

Weitere Informationen finden Sie unter <a href="https://docs.python.org/2/library/os.html#os.environ" target="_blank">`os.environ`<span class="docon docon-navigate-external x-hidden-focus"></span></a>.

```python
import os

# Get the named env var, and assign it to the value variable
value = os.environ['ENVIRONMENT_VARIABLE_KEY']
```

# <a name="objective-c"></a>[Objective-C](#tab/objective-c)

Weitere Informationen finden Sie unter <a href="https://developer.apple.com/documentation/foundation/nsprocessinfo/1417911-environment?language=objc" target="_blank">`environment`<span class="docon docon-navigate-external x-hidden-focus"></span></a>.

```objectivec
// Get the named env var, and assign it to the value variable
NSString* value =
    [[[NSProcessInfo processInfo]environment]objectForKey:@"ENVIRONMENT_VARIABLE_KEY"];
```

---

## <a name="customer-lockbox"></a>Kunden-Lockbox

[Kunden-Lockbox für Microsoft Azure](../security/fundamentals/customer-lockbox-overview.md) bietet eine Oberfläche, auf der Kunden Anforderungen nach Zugriff auf Kundendaten prüfen und dann genehmigen oder ablehnen können. Das Feature wird in Fällen verwendet, in denen ein Microsoft-Techniker während einer Supportanfrage auf Kundendaten zugreifen muss. Informationen zu der Weise, in der Kunden-Lockbox-Anforderungen initiiert, nachverfolgt und für spätere Überprüfungen und Audits speichert, finden Sie unter [Kunden-Lockbox](../security/fundamentals/customer-lockbox-overview.md). 

Kunden-Lockbox ist für diesen Cognitive Service erhältlich:

* Übersetzer

Für die folgenden Dienste greifen die Microsoft-Techniker im Tarif „E0“ auf keine Kundendaten zu: 

* Language Understanding
* Gesicht
* Content Moderator
* Personalisierung

> [!IMPORTANT]
> Für die **Formularerkennung** greifen Microsoft-Techniker auf keine Kundendaten in Ressourcen zu, die nach dem 10. Juli 2020 erstellt wurden.

Wenn Sie die Verwendung der E0-SKU beantragen möchten, füllen Sie dieses  [Anforderungsformular](https://aka.ms/cogsvc-cmk) aus, und reichen Sie es ein. Nach ca. 3–5 Werktagen erhalten Sie eine Rückmeldung zum Status Ihrer Anforderung. Je nach Bedarf können Sie in einer Warteschlange platziert und genehmigt werden, sobald Platz verfügbar ist. Nachdem Ihre Verwendung der E0-SKU mit LUIS genehmigt wurde, müssen Sie im Azure-Portal eine neue Ressource erstellen und E0 als Tarif auswählen. Benutzer können kein Upgrade von F0 auf die neue E0-SKU durchführen.

Der Sprachdienst unterstützt zurzeit keine Kunden-Lockbox. Kundendaten können jedoch mithilfe von BYOS („Bring-Your-Own-Storage“) gespeichert werden, sodass Sie eine ähnliche Datenkontrolle wie bei Kunden-Lockbox erreichen können. Bedenken Sie, dass Daten des Sprachdiensts in der Region verarbeitet werden und verbleiben, in der die Speech-Ressource erstellt wurde. Dies betrifft alle Daten, sowohl ruhende Daten als auch übertragene Daten. Bei der Verwendung von Anpassungsfunktionen wie Custom Speech und Custom Voice werden alle Kundendaten in der gleichen Region übertragen, gespeichert und verarbeitet, in der Ihr BYOS (sofern verwendet) und die Sprachdienstressource gespeichert sind.

> [!IMPORTANT]
> Microsoft verwendet **keine** Kundendaten zur Verbesserung seiner Sprachmodelle. Wenn darüber hinaus die Endpunktprotokollierung deaktiviert ist und keine Anpassungen verwendet werden, werden keine Kundendaten gespeichert. 

## <a name="next-steps"></a>Nächste Schritte

* Machen Sie sich mit [Azure Cognitive Services](./what-are-cognitive-services.md) vertraut.
* Erfahren Sie mehr über [virtuelle Netzwerke für Cognitive Services](cognitive-services-virtual-networks.md).