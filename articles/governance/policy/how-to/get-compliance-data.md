---
title: Abrufen von Daten zur Richtlinienkonformität
description: Azure Policy-Auswertungen und -Effekte bestimmen die Konformität. Erfahren Sie, wie Sie Konformitätsinformationen Ihrer Azure-Ressourcen abrufen.
ms.date: 10/05/2020
ms.topic: how-to
ms.openlocfilehash: 112badce00ec56df0f80c7b51bb4789a414cdcbd
ms.sourcegitcommit: 03c0a713f602e671b278f5a6101c54c75d87658d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/19/2020
ms.locfileid: "94920235"
---
# <a name="get-compliance-data-of-azure-resources"></a>Abrufen von Compliancedaten von Azure-Ressourcen

Sie genießen mit Azure Policy den Vorteil, Einblicke in Steuerelemente für Ressourcen in einem Abonnement oder einer [Verwaltungsgruppe](../../management-groups/overview.md) von Abonnements zu erhalten. Dieses Steuerelement kann unterschiedlich verwendet werden, z.B. zum Verhindern, dass Ressourcen am falschen Speicherort erstellt werden, zum Erzwingen häufiger und konsistenter Tagnutzung oder zur Überwachung vorhandener Ressourcen für geeignete Konfigurationen und Einstellungen. In jedem Fall werden Daten aber von Azure Policy generiert, damit Sie den Konformitätsstatus Ihrer Umgebung kennen.

Es gibt mehrere Möglichkeiten, auf Konformitätsinformationen, die von Ihrer Richtlinie sowie Initiativenzuweisungen erstellt wurden, zuzugreifen:

- Verwenden des [Azure-Portals](#portal)
- Über [Befehlszeilen](#command-line)-Skripting

Bevor wir uns die Methoden zur Berichterstellung zur Konformität ansehen, beschäftigen wir uns damit, wann Konformitätsinformationen aktualisiert werden und mit den Ereignissen, die einen Auswertungszyklus auslösen sowie mit der Häufigkeit.

> [!WARNING]
> Wenn der Konformitätszustand als **Nicht registriert** gemeldet wird, sollten Sie überprüfen, ob der **Microsoft.PolicyInsights**-Ressourcenanbieter registriert ist und der Benutzer über die entsprechenden Berechtigungen für die rollenbasierte Zugriffssteuerung in Azure (Azure RBAC) verfügt. Dies ist unter [Azure RBAC-Berechtigungen in Azure Policy](../overview.md#azure-rbac-permissions-in-azure-policy) beschrieben.

## <a name="evaluation-triggers"></a>Auswertungsauslöser

Die Ergebnisse eines abgeschlossenen Auswertungszyklus stehen im Ressourcenanbieter `Microsoft.PolicyInsights` über die Vorgänge `PolicyStates` und `PolicyEvents` zur Verfügung. Weitere Informationen zu den Vorgängen der Azure Policy Insights-REST-API finden Sie unter [Policy Insights](/rest/api/policy-insights/).

Auswertungen zugewiesener Richtlinien und Initiativen geschehen im Zuge unterschiedlicher Ereignisse:

- Eine Richtlinie oder Initiative wird einem Bereich neu zugewiesen. Es dauert etwa 30 Minuten, bis die Zuweisung auf den definierten Bereich angewendet wird. Sobald sie angewendet wird, startet der Auswertungszyklus für Ressourcen innerhalb dieses Bereich für die neu zugewiesene Richtlinie oder Initiative. Je nach den Auswirkungen, die von der Richtlinie oder Initiative verwendet werden, werden Ressourcen als „konform“ oder „nicht konform“ oder „Ausnahme“ markiert. Eine große Richtlinie oder Initiative, die für einen großen Ressourcenbereich angewendet wird, kann einige Zeit in Anspruch nehmen. Es kann leider keine exakte Dauer des Auswertungszyklus angegeben werden. Sobald er abgeschlossen ist, sind aktualisierte Konformitätsergebnisse im Portal und den SDKs verfügbar.

- Eine Richtlinie oder Initiative, die bereits einem Bereich zugewiesen ist, wird aktualisiert. Der Auswertungszyklus und die zeitliche Steuerung für dieses Szenario entspricht denjenigen von Neuzuweisungen zu einem Bereich.

- Eine Ressource wird in einem Bereich mit einer Zuweisung über Azure Resource Manager, die REST-API oder ein unterstütztes SDK bereitgestellt oder aktualisiert. In diesem Szenario werden nach etwa 15 Minuten die Informationen über das betroffene Ereignis (Anfügen, Überwachen, Verweigern, Bereitstellen) und den Konformitätsstatus für die jeweilige Ressource im Portal und den SDKs verfügbar. Dieses Ereignis löst keine Auswertung anderer Ressourcen aus.

- Eine [Richtlinienausnahme](../concepts/exemption-structure.md) wird erstellt, aktualisiert oder gelöscht. In diesem Szenario wird die entsprechende Zuweisung für den definierten Ausnahmebereich ausgewertet.

- Standard-Konformitätsauswertungszyklus. Zuweisungen werden alle 24 Stunden automatisch neu ausgewertet. Eine große Richtlinie oder Initiative mit vielen Ressourcen kann einige Zeit in Anspruch nehmen. Es kann leider keine exakte Dauer des Auswertungszyklus angegeben werden. Sobald er abgeschlossen ist, sind aktualisierte Konformitätsergebnisse im Portal und den SDKs verfügbar.

- Der Ressourcenanbieter für die [Gastkonfiguration](../concepts/guest-configuration.md) wird von einer verwalteten Ressource mit Konformitätsdetails aktualisiert.

- Bedarfsgesteuerter Scan

### <a name="on-demand-evaluation-scan"></a>Bedarfsgesteuerter Auswertungsscan

Ein Auswertungsscan für ein Abonnement oder eine Ressourcengruppe kann mit Azure CLI, Azure PowerShell, einem Aufruf an die REST-API oder mithilfe der [GitHub-Aktion für die Azure Policy-Konformitätsprüfung](https://github.com/marketplace/actions/azure-policy-compliance-scan) gestartet werden.
Dieser Scan ist ein asynchroner Prozess.

#### <a name="on-demand-evaluation-scan---github-action"></a>Bedarfsgesteuerter Auswertungsscan – GitHub-Aktion

Verwenden Sie die [Aktion „Azure Policy-Konformitätsprüfung“](https://github.com/marketplace/actions/azure-policy-compliance-scan), um von Ihrem [GitHub-Workflow](https://docs.github.com/actions/configuring-and-managing-workflows/configuring-a-workflow#about-workflows) aus für eine oder mehrere Ressourcen, Ressourcengruppen oder Abonnements bei Bedarf einen Auswertungsscan auszulösen und den Workflow basierend auf dem Konformitätsstatus der Ressourcen zu steuern. Sie können den Workflow auch so konfigurieren, dass er zu einer geplanten Zeit abläuft, sodass Sie zu einem geeigneten Zeitpunkt den neuesten Konformitätsstatus erhalten. Optional kann mit dieser GitHub-Aktion ein Bericht über den Konformitätsstatus der überprüften Ressourcen zur weiteren Analyse oder zur Archivierung erstellt werden.

Im folgenden Beispiel wird eine Konformitätsprüfung für ein Abonnement ausgeführt. 

```yaml
on:
  schedule:    
    - cron:  '0 8 * * *'  # runs every morning 8am
jobs:
  assess-policy-compliance:    
    runs-on: ubuntu-latest
    steps:         
    - name: Login to Azure
      uses: azure/login@v1
      with:
        creds: ${{secrets.AZURE_CREDENTIALS}} 

    
    - name: Check for resource compliance
      uses: azure/policy-compliance-scan@v0
      with:
        scopes: |
          /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

Weitere Informationen und Workflowbeispiele finden Sie im [Repository „GitHub-Aktion für die Azure Policy-Konformitätsprüfung“](https://github.com/Azure/policy-compliance-scan).

#### <a name="on-demand-evaluation-scan---azure-cli"></a>Bedarfsgesteuerter Auswertungsscan – Azure CLI

Die Kompatibilitätsüberprüfung wird mit dem Befehl [az policy state trigger-scan](/cli/azure/policy/state#az_policy_state_trigger_scan) gestartet.

Standardmäßig startet `az policy state trigger-scan` eine Auswertung für alle Ressourcen im aktuellen Abonnement. Verwenden Sie den Parameter **resource-group**, um eine Auswertung für eine bestimmte Ressourcengruppe zu starten. Im folgenden Beispiel wird eine Kompatibilitätsüberprüfung im aktuellen Abonnement für die _MyRG_-Ressourcengruppe gestartet:

```azurecli-interactive
az policy state trigger-scan --resource-group "MyRG"
```

Sie können sich dafür entscheiden, nicht auf den Abschluss des asynchronen Prozesses zu warten, bevor Sie mit dem Parameter **no-wait** fortfahren.

#### <a name="on-demand-evaluation-scan---azure-powershell"></a>Bedarfsgesteuerter Auswertungsscan – Azure PowerShell

Die Kompatibilitätsüberprüfung wird mit dem [Start-AzPolicyComplianceScan](/powershell/module/az.policyinsights/start-azpolicycompliancescan)-Cmdlet gestartet.

Standardmäßig startet `Start-AzPolicyComplianceScan` eine Auswertung für alle Ressourcen im aktuellen Abonnement. Verwenden Sie den Parameter **ResourceGroupName**, um eine Auswertung für eine bestimmte Ressourcengruppe zu starten. Im folgenden Beispiel wird eine Kompatibilitätsüberprüfung im aktuellen Abonnement für die _MyRG_-Ressourcengruppe gestartet:

```azurepowershell-interactive
Start-AzPolicyComplianceScan -ResourceGroupName 'MyRG'
```

Sie können PowerShell auf den Abschluss des asynchronen Aufrufes warten lassen, bevor Sie die Ergebnisausgabe bereitstellen, oder PowerShell im Hintergrund als [Auftrag](/powershell/module/microsoft.powershell.core/about/about_jobs) ausführen. Wenn Sie die Kompatibilitätsüberprüfung im Hintergrund mit einem PowerShell-Auftrag ausführen möchten, verwenden Sie den Parameter **AsJob**, und legen Sie den Wert auf ein Objekt fest, wie z. B. `$job` in diesem Beispiel:

```azurepowershell-interactive
$job = Start-AzPolicyComplianceScan -AsJob
```

Sie können den Status des Auftrags überprüfen, indem Sie das `$job`-Objekt überprüfen. Der Auftrag ist vom Typ `Microsoft.Azure.Commands.Common.AzureLongRunningJob`. Verwenden Sie `Get-Member` für das `$job`-Objekt, um die verfügbaren Eigenschaften und Methoden anzuzeigen.

Während der Kompatibilitätsüberprüfung ergibt die Überprüfung der `$job`-Objektausgaben Ergebnisse wie diese:

```azurepowershell-interactive
$job

Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
2      Long Running O… AzureLongRunni… Running       True            localhost            Start-AzPolicyCompliance…
```

Wenn die Kompatibilitätsüberprüfung abgeschlossen ist, ändert sich die **Status**-Eigenschaft in _Abgeschlossen_.

#### <a name="on-demand-evaluation-scan---rest"></a>Bedarfsgesteuerter Auswertungsscan – REST

Daher wartet der REST-Endpunkt als asynchroner Prozess zum Starten des Scans nicht, bis der Scan abgeschlossen ist, um zu reagieren. Stattdessen stellt er einen URI bereit, um den Status der angeforderten Auswertung abzufragen.

In jedem REST-API-URI gibt es Variablen, die Sie durch Ihre eigenen Werte ersetzen müssen:

- Ersetzen Sie `{YourRG}` durch den Namen Ihrer Ressourcengruppe.
- Ersetzen Sie `{subscriptionId}` durch Ihre Abonnement-ID.

Der Scan unterstützt die Auswertung von Ressourcen in einem Abonnement oder in einer Ressourcengruppe. Starten Sie einen Scan nach Bereich mit einem REST-API-Befehl **POST** anhand der folgenden URI-Strukturen:

- Subscription

  ```http
  POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/triggerEvaluation?api-version=2019-10-01
  ```

- Resource group

  ```http
  POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{YourRG}/providers/Microsoft.PolicyInsights/policyStates/latest/triggerEvaluation?api-version=2019-10-01
  ```

Der Aufruf gibt einen Status **202 - Akzeptiert** zurück. Im Antwortheader enthalten ist eine **Speicherort**-Eigenschaft mit dem folgenden Format:

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/asyncOperationResults/{ResourceContainerGUID}?api-version=2019-10-01
```

`{ResourceContainerGUID}` wird für den angeforderten Bereich statisch generiert. Wird für einen Bereich bereits ein bedarfsgesteuerter Scan ausgeführt, wird kein neuer Scan gestartet. Stattdessen wird der neuen Anforderung derselbe **Speicherort**-URI `{ResourceContainerGUID}` für den Status bereitgestellt. Ein REST-API-Befehl **GET** für den **Speicherort**-URI gibt während der laufenden Auswertung einen Status **202 - Akzeptiert** zurück. Nach Abschluss des Auswertungsscans wird ein Status **200 - OK** zurückgegeben. Der Text eines abgeschlossenen Scans ist eine JSON-Antwort mit folgendem Status:

```json
{
    "status": "Succeeded"
}
```

#### <a name="on-demand-evaluation-scan---visual-studio-code"></a>Bedarfsgesteuerter Auswertungsscan – Visual Studio Code

Mit der Azure Policy-Erweiterung für Visual Studio Code kann ein Auswertungsscan für eine bestimmte Ressource ausgeführt werden. Bei diesem Scan handelt es sich im Gegensatz zu den Azure PowerShell- und REST-Methoden um einen synchronen Prozess.
Ausführliche Informationen und Schritte finden Sie unter [Verwenden der Azure Policy-Erweiterung für Visual Studio Code](./extension-for-vscode.md#on-demand-evaluation-scan).

## <a name="how-compliance-works"></a>Funktionsweise der Konformität

In einer Zuweisung ist eine Ressource **nicht konform**, wenn dafür die Richtlinien- oder Initiativenregeln nicht eingehalten werden und wenn sie keine _Ausnahme_ ist. Die folgende Tabelle gibt Aufschluss über das Zusammenspiel zwischen den verschiedenen Richtlinienauswirkungen, der Bedingungsauswertung und dem resultierenden Konformitätszustand:

| Ressourcenzustand | Wirkung | Richtlinienauswertung | Konformitätszustand |
| --- | --- | --- | --- |
| Neu oder aktualisiert | Audit, Modify, AuditIfNotExist | True | Nicht konform |
| Neu oder aktualisiert | Audit, Modify, AuditIfNotExist | False | Konform |
| Exists | Deny, Audit, Append, Modify, DeployIfNotExist, AuditIfNotExist | True | Nicht konform |
| Exists | Deny, Audit, Append, Modify, DeployIfNotExist, AuditIfNotExist | False | Konform |

> [!NOTE]
> Für die Auswirkungen „DeployIfNotExist“ und „AuditIfNotExist“ muss die IF-Anweisung TRUE und die Existenzbedingung FALSE sein, um nicht konform zu sein. Bei TRUE löst die IF-Bedingung die Auswertung der Existenzbedingung für die zugehörigen Ressourcen aus.

Angenommen, Sie verfügen über die Ressourcengruppe ContosoRG mit einigen Speicherkonten (rot hervorgehoben), die in öffentlichen Netzwerken verfügbar gemacht werden.

:::image type="complex" source="../media/getting-compliance-data/resource-group01.png" alt-text="Diagramm der Speicherkonten, die für öffentliche Netzwerke in der Ressourcengruppe „ContosoRG“ verfügbar gemacht werden." border="false":::
   Im Diagramm werden Bilder für fünf Speicherkonten in der Ressourcengruppe „ContosoRG“ angezeigt.  Die Speicherkonten 1 und 3 sind blau, und die Speicherkonten zwei, vier und fünf sind rot.
:::image-end:::

In diesem Beispiel ist Vorsicht aufgrund von Sicherheitsrisiken geboten. Nachdem Sie nun eine Richtlinienzuweisung erstellt haben, wird sie für alle eingeschlossenen und nicht ausgenommenen Speicherkonten in der Ressourcengruppe „ContosoRG“ ausgewertet. Sie überprüft die drei nicht konformen Speicherkonten und ändert den Status daher jeweils in **nicht konform**.

:::image type="complex" source="../media/getting-compliance-data/resource-group03.png" alt-text="Diagramm der Speicherkontenkonformität in der Ressourcengruppe „ContosoRG“" border="false":::
   Im Diagramm werden Bilder für fünf Speicherkonten in der Ressourcengruppe „ContosoRG“ angezeigt. Unter den Speicherkonten 1 und 3 befinden sich jetzt grüne Häkchen, und unter den Speicherkonten 2, 4 und 5 werden jetzt rote Warnzeichen angezeigt.
:::image-end:::

Neben **Konform** und **Nicht konform** können Richtlinien und Ressourcen vier weitere Zustände aufweisen:

- **Ausnahme**: Die Ressource befindet sich im Bereich einer Zuweisung, verfügt jedoch über eine [definierte Ausnahme](../concepts/exemption-structure.md).
- **Steht in Konflikt**: Mindestens zwei Richtliniendefinitionen mit in Konflikt stehenden Regeln sind vorhanden. Beispielsweise zwei Definitionen, die das gleiche Tag mit unterschiedlichen Werten anfügen.
- **Nicht gestartet**: Der Auswertungszyklus für die Richtlinie oder Ressource wurde noch nicht gestartet.
- **Nicht registriert**: Der Azure Policy-Ressourcenanbieter wurde nicht registriert, oder das angemeldete Konto hat keine Berechtigung zum Lesen von Konformitätsdaten.

Azure Policy verwendet die Felder **type**, **name** oder **kind** in der Definition der Richtlinienregel, um zu ermitteln, ob eine Ressource übereinstimmend ist. Falls ja, wird sie als anwendbar angesehen und hat entweder den Zustand **Konform**, **Nicht konform** oder **Ausnahme**. Wenn entweder **type**, **name** oder **kind** die einzige Eigenschaft in der Definition ist, werden alle eingeschlossenen und nicht ausgenommenen Ressourcen als anwendbar angesehen und ausgewertet.

Der Prozentsatz der Konformität wird ermittelt, indem die **konformen** und **ausgenommen** Ressourcen durch _Ressourcen gesamt_ geteilt werden. _Ressourcen gesamt_ ist als Summe der Ressourcen mit dem Zustand **Konform**, **Nicht konform**, **Ausnahme** und **Konflikt** definiert. Die Gesamtzahl für die Konformität ist die Summe der einzelnen Ressourcen mit dem Zustand **Konform** oder **Ausnahme**, dividiert durch die Summe aller einzelnen Ressourcen. Die Abbildung unten enthält 20 einzelne Ressourcen, die zutreffen, und nur eine davon ist **nicht konform**.
Daher lautet der Gesamtwert für die Ressourcenkonformität 95 % (19 von 20).

:::image type="content" source="../media/getting-compliance-data/simple-compliance.png" alt-text="Screenshot der Richtlinienkonformitätsdetails auf der Seite „Konformität“" border="false":::

> [!NOTE]
> Die Einhaltung gesetzlicher Bestimmungen in Azure Policy ist eine Funktion in der Vorschauversion. Complianceeigenschaften von SDK und Seiten im Portal unterscheiden sich für aktivierte Initiativen. Weitere Informationen finden Sie unter [Einhaltung gesetzlicher Bestimmungen](../concepts/regulatory-compliance.md).

## <a name="portal"></a>Portal

Im Azure-Portal ist eine grafische Benutzeroberfläche zum Anzeigen und Verstehen des Konformitätsstatus Ihrer Umgebung dargestellt. Auf der Seite **Richtlinie** stellt die Option **Übersicht** Details für verfügbare Bereiche zur Konformität für Richtlinien und Initiativen bereit. Neben dem Konformitätsstatus und der Anzahl pro Zuweisung ist ein Diagramm enthalten, das die Konformität der letzten sieben Tage anzeigt. Die Seite **Konformität** enthält im Grunde genommen die gleichen Informationen (mit Ausnahme des Diagramms), stellt jedoch zusätzliche Optionen zum Filtern und Sortieren bereit.

:::image type="content" source="../media/getting-compliance-data/compliance-page.png" alt-text="Screenshot der Seite „Konformität“, Filteroptionen und Details" border="false":::

Da eine Richtlinie oder Initiative unterschiedlichen Bereichen zugewiesen werden kann, finden Sie in der Tabelle den Bereich für jede Zuweisung und den Typ der Definition, die zugewiesen wurde. Die Anzahl der nicht konformen Ressourcen und Richtlinien für jede Zuweisung wird ebenfalls bereitgestellt. Wenn Sie eine Richtlinie oder Initiative in der Tabelle auswählen, erhalten Sie weitere Informationen zur Konformität für eine bestimmte Zuweisung.

:::image type="content" source="../media/getting-compliance-data/compliance-details.png" alt-text="Screenshot der Seite „Kompatibilitätsdetails“, einschließlich der Anzahl und Ressourcenkonformitätsdetails" border="false":::

Die Liste der Ressourcen auf der Registerkarte **Ressourcenkonformität** zeigt den Bewertungsstatus der vorhandenen Ressourcen für die aktuelle Zuweisung. Die Registerkarte ist standardmäßig auf **Nicht konform** festgelegt, kann aber gefiltert werden.
Ereignisse (Anfügung, Überwachung, Verweigerung, Bereitstellung, Änderung), die durch die Anforderung zum Erstellen einer Ressource ausgelöst wurden, werden auf der Registerkarte **Ereignisse** angezeigt.

> [!NOTE]
> Bei einer AKS-Engine-Richtlinie ist die angezeigte Ressource die Ressourcengruppe.

:::image type="content" source="../media/getting-compliance-data/compliance-events.png" alt-text="Screenshot der Registerkarte „Ereignisse“ auf der Seite „Kompatibilitätsdetails“" border="false":::

<a name="component-compliance"></a> Wenn Sie bei Ressourcen im [Ressourcenanbietermodus](../concepts/definition-structure.md#resource-provider-modes) auf der Registerkarte **Ressourcenkonformität** die Ressource auswählen oder mit der rechten Maustaste auf die Zeile klicken und **Konformitätsdetails anzeigen** auswählen, wird die Seite mit Details zur Komponentenkompatibilität geöffnet. Diese Seite bietet auch Registerkarten, auf denen die Richtlinien angezeigt werden, die dieser Ressource, Ereignissen, Komponentenereignissen und dem Änderungsverlauf zugewiesen sind.

:::image type="content" source="../media/getting-compliance-data/compliance-components.png" alt-text="Screenshot der Registerkarte „Komponentenkonformität“ und Konformitätsdetails für eine Zuweisung im Ressourcenanbietermodus" border="false":::

Wenn Sie sich wieder auf der Seite für Ressourcenkonformität befinden, klicken Sie mit der rechten Maustaste auf die Zeile des Ereignisses, über das Sie gern mehr Details erhalten möchten, und wählen Sie **Aktivitätsprotokolle anzeigen** aus. Die Seite des Aktivitätsprotokolls wird geöffnet und wird durch die Suche gefiltert. Die Details für die Zuweisung und Ereignisse werden angezeigt. Das Aktivitätsprotokoll stellt zusätzlichen Kontext sowie Informationen über diese Ereignisse bereit.

:::image type="content" source="../media/getting-compliance-data/compliance-activitylog.png" alt-text="Screenshot des Aktivitätsprotokolls für Azure Policy -Aktivitäten und -Auswertungen" border="false":::

### <a name="understand-non-compliance"></a>Grundlagen der Nichtkompatibilität

Wenn Ressourcen als **nicht kompatibel** bestimmt werden, sind hierfür viele Ursachen möglich. Wie Sie die Ursache für die **Nichtkompatibilität** einer Ressource bestimmen oder die dafür verantwortliche Änderung finden können, ist unter [Bestimmen der Nichtkompatibilität](./determine-non-compliance.md) beschrieben.

## <a name="command-line"></a>Befehlszeile

Die gleichen Informationen, die im Portal verfügbar sind, können über die REST-API (u. a. mit [ARMClient](https://github.com/projectkudu/ARMClient)), Azure PowerShell und die Azure CLI abgerufen werden. Ausführliche Informationen zur REST-API finden Sie in der Referenz zu [Azure Policy Insights](/rest/api/policy-insights/). Die Referenzseiten zur REST-API verfügen über eine grüne „Ausprobieren“-Schaltfläche, mit der Sie jeden Vorgang direkt im Browser testen können.

Verwenden Sie ARMClient oder ein ähnliches Tool, um die Authentifizierung in Azure für die REST-API-Beispiele vorzunehmen.

### <a name="summarize-results"></a>Zusammenfassen der Ergebnisse

Mithilfe der REST-API kann die Zusammenfassung nach Container, Definition oder Zuweisung erfolgen. Hier sehen Sie ein Beispiel der Zusammenfassung auf Abonnementebene mithilfe der Option [Summarize For Subscription](/rest/api/policy-insights/policystates/summarizeforsubscription) (Für Abonnement zusammenfassen) von Azure Policy Insights:

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/summarize?api-version=2019-10-01
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
            "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=ComplianceState eq 'NonCompliant'",
            "nonCompliantResources": 15,
            "nonCompliantPolicies": 1
        },
        "policyAssignments": [{
            "policyAssignmentId": "/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77",
            "policySetDefinitionId": "",
            "results": {
                "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=ComplianceState eq 'NonCompliant' and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77'",
                "nonCompliantResources": 15,
                "nonCompliantPolicies": 1
            },
            "policyDefinitions": [{
                "policyDefinitionReferenceId": "",
                "policyDefinitionId": "/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "effect": "deny",
                "results": {
                    "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=ComplianceState eq 'NonCompliant' and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77' and PolicyDefinitionId eq '/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62'",
                    "nonCompliantResources": 15
                }
            }]
        }]
    }]
}
```

### <a name="query-for-resources"></a>Abfragen von Ressourcen

Im obigen Beispiel stellt **value.policyAssignments.policyDefinitions.results.queryResultsUri** einen Beispiel-URI bereit, mit dem alle nicht konformen Ressourcen für eine bestimmte Richtliniendefinition abgerufen werden können. Sehen Sie sich den **$filter**-Wert an. Sie werden feststellen, dass „ComplianceState“ gleich (eq) „NonCompliant“ ist und „PolicyAssignmentId“ für die Richtliniendefinition sowie für „PolicyDefinitionId“ selbst angegeben ist. Der Grund für das Einschließen der „PolicyAssignmentId“ in den Filter ist der, dass die „PolicyDefinitionId“ in mehreren Zuweisungen von Richtlinien oder Initiativen mit verschiedenen Bereichen vorhanden sein kann. Durch Angeben der „PolicyAssignmentId“ und der „PolicyDefinitionId“ können wir die Ergebnisse eingrenzen, die wir suchen. Zuvor haben wir für „PolicyStates“ **latest** verwendet, womit automatisch ein Zeitfenster **from** (von) und **to** (bis) der letzten 24 Stunden festgelegt wird.

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=ComplianceState eq 'NonCompliant' and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77' and PolicyDefinitionId eq '/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62'
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
        "ComplianceState": "NonCompliant",
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

### <a name="view-events"></a>Anzeigen von Ereignissen

Wenn eine Ressource erstellt oder aktualisiert wird, wird ein Ergebnis der Richtlinienauswertung generiert. Diese Ergebnisse werden als _Richtlinienereignisse_ bezeichnet. Verwenden Sie den folgenden URI, um die neuesten Richtlinienereignisse anzuzeigen, die dem Abonnement zugewiesen sind.

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyEvents/default/queryResults?api-version=2019-10-01
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

### <a name="azure-cli"></a>Azure CLI

Die [Azure CLI](/cli/azure/what-is-azure-cli)-Befehlsgruppe für Azure Policy umfasst die meisten Vorgänge, die in REST oder Azure PowerShell verfügbar sind. Die vollständige Liste der verfügbaren Befehle finden Sie unter [Azure CLI – Azure Policy: Übersicht](/cli/azure/policy).

Beispiel: Abrufen der Zustandszusammenfassung für die oberste zugewiesene Richtlinie mit der höchsten Anzahl nicht konformer Ressourcen

```azurecli-interactive
az policy state summarize --top 1
```

Der erste Teil der Antwort sieht in etwa wie im folgenden Beispiel aus:

```json
{
    "odatacontext": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#summary/$entity",
    "odataid": null,
    "policyAssignments": [{
            "policyAssignmentId": "/subscriptions/{subscriptionId}/providers/microsoft.authorization/policyassignments/e0704696df5e4c3c81c873e8",
            "policyDefinitions": [{
                "effect": "audit",
                "policyDefinitionGroupNames": [
                    ""
                ],
                "policyDefinitionId": "/subscriptions/{subscriptionId}/providers/microsoft.authorization/policydefinitions/2e3197b6-1f5b-4b01-920c-b2f0a7e9b18a",
                "policyDefinitionReferenceId": "",
                "results": {
                    "nonCompliantPolicies": null,
                    "nonCompliantResources": 398,
                    "policyDetails": [{
                        "complianceState": "noncompliant",
                        "count": 1
                    }],
                    "policyGroupDetails": [{
                        "complianceState": "noncompliant",
                        "count": 1
                    }],
                    "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$from=2020-07-14 14:01:22Z&$to=2020-07-15 14:01:22Z and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/providers/microsoft.authorization/policyassignments/e0704696df5e4c3c81c873e8' and PolicyDefinitionId eq '/subscriptions/{subscriptionId}/providers/microsoft.authorization/policydefinitions/2e3197b6-1f5b-4b01-920c-b2f0a7e9b18a'",
                    "resourceDetails": [{
                            "complianceState": "noncompliant",
                            "count": 398
                        },
                        {
                            "complianceState": "compliant",
                            "count": 4
                        }
                    ]
                }
            }],
    ...
```

Beispiel: Abrufen des Zustandsdatensatzes für die zuletzt ausgewertete Ressource (Standardwert ist nach Zeitstempel in absteigender Reihenfolge)

```azurecli-interactive
az policy state list --top 1
```

```json
[
  {
    "complianceReasonCode": "",
    "complianceState": "Compliant",
    "effectiveParameters": "",
    "isCompliant": true,
    "managementGroupIds": "{managementgroupId}",
    "odatacontext": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
    "odataid": null,
    "policyAssignmentId": "/subscriptions/{subscriptionId}/providers/microsoft.authorization/policyassignments/securitycenterbuiltin",
    "policyAssignmentName": "SecurityCenterBuiltIn",
    "policyAssignmentOwner": "tbd",
    "policyAssignmentParameters": "",
    "policyAssignmentScope": "/subscriptions/{subscriptionId}",
    "policyAssignmentVersion": "",
    "policyDefinitionAction": "auditifnotexists",
    "policyDefinitionCategory": "tbd",
    "policyDefinitionGroupNames": [
      ""
    ],
    "policyDefinitionId": "/providers/microsoft.authorization/policydefinitions/aa633080-8b72-40c4-a2d7-d00c03e80bed",
    "policyDefinitionName": "aa633080-8b72-40c4-a2d7-d00c03e80bed",
    "policyDefinitionReferenceId": "identityenablemfaforownerpermissionsmonitoring",
    "policyDefinitionVersion": "",
    "policyEvaluationDetails": null,
    "policySetDefinitionCategory": "security center",
    "policySetDefinitionId": "/providers/Microsoft.Authorization/policySetDefinitions/1f3afdf9-d0c9-4c3d-847f-89da613e70a8",
    "policySetDefinitionName": "1f3afdf9-d0c9-4c3d-847f-89da613e70a8",
    "policySetDefinitionOwner": "",
    "policySetDefinitionParameters": "",
    "policySetDefinitionVersion": "",
    "resourceGroup": "",
    "resourceId": "/subscriptions/{subscriptionId}",
    "resourceLocation": "",
    "resourceTags": "tbd",
    "resourceType": "Microsoft.Resources/subscriptions",
    "subscriptionId": "{subscriptionId}",
    "timestamp": "2020-07-15T08:37:07.903433+00:00"
  }
]
```

Beispiel: Abrufen der Details für alle nicht konformen Ressourcen virtueller Netzwerke

```azurecli-interactive
az policy state list --filter "ResourceType eq 'Microsoft.Network/virtualNetworks'"
```

```json
[
  {
    "complianceReasonCode": "",
    "complianceState": "NonCompliant",
    "effectiveParameters": "",
    "isCompliant": false,
    "managementGroupIds": "{managementgroupId}",
    "odatacontext": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
    "odataid": null,
    "policyAssignmentId": "/subscriptions/{subscriptionId}/providers/microsoft.authorization/policyassignments/e0704696df5e4c3c81c873e8",
    "policyAssignmentName": "e0704696df5e4c3c81c873e8",
    "policyAssignmentOwner": "tbd",
    "policyAssignmentParameters": "",
    "policyAssignmentScope": "/subscriptions/{subscriptionId}",
    "policyAssignmentVersion": "",
    "policyDefinitionAction": "audit",
    "policyDefinitionCategory": "tbd",
    "policyDefinitionGroupNames": [
      ""
    ],
    "policyDefinitionId": "/subscriptions/{subscriptionId}/providers/microsoft.authorization/policydefinitions/2e3197b6-1f5b-4b01-920c-b2f0a7e9b18a",
    "policyDefinitionName": "2e3197b6-1f5b-4b01-920c-b2f0a7e9b18a",
    "policyDefinitionReferenceId": "",
    "policyDefinitionVersion": "",
    "policyEvaluationDetails": null,
    "policySetDefinitionCategory": "",
    "policySetDefinitionId": "",
    "policySetDefinitionName": "",
    "policySetDefinitionOwner": "",
    "policySetDefinitionParameters": "",
    "policySetDefinitionVersion": "",
    "resourceGroup": "RG-Tags",
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Microsoft.Network/virtualNetworks/RG-Tags-vnet",
    "resourceLocation": "westus2",
    "resourceTags": "tbd",
    "resourceType": "Microsoft.Network/virtualNetworks",
    "subscriptionId": "{subscriptionId}",
    "timestamp": "2020-07-15T08:37:07.901911+00:00"
  }
]
```

Beispiel: Abrufen von Ereignissen, die auf nicht konforme Ressourcen virtueller Netzwerke bezogen sind, die nach einem bestimmten Datum aufgetreten sind

```azurecli-interactive
az policy state list --filter "ResourceType eq 'Microsoft.Network/virtualNetworks'" --from '2020-07-14T00:00:00Z'
```

```json
[
  {
    "complianceReasonCode": "",
    "complianceState": "NonCompliant",
    "effectiveParameters": "",
    "isCompliant": false,
    "managementGroupIds": "{managementgroupId}",
    "odatacontext": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
    "odataid": null,
    "policyAssignmentId": "/subscriptions/{subscriptionId}/providers/microsoft.authorization/policyassignments/e0704696df5e4c3c81c873e8",
    "policyAssignmentName": "e0704696df5e4c3c81c873e8",
    "policyAssignmentOwner": "tbd",
    "policyAssignmentParameters": "",
    "policyAssignmentScope": "/subscriptions/{subscriptionId}",
    "policyAssignmentVersion": "",
    "policyDefinitionAction": "audit",
    "policyDefinitionCategory": "tbd",
    "policyDefinitionGroupNames": [
      ""
    ],
    "policyDefinitionId": "/subscriptions/{subscriptionId}/providers/microsoft.authorization/policydefinitions/2e3197b6-1f5b-4b01-920c-b2f0a7e9b18a",
    "policyDefinitionName": "2e3197b6-1f5b-4b01-920c-b2f0a7e9b18a",
    "policyDefinitionReferenceId": "",
    "policyDefinitionVersion": "",
    "policyEvaluationDetails": null,
    "policySetDefinitionCategory": "",
    "policySetDefinitionId": "",
    "policySetDefinitionName": "",
    "policySetDefinitionOwner": "",
    "policySetDefinitionParameters": "",
    "policySetDefinitionVersion": "",
    "resourceGroup": "RG-Tags",
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Microsoft.Network/virtualNetworks/RG-Tags-vnet",
    "resourceLocation": "westus2",
    "resourceTags": "tbd",
    "resourceType": "Microsoft.Network/virtualNetworks",
    "subscriptionId": "{subscriptionId}",
    "timestamp": "2020-07-15T08:37:07.901911+00:00"
  }
]
```

### <a name="azure-powershell"></a>Azure PowerShell

Das Azure PowerShell-Modul für Azure Policy ist im PowerShell-Katalog unter [Az.PolicyInsights](https://www.powershellgallery.com/packages/Az.PolicyInsights) verfügbar. Mit PowerShellGet können Sie das Modul mit `Install-Module -Name Az.PolicyInsights` installieren (stellen Sie sicher, dass Sie die neueste Version von [Azure PowerShell](/powershell/azure/install-az-ps) installiert haben):

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
ComplianceState            : NonCompliant
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
ComplianceState            : NonCompliant
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

Beispiel: Abrufen von Ereignissen in Bezug auf nicht konforme virtuelle Netzwerkressourcen, die nach einem bestimmten Datum aufgetreten sind, Konvertierung in ein CSV-Objekt und Export in eine Datei.

```azurepowershell-interactive
$policyEvents = Get-AzPolicyEvent -Filter "ResourceType eq '/Microsoft.Network/virtualNetworks'" -From '2020-09-19'
$policyEvents | ConvertTo-Csv | Out-File 'C:\temp\policyEvents.csv'
```

Die Ausgabe des `$policyEvents`-Objekts sieht wie folgt aus:

```output
Timestamp                  : 9/19/2020 5:18:53 AM
ResourceId                 : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Network/virtualNetworks/RG-Tags-vnet
PolicyAssignmentId         : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Authorization/policyAssignments/37ce239ae4304622914f0c77
PolicyDefinitionId         : /providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62
ComplianceState            : NonCompliant
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

Wenn Sie über einen [Log Analytics-Arbeitsbereich](../../../azure-monitor/log-query/log-query-overview.md) mit `AzureActivity` aus der [Log Analytics-Aktivitätslösung](../../../azure-monitor/platform/activity-log.md) verfügen, die mit Ihrem Abonnement verknüpft ist, können Sie auch nicht kompatible Ergebnisse der Auswertung von neuen und aktualisierten Ressourcen mithilfe einfacher Kusto-Abfragen und über die Tabelle `AzureActivity` anzeigen. Mithilfe von Details in Azure Monitor-Protokollen können Warnmeldungen konfiguriert werden, um Verstöße gegen die Konformität zu überwachen.

:::image type="content" source="../media/getting-compliance-data/compliance-loganalytics.png" alt-text="Screenshot der Azure Monitor-Protokolle, in denen Azure Policy-Aktionen in der Tabelle „AzureActivity“ angezeigt werden" border="false":::

## <a name="next-steps"></a>Nächste Schritte

- Sehen Sie sich die Beispiele unter [Azure Policy-Beispiele](../samples/index.md) an.
- Lesen Sie die Informationen unter [Struktur von Azure Policy-Definitionen](../concepts/definition-structure.md).
- Lesen Sie [Grundlegendes zu Richtlinienauswirkungen](../concepts/effects.md).
- Informieren Sie sich über das [programmgesteuerte Erstellen von Richtlinien](programmatically-create.md).
- Erfahren Sie, wie Sie [nicht konforme Ressourcen korrigieren](remediate-resources.md) können.
- Weitere Informationen zu Verwaltungsgruppen finden Sie unter [Organisieren Ihrer Ressourcen mit Azure-Verwaltungsgruppen](../../management-groups/overview.md).
