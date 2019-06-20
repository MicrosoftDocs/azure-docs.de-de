---
title: Konfigurieren der NPS-Erweiterung für Azure MFA – Azure Active Directory
description: Befolgen Sie nach dem Installieren der NPS-Erweiterung diese Schritte für die erweiterte Konfigurierung wie IP-Whitelists und UPN-Ersetzung.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: b8ac0497b13dad6795e8dc7ffaf761fe887a9953
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65988625"
---
# <a name="advanced-configuration-options-for-the-nps-extension-for-multi-factor-authentication"></a>Erweiterte Konfigurationsoptionen für die NPS-Erweiterung für Multi-Factor Authentication

Die Erweiterung für den Netzwerkrichtlinienserver (Network Policy Server, NPS) erweitert Ihre cloudbasierten Funktionen von Azure Multi-Factor Authentication in Ihre lokale Infrastruktur. In diesem Artikel wird davon ausgegangen, dass Sie die Erweiterung bereits installiert haben und sie nun an Ihre Anforderungen anpassen möchten. 

## <a name="alternate-login-id"></a>Alternative Anmelde-ID

Da die NPS-Erweiterung eine Verbindung zu Ihren lokalen Verzeichnissen und zu Ihren Cloudverzeichnissen herstellt, tritt möglicherweise ein Problem auf, wenn Ihre lokalen Benutzerprinzipalnamen (User Principal Names, UPNs) nicht mit den Namen in der Cloud übereinstimmen. Verwenden Sie alternative Anmelde-IDs, um dieses Problem zu beheben. 

In der NPS-Erweiterung können Sie ein Active Directory-Attribut festlegen, das anstatt des UPN für Multi-Factor Authentication verwendet wird. Dadurch können Sie Ihre lokalen Ressourcen mit einer zweistufigen Überprüfung schützen, ohne Ihre lokalen UPNs zu ändern. 

Wechseln Sie zum Konfigurieren alternativer Anmelde-IDs zu `HKLM\SOFTWARE\Microsoft\AzureMfa`, und bearbeiten Sie die folgenden Registrierungswerte:

| NAME | Type | Standardwert | BESCHREIBUNG |
| ---- | ---- | ------------- | ----------- |
| LDAP_ALTERNATE_LOGINID_ATTRIBUTE | Zeichenfolge | Leer | Legen Sie den Namen des Active Directory-Attributs fest, das Sie anstelle des UPN verwenden möchten. Dieses Attribut wird als AlternateLoginId-Attribut verwendet. Wenn dieser Registrierungswert auf ein [gültiges Active Directory-Attribut](https://msdn.microsoft.com/library/ms675090.aspx) (z.B. mail oder displayName) festgelegt wird, wird der Wert des Attributs anstelle des UPN des Benutzers zur Authentifizierung verwendet. Wenn dieser Registrierungswert leer oder nicht konfiguriert ist, wird AlternateLoginId deaktiviert und der UPN des Benutzers wird zur Authentifizierung verwendet. |
| LDAP_FORCE_GLOBAL_CATALOG | boolean | False | Verwenden Sie dieses Flag, um die Verwendung des globalen Katalogs für LDAP-Suchvorgänge beim Suchen nach AlternateLoginId zu erzwingen. Konfigurieren Sie einen Domänencontroller als globalen Katalog, fügen Sie das AlternateLoginId-Attribut zum globalen Katalog hinzu, und aktivieren Sie dann dieses Flag. <br><br> Wenn LDAP_LOOKUP_FORESTS konfiguriert (nicht leer) ist, wird **dieses Flag als TRUE erzwungen**, unabhängig vom Wert der Registrierungseinstellung. In diesem Fall erfordert die NPS-Erweiterung, dass der globale Katalog mit dem AlternateLoginId-Attribut für jede Gesamtstruktur konfiguriert werden kann. |
| LDAP_LOOKUP_FORESTS | Zeichenfolge | Leer | Stellen Sie eine durch Semikolons getrennte Liste der Gesamtstrukturen bereit, die durchsucht werden sollen. Beispiel: *contoso.com;foobar.com*. Wenn dieser Registrierungswert konfiguriert wird, durchsucht die NPS-Erweiterung iterativ alle Gesamtstrukturen in der Reihenfolge, in der sie aufgelistet wurden, und gibt den ersten erfolgreichen AlternateLoginId-Wert zurück. Wenn dieser Registrierungswert nicht konfiguriert wird, ist die Suche nach AlternateLoginId auf die aktuelle Domäne beschränkt.|

Befolgen Sie zum Beheben von Problemen mit alternativen Anmelde-IDs die empfohlenen Schritte für [Alternate login ID errors (Fehler bei alternativen Anmelde-IDs)](howto-mfa-nps-extension-errors.md#alternate-login-id-errors).

## <a name="ip-exceptions"></a>IP-Ausnahmen

Wenn Sie die Verfügbarkeit von Servern überwachen müssen, z.B. wenn durch Lastenausgleichsmodule vor dem Übermitteln von Workloads überprüft wird, welche Server ausgeführt werden, sollen diese Überprüfungen nicht durch Überprüfungsanfragen blockiert werden. Erstellen Sie stattdessen eine Liste von IP-Adressen, die von Dienstkonten verwendet werden, und deaktivieren Sie die Multi-Factor Authentication-Anforderungen für diese Liste.

Wechseln Sie zu `HKLM\SOFTWARE\Microsoft\AzureMfa`, um eine Liste zulässiger IP-Adressen zu konfigurieren, und konfigurieren Sie den folgenden Registrierungswert:

| NAME | Type | Standardwert | BESCHREIBUNG |
| ---- | ---- | ------------- | ----------- |
| IP_WHITELIST | Zeichenfolge | Leer | Stellen Sie eine durch Semikolons getrennte Liste mit IP-Adressen bereit. Darunter sollten die IP-Adressen der Computer sein, von denen Serviceanforderungen stammen, wie der NAS/VPN-Server. IP-Adressbereiche und Subnetze werden nicht unterstützt. <br><br> Beispiel: *10.0.0.1;10.0.0.2;10.0.0.3*.

Wenn eine Anforderung von einer IP-Adresse eingeht, die in `IP_WHITELIST` enthalten ist, wird die zweistufige Überprüfung übersprungen. Die IP-Liste wird mit der IP-Adresse verglichen, die im *ratNASIPAddress*-Attribut der RADIUS-Anforderung angegeben ist. Wenn eine RADIUS-Anforderung ohne das Attribut ratNASIPAddress eingeht, wird die folgende Warnung protokolliert: „P_WHITE_LIST_WARNING::IP Whitelist is being ignored as source IP is missing in RADIUS request in NasIpAddress attribute.“ (P_WHITE_LIST_WARNING::IP-Whitelist wird ignoriert, da die Quell-IP in der RADIUS-Anforderung im NasIpAddress-Attribut fehlt.)

## <a name="next-steps"></a>Nächste Schritte

[Auflösen von Fehlermeldungen in der NPS-Erweiterung für Azure Multi-Factor Authentication](howto-mfa-nps-extension-errors.md)
