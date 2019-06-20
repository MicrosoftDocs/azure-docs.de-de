---
title: Beheben von Verzeichniskonfliktfehlern für vorhandene verwaltete Azure AD Domain Services-Domänen | Microsoft-Dokumentation
description: Verstehen und Lösen von Verzeichniskonfliktfehlern für vorhandene verwaltete Azure AD Domain Services-Domänen
services: active-directory-ds
documentationcenter: ''
author: MikeStephens-MS
manager: daveba
editor: curtand
ms.assetid: 40eb75b7-827e-4d30-af6c-ca3c2af915c7
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: mstephen
ms.openlocfilehash: cc5b7286536901a516cb694013f1ad6eb594dcb1
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66245287"
---
# <a name="resolve-mismatched-directory-errors-for-existing-azure-ad-domain-services-managed-domains"></a>Beheben von Verzeichniskonfliktfehlern für vorhandene verwaltete Azure AD Domain Services-Domänen
Sie verwenden eine durch Azure AD Domain Services verwaltete Domäne. Wenn Sie zum Azure-Portal navigieren und die verwaltete Domäne anzeigen, wird die folgende Fehlermeldung angezeigt:

![Verzeichniskonfliktfehler](./media/getting-started/mismatched-tenant-error.png)

Sie können diese verwaltete Domäne nicht verwalten, bis der Fehler behoben wurde.


## <a name="whats-causing-this-error"></a>Was ist die Ursache dieses Fehlers?
Dieser Fehler wird verursacht, wenn die verwaltete Domäne, und das virtuelle Netzwerk, in der Sie aktiviert ist, zu zwei verschiedenen Azure AD-Mandanten gehören. Beispielsweise verfügen Sie über eine verwaltete Domäne mit dem Namen „contoso.com“, und diese wurde für den Azure AD-Mandanten von Contoso aktiviert. Allerdings gehört das virtuelle Azure-Netzwerk, in welchem die verwaltete Domäne aktiviert wurde, zu Fabrikam, einem anderen Azure AD-Mandanten.

Das neue Azure-Portal (und speziell die Azure AD Domain Services-Erweiterung) basiert auf dem Azure Resource Manager. In der modernen Azure Resource Manager-Umgebung werden bestimmte Einschränkungen erzwungen, um ein höheres Maß an Sicherheit zu bieten und die rollenbasierte Zugriffssteuerung (RBAC) auf Ressourcen zu ermöglichen. Das Aktivieren von Azure AD Domain Services für Azure AD-Mandanten ist ein sensibler Vorgang, da er bewirkt, dass Anmeldeinformationshashes mit der verwalteten Domäne synchronisiert werden. Für diesen Vorgang ist es erforderlich, dass Sie Mandantenadministrator für das Verzeichnis sind. Darüber hinaus müssen Sie über Administratorrechte über das virtuelle Netzwerk verfügen, in dem Sie die verwaltete Domäne aktivieren. Damit die RBAC-Prüfungen konsequent arbeiten können, sollten die verwaltete Domäne und das virtuelle Netzwerk zum gleichen Azure AD-Mandanten gehören.

Kurz gesagt, Sie können keine verwaltete Domäne für einen Azure AD-Mandanten „contoso.com“ in einem virtuellen Netzwerk aktivieren, das zu einem Azure-Abonnement gehört, das im Besitz eines anderen Azure AD-Mandanten „fabrikam.com“ ist. 

**Gültige Konfiguration**: In diesem Bereitstellungsszenario ist die verwaltete Contoso-Domäne für den Contoso Azure AD-Mandanten aktiviert. Die verwaltete Domäne wird in einem virtuellen Netzwerk verfügbar gemacht, das zu einem Azure-Abonnement gehört, welches sich im Besitz des Contoso Azure AD-Mandanten befindet. Daher gehören sowohl die verwaltete Domäne als auch das virtuelle Netzwerk zum gleichen Azure AD-Mandanten. Diese Konfiguration ist gültig und wird vollständig unterstützt.

![Gültige Mandantenkonfiguration](./media/getting-started/valid-tenant-config.png)

**Nicht übereinstimmende Mandantenkonfiguration**: In diesem Bereitstellungsszenario ist die verwaltete Contoso-Domäne für den Contoso Azure AD-Mandanten aktiviert. Allerdings wird die verwaltete Domäne in einem virtuellen Netzwerk verfügbar gemacht, das zu einem Azure-Abonnement gehört, das im Besitz des Fabrikam-Azure AD-Mandanten ist. Aus diesem Grund gehören die verwaltete Domäne und das virtuelle Netzwerk zu zwei verschiedenen Azure AD-Mandanten. Diese Konfiguration ist die nicht übereinstimmende Mandantenkonfiguration und wird nicht unterstützt. Das virtuelle Netzwerk muss zum gleichen Azure AD-Mandanten (d.h. „Contoso“) verschoben werden, bei dem sich die verwaltete Domäne befindet. Ausführliche Informationen finden Sie im Abschnitt [Auflösung](#resolution).

![Nicht übereinstimmende Mandantenkonfiguration](./media/getting-started/mismatched-tenant-config.png)

Darum wird Ihnen diese Fehlermeldung angezeigt, wenn die verwaltete Domäne und das virtuelle Netzwerk, in dem diese aktiviert ist, zu zwei verschiedenen Azure AD-Mandanten gehören.

In der Resource Manager-Umgebung gelten die folgenden Regeln:
- Ein Azure AD-Verzeichnis kann über mehrere Azure-Abonnements verfügen.
- Ein Azure-Abonnement kann über mehrere Ressourcen, wie etwa virtuelle Netzwerke, verfügen.
- Eine einzelne verwaltete Azure AD Domain Services-Domäne ist für ein Azure AD-Verzeichnis aktiviert.
- Eine verwaltete Azure AD Domain Services-Domäne kann auf einem virtuellen Netzwerk aktiviert sein, das zu einem der Azure-Abonnements des gleichen Azure AD-Mandanten gehört.


## <a name="resolution"></a>Lösung
Sie haben zwei Optionen zum Beheben des Verzeichniskonfliktfehlers. Sie können:

- Auf die Schaltfläche **Löschen** klicken, um die vorhandene verwaltete Domäne zu löschen. Mit dem [Azure-Portal](https://portal.azure.com) neu erstellen, sodass die verwaltete Domäne und das virtuelle Netzwerk, in dem sie zur Verfügung steht, dem Azure AD-Verzeichnis angehören. Verknüpfen Sie alle Computer, die zuvor mit der gelöschten Domäne verknüpft waren, mit der neu erstellten verwalteten Domäne.

- Verschieben Sie das Azure-Abonnement, welches das virtuelle Netzwerk enthält, in das Azure AD-Verzeichnis, zu dem die verwaltete Domäne gehört. Führen Sie die Schritte im Artikel [Übertragen des Besitzes eines Azure-Abonnements auf ein anderes Konto](../billing/billing-subscription-transfer.md) aus.


## <a name="related-content"></a>Verwandte Inhalte
* [Azure AD-Domänendienste – Leitfaden zu den ersten Schritten](create-instance.md)
* [Leitfaden zur Problembehandlung – Azure AD Domain Services](troubleshoot.md)
