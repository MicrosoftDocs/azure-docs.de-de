---
title: Runbookeingabeparameter
description: Runbookeingabeparameter erhöhen die Flexibilität von Runbooks, weil Sie mit ihnen Daten an ein Runbook übergeben können, wenn es gestartet wird. In diesem Artikel werden verschiedene Szenarien beschrieben, in denen Eingabeparameter in Runbooks verwendet werden.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 02/14/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 5f190d60a059108b9763f35e2ee8cf99ae77b694
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60499959"
---
# <a name="runbook-input-parameters"></a>Runbookeingabeparameter

Runbookeingabeparameter erhöhen die Flexibilität von Runbooks, weil Sie hiermit beim Startvorgang Daten dafür übergeben können. Mit den Parametern können die Runbookaktionen auf bestimmte Szenarien und Umgebungen abgestimmt werden. In diesem Artikel lernen Sie verschiedene Szenarien kennen, in denen Eingabeparameter in Runbooks verwendet werden.

## <a name="configure-input-parameters"></a>Konfigurieren von Eingabeparametern

Eingabeparameter können in PowerShell-, PowerShell-Workflow, Python und grafischen Runbooks konfiguriert werden. Ein Runbook kann mehrere Parameter mit unterschiedlichen Datentypen oder gar keine Parameter aufweisen. Eingabeparameter können erforderlich oder optional sein, und Sie können einen Standardwert für optionale Parameter verwenden. Sie weisen den Eingabeparametern für ein Runbook Werte zu, wenn Sie es mit einer der verfügbaren Methoden starten. Zu diesen Methoden gehört das Starten eines Runbooks über das Azure-Portal, einen Webdienst oder PowerShell. Sie haben auch die Möglichkeit, ein Runbook als ein untergeordnetes Runbook zu starten, das inline in einem anderen Runbook aufgerufen werden kann.

## <a name="configure-input-parameters-in-powershell-runbooks"></a>Konfigurieren von Eingabeparametern in PowerShell-Runbooks

PowerShell- und PowerShell-Workflow-Runbooks in Azure Automation unterstützen Eingabeparameter, die mit den folgenden Attributen definiert werden:  

| **Eigenschaft** | **Beschreibung** |
|:--- |:--- |
| `Type` |Erforderlich. Der für den Parameterwert erwartete Datentyp. Jeder .NET-Typ ist valide. |
| `Name` |Erforderlich. Der Name des Parameters. Dieser muss innerhalb des Runbooks eindeutig sein und kann nur Buchstaben, Zahlen oder Unterstriche enthalten. Er muss mit einem Buchstaben beginnen. |
| `Mandatory` |Optional. Gibt an, ob ein Wert für den Parameter bereitgestellt werden muss. Wenn Sie hier **\$true** festlegen, muss beim Start des Runbooks ein Wert angegeben werden. Wenn Sie hier **\$false** festlegen, ist das Angeben eines Werts optional. |
| `Default value` |Optional. Gibt einen Wert an, der für den Parameter verwendet wird, wenn beim Start des Runbooks kein Wert übergeben wird. Ein Standardwert kann für jeden Parameter festgelegt werden und macht den Parameter unabhängig von der Einstellung „Erforderlich“ optional. |

Windows PowerShell unterstützt neben den hier genannten Eingabeparametern auch andere wie beispielsweise Validierung, Alias und Parametersätze. Azure Automation hingegen unterstützt zurzeit lediglich die oben genannten Eingabeparameter.

Eine Parameterdefinition in PowerShell-Workflow-Runbooks hat die folgende allgemeine Form, wobei mehrere Parameter durch Kommas getrennt werden.

```powershell
Param
(
  [Parameter (Mandatory= $true/$false)]
  [Type] $Name1 = <Default value>,

  [Parameter (Mandatory= $true/$false)]
  [Type] $Name2 = <Default value>
)
```

> [!NOTE]
> Wenn Sie bei der Definition der Parameter nicht das Attribut „ **Erforderlich** ” angeben, gilt der Parameter standardmäßig als optional. Wenn Sie für einen Parameter in PowerShell-Workflow-Runbooks einen Standardwert festlegen, wird dieser Parameter von PowerShell als optional behandelt, unabhängig vom Wert des Attributs **Erforderlich**.

Als Beispiel konfigurieren wir die Eingabeparameter für ein PowerShell-Workflow-Runbook, das Details virtueller Computer ausgibt - entweder eines einzelnen virtuellen Computers oder aller virtuellen Computer in einer Ressourcengruppe. Dieses Runbook hat, wie im folgenden Screenshot gezeigt, zwei Parameter: den Namen des virtuellen Computers und den Namen der Ressourcengruppe.

![Automation: PowerShell-Workflow](media/automation-runbook-input-parameters/automation-01-powershellworkflow.png)

In dieser Parameterdefinition sind **\$VMName** und **\$resourceGroupName** einfache Parameter vom Typ „Zeichenfolge“. PowerShell- und PowerShell-Workflow-Runbooks unterstützen jedoch alle einfachen Typen und komplexe Typen wie **Objekt** oder **PSCredential** als Eingabeparameter.

Wenn Ihr Runbook einen Eingabeparameter vom Typ „Objekt“ aufweist, verwenden Sie eine PowerShell-Hashtabelle mit (Name, Wert)-Paaren, um einen Wert zu übergeben. Ihr Runbook weist beispielsweise den folgenden Parameter auf:

```powershell
[Parameter (Mandatory = $true)]
[object] $FullName
```

Dann können Sie den folgenden Wert an den Parameter übergeben:

```powershell
@{"FirstName"="Joe";"MiddleName"="Bob";"LastName"="Smith"}
```

> [!NOTE]
> Wenn Sie keinen Wert an einen optionalen Parameter vom Typ `[String]` übergeben, der einen _Standardwert_ von `\$null` hat, dann ist der Wert des Parameters eine _leere Zeichenfolge_, **nicht** `\$null`.

## <a name="configure-input-parameters-in-graphical-runbooks"></a>Konfigurieren von Eingabeparametern in grafischen Runbooks

Zum Konfigurieren eines [grafischen Runbooks](automation-first-runbook-graphical.md) mit Eingabeparametern erstellen wir ein grafisches Runbook, das Details zu virtuellen Computern – entweder eines einzelnen oder aller virtuellen Computer innerhalb einer Ressourcengruppe – ausgibt. Das Konfigurieren eines Runbooks erfodert, wie unten beschrieben, zwei Hauptaktivitäten.

[**Authentifizieren Sie Runbooks mit der Azure-Option „Ausführendes Konto“** ](automation-sec-configure-azure-runas-account.md) für die Authentifizierung bei Azure.

Rufen Sie mit [**Get-AzureRmVm**](/powershell/module/azurerm.compute/get-azurermvm) die Eigenschaften eines virtuellen Computers ab.

Sie können mit der Aktivität [**Write-Output**](/powershell/module/microsoft.powershell.utility/write-output) die Namen von virtuellen Computern abrufen. Die Aktivität **Get-AzureRmVm** akzeptiert zwei Parameter: den **Namen des virtuellen Computers** und den **Ressourcengruppennamen**. Da diese Parameter bei jedem Start des Runbooks unterschiedliche Werte benötigen könnten, können Sie Ihrem Runbook Eingabeparameter hinzufügen. Mit diesen Schritten fügen Sie Eingabeparameter hinzu:

1. Wählen Sie auf dem Blatt **Runbooks** das grafische Runbook aus, und klicken Sie dann auf [**Bearbeiten**](automation-graphical-authoring-intro.md).
2. Klicken Sie im Runbook-Editor auf **Eingabe und Ausgabe**, um das Blatt **Eingabe- und Ausgabe** zu öffnen.

   ![Automation: Grafisches Runbook](media/automation-runbook-input-parameters/automation-02-graphical-runbok-editor.png)

3. Das Blatt „ **Eingabe und Ausgabe** ” zeigt eine Liste der für das Runbook definierten Eingabeparameter an. In diesem Blatt können Sie entweder einen neuen Eingabeparameter hinzufügen oder die Konfiguration eines bestehenden Eingabeparameters bearbeiten. Um einen neuen Runbook-Parameter hinzuzufügen, klicken Sie auf **Eingabe hinzufügen** und öffnen damit das Blatt **Runbook-Eingabeparameter**. Hier können Sie die folgenden Parameter konfigurieren:

   | **Eigenschaft** | **Beschreibung** |
   |:--- |:--- |
   | `Name` |Erforderlich. Der Name des Parameters. Dieser muss innerhalb des Runbooks eindeutig sein und kann nur Buchstaben, Zahlen oder Unterstriche enthalten. Er muss mit einem Buchstaben beginnen. |
   | `Description` |Optional. Beschreibung zum Zweck des Eingabeparameters. |
   | `Type` |Optional. Der für den Parameterwert erwartete Datentyp. Die Parametertypen **Zeichenfolge**, **Int32**, **Int64**, **Dezimal**, **Boolesch**, **Datum/Uhrzeit** und **Objekt** werden unterstützt. Wenn kein Datentyp ausgewählt wird, wird standardmäßig „ **Zeichenfolge**” verwendet. |
   | `Mandatory` |Optional. Gibt an, ob ein Wert für den Parameter bereitgestellt werden muss. Wenn Sie **ja**wählen, muss beim Start des Runbooks ein Wert angegeben werden. Wenn Sie **nein**wählen, ist kein Wert erforderlich, wenn das Runbook gestartet wird, und ein Standardwert könnte festgelegt werden. |
   | `Default Value` |Optional. Gibt einen Wert an, der für den Parameter verwendet wird, wenn beim Start des Runbooks kein Wert übergeben wird. Ein Standardwert kann für optionale Parameter vergeben werden. Wählen Sie „ **Benutzerdefiniert**”, um einen Standardwert vorzugeben. Dieser Wert wird verwendet, es sei denn, ein anderer Wert wird bereitgestellt, wenn das Runbook gestartet wird. Wählen Sie **Keiner** , wenn Sie keinen Standardwert angeben möchten. |

    ![Neue Eingabe hinzufügen](media/automation-runbook-input-parameters/automation-runbook-input-parameter-new.png)
4. Erstellen Sie zwei Parameter mit den folgenden Eigenschaften, die von der Aktivität **Get-AzureRmVm** verwendet werden:

   * **Parameter1:**
     * Name: VMName
     * Typ: Zeichenfolge
     * Erforderlich: Nein
   * **Parameter2:**
     * Name: resourceGroupName
     * Typ: Zeichenfolge
     * Erforderlich: Nein
     * Standardwert: Benutzerdefiniert
     * Benutzerdefinierter Standardwert: \<Name der Ressourcengruppe, die die virtuellen Computer enthält\>

5. Wenn Sie die Parameter hinzugefügt haben, klicken Sie auf **OK**. Sie können die Parameter jetzt auf der Seite **Eingabe und Ausgabe** anzeigen. Klicken Sie erneut auf **OK**, dann auf **Speichern**, und **Veröffentlichen** Sie Ihr Runbook.

## <a name="configure-input-parameters-in-python-runbooks"></a>Konfigurieren von Eingabeparametern in Python-Runbooks

Im Gegensatz zu PowerShell, PowerShell-Workflow und grafischen Runbooks akzeptieren Python-Runbooks keine benannten Parameter.
Alle Eingabeparameter werden als Array von Argumentwerten analysiert.
Sie greifen auf das Array zu, indem Sie das `sys`-Modul in Ihr Python-Skript importieren und dann das `sys.argv`-Array verwenden.
Sie müssen beachten, dass das erste Element des Arrays (`sys.argv[0]`) der Name des Skripts ist. Der erste tatsächliche Eingabeparameter lautet daher `sys.argv[1]`.

Ein Beispiel für die Verwendung von Eingabeparametern in einem Python-Runbook finden Sie unter [Mein erstes Python-Runbook in Azure Automation](automation-first-runbook-textual-python2.md).

## <a name="assign-values-to-input-parameters-in-runbooks"></a>Eingabeparametern in Runbooks Werte zuweisen

In den folgenden Szenarien können Sie Werte an die Eingabeparameter in Runbooks übergeben:

### <a name="start-a-runbook-and-assign-parameters"></a>Starten eines Runbooks und Zuweisen von Parametern

Ein Runbook kann auf viele verschiedene Arten gestartet werden: Über das Azure-Portal, mit einem Webhook, mit PowerShell-Cmdlets, mit der REST-API oder mit dem SDK. Unten werden verschiedene Methoden beschrieben, um ein Runbook zu starten und Parameter zuzuweisen.

#### <a name="start-a-published-runbook-by-using-the-azure-portal-and-assign-parameters"></a>Starten eines veröffentlichten Runbooks über das Azure-Portal und Zuweisen von Parametern

Wenn Sie das [Runbook starten](start-runbooks.md#start-a-runbook-with-the-azure-portal), wird das Blatt **Runbook starten** geöffnet, und Sie können Werte für die von Ihnen erstellten Parameter eingeben.

![Starten unter Verwendung des Portals](media/automation-runbook-input-parameters/automation-04-startrunbookusingportal.png)

In der Beschriftung unter dem Eingabefeld können Sie sehen, welche Attribute für die Parameter festgelegt wurden. Diese können sein: „Erforderlich“ oder „Optional“, „Typ“ und „Standardwert“. In der Hilfesprechblase neben dem Namen des Parameters sehen Sie alle wichtige Informationen, die Sie benötigen, um Entscheidungen über Parametereingabewerte zu treffen. Dies schließt die Information ein, ob es sich um einen erforderlichen Parameter handelt. Sie zeigen auch den Typ,  den Standardwert (sofern vorhanden) und andere hilfreiche Hinweise an.

> [!NOTE]
> Parameter des Typs „Zeichenfolge“ unterstützen **leere** Zeichenfolgenwerte.  Die Eingabe einer **[leeren Zeichenfolge]** in das Feld für den Eingabeparameter bewirkt, dass eine leere Zeichenfolge an den Parameter übergeben wird. Außerdem unterstützen Parameter vom Typ „Zeichenfolge“ nicht die Übergabe von **Null** -Werten. Wenn Sie keinen Wert an den Zeichenfolge-Parameter übergeben, interpretiert PowerShell dies als NULL.

#### <a name="start-a-published-runbook-by-using-powershell-cmdlets-and-assign-parameters"></a>Starten eines veröffentlichten Runbooks durch PowerShell -Cmdlets und Zuweisen von Parametern

* **Azure Resource Manager-Cmdlets**: Sie können ein Automation-Runbook, das in einer Ressourcengruppe erstellt wurde, mit [Start-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/start-azurermautomationrunbook) starten.
  
  **Beispiel:**

  ```powershell
  $params = @{"VMName"="WSVMClassic";"resourceGroupeName"="WSVMClassicSG"}
  
  Start-AzureRmAutomationRunbook -AutomationAccountName "TestAutomation" -Name "Get-AzureVMGraphical" –ResourceGroupName $resourceGroupName -Parameters $params
  ```

* **Cmdlets für das klassische Azure-Bereitstellungsmodell:** Sie können ein Automation-Runbook, das in einer Standardressourcengruppe erstellt wurde, mit [Start-AzureAutomationRunbook](/powershell/module/servicemanagement/azure/start-azureautomationrunbook) starten.
  
  **Beispiel:**

  ```powershell
  $params = @{"VMName"="WSVMClassic"; "ServiceName"="WSVMClassicSG"}
  
  Start-AzureAutomationRunbook -AutomationAccountName "TestAutomation" -Name "Get-AzureVMGraphical" -Parameters $params
  ```

> [!NOTE]
> Wenn Sie ein Runbook mit PowerShell-Cmdlets starten, wird der Standardparameter **MicrosoftApplicationManagementStartedBy** mit dem Wert **PowerShell** erstellt. Sie können diesen Parameter auf der Seite **Auftragsdetails** anzeigen.  

#### <a name="start-a-runbook-by-using-an-sdk-and-assign-parameters"></a>Starten eines Runbooks mit dem SDK und Zuweisen von Parametern

* **Azure Resource Manager-Methode:** Sie können ein Runbook mit dem SDK einer Programmiersprache starten. Unten befindet sich ein C#-Codeausschnitt um ein Runbook in Ihrem Automation-Konto zu starten. Sie können den kompletten Code in unserer [GitHub-Repository](https://github.com/Azure/azure-sdk-for-net/blob/master/src/ResourceManagement/Automation/Automation.Tests/TestSupport/AutomationTestBase.cs)ansehen.  

  ```csharp
   public Job StartRunbook(string runbookName, IDictionary<string, string> parameters = null)
      {
        var response = AutomationClient.Jobs.Create(resourceGroupName, automationAccount, new JobCreateParameters
         {
            Properties = new JobCreateProperties
             {
                Runbook = new RunbookAssociationProperty
                 {
                   Name = runbookName
                 },
                   Parameters = parameters
             }
         });
      return response.Job;
      }
  ```

* **Methode für das klassische Azure-Bereitstellungsmodell:** Sie können ein Runbook mit dem SDK einer Programmiersprache starten. Unten befindet sich ein C#-Codeausschnitt um ein Runbook in Ihrem Automation-Konto zu starten. Sie können den kompletten Code in unserer [GitHub-Repository](https://github.com/Azure/azure-sdk-for-net/blob/master/src/ServiceManagement/Automation/Automation.Tests/TestSupport/AutomationTestBase.cs)ansehen.

  ```csharp
  public Job StartRunbook(string runbookName, IDictionary<string, string> parameters = null)
    {
      var response = AutomationClient.Jobs.Create(automationAccount, new JobCreateParameters
    {
      Properties = new JobCreateProperties
         {
           Runbook = new RunbookAssociationProperty
         {
           Name = runbookName
              },
                Parameters = parameters
              }
       });
      return response.Job;
    }
  ```

  Zum Starten dieser Methode erstellen Sie ein Wörterbuch, um die Runbook-Parameter **VMName** und **resourceGroupName** und deren Werte zu speichern. Starten Sie jetzt das Runbook. Unten befindet sich ein C#-Codeausschnitt um die oben definierte Methode aufzurufen.

  ```csharp
  IDictionary<string, string> RunbookParameters = new Dictionary<string, string>();
  
  // Add parameters to the dictionary.
  RunbookParameters.Add("VMName", "WSVMClassic");
  RunbookParameters.Add("resourceGroupName", "WSSC1");
  
  //Call the StartRunbook method with parameters
  StartRunbook("Get-AzureVMGraphical", RunbookParameters);
  ```

#### <a name="start-a-runbook-by-using-the-rest-api-and-assign-parameters"></a>Starten eines Runbooks mit REST-API und Zuweisen von Parametern

Ein Runbookauftrag kann mit der REST-API von Azure Automation erstellt und gestartet werden. Hierzu wird die **PUT**-Methode mit dem folgenden Anforderungs-URI verwendet: `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Automation/automationAccounts/{automationAccountName}/jobs/{jobName}?api-version=2017-05-15-preview`.


Ersetzen Sie in der Anforderungs-URI die folgenden Parameter:

* **subscriptionId:** Die ID Ihres Azure-Abonnements.  
* **resourceGroupName:** Der Name der Ressourcengruppe für das Automation-Konto.
* **automationAccountName:** Der Name Ihres Automation-Kontos, das im angegebenen Clouddienst gehostet wird.  
* **jobName:** Die GUID für den Auftrag. Die GUID in PowerShell kann mit dem Befehl **[GUID]::NewGuid().ToString()** erstellt werden.

Um Parameter an den Runbookauftrag zu übergeben, verwenden Sie den Anforderungstext. Die folgenden zwei Eigenschaften werden im JSON-Format benötigt:

* **Runbookname:** Erforderlich. Der Name des Runbooks für den zu startenden Auftrag.  
* **Runbookparameter:** Optional. Ein Wörterbuch der Parameterliste im Format (Name, Wert), wobei der Name vom Typ „Zeichenfolge“ sein sollte, und als Wert jeder gültige JSON-Wert infrage kommt.

Wenn Sie das zuvor erstellte Runbook **Get-AzureVMTextual** mit **VMName** und **resourceGroupName** als Parameter starten möchten, verwenden Sie das folgende JSON-Format für den Anforderungstext.

   ```json
    {
      "properties":{
        "runbook":{
        "name":"Get-AzureVMTextual"},
      "parameters":{
         "VMName":"WindowsVM",
         "resourceGroupName":"ContosoSales"}
        }
    }
   ```

Wenn der Auftrag erfolgreich erstellt wurde, erhalten Sie als Antwort den HTTP-Statuscode 201. Weitere Informationen zu Antwortheadern und Antworttext finden Sie im Artikel zum [Erstellen eines Runbookauftrags mit der REST-API](/rest/api/automation/job/create).

### <a name="test-a-runbook-and-assign-parameters"></a>Testen eines Runbooks und Zuweisen von Parametern

Wenn Sie mithilfe der Testoption [die Entwurfsversion Ihres Runbooks testen](automation-testing-runbook.md), wird das Blatt **Testen** geöffnet, und Sie können die Werte für Ihre erstellten Parameter konfigurieren.

![Testen und Zuweisen von Parametern](media/automation-runbook-input-parameters/automation-06-testandassignparameters.png)

### <a name="link-a-schedule-to-a-runbook-and-assign-parameters"></a>Verknüpfen eines Zeitplans mit einem Runbook und Zuweisen von Parametern

Sie können einen [Zeitplan mit Ihrem Runbook verknüpfen](automation-schedules.md) , damit Ihr Runbook zu einem bestimmten Zeitpunkt startet. Beim Erstellen des Zeitplans weisen Sie Eingabeparameter zu, und das Runbook verwendet diese Werte, wenn es vom Zeitplan gestartet wird. Sie können den Zeitplan erst speichern, wenn alle erforderlichen Parameterwerte bereitgestellt sind.

![Planen und Zuweisen von Parametern](media/automation-runbook-input-parameters/automation-07-scheduleandassignparameters.png)

### <a name="create-a-webhook-for-a-runbook-and-assign-parameters"></a>Erstellen eines Webhooks für ein Runbook, und Zuweisen von Parametern

Sie können einen [Webhook](automation-webhooks.md) für Ihr Runbook erstellen und Runbookeingabeparameter konfigurieren. Sie können den Webhook erst speichern, wenn alle erforderlichen Parameterwerte bereitgestellt sind.

![Erstellen eines Webhooks und Zuweisen von Parametern](media/automation-runbook-input-parameters/automation-08-createwebhookandassignparameters.png)

Wenn Sie einen Webhook verwenden, um ein Runbook auszuführen, wird der vordefinierte Eingabeparameter **[Webhookdata](automation-webhooks.md#details-of-a-webhook)** zusammen mit den von Ihnen definierten Eingabeparametern gesendet. Sie können auf den **WebhookData** -Parameter klicken, um ihn zu erweitern und weitere Details anzuzeigen.

![WebhookData-Parameter](media/automation-runbook-input-parameters/automation-09-webhook-data-parameters.png)

## <a name="pass-a-json-object-to-a-runbook"></a>Übergeben eines JSON-Objekts an ein Runbook

Es kann hilfreich sein, Daten, die Sie an ein Runbook übergeben möchten, in einer JSON-Datei zu speichern.
Sie können z.B. eine JSON-Datei erstellen, die alle Parameter enthält, die an ein Runbook übergeben werden sollen. Zu diesem Zweck müssen Sie den JSON-Code in eine Zeichenfolge und diese dann in ein PowerShell-Objekt konvertieren, bevor die Übergabe an das Runbook erfolgt.

In diesem Beispiel verfügen Sie über ein PowerShell-Skript. Mit dem Skript wird [Start-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/start-azurermautomationrunbook) aufgerufen, um ein PowerShell-Runbook zu starten, mit dem der Inhalt der JSON-Datei an das Runbook übergeben wird.
Das PowerShell-Runbook startet einen virtuellen Azure-Computer und ruft die Parameter für den virtuellen Computer aus der übergebenen JSON-Datei ab.

### <a name="create-the-json-file"></a>Erstellen der JSON-Datei

Geben Sie den folgenden Test in eine Textdatei ein, und speichern Sie diese als `test.json` auf dem lokalen Computer.

```json
{
   "VmName" : "TestVM",
   "ResourceGroup" : "AzureAutomationTest"
}
```

### <a name="create-the-runbook"></a>Erstellen des Runbooks

Erstellen Sie ein neues PowerShell-Runbook mit dem Namen „Test-Json“ in Azure Automation.
Weitere Informationen zum Erstellen eines PowerShell-Runbooks finden Sie unter [Mein erstes PowerShell-Runbook](automation-first-runbook-textual-powershell.md).

Damit die JSON-Daten akzeptiert werden, muss das Runbook ein Objekt als Eingabeparameter annehmen.

Das Runbook kann dann die in der JSON-Datei definierten Eigenschaften verwenden.

```powershell
Param(
     [parameter(Mandatory=$true)]
     [object]$json
)

# Connect to Azure account
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID `
    -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

# Convert object to actual JSON
$json = $json | ConvertFrom-Json

# Use the values from the JSON object as the parameters for your command
Start-AzureRmVM -Name $json.VMName -ResourceGroupName $json.ResourceGroup
```

Speichern und veröffentlichen Sie dieses Runbook in Ihrem Automation-Konto.

### <a name="call-the-runbook-from-powershell"></a>Aufrufen des Runbooks in PowerShell

Jetzt können Sie das Runbook auf dem lokalen Computer mithilfe von Azure PowerShell aufrufen.
Führen Sie die folgenden PowerShell-Befehle aus:

1. Melden Sie sich bei Azure an:

   ```powershell
   Connect-AzureRmAccount
   ```

   Sie werden zur Eingabe Ihrer Azure-Anmeldeinformationen aufgefordert.

   > [!IMPORTANT]
   > **Add-AzureRmAccount** ist jetzt ein Alias für **Connect-AzureRMAccount**. Sollte **Connect-AzureRMAccount** beim Durchsuchen der Bibliothekselemente nicht angezeigt werden, können Sie **Add-AzureRmAccount** verwenden oder die Module in Ihrem Automation-Konto aktualisieren.

1. Rufen Sie den Inhalt der JSON-Datei ab, und konvertieren Sie ihn in eine Zeichenfolge:

   ```powershell
   $json =  (Get-content -path 'JsonPath\test.json' -Raw) | Out-string
   ```

   `JsonPath` ist der Pfad, unter dem Sie die JSON-Datei gespeichert haben.

1. Konvertieren des Zeichenfolgeninhalts von `$json` in ein PowerShell-Objekt:

   ```powershell
   $JsonParams = @{"json"=$json}
   ```

1. Erstellen einer Hashtabelle der Parameter für `Start-AzureRmAutomationRunbook`:

   ```powershell
   $RBParams = @{
        AutomationAccountName = 'AATest'
        ResourceGroupName = 'RGTest'
        Name = 'Test-Json'
        Parameters = $JsonParams
   }
   ```

   Beachten Sie, dass Sie den Wert von `Parameters` auf das PowerShell-Objekt festlegen, in dem die Werte aus der JSON-Datei enthalten sind.
1. Starten des Runbooks

   ```powershell
   $job = Start-AzureRmAutomationRunbook @RBParams
   ```

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen über die verschiedenen Methoden zum Starten eines Runbooks finden Sie unter [Starten eines Runbooks](automation-starting-a-runbook.md).
* Informationen zum Bearbeiten eines Textrunbooks finden Sie unter [Bearbeiten von Textrunbooks](automation-edit-textual-runbook.md).
* Informationen zum Bearbeiten eines grafischen Runbooks finden Sie unter [Grafische Erstellung in Azure Automation](automation-graphical-authoring-intro.md).
