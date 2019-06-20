---
title: Abrufen von Daten zur Richtlinienkonformität
description: Azure Policy-Auswertungen und -Effekte bestimmen die Konformität. Erfahren Sie, wie Sie Konformitätsinformationen abrufen.
author: DCtheGeek
ms.author: dacoulte
ms.date: 02/01/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 428a1614889409300064420e1d3d4fbc0423a0ec
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66237526"
---
# <a name="get-compliance-data-of-azure-resources"></a>Abrufen von Compliancedaten von Azure-Ressourcen

Sie genießen mit Azure Policy den Vorteil, Einblicke in Steuerelemente für Ressourcen in einem Abonnement oder einer [Verwaltungsgruppe](../../management-groups/overview.md) von Abonnements zu erhalten. Dieses Steuerelement kann unterschiedlich verwendet werden, z.B. zum Verhindern, dass Ressourcen am falschen Speicherort erstellt werden, zum Erzwingen häufiger und konsistenter Tagnutzung oder zur Überwachung vorhandener Ressourcen für geeignete Konfigurationen und Einstellungen. In jedem Fall werden Daten aber von Azure Policy generiert, damit Sie den Konformitätsstatus Ihrer Umgebung kennen.

Es gibt mehrere Möglichkeiten, auf Konformitätsinformationen, die von Ihrer Richtlinie sowie Initiativenzuweisungen erstellt wurden, zuzugreifen:

- Verwenden des [Azure-Portals](#portal)
- Über [Befehlszeilen](#command-line)-Skripting

Bevor wir uns die Methoden zur Berichterstellung zur Konformität ansehen, beschäftigen wir uns damit, wann Konformitätsinformationen aktualisiert werden und mit den Ereignissen, die einen Auswertungszyklus auslösen sowie mit der Häufigkeit.

> [!WARNING]
> Wenn der Konformitätszustand als **Nicht registriert** gemeldet wird, sollten Sie überprüfen, ob der **Microsoft.PolicyInsights**-Ressourcenanbieter registriert ist und der Benutzer über die entsprechenden Berechtigungen für die rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC) verfügt. Dies ist unter [Rollenbasierte Zugriffssteuerung in Azure Policy](../overview.md#rbac-permissions-in-azure-policy) beschrieben.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="evaluation-triggers"></a>Auswertungsauslöser

Die Ergebnisse eines abgeschlossenen Auswertungszyklus stehen im Ressourcenanbieter `Microsoft.PolicyInsights` über die Vorgänge `PolicyStates` und `PolicyEvents` zur Verfügung. Weitere Informationen zu den Vorgängen der Azure Policy Insights-REST-API finden Sie unter [Policy Insights](/rest/api/policy-insights/).

Auswertungen zugewiesener Richtlinien und Initiativen geschehen im Zuge unterschiedlicher Ereignisse:

- Eine Richtlinie oder Initiative wird einem Bereich neu zugewiesen. Es dauert etwa 30 Minuten, bis die Zuweisung auf den definierten Bereich angewendet wird. Sobald sie angewendet wird, startet der Auswertungszyklus für Ressourcen innerhalb dieses Bereich für die neu zugewiesene Richtlinie oder Initiative. Je nach den Auswirkungen, die von der Richtlinie oder Initiative verwendet werden, werden Ressourcen als „konform“ oder „nicht konform“ markiert. Eine große Richtlinie oder Initiative, die für einen großen Ressourcenbereich angewendet wird, kann einige Zeit in Anspruch nehmen. Es kann leider keine exakte Dauer des Auswertungszyklus angegeben werden. Sobald er abgeschlossen ist, sind aktualisierte Konformitätsergebnisse im Portal und den SDKs verfügbar.

- Eine Richtlinie oder Initiative, die bereits einem Bereich zugewiesen ist, wird aktualisiert. Der Auswertungszyklus und die zeitliche Steuerung für dieses Szenario entspricht denjenigen von Neuzuweisungen zu einem Bereich.

- Eine Ressource wird in einem Bereich mit einer Zuweisung über den Ressourcen-Manager, REST, Azure CLI oder Azure PowerShell bereitgestellt. In diesem Szenario werden nach etwa 15 Minuten die Informationen über das betroffene Ereignis (Anfügen, Überwachen, Verweigern, Bereitstellen) und den Konformitätsstatus für die jeweilige Ressource im Portal und den SDKs verfügbar. Dieses Ereignis löst keine Auswertung anderer Ressourcen aus.

- Standard-Konformitätsauswertungszyklus. Zuweisungen werden alle 24 Stunden automatisch neu ausgewertet. Eine große Richtlinie oder Initiative mit vielen Ressourcen kann einige Zeit in Anspruch nehmen. Es kann leider keine exakte Dauer des Auswertungszyklus angegeben werden. Sobald er abgeschlossen ist, sind aktualisierte Konformitätsergebnisse im Portal und den SDKs verfügbar.

- Der Ressourcenanbieter für die [Gastkonfiguration](../concepts/guest-configuration.md) wird von einer verwalteten Ressource mit Konformitätsdetails aktualisiert.

- Bedarfsgesteuerter Scan

### <a name="on-demand-evaluation-scan"></a>Bedarfsgesteuerter Auswertungsscan

Ein Auswertungsscan für ein Abonnement oder eine Ressourcengruppe kann mit einem Aufruf der REST-API gestartet werden. Dieser Scan ist ein asynchroner Prozess. Daher wartet der REST-Endpunkt zum Starten des Scans nicht, bis der Scan abgeschlossen ist, um zu reagieren. Stattdessen stellt er einen URI bereit, um den Status der angeforderten Auswertung abzufragen.

In jedem REST-API-URI gibt es Variablen, die Sie durch Ihre eigenen Werte ersetzen müssen:

- Ersetzen Sie `{YourRG}` durch den Namen Ihrer Ressourcengruppe.
- Ersetzen Sie `{subscriptionId}` durch Ihre Abonnement-ID.

Der Scan unterstützt die Auswertung von Ressourcen in einem Abonnement oder in einer Ressourcengruppe. Starten Sie einen Scan nach Bereich mit einem REST-API-Befehl **POST** anhand der folgenden URI-Strukturen:

- Abonnement

  ```http
  POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/triggerEvaluation?api-version=2018-07-01-preview
  ```

- Ressourcengruppe

  ```http
  POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{YourRG}/providers/Microsoft.PolicyInsights/policyStates/latest/triggerEvaluation?api-version=2018-07-01-preview
  ```

Der Aufruf gibt einen Status **202 - Akzeptiert** zurück. Im Antwortheader enthalten ist eine **Speicherort**-Eigenschaft mit dem folgenden Format:

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/asyncOperationResults/{ResourceContainerGUID}?api-version=2018-07-01-preview
```

`{ResourceContainerGUID}` wird für den angeforderten Bereich statisch generiert. Wird für einen Bereich bereits ein bedarfsgesteuerter Scan ausgeführt, wird kein neuer Scan gestartet. Stattdessen wird der neuen Anforderung derselbe **Speicherort**-URI `{ResourceContainerGUID}` für den Status bereitgestellt. Ein REST-API-Befehl **GET** für den **Speicherort**-URI gibt während der laufenden Auswertung einen Status **202 - Akzeptiert** zurück. Nach Abschluss des Auswertungsscans wird ein Status **200 - OK** zurückgegeben. Der Text eines abgeschlossenen Scans ist eine JSON-Antwort mit folgendem Status:

```json
{
    "status": "Succeeded"
}
```

## <a name="how-compliance-works"></a>Funktionsweise der Konformität

In einer Zuweisung ist eine Ressource **nicht konform**, wenn dafür die Richtlinien- oder Initiativenregeln nicht eingehalten werden.
Die folgende Tabelle gibt Aufschluss über das Zusammenspiel zwischen den verschiedenen Richtlinienauswirkungen, der Bedingungsauswertung und dem resultierenden Konformitätszustand:

| Ressourcenzustand | Wirkung | Richtlinienauswertung | Konformitätszustand |
| --- | --- | --- | --- |
| Exists | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | True | Nicht konform |
| Exists | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | False | Konform |
| Neu | Audit, AuditIfNotExist\* | True | Nicht konform |
| Neu | Audit, AuditIfNotExist\* | False | Konform |

\* Für die Auswirkungen „Append“, „DeployIfNotExist“ und „AuditIfNotExist“ muss die IF-Anweisung auf TRUE festgelegt sein.
Für die Auswirkungen muss die Existenzbedingung außerdem auf FALSE festgelegt sein, damit sie nicht konform sind. Bei TRUE löst die IF-Bedingung die Auswertung der Existenzbedingung für die zugehörigen Ressourcen aus.

Angenommen, Sie verfügen über die Ressourcengruppe ContosoRG mit einigen Speicherkonten (rot hervorgehoben), die in öffentlichen Netzwerken verfügbar gemacht werden.

![In öffentlichen Netzwerken verfügbar gemachte Speicherkonten](../media/getting-compliance-data/resource-group01.png)

In diesem Beispiel ist Vorsicht aufgrund von Sicherheitsrisiken geboten. Nachdem Sie nun eine Richtlinienzuweisung erstellt haben, wird sie für alle Speicherkonten in der Ressourcengruppe „ContosoRG“ ausgewertet. Sie überprüft die drei nicht konformen Speicherkonten und ändert den Status daher jeweils in **nicht konform**.

![Überwachte nicht konforme Speicherkonten](../media/getting-compliance-data/resource-group03.png)

Neben **Konform** und **Nicht konform** können Richtlinien und Ressourcen drei andere Zustände aufweisen:

- **Steht in Konflikt**: Mindestens zwei Richtlinien mit in Konflikt stehenden Regeln sind vorhanden. Beispielsweise zwei Richtlinien, die das gleiche Tag mit unterschiedlichen Werten anfügen.
- **Nicht gestartet**: Der Auswertungszyklus für die Richtlinie oder Ressource wurde noch nicht gestartet.
- **Nicht registriert**: Der Azure Policy-Ressourcenanbieter wurde nicht registriert, oder das angemeldete Konto hat keine Berechtigung zum Lesen von Konformitätsdaten.

Azure Policy verwendet die Felder **type** und **name** in der Definition der Richtlinienregel, um zu ermitteln, ob eine Ressource übereinstimmend ist. Falls ja, wird sie als anwendbar angesehen und hat entweder den Status **Konform** oder **Nicht konform**. Wenn entweder **type** oder **name** die einzige Eigenschaft in der Definition ist, werden alle Ressourcen als anwendbar angesehen und ausgewertet.

Der Prozentsatz der Konformität wird ermittelt, indem die **konformen** Ressourcen durch _Ressourcen gesamt_ geteilt werden.
_Ressourcen gesamt_ ist als Summe der Ressourcen mit dem Zustand **Konform**, **Nicht konform** und **Konflikt** definiert. Die Gesamtzahl für die Konformität ist die Summe der einzelnen Ressourcen, die **konform** sind, geteilt durch die Summe aller einzelnen Ressourcen. Die Abbildung unten enthält 20 einzelne Ressourcen, die zutreffen, und nur eine davon ist **nicht konform**. Daher lautet der Gesamtwert für die Ressourcenkonformität 95 % (19 von 20).

![Beispiel für Richtlinienkonformität auf der Seite „Compliance“](../media/getting-compliance-data/simple-compliance.png)

## <a name="portal"></a>Portal

Im Azure-Portal ist eine grafische Benutzeroberfläche zum Anzeigen und Verstehen des Konformitätsstatus Ihrer Umgebung dargestellt. Auf der Seite **Richtlinie** stellt die Option **Übersicht** Details für verfügbare Bereiche zur Konformität für Richtlinien und Initiativen bereit. Neben dem Konformitätsstatus und der Anzahl pro Zuweisung ist ein Diagramm enthalten, das die Konformität der letzten sieben Tage anzeigt. Die Seite **Konformität** enthält im Grunde genommen die gleichen Informationen (mit Ausnahme des Diagramms), stellt jedoch zusätzliche Optionen zum Filtern und Sortieren bereit.

![Beispiel der Azure-Seite für Richtlinienkonformität](../media/getting-compliance-data/compliance-page.png)

Da eine Richtlinie oder Initiative unterschiedlichen Bereichen zugewiesen werden kann, finden Sie in der Tabelle den Bereich für jede Zuweisung und den Typ der Definition, die zugewiesen wurde. Die Anzahl der nicht konformen Ressourcen und Richtlinien für jede Zuweisung wird ebenfalls bereitgestellt. Wenn Sie auf eine Richtlinie oder Initiative in der Tabelle klicken, erhalten Sie weitere Informationen zur Konformität für eine bestimmte Zuweisung.

![Beispiel der Azure-Seite für Konformitätsdetails](../media/getting-compliance-data/compliance-details.png)

Die Liste der Ressourcen auf der Registerkarte **Ressourcenkonformität** zeigt den Bewertungsstatus der vorhandenen Ressourcen für die aktuelle Zuweisung. Die Registerkarte ist standardmäßig auf **Nicht konform** festgelegt, kann aber gefiltert werden.
Ereignisse (Anfügung, Überwachung, Verweigerung, Bereitstellung), die durch die Anforderung zum Erstellen einer Ressource ausgelöst wurden, werden auf der Registerkarte **Ereignisse** angezeigt.

![Beispiel für Ereignisse auf der Azure-Seite für Richtlinienkonformität](../media/getting-compliance-data/compliance-events.png)

Klicken Sie mit der rechten Maustaste auf die Zeile des Ereignisses, über das Sie gern mehr Details erhalten möchten, und wählen Sie **Aktivitätsprotokolle anzeigen** aus. Die Seite des Aktivitätsprotokolls wird geöffnet und wird durch die Suche gefiltert. Die Details für die Zuweisung und Ereignisse werden angezeigt. Das Aktivitätsprotokoll stellt zusätzlichen Kontext sowie Informationen über diese Ereignisse bereit.

![Beispiel des Richtlinienkonformitäts-Aktivitätsprotokolls von Azure](../media/getting-compliance-data/compliance-activitylog.png)

### <a name="understand-non-compliance"></a>Grundlagen der Nichtkompatibilität

<a name="change-history-preview"></a>

Wenn Ressourcen als **nicht kompatibel** bestimmt werden, kann das viele mögliche Ursachen haben. Wie Sie die Ursache für die **Nichtkompatibilität** einer Ressource bestimmen oder die dafür verantwortliche Änderung finden können, ist unter [Bestimmen der Nichtkompatibilität](./determine-non-compliance.md) beschrieben.

## <a name="command-line"></a>Befehlszeile

Die gleichen Informationen, die im Portal verfügbar sind, können über die REST-API (u. a. mit [ARMClient](https://github.com/projectkudu/ARMClient)) oder Azure PowerShell abgerufen werden. Ausführliche Informationen zur REST-API finden Sie in der Referenz zu [Azure Policy Insights](/rest/api/policy-insights/). Die Referenzseiten zur REST-API verfügen über eine grüne „Ausprobieren“-Schaltfläche, mit der Sie jeden Vorgang direkt im Browser testen können.

Um die folgenden Beispiele in Azure PowerShell zu verwenden, erstellen Sie ein Authentifizierungstoken mit folgendem Beispielcode. Ersetzen Sie dann „$restUri“ durch die gewünschten Zeichenfolge in den Beispielen, um ein JSON-Objekt abzurufen, das dann analysiert werden kann.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

$azContext = Get-AzContext
$azProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
$profileClient = New-Object -TypeName Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient -ArgumentList ($azProfile)
$token = $profileClient.AcquireAccessToken($azContext.Subscription.TenantId)
$authHeader = @{
    'Content-Type'='application/json'
    'Authorization'='Bearer ' + $token.AccessToken
}

# Define the REST API to communicate with
# Use double quotes for $restUri as some endpoints take strings passed in single quotes
$restUri = "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/summarize?api-version=2018-04-04"

# Invoke the REST API
$response = Invoke-RestMethod -Uri $restUri -Method POST -Headers $authHeader

# View the response object (as JSON)
$response
```

### <a name="summarize-results"></a>Zusammenfassen der Ergebnisse

Mithilfe der REST-API kann die Zusammenfassung nach Container, Definition oder Zuweisung erfolgen. Hier sehen Sie ein Beispiel der Zusammenfassung auf Abonnementebene mithilfe der Option [Summarize For Subscription](/rest/api/policy-insights/policystates/summarizeforsubscription) (Für Abonnement zusammenfassen) von Azure Policy Insights:

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/summarize?api-version=2018-04-04
```

Die Ausgabe fasst das Abonnement zusammen. In der Beispielausgabe unten befindet sich die zusammengefasste Konformität unter **value.results.nonCompliantResources** und **value.results.nonCompliantPolicies**. Diese Anforderung stellt weitere Details bereit, einschließlich jeder Zuweisung, aus der die nicht konformen Zahlen und die Definitionsinformationen für jede Zuweisung bestehen. Jedes Richtlinienobjekt in der Hierarchie bietet einen **queryResultsUri**, der zum Abrufen zusätzlicher Details auf dieser Ebene verwendet werden kann.

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#summary",
    "@odata.count": 1,
    "value": [{
        "@odata.id": null,
        "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#summary/$entity",
        "results": {
            "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2018-04-04&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false",
            "nonCompliantResources": 15,
            "nonCompliantPolicies": 1
        },
        "policyAssignments": [{
            "policyAssignmentId": "/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77",
            "policySetDefinitionId": "",
            "results": {
                "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2018-04-04&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77'",
                "nonCompliantResources": 15,
                "nonCompliantPolicies": 1
            },
            "policyDefinitions": [{
                "policyDefinitionReferenceId": "",
                "policyDefinitionId": "/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "effect": "deny",
                "results": {
                    "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2018-04-04&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77' and PolicyDefinitionId eq '/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62'",
                    "nonCompliantResources": 15
                }
            }]
        }]
    }]
}
```

### <a name="query-for-resources"></a>Abfragen von Ressourcen

Im obigen Beispiel stellt **value.policyAssignments.policyDefinitions.results.queryResultsUri** einen Beispiel-URI bereit, mit dem alle nicht konformen Ressourcen für eine bestimmte Richtliniendefinition abgerufen werden können. Sehen Sie sich den Wert **$filter** an. Sie werden feststellen, dass „IsCompliant“ gleich (eq) FALSE ist und „PolicyAssignmentId“ für die Richtliniendefinition sowie die PolicyDefinitionId selbst angegeben ist. Der Grund für das Einschließen der „PolicyAssignmentId“ in den Filter ist der, dass die „PolicyDefinitionId“ in mehreren Zuweisungen von Richtlinien oder Initiativen mit verschiedenen Bereichen vorhanden sein kann. Durch Angeben der „PolicyAssignmentId“ und der „PolicyDefinitionId“ können wir die Ergebnisse eingrenzen, die wir suchen. Zuvor haben wir für „PolicyStates“ **latest** verwendet, womit automatisch ein Zeitfenster **from** (von) und **to** (bis) der letzten 24 Stunden festgelegt wird.

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2018-04-04&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77' and PolicyDefinitionId eq '/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62'
```

Die folgende Beispielantwort wurde der Kürze halber auf eine einzige nicht konforme Ressource reduziert. Die ausführliche Antwort enthält mehrere Teile von Ressourcendaten, die Richtlinie (oder Initiative) sowie die Zuweisung. Beachten Sie, dass Sie auch anzeigen können, welche Zuweisungsparameter an die Richtliniendefinition übergeben wurden.

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest",
    "@odata.count": 15,
    "value": [{
        "@odata.id": null,
        "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
        "timestamp": "2018-05-19T04:41:09Z",
        "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/rg-tags/providers/Microsoft.Compute/virtualMachines/linux",
        "policyAssignmentId": "/subscriptions/{subscriptionId}/resourceGroups/rg-tags/providers/Microsoft.Authorization/policyAssignments/37ce239ae4304622914f0c77",
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
        "effectiveParameters": "",
        "isCompliant": false,
        "subscriptionId": "{subscriptionId}",
        "resourceType": "/Microsoft.Compute/virtualMachines",
        "resourceLocation": "westus2",
        "resourceGroup": "RG-Tags",
        "resourceTags": "tbd",
        "policyAssignmentName": "37ce239ae4304622914f0c77",
        "policyAssignmentOwner": "tbd",
        "policyAssignmentParameters": "{\"tagName\":{\"value\":\"costCenter\"},\"tagValue\":{\"value\":\"Contoso-Test\"}}",
        "policyAssignmentScope": "/subscriptions/{subscriptionId}/resourceGroups/RG-Tags",
        "policyDefinitionName": "1e30110a-5ceb-460c-a204-c1c3969c6d62",
        "policyDefinitionAction": "deny",
        "policyDefinitionCategory": "tbd",
        "policySetDefinitionId": "",
        "policySetDefinitionName": "",
        "policySetDefinitionOwner": "",
        "policySetDefinitionCategory": "",
        "policySetDefinitionParameters": "",
        "managementGroupIds": "",
        "policyDefinitionReferenceId": ""
    }]
}
```

### <a name="view-events"></a>Ereignisse anzeigen

Wenn eine Ressource erstellt oder aktualisiert wird, wird ein Ergebnis der Richtlinienauswertung generiert. Diese Ergebnisse werden als _Richtlinienereignisse_ bezeichnet. Verwenden Sie den folgenden URI, um die neuesten Richtlinienereignisse anzuzeigen, die dem Abonnement zugewiesen sind.

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyEvents/default/queryResults?api-version=2018-04-04
```

Ihre Ergebnisse sollten in etwa wie im folgenden Beispiel aussehen:

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyEvents/$metadata#default",
    "@odata.count": 1,
    "value": [{
        "@odata.id": null,
        "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyEvents/$metadata#default/$entity",
        "NumAuditEvents": 16
    }]
}
```

Weitere Informationen zum Abfragen von Richtlinienereignissen finden Sie im Referenzartikel zu [Azure Policy-Ereignissen](/rest/api/policy-insights/policyevents).

### <a name="azure-powershell"></a>Azure PowerShell

Das Azure PowerShell-Modul für Azure Policy ist im PowerShell-Katalog unter [Az.PolicyInsights](https://www.powershellgallery.com/packages/Az.PolicyInsights) verfügbar.
Mit PowerShellGet können Sie das Modul mit `Install-Module -Name Az.PolicyInsights` installieren (stellen Sie sicher, dass Sie die neueste Version von [Azure PowerShell](/powershell/azure/install-az-ps) installiert haben):

```azurepowershell-interactive
# Install from PowerShell Gallery via PowerShellGet
Install-Module -Name Az.PolicyInsights

# Import the downloaded module
Import-Module Az.PolicyInsights

# Login with Connect-AzAccount if not using Cloud Shell
Connect-AzAccount
```

Das Modul umfasst die folgenden Cmdlets:

- `Get-AzPolicyStateSummary`
- `Get-AzPolicyState`
- `Get-AzPolicyEvent`
- `Get-AzPolicyRemediation`
- `Remove-AzPolicyRemediation`
- `Start-AzPolicyRemediation`
- `Stop-AzPolicyRemediation`

Beispiel: Abrufen der Zustandszusammenfassung für die oberste zugewiesene Richtlinie mit der höchsten Anzahl nicht konformer Ressourcen

```azurepowershell-interactive
PS> Get-AzPolicyStateSummary -Top 1

NonCompliantResources : 15
NonCompliantPolicies  : 1
PolicyAssignments     : {/subscriptions/{subscriptionId}/resourcegroups/RG-Tags/providers/micros
                        oft.authorization/policyassignments/37ce239ae4304622914f0c77}
```

Beispiel: Abrufen des Zustandsdatensatzes für die zuletzt ausgewertete Ressource (Standardwert ist nach Zeitstempel in absteigender Reihenfolge)

```azurepowershell-interactive
PS> Get-AzPolicyState -Top 1

Timestamp                  : 5/22/2018 3:47:34 PM
ResourceId                 : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Network/networkInterfaces/linux316
PolicyAssignmentId         : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Authorization/policyAssignments/37ce239ae4304622914f0c77
PolicyDefinitionId         : /providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62
IsCompliant                : False
SubscriptionId             : {subscriptionId}
ResourceType               : /Microsoft.Network/networkInterfaces
ResourceLocation           : westus2
ResourceGroup              : RG-Tags
ResourceTags               : tbd
PolicyAssignmentName       : 37ce239ae4304622914f0c77
PolicyAssignmentOwner      : tbd
PolicyAssignmentParameters : {"tagName":{"value":"costCenter"},"tagValue":{"value":"Contoso-Test"}}
PolicyAssignmentScope      : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags
PolicyDefinitionName       : 1e30110a-5ceb-460c-a204-c1c3969c6d62
PolicyDefinitionAction     : deny
PolicyDefinitionCategory   : tbd
```

Beispiel: Abrufen der Details für alle nicht konformen Ressourcen virtueller Netzwerke

```azurepowershell-interactive
PS> Get-AzPolicyState -Filter "ResourceType eq '/Microsoft.Network/virtualNetworks'"

Timestamp                  : 5/22/2018 4:02:20 PM
ResourceId                 : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Network/virtualNetworks/RG-Tags-vnet
PolicyAssignmentId         : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Authorization/policyAssignments/37ce239ae4304622914f0c77
PolicyDefinitionId         : /providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62
IsCompliant                : False
SubscriptionId             : {subscriptionId}
ResourceType               : /Microsoft.Network/virtualNetworks
ResourceLocation           : westus2
ResourceGroup              : RG-Tags
ResourceTags               : tbd
PolicyAssignmentName       : 37ce239ae4304622914f0c77
PolicyAssignmentOwner      : tbd
PolicyAssignmentParameters : {"tagName":{"value":"costCenter"},"tagValue":{"value":"Contoso-Test"}}
PolicyAssignmentScope      : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags
PolicyDefinitionName       : 1e30110a-5ceb-460c-a204-c1c3969c6d62
PolicyDefinitionAction     : deny
PolicyDefinitionCategory   : tbd
```

Beispiel: Abrufen von Ereignissen, die auf nicht konforme Ressourcen virtueller Netzwerke bezogen sind, die nach einem bestimmten Datum aufgetreten sind

```azurepowershell-interactive
PS> Get-AzPolicyEvent -Filter "ResourceType eq '/Microsoft.Network/virtualNetworks'" -From '2018-05-19'

Timestamp                  : 5/19/2018 5:18:53 AM
ResourceId                 : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Network/virtualNetworks/RG-Tags-vnet
PolicyAssignmentId         : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Authorization/policyAssignments/37ce239ae4304622914f0c77
PolicyDefinitionId         : /providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62
IsCompliant                : False
SubscriptionId             : {subscriptionId}
ResourceType               : /Microsoft.Network/virtualNetworks
ResourceLocation           : eastus
ResourceGroup              : RG-Tags
ResourceTags               : tbd
PolicyAssignmentName       : 37ce239ae4304622914f0c77
PolicyAssignmentOwner      : tbd
PolicyAssignmentParameters : {"tagName":{"value":"costCenter"},"tagValue":{"value":"Contoso-Test"}}
PolicyAssignmentScope      : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags
PolicyDefinitionName       : 1e30110a-5ceb-460c-a204-c1c3969c6d62
PolicyDefinitionAction     : deny
PolicyDefinitionCategory   : tbd
TenantId                   : {tenantId}
PrincipalOid               : {principalOid}
```

Das **PrincipalOid**-Feld kann verwendet werden, um einen bestimmten Benutzer mit dem Azure PowerShell-Cmdlet `Get-AzADUser` abzurufen. Ersetzen Sie **{principalOid}** mit der Antwort, die Sie aus dem vorherigen Beispiel erhalten haben.

```azurepowershell-interactive
PS> (Get-AzADUser -ObjectId {principalOid}).DisplayName
Trent Baker
```

## <a name="azure-monitor-logs"></a>Azure Monitor-Protokolle

Wenn Sie über einen [Log Analytics-Arbeitsbereich](../../../log-analytics/log-analytics-overview.md) mit `AzureActivity` aus der [Log Analytics-Aktivitätslösung](../../../azure-monitor/platform/activity-log-collect.md) verfügen, die mit Ihrem Abonnement verknüpft ist, können Sie auch nicht kompatible Ergebnisse aus dem Auswertungszyklus mithilfe einfacher Kusto-Abfragen und über die Tabelle `AzureActivity` anzeigen. Mithilfe von Details in Azure Monitor-Protokollen können Warnmeldungen konfiguriert werden, um Verstöße gegen die Konformität zu überwachen.


![Azure Policy-Konformität mithilfe von Azure Monitor-Protokollen](../media/getting-compliance-data/compliance-loganalytics.png)

## <a name="next-steps"></a>Nächste Schritte

- Sehen Sie sich die Beispiele unter [Azure Policy-Beispiele](../samples/index.md) an.
- Lesen Sie die Informationen unter [Struktur von Azure Policy-Definitionen](../concepts/definition-structure.md).
- Lesen Sie [Grundlegendes zu Richtlinienauswirkungen](../concepts/effects.md).
- Informieren Sie sich über das [programmgesteuerte Erstellen von Richtlinien](programmatically-create.md).
- Erfahren Sie, wie Sie [nicht konforme Ressourcen korrigieren](remediate-resources.md) können.
- Weitere Informationen zu Verwaltungsgruppen finden Sie unter [Organisieren Ihrer Ressourcen mit Azure-Verwaltungsgruppen](../../management-groups/overview.md).