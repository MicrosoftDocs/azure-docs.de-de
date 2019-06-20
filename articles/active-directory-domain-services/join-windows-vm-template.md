---
title: Einbinden eines virtuellen Windows Server-Computers in Azure Active Directory Domain Services | Microsoft-Dokumentation
description: Binden Sie einen virtuellen Windows Server-Computer mithilfe von Azure Resource Manager-Vorlagen in eine verwaltete Domäne ein.
services: active-directory-ds
documentationcenter: ''
author: MikeStephens-MS
manager: daveba
editor: curtand
ms.assetid: 4eabfd8e-5509-4acd-86b5-1318147fddb5
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: mstephen
ms.openlocfilehash: e4ca613059e10755056616b964cc500625fef187
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66245305"
---
# <a name="join-a-windows-server-virtual-machine-to-a-managed-domain-using-a-resource-manager-template"></a>Einbinden eines virtuellen Windows Server-Computers mithilfe einer Resource Manager-Vorlage in eine verwaltete Domäne
Dieser Artikel veranschaulicht, wie Sie einen virtuellen Windows Server-Computer mithilfe von Resource Manager-Vorlagen in eine durch Azure AD Domain Services verwaltete Domäne einbinden.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Voraussetzungen
Um die in diesem Artikel beschriebenen Aufgaben auszuführen, benötigen Sie Folgendes:
1. Ein gültiges **Azure-Abonnement**.
2. Ein **Azure AD-Verzeichnis** – entweder synchronisiert mit einem lokalen Verzeichnis oder als reines Cloud-Verzeichnis
3. **Azure AD Domain Services** müssen für das Azure AD-Verzeichnis aktiviert sein. Wenn dies noch nicht der Fall ist, führen Sie alle Aufgaben im Leitfaden [Erste Schritte](create-instance.md)aus.
4. Stellen Sie sicher, dass Sie die IP-Adressen der verwalteten Domäne nicht als DNS-Server für das virtuelle Netzwerk konfiguriert haben. Weitere Informationen finden Sie unter [Aktualisieren der DNS-Einstellungen für das virtuelle Azure-Netzwerk](active-directory-ds-getting-started-dns.md).
5. Führen Sie die Schritte aus, die zum [Synchronisieren der Kennwörter für Ihre mit Azure AD Domain Services verwaltete Domäne](active-directory-ds-getting-started-password-sync.md) erforderlich sind.


## <a name="install-and-configure-required-tools"></a>Installieren und Konfigurieren der erforderlichen Tools
Sie können eine der folgenden Optionen verwenden, um die in diesem Dokument beschriebenen Schritte auszuführen:
* **Azure PowerShell:** [Installieren und konfigurieren](https://azure.microsoft.com/documentation/articles/powershell-install-configure/)
* **Azure CLI**: [Installieren und konfigurieren](https://azure.microsoft.com/documentation/articles/xplat-cli-install/)


## <a name="option-1-provision-a-new-windows-server-vm-and-join-it-to-a-managed-domain"></a>Option 1: Bereitstellen einer neuen Windows Server-VM und Einbinden der VM in eine verwaltete Domäne
**Name der Schnellstartvorlage**: [201-vm-domain-join](https://azure.microsoft.com/resources/templates/201-vm-domain-join/)

Führen Sie die folgenden Schritte aus, um einen virtuellen Windows Server-Computer bereitzustellen und in eine verwaltete Domäne einzubinden:
1. Navigieren Sie zur [Schnellstartvorlage](https://azure.microsoft.com/resources/templates/201-vm-domain-join/).
2. Klicken Sie auf Schaltfläche zum **Bereitstellen in Azure**.
3. Geben Sie auf der Seite **Benutzerdefinierte Bereitstellung** die erforderlichen Informationen zum Bereitstellen des virtuellen Computers an.
4. Wählen Sie das **Azure-Abonnement** aus, in dem der virtuelle Computer bereitgestellt werden soll. Wählen Sie dasselbe Azure-Abonnement aus, in dem Sie Azure AD Domain Services aktiviert haben.
5. Wählen Sie eine vorhandene **Ressourcengruppe** aus, oder erstellen Sie eine neue Ressourcengruppe.
6. Wählen Sie einen **Standort** aus, an dem der neue virtuelle Computer bereitgestellt wird.
7. Geben Sie in **Existing VNET Name** (Vorhandener VNET-Name) das virtuelle Netzwerk an, in dem Sie die verwaltete Domäne für Azure AD Domain Services bereitgestellt haben.
8. Geben Sie in **Existing Subnet Name** (Vorhandener Subnetzname) das Subnetz im virtuellen Netzwerk an, in dem Sie diesen virtuellen Computer bereitstellen möchten. Wählen Sie nicht das Gatewaysubnetz im virtuellen Netzwerk aus. Wählen Sie ebenfalls nicht das dedizierte Subnetz aus, in dem Ihre verwaltete Domäne bereitgestellt wird.
9. Geben Sie in **DNS Label Prefix** (DNS-Bezeichnungspräfix) den Hostnamen für den bereitzustellenden virtuellen Computer an. Beispiel: „contoso-win“.
10. Wählen Sie die geeignete **VM-Größe** für den virtuellen Computer aus.
11. Geben Sie in **Domain To Join** (Einzubindende Domäne) den DNS-Domänennamen Ihrer verwalteten Domäne an.
12. Geben Sie in **Domain Username** (Domänenbenutzername) den Benutzerkontonamen für Ihre verwaltete Domäne an, der zum Einbinden des virtuellen Computers in der verwalteten Domäne verwendet werden soll.
13. Geben Sie in **Domain Password** (Domänenkennwort) das Kennwort des Domänenbenutzerkontos an, auf das der Parameter „domainUsername“ verweist.
14. Optional: Sie können einen **Pfad der Organisationseinheit** zu einer benutzerdefinierten Organisationseinheit angeben, zu der die VM hinzugefügt wird. Wenn Sie für diesen Parameter keinen Wert angeben, wird der virtuelle Computer zur Standardorganisationseinheit **AAD DC-Computer** in der verwalteten Domäne hinzugefügt.
15. Geben Sie im Feld **VM Admin Username** (Benutzername des VM-Administrators) den Namen für ein lokales Administratorkonto für den virtuellen Computer an.
16. Geben Sie im Feld **VM Admin Password** (Kennwort des VM-Administrators) das Kennwort eines lokalen Administrators für den virtuellen Computer an. Stellen Sie ein sicheres Kennwort für den lokalen Administrator bereit, um den virtuellen Computer vor Brute-Force-Kennwortangriffen zu schützen.
17. Klicken Sie auf **Ich stimme den oben genannten Geschäftsbedingungen zu**.
18. Klicken Sie auf **Kaufen**, um den virtuellen Computer bereitzustellen.

> [!WARNING]
> **Behandeln Sie Kennwörter mit Vorsicht.**
> Die Vorlagenparameterdatei enthält Kennwörter für Domänenkonten sowie lokale Administratorkennwörter für den virtuellen Computer. Stellen Sie sicher, dass diese Datei nicht über Dateifreigaben oder andere freigegebene Speicherorte verfügbar ist. Es wird empfohlen, diese Datei zu entsorgen, sobald Sie mit der Bereitstellung der virtuellen Computer fertig sind.
>

Nachdem die Bereitstellung erfolgreich abgeschlossen wurde, wird Ihr frisch bereitgestellter virtueller Windows-Computer zur verwalteten Domäne hinzugefügt.


## <a name="option-2-join-an-existing-windows-server-vm-to-a-managed-domain"></a>Option 2: Einbinden einer vorhandenen Windows Server-VM in eine verwaltete Domäne
**Schnellstartvorlage**: [201-vm-domain-join-existing](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/)

Führen Sie die folgenden Schritte aus, um einen vorhandenen virtuellen Windows Server-Computer in eine verwaltete Domäne einzubinden:
1. Navigieren Sie zur [Schnellstartvorlage](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/).
2. Klicken Sie auf Schaltfläche zum **Bereitstellen in Azure**.
3. Geben Sie auf der Seite **Benutzerdefinierte Bereitstellung** die erforderlichen Informationen zum Bereitstellen des virtuellen Computers an.
4. Wählen Sie das **Azure-Abonnement** aus, in dem der virtuelle Computer bereitgestellt werden soll. Wählen Sie dasselbe Azure-Abonnement aus, in dem Sie Azure AD Domain Services aktiviert haben.
5. Wählen Sie eine vorhandene **Ressourcengruppe** aus, oder erstellen Sie eine neue Ressourcengruppe.
6. Wählen Sie einen **Standort** aus, an dem der neue virtuelle Computer bereitgestellt wird.
7. Geben Sie im Feld **VM List** (VM-Liste) die Namen der vorhandenen virtuellen Computer an, die in die verwaltete Domäne eingebunden werden sollen. Verwenden Sie ein Komma, um die Namen der einzelnen virtuellen Computer zu trennen. Beispiel: **contoso-web, contoso-api**.
8. Geben Sie in **Domain Join User Name** (Benutzername für Domänenbeitritt) den Benutzerkontonamen für Ihre verwaltete Domäne an, der zum Einbinden des virtuellen Computers in der verwalteten Domäne verwendet werden soll.
9. Geben Sie in **Domain Join User Password** (Benutzerkennwort für Domänenbeitritt) das Kennwort des Domänenbenutzerkontos an, auf das der Parameter „domainUsername“ verweist.
10. Geben Sie in **Domain FQDN** (Domänen-FQDN) den DNS-Domänennamen Ihrer verwalteten Domäne an.
11. Optional: Sie können einen **Pfad der Organisationseinheit** zu einer benutzerdefinierten Organisationseinheit angeben, zu der die VM hinzugefügt wird. Wenn Sie für diesen Parameter keinen Wert angeben, wird der virtuelle Computer zur Standardorganisationseinheit **AAD DC-Computer** in der verwalteten Domäne hinzugefügt.
12. Klicken Sie auf **Ich stimme den oben genannten Geschäftsbedingungen zu**.
13. Klicken Sie auf **Kaufen**, um den virtuellen Computer bereitzustellen.

> [!WARNING]
> **Behandeln Sie Kennwörter mit Vorsicht.**
> Die Vorlagenparameterdatei enthält Kennwörter für Domänenkonten sowie lokale Administratorkennwörter für den virtuellen Computer. Stellen Sie sicher, dass diese Datei nicht über Dateifreigaben oder andere freigegebene Speicherorte verfügbar ist. Es wird empfohlen, diese Datei zu entsorgen, sobald Sie mit der Bereitstellung der virtuellen Computer fertig sind.
>

Nachdem die Bereitstellung erfolgreich abgeschlossen wurde, werden die angegebenen virtuellen Windows-Computer zur verwalteten Domäne hinzugefügt.


## <a name="related-content"></a>Verwandte Inhalte
* [Übersicht über Azure PowerShell](/powershell/azure/overview)
* [Azure-Schnellstartvorlage – Domänenbeitritt für neuen virtuellen Computer](https://azure.microsoft.com/resources/templates/201-vm-domain-join/)
* [Azure-Schnellstartvorlage – Domänenbeitritt für vorhandene virtuelle Computer](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/)
* [Bereitstellen von Ressourcen mit Azure Resource Manager-Vorlagen und Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md)
