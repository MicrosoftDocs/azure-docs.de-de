---
title: Entwerfen von Hybrididentitäten – Anforderungen für die Verzeichnissynchronisierung in Azure | Microsoft-Dokumentation
description: Identifizieren Sie, welche Anforderungen für die Synchronisierung aller Benutzer zwischen lokalen Speicherorten und Cloudspeicherorten für das Unternehmen gelten.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: 593eaa71-17eb-4c16-8c98-43cc62987e65
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 21558c4eccf0cd1f4e9e1d630f0e89dbb6f01c51
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60381160"
---
# <a name="determine-directory-synchronization-requirements"></a>Ermitteln der Anforderungen an die Verzeichnissynchronisierung
Bei der Synchronisierung geht es um das Bereitstellen einer Identität für Benutzer in der Cloud, und zwar basierend auf ihrer lokalen Identität. Benutzer müssen unabhängig davon, ob sie das synchronisierte Konto für die Authentifizierung oder die Verbundauthentifizierung verwenden, über eine Identität in der Cloud verfügen.  Diese Identität muss regelmäßig verwaltet und aktualisiert werden.  Die Updates können viele Formen annehmen – von der Änderung des Titels bis zur Änderung des Kennworts.  

Beginnen Sie, indem Sie für das Unternehmen die lokale Identitätslösung und die Benutzeranforderungen auswerten. Diese Auswertung ist wichtig, um die technischen Anforderungen dafür zu definieren, wie Benutzeridentitäten in der Cloud erstellt und verwaltet werden.  Für die Mehrzahl der Unternehmen wird Active Directory lokal verwendet und dient als lokales Verzeichnis, über das Benutzer synchronisiert werden. Dies ist aber nicht immer der Fall.  

Beantworten Sie die folgenden Fragen:

* Haben Sie eine, keine oder mehrere Active Directory-Gesamtstrukturen?
  
  * Für wie viele Azure AD-Verzeichnisse wird die Synchronisierung durchgeführt?
    
    1. Nutzen Sie die Filterung?
    2. Haben Sie mehrere Azure AD Connect-Server geplant?
* Verwenden Sie derzeit lokal ein Synchronisierungstool?
  
  * Wenn ja: Ist für Ihre Benutzer ein virtuelles Verzeichnis bzw. eine Integration der Identitäten vorhanden?
* Sind lokal noch weitere Verzeichnisse vorhanden, die synchronisiert werden sollen (z. B. LDAP-Verzeichnis, HR-Datenbank usw.)?
  * Sollen GALSync-Vorgänge durchgeführt werden?
  * Welchen aktuellen Stand haben UPNs in Ihrem Unternehmen? 
  * Verwenden Sie ein anderes Verzeichnis, das für die Authentifizierung der Benutzer eingesetzt wird?
  * Wird in Ihrem Unternehmen Microsoft Exchange verwendet?
    * Ist eine Hybrid-Exchange-Bereitstellung geplant?

Nachdem Sie nun eine Vorstellung von Ihren Synchronisierungsanforderungen gewonnen haben, müssen Sie bestimmen, welches Tool sich am besten zur Erfüllung dieser Anforderungen eignet.  Microsoft bietet verschiedene Tools zur Integration und Synchronisierung von Verzeichnissen.  Weitere Informationen finden Sie unter [Hybrid Identity directory integration tools comparison](plan-hybrid-identity-design-considerations-tools-comparison.md) (Vergleich von Tools für die Verzeichnisintegration für Hybrid-Identitäten). 

Nachdem Sie nun Erkenntnisse zu Ihren Synchronisierungsanforderungen und dem richtigen Tool für ihr Unternehmen gewonnen haben, müssen Sie die Anwendungen auswerten, von denen diese Verzeichnisdienste verwendet werden. Diese Auswertung ist wichtig, um die technischen Anforderungen zum Integrieren dieser Anwendungen in die Cloud zu definieren. Beantworten Sie die folgenden Fragen:

* Werden diese Anwendungen in die Cloud verschoben, und soll das Verzeichnis dafür genutzt werden?
* Gibt es spezielle Attribute, die mit der Cloud synchronisiert werden müssen, damit sie von den Anwendungen erfolgreich genutzt werden können?
* Müssen diese Anwendungen umgeschrieben werden, um die Cloudauthentifizierung nutzen zu können?
* Werden diese Anwendungen weiterhin lokal vorgehalten, während Benutzer mit der Cloud-Identität darauf zugreifen?

Außerdem müssen Sie die Sicherheitsanforderungen und Einschränkungen der Verzeichnissynchronisierung ermitteln. Diese Auswertung ist wichtig, um eine Liste mit den Anforderungen zu erhalten, die zum Erstellen und Verwalten von Benutzeridentitäten in der Cloud benötigt werden. Beantworten Sie die folgenden Fragen:

* Wo soll sich der Synchronisierungsserver befinden?
* Soll er Mitglied einer Domäne sein?
* Befindet sich der Server in einem eingeschränkten Netzwerk hinter einer Firewall, z. B. einer DMZ?
  * Können Sie die erforderlichen Firewallports öffnen, um die Synchronisierung zu unterstützen?
* Verfügen Sie für den Synchronisierungsserver über einen Plan für die Notfallwiederherstellung?
* Verfügen Sie über ein Konto mit den richtigen Berechtigungen für alle Gesamtstrukturen, für die eine Synchronisierung durchgeführt werden soll?
  * Falls Sie in Ihrem Unternehmen diese Frage nicht beantworten können, helfen Ihnen die Informationen im Abschnitt „Berechtigungen für die Kennwortsynchronisierung“ des Artikels [Installieren des Azure Active Directory-Synchronisierungsdiensts](https://msdn.microsoft.com/library/azure/dn757602.aspx#BKMK_CreateAnADAccountForTheSyncService) weiter. Mit diesen Informationen können Sie ermitteln, ob bereits ein Konto mit diesen Berechtigungen vorhanden ist oder ob Sie ein Konto erstellen müssen.
* Kann der Synchronisierungsserver auf alle Gesamtstrukturen zugreifen, wenn bei Ihnen mehrere Gesamtstrukturen an der Synchronisierung beteiligt sind?

> [!NOTE]
> Notieren Sie sich alle Antworten, und stellen Sie sicher, dass Ihnen die Begründung der Antwort jeweils klar ist. [Bestimmen der Anforderungen an Reaktionen auf Vorfälle](plan-hybrid-identity-design-considerations-incident-response-requirements.md) sind die verfügbaren Optionen beschrieben. Indem Sie diese Fragen beantworten, wählen Sie aus, welche Option Ihre Geschäftsanforderungen am besten erfüllt.
> 
> 

## <a name="next-steps"></a>Nächste Schritte
[Ermitteln der Anforderungen für die Multi-Factor Authentication](plan-hybrid-identity-design-considerations-multifactor-auth-requirements.md)

## <a name="see-also"></a>Weitere Informationen
[Überlegungen zum Entwurf – Übersicht](plan-hybrid-identity-design-considerations-overview.md)

