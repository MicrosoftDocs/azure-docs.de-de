---
title: 'Zugreifen auf den Key Vault hinter einer Firewall: Azure Key Vault | Microsoft-Dokumentation'
description: Hier erfahren Sie mehr über die Ports, Hosts oder IP-Adressen, die geöffnet werden sollten, damit eine Schlüsseltresor-Clientanwendung hinter einer Firewall auf einen Schlüsseltresor zugreifen kann.
services: key-vault
author: amitbapat
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/12/2019
ms.author: ambapat
ms.openlocfilehash: ca5842fb268c20f8ae58eb5f683229c4ae3919f4
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93289165"
---
# <a name="access-azure-key-vault-behind-a-firewall"></a>Zugreifen auf Azure Key Vault hinter einer Firewall

## <a name="what-ports-hosts-or-ip-addresses-should-i-open-to-enable-my-key-vault-client-application-behind-a-firewall-to-access-key-vault"></a>Welche Ports, Hosts oder IP-Adressen sollte ich öffnen, damit meine Key Vault-Clientanwendung hinter einer Firewall auf den Schlüsseltresor zugreifen kann?

Für den Zugriff auf einen Schlüsseltresor muss Ihre Schlüsseltresor-Clientanwendung auf mehrere Endpunkte für unterschiedliche Funktionalitäten zugreifen:

* Authentifizierung über Azure Active Directory (Azure AD)
* Verwaltung von Azure Key Vault Dies umfasst das Erstellen, Lesen, Aktualisieren, Löschen und Festlegen von Zugriffsrichtlinien per Azure Resource Manager.
* Der Zugriff auf Objekte (Schlüssel und Geheimnisse), die in Key Vault gespeichert sind, sowie deren Verwaltung werden über den Key Vault-spezifischen Endpunkt abgewickelt (Beispiel: `https://yourvaultname.vault.azure.net`).  

Je nach Konfiguration und Umgebung gibt es verschiedene Varianten.

## <a name="ports"></a>Ports

Der gesamte Datenverkehr an einen Schlüsseltresor verläuft für alle drei Funktionen (Authentifizierung, Verwaltung und Datenebenenzugriff) über „HTTPS: Port 443“. Gelegentlich fällt aber auch HTTP-Datenverkehr (Port 80) für CRL an. Clients mit OCSP-Unterstützung sollten die Zertifikatsperrliste nicht erreichen, sondern nur gelegentlich [http://cdp1.public-trust.com/CRL/Omniroot2025.crl](http://cdp1.public-trust.com/CRL/Omniroot2025.crl).  

## <a name="authentication"></a>Authentifizierung

Schlüsseltresor-Clientanwendungen müssen zur Authentifizierung auf Azure Active Directory-Endpunkte zugreifen. Der verwendete Endpunkt richtet sich nach der Azure AD-Mandantenkonfiguration, dem Typ von Prinzipal (Benutzerprinzipal oder Dienstprinzipal) und dem Kontotyp, z.B. Microsoft-Konto oder Geschäfts-, Schul- oder Unikonto.  

| Prinzipaltyp | Endpunkt:Port |
| --- | --- |
| Benutzer mit Microsoft-Konto<br> (Beispiel: user@hotmail.com) |**Global:**<br> login.microsoftonline.com:443<br><br> **Azure China:**<br> login.chinacloudapi.cn:443<br><br>**Azure US Government:**<br> login.microsoftonline.us:443<br><br>**Azure Deutschland:**<br> login.microsoftonline.de:443<br><br> and <br>login.live.com:443 |
| Benutzer- oder Dienstprinzipal mit einem Geschäfts-, Schul- oder Unikonto bei Azure AD (z.B. user@contoso.com) |**Global:**<br> login.microsoftonline.com:443<br><br> **Azure China:**<br> login.chinacloudapi.cn:443<br><br>**Azure US Government:**<br> login.microsoftonline.us:443<br><br>**Azure Deutschland:**<br> login.microsoftonline.de:443 |
| Benutzer- oder Dienstprinzipal mit einem Geschäfts-, Schul- oder Unikonto plus Active Directory-Verbunddienste (AD FS) oder einem anderen Verbundendpunkt (z.B. user@contoso.com) |Alle Endpunkte für ein Geschäfts-, Schul- oder Unikonto plus AD FS oder anderen Verbundendpunkten |

Es sind noch andere komplexe Szenarien möglich. Weitere Informationen finden Sie unter [Authentifizierungsszenarien für Azure AD](../../active-directory/develop/authentication-vs-authorization.md), [Integrieren von Anwendungen in Azure Active Directory](../../active-directory/develop/active-directory-how-to-integrate.md) und [Active Directory-Authentifizierungsprotokolle](/previous-versions/azure/dn151124(v=azure.100)).  

## <a name="key-vault-management"></a>Schlüsseltresorverwaltung

Für die Schlüsseltresorverwaltung (CRUD und Festlegen der Zugriffsrichtlinie) benötigt die Schlüsseltresor-Clientanwendung Zugriff auf einen Azure Resource Manager-Endpunkt.  

| Typ der Operation | Endpunkt:Port |
| --- | --- |
| Vorgänge auf der Steuerungsebene des Schlüsseltresors<br> mit Azure Resource Manager |**Global:**<br> management.azure.com:443<br><br> **Azure China:**<br> management.chinacloudapi.cn:443<br><br> **Azure US Government:**<br> management.usgovcloudapi.net:443<br><br> **Azure Deutschland:**<br> management.microsoftazure.de:443 |
| Microsoft Graph-API |**Global:**<br> graph.microsoft.com:443<br><br> **Azure China:**<br> graph.chinacloudapi.cn:443<br><br> **Azure US Government:**<br> graph.microsoft.com:443<br><br> **Azure Deutschland:**<br> graph.cloudapi.de:443 |

## <a name="key-vault-operations"></a>Schlüsseltresorvorgänge

Für alle Schlüsseltresorvorgänge in Bezug auf die Objektverwaltung (Schlüssel und Geheimnisse) und Kryptografie benötigt der Schlüsseltresorclient Zugriff auf den Schlüsseltresor-Endpunkt. Das DNS-Suffix des Endpunkts unterscheidet sich je nach Standort des Schlüsseltresors. Der Schlüsseltresor-Endpunkt hat das folgende Format: *Tresorname*. *regionsspezifisches DNS-Suffix*. Dies ist in der Tabelle unten beschrieben.  

| Typ der Operation | Endpunkt:Port |
| --- | --- |
| Vorgänge, z.B. kryptografische Vorgänge für Schlüssel, Erstellen, Lesen, Aktualisieren und Löschen von Schlüsseln und Geheimnissen, Festlegen oder Abrufen von Tags und anderen Attributen für Sicherheitstresorobjekte (Schlüssel oder Geheimnisse) |**Global:**<br> &lt;Tresorname&gt;.vault.azure.net:443<br><br> **Azure China:**<br> &lt;Tresorname&gt;.vault.azure.cn:443<br><br> **Azure US Government:**<br> &lt;Tresorname&gt;.vault.usgovcloudapi.net:443<br><br> **Azure Deutschland:**<br> &lt;Tresorname&gt;.vault.microsoftazure.de:443 |

## <a name="ip-address-ranges"></a>IP-Adressbereiche

Der Key Vault-Dienst nutzt andere Azure-Ressourcen, z.B. die PaaS-Infrastruktur. Daher ist es nicht möglich, einen bestimmten Bereich mit IP-Adressen anzugeben, über die Endpunkte des Key Vault-Diensts zu bestimmten Zeiten verfügen. Wenn Ihre Firewall nur IP-Adressbereiche unterstützt, helfen Ihnen die Informationen in den folgenden Dokumenten zu IP-Bereichen für Microsoft Azure-Rechenzentren weiter:
* [Öffentlich](https://www.microsoft.com/en-us/download/details.aspx?id=56519)
* [US Gov](https://www.microsoft.com/en-us/download/details.aspx?id=57063)
* [Deutschland](https://www.microsoft.com/en-us/download/details.aspx?id=57064)
* [China](https://www.microsoft.com/en-us/download/details.aspx?id=57062)

Authentifizierung und Identität (Azure Active Directory) ist ein globaler Dienst und kann ohne vorherige Ankündigung ein Failover auf andere Regionen durchführen oder Datenverkehr verlagern. In diesem Szenario müssen alle [IP-Adressbereiche für Authentifizierung und Identität](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity_ip) der Firewall hinzugefügt werden.

## <a name="next-steps"></a>Nächste Schritte

Besuchen Sie die [Microsoft-Seite mit häufig gestellten Fragen zu Azure Key Vault](/answers/topics/azure-key-vault.html), wenn Sie Fragen zu Key Vault haben.