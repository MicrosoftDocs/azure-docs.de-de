---
title: 'Azure AD Connect: „msExchUserHoldPolicies“ und „cloudMsExchUserHoldPolicies“ | Microsoft-Dokumentation'
description: In diesem Thema wird das Attributverhalten der Attribute „msExchUserHoldPolicies“ und „cloudMsExchUserHoldPolicies“ beschrieben.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 09/15/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0fbda588d99de44c77118586519055a8fc474104
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96861764"
---
# <a name="azure-ad-connect---msexchuserholdpolicies-and-cloudmsexchuserholdpolicies"></a>Azure AD Connect: „msExchUserHoldPolicies“ und „cloudMsExchUserHoldPolicies“
Im folgenden Referenzdokument werden diese von Exchange verwendeten Attribute beschrieben, und Sie erfahren, wie Sie die Standardsynchronisierungsregeln ordnungsgemäß bearbeiten.

## <a name="what-are-msexchuserholdpolicies-and-cloudmsexchuserholdpolicies"></a>Worum handelt es sich bei „msExchUserHoldPolicies“ und „cloudMsExchUserHoldPolicies“?
Für Exchange Server sind zwei [Aufbewahrungsarten](/Exchange/policy-and-compliance/holds/holds) verfügbar: Beweissicherungsverfahren und In-Situ-Aufbewahrung. Bei aktiviertem Beweissicherungsverfahren werden alle Postfachelemente aufbewahrt.  Bei der In-Situ-Aufbewahrung werden nur Elemente aufbewahrt, die den Kriterien einer Suchabfrage entsprechen, die Sie mithilfe des In-Situ-eDiscovery-Tools definiert haben.

Anhand der Attribute „MsExchUserHoldPolicies“ und „cloudMsExchUserHoldPolicies“ können lokale AD-Instanzen und Azure AD-Instanzen die Benutzer ermitteln, für die eine Aufbewahrungspflicht gilt (abhängig davon, ob sie eine lokale Exchange-Instanz oder Exchange Online verwenden).

## <a name="msexchuserholdpolicies-synchronization-flow"></a>Synchronisierungsfluss für „msExchUserHoldPolicies“
„MsExchUserHoldPolicies“ wird von Azure AD Connect standardmäßig direkt mit dem Attribut „msExchUserHoldPolicies“ im Metaverse und anschließend mit dem Attribut „msExchUserHoldPolicies“ in Azure AD synchronisiert.

Der Fluss wird in den folgenden Tabellen beschrieben:

Eingehend aus der lokalen Active Directory-Instanz:

|Active Directory-Attribut|Attributname|Attributflusstyp|Metaverse-Attribut|Synchronisierungsregel|
|-----|-----|-----|-----|-----|
|Lokales Active Directory|msExchUserHoldPolicies|Direkt|msExchUserHoldPolicies|Eingehend aus AD: Benutzer/Exchange|

Ausgehend an Azure AD:

|Metaverse-Attribut|Attributname|Attributflusstyp|Azure AD-Attribut|Synchronisierungsregel|
|-----|-----|-----|-----|-----|
|Azure Active Directory|msExchUserHoldPolicies|Direkt|msExchUserHoldPolicies|Ausgehend an AAD: UserExchangeOnline|

## <a name="cloudmsexchuserholdpolicies-synchronization-flow"></a>Synchronisierungsfluss für „cloudMsExchUserHoldPolicies“
„cloudMsExchUserHoldPolicies“ wird von Azure AD Connect standardmäßig direkt mit dem Attribut „cloudMsExchUserHoldPolicies“ im Metaverse synchronisiert. Anschließend wird das Attribut an Active Directory weitergegeben, sofern „msExchUserHoldPolicies“ im Metaverse nicht NULL ist.

Der Fluss wird in den folgenden Tabellen beschrieben:

Eingehend aus Azure AD:

|Active Directory-Attribut|Attributname|Attributflusstyp|Metaverse-Attribut|Synchronisierungsregel|
|-----|-----|-----|-----|-----|
|Lokales Active Directory|cloudMsExchUserHoldPolicies|Direkt|cloudMsExchUserHoldPolicies|Eingehend aus AAD: Benutzer/Exchange|

Ausgehend an die lokale Active Directory-Instanz:

|Metaverse-Attribut|Attributname|Attributflusstyp|Azure AD-Attribut|Synchronisierungsregel|
|-----|-----|-----|-----|-----|
|Azure Active Directory|cloudMsExchUserHoldPolicies|IF(NOT NULL)|msExchUserHoldPolicies|Ausgehend an AD: UserExchangeOnline|

## <a name="information-on-the-attribute-behavior"></a>Informationen zum Attributverhalten
„msExchangeUserHoldPolicies“ ist ein Attribut mit einer einzelnen Autorität.  Ein Attribut mit einer einzelnen Autorität kann auf ein Objekt (in diesem Fall: ein Benutzerobjekt) im lokalen Verzeichnis oder im Cloudverzeichnis festgelegt werden.  Aufgrund der Regeln für den Autoritätsursprung gilt: Wird das Attribut aus dem lokalen Verzeichnis synchronisiert, kann es von Azure AD nicht aktualisiert werden.

Um Benutzern das Festlegen einer Aufbewahrungsrichtlinie für ein Benutzerobjekt in der Cloud zu ermöglichen, wird das Attribut „cloudMSExchangeUserHoldPolicies“ verwendet. Dieses Attribut wird verwendet, da „msExchangeUserHoldPolicies“ aufgrund der oben erläuterten Regeln nicht direkt von Azure AD festgelegt werden kann.  Das Attribut wird dann wieder mit dem lokalen Verzeichnis synchronisiert, sofern „msExchangeUserHoldPolicies“ nicht NULL ist, und ersetzt den aktuellen Wert von „msExchangeUserHoldPolicies“.

In bestimmten Fällen kann es zu Problemen kommen – beispielsweise, wenn beide gleichzeitig lokal und in Azure geändert wurden.  

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zum [Integrieren lokaler Identitäten in Azure Active Directory](whatis-hybrid-identity.md).
