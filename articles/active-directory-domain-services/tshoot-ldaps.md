---
title: Behandeln von Problemen mit sicherem LDAP (LDAPS) in Azure AD Domain Services | Microsoft-Dokumentation
description: Behandeln von Problemen mit sicherem LDAP (LDAPS) für eine über Azure AD Domain Services verwaltete Domäne
services: active-directory-ds
documentationcenter: ''
author: MikeStephens-MS
manager: daveba
editor: curtand
ms.assetid: 445c60da-e115-447b-841d-96739975bdf6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: mstephen
ms.openlocfilehash: c9732545e1759ea23d62c0a56379e3868ed40a0b
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66245395"
---
# <a name="troubleshoot-secure-ldap-ldaps-for-an-azure-ad-domain-services-managed-domain"></a>Behandeln von Problemen mit sicherem LDAP (LDAPS) für eine über Azure AD Domain Services verwaltete Domäne

## <a name="connection-issues"></a>Verbindungsprobleme
Wenn Sie Probleme beim Herstellen einer Verbindung mit der verwalteten Domäne über sicheres LDAP haben:

* Die Ausstellerkette des Zertifikats für sicheres LDAP muss auf dem Client als vertrauenswürdig eingestuft werden. Sie könnten die Stammzertifizierungsstelle dem vertrauenswürdigen Stammzertifikatspeicher auf dem Client hinzufügen, um die Vertrauensstellung einzurichten.
* Stellen Sie sicher, dass der LDAP-Client (z.B. „ldp.exe“) eine Verbindung mit dem sicheren LDAP-Endpunkt mit einem DNS-Namen herstellt, nicht mit der IP-Adresse.
* Überprüfen Sie den DNS-Namen, mit dem der LDAP-Client eine Verbindung herstellt. Er muss in die öffentliche IP-Adresse für sicheres LDAP in der verwalteten Domäne aufgelöst werden.
* Stellen Sie sicher, dass das sichere LDAP-Zertifikat für die verwaltete Domäne den DNS-Namen im Attribut für den Antragsteller oder den alternativen Antragstellernamen aufweist.
* Die NSG-Einstellungen für das virtuelle Netzwerk müssen Datenverkehr über Port 636 aus dem Internet zulassen. Dieser Schritt gilt nur, wenn Sie den sicheren LDAP-Zugriff über das Internet aktiviert haben.


## <a name="need-help"></a>Sie brauchen Hilfe?
Wenn Sie weiterhin Probleme beim Herstellen einer Verbindung mit der verwalteten Domäne über sicheres LDAP haben, [bitten Sie das Produktteam](contact-us.md) um Hilfe. Nehmen Sie dabei die folgende Informationen auf, damit das Problem besser diagnostiziert werden kann:
* Einen Screenshot von „ldp.exe“ beim Herstellen einer Verbindung, die fehlschlägt.
* Die ID des Azure AD-Mandanten und den DNS-Domänennamen Ihrer verwalteten Domäne.
* Den genauen Benutzernamen, den Sie verwenden möchten.


## <a name="related-content"></a>Verwandte Inhalte
* [Azure AD-Domänendienste – Leitfaden zu den ersten Schritten](create-instance.md)
* [Verwalten einer Azure AD Domain Services-Domäne](manage-domain.md)
* [LDAP-Abfragegrundlagen](https://technet.microsoft.com/library/aa996205.aspx)
* [Verwalten von Gruppenrichtlinien für Azure AD Domain Services](manage-group-policy.md)
* [Netzwerksicherheitsgruppen](../virtual-network/security-overview.md)
* [Erstellen einer Netzwerksicherheitsgruppe](../virtual-network/tutorial-filter-network-traffic.md)
