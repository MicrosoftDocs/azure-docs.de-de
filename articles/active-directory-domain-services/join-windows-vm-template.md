---
title: Einbinden eines virtuellen Windows-Computers in Azure AD DS mithilfe einer Vorlage | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Azure Resource Manager-Vorlagen verwenden können, um eine neue oder bestehende Windows Server-VM in eine mit Azure Active Directory Domain Services verwaltete Domäne einzubinden.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 4eabfd8e-5509-4acd-86b5-1318147fddb5
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: e7245e8e468ea051ee095d97cc250ad303aa80a5
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/05/2020
ms.locfileid: "96619435"
---
# <a name="join-a-windows-server-virtual-machine-to-an-azure-active-directory-domain-services-managed-domain-using-a-resource-manager-template"></a>Informationen zum Einbinden eines virtuellen Windows Server-Computers in eine mit Azure Active Directory Domain Services verwaltete Domäne

Mit einer Resource Manager-Vorlage können Sie die Bereitstellung und Konfiguration von virtuellen Azure-Computern (VMs) automatisieren. Mit diesen Vorlagen können Sie jedes Mal eine gleichbleibende Bereitstellung gewährleisten. In Vorlagen können auch Erweiterungen enthalten sein, um eine VM im Rahmen der Bereitstellung automatisch zu konfigurieren. Eine der nützlichen Erweiterungen bindet VMs in eine Domäne ein, die mit von Azure Active Directory Domain Services (Azure AD DS) verwalteten Domänen verwendet werden kann.

Dieser Artikel veranschaulicht, wie Sie eine Windows Server-VM erstellen und mithilfe von Resource Manager-Vorlagen in eine mit Azure AD DS verwaltete Domäne einbinden. Außerdem erfahren Sie, wie Sie eine vorhandene Windows Server-VM in eine Azure AD DS-Domäne einbinden.

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie die folgenden Ressourcen und Berechtigungen:

* Ein aktives Azure-Abonnement.
    * Wenn Sie kein Azure-Abonnement besitzen, [erstellen Sie ein Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Einen mit Ihrem Abonnement verknüpften Azure Active Directory-Mandanten, der entweder mit einem lokalen Verzeichnis synchronisiert oder ein reines Cloudverzeichnis ist.
    * [Erstellen Sie einen Azure Active Directory-Mandanten][create-azure-ad-tenant], oder [verknüpfen Sie ein Azure-Abonnement mit Ihrem Konto][associate-azure-ad-tenant], sofern erforderlich.
* Eine verwaltete Azure Active Directory Domain Services-Domäne, die in Ihrem Azure AD-Mandanten aktiviert und konfiguriert ist.
    * Falls Sie keine solche Domäne haben, gehen Sie wie im ersten Tutorial beschrieben vor, um eine [verwaltete Azure Active Directory Domain Services-Domäne zu erstellen und zu konfigurieren][create-azure-ad-ds-instance].
* Ein Benutzerkonto, das Teil der verwalteten Domäne ist.

## <a name="azure-resource-manager-template-overview"></a>Übersicht über Azure Resource Manager-Vorlagen

Mithilfe von Resource Manager-Vorlagen können Sie Azure-Infrastruktur in Code definieren. Die erforderlichen Ressourcen, Netzwerkverbindungen oder Konfigurationen von VMs können in einer Vorlage definiert werden. Mithilfe dieser Vorlagen werden jedes Mal gleichbleibende, reproduzierbare Bereitstellungen erstellt, die bei Änderungen mit einer Versionsangabe versehen werden können. Weitere Informationen finden Sie in der Übersicht über [Azure Resource Manager-Vorlagen][template-overview].

Die einzelnen Ressourcen werden in einer Vorlage mithilfe von JavaScript Object Notation (JSON) definiert. Im folgenden JSON-Beispiel wird der Ressourcentyp *Microsoft.Compute/virtualMachines/extensions* verwendet, um die Erweiterung für das Einbinden in die Active Directory-Domäne zu installieren. Es werden Parameter verwendet, die Sie zum Zeitpunkt der Bereitstellung angeben. Bei der Bereitstellung der Erweiterung wird die VM in die angegebene verwaltete Domäne eingebunden.

```json
 {
      "apiVersion": "2015-06-15",
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "[concat(parameters('dnsLabelPrefix'),'/joindomain')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', parameters('dnsLabelPrefix'))]"
      ],
      "properties": {
        "publisher": "Microsoft.Compute",
        "type": "JsonADDomainExtension",
        "typeHandlerVersion": "1.3",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "Name": "[parameters('domainToJoin')]",
          "OUPath": "[parameters('ouPath')]",
          "User": "[concat(parameters('domainToJoin'), '\\', parameters('domainUsername'))]",
          "Restart": "true",
          "Options": "[parameters('domainJoinOptions')]"
        },
        "protectedSettings": {
          "Password": "[parameters('domainPassword')]"
        }
      }
    }
```

Diese VM-Erweiterung kann auch dann bereitgestellt werden, wenn Sie in derselben Vorlage keine VM erstellen. In den Beispielen in diesem Artikel werden die beiden folgenden Ansätze veranschaulicht:

* [Erstellen und Einbinden einer Windows Server-VM in eine verwaltete Domäne](#create-a-windows-server-vm-and-join-to-a-managed-domain)
* [Einbinden einer vorhandenen Windows Server-VM in eine verwaltete Domäne](#join-an-existing-windows-server-vm-to-a-managed-domain)

## <a name="create-a-windows-server-vm-and-join-to-a-managed-domain"></a>Erstellen und Einbinden einer Windows Server-VM in eine verwaltete Domäne

Wenn Sie eine Windows Server-VM benötigen, können Sie sie mithilfe einer Ressourcen-Manager Vorlage erstellen und konfigurieren. Bei der Bereitstellung der VM wird dann eine Erweiterung installiert, um die VM in eine verwaltete Domäne einzubinden. Wenn Sie bereits über eine VM verfügen, die Sie in eine verwaltete Domäne einbinden möchten, fahren Sie mit dem Abschnitt [Einbinden einer vorhandenen Windows Server-VM in eine verwaltete Domäne](#join-an-existing-windows-server-vm-to-a-managed-domain) fort.

Führen Sie die folgenden Schritte aus, um eine Windows Server-VM zu erstellen und anschließend in eine verwaltete Domäne einzubinden:

1. Navigieren Sie zur [Schnellstartvorlage](https://azure.microsoft.com/resources/templates/201-vm-domain-join/). Wählen Sie die Option **Bereitstellung in Azure** aus.
1. Geben Sie auf der Seite **Benutzerdefinierte Bereitstellung** die folgenden Informationen ein, um eine Windows Server-VM zu erstellen und in die verwaltete Domäne einzubinden:

    | Einstellung                   | Wert |
    |---------------------------|-------|
    | Subscription              | Wählen Sie dasselbe Azure-Abonnement aus, in dem Sie Azure AD Domain Services aktiviert haben. |
    | Resource group            | Wählen Sie die Ressourcengruppe für Ihre VM aus. |
    | Standort                  | Wählen Sie den Standort für Ihre VM aus. |
    | Name des vorhandenen VNet        | Der Name des vorhandenen virtuellen Netzwerks, mit dem die VM verbunden werden soll, z. B. *myVnet*. |
    | Name des vorhandenen Subnetzes      | Der Name des vorhandenen Subnetzes des virtuellen Netzwerks, z. B. *Workloads*. |
    | Präfix der DNS-Bezeichnung          | Geben Sie einen DNS-Namen für die VM ein, z. B. *myvm*. |
    | Größe des virtuellen Computers                   | Geben Sie eine VM-Größe an, z. B. *Standard_DS2_v2*. |
    | Domäne für den Beitritt            | Der DNS-Name der verwalteten Domäne, z. B. *aaddscontoso.com*. |
    | Domänenbenutzername           | Das Benutzerkonto in der verwalteten Domäne, das zum Einbinden der VM in die verwaltete Domäne verwendet werden soll, z. B. `contosoadmin@aaddscontoso.com`. Dieses Konto muss Teil der verwalteten Domäne sein. |
    | Domänenkennwort           | Das Kennwort des Benutzerkontos, das in der vorherigen Einstellung angegeben wurde. |
    | Optionaler OU-Pfad          | Die benutzerdefinierte Organisationseinheit, der die VM hinzugefügt wird. Wenn Sie für diesen Parameter keinen Wert angeben, wird die VM der Standardorganisationseinheit *AAD DC-Computer* hinzugefügt. |
    | Administratorbenutzername der VM         | Geben Sie ein lokales Administratorkonto an, das für die VM erstellt werden soll. |
    | Administratorkennwort der VM         | Geben Sie das Kennwort des lokalen Administrators für die VM an. Wählen Sie für den lokalen Administrator als Schutz vor Brute-Force-Kennwortangriffen ein sicheres Kennwort. |

1. Lesen Sie die allgemeinen Geschäftsbedingungen, und aktivieren Sie dann das Kontrollkästchen **Ich stimme den oben genannten Geschäftsbedingungen zu**. Wenn Sie bereit sind, wählen Sie **Kaufen** aus, um die VM zu erstellen und in die verwaltete Domäne einzubinden.

> [!WARNING]
> **Behandeln Sie Kennwörter mit Vorsicht.**
> Die Vorlagenparameterdatei fordert das Kennwort für ein Benutzerkonto an, das Teil der verwalteten Domäne ist. Geben Sie in diese Datei keine Werte manuell ein, und machen Sie sie auf Dateifreigaben oder anderen freigegebenen Speicherorten zugänglich.

Es dauert einige Minuten, bis die Bereitstellung erfolgreich abgeschlossen ist. Nach Abschluss des Vorgangs ist die Windows-VM erstellt und in die verwaltete Domäne eingebunden. Mithilfe von Domänenkonten ist die Verwaltung oder Anmeldung bei der VM möglich.

## <a name="join-an-existing-windows-server-vm-to-a-managed-domain"></a>Einbinden einer vorhandenen Windows Server-VM in eine verwaltete Domäne

Wenn Sie über eine vorhandene VM oder Gruppe von VMs verfügen, die Sie in eine verwaltete Domäne einbinden möchten, können Sie eine Resource Manager-Vorlage verwenden, um nur die VM-Erweiterung bereitzustellen.

Führen Sie die folgenden Schritte aus, um eine vorhandene Windows Server-VM in eine verwaltete Domäne einzubinden:

1. Navigieren Sie zur [Schnellstartvorlage](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/). Wählen Sie die Option **Bereitstellung in Azure** aus.
1. Geben Sie auf der Seite **Benutzerdefinierte Bereitstellung** die folgenden Informationen ein, um die VM in eine verwaltete Domäne einzubinden:

    | Einstellung                   | Wert |
    |---------------------------|-------|
    | Subscription              | Wählen Sie dasselbe Azure-Abonnement aus, in dem Sie Azure AD Domain Services aktiviert haben. |
    | Resource group            | Wählen Sie die Ressourcengruppe mit Ihrer vorhandenen VM aus. |
    | Standort                  | Wählen Sie den Standort Ihrer vorhandenen VM aus. |
    | VM-Liste                   | Geben Sie die durch Trennzeichen getrennte Liste mit den vorhandenen VMs ein, die in die verwaltete Domäne eingebunden werden sollen, z. B. *myVM1,myVM2*. |
    | Benutzername für Domänenbeitritt     | Das Benutzerkonto in der verwalteten Domäne, das zum Einbinden der VM in die verwaltete Domäne verwendet werden soll, z. B. `contosoadmin@aaddscontoso.com`. Dieses Konto muss Teil der verwalteten Domäne sein. |
    | Benutzerkennwort für Domänenbeitritt | Das Kennwort des Benutzerkontos, das in der vorherigen Einstellung angegeben wurde. |
    | Optionaler OU-Pfad          | Die benutzerdefinierte Organisationseinheit, der die VM hinzugefügt wird. Wenn Sie für diesen Parameter keinen Wert angeben, wird die VM der Standardorganisationseinheit *AAD DC-Computer* hinzugefügt. |

1. Lesen Sie die allgemeinen Geschäftsbedingungen, und aktivieren Sie dann das Kontrollkästchen **Ich stimme den oben genannten Geschäftsbedingungen zu**. Wenn Sie bereit sind, wählen Sie **Kaufen** aus, um die VM in die verwaltete Domäne einzubinden.

> [!WARNING]
> **Behandeln Sie Kennwörter mit Vorsicht.**
> Die Vorlagenparameterdatei fordert das Kennwort für ein Benutzerkonto an, das Teil der verwalteten Domäne ist. Geben Sie in diese Datei keine Werte manuell ein, und machen Sie sie auf Dateifreigaben oder anderen freigegebenen Speicherorten zugänglich.

Es dauert einige Augenblicke, bis die Bereitstellung erfolgreich abgeschlossen ist. Nach Abschluss des Vorgangs sind die angegebenen Windows-VMs in die verwaltete Domäne eingebunden. Über Domänenkonten ist dann die Verwaltung oder Anmeldung möglich.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie im Azure-Portal Ressourcen mithilfe von Vorlagen konfiguriert und bereitgestellt. Sie können Resource Manager-Vorlagen auch mithilfe von [Azure PowerShell][deploy-powershell] oder der [Azure CLI][deploy-cli] bereitstellen.

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[template-overview]: ../azure-resource-manager/templates/overview.md
[deploy-powershell]: ../azure-resource-manager/templates/deploy-powershell.md
[deploy-cli]: ../azure-resource-manager/templates/deploy-cli.md
