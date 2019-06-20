---
title: 'Azure Active Directory Domain Services: Beheben von Problemen bei der Konfiguration von Secure LDAP | Microsoft-Dokumentation'
description: Beheben von Problemen mit Secure LDAP für Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: MikeStephens-MS
manager: ''
editor: ''
ms.assetid: 81208c0b-8d41-4f65-be15-42119b1b5957
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: mstephen
ms.openlocfilehash: dde4a02e5be32d5549ba499742d1ab650fa146c0
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66245389"
---
# <a name="azure-ad-domain-services---troubleshooting-secure-ldap-configuration"></a>Azure Active Directory Domain Services: Beheben von Problemen bei der Konfiguration von Secure LDAP

Dieser Artikel bietet Lösungen für häufiger auftretende Probleme bei der [Konfiguration von Secure LDAP](configure-ldaps.md) für Azure AD Domain Services.

## <a name="aadds101-secure-ldap-network-security-group-configuration"></a>AADDS101: Konfiguration der Netzwerksicherheitsgruppe für Secure LDAP

**Warnmeldung:**

*Secure LDAP über das Internet ist für die verwaltete Domäne aktiviert. Der Zugriff auf Port 636 ist jedoch nicht über eine Netzwerksicherheitsgruppe gesperrt. Dies kann Brute-Force-Kennwortangriffe auf Benutzerkonten in der verwalteten Domäne möglich machen.*

### <a name="secure-ldap-port"></a>Secure LDAP-Port

Wenn Secure LDAP aktiviert ist, wird das Erstellen zusätzlicher Regeln empfohlen, die eingehenden LDAPS-Zugriff nur von bestimmten IP-Adressen zulassen. Diese Regeln schützen Ihre Domäne vor Brute-Force-Angriffen, die ein Sicherheitsrisiko darstellen können. Port 636 erlaubt den Zugriff auf Ihre verwaltete Domäne. So aktualisieren Sie die NSG für das Zulassen des Zugriffs für Secure LDAP

1. Navigieren Sie im Azure-Portal zur Registerkarte [Netzwerksicherheitsgruppen](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FNetworkSecurityGroups).
2. Wählen Sie in der Tabelle die NSG aus, die Ihrer Domäne zugeordnet ist.
3. Klicken Sie auf **Eingangssicherheitsregeln**.
4. Erstellen der Regel für Port 636
   1. Klicken Sie auf der oberen Navigationsleiste auf **Hinzufügen**.
   2. Wählen Sie **IP-Adressen** für die Quelle aus.
   3. Geben Sie die Quellportbereiche für diese Regel an.
   4. Geben Sie als Zielportbereich „636“ ein.
   5. Das Protokoll ist **TCP**.
   6. Fügen Sie der Regel einen geeigneten Namen, eine Beschreibung und eine Priorität hinzu. Die Priorität der Regel sollte größer als die der Regel „Alle verweigern“ (sofern vorhanden) sein.
   7. Klicken Sie auf **OK**.
5. Überprüfen Sie, ob die Regel erstellt wurde.
6. Überprüfen Sie in zwei Stunden die Integrität Ihrer Domäne, um sicherzustellen, dass Sie die Schritte ordnungsgemäß abgeschlossen haben.

> [!TIP]
> Port 636 ist nicht die einzige Regel, die für Azure AD Domain Services reibungslos ausgeführt werden muss. Weitere Informationen finden Sie in den Artikeln [Netzwerkrichtlinien](network-considerations.md) und [Problembehandlung für die Konfiguration von Netzwerksicherheitsgruppen](alert-nsg.md).
>

## <a name="aadds502-secure-ldap-certificate-expiring"></a>AADDS502: Secure LDAP-Zertifikat läuft ab

**Warnmeldung:**

*Das sichere LDAP-Zertifikat für die verwaltete Domäne läuft am [Datum] ab.*

**Lösung:**

Erstellen Sie ein neues sicheres LDAP-Zertifikat anhand der Schritte, die im Artikel [Konfigurieren von sicherem LDAP (LDAPS) für eine über Azure AD Domain Services verwaltete Domäne](configure-ldaps.md) beschrieben werden.

## <a name="contact-us"></a>Kontakt
Wenden Sie sich an das Produktteam der Azure Active Directory Domain Services, um [Feedback zu geben oder Support zu erhalten](contact-us.md).
