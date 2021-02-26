---
title: 'Schnellstart: Ihre erste REST-API-Abfrage'
description: In dieser Schnellstartanleitung führen Sie die Schritte zum Aufrufen des Resource Graph-Endpunkts für die REST-API und zum Ausführen Ihrer ersten Abfrage aus.
ms.date: 01/27/2021
ms.topic: quickstart
ms.openlocfilehash: 670ceba95d937be278c44c34704cb844eead480d
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98920053"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-rest-api"></a>Schnellstart: Ausführen Ihrer ersten Resource Graph-Abfrage per REST-API

Der erste Schritt zum Verwenden von Azure Resource Graph mit der REST-API umfasst die Überprüfung, ob Sie über ein Tool zum Aufrufen von REST-APIs verfügen. In dieser Schnellstartanleitung wird dann Schritt für Schritt beschrieben, wie Sie eine Abfrage ausführen und die Ergebnisse abrufen, indem Sie den Azure Resource Graph-REST-API-Endpunkt aufrufen.

Am Ende dieses Prozesses verfügen Sie über die Tools zum Aufrufen von REST-API-Endpunkten und zum Ausführen Ihrer ersten Resource Graph-Abfrage.

## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="getting-started-with-rest-api"></a>Erste Schritte mit der REST-API

Sollten Sie noch nicht mit der REST-API vertraut sein, finden Sie unter [Azure REST-API-Referenz](/rest/api/azure/) eine allgemeine Übersicht über die REST-API (insbesondere über den Anforderungs-URI und den Anforderungstext). In diesem Artikel dienen diese Konzepte als Grundlage für die Anweisungen zur Verwendung von Azure Resource Graph. Aus diesem Grund werden entsprechende Grundkenntnisse vorausgesetzt. Mit Tools wie [ARMClient](https://github.com/projectkudu/ARMClient) kann die Autorisierung automatisch durchgeführt werden. Diese Tools werden für Anfänger empfohlen.

Informationen zu den Spezifikationen von Azure Resource Graph finden Sie unter [Azure Resource Graph-REST-API](/rest/api/azure-resourcegraph/).

### <a name="rest-api-and-powershell"></a>REST-API und PowerShell

Wenn Sie noch über kein Tool für REST-API-Aufrufe verfügen, können Sie PowerShell für diese Anweisungen verwenden. Im folgenden Codebeispiel wird ein Header für die Authentifizierung bei Azure abgerufen. Generieren Sie einen Authentifizierungsheader, der manchmal als **Bearertoken** bezeichnet wird, und geben Sie den REST-API-URI für die Verbindung mit Parametern oder einen **Anforderungstext** an:

```azurepowershell-interactive
# Log in first with Connect-AzAccount if not using Cloud Shell

$azContext = Get-AzContext
$azProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
$profileClient = New-Object -TypeName Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient -ArgumentList ($azProfile)
$token = $profileClient.AcquireAccessToken($azContext.Subscription.TenantId)
$authHeader = @{
    'Content-Type'='application/json'
    'Authorization'='Bearer ' + $token.AccessToken
}

# Invoke the REST API
$restUri = 'https://management.azure.com/subscriptions/{subscriptionId}?api-version=2020-01-01'
$response = Invoke-RestMethod -Uri $restUri -Method Get -Headers $authHeader
```

Ersetzen Sie `{subscriptionId}` in der Variablen `$restUri`, um Informationen zu Ihrem Abonnement zu erhalten.
Die Variable `$response` enthält das Ergebnis des Cmdlets `Invoke-RestMethod`, das mit Cmdlets wie [ConvertFrom-Json](/powershell/module/microsoft.powershell.utility/convertfrom-json) analysiert werden kann. Wenn im REST-API-Dienstendpunkt ein **Anforderungstext** erwartet wird, geben Sie eine JSON-formatierte Variable für den Parameter `-Body` von `Invoke-RestMethod` an.

## <a name="run-your-first-resource-graph-query"></a>Ausführen Ihrer ersten Resource Graph-Abfrage

Nachdem die REST-API-Tools der Umgebung Ihrer Wahl hinzugefügt wurden, können Sie jetzt eine einfache Resource Graph-Abfrage ausprobieren. Die Abfrage gibt die ersten fünf Azure-Ressourcen mit dem **Namen** und **Ressourcentyp** der einzelnen Ressourcen zurück.

Im Anforderungstext jedes REST-API-Aufrufs ist eine Variable enthalten, die Sie durch Ihren eigenen Wert ersetzen müssen:

- Ersetzen Sie `{subscriptionID}` durch Ihre Abonnement-ID.

1. Führen Sie Ihre erste Azure Resource Graph-Abfrage aus, indem Sie die REST-API und den Endpunkt `resources` verwenden:

   - REST-API-URI

     ```http
     POST https://management.azure.com/providers/Microsoft.ResourceGraph/resources?api-version=2019-04-01
     ```

   - Anforderungstext

     ```json
     {
         "subscriptions": [
             "{subscriptionID}"
         ],
         "query": "Resources | project name, type | limit 5"
     }
     ```

   > [!NOTE]
   > Da dieses Abfragebeispiel keinen Sortierungsmodifizierer wie `order by` umfasst, ergibt die mehrfache Ausführung dieser Abfrage unter Umständen pro Anforderung einen anderen Satz von Ressourcen.

1. Aktualisieren Sie den Aufruf des Endpunkts `resouces`, und ändern Sie die **Abfrage** so, dass die Sortierung (`order by`) nach der Eigenschaft **Name** erfolgt:

   - REST-API-URI

     ```http
     POST https://management.azure.com/providers/Microsoft.ResourceGraph/resources?api-version=2019-04-01
     ```

   - Anforderungstext

     ```json
     {
         "subscriptions": [
             "{subscriptionID}"
         ],
         "query": "Resources | project name, type | limit 5 | order by name asc"
     }
     ```

   > [!NOTE]
   > Genau wie bei der ersten Abfrage ergibt die mehrfache Ausführung dieser Abfrage vermutlich pro Anforderung einen anderen Satz von Ressourcen. Die Reihenfolge der Abfragebefehle ist wichtig. In diesem Beispiel kommt `order by` nach `limit`. Durch diese Befehlsreihenfolge werden die Abfrageergebnisse zuerst eingeschränkt und dann sortiert.

1. Aktualisieren Sie den Aufruf des Endpunkts `resources`, und ändern Sie die **Abfrage** so, dass zuerst die Sortierung (`order by`) nach der Eigenschaft **Name** und dann die Begrenzung (`limit`) auf die ersten fünf Ergebnisse erfolgt:

   - REST-API-URI

     ```http
     POST https://management.azure.com/providers/Microsoft.ResourceGraph/resources?api-version=2019-04-01
     ```

   - Anforderungstext

     ```json
     {
         "subscriptions": [
             "{subscriptionID}"
         ],
         "query": "Resources | project name, type | order by name asc | limit 5"
     }
     ```

Wenn die letzte Abfrage mehrmals ausgeführt wird und in Ihrer Umgebung keine Änderungen vorgenommenen werden, sind die zurückgegebenen Ergebnisse konsistent und nach der Eigenschaft **Name** sortiert, aber immer noch auf die ersten fünf Ergebnisse begrenzt.

Weitere Beispiele für REST-API-Aufrufe für Azure Resource Graph finden Sie in den [REST-Beispielen für Azure Resource Graph](/rest/api/azureresourcegraph/resourcegraph(2019-04-01)/resources/resources#examples).

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Für die REST-API müssen keine Bibliotheken oder Module deinstalliert werden. Wenn Sie ein Tool zum Durchführen der Aufrufe installiert haben, z. B. _ARMClient_ oder _Postman_, und es nicht mehr benötigen, können Sie es jetzt deinstallieren.

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie den Resource Graph-REST-API-Endpunkt aufgerufen und Ihre erste Abfrage ausgeführt. Wenn Sie mehr über die Resource Graph-Sprache erfahren möchten, fahren Sie mit der Seite mit den Details zur Abfragesprache fort.

> [!div class="nextstepaction"]
> [Erfahren Sie mehr über die Abfragesprache](./concepts/query-language.md).
