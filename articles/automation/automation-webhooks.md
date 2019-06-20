---
title: Starten eines Azure Automation-Runbooks mit einem Webhook
description: Ein Webhook, der es einem Client ermöglicht, ein Runbook in Azure Automation über einen HTTP-Aufruf zu starten.  In diesem Artikel wird beschrieben, wie Sie einen Webhook erstellen und aufrufen, um ein Runbook zu starten.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/19/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 153bb0304102906f7be64ae55dd0e0f6bb8d7146
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61304565"
---
# <a name="starting-an-azure-automation-runbook-with-a-webhook"></a>Starten eines Azure Automation-Runbooks mit einem Webhook

Mit einem *Webhook* können Sie ein bestimmtes Runbook in Azure Automation über eine einfache HTTP-Anforderung starten. Dadurch wird es externen Diensten wie Azure DevOps Services, GitHub, Azure Monitor-Protokolle oder benutzerdefinierten Anwendungen ermöglicht, Runbooks zu starten, ohne eine vollständige Lösung unter Verwendung der Azure Automation-API zu implementieren.  
![WebhooksOverview](media/automation-webhooks/webhook-overview-image.png)

In [Starten eines Runbooks in Azure Automation](automation-starting-a-runbook.md)

> [!NOTE]
> Die Verwendung eines Webhooks zum Starten eines Python-Runbooks wird nicht unterstützt.

## <a name="details-of-a-webhook"></a>Details zu einem Webhook

Die folgende Tabelle beschreibt die Eigenschaften, die Sie für einen Webhook konfigurieren müssen.

| Eigenschaft | BESCHREIBUNG |
|:--- |:--- |
| NAME |Sie können einem Webhook einen beliebigen Namen zuweisen, da er nicht für den Client verfügbar gemacht wird. Sie benötigen den Namen nur zur Identifizierung des Runbooks in Azure Automation. <br> Es empfiehlt sich, den Webhook entsprechend dem Client zu benennen, der ihn verwenden wird. |
| URL |Die URL des Webhooks ist die eindeutige Adresse, die ein Client mit einer HTTP POST-Anforderung aufruft, um das mit dem Webhook verknüpfte Runbook zu starten. Sie wird beim Erstellen des Webhooks automatisch generiert. Sie können keine benutzerdefinierte URL angeben. <br> <br> Die URL enthält ein Sicherheitstoken, das es ermöglicht, dass das Runbook ohne weitere Authentifizierung von einem Drittanbietersystem aufgerufen werden kann. Daher sollte sie wie ein Kennwort behandelt werden. Aus Sicherheitsgründen können Sie die URL im Azure-Portal nur zu dem Zeitpunkt anzeigen, zu dem der Webhook erstellt wird. Sie sollten die URL zur späteren Verwendung an einem sicheren Ort speichern. |
| Ablaufdatum |Ebenso wie ein Zertifikat verfügt jeder Webhook über ein Ablaufdatum, nach dem er nicht mehr verwendet werden kann. Dieses Ablaufdatum kann nach dem Erstellen des Webhooks geändert werden, solange der Webhook noch nicht abgelaufen ist. |
| Enabled |Ein Webhook ist bei Erstellung standardmäßig aktiviert. Wenn Sie "Deaktiviert" festlegen, kann er von keinem Client verwendet werden. Sie können die Eigenschaft **Aktiviert** beim Erstellen des Webhooks oder zu einem anderen Zeitpunkt nach der Erstellung festlegen. |

### <a name="parameters"></a>Parameter

Ein Webhook kann Werte für Runbookparameter definieren, die verwendet werden, wenn das Runbook von diesem Webhook gestartet wird. Der Webhook muss Werte für alle obligatorischen Parameter des Runbooks einschließen und kann Werte für optionale Parameter einschließen. Ein Parameterwert, der für ein Webhook konfiguriert wurde, kann auch nach der Erstellung des Webhooks geändert werden. Mehrere mit einem einzelnen Runbook verknüpften Webhooks können verschiedene Parameterwerte verwenden.

Wenn ein Client ein Runbook mithilfe eines Webhooks startet, können die im Webhook definierten Parameterwerte nicht überschrieben werden. Um Daten von Client zu empfangen, kann das Runbook einen einzelnen Parameter mit dem Namen **$WebhookData** akzeptieren. Dieser Parameter hat einen Typ [object], der Daten enthält, die der Client in die POST-Anforderung einschließt.

![WebhookData-Eigenschaften](media/automation-webhooks/webhook-data-properties.png)

Das **$WebhookData**-Objekt weist die folgenden Eigenschaften auf:

| Eigenschaft | BESCHREIBUNG |
|:--- |:--- |
| WebhookName |Der Name des Webhooks. |
| RequestHeader |Die Hashtabelle mit den Headern der eingehenden POST-Anforderung |
| RequestBody |Der Header der eingehenden POST-Anforderung. Hiermit werden sämtliche Formatierungen beibehalten, z.B. Zeichenfolgenformat, JSON, XML oder formularcodierte Daten. Das Runbook muss so verfasst werden, dass es mit dem erwarteten Datenformat funktioniert. |

Zur Unterstützung des Parameters **$WebhookData** ist keine Konfiguration des Webhooks erforderlich, und das Runbook muss ihn nicht akzeptieren. Wenn das Runbook den Parameter nicht definiert, werden alle vom Client gesendeten Details der Anforderung ignoriert.

Wenn Sie beim Erstellen des Webhooks einen Wert für „$WebhookData“ angeben, wird dieser Wert beim Starten des Runbooks durch den Webhook mit den Daten aus der POST-Anforderung des Clients überschrieben, selbst wenn der Anforderungstext des Clients keine Daten enthält. Wenn Sie ein Runbook, das einen „$WebhookData“-Wert enthält, mit einer anderen Methode als einem Webhook starten, können Sie einen Wert für „$WebhookData“ bereitstellen, der vom Runbook erkannt wird. Bei diesem Wert sollte es sich um ein Objekt mit den gleichen [Eigenschaften](#details-of-a-webhook) wie „$WebhookData“ handeln, damit das Runbook damit ordnungsgemäß funktioniert, als würde es mit WebhookData arbeiten, die über einen Webhook übergeben werden.

Wenn Sie beispielsweise das folgende Runbook aus dem Azure-Portal starten und WebhookData als Testbeispiele übergeben möchten, sollten diese in der Benutzeroberfläche als JSON übergeben werden, da WebhookData ein Objekt ist.

![WebhookData-Parameter von der Benutzeroberfläche](media/automation-webhooks/WebhookData-parameter-from-UI.png)

Angenommen, dass für das folgende Runbook Eigenschaften mit folgenden Werten für den WebhookData-Parameter vorliegen:

* WebhookName: *MyWebhook*
* RequestBody: *[{'ResourceGroup': 'myResourceGroup','Name': 'vm01'},{'ResourceGroup': 'myResourceGroup','Name': 'vm02'}]*

Dann würden Sie den folgenden JSON-Wert in der Benutzeroberfläche für den WebhookData-Parameter übergeben: Das folgende Beispiel mit Wagenrücklauf und Zeilenumbruchzeichen entspricht dem vom Webhook übergebenen Format.

```json
{"WebhookName":"mywebhook","RequestBody":"[\r\n {\r\n \"ResourceGroup\": \"vm01\",\r\n \"Name\": \"vm01\"\r\n },\r\n {\r\n \"ResourceGroup\": \"vm02\",\r\n \"Name\": \"vm02\"\r\n }\r\n]"}
```

![WebhookData-Startparameter von der Benutzeroberfläche](media/automation-webhooks/Start-WebhookData-parameter-from-UI.png)

> [!NOTE]
> Die Werte aller Eingabeparameter werden mit dem Runbookauftrag protokolliert. Dies bedeutet, dass alle vom Client in der Webhookanforderung bereitgestellten Eingaben protokolliert werden und jedem Benutzer mit Zugriff auf den Automatisierungsauftrag zur Verfügung stehen.  Aus diesem Grund sollten Sie sorgfältig überlegen, welche vertraulichen Daten Sie in Webhookaufrufe einschließen.

## <a name="security"></a>Sicherheit

Die Sicherheit eines Webhooks beruht auf dem Datenschutz seiner URL, die ein Sicherheitstoken enthält, mit dem es aufgerufen werden kann. Azure Automation führt keinerlei Authentifizierung der Anforderung durch, sofern diese über die richtige URL erfolgt. Aus diesem Grund sollten Webhooks nicht für Runbooks verwendet werden, die sehr vertrauliche Funktionen ausführen, ohne eine alternative Möglichkeit zur Überprüfung der Anforderung zu verwenden.

Sie können Logik in das Runbook integrieren, um zu ermitteln, ob es von einem Webhook aufgerufen wurde, indem Sie die Eigenschaft **WebhookName** des Parameters "$WebhookData" aktivieren. Das Runbook kann eine weitere Überprüfung durchführen, indem es in den Eigenschaften **RequestHeader** oder **RequestBody** nach bestimmten Informationen sucht.

Eine andere Strategie besteht darin, dass das Runbook eine externe Bedingung überprüft, wenn es eine Webhookanforderung empfängt. Betrachten Sie z. B. ein Runbook, das von GitHub aufgerufen wird, sobald ein neuer Commit für ein GitHub-Repository vorhanden ist. Das Runbook kann eine Verbindung mit GitHub herstellen, um zu überprüfen, ob ein neuer Commit durchgeführt wurde, bevor es mit der Ausführung fortfährt.

## <a name="creating-a-webhook"></a>Erstellen eines Webhooks

Gehen Sie wie folgt vor, um einen neuen Webhook zu erstellen, der mit einem Runbook im Azure-Portal verknüpft ist

1. Klicken Sie im Azure-Portal auf der Seite **Runbooks** auf das Runbook, das vom Webhook gestartet werden soll, um die Detailseite des Runbooks anzuzeigen. Vergewissern Sie sich, dass als **Status** des Runbooks **Veröffentlicht** angezeigt wird.
2. Klicken Sie oben auf der Seite auf **Webhook**, um die Seite **Webhook hinzufügen** zu öffnen.
3. Klicken Sie auf **Neuen Webhook erstellen**, um die Seite **Webhook erstellen** zu öffnen.
4. Geben Sie einen **Namen** und ein **Ablaufdatum** für den Webhook an, und legen Sie fest, ob er aktiviert werden soll. Weitere Informationen zu diesen Eigenschaften finden Sie unter [Details zu einem Webhook](#details-of-a-webhook) .
5. Klicken Sie auf das Symbol zum Kopieren, und drücken Sie STRG+C, um die URL des Webhooks zu kopieren. Speichern Sie die URL an einem sicheren Ort. **Nachdem Sie den Webhook erstellt haben, können Sie die URL nicht erneut abrufen.**

   ![Webhook-URL](media/automation-webhooks/copy-webhook-url.png)

1. Klicken Sie auf **Parameter** , um Werte für die Runbookparameter bereitzustellen. Wenn das Runbook über obligatorische Parameter verfügt, können Sie den Webhook nur erstellen, wenn die entsprechenden Werte bereitgestellt wurden.
1. Klicken Sie auf **Erstellen** , um das Projekt zu erstellen.

## <a name="using-a-webhook"></a>Verwenden eines Webhooks

Nachdem ein Webhook erstellt wurde, muss Ihre Clientanwendung zum Verwenden des Webhooks eine HTTP POST-Anforderung mit der URL für den Webhook ausgeben. Die Syntax des Webhooks weist folgendes Format auf:

```http
http://<Webhook Server>/token?=<Token Value>
```

Der Client empfängt einen der folgenden Rückgabecodes als Antwort auf die POST-Anforderung.

| Code | Text | BESCHREIBUNG |
|:--- |:--- |:--- |
| 202 |Zulässig |Die Anforderung wurde akzeptiert, und das Runbook wurde erfolgreich in die Warteschlange gestellt. |
| 400 |Ungültige Anforderung |Die Anforderung wurde aus einem der folgenden Gründe nicht akzeptiert: <ul> <li>Der Webhook ist abgelaufen.</li> <li>Der Webhook ist deaktiviert.</li> <li>Das Token in der URL ist ungültig.</li>  </ul> |
| 404 |Nicht gefunden |Die Anforderung wurde aus einem der folgenden Gründe nicht akzeptiert: <ul> <li>Der Webhook wurde nicht gefunden.</li> <li>Das Runbook wurde nicht gefunden.</li> <li>Das Konto wurde nicht gefunden.</li>  </ul> |
| 500 |Interner Serverfehler |Die URL ist gültig, es ist jedoch ein Fehler aufgetreten. Senden Sie die Anforderung erneut. |

Wenn die Anforderung erfolgreich ist, enthält die Antwort des Webhooks wie im Folgenden dargestellt die Auftrags-ID im JSON-Format. Die Antwort enthält eine einzelne Auftrags-ID, wobei das JSON-Format Potenzial für künftige Verbesserungen bietet.

```json
{"JobIds":["<JobId>"]}
```

Der Client kann weder die Bestätigung, dass ein Runbookauftrag abgeschlossen wurde, noch den Abschlussstatus aus einem Webhook abrufen. Diese Informationen können mithilfe der Auftrags-ID unter Verwendung einer anderen Methode ermittelt werden, z.B. mit [Windows PowerShell](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationjob) oder der [Azure Automation-API](/rest/api/automation/job).

## <a name="renew-webhook"></a>Verlängern eines Webhooks

Ein Webhook ist nach der Erstellung ein Jahr lang gültig. Danach läuft die Gültigkeit des Webhooks automatisch ab. Ein abgelaufener Webhook kann nicht reaktiviert werden. Er muss entfernt und neu erstellt werden. Vor Erreichen des Ablauftermins kann der Webhook verlängert werden.

Navigieren Sie zum Verlängern eines Webhooks zu dem Runbook, das den Webhook enthält. Wählen Sie unter **Ressourcen** die Option **Webhooks** aus. Klicken Sie auf den Webhook, den Sie verlängern möchten. Daraufhin wird die Seite **Webhook** geöffnet.  Wählen Sie einen neuen Ablauftermin (Datum und Uhrzeit) aus, und klicken Sie auf **Speichern**.

## <a name="sample-runbook"></a>Beispiel-Runbook

Das folgende Beispiel-Runbook akzeptiert Webhook-Daten und startet die im Anforderungstext angegebenen virtuellen Computer. Wenn Sie dieses Runbook testen möchten, klicken Sie in Ihrem Automation-Konto unter **Runbooks** auf **+ Runbook hinzufügen**. Sollten Sie nicht wissen, wie man ein Runbook erstellt, finden Sie unter [Erstellen eines Azure Automation-Runbooks](automation-quickstart-create-runbook.md) weitere Informationen.

```powershell
param
(
    [Parameter (Mandatory = $false)]
    [object] $WebhookData
)



# If runbook was called from Webhook, WebhookData will not be null.
if ($WebhookData) {

    # Check header for message to validate request
    if ($WebhookData.RequestHeader.message -eq 'StartedbyContoso')
    {
        Write-Output "Header has required information"}
    else
    {
        Write-Output "Header missing required information";
        exit;
    }

    # Retrieve VM's from Webhook request body
    $vms = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

    # Authenticate to Azure by using the service principal and certificate. Then, set the subscription.

    Write-Output "Authenticating to Azure with service principal and certificate"
    $ConnectionAssetName = "AzureRunAsConnection"
    Write-Output "Get connection asset: $ConnectionAssetName"

    $Conn = Get-AutomationConnection -Name $ConnectionAssetName
            if ($Conn -eq $null)
            {
                throw "Could not retrieve connection asset: $ConnectionAssetName. Check that this asset exists in the Automation account."
            }
            Write-Output "Authenticating to Azure with service principal." 
            Add-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Write-Output

        # Start each virtual machine
        foreach ($vm in $vms)
        {
            $vmName = $vm.Name
            Write-Output "Starting $vmName"
            Start-AzureRMVM -Name $vm.Name -ResourceGroup $vm.ResourceGroup
        }
}
else {
    # Error
    write-Error "This runbook is meant to be started from an Azure alert webhook only."
}
```

## <a name="test-the-example"></a>Testen des Beispiels

Im folgenden Beispiel wird Windows PowerShell verwendet, um ein Runbook mit einem Webhook zu starten. In jeder Sprache, in der eine HTTP-Anforderung gesendet werden kann, ist auch die Verwendung eines Webhooks möglich; Windows PowerShell dient hier lediglich als Beispiel.

Das Runbook erwartet im Anforderungstext eine Liste von virtuellen Computern, die in JSON formatiert ist. Das Runbook überprüft auch, ob die Header eine definierte Nachricht enthalten, um sicherzustellen, dass der Webhookaufrufer gültig ist.

```azurepowershell-interactive
$uri = "<webHook Uri>"

$vms  = @(
            @{ Name="vm01";ResourceGroup="vm01"},
            @{ Name="vm02";ResourceGroup="vm02"}
        )
$body = ConvertTo-Json -InputObject $vms
$header = @{ message="StartedbyContoso"}
$response = Invoke-WebRequest -Method Post -Uri $uri -Body $body -Headers $header
$jobid = (ConvertFrom-Json ($response.Content)).jobids[0]
```

Das folgende Beispiel zeigt den Anforderungstext, der dem Runbook in der **RequestBody**-Eigenschaft von **WebhookData** zur Verfügung steht. Der Text ist als JSON formatiert, da dieses Format im Anforderungstext enthalten war.

```json
[
    {
        "Name":  "vm01",
        "ResourceGroup":  "myResourceGroup"
    },
    {
        "Name":  "vm02",
        "ResourceGroup":  "myResourceGroup"
    }
]
```

Die folgende Abbildung zeigt die Anforderung, die von Windows PowerShell gesendet wurde, sowie die daraus resultierende Antwort. Die Auftrags-ID wurde aus der Antwort extrahiert und in eine Zeichenfolge konvertiert.

![Schaltfläche "Webhooks"](media/automation-webhooks/webhook-request-response.png)

## <a name="next-steps"></a>Nächste Schritte

* Informationen zur Verwendung von Azure Automation für die Reaktion auf Azure-Warnungen finden Sie unter [Verwenden einer Warnung zum Auslösen eines Azure Automation-Runbooks](automation-create-alert-triggered-runbook.md).

