---
title: Beheben von Verzeichniskonfliktfehlern in Azure AD Domain Services | Microsoft-Dokumentation
description: Erfahren Sie, was ein Verzeichniskonfliktfehler bedeutet und wie Sie ihn in Azure AD Domain Services beheben können.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 40eb75b7-827e-4d30-af6c-ca3c2af915c7
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: ee8174114f1b892210e8ee9173ce0eb1d09c7e31
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/05/2020
ms.locfileid: "96619299"
---
# <a name="resolve-mismatched-directory-errors-for-existing-azure-active-directory-domain-services-managed-domains"></a>Beheben von Verzeichniskonfliktfehlern für vorhandene verwaltete Azure Active Directory Domain Services-Domänen

Wenn eine von Azure Active Directory Domain Services (Azure AD DS) verwaltete Domäne einen Fehler durch nicht übereinstimmende Mandanten anzeigt, können Sie die verwaltete Domäne erst dann verwalten, wenn er behoben wurde. Dieser Fehler tritt auf, wenn das zugrunde liegende virtuelle Azure-Netzwerk in ein anderes Azure AD-Verzeichnis verschoben wird.

In diesem Artikel wird erläutert, warum der Fehler auftritt und wie er behoben werden kann.

## <a name="what-causes-this-error"></a>Was verursacht diesen Fehler?

Ein Verzeichniskonfliktfehler tritt auf, wenn eine von Azure AD DS verwaltete Domäne und ein virtuelles Netzwerk zu zwei verschiedenen Azure AD Mandanten gehören. Angenommen, Sie verfügen über die verwaltete Domäne *aaddscontoso.com*, die auf dem Azure AD-Mandanten von Contoso ausgeführt wird. Das virtuelle Azure-Netzwerk für die verwaltete Domäne ist jedoch Teil des Azure AD-Mandanten von Fabrikam.

Der Zugriff auf Ressourcen wird mithilfe der rollenbasierten Zugriffssteuerung von Azure (Azure Role-Based Access Control, Azure RBAC) verwaltet. Wenn Sie Azure AD DS in einem Azure AD-Mandanten aktivieren, werden Anmeldeinformationshashes mit der verwalteten Domäne synchronisiert. Dieser Vorgang erfordert es, dass Sie ein Mandantenadministrator für das Azure AD-Verzeichnis sind, und der Zugriff auf die Anmeldeinformationen muss gesteuert werden.

Zum Bereitstellen von Ressourcen in einem virtuellen Azure-Netzwerk und zum Steuern des Datenverkehrs müssen Sie über Administratorrechte für das virtuelle Netzwerk verfügen, in dem Sie die verwaltete Domäne bereitstellen.

Die verwaltete Domäne und das virtuelle Netzwerk müssen zu demselben Azure AD-Mandanten gehören, damit Azure RBAC auf alle von Azure AD DS verwendeten Ressourcen konsistent und sicher zugreifen kann.

Für Bereitstellungen gelten folgende Regeln:

- Ein Azure AD-Verzeichnis kann über mehrere Azure-Abonnements verfügen.
- Ein Azure-Abonnement kann über mehrere Ressourcen, wie etwa virtuelle Netzwerke, verfügen.
- Eine einzelne verwaltete Domäne ist für ein Azure AD-Verzeichnis aktiviert.
- Eine verwaltete Domäne kann auf einem virtuellen Netzwerk aktiviert sein, das zu einem der Azure-Abonnements des gleichen Azure AD-Mandanten gehört.

### <a name="valid-configuration"></a>Gültige Konfiguration

Im folgenden Beispielszenario für die Bereitstellung ist die verwaltete Domäne von Contoso im Mandanten „Contoso Azure AD“ aktiviert. Die verwaltete Domäne wird in einem virtuellen Netzwerk bereitgestellt, das zu einem Azure-Abonnement gehört, dessen Besitzer der Mandant „Contoso Azure AD“ ist.

Sowohl die verwaltete Domäne als auch das virtuelle Netzwerk gehören zu demselben Azure AD-Mandanten. Diese Beispielkonfiguration ist gültig und wird vollständig unterstützt.

![Gültige Azure AD DS-Mandantenkonfiguration mit der verwalteten Domäne und dem virtuellen Netzwerkteil desselben Azure AD-Mandanten](./media/getting-started/valid-tenant-config.png)

### <a name="mismatched-tenant-configuration"></a>Nicht übereinstimmende Mandantenkonfiguration

In diesem Beispielszenario für die Bereitstellung ist die verwaltete Domäne von Contoso im Mandanten „Contoso Azure AD“ aktiviert. Allerdings wird die verwaltete Domäne in einem virtuellen Netzwerk bereitgestellt, das zu einem Azure-Abonnement gehört, dessen Besitzer der Fabrikam-Azure AD-Mandant ist.

Die verwaltete Domäne und das virtuelle Netzwerk gehören zu zwei verschiedenen Azure AD-Mandanten. Diese Beispielkonfiguration ist ein nicht übereinstimmender Mandant und wird nicht unterstützt. Das virtuelle Netzwerk muss in denselben Azure AD-Mandanten wie die verwaltete Domäne verschoben werden.

![Nicht übereinstimmende Mandantenkonfiguration](./media/getting-started/mismatched-tenant-config.png)

## <a name="resolve-mismatched-tenant-error"></a>Beheben eines Fehlers durch nicht übereinstimmende Mandanten

Die beiden folgenden Optionen beheben den Verzeichniskonfliktfehler:

* [Löschen Sie zuerst die verwaltete Domäne](delete-aadds.md) aus Ihrem vorhandenen Azure AD-Verzeichnis. [Erstellen Sie dann eine verwaltete Ersatzdomäne](tutorial-create-instance.md) im selben Azure AD-Verzeichnis wie das virtuelle Netzwerk, das Sie verwenden möchten. Wenn Sie fertig sind, verknüpfen Sie alle Computer, die zuvor mit der gelöschten Domäne verknüpft waren, mit der neu erstellten verwalteten Domäne.
* [Verschieben Sie das Azure-Abonnement](../cost-management-billing/manage/billing-subscription-transfer.md) mit dem virtuellen Netzwerk in dasselbe Azure AD-Verzeichnis wie die verwaltete Domäne.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Problembehandlung bei Azure AD DS finden Sie im [Leitfaden zur Problembehandlung](troubleshoot.md).
