---
title: Aktivieren von Azure Active Directory Domain Services mithilfe einer Vorlage | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Azure Active Directory Domain Services mithilfe einer Azure Resource Manager-Vorlage konfigurieren und aktivieren
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: sample
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: e18825da64d0d200f55ce72985ac843b93b1e612
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/05/2020
ms.locfileid: "96618789"
---
# <a name="create-an-azure-active-directory-domain-services-managed-domain-using-an-azure-resource-manager-template"></a>Erstellen einer verwalteten Azure Active Directory Domain Services-Domäne mithilfe einer Resource Manager-Vorlage

Azure Active Directory Domain Services (Azure AD DS) stellt verwaltete Domänendienste bereit, z. B. Domänenbeitritt, Gruppenrichtlinie, LDAP und Kerberos-/NTLM-Authentifizierung, die mit Windows Server Active Directory vollständig kompatibel sind. Sie können diese Domänendienste nutzen, ohne selbst Domänencontroller bereitstellen, verwalten und patchen zu müssen. Azure AD DS lässt sich in Ihren vorhandenen Azure AD-Mandanten integrieren. Dank dieser Integration können Benutzer sich mit ihren Unternehmensanmeldeinformationen anmelden, und Sie können vorhandene Gruppen und Benutzerkonten verwenden, um den Zugriff auf Ressourcen zu sichern.

In diesem Artikel wird beschrieben, wie Sie eine verwaltete Domäne mithilfe einer Azure Resource Manager-Vorlage erstellen. Unterstützende Ressourcen werden mithilfe von Azure PowerShell erstellt.

## <a name="prerequisites"></a>Voraussetzungen

Damit Sie die Anweisungen in diesem Artikel ausführen können, benötigen Sie folgende Ressourcen:

* Installieren und konfigurieren Sie Azure PowerShell.
    * Führen Sie bei Bedarf die Anweisungen zum [Installieren des Azure PowerShell-Moduls und Verbinden mit Ihrem Azure-Abonnement](/powershell/azure/install-az-ps) aus.
    * Stellen Sie sicher, dass Sie sich mit dem Cmdlet [Connect-AzAccount][Connect-AzAccount] bei Ihrem Azure-Abonnement anmelden.
* Installieren und konfigurieren Sie Azure AD PowerShell.
    * Führen Sie bei Bedarf die Anweisungen zum [Installieren des Azure AD PowerShell-Moduls und Verbinden mit Azure AD](/powershell/azure/active-directory/install-adv2) aus.
    * Stellen Sie sicher, dass Sie sich mit dem Cmdlet [Connect-AzureAD][Connect-AzureAD] bei Ihrem Azure AD-Mandanten anmelden.
* Sie benötigen Berechtigungen als *globaler Administrator* in Ihrem Azure AD-Mandanten, um Azure AD DS zu aktivieren.
* Sie benötigen Berechtigungen als *Mitwirkender* in Ihrem Azure-Abonnement, um die erforderlichen Azure AD DS-Ressourcen zu erstellen.

## <a name="dns-naming-requirements"></a>DNS-Benennungsanforderungen

Beim Erstellen einer verwalteten Azure AD DS-Domäne geben Sie einen DNS-Namen an. Bei der Auswahl dieses DNS-Namens sind folgende Aspekte zu berücksichtigen:

* **Integrierter Domänenname:** Standardmäßig wird der integrierte Domänenname des Verzeichnisses verwendet (das Suffix *.onmicrosoft.com*). Wenn Sie Secure LDAP für den Zugriff auf die verwaltete Domäne über das Internet aktivieren möchten, können Sie kein digitales Zertifikat erstellen, um die Verbindung mit dieser Standarddomäne zu sichern. Die Domäne *.onmicrosoft.com* ist im Besitz von Microsoft, daher stellt keine Zertifizierungsstelle ein Zertifikat aus.
* **Benutzerdefinierte Domänennamen:** Die gängigste Vorgehensweise besteht darin, einen benutzerdefinierten Domänennamen anzugeben – in der Regel den Namen einer Domäne, die Sie bereits besitzen und die routingfähig ist. Wenn Sie eine routingfähige benutzerdefinierte Domäne verwenden, kann der Datenverkehr ordnungsgemäß und bedarfsgerecht weitergeleitet werden, um Ihre Anwendungen zu unterstützen.
* **Nicht routingfähige Domänensuffixe:** Im Allgemeinen wird empfohlen, nicht routingfähige Domänennamesuffixe wie z. B. *contoso.local* zu vermeiden. Das Suffix *.local* ist nicht routingfähig und kann zu Problemen mit der DNS-Auflösung führen.

> [!TIP]
> Lassen Sie beim Erstellen eines benutzerdefinierten Domänennamens Vorsicht in Bezug auf DNS-Namespaces walten. Es wird empfohlen, einen Domänennamen zu verwenden, der von vorhandenen Azure- oder lokalen DNS-Namespaces getrennt ist.
>
> Lautet Ihr vorhandener DNS-Namespace beispielsweise *contoso.com*, erstellen Sie eine verwaltete Domäne mit dem benutzerdefinierten Domänennamen *aaddscontoso.com*. Wenn Sie Secure LDAP verwenden, müssen Sie diesen benutzerdefinierten Domänennamen registrieren und sein Besitzer sein, um die erforderlichen Zertifikate generieren zu können.
>
> Möglicherweise müssen Sie einige zusätzliche DNS-Einträge für andere Dienste in Ihrer Umgebung oder bedingte DNS-Weiterleitungen zwischen vorhandenen DNS-Namespaces in Ihrer Umgebung erstellen. Beispiel: Wenn Sie einen Webserver ausführen, der unter Verwendung des DNS-Stammnamens eine Website hostet, können Namenskonflikte auftreten, aufgrund derer zusätzliche DNS-Einträge erforderlich sind.
>
> In diesem Beispiel und in diesen Anleitungsartikeln wird die benutzerdefinierte Domäne *aaddscontoso.com* als kurzes Beispiel verwendet. Geben Sie in allen Befehlen Ihren eigenen Domänennamen an.

Es gelten außerdem die folgenden Einschränkungen für DNS-Namen:

* **Einschränkungen für Domänenpräfixe:** Sie können keine verwaltete Domäne mit einem Präfix erstellen, das länger ist als 15 Zeichen. Das Präfix des angegebenen Domänennamens (beispielsweise *aaddscontoso* im Domänennamen *aaddscontoso.com*) darf maximal 15 Zeichen lang sein.
* **Netzwerknamenskonflikte:** Der DNS-Domänenname für Ihre verwaltete Domäne darf im virtuellen Netzwerk noch nicht vorhanden sein. Achten Sie speziell auf die folgenden Szenarien, die zu einem Namenskonflikt führen würden:
    * Im virtuellen Azure-Netzwerk ist bereits eine Active Directory-Domäne mit dem gleichen DNS-Domänennamen vorhanden.
    * Das virtuelle Netzwerk, in dem Sie die verwaltete Domäne aktivieren möchten, verfügt über eine VPN-Verbindung mit Ihrem lokalen Netzwerk. In diesem Szenario stellen Sie sicher, dass Sie keine Domäne mit demselben DNS-Domänennamen in Ihrem lokalen Netzwerk haben.
    * Im virtuellen Azure-Netzwerk ist bereits ein Azure-Clouddienst mit diesem Namen vorhanden.

## <a name="create-required-azure-ad-resources"></a>Erstellen der erforderlichen Azure AD-Ressourcen

Azure AD DS erfordert einen Dienstprinzipal und eine Azure AD-Gruppe. Mit diesen Ressourcen kann die verwaltete Domäne Daten synchronisieren und definieren, welche Benutzer über Administratorrechte in der verwalteten Domäne verfügen.

Registrieren Sie zunächst den Azure AD Domain Services-Ressourcenanbieter mithilfe des Cmdlets [Register-AzResourceProvider][Register-AzResourceProvider]:

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.AAD
```

Erstellen Sie mit dem Cmdlet [New-AzureADServicePrincipal][New-AzureADServicePrincipal] für Azure AD DS einen Azure AD-Dienstprinzipal für die Kommunikation und Authentifizierung. Es wird eine bestimmte Anwendungs-ID namens *Domain Controller Services* mit der ID *6ba9a5d4-8456-4118-b521-9c5ca10cdf84* verwendet. Ändern Sie diese Anwendungs-ID nicht.

```powershell
New-AzureADServicePrincipal -AppId "6ba9a5d4-8456-4118-b521-9c5ca10cdf84"
```

Erstellen Sie nun mit dem Cmdlet [New-AzureADGroup][New-AzureADGroup] eine Azure AD-Gruppe namens *AAD DC-Administratoren*. Den dieser Gruppe hinzugefügten Benutzern werden dann Berechtigungen zum Ausführen von Verwaltungsaufgaben für die verwaltete Domäne erteilt.

```powershell
New-AzureADGroup -DisplayName "AAD DC Administrators" `
  -Description "Delegated group to administer Azure AD Domain Services" `
  -SecurityEnabled $true -MailEnabled $false `
  -MailNickName "AADDCAdministrators"
```

Fügen Sie, nachdem Sie die Gruppe *AAD DC Administrators* erstellt haben, mit dem Cmdlet [Add-AzureADGroupMember][Add-AzureADGroupMember] einen Benutzer zur Gruppe hinzu. Zuerst rufen Sie mit dem Cmdlet [Get-AzureADGroup][Get-AzureADGroup] die Objekt-ID der Gruppe *AAD DC Administrators* und dann mit dem Cmdlet [Get-AzureADUser][Get-AzureADUser] die Objekt-ID des gewünschten Benutzers ab.

Das folgende Beispiel zeigt die Objekt-ID des Benutzers für das Konto mit dem Benutzerprinzipalnamen `admin@contoso.onmicrosoft.com`. Ersetzen Sie dieses Benutzerkonto durch den Benutzerprinzipalnamen des Benutzers, den Sie der Gruppe *AAD DC Administrators* hinzufügen möchten:

```powershell
# First, retrieve the object ID of the newly created 'AAD DC Administrators' group.
$GroupObjectId = Get-AzureADGroup `
  -Filter "DisplayName eq 'AAD DC Administrators'" | `
  Select-Object ObjectId

# Now, retrieve the object ID of the user you'd like to add to the group.
$UserObjectId = Get-AzureADUser `
  -Filter "UserPrincipalName eq 'admin@contoso.onmicrosoft.com'" | `
  Select-Object ObjectId

# Add the user to the 'AAD DC Administrators' group.
Add-AzureADGroupMember -ObjectId $GroupObjectId.ObjectId -RefObjectId $UserObjectId.ObjectId
```

Erstellen Sie schließlich mit dem Cmdlet [New-AzResourceGroup][New-AzResourceGroup] eine Ressourcengruppe. Im folgenden Beispiel wird eine Ressourcengruppe mit dem Namen *myResourceGroup* in der Region *westus* erstellt. Verwenden Sie Ihren eigenen Namen und die gewünschte Region:

```powershell
New-AzResourceGroup `
  -Name "myResourceGroup" `
  -Location "WestUS"
```

Wenn Sie eine Region mit Unterstützung von Verfügbarkeitszonen auswählen, werden die Azure AD DS-Ressourcen auf mehrere Zonen verteilt, um zusätzliche Redundanz zu erzielen. Verfügbarkeitszonen sind eindeutige physische Standorte in einer Azure-Region. Jede Zone besteht aus mindestens einem Rechenzentrum, dessen Stromversorgung, Kühlung und Netzwerkbetrieb unabhängig funktionieren. Zur Gewährleistung der Resilienz sind in allen aktivierten Regionen mindestens drei separate Zonen vorhanden.

Für die Verteilung auf Zonen für Azure AD DS fällt für Sie kein Konfigurationsaufwand an. Die Verteilung der Ressourcen auf Zonen wird von der Azure-Plattform automatisch durchgeführt. Weitere Informationen, z. B. zur regionalen Verfügbarkeit, finden Sie unter [Was sind Verfügbarkeitszonen in Azure?][availability-zones].

## <a name="resource-definition-for-azure-ad-ds"></a>Ressourcendefinition für Azure AD DS

Für die Resource Manager-Ressourcendefinition sind u. a. die folgenden Konfigurationsparameter erforderlich:

| Parameter               | Wert |
|-------------------------|---------|
| domainName              | Geben Sie den DNS-Domänennamen für Ihre verwaltete Domäne ein, und berücksichtigen Sie dabei die obigen Ausführungen zu Namenspräfixen und Konflikten. |
| filteredSync            | Mit Azure AD DS können Sie *alle* in Azure AD verfügbaren Benutzer und Gruppen synchronisieren oder eine *bereichsbezogene* Synchronisierung spezifischer Gruppen durchführen.<br /><br /> Weitere Informationen zur bereichsbezogenen Synchronisierung finden Sie unter [Bereichsbezogene Synchronisierung für Azure AD Domain Services][scoped-sync].|
| notificationSettings    | Wenn in der verwalteten Domäne Warnungen generiert werden, können E-Mail-Benachrichtigungen gesendet werden. <br /><br />*Globale Administratoren* des Azure-Mandanten und Mitglieder der Gruppe *AAD DC-Administratoren* können für diese Benachrichtigungen *aktiviert* werden.<br /><br /> Bei Bedarf können Sie weitere Empfänger von Benachrichtigungen hinzufügen, wenn für Warnungen Maßnahmen erforderlich sind.|
| domainConfigurationType | Eine verwaltete Domäne wird standardmäßig als *Benutzergesamtstruktur* erstellt. Mit dieser Art von Gesamtstruktur werden alle Objekte aus Azure AD synchronisiert – einschließlich aller in einer lokalen AD DS-Umgebung erstellten Benutzerkonten. Den Wert *domainConfiguration* müssen Sie nicht angeben, um eine Benutzergesamtstruktur zu erstellen.<br /><br /> Eine *Ressourcengesamtstruktur* synchronisiert nur Benutzer und Gruppen, die direkt in Azure AD erstellt werden. Legen Sie den Wert auf *ResourceTrusting* fest, um eine Ressourcengesamtstruktur zu erstellen.<br /><br />Weitere Informationen zu *Ressourcengesamtstrukturen* finden Sie in der [Übersicht zu Azure AD DS-Ressourcengesamtstrukturen][resource-forests]. Dort werden u. a. die Gründe für ihre Verwendung sowie die Vorgehensweise zum Erstellen von Gesamtstrukturvertrauensstellungen mit lokalen AD DS-Domänen erläutert.|

Die folgende Parameterdefinition in Kurzform zeigt, wie diese Werte deklariert werden. Eine Benutzergesamtstruktur namens *aaddscontoso.com* wird erstellt, wobei alle Benutzer von Azure AD mit der verwalteten Domäne synchronisiert werden:

```json
"parameters": {
    "domainName": {
        "value": "aaddscontoso.com"
    },
    "filteredSync": {
        "value": "Disabled"
    },
    "notificationSettings": {
        "value": {
            "notifyGlobalAdmins": "Enabled",
            "notifyDcAdmins": "Enabled",
            "additionalRecipients": []
        }
    },
    [...]
}
```

Der folgende Ressourcentyp der Resource Manager-Vorlage (komprimiert) wird dann verwendet, um die verwaltete Domäne zu definieren und zu erstellen. Ein virtuelles Azure-Netzwerk und ein Subnetz müssen bereits vorhanden sein oder als Teil der Resource Manager-Vorlage erstellt werden. Die verwaltete Domäne wird mit diesem Subnetz verbunden.

```json
"resources": [
    {
        "apiVersion": "2017-06-01",
        "type": "Microsoft.AAD/DomainServices",
        "name": "[parameters('domainName')]",
        "location": "[parameters('location')]",
        "dependsOn": [
            "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]"
        ],
        "properties": {
            "domainName": "[parameters('domainName')]",
            "subnetId": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', parameters('vnetName'), '/subnets/', parameters('subnetName'))]",
            "filteredSync": "[parameters('filteredSync')]",
            "notificationSettings": "[parameters('notificationSettings')]"
        }
    },
    [...]
]
```

Diese Parameter und der Ressourcentyp können als Teil einer umfassenderen Resource Manager-Vorlage zum Bereitstellen einer verwalteten Domäne verwendet werden, wie im folgenden Abschnitt gezeigt.

## <a name="create-a-managed-domain-using-sample-template"></a>Erstellen einer verwalteten Domäne mithilfe einer Beispielvorlage

Mit der folgenden vollständigen Resource Manager-Beispielvorlage werden eine verwaltete Domäne sowie das unterstützende virtuelle Netzwerk, das Subnetz und Regeln für Netzwerksicherheitsgruppen erstellt. Die Regeln für Netzwerksicherheitsgruppen sind erforderlich, um die verwaltete Domäne zu schützen und einen ordnungsgemäßen Datenverkehrsfluss zu gewährleisten. Eine Benutzergesamtstruktur mit dem DNS-Namen *aaddscontoso.com* wird erstellt, wobei alle Benutzer von Azure AD synchronisiert werden:

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "apiVersion": {
            "value": "2017-06-01"
        },
        "domainConfigurationType": {
            "value": "FullySynced"
        },
        "domainName": {
            "value": "aaddscontoso.com"
        },
        "filteredSync": {
            "value": "Disabled"
        },
        "location": {
            "value": "westus"
        },
        "notificationSettings": {
            "value": {
                "notifyGlobalAdmins": "Enabled",
                "notifyDcAdmins": "Enabled",
                "additionalRecipients": []
            }
        },
        "subnetName": {
            "value": "aadds-subnet"
        },
        "vnetName": {
            "value": "aadds-vnet"
        },
        "vnetAddressPrefixes": {
            "value": [
                "10.1.0.0/24"
            ]
        },
        "subnetAddressPrefix": {
            "value": "10.1.0.0/24"
        },
        "nsgName": {
            "value": "aadds-nsg"
        }
    },
    "resources": [
        {
            "apiVersion": "2017-06-01",
            "type": "Microsoft.AAD/DomainServices",
            "name": "[parameters('domainName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]"
            ],
            "properties": {
                "domainName": "[parameters('domainName')]",
                "subnetId": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', parameters('vnetName'), '/subnets/', parameters('subnetName'))]",
                "filteredSync": "[parameters('filteredSync')]",
                "domainConfigurationType": "[parameters('domainConfigurationType')]",
                "notificationSettings": "[parameters('notificationSettings')]"
            }
        },
        {
            "type": "Microsoft.Network/NetworkSecurityGroups",
            "name": "[parameters('nsgName')]",
            "location": "[parameters('location')]",
            "properties": {
                "securityRules": [
                    {
                        "name": "AllowSyncWithAzureAD",
                        "properties": {
                            "access": "Allow",
                            "priority": 101,
                            "direction": "Inbound",
                            "protocol": "Tcp",
                            "sourceAddressPrefix": "AzureActiveDirectoryDomainServices",
                            "sourcePortRange": "*",
                            "destinationAddressPrefix": "*",
                            "destinationPortRange": "443"
                        }
                    },
                    {
                        "name": "AllowPSRemoting",
                        "properties": {
                            "access": "Allow",
                            "priority": 301,
                            "direction": "Inbound",
                            "protocol": "Tcp",
                            "sourceAddressPrefix": "AzureActiveDirectoryDomainServices",
                            "sourcePortRange": "*",
                            "destinationAddressPrefix": "*",
                            "destinationPortRange": "5986"
                        }
                    },
                    {
                        "name": "AllowRD",
                        "properties": {
                            "access": "Allow",
                            "priority": 201,
                            "direction": "Inbound",
                            "protocol": "Tcp",
                            "sourceAddressPrefix": "CorpNetSaw",
                            "sourcePortRange": "*",
                            "destinationAddressPrefix": "*",
                            "destinationPortRange": "3389"
                        }
                    }
                ]
            },
            "apiVersion": "2018-04-01"
        },
        {
            "type": "Microsoft.Network/virtualNetworks",
            "name": "[parameters('vnetName')]",
            "location": "[parameters('location')]",
            "apiVersion": "2018-04-01",
            "dependsOn": [
                "[concat('Microsoft.Network/NetworkSecurityGroups/', parameters('nsgName'))]"
            ],
            "properties": {
                "addressSpace": {
                    "addressPrefixes": "[parameters('vnetAddressPrefixes')]"
                },
                "subnets": [
                    {
                        "name": "[parameters('subnetName')]",
                        "properties": {
                            "addressPrefix": "[parameters('subnetAddressPrefix')]",
                            "networkSecurityGroup": {
                                "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/NetworkSecurityGroups/', parameters('nsgName'))]"
                            }
                        }
                    }
                ]
            }
        }
    ],
    "outputs": {}
}
```

Diese Vorlage kann über Ihre bevorzugte Bereitstellungsmethode (z B. [Azure-Portal][portal-deploy], [Azure PowerShell][powershell-deploy] oder CI/CD-Pipeline) implementiert werden. Im folgenden Beispiel wird das Cmdlet [New-AzResourceGroupDeployment][New-AzResourceGroupDeployment] verwendet. Geben Sie Ihren eigenen Namen für die Ressourcengruppe und einen Dateiname für die Vorlage an:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myResourceGroup" -TemplateFile <path-to-template>
```

Es dauert einige Minuten, bis die Ressource erstellt und die Steuerung an die PowerShell-Eingabeaufforderung zurückgegeben wird. Die verwaltete Domäne wird weiterhin im Hintergrund bereitgestellt, und es kann bis zu einer Stunde dauern, bis die Bereitstellung abgeschlossen ist. In dieser Bereitstellungsphase wird im Azure-Portal auf der Seite **Übersicht** für Ihre verwaltete Domäne der aktuelle Status angezeigt.

Wenn im Azure-Portal angezeigt wird, dass die Bereitstellung der verwalteten Domäne abgeschlossen ist, müssen Sie die folgenden Aufgaben ausführen:

* Aktualisieren Sie DNS-Einstellungen für das virtuelle Netzwerk, damit virtuelle Computer die verwaltete Domäne für den Domänenbeitritt oder die Domänenauthentifizierung finden können.
    * Wählen Sie zum Konfigurieren von DNS Ihre verwaltete Domäne im Portal aus. Im Fenster **Übersicht** werden Sie aufgefordert, diese DNS-Einstellungen automatisch zu konfigurieren.
* [Aktivieren Sie die Kennwortsynchronisierung für Azure AD DS](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds), damit sich Endbenutzer mit ihren Unternehmensanmeldeinformationen bei der verwalteten Domäne anmelden können.

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie die verwaltete Domäne in Aktion sehen möchten, können Sie [einen virtuellen Windows-Computer in die Domäne einbinden][windows-join], [sicheres LDAP konfigurieren][tutorial-ldaps] und die [Kennworthashsynchronisierung konfigurieren][tutorial-phs].

<!-- INTERNAL LINKS -->
[windows-join]: join-windows-vm.md
[tutorial-ldaps]: tutorial-configure-ldaps.md
[tutorial-phs]: tutorial-configure-password-hash-sync.md
[availability-zones]: ../availability-zones/az-overview.md
[portal-deploy]: ../azure-resource-manager/templates/deploy-portal.md
[powershell-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[scoped-sync]: scoped-synchronization.md
[resource-forests]: concepts-resource-forest.md

<!-- EXTERNAL LINKS -->
[Connect-AzAccount]: /powershell/module/Az.Accounts/Connect-AzAccount
[Connect-AzureAD]: /powershell/module/AzureAD/Connect-AzureAD
[New-AzureADServicePrincipal]: /powershell/module/AzureAD/New-AzureADServicePrincipal
[New-AzureADGroup]: /powershell/module/AzureAD/New-AzureADGroup
[Add-AzureADGroupMember]: /powershell/module/AzureAD/Add-AzureADGroupMember
[Get-AzureADGroup]: /powershell/module/AzureAD/Get-AzureADGroup
[Get-AzureADUser]: /powershell/module/AzureAD/Get-AzureADUser
[Register-AzResourceProvider]: /powershell/module/Az.Resources/Register-AzResourceProvider
[New-AzResourceGroup]: /powershell/module/Az.Resources/New-AzResourceGroup
[Get-AzSubscription]: /powershell/module/Az.Accounts/Get-AzSubscription
[cloud-shell]: ../cloud-shell/cloud-shell-windows-users.md
[naming-prefix]: /windows-server/identity/ad-ds/plan/selecting-the-forest-root-domain
[New-AzResourceGroupDeployment]: /powershell/module/Az.Resources/New-AzResourceGroupDeployment