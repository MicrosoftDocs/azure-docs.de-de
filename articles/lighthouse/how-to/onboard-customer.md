---
title: Onboarding eines Kunden in Azure Lighthouse durchführen
description: Erfahren Sie, wie Sie das Onboarding eines Kunden in Azure Lighthouse durchführen, sodass Ihr eigener Mandant über die delegierte Azure-Ressourcenverwaltung auf dessen Ressourcen zugreifen und sie verwalten kann.
ms.date: 12/04/2020
ms.topic: how-to
ms.openlocfilehash: b353a8194b9f5dd48b315340435669531359e8d5
ms.sourcegitcommit: 4c89d9ea4b834d1963c4818a965eaaaa288194eb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/04/2020
ms.locfileid: "96608468"
---
# <a name="onboard-a-customer-to-azure-lighthouse"></a>Onboarding eines Kunden in Azure Lighthouse durchführen

In diesem Artikel wird erläutert, wie Sie als Dienstanbieter das Onboarding eines Kunden in Azure Lighthouse durchführen können. Wenn Sie dies tun, kann Ihr eigener Azure Active Directory-Mandant (Azure AD) mithilfe der [delegierten Azure-Ressourcenverwaltung](../concepts/azure-delegated-resource-management.md) auf die delegierten Ressourcen des Kunden (Abonnements und/oder Ressourcengruppen) zugreifen und sie verwalten.

> [!TIP]
> In diesem Thema ist zwar von Dienstanbietern und Kunden die Rede, aber auch [Unternehmen, die mehrere Mandanten verwalten](../concepts/enterprise.md), können mithilfe desselben Verfahrens Azure Lighthouse einrichten und ihre Verwaltungsumgebung konsolidieren.

Sie können den Onboardingprozess für mehrere Kunden wiederholen. Wenn sich ein Benutzer mit den entsprechenden Berechtigungen bei Ihrem verwaltenden Mandanten anmeldet, kann dieser Benutzer über Kundenmandantenbereiche hinweg autorisiert werden, Verwaltungsvorgänge durchzuführen, ohne sich bei jedem einzelnen Kundenmandanten anmelden zu müssen.

Um ihre Wirksamkeit hinsichtlich der Kundenbindung nachzuverfolgen und Bekanntheit zu erlangen, ordnen Sie Ihre MPN-ID (Microsoft Partner Network) mindestens einem Benutzerkonto zu, das Zugriff auf jedes Ihrer integrierten Abonnements hat. Diese Zuordnung müssen Sie in Ihrem Dienstanbietermandanten ausführen. Wir empfehlen, in Ihrem Mandanten ein Dienstprinzipalkonto zu erstellen, das mit Ihrer MPN-ID verknüpft ist, und diesen Dienstprinzipal bei jedem Onboarding eines Kunden einzubeziehen. Weitere Informationen finden Sie im Thema zum [Verknüpfen der Partner-ID, um Partner Earned Credit (PEC) für delegierte Ressourcen zu aktivieren](partner-earned-credit.md).

> [!NOTE]
> Das Onboarding in Azure Lighthouse kann auch für Kunden durchgeführt werden, die ein Angebot für verwaltete Dienste (öffentlich oder privat) kaufen, das Sie [in Azure Marketplace veröffentlichen](publish-managed-services-offers.md). Sie können den hier beschriebenen Onboardingprozess auch zusammen mit den in Azure Marketplace veröffentlichten Angeboten verwenden.

Der Onboardingprozess erfordert, dass Aktionen sowohl innerhalb des Mandanten des Dienstanbieters als auch vom Mandanten des Kunden aus durchgeführt werden. Alle diese Schritte werden in diesem Artikel beschrieben.

## <a name="gather-tenant-and-subscription-details"></a>Sammeln von Mandanten- und Abonnementinformationen

Für das Onboarding des Mandanten eines Kunden muss dieser ein aktives Azure-Abonnement besitzen. Sie müssen Folgendes wissen:

- Die Mandanten-ID des Mandanten des Dienstanbieters (wo Sie die Ressourcen des Kunden verwalten werden)
- Die Mandanten-ID des Mandanten des Kunden (der über Ressourcen verfügt, die vom Dienstanbieter verwaltet werden)
- Die Abonnement-IDs für jedes einzelne Abonnement im Mandanten des Kunden, das vom Dienstanbieter verwaltet wird (oder das die Ressourcengruppen enthält, die vom Dienstanbieter verwaltet werden).

Wenn Sie diese ID-Werte nicht schon besitzen, können Sie sie mit einer der folgenden Methoden abrufen. Achten Sie darauf, dass Sie diese genauen Werte in Ihrer Bereitstellung verwenden.

### <a name="azure-portal"></a>Azure-Portal

Ihre Mandanten-ID können Sie anzeigen, indem Sie rechts oben im Azure-Portal auf Ihren Kontonamen zeigen, oder indem Sie **Verzeichnis wechseln** auswählen. Um Ihre Mandanten-ID auszuwählen und zu kopieren, suchen Sie im Portal nach „Azure Active Directory“, wählen Sie dann **Eigenschaften** aus, und kopieren Sie den im Feld **Verzeichnis-ID** angezeigten Wert. Um die ID eines Abonnements im Kundenmandanten zu ermitteln, suchen Sie nach „Abonnements“, und wählen Sie dann die entsprechende Abonnement-ID aus.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

Select-AzSubscription <subscriptionId>
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az account set --subscription <subscriptionId/name>
az account show
```

> [!NOTE]
> Wenn Sie ein Abonnement (oder mindestens eine Ressourcengruppe innerhalb eines Abonnements) mithilfe des hier beschriebenen Prozesses integrieren, wird der Ressourcenanbieter **Microsoft.ManagedServices** für dieses Abonnement registriert.

## <a name="define-roles-and-permissions"></a>Definieren von Rolle und Berechtigungen

Als Dienstanbieter können Sie mehrere Aufgaben für einen einzelnen Kunden ausführen, die für unterschiedliche Bereiche einen anderen Zugriff erfordern. Sie können so viele Autorisierungen definieren, wie Sie benötigen, um Benutzern in Ihrem Mandanten die entsprechenden [integrierten Azure-Rollen](../../role-based-access-control/built-in-roles.md) zuzuweisen.

Um die Verwaltung zu vereinfachen, empfiehlt es sich, Azure AD-Benutzergruppen für jede Rolle zu verwenden. Dadurch haben Sie die Flexibilität, einzelne Benutzer der Gruppe mit Zugriffsberechtigung hinzuzufügen oder daraus zu entfernen, sodass Sie den Onboardingprozess nicht wiederholen müssen, um Benutzeränderungen vorzunehmen. Sie können einem Dienstprinzipal Rollen zuweisen, was für Automatisierungsszenarien nützlich sein kann.

> [!IMPORTANT]
> Um Berechtigungen für eine Azure AD-Gruppe hinzuzufügen, muss der **Gruppentyp** auf **Sicherheit** festgelegt werden. Diese Option wird bei der Erstellung der Gruppe ausgewählt. Weitere Informationen dazu finden Sie in [Erstellen einer Basisgruppe und Hinzufügen von Mitgliedern mit Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

Achten Sie beim Definieren der Autorisierungen darauf, das Prinzip der geringsten Rechte zu befolgen, damit Benutzer nur über die Berechtigungen verfügen, die zum Durchführen ihrer Aufgaben erforderlich sind. Richtlinien und Informationen zu unterstützten Rollen finden Sie unter [Mandanten, Benutzer und Rollen in Azure Lighthouse-Szenarien](../concepts/tenants-users-roles.md).

Um Autorisierungen zu definieren, müssen Sie die ID-Werte für jeden Benutzer, jede Benutzergruppe oder jeden Dienstprinzipal im Dienstanbietermandanten kennen, dem bzw. der Sie Zugriff gewähren möchten. Außerdem benötigen Sie die Rollendefinitions-ID für jede integrierten Rolle, die Sie zuweisen möchten. Wenn Sie über diese nicht bereits verfügen, können Sie sie abrufen, indem Sie die folgenden Befehle aus dem Dienstanbietermandanten ausführen.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# To retrieve the objectId for an Azure AD group
(Get-AzADGroup -DisplayName '<yourGroupName>').id

# To retrieve the objectId for an Azure AD user
(Get-AzADUser -UserPrincipalName '<yourUPN>').id

# To retrieve the objectId for an SPN
(Get-AzADApplication -DisplayName '<appDisplayName>' | Get-AzADServicePrincipal).Id

# To retrieve role definition IDs
(Get-AzRoleDefinition -Name '<roleName>').id
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# To retrieve the objectId for an Azure AD group
az ad group list --query "[?displayName == '<yourGroupName>'].objectId" --output tsv

# To retrieve the objectId for an Azure AD user
az ad user show --id "<yourUPN>" --query "objectId" --output tsv

# To retrieve the objectId for an SPN
az ad sp list --query "[?displayName == '<spDisplayName>'].objectId" --output tsv

# To retrieve role definition IDs
az role definition list --name "<roleName>" | grep name
```

> [!TIP]
> Beim Onboarding eines Kunden empfiehlt es sich, die [Rolle „Registrierungszuweisung für verwaltete Dienste“](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) zuzuweisen, damit Benutzer in Ihrem Mandanten später bei Bedarf [den Zugriff auf die Delegierung entfernen](remove-delegation.md) können. Wenn diese Rolle nicht zugewiesen wird, können delegierte Ressourcen nur durch einen Benutzer im Kundenmandanten entfernt werden.

## <a name="create-an-azure-resource-manager-template"></a>Erstellen einer Azure Resource Manager-Vorlage

Um Ihren Kunden zu integrieren, müssen Sie eine [Azure Resource Manager](../../azure-resource-manager/index.yml)-Vorlage für das Angebot erstellen, die folgende Informationen enthält: Die Werte **mspOfferName** und **mspOfferDescription** sind für den Kunden auf der Seite [Dienstanbieter](view-manage-service-providers.md) im Azure-Portal sichtbar.

|Feld  |Definition  |
|---------|---------|
|**mspOfferName**     |Ein Name, der diese Definition beschreibt. Dieser Wert wird dem Kunden als Titel des Angebots angezeigt und muss ein eindeutiger Wert sein.        |
|**mspOfferDescription**     |Eine kurze Beschreibung Ihres Angebots (z. B. „Angebot für die Verwaltung von virtuellen Contoso-Computern“)      |
|**managedByTenantId**     |Ihre Mandanten-ID.          |
|**authorizations**     |Die **principalId**-Werte für die Benutzer/Gruppen/SPNs Ihres Mandanten, jeweils mit einem **principalIdDisplayName**-Element, um Ihrem Kunden zu helfen, den Zweck der Autorisierung zu verstehen, und einem integrierten **roleDefinitionId**-Wert zugeordnet, um die Zugriffsebene anzugeben      |

Der Onboardingvorgang erfordert eine Azure Resource Manager-Vorlage (die wir in unserem [Beispielrepository](https://github.com/Azure/Azure-Lighthouse-samples/) bereitstellen) und eine zugehörige Parameterdatei, die Sie so ändern müssen, dass sie Ihrer Konfiguration entspricht und Ihre Autorisierungen definiert.

> [!IMPORTANT]
> Der hier beschriebene Prozess erfordert für jedes Abonnement, das integriert wird, eine separate Bereitstellung. Dies gilt selbst dann, wenn Sie Abonnements im gleichen Kundenmandanten integrieren. Gesonderte Bereitstellungen sind auch erforderlich, wenn Sie mehrere Ressourcengruppen innerhalb verschiedener Abonnements im gleichen Kundenmandanten integrieren. Das Onboarding mehrerer Ressourcengruppen innerhalb eines einzelnen Abonnements kann jedoch in einer einzigen Bereitstellung erfolgen.
>
> Separate Bereitstellungen sind auch erforderlich für mehrere Angebote, die auf dasselbe Abonnement (oder dieselben Ressourcengruppen in einem Abonnement) angewendet werden. Für jedes angewendete Angebot muss ein anderer **mspOfferName** verwendet werden.

Die ausgewählte Vorlage hängt davon ab, ob Sie ein gesamtes Abonnement, eine Ressourcengruppe oder mehrere Ressourcengruppen innerhalb eines Abonnements integrieren. Wir stellen auch eine Vorlage bereit, die für Kunden verwendet werden kann, die ein Angebot für verwaltete Dienste gekauft haben, das Sie im Azure Marketplace veröffentlicht haben, wenn Sie deren Abonnement(s) lieber auf diese Weise integrieren möchten.

|So führen Sie das Onboarding durch  |Verwenden Sie diese Azure Resource Manager-Vorlage:  |Ändern Sie außerdem diese Parameterdatei: |
|---------|---------|---------|
|Subscription   |[delegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management/delegatedResourceManagement.json)  |[delegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management/delegatedResourceManagement.parameters.json)    |
|Resource group   |[rgDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.json)  |[rgDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.parameters.json)    |
|Mehrere Ressourcengruppen in einem Abonnement   |[multipleRgDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.json)  |[multipleRgDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.parameters.json)    |
|Abonnement (bei Verwendung eines in Azure Marketplace veröffentlichten Angebots)   |[marketplaceDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.json)  |[marketplaceDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.parameters.json)    |

> [!TIP]
> Sie können zwar kein Onboarding für eine gesamte Verwaltungsgruppe in einer Bereitstellung durchführen, Sie können jedoch [eine Richtlinie auf Verwaltungsgruppenebene bereitstellen](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-delegate-management-groups). Die Richtlinie überprüft, ob die einzelnen Abonnements in der Verwaltungsgruppe an den angegebenen Verwaltungsmandanten delegiert wurden. Ist dies nicht der Fall, wird die Zuweisung basierend auf den von Ihnen angegebenen Werten erstellt.

Das folgende Beispiel zeigt eine geänderte Datei **delegatedResourceManagement.parameters.json**, die für das Onboarding eines Abonnements verwendet werden kann. Die Ressourcengruppen-Parameterdateien (im Ordner [rg-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/rg-delegated-resource-management)) sind ähnlich, enthalten aber auch einen **rgName**-Parameter, um die spezifische(n) Ressourcengruppe(n) zu identifizieren, die integriert werden soll(en).

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "mspOfferName": {
            "value": "Fabrikam Managed Services - Interstellar"
        },
        "mspOfferDescription": {
            "value": "Fabrikam Managed Services - Interstellar"
        },
        "managedByTenantId": {
            "value": "df4602a3-920c-435f-98c4-49ff031b9ef6"
        },
        "authorizations": {
            "value": [
                {
                    "principalId": "0019bcfb-6d35-48c1-a491-a701cf73b419",
                    "principalIdDisplayName": "Tier 1 Support",
                    "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"
                },
                {
                    "principalId": "0019bcfb-6d35-48c1-a491-a701cf73b419",
                    "principalIdDisplayName": "Tier 1 Support",
                    "roleDefinitionId": "36243c78-bf99-498c-9df9-86d9f8d28608"
                },
                {
                    "principalId": "0afd8497-7bff-4873-a7ff-b19a6b7b332c",
                    "principalIdDisplayName": "Tier 2 Support",
                    "roleDefinitionId": "acdd72a7-3385-48ef-bd42-f606fba81ae7"
                },
                {
                    "principalId": "9fe47fff-5655-4779-b726-2cf02b07c7c7",
                    "principalIdDisplayName": "Service Automation Account",
                    "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"
                },
                {
                    "principalId": "3kl47fff-5655-4779-b726-2cf02b05c7c4",
                    "principalIdDisplayName": "Policy Automation Account",
                    "roleDefinitionId": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
                    "delegatedRoleDefinitionIds": [
                        "b24988ac-6180-42a0-ab88-20f7382dd24c",
                        "92aaf0da-9dab-42b6-94a3-d43ce8d16293"
                    ]
                }
            ]
        }
    }
}
```

Die letzte Autorisierung im obigen Beispiel fügt eine **prinzipalId** mit der Rolle „Benutzerzugriffsadministrator“ hinzu (18d7d88d-d35e-4b5-a5c3-7773c20a72d9). Wenn Sie diese Rolle zuweisen, müssen Sie die **delegatedRoleDefinitionIds**-Eigenschaft und mindestens eine integrierte Rolle einschließen. Der in dieser Autorisierung erstellte Benutzer kann diese integrierten Rollen [verwalteten Identitäten](../../active-directory/managed-identities-azure-resources/overview.md) im Kundenmandanten zuweisen. Dies ist erforderlich, um [Richtlinien bereitzustellen, die korrigiert werden können](deploy-policy-remediation.md).  Der Benutzer kann außerdem Supportfälle erstellen.  Für diesen Benutzer gelten keine anderen Berechtigungen, die normalerweise der Rolle „Benutzerzugriffsadministrator“ zugeordnet sind.

## <a name="deploy-the-azure-resource-manager-templates"></a>Bereitstellen der Azure Resource Manager-Vorlagen

Nachdem Sie Ihre Parameterdatei aktualisiert haben, muss ein Benutzer im Kundenmandanten die Azure Resource Manager-Vorlage im Mandanten bereitstellen. Eine gesonderte Bereitstellung ist für jedes Abonnement erforderlich, für das Sie ein Onboarding durchführen möchten (oder für jedes Abonnement, das Ressourcengruppen enthält, die Sie integrieren möchten).

> [!IMPORTANT]
> Diese Bereitstellung muss von einem Nicht-Gastkonto im Mandanten des Kunden durchgeführt werden, das über die [integrierte Rolle „Besitzer“](../../role-based-access-control/built-in-roles.md#owner) für das Abonnement verfügt, das integriert wird (oder das die Ressourcengruppen enthält, die integriert werden). Um alle Benutzer anzuzeigen, die das Abonnement delegieren können, kann ein Benutzer im Mandanten des Kunden das Abonnement im Azure-Portal auswählen, **Zugriffssteuerung (IAM)** öffnen und [alle Benutzer mit der Rolle „Besitzer“ anzeigen](../../role-based-access-control/role-assignments-list-portal.md#list-owners-of-a-subscription). 
>
> Wenn das Abonnement über das [CSP-Programm (Cloud Solution Provider)](../concepts/cloud-solution-provider.md) erstellt wurde, kann jeder Benutzer, der die Rolle [Administrator-Agent](/partner-center/permissions-overview#manage-commercial-transactions-in-partner-center-azure-ad-and-csp-roles) in Ihrem Dienstanbietermandanten ausübt, die Bereitstellung ausführen.

Die Bereitstellung kann im Azure-Portal, mit PowerShell oder mit der Azure CLI erfolgen, wie unten dargestellt.

### <a name="azure-portal"></a>Azure-Portal

1. Wählen Sie in unserem [GitHub-Repository](https://github.com/Azure/Azure-Lighthouse-samples/) die Schaltfläche **In Azure bereitstellen** aus, die neben der Vorlage angezeigt wird, die Sie verwenden möchten. Die Vorlage wird im Azure-Portal geöffnet.
1. Geben Sie Ihre Werte für **Msp Offer Name** (MSP-Angebotsname), **Msp Offer Description** (MSP-Angebotsbeschreibung), **Managed by Tenant Id** (Verwaltet von Mandanten-ID) und **Authorizations** (Autorisierungen) ein. Wenn Sie es vorziehen, können Sie **Parameter bearbeiten** auswählen, um Werte für `mspOfferName`, `mspOfferDescription`, `managedbyTenantId` und `authorizations` direkt in die Parameterdatei einzugeben. Achten Sie darauf, diese Werte zu aktualisieren, anstatt die Standardwerte aus der Vorlage zu verwenden.
1. Wählen Sie **Überprüfen und erstellen** und dann **Erstellen** aus.

Nach einigen Minuten müsste eine Benachrichtigung angezeigt werden, dass die Bereitstellung abgeschlossen ist.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Deploy Azure Resource Manager template using template and parameter file locally
New-AzSubscriptionDeployment -Name <deploymentName> `
                 -Location <AzureRegion> `
                 -TemplateFile <pathToTemplateFile> `
                 -TemplateParameterFile <pathToParameterFile> `
                 -Verbose

# Deploy Azure Resource Manager template that is located externally
New-AzSubscriptionDeployment -Name <deploymentName> `
                 -Location <AzureRegion> `
                 -TemplateUri <templateUri> `
                 -TemplateParameterUri <parameterUri> `
                 -Verbose
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# Deploy Azure Resource Manager template using template and parameter file locally
az deployment sub create --name <deploymentName> \
                         --location <AzureRegion> \
                         --template-file <pathToTemplateFile> \
                         --parameters <parameters/parameterFile> \
                         --verbose

# Deploy external Azure Resource Manager template, with local parameter file
az deployment sub create --name <deploymentName> \
                         --location <AzureRegion> \
                         --template-uri <templateUri> \
                         --parameters <parameterFile> \
                         --verbose
```

## <a name="confirm-successful-onboarding"></a>Bestätigen des erfolgreichen Onboardings

Wenn ein Kundenabonnement erfolgreich in Azure Lighthouse integriert wurde, können Benutzer im Mandanten des Dienstanbieters das Abonnement und seine Ressourcen sehen (wenn Ihnen mithilfe des oben geschilderten Vorgangs der Zugriff gewährt wurde, entweder einzeln oder als Mitglied einer Azure AD-Gruppe mit den entsprechenden Berechtigungen). Um dies zu bestätigen, überprüfen Sie auf eine der folgenden Arten, ob das Abonnement angezeigt wird.  

### <a name="azure-portal"></a>Azure-Portal

Im Mandanten des Dienstanbieters:

1. Navigieren Sie zur Seite [Meine Kunden](view-manage-customers.md).
2. Wählen Sie **Kunden** aus.
3. Vergewissern Sie sich, dass die Abonnements mit dem Angebotsnamen angezeigt werden, den Sie in der Resource Manager-Vorlage angegeben haben.

> [!IMPORTANT]
> Um das delegierte Abonnement in [Meine Kunden](view-manage-customers.md) anzuzeigen, muss den Benutzern im Mandanten des Dienstanbieters beim Onboarding des Abonnements die Rolle [Leser](../../role-based-access-control/built-in-roles.md#reader) (oder eine andere integrierte Rolle mit Lesezugriff) erteilt worden sein.

Im Mandanten des Kunden:

1. Navigieren Sie zur Seite [Dienstanbieter](view-manage-service-providers.md).
2. Wählen Sie **Dienstanbieterangebote** aus.
3. Vergewissern Sie sich, dass die Abonnements mit dem Angebotsnamen angezeigt werden, den Sie in der Resource Manager-Vorlage angegeben haben.

> [!NOTE]
> Nach dem Abschluss Ihrer Bereitstellung kann es noch einige Minuten dauern, bis die Aktualisierungen im Azure-Portal zu sehen sind.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

Get-AzContext

# Confirm successful onboarding for Azure Lighthouse

Get-AzManagedServicesDefinition
Get-AzManagedServicesAssignment
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az account list
```

Wenn Sie nach dem Onboarding des Kunden Änderungen vornehmen müssen, können Sie [die Delegierung aktualisieren](update-delegation.md). Sie können auch vollständig den [Zugriff auf die Delegierung entfernen](remove-delegation.md).

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie über [Mandantenübergreifende Verwaltungsmöglichkeiten](../concepts/cross-tenant-management-experience.md).
- [Anzeigen und Verwalten von Kunden](view-manage-customers.md), indem sie im Azure-Portal zu **Meine Kunden** navigieren.
