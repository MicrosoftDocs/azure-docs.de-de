---
title: Verwalten von Bedrohungen für Ressourcen in Azure Active Directory B2C | Microsoft-Dokumentation
description: Informieren Sie sich über Verfahren zur Erkennung und Abwehr von Denial-of-Service- und Kennwortangriffen in Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: f961e873bb230e4e6d7e14b03e2664b7f987974e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66508904"
---
# <a name="manage-threats-to-resources-and-data-in-azure-active-directory-b2c"></a>Verwalten von Bedrohungen für Ressourcen in Azure Active Directory B2C

Azure Active Directory (Azure AD) B2C verfügt über integrierte Features, mit denen Sie Ihre Ressourcen und Daten vor Bedrohungen schützen können. Zu diesen Bedrohungen gehören Denial-of-Service- und Kennwortangriffe. Denial-of-Service-Angriffe können dazu führen, dass Ressourcen nicht mehr für die vorgesehenen Benutzer zur Verfügung stehen. Kennwortangriffe führen zu nicht autorisiertem Zugriff auf Ressourcen. 

## <a name="denial-of-service-attacks"></a>Denial-of-Service-Angriffe

Azure AD B2C dient zur Abwehr von SYN-Flutangriffen über ein SYN-Cookie. Außerdem schützt Azure AD B2C vor Denial-of-Service-Angriffen, indem Einschränkungen für Raten und Verbindungen festgelegt werden.

## <a name="password-attacks"></a>Kennwortangriffe

Benutzerkennwörter müssen ausreichend komplex sein. Azure AD B2C umfasst Abwehrmaßnahmen für Kennwortangriffe. Diese decken unter anderem Brute-Force-Kennwortangriffe und Wörterbuchangriffe ab. Anhand verschiedener Signale analysiert Azure AD B2C die Integrität von Anforderungen. Azure AD B2C kann auf intelligente Weise zwischen vorgesehenen Benutzern und Hackern oder Botnetzen unterscheiden. 

Azure AD B2C verwendet eine ausgereifte Strategie zum Sperren von Konten. Die Konten werden basierend auf der IP-Adresse der Anforderung und den eingegebenen Kennwörtern gesperrt. Die Dauer der Sperre wird zudem gemäß der Wahrscheinlichkeit erhöht, dass es sich um einen Angriff handelt. Nachdem ein Kennwort 10 Mal erfolglos eingegeben wurde, tritt eine Sperre von einer Minute auf. Bei der nächsten nicht erfolgreichen Anmeldung nach dem Entsperren des Kontos tritt eine weitere Sperre von einer Minute auf und wird für jede nicht erfolgreiche Anmeldung fortgesetzt. Die wiederholte Eingabe des gleichen Kennworts wird nicht als mehrere erfolglose Anmeldungen gezählt. 

Die ersten 10 Sperrzeiträume sind eine Minute lang. Die nächsten 10 Sperrzeiträume sind etwas länger, und die Dauer wird jeweils nach 10 Sperrzeiträumen verlängert. Der Zähler für die Kontosperrung wird nach einer erfolgreichen Anmeldung auf null zurückgesetzt, wenn das Konto nicht gesperrt ist. Sperrzeiträume können bis zu fünf Stunden dauern. 

Derzeit ist Folgendes nicht möglich:

- Eine Sperre mit weniger als 10 fehlerhaften Anmeldungen auslösen
- Eine Liste mit gesperrten Konten abrufen
- Die Sperrrichtlinie konfigurieren

Weitere Informationen finden Sie im [Microsoft Trust Center](https://www.microsoft.com/trustcenter/default.aspx).
