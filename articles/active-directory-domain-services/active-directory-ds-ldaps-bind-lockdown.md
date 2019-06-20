---
title: Binden mit sicherem LDAP (LDAPS) an eine über Azure AD Domain Services verwaltete Domäne | Microsoft-Dokumentation
description: Binden an eine über Azure AD Domain Services verwaltete Domäne mithilfe von sicherem LDAP (LDAPS)
services: active-directory-ds
documentationcenter: ''
author: MikeStephens-MS
manager: daveba
editor: curtand
ms.assetid: 6871374a-0300-4275-9a45-a39a52c65ae4
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: mstephen
ms.openlocfilehash: 47c9553b4191fe6dbae8d92d75dfae83f191a063
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66234853"
---
# <a name="bind-to-an-azure-ad-domain-services-managed-domain-using-secure-ldap-ldaps"></a>Binden an eine über Azure AD Domain Services verwaltete Domäne mithilfe von sicherem LDAP (LDAPS)

## <a name="before-you-begin"></a>Voraussetzungen
Führen Sie [Aufgabe 4: Konfigurieren von DNS für den Zugriff auf die verwaltete Domäne über das Internet](active-directory-ds-ldaps-configure-dns.md) aus.


## <a name="task-5-bind-to-the-managed-domain-over-ldap-using-ldpexe"></a>Aufgabe 5: Binden an die verwaltete Domäne über LDAP mit „LDP.exe“
Sie können das Tool „LDP.exe“ verwenden, das im Paket „Remoteserver-Verwaltungstools“ enthalten ist, um über LDAP zu binden und zu suchen.

Öffnen Sie zunächst LDP, und stellen Sie eine Verbindung mit der verwalteten Domäne her. Klicken Sie auf **Verbindung**, und klicken Sie dann im Menü auf **Verbinden**. Geben Sie den DNS-Domänennamen der verwalteten Domäne an. Geben Sie den Port an, der für Verbindungen verwendet werden soll. Verwenden Sie Port 389 für LDAP-Verbindungen. Verwenden Sie Port 636 für LDAPS-Verbindungen. Klicken Sie auf die Schaltfläche **OK**, um eine Verbindung mit der verwalteten Domäne herzustellen.

Binden Sie im nächsten Schritt an die verwaltete Domäne. Klicken Sie auf **Verbindung**, und klicken Sie dann im Menü auf **Binden**. Geben Sie die Anmeldeinformationen eines Benutzerkontos an, das zur Gruppe „AAD DC-Administratoren“ gehört.

Wählen Sie **Ansicht** aus, und wählen Sie dann im Menü **Baum** aus. Lassen Sie das Basis-DN-Feld leer, und klicken Sie auf „OK“. Navigieren Sie zu dem Container, den Sie durchsuchen möchten, klicken Sie mit der rechten Maustaste auf den Container, und wählen Sie „Suchen“ aus.

> [!TIP]
> - Benutzer und Gruppen, die aus Azure AD synchronisiert werden, werden in der Organisationseinheit **AADDC-Benutzer** gespeichert. Der Suchpfad für diese Organisationseinheit sieht wie folgt aus: ```OU=AADDC Users,DC=CONTOSO100,DC=COM```.
> - Computerkonten für Computer, die in die verwaltete Domäne eingebunden sind, werden in der Organisationseinheit **AADDC-Computer** gespeichert. Der Suchpfad für diese Organisationseinheit sieht wie folgt aus: ```OU=AADDC Computers,DC=CONTOSO100,DC=COM```.
>
>

Weitere Informationen: [LDAP-Abfragegrundlagen](https://technet.microsoft.com/library/aa996205.aspx)


## <a name="task-6-lock-down-secure-ldap-access-to-your-managed-domain-over-the-internet"></a>Aufgabe 6: Sperren des sicheren LDAP-Zugriffs auf Ihre verwaltete Domäne über das Internet
> [!NOTE]
> Wenn Sie den LDAPS-Zugriff auf die verwaltete Domäne über das Internet nicht aktiviert haben, können Sie diese Konfigurationsaufgabe überspringen.
>
>

Vor dem Ausführen dieser Aufgabe führen Sie die in [Aufgabe 3](active-directory-ds-admin-guide-configure-secure-ldap-enable-ldaps.md) beschriebenen Schritte aus.

Wenn Sie LDAPS-Zugriff über das Internet auf Ihre verwaltete Domäne zulassen, entsteht ein Sicherheitsrisiko. Die verwaltete Domäne ist aus dem Internet über den für das sichere LDAP verwendeten Port erreichbar (d.h. Port 636). Sie können sich entscheiden, den Zugriff auf die verwaltete Domäne auf bestimmte bekannte IP-Adressen zu beschränken. Erstellen Sie eine Netzwerksicherheitsgruppe (NSG), und ordnen Sie diese dem Subnetz zu, in dem Sie Azure AD Domain Services aktiviert haben.

Die Beispiel-NSG in der folgenden Tabelle sperrt den sicheren LDAP-Zugriff über das Internet. Die Regeln in der NSG lassen den eingehenden sicheren LDAP-Zugriff über TCP-Port 636 nur aus einer angegebenen Gruppe von IP-Adressen zu. Die standardmäßige Regel „DenyAll“ gilt für sämtlichen eingehenden Datenverkehr aus dem Internet. Die NSG-Regel zum Zulassen des LDAPS-Zugriffs über das Internet von angegebenen IP-Adressen hat eine höhere Priorität als die NSG-Regel „DenyAll“.

![Beispiel-NSG zum Schutz des sicheren LDAPS-Zugriffs über das Internet](./media/active-directory-domain-services-admin-guide/secure-ldap-sample-nsg.png)

**Weitere Informationen** - [Netzwerksicherheitsgruppen](../virtual-network/security-overview.md).


## <a name="related-content"></a>Verwandte Inhalte
* [Azure AD-Domänendienste – Leitfaden zu den ersten Schritten](create-instance.md)
* [Verwalten einer Azure AD Domain Services-Domäne](manage-domain.md)
* [LDAP-Abfragegrundlagen](https://technet.microsoft.com/library/aa996205.aspx)
* [Verwalten von Gruppenrichtlinien für Azure AD Domain Services](manage-group-policy.md)
* [Netzwerksicherheitsgruppen](../virtual-network/security-overview.md)
* [Erstellen einer Netzwerksicherheitsgruppe](../virtual-network/tutorial-filter-network-traffic.md)


## <a name="next-step"></a>Nächster Schritt
[Problembehandlung bei sicherem LDAP in einer verwalteten Domäne](tshoot-ldaps.md)
