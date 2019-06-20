---
title: Referenz für Risikoereignisse in Azure Active Directory Identity Protection | Microsoft-Dokumentation
description: Referenz für Risikoereignisse in Azure Active Directory Identity Protection.
services: active-directory
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: mtillman
ms.assetid: 14f7fc83-f4bb-41bf-b6f1-a9bb97717c34
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2018
ms.author: joflore
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3973a35acb4cb95d3392a8daa59e7fd9a8c56eb1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65191526"
---
# <a name="azure-active-directory-identity-protection-risk-events-reference"></a>Referenz für Risikoereignisse in Azure Active Directory Identity Protection

Die allermeisten Sicherheitsverletzungen kommen vor, wenn Angreifer Zugriff auf eine Umgebung erhalten, indem sie die Identität eines Benutzers stehlen. Die Ermittlung von kompromittierten Identitäten ist keine einfache Aufgabe. Azure Active Directory verwendet adaptive Machine Learning-Algorithmen und -Heuristiken, um verdächtige Aktivitäten im Zusammenhang mit Ihren Benutzerkonten zu erkennen. Jede erkannte verdächtige Aktion wird in einem Datensatz gespeichert, der als Risikoereignis bezeichnet wird.


## <a name="anonymous-ip-address"></a>Anonyme IP-Adresse

**Erkennungstyp**: Echtzeit  
**Alter Name**: Anmeldungen von einer anonymen IP-Adresse


Diese Art von Risikoereignis gibt Anmeldungen über eine anonyme IP-Adresse (z. B. Tor-Browser, anonymisierte VPNs) an.
Diese IP-Adressen werden in der Regel von Akteuren verwendet, die ihre Anmeldungstelemetriedaten (IP-Adresse, Standort, Gerät usw.) für potenziell böswillige Absichten verbergen möchten.


## <a name="atypical-travel"></a>Ungewöhnlicher Ortswechsel

**Erkennungstyp**: Offline  
**Alter Name**: Unmöglicher Ortswechsel zu atypischen Orten


Mit dieser Art von Risikoereignis werden zwei Anmeldungen identifiziert, die von weit entfernten Orten durchgeführt wurden und bei denen mindestens einer der Orte aufgrund des bisherigen Verhaltens atypisch für den Benutzer ist. Neben zahlreichen anderen Faktoren berücksichtigt dieser Machine Learning-Algorithmus die Zeit zwischen den beiden Anmeldungen sowie die Zeit, die der Benutzer für einen Ortswechsel benötigen würde, wovon sich ableiten lässt, dass ein anderer Benutzer die gleichen Anmeldeinformationen verwendet.

Bei diesem Algorithmus werden offensichtliche falsch positive Ergebnisse ignoriert, die zu den unmöglichen Ortswechselbedingungen beitragen. Hierzu zählen etwa VPNs und regelmäßig von anderen Benutzern der Organisation verwendete Standorte. Das System verfügt über einen anfänglichen Lernzeitraum von 14 Tagen oder 10 Anmeldungen (je nachdem, welcher Punkt früher erreicht wird), in dem das Anmeldeverhalten des neuen Benutzers erlernt wird.


## <a name="leaked-credentials"></a>Kompromittierte Anmeldeinformationen

**Erkennungstyp**: Offline  
**Alter Name**: Benutzer mit kompromittierten Anmeldeinformationen


Diese Art von Risikoereignis gibt an, dass die gültigen Anmeldeinformationen des Benutzers offengelegt wurden.
Wenn Internetkriminelle an gültige Kennwörter von berechtigten Benutzern gelangen, geben sie diese Anmeldeinformationen häufig weiter. Dies erfolgt in der Regel durch eine Veröffentlichung im Darknet oder auf Paste Sites oder durch den Handel bzw. Verkauf der Anmeldeinformationen auf dem Schwarzmarkt. Der Microsoft-Dienst für offengelegte Anmeldeinformationen sammelt Kombinationen aus Benutzername und Kennwort durch die Überwachung von öffentlichen und Dark Web-Websites und die Zusammenarbeit mit:

- Forschern

- Strafverfolgungsbehörden

- Sicherheitsteams bei Microsoft

- Anderen vertrauenswürdigen Quellen

Wenn der Dienst Benutzeranmeldeinformationen aus dem Darknet, von Paste Sites oder den oben aufgeführten Quellen erwirbt, werden sie mit aktuell gültigen Anmeldeinformationen von Azure AD-Benutzern verglichen, um gültige Übereinstimmungen zu finden.


## <a name="malware-linked-ip-address"></a>Mit Schadsoftware verknüpfte IP-Adresse

**Erkennungstyp**: Offline  
**Alter Name**: Anmeldungen von infizierten Geräten


Bei dieser Art von Risikoereignis werden Anmeldungen von IP-Adressen identifiziert, die mit Schadsoftware infiziert sind und bekanntermaßen aktiv mit einem Botserver kommunizieren. Dies wird ermittelt, indem IP-Adressen des Benutzergeräts mit IP-Adressen korreliert werden, die in Kontakt mit einem Botserver gestanden haben, während der Botserver aktiv war.


## <a name="unfamiliar-sign-in-properties"></a>Ungewöhnliche Anmeldeeigenschaften

**Erkennungstyp**: Echtzeit  
**Alter Name**: Anmeldungen von unbekannten Standorten


Dieser Risikoereignistyp berücksichtigt die bisherige Anmeldehistorie (IP-Adresse, Breitengrad/Längengrad und ASN), um nach anomalen Anmeldungen zu suchen. Im System werden Informationen zu den vorherigen Standorten gespeichert, die von einem Benutzer genutzt wurden, und diese werden als „vertraute“ Standorte angesehen. Das Risikoereignis wird ausgelöst, wenn die Anmeldung von einem Standort aus erfolgt, der in der Liste der vertrauten Standorte noch nicht enthalten ist. Neu erstellte Benutzer befinden sich für einen bestimmten Zeitraum im „Lernmodus“, in dem ungewohnte Anmeldeeigenschaften-Risikoereignisse deaktiviert werden, während unsere Algorithmen das Verhalten des Benutzers lernen. Die Dauer des Lernmodus ist dynamisch und hängt davon ab, wie lange es dauert, bis der Algorithmus genügend Informationen über die Anmeldemuster des Benutzers gesammelt hat. Die Mindestdauer beträgt fünf Tage. Ein Benutzer kann nach einer langen Zeit der Inaktivität erneut in den Lernmodus wechseln. Außerdem ignoriert das System Anmeldungen von vertrauten Geräten und von Standorten aus, die geografisch nahe an einem bekannten Speicherort liegen. 

Diese Erkennung wird auch für die Standardauthentifizierung (bzw. ältere Protokolle) ausgeführt. Da diese Protokolle nicht über moderne Eigenschaften (z. B. Client-ID) verfügen, reichen die Telemetriedaten nicht aus, um falsch positive Ergebnisse zu reduzieren. Wir empfehlen unseren Kunden, auf eine moderne Authentifizierung umzusteigen.


## <a name="azure-ad-threat-intelligence"></a>Azure AD Threat Intelligence

**Erkennungstyp**: Offline <br>
**Alter Name**: Diese Erkennung wird in älteren Azure AD Identity Protection-Berichten (Benutzer mit Risikokennzeichnung, Risikoereignisse) als „Benutzer mit kompromittierten Anmeldeinformationen“ angezeigt.

Dieser Risikoereignistyp zeigt Benutzeraktivitäten an, die für den angegebenen Benutzer unüblich oder mit bekannten Angriffsmustern konsistent sind, so wie in den internen und externen Threat Intelligence-Quellen von Microsoft angegeben.
