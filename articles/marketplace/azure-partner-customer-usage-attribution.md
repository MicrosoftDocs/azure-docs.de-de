---
title: Zuordnung der Nutzung durch Partner und Kunden des kommerziellen Marketplace
description: Hier erhalten Sie einen Überblick über die Nachverfolgung der Kundennutzung für Azure Marketplace-Lösungen.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: cpercy737
ms.author: camper
ms.date: 11/4/2020
ms.custom: devx-track-terraform
ms.openlocfilehash: 99e1e77a37afbdc1ed54767700574316ed03fae3
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99525244"
---
# <a name="commercial-marketplace-partner-and-customer-usage-attribution"></a>Zuordnung der Nutzung durch Partner und Kunden des kommerziellen Marketplace

Die Zuordnung der Nutzung durch Kunden ist eine Methode, um in Kundenabonnements ausgeführte Azure-Ressourcen, die für die Ausführung Ihrer Lösung bereitgestellt werden, Ihnen als Partner zuzuordnen. Die Bildung dieser Zuordnungen in internen Microsoft-Systemen sorgt für mehr Transparenz des Azure-Speicherbedarfs Ihrer Software. Wenn Sie diese Nachverfolgungsfunktion einsetzen, können Sie sich mit den Microsoft-Vertriebsteams abstimmen und Gutschriften für Microsoft-Partnerprogramme erhalten.

Sie können die Zuordnung über Azure Marketplace, das Schnellstartrepository, private GitHub-Repositorys und 1:1-Kundeninteraktionen herstellen, die eine permanente IP-Adresse erstellen (wie z. B. die Entwicklung einer App).

Die Zuordnung der Nutzung durch Kunden unterstützt drei Bereitstellungsmethoden:

- Azure Resource Manager-Vorlagen: Partner können Resource Manager-Vorlagen verwenden, um Azure-Dienste zum Ausführen der Software des Partners bereitzustellen. Partner können Resource Manager-Vorlagen erstellen, um die Infrastruktur und Konfiguration ihrer Azure-Lösung zu definieren. Mit einer Resource Manager-Vorlage können Sie und Ihre Kunden die Lösung während des gesamten Lebenszyklus bereitstellen. Sie können sicher sein, dass Ihre Ressourcen in einem konsistenten Zustand bereitgestellt werden.
- Azure Resource Manager-APIs: Partner können die Resource Manager-APIs direkt aufrufen, um eine Resource Manager-Vorlage bereitzustellen oder um API-Aufrufe für die direkte Bereitstellung von Azure-Diensten zu generieren.
- Terraform: Partner können Terraform verwenden, um eine Resource Manager-Vorlage oder die Azure-Dienste direkt bereitzustellen.

>[!IMPORTANT]
>- Die Zuordnung der Nutzung durch Kunden dient nicht dazu, die Arbeit von Systemintegratoren, Anbietern verwalteter Dienste oder von Tools nachzuverfolgen, die für das Bereitstellen und Verwalten von in Azure ausgeführter Software konzipiert sind.
>
>- Die Zuordnung der Nutzung durch Kunden gilt für neue Bereitstellungen und unterstützt NICHT das Tagging vorhandener, bereits bereitgestellter Ressourcen.
>
>- Die Zuordnung der Nutzung durch Kunden ist für [Azure-Anwendungsangebote](./create-new-azure-apps-offer.md) erforderlich, die in Azure Marketplace veröffentlicht werden.
>
>- Nicht alle Azure-Dienste sind mit der Zuordnung der Nutzung durch Kunden kompatibel. Bei Azure Kubernetes Services (AKS) und VM Scale Sets sind zum jetzigen Zeitpunkt Probleme bekannt, die zu einer unzureichenden Meldung der Nutzung führen.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="create-guids"></a>Erstellen von GUIDs

Eine GUID ist eine eindeutige Verweis-ID mit 32 Hexadezimalziffern. Zum Erstellen einer GUID für die Nachverfolgung sollten Sie einen GUID-Generator verwenden, beispielsweise über PowerShell.

```powershell
[guid]::NewGuid()
```

Es empfiehlt sich, für jedes Produkt eine eindeutige GUID für jedes Angebot und jeden Distributionskanal zu erstellen. Sie können festlegen, dass eine einzige GUID für die vielfältigen Verteilungskanäle des Produkts verwendet wird, wenn die Berichterstellung nicht aufgeteilt werden soll.

Wenn Sie ein Produkt mithilfe einer Vorlage bereitstellen und das Produkt sowohl im Azure Marketplace als auch auf GitHub verfügbar ist, können Sie zwei unterschiedliche GUIDs erstellen und registrieren:

- Produkt A im Azure Marketplace
- Produkt A auf GitHub

Die Berichterstellung erfolgt über die Microsoft Partner Network-ID und die GUID.

Sie können die Nutzung auch genauer nachverfolgen, indem Sie zusätzliche GUIDs registrieren und die GUIDs zwischen den Plänen ändern, wobei es sich bei den Plänen um Varianten eines Angebots handelt.

## <a name="register-guids"></a>Registrieren von GUIDs

Die GUIDs müssen in Partner Center registriert werden, um die Zuordnung der Nutzung durch Kunden zu ermöglichen.

Nachdem Sie Ihrer Vorlage oder im Benutzer-Agent eine GUID hinzugefügt und die GUID in Partner Center registriert haben, werden zukünftige Bereitstellungen nachverfolgt.

> [!NOTE]
> Wenn Sie Ihr [Azure-Anwendungsangebot](./create-new-azure-apps-offer.md) im Azure Marketplace über Partner Center veröffentlichen, wird jede neue GUID, die in Ihrer Vorlage verwendet wird, beim Hochladen der Vorlage automatisch im Partner Center-Profil registriert.  

1. Melden Sie sich bei [Partner Center](https://partner.microsoft.com/dashboard) an.

1. Registrieren Sie sich als [kommerzieller Marketplace-Herausgeber](https://aka.ms/JoinMarketplace).

   * Partner müssen [über ein Profil im Partner Center verfügen](./partner-center-portal/create-account.md). Wir empfehlen Ihnen, das Angebot im Azure Marketplace oder in AppSource aufzulisten.
   * Partner können mehrere GUIDs registrieren.
   * Partner können auch GUIDs für nicht im Marketplace verfügbare Lösungsvorlagen und Angebote registrieren.

1. Wählen Sie in der oberen rechten Ecke **Einstellungen** (Zahnradsymbol) und dann **Kontoeinstellungen** aus.

1. Wählen Sie unter **Organisationsprofil** > **Bezeichner** die Option **Nachverfolgungs-GUID hinzufügen** aus.

1. Geben Sie im Feld **GUID** Ihre Nachverfolgungs-GUID ein. Geben Sie nur die GUID ohne das Präfix `pid-` ein. Geben Sie im Feld **Beschreibung** den Angebotsnamen oder eine Beschreibung ein.

1. Wenn Sie mehrere GUIDs registrieren möchten, klicken Sie erneut auf **Nachverfolgungs-GUID hinzufügen**. Auf der Seite werden weitere Felder angezeigt.

1. Wählen Sie **Speichern** aus.

## <a name="use-resource-manager-templates"></a>Verwenden von Resource Manager-Vorlagen
Viele Partnerlösungen werden mithilfe von Azure Resource Manager-Vorlagen bereitgestellt. Wenn Sie über eine Resource Manager-Vorlage verfügen, die im Azure Marketplace, auf GitHub oder als Schnellstart verfügbar ist, können Sie die Vorlage ganz einfach ändern, um die Zuordnung der Nutzung durch Kunden zu ermöglichen.

> [!NOTE]
> Weitere Informationen zum Erstellen und Veröffentlichen von Lösungsvorlagen finden Sie unter:
> * [Erstellen und Bereitstellen Ihrer ersten Resource Manager-Vorlage](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)
>* [Azure-Anwendungsangebot](./create-new-azure-apps-offer.md)
>* Video: [Erstellen von Lösungsvorlagen und verwalteten Anwendungen für den Azure Marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603).


Um einen global eindeutigen Bezeichner (GUID) hinzuzufügen, nehmen Sie an der Hauptvorlagendatei eine einzige Änderung vor:

1. [Erstellen Sie eine GUID](#create-guids) unter Verwendung der vorgeschlagenen Methode, und [registrieren Sie die GUID](#register-guids).

1. Öffnen Sie die Resource Manager-Vorlage.

1. Fügen Sie eine neue Ressource vom Typ [Microsoft.Resources/deployments](/azure/templates/microsoft.resources/deployments) in der Hauptvorlagendatei hinzu. Die Ressource darf sich nur in der Datei **mainTemplate.json** oder **azuredeploy.json** befinden, nicht in geschachtelten oder verknüpften Vorlagen.

1. Geben Sie den GUID-Wert nach dem Präfix `pid-` als Namen der Ressource ein. Wenn die GUID z. B. eb7927c8-dd66-43e1-b0cf-c346a422063 lautet, ist der Ressourcenname _pid-eb7927c8-dd66-43e1-b0cf-c346a422063_.

1. Überprüfen Sie die Vorlage auf Fehler.

1. Veröffentlichen Sie die Vorlage in den entsprechenden Repositorys erneut.

1. [Überprüfen Sie den Erfolg der GUID in der Vorlagenbereitstellung](#verify-the-guid-deployment).

### <a name="sample-resource-manager-template-code"></a>Beispielcode für Resource Manager-Vorlage

Um die Überwachung von Ressourcen für Ihre Vorlage zu aktivieren, müssen Sie im Abschnitt „Resources“ die folgende zusätzliche Ressource hinzufügen. Stellen Sie sicher, dass Sie den unten stehenden Beispielcode mit Ihren eigenen Eingaben ändern, wenn Sie ihn zur Hauptvorlagendatei hinzufügen.
Die Ressource muss nur in der Datei **mainTemplate.json** oder **azuredeploy.json** hinzugefügt werden, nicht in geschachtelten oder verknüpften Vorlagen.

```json
// Make sure to modify this sample code with your own inputs where applicable

{ // add this resource to the resources section in the mainTemplate.json (do not add the entire file)
    "apiVersion": "2020-06-01",
    "name": "pid-XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX", // use your generated GUID here
    "type": "Microsoft.Resources/deployments",
    "properties": {
        "mode": "Incremental",
        "template": {
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
            "resources": []
        }
    }
} // remove all comments from the file when complete
```

## <a name="use-the-resource-manager-apis"></a>Verwenden der Resource Manager-APIs

In einigen Fällen bevorzugen Sie möglicherweise zum Bereitstellen von Azure-Diensten direkte Aufrufe an die Resource Manager-REST-APIs. [Azure unterstützt mehrere SDKs](../index.yml?pivot=sdkstools), um diese Aufrufe zu ermöglichen. Sie können eines der SDKs verwenden oder die REST-APIs direkt aufrufen, um Ressourcen bereitzustellen.

Wenn Sie eine Azure Resource Manager-Vorlage verwenden, sollten Sie Ihre Lösung mithilfe der zuvor beschriebenen Anweisungen markieren. Wenn Sie keine Resource Manager-Vorlage verwenden und direkte API-Aufrufe vornehmen, können Sie dennoch die Bereitstellung markieren, um die Nutzung von Azure-Ressourcen zuzuordnen.

### <a name="tag-a-deployment-with-the-resource-manager-apis"></a>Markieren einer Bereitstellung mithilfe der Resource Manager-APIs

Fügen Sie Entwerfen Ihrer API-Aufrufe eine GUID in den Benutzer-Agent-Header in der Anforderung ein, um die Zuordnung der Nutzung durch Kunden zu aktivieren. Fügen Sie die GUID für jedes Angebot oder SKU hinzu. Formatieren Sie die Zeichenfolge mit dem Präfix `pid-`, und fügen Sie die vom Partner generierte GUID ein. Nachfolgend finden Sie ein Beispiel für das GUID-Format zum Einfügen in den Benutzer-Agent:

![GUID-Beispielformat](media/marketplace-publishers-guide/tracking-sample-guid-for-lu-2.PNG)

> [!NOTE]
> Das Format der Zeichenfolge ist wichtig. Wenn das Präfix `pid-` nicht enthalten ist, können die Daten nicht abgefragt werden. Verschiedene SDKs gehen bei der Nachverfolgung unterschiedlich vor. Zum Implementieren dieser Methode müssen Sie die Unterstützung und den Nachverfolgungsansatz Ihres bevorzugten Azure SDK überprüfen.

#### <a name="example-the-python-sdk"></a>Beispiel: Python SDK

Bei Python wird das Attribut **config** verwendet. Sie können das Attribut nur einem Benutzer-Agent hinzufügen. Hier sehen Sie ein Beispiel:

![Attribut zu einem Benutzer-Agent hinzufügen](media/marketplace-publishers-guide/python-for-lu.PNG)

> [!NOTE]
> Fügen Sie das Attribut für jeden Client hinzu. Es gibt keine globale statische Konfiguration. Sie können eine Clientfactory markieren, um sicherzustellen, dass jeder Client nachverfolgt wird. Weitere Informationen finden Sie im [Clientfactory-Beispiel auf GitHub](https://github.com/Azure/azure-cli/blob/7402fb2c20be2cdbcaa7bdb2eeb72b7461fbcc30/src/azure-cli-core/azure/cli/core/commands/client_factory.py#L70-L79).

#### <a name="example-the-net-sdk"></a>Beispiel: .NET SDK

Stellen Sie für .NET sicher, dass Sie den Benutzer-Agent festlegen. Die [Microsoft.Azure.Management.Fluent](/dotnet/api/microsoft.azure.management.fluent)-Bibliothek kann verwendet werden, um den Benutzer-Agent mit folgendem Code (Beispiel in C#) festzulegen:

```csharp

var azure = Microsoft.Azure.Management.Fluent.Azure
    .Configure()
    // Add your pid in the user agent header
    .WithUserAgent("pid-XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX", String.Empty) 
    .Authenticate(/* Credentials created via Microsoft.Azure.Management.ResourceManager.Fluent.SdkContext.AzureCredentialsFactory */)
    .WithSubscription("<subscription ID>");
```

#### <a name="tag-a-deployment-by-using-the-azure-powershell"></a>Markieren einer Bereitstellung mithilfe von Azure PowerShell

Wenn Sie Ressourcen mithilfe von Azure PowerShell bereitstellen, fügen Sie Ihre GUID mit der folgenden Methode an:

```powershell
[Microsoft.Azure.Common.Authentication.AzureSession]::ClientFactory.AddUserAgent("pid-eb7927c8-dd66-43e1-b0cf-c346a422063")
```

#### <a name="tag-a-deployment-by-using-the-azure-cli"></a>Markieren einer Bereitstellung mithilfe der Azure CLI

Wenn Sie Ihre GUID mithilfe der Azure CLI anfügen, legen Sie die Umgebungsvariable **AZURE_HTTP_USER_AGENT** fest. Sie können diese Variable im Rahmen eines Skripts festlegen. Sie können die Variable auch global für den Bereich der Shell festlegen:

```powershell
export AZURE_HTTP_USER_AGENT='pid-eb7927c8-dd66-43e1-b0cf-c346a422063'
```

Weitere Informationen finden Sie unter [Azure SDK für Go](/azure/developer/go/).

## <a name="use-terraform"></a>Einsatz von Terraform

Terraform wird seit dem Release 1.21.0 für Azure-Anbieter unterstützt: [https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019](https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019).  Diese Unterstützung gilt für alle Partner, die ihre Lösungen über Terraform bereitstellen, sowie für alle bereitgestellten und vom Azure-Anbieter (Version 1.21.0 oder höher) abgerechneten Ressourcen.

Im Azure-Anbieter für Terraform wurde ein neues optionales Feld namens [*partner_id*](https://www.terraform.io/docs/providers/azurerm/#partner_id) hinzugefügt, über das Sie die für die Lösung verwendete GUID nachverfolgen können. Der Wert dieses Feld kann zudem aus der Umgebungsvariable *ARM_PARTNER_ID* abgerufen werden.

```
provider "azurerm" {
          subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          client_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          ……
          # new stuff for ISV attribution
          partner_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"}
```
Partner, die Ihre Terraform-Bereitstellung durch die Zuordnung der Nutzung durch Kunden nachverfolgen möchten, müssen folgende Schritte durchführen:

* Erstellen Sie eine GUID (diese sollte für jedes Angebot oder jede SKU hinzugefügt werden).
* Legen Sie den Wert von *partner_id* des Azure-Anbieters auf die GUID fest (stellen Sie der GUID nicht „pid-“ voran, sondern geben Sie die tatsächliche GUID an).

## <a name="verify-the-guid-deployment"></a>Überprüfen der GUID-Bereitstellung

Nachdem Sie Ihre Vorlage geändert und eine Testbereitstellung ausgeführt haben, können Sie mit dem folgenden PowerShell-Skript die Ressourcen abrufen, die Sie bereitgestellt und markiert haben.

Mit dem Skript können Sie überprüfen, ob die GUID erfolgreich der Resource Manager-Vorlage hinzugefügt wurde. Das Skript gilt nicht für Resource Manager-API- oder Terraform-Bereitstellungen.

Melden Sie sich bei Azure an. Wählen Sie das Abonnement mit der Bereitstellung aus, die Sie vor dem Ausführen des Skripts überprüfen möchten. Führen Sie das Skript im Abonnementkontext der Bereitstellung aus.

Die **GUID** und der Name der **Ressourcengruppe** der Bereitstellung sind erforderliche Parameter.

Sie erhalten [das ursprüngliche Skript](https://gist.github.com/bmoore-msft/ae6b8226311014d6e7177c5127c7eba1#file-verify-deploymentguid-ps1) auf GitHub.

```powershell
Param(
    [GUID][Parameter(Mandatory=$true)]$guid,
    [string][Parameter(Mandatory=$true)]$resourceGroupName
)

# Get the correlationId of the pid deployment

$correlationId = (Get-AzResourceGroupDeployment -ResourceGroupName
$resourceGroupName -Name "pid-$guid").correlationId

# Find all deployments with that correlationId

$deployments = Get-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName | Where-Object{$_.correlationId -eq $correlationId}

# Find all deploymentOperations in a deployment by name
# PowerShell doesn't surface outputResources on the deployment
# or correlationId on the deploymentOperation

foreach ($deployment in $deployments){

# Get deploymentOperations by deploymentName
# then the resourceId for any create operation

($deployment | Get-AzResourceGroupDeploymentOperation | Where-Object{$_.properties.provisioningOperation -eq "Create" -and $_.properties.targetResource.resourceType -ne "Microsoft.Resources/deployments"}).properties.targetResource.id

}
```
## <a name="report"></a>Bericht
Die Berichterstellung für den Azure-Verbrauch, die über die Zuordnung der Nutzung durch Kunden nachverfolgt wird, ist heute für ISV-Partner nicht verfügbar. Die Berichterstellung für das Programm „Kommerzieller Marketplace“ in Partner Center zur Abdeckung der Kundennutzung soll in der zweiten Hälfte des Jahres 2021 hinzugefügt werden.

## <a name="notify-your-customers"></a>Benachrichtigen der Kunden

Partner sollten ihre Kunden über Bereitstellungen informieren, bei denen die Zuordnung der Nutzung durch Kunden eingesetzt wird. Microsoft erstellt für den Partner Berichte über die Azure-Nutzung, die mit diesen Bereitstellungen verknüpft ist. Die folgenden Beispiele enthalten Inhalte, die Sie für die Benachrichtigung Ihrer Kunden über diese Bereitstellungen verwenden können. Ersetzen Sie in den Beispielen \<PARTNER> durch den Namen Ihres Unternehmens. Der Partner muss sicherstellen, dass die Benachrichtigung den eigenen Richtlinien für Datenschutz und Datensammlung entspricht, und Optionen für Kunden vorsehen, die von der Nachverfolgung ausgenommen werden möchten.

### <a name="notification-for-resource-manager-template-deployments"></a>Benachrichtigung für Bereitstellungen über Resource Manager-Vorlagen

Wenn Sie diese Vorlage bereitstellen, kann Microsoft die Installation der Software von \<PARTNER> mit den bereitgestellten Azure-Ressourcen identifizieren. Microsoft kann die Azure-Ressourcen korrelieren, die zum Unterstützen der Software verwendet werden. Microsoft erfasst diese Informationen, um die Nutzung der Produkte zu optimieren und um Geschäfte zu ermöglichen. Diese Daten unterliegen den Datenschutzrichtlinien von Microsoft und werden gemäß dieser Richtlinien gesammelt. Diese Datenschutzrichtlinien können unter [https://www.microsoft.com/trustcenter](https://www.microsoft.com/trustcenter) eingesehen werden.

### <a name="notification-for-sdk-or-api-deployments"></a>Benachrichtigung für SDK- oder API-Bereitstellungen

Wenn Sie Software von \<PARTNER> bereitstellen, kann Microsoft die Installation der Software von \<PARTNER> mit den bereitgestellten Azure-Ressourcen identifizieren. Microsoft kann die Azure-Ressourcen korrelieren, die zum Unterstützen der Software verwendet werden. Microsoft erfasst diese Informationen, um die Nutzung der Produkte zu optimieren und um Geschäfte zu ermöglichen. Diese Daten unterliegen den Datenschutzrichtlinien von Microsoft und werden gemäß dieser Richtlinien gesammelt. Diese Datenschutzrichtlinien können unter [https://www.microsoft.com/trustcenter](https://www.microsoft.com/trustcenter) eingesehen werden.

## <a name="get-support"></a>Support

Informationen zu den Supportoptionen im kommerziellen Marketplace finden Sie unter [Support für das Programm „Kommerzieller Marketplace“ in Partner Center](support.md).

### <a name="how-to-submit-a-technical-consultation-request"></a>So fragen Sie technische Beratung an

1. Besuchen Sie [Partner Technical Services](https://aka.ms/TechnicalJourney).
1. Klicken Sie auf „Cloudinfrastruktur und -management“. Daraufhin wird eine neue Seite geöffnet, auf der der technische Workflow dargestellt wird.
1. Klicken Sie unter „Bereitstellungsdienste“ auf „Anforderung übermitteln“.
1. Melden Sie sich mit Ihrem verwalteten Dienstkonto (MPN-Konto) oder Ihrem Azure Active Directory-Konto (Partnerdashboard-Konto) an. Je nach Konto wird ein entsprechendes Anforderungsformular geöffnet:
    * Vervollständigen bzw. überprüfen Sie die Kontaktinformationen.
    * Wenn die Details zur Beratung nicht vorab ausgefüllt wurden, können Sie diese aus den Dropdownmenüs auswählen.
    * Geben Sie einen Titel und eine Beschreibung (so detailliert wie möglich) für das Problem ein.

1. Klicken Sie auf „Senden“.

Eine ausführliche Anleitung mit Screenshots finden Sie unter [Verwenden von technischen Presales- und Bereitstellungsdiensten](https://aka.ms/TechConsultInstructions).

### <a name="whats-next"></a>Nächste Schritte

Im Anschluss werden Sie von einem technischen Berater eines Microsoft-Partners kontaktiert, der mit Ihnen einen passenden Termin für einen Anruf festlegt.

## <a name="faq"></a>Häufig gestellte Fragen

**Welchen Vorteil hat das Hinzufügen der GUID zur Vorlage?**

Microsoft stellt Partnern eine Ansicht über die Kundenbereitstellungen ihrer Lösungen und Erkenntnisse über den Einfluss auf die Nutzung bereit. Sowohl Microsoft als auch der Partner können diese Informationen dazu verwenden, eine engere Beziehung zwischen den Vertriebsteams zu fördern. Microsoft und der Partner können damit auch eine konsistentere Ansicht des Einflusses der einzelnen Partner auf das Azure-Wachstum erhalten.

**Kann eine GUID geändert werden, nachdem sie hinzugefügt wurde?**

Ja, ein Kunde oder Implementierungspartner kann die Vorlage anpassen und die GUID ändern oder entfernen. Es wird empfohlen, dass Partner ihren Kunden und Partnern proaktiv die Funktion der Ressource und der GUID beschreiben, um das Entfernen oder Ändern der GUID zu verhindern. Eine Änderung der GUID hat nur Auswirkungen auf neue, noch nicht vorhandene Bereitstellungen und Ressourcen.

**Kann ich Vorlagen nachverfolgen, die nicht über ein Microsoft-Repository, sondern z.B. über GitHub bereitgestellt wurden?**

Ja, solange die GUID bei der Bereitstellung der Vorlage vorhanden ist, wird die Nutzung nachverfolgt. Partner müssen Ihre GUIDs jedoch trotzdem registrieren.

**Erhält der Kunde die Berichte auch?**

Kunden können ihre Nutzung einzelner Ressourcen oder benutzerdefinierter Ressourcengruppen im Azure-Portal nachverfolgen. Kunden wird die Nutzung nicht nach GUID aufgeschlüsselt angezeigt.

**Ist diese Methode mit dem digitalen Partner des Eintrags (Digital Partner of Record, DPOR) vergleichbar?**

Diese neue Methode für das Verbinden von Bereitstellung und Nutzung mit der Lösung eines Partners stellt einen Mechanismus zum Verknüpfen einer Partnerlösung mit der Azure-Nutzung bereit. Mit DPOR soll ein Beratungspartner (Systemintegrator) oder Verwaltungspartner (Managed Services Provider) dem Azure-Abonnement eines Kunden zugeordnet werden.

**Kann ich eine private, benutzerdefinierte virtuelle Festplatte für ein Angebot für Lösungsvorlagen im Azure Marketplace verwenden?**

Nein, das ist nicht möglich. Das VM-Image muss aus dem Azure Marketplace stammen. Weitere Informationen finden Sie unter [Leitfaden für die Veröffentlichung von Virtual Machine-Angeboten](marketplace-virtual-machines.md).

Sie können ein mit einer benutzerdefinierten virtuellen Festplatte ein VM-Angebot im Marketplace erstellen und es als „Privat“ markieren, sodass andere Benutzer dieses nicht einsehen können. Verweisen Sie dann in der Lösungsvorlage auf diese VM.

**Warum ist das Aktualisieren der Eigenschaft *contentVersion* für die Hauptvorlage fehlgeschlagen?**

Es liegt wahrscheinlich ein Fehler vor in Fällen, in denen die Vorlage mithilfe einer TemplateLink-Eigenschaft einer anderen Vorlage bereitgestellt wird, die aus irgendeinem Grund eine ältere contentVersion-Eigenschaft erwartet. Verwenden Sie als Problemumgehung die Metadateneigenschaft:

```
"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "metadata": {
        "contentVersion": "1.0.1.0"
    },
    "parameters": {
```
