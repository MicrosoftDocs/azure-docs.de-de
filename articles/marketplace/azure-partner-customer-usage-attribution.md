---
title: Zuordnen der Nutzung durch Azure-Partner und -Kunden | Azure Marketplace
description: Übersicht über die Nachverfolgung der Kundennutzung für Azure Marketplace-Lösungen
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: yijenj
ms.service: marketplace
ms.topic: article
ms.date: 11/17/2018
ms.author: yijenj
ms.openlocfilehash: 45d575488db8d740069fba961203842899024747
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66807181"
---
# <a name="azure-partner-customer-usage-attribution"></a>Zuordnen der Nutzung durch Kunden von Azure-Partnern

Als Softwarepartner für Azure erfordern Ihre Lösungen entweder Azure-Komponenten oder müssen direkt in der Azure-Infrastruktur bereitgestellt werden. Für Kunden, die eine Partnerlösung und eigene Azure-Ressourcen bereitstellen, kann es möglicherweise schwierig sein, einen Einblick in den Status der Bereitstellung und den Einfluss auf das Azure-Wachstum zu erhalten. Ein höherer Sichtbarkeitsgrad hilft bei der Abstimmung mit den Microsoft-Vertriebsteams und dem Erhalt von Gutschriften für Microsoft-Partnerprogramme.

Microsoft bietet jetzt eine Methode an, um Partner bei der besseren Verfolgung der Azure-Nutzung durch Kundenbereitstellungen ihrer Software unter Azure zu unterstützen. Diese neue Methode verwendet Azure Resource Manager, um die Bereitstellung von Azure-Diensten zu orchestrieren.

Als Microsoft-Partner können Sie die Azure-Nutzung allen Azure-Ressourcen zuordnen, die Sie für einen Kunden bereitstellen. Sie können die Zuordnung über den Azure Marketplace, das Schnellstartrepository, private GitHub-Repositorys und direkte Kundeninteraktion herstellen. Die Zuordnung der Nutzung durch Kunden unterstützt drei Bereitstellungsmethoden:

- Azure Resource Manager-Vorlagen: Partner können Resource Manager-Vorlagen verwenden, um Azure-Dienste zum Ausführen der Software des Partners bereitzustellen. Partner können Resource Manager-Vorlagen erstellen, um die Infrastruktur und Konfiguration ihrer Azure-Lösung zu definieren. Mit einer Resource Manager-Vorlage können Sie und Ihre Kunden die Lösung während des gesamten Lebenszyklus bereitstellen. Sie können sicher sein, dass Ihre Ressourcen in einem konsistenten Zustand bereitgestellt werden.
- Azure Resource Manager-APIs: Partner können die Resource Manager-APIs direkt aufrufen, um eine Resource Manager-Vorlage bereitzustellen oder um API-Aufrufe für die direkte Bereitstellung von Azure-Diensten zu generieren.
- Terraform: Partner können einen Cloudorchestrator wie Terraform verwenden, um eine Resource Manager-Vorlage oder die Azure-Dienste direkt bereitzustellen.

Die Zuordnung der Nutzung durch Kunden gilt für neue Bereitstellung. Das Markieren vorhandener, bereits bereitgestellter Ressourcen wird nicht unterstützt.

Die Zuordnung der Nutzung durch Kunden ist für [Azure-Anwendungsangebote](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/azure-applications/cpp-azure-app-offer) für Lösungsvorlagen erforderlich, die über den Azure Marketplace veröffentlicht werden.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="use-resource-manager-templates"></a>Verwenden von Resource Manager-Vorlagen
Viele Partnerlösungen werden mithilfe von Resource Manager-Vorlagen im Abonnement eines Kunden bereitgestellt. Wenn Sie eine Resource Manager-Vorlage besitzen, die im Azure Marketplace, auf GitHub oder als Schnellstart verfügbar ist, können Sie die Vorlage einfach bearbeiten, um die Zuordnung der Nutzung durch Kunden zu aktivieren.

Weitere Informationen zum Erstellen und Veröffentlichen von Lösungsvorlagen finden Sie unter:

* [Erstellen und Bereitstellen Ihrer ersten Resource Manager-Vorlage](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)
* [Azure-Anwendungsangebot](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/azure-applications/cpp-azure-app-offer)
* Video: [Erstellen von Lösungsvorlagen und verwalteten Anwendungen für den Azure Marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603)


## <a name="add-a-guid-to-your-template"></a>Hinzufügen einer GUID zu Ihrer Vorlage

Um einen global eindeutigen Bezeichner (GUID) hinzuzufügen, nehmen Sie an der Hauptvorlagendatei eine einzige Änderung vor:

1. [Erstellen Sie eine GUID](#create-guids) unter Verwendung der vorgeschlagenen Methode, und [registrieren Sie die GUID](#register-guids-and-offers).

1. Öffnen Sie die Resource Manager-Vorlage.

1. Fügen Sie eine neue Ressource in der Hauptvorlagendatei hinzu. Die Ressource darf sich nur in der Datei **mainTemplate.json** oder **azuredeploy.json** befinden, nicht in geschachtelten oder verknüpften Vorlagen.

1. Geben Sie den GUID-Wert nach dem Präfix **pid-** ein (z. B. pid-eb7927c8-dd66-43e1-b0cf-c346a422063).

1. Überprüfen Sie die Vorlage auf Fehler.

1. Veröffentlichen Sie die Vorlage in den entsprechenden Repositorys erneut.

1. [Überprüfen Sie den Erfolg der GUID in der Vorlagenbereitstellung](#verify-the-guid-deployment).

### <a name="sample-resource-manager-template-code"></a>Beispielcode für Resource Manager-Vorlage

Um die Überwachung von Ressourcen für Ihre Vorlage zu aktivieren, müssen Sie im Abschnitt „Resources“ die folgende zusätzliche Ressource hinzufügen. Stellen Sie sicher, dass Sie den unten stehenden Beispielcode mit Ihren eigenen Eingaben ändern, wenn Sie ihn zur Hauptvorlagendatei hinzufügen.
Die Ressource muss nur in der Datei **mainTemplate.json** oder **azuredeploy.json** hinzugefügt werden, nicht in geschachtelten oder verknüpften Vorlagen.

```
// Make sure to modify this sample code with your own inputs where applicable

{ // add this resource to the resources section in the mainTemplate.json (do not add the entire file)
    "apiVersion": "2018-02-01",
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

In einigen Fällen bevorzugen Sie möglicherweise zum Bereitstellen von Azure-Diensten direkte Aufrufe an die Resource Manager-REST-APIs. [Azure unterstützt mehrere SDKs](https://docs.microsoft.com/azure/#pivot=sdkstools), um diese Aufrufe zu ermöglichen. Sie können eines der SDKs verwenden oder die REST-APIs direkt aufrufen, um Ressourcen bereitzustellen.

Wenn Sie eine Azure Resource Manager-Vorlage verwenden, sollten Sie Ihre Lösung mithilfe der zuvor beschriebenen Anweisungen markieren. Wenn Sie keine Resource Manager-Vorlage verwenden und direkte API-Aufrufe vornehmen, können Sie dennoch die Bereitstellung markieren, um die Nutzung von Azure-Ressourcen zuzuordnen.

### <a name="tag-a-deployment-with-the-resource-manager-apis"></a>Markieren einer Bereitstellung mithilfe der Resource Manager-APIs

Fügen Sie Entwerfen Ihrer API-Aufrufe eine GUID in den Benutzer-Agent-Header in der Anforderung ein, um die Zuordnung der Nutzung durch Kunden zu aktivieren. Fügen Sie die GUID für jedes Angebot oder SKU hinzu. Formatieren Sie die Zeichenfolge mit dem Präfix **pid-** , und fügen Sie die vom Partner generierte GUID ein. Nachfolgend finden Sie ein Beispiel für das GUID-Format zum Einfügen in den Benutzer-Agent:

![GUID-Beispielformat](media/marketplace-publishers-guide/tracking-sample-guid-for-lu-2.PNG)

> [!Note]
> Das Format der Zeichenfolge ist wichtig. Wenn das Präfix **pid-** nicht enthalten ist, können die Daten nicht abgefragt werden. Verschiedene SDKs gehen bei der Nachverfolgung unterschiedlich vor. Zum Implementieren dieser Methode müssen Sie die Unterstützung und den Nachverfolgungsansatz Ihres bevorzugten Azure SDK überprüfen.

#### <a name="example-the-python-sdk"></a>Beispiel: Python SDK

Bei Python wird das Attribut **config** verwendet. Sie können das Attribut nur einem Benutzer-Agent hinzufügen. Hier sehen Sie ein Beispiel:

![Attribut zu einem Benutzer-Agent hinzufügen](media/marketplace-publishers-guide/python-for-lu.PNG)

> [!Note]
> Fügen Sie das Attribut für jeden Client hinzu. Es gibt keine globale statische Konfiguration. Sie können eine Clientfactory markieren, um sicherzustellen, dass jeder Client nachverfolgt wird. Weitere Informationen finden Sie im [Clientfactory-Beispiel auf GitHub](https://github.com/Azure/azure-cli/blob/7402fb2c20be2cdbcaa7bdb2eeb72b7461fbcc30/src/azure-cli-core/azure/cli/core/commands/client_factory.py#L70-L79).

#### <a name="tag-a-deployment-by-using-the-azure-powershell"></a>Markieren einer Bereitstellung mithilfe von Azure PowerShell

Wenn Sie Ressourcen mithilfe von Azure PowerShell bereitstellen, fügen Sie Ihre GUID mit der folgenden Methode an:

```powershell
[Microsoft.Azure.Common.Authentication.AzureSession]::ClientFactory.AddUserAgent("pid-eb7927c8-dd66-43e1-b0cf-c346a422063")
```

#### <a name="tag-a-deployment-by-using-the-azure-cli"></a>Markieren einer Bereitstellung mithilfe der Azure CLI

Wenn Sie Ihre GUID mithilfe der Azure CLI anfügen, legen Sie die Umgebungsvariable **AZURE_HTTP_USER_AGENT** fest. Sie können diese Variable im Rahmen eines Skripts festlegen. Sie können die Variable auch global für den Bereich der Shell festlegen:

```
export AZURE_HTTP_USER_AGENT='pid-eb7927c8-dd66-43e1-b0cf-c346a422063'
```
Weitere Informationen finden Sie unter [Azure SDK für Go](https://docs.microsoft.com/go/azure/).

## <a name="use-terraform"></a>Einsatz von Terraform

Terraform wird seit dem Release 1.21.0 für Azure-Anbieter unterstützt: [https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019](https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019).  Diese Unterstützung gilt für alle Partner, die ihre Lösungen über Terraform bereitstellen, sowie für alle bereitgestellten und vom Azure-Anbieter (Version 1.21.0 oder höher) abgerechneten Ressourcen.

Im Azure-Anbieter für Terraform wurde ein neues optionales Feld namens [*partner_id*](https://www.terraform.io/docs/providers/azurerm/#partner_id) hinzugefügt, über das Sie die für die Lösung verwendete GUID nachverfolgen können. Der Wert dieses Feld kann zudem aus der Umgebungsvariable *ARM_PARTNER_ID* abgerufen werden.

```
provider "azurerm" {
          subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          client_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          ……
          # new stuff for ISV attribution
          partner_id = “xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"}
```
Partner, die Ihre Terraform-Bereitstellung durch die Zuordnung der Nutzung durch Kunden nachverfolgen möchten, müssen folgende Schritte durchführen:

* Erstellen Sie eine GUID (diese sollte für jedes Angebot oder jede SKU hinzugefügt werden).
* Legen Sie den Wert von *partner_id* des Azure-Anbieters auf die GUID fest (stellen Sie der GUID nicht „pid-“ voran, sondern geben Sie die tatsächliche GUID an).

## <a name="create-guids"></a>Erstellen von GUIDs

Eine GUID ist ein eindeutiger Verweis mit 32 Hexadezimalziffern. Zum Erstellen einer GUID für die Nachverfolgung sollten Sie einen GUID-Generator verwenden. Das Azure Storage-Team hat ein [Formular für den GUID-Generator](https://aka.ms/StoragePartners) erstellt, über das Sie per E-Mail eine GUID im richtigen Format erhalten. Dieses Formular kann für verschiedene Nachverfolgungssysteme wiederverwendet werden.

> [!Note]
> Es wird dringend empfohlen, das [GUID-Generatorformular von Azure Storage](https://aka.ms/StoragePartners) zu verwenden, um Ihre GUID zu erstellen. Weitere Informationen finden Sie in den [häufig gestellten Fragen](#faq).

Es empfiehlt sich, für jedes Produkt eine eindeutige GUID für jedes Angebot und jeden Distributionskanal zu erstellen. Sie können festlegen, dass eine einzige GUID für die vielfältigen Verteilungskanäle des Produkts verwendet wird, wenn die Berichterstellung nicht aufgeteilt werden soll.

Wenn Sie ein Produkt mit einer Vorlage bereitstellen und das Produkt sowohl im Azure Marketplace als auch auf GitHub verfügbar ist, können Sie zwei unterschiedliche GUIDs erstellen und registrieren:

*   Produkt A im Azure Marketplace
*   Produkt A auf GitHub

Die Berichterstellung erfolgt über den Partnerwert (Microsoft-Partner-ID) und die GUIDs.

Sie können GUIDs auch auf einer differenzierteren Ebene (z. B. pro SKU) nachverfolgen (wobei SKUs Varianten eines Angebots sind).

## <a name="register-guids-and-offers"></a>Registrieren von GUIDs und Angeboten

Die GUIDs müssen registriert werden, damit die Zuordnung der Nutzung durch Kunden aktiviert wird.

Alle Registrierungen für Vorlagen-GUIDs erfolgen über das Azure Marketplace-Cloud-Partnerportal (CPP).

Nachdem Sie die GUID in der Vorlage oder im Benutzer-Agent hinzugefügt und die GUID im CPP registriert haben, werden alle Bereitstellungen nachverfolgt.

1. Stellen Sie beim [Azure Marketplace](https://aka.ms/listonazuremarketplace) einen Antrag, damit Sie Zugriff auf das Cloud-Partnerportal (CPP) erhalten.

   * Partner müssen [ein Profil im CPP](https://docs.microsoft.com/azure/marketplace/become-publisher) haben. Wir empfehlen Ihnen, das Angebot im Azure Marketplace oder in AppSource aufzulisten.
   * Partner können mehrere GUIDs registrieren.
   * Partner können auch eine GUID für die nicht im Marketplace verfügbaren Lösungsvorlagen und Angebote registrieren.

1. Melden Sie sich beim [Cloudpartnerportal](https://cloudpartner.azure.com/) an.

1. Wählen Sie oben rechts Ihr Kontosymbol und dann **Herausgeberprofil** aus.

   ![Herausgeberprofil auswählen](media/marketplace-publishers-guide/guid-image-for-lu.png)

1. Wählen Sie auf der Seite **Profil** die Option **Nachverfolgungs-GUID hinzufügen** aus.

   ![„Nachverfolgungs-GUID hinzufügen“ auswählen](media/marketplace-publishers-guide/guid-how-to-add-tracking.png)

1. Geben Sie im Feld **Nachverfolgungs-GUID** Ihre Nachverfolgungs-GUID ein. Geben Sie nur die GUID ohne das Präfix **pid-** ein. Geben Sie im Feld **Benutzerdefinierte Beschreibung** den Angebotsnamen oder eine Beschreibung ein.

   ![Seite „Profil“](media/marketplace-publishers-guide/guid-dev-center-login.png)

   ![GUID und Angebotsbeschreibung eingeben](media/marketplace-publishers-guide/guid-dev-center-example.png)

1. Wenn Sie mehrere GUIDs registrieren möchten, klicken Sie erneut auf **Nachverfolgungs-GUID hinzufügen**. Auf der Seite werden weitere Felder angezeigt.

   ![„Nachverfolgungs-GUID hinzufügen“ erneut auswählen](media/marketplace-publishers-guide/guid-dev-center-example-add.png)

   ![Weitere GUID und Angebotsbeschreibung eingeben](media/marketplace-publishers-guide/guid-dev-center-example-description.png)

1. Wählen Sie **Speichern** aus.

   ![„Speichern“ auswählen](media/marketplace-publishers-guide/guid-dev-center-save.png)

Nachdem Sie die GUID in der Vorlage oder im Benutzer-Agent hinzugefügt und die GUID im CPP registriert haben, werden alle Bereitstellungen nachverfolgt.

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

Den Bericht für die Zuordnung der Nutzung durch Kunden finden Sie Dashboard „Analyse“ im Partner Center ([https://partner.microsoft.com/en-us/dashboard/mpn/analytics/CPP/MicrosoftAzure](https://partner.microsoft.com/dashboard/mpn/analytics/CPP/MicrosoftAzure)). Um den Bericht anzuzeigen, müssen Sie sich mit Ihren Anmeldeinformationen für das Partner Center anmelden. Wenn Sie Probleme mit dem Bericht oder der Anmeldung haben, erstellen Sie eine Supportanfrage gemäß der Anleitung im Abschnitt „Support“.

Wählen Sie in der Dropdownliste für den Partnerzuordnungstyp „Tracked Template“ (Nachverfolgte Vorlage) aus, um den Bericht anzuzeigen.

![Bericht für die Zuordnung der Nutzung durch Kunden](media/marketplace-publishers-guide/customer-usage-attribution-report.png)

## <a name="notify-your-customers"></a>Benachrichtigen der Kunden

Partner sollten ihre Kunden über Bereitstellungen informieren, bei denen die Zuordnung der Nutzung durch Kunden eingesetzt wird. Microsoft erstellt für den Partner Berichte über die Azure-Nutzung, die mit diesen Bereitstellungen verknüpft ist. Die folgenden Beispiele enthalten Inhalte, die Sie für die Benachrichtigung Ihrer Kunden über diese Bereitstellungen verwenden können. Ersetzen Sie in den Beispielen \<PARTNER> durch den Namen Ihres Unternehmens. Der Partner muss sicherstellen, dass die Benachrichtigung den eigenen Richtlinien für Datenschutz und Datensammlung entspricht, und Optionen für Kunden vorsehen, die von der Nachverfolgung ausgenommen werden möchten.

### <a name="notification-for-resource-manager-template-deployments"></a>Benachrichtigung für Bereitstellungen über Resource Manager-Vorlagen

Wenn Sie diese Vorlage bereitstellen, kann Microsoft die Installation der Software von \<PARTNER> mit den bereitgestellten Azure-Ressourcen identifizieren. Microsoft kann die Azure-Ressourcen korrelieren, die zum Unterstützen der Software verwendet werden. Microsoft erfasst diese Informationen, um die Nutzung der Produkte zu optimieren und um Geschäfte zu ermöglichen. Diese Daten unterliegen den Datenschutzrichtlinien von Microsoft und werden gemäß diesen Richtlinien gesammelt. Diese Datenschutzrichtlinien können unter https://www.microsoft.com/trustcenter eingesehen werden.

### <a name="notification-for-sdk-or-api-deployments"></a>Benachrichtigung für SDK- oder API-Bereitstellungen

Wenn Sie Software von \<PARTNER> bereitstellen, kann Microsoft die Installation der \<PARTNER>-Software mit den bereitgestellten Azure-Ressourcen identifizieren. Microsoft kann die Azure-Ressourcen korrelieren, die zum Unterstützen der Software verwendet werden. Microsoft erfasst diese Informationen, um die Nutzung der Produkte zu optimieren und um Geschäfte zu ermöglichen. Diese Daten unterliegen den Datenschutzrichtlinien von Microsoft und werden gemäß diesen Richtlinien gesammelt. Diese Datenschutzrichtlinien können unter https://www.microsoft.com/trustcenter eingesehen werden.

## <a name="get-support"></a>Support

Es gibt zwei Supportkanäle, abhängig vom Problem, mit dem Sie sich befassen.

Wenn Probleme im Partner Center auftreten, z.B. beim Anzeigen des Berichts über die Zuordnung der Nutzung durch Kunden oder beim Anmelden, erstellen Sie hier eine Supportanfrage an das Partner Center-Supportteam: [https://partner.microsoft.com/en-US/support](https://partner.microsoft.com/support)

![](./media/marketplace-publishers-guide/partner-center-log-in-support.png)

Wenn Sie Unterstützung beim Marketplace-Onboarding und/oder beim Zuordnen der Nutzung durch Kunden im Allgemeinen benötigen, wie z. B. beim Einrichten der Zuordnen der Nutzung durch Kunden, führen Sie die folgenden Schritte aus:

1. Navigieren Sie zur [Supportseite](https://go.microsoft.com/fwlink/?linkid=844975).

1. Wählen Sie unter **Problemtyp** die Option **Marketplace Onboarding** aus.

1. Wählen Sie die **Kategorie** für Ihr Problem aus:

   - Wählen Sie für Zuordnungsprobleme bei der Nutzung **Sonstige** aus.
   - Wählen Sie bei Problemen mit dem Zugriff auf das Azure Marketplace-CPP **Zugriffsprobleme** aus.

     ![Problemkategorie auswählen](media/marketplace-publishers-guide/lu-article-incident.png)

1. Wählen Sie **Anfrage starten** aus.

1. Geben Sie auf der nächsten Seite die erforderlichen Werte ein. Wählen Sie **Weiter**.

1. Geben Sie auf der nächsten Seite die erforderlichen Werte ein.

   > [!Important]
   > Geben Sie im Feld **Incident-Titel** den Wert **ISV-Nutzungsverfolgung** ein. Beschreiben Sie Ihr Problem im Detail.

   ![„ISV-Nutzungsverfolgung“ als „Incident-Titel“ eingeben](media/marketplace-publishers-guide/guid-dev-center-help-hd%201.png)

1. Füllen Sie das Formular vollständig aus, und wählen Sie dann **Senden** aus.

Sie können sich von einem technischen Berater eines Microsoft-Partners zudem zu Themen wie technischen Presales und Szenarios für die Bereitstellung und App-Entwicklung beraten lassen, wenn Sie die Zuordnung der Nutzung durch Kunden besser kennenlernen und einsetzen möchten.

### <a name="how-to-submit-a-technical-consultation-request"></a>So fragen Sie technische Beratung an

1. Besuchen Sie [https://aka.ms/TechnicalJourney](https://aka.ms/TechnicalJourney).
1. Klicken Sie auf „Cloudinfrastruktur und -management“. Daraufhin wird eine neue Seite geöffnet, auf der der technische Workflow dargestellt wird.
1. Klicken Sie unter „Bereitstellungsdienste“ auf „Anforderung übermitteln“.
1. Melden Sie sich mit Ihrem verwalteten Dienstkonto (MPN-Konto) oder Ihrem Azure Active Directory-Konto (Partnerdashboard-Konto) an. Je nach Konto wird ein entsprechendes Anforderungsformular geöffnet:
    * Vervollständigen bzw. überprüfen Sie die Kontaktinformationen.
    * Wenn die Details zur Beratung nicht vorab ausgefüllt wurden, können Sie diese aus den Dropdownmenüs auswählen.
    * Geben Sie einen Titel und eine Beschreibung (so detailliert wie möglich) für das Problem ein.
1. Klicken Sie auf „Senden“.

Eine ausführliche Anleitung mit Screenshots finden Sie unter [https://aka.ms/TechConsultInstructions](https://aka.ms/TechConsultInstructions).

### <a name="whats-next"></a>Nächste Schritte

Im Anschluss werden Sie von einem technischen Berater eines Microsoft-Partners kontaktiert, der mit Ihnen einen passenden Termin für einen Anruf festlegt.

## <a name="faq"></a>Häufig gestellte Fragen

**Welchen Vorteil hat das Hinzufügen der GUID zur Vorlage?**

Microsoft stellt Partnern eine Ansicht über die Kundenbereitstellungen ihrer Lösungen und Erkenntnisse über den Einfluss auf die Nutzung bereit. Sowohl Microsoft als auch der Partner können diese Informationen dazu verwenden, eine engere Beziehung zwischen den Vertriebsteams zu fördern. Microsoft und der Partner können damit auch eine konsistentere Ansicht des Einflusses der einzelnen Partner auf das Azure-Wachstum erhalten.

**Kann eine GUID geändert werden, nachdem sie hinzugefügt wurde?**

Ja, ein Kunde oder Implementierungspartner kann die Vorlage anpassen und die GUID ändern oder entfernen. Es wird empfohlen, dass Partner ihren Kunden und Partnern proaktiv die Funktion der Ressource und der GUID beschreiben, um das Entfernen oder Ändern der GUID zu verhindern. Eine Änderung der GUID hat nur Auswirkungen auf neue, noch nicht vorhandene Bereitstellungen und Ressourcen.

**Kann ich Vorlagen nachverfolgen, die nicht über ein Microsoft-Repository, sondern z.B. über GitHub bereitgestellt wurden?**

Ja, solange die GUID bei der Bereitstellung der Vorlage vorhanden ist, wird die Nutzung nachverfolgt. Partner müssen über ein Profil im Cloudpartnerportal verfügen, um die GUIDs zu registrieren, die für Bereitstellungen außerhalb vom Azure Marketplace verwendet werden.

**Erhält der Kunde die Berichte auch?**

Kunden können ihre Nutzung einzelner Ressourcen oder benutzerdefinierter Ressourcengruppen im Azure-Portal nachverfolgen.

**Ist diese Methode mit dem digitalen Partner des Eintrags (Digital Partner of Record, DPOR) vergleichbar?**

Diese neue Methode für das Verbinden von Bereitstellung und Nutzung mit der Lösung eines Partners stellt einen Mechanismus zum Verknüpfen einer Partnerlösung mit der Azure-Nutzung bereit. Mit DPOR soll ein Beratungspartner (Systemintegrator) oder Verwaltungspartner (Managed Services Provider) dem Azure-Abonnement eines Kunden zugeordnet werden.

**Was ist der Vorteil bei der Verwendung des GUID-Generatorformulars von Azure Storage?**

Das GUID-Generatorformular von Azure Storage generiert eine GUID garantiert im erforderlichen Format. Wenn Sie außerdem eine der Verfolgungsmethoden von Azure Storage für die Datenebene verwenden, können Sie dieselbe GUID für die Verfolgung der Marketplace-Steuerungsebene verwenden. Auf diese Weise können Sie eine einzelne einheitliche GUID für die Partnerzuordnung nutzen, ohne separate GUIDs verwalten zu müssen.

**Kann ich eine private, benutzerdefinierte virtuelle Festplatte für ein Angebot für Lösungsvorlagen im Azure Marketplace verwenden?**

Nein, das ist nicht möglich. Das VM-Image muss aus dem Azure Marketplace stammen. Weitere Informationen finden Sie unter [https://docs.microsoft.com/azure/marketplace/marketplace-virtual-machines](https://docs.microsoft.com/azure/marketplace/marketplace-virtual-machines).

Sie können ein mit einer benutzerdefinierten virtuellen Festplatte ein VM-Angebot im Marketplace erstellen und es als „Privat“ markieren, sodass andere Benutzer dieses nicht einsehen können. Verweisen Sie dann in der Lösungsvorlage auf diese VM.

**Warum ist das Aktualisieren der Eigenschaft *contentVersion* für die Hauptvorlage fehlgeschlagen?**

Es liegt wahrscheinlich ein Fehler vor, bei dem die Vorlage mithilfe der TemplateLink-Eigenschaft einer anderen Vorlage bereitgestellt wurde, die eine ältere contentVersion-Eigenschaft erwartet. Verwenden Sie als Problemumgehung die Metadateneigenschaft:

```
"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "metadata": {
        "contentVersion": "1.0.1.0"
    },
    "parameters": {
```
