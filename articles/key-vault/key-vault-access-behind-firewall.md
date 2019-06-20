---
title: 'Zugreifen auf den Key Vault hinter einer Firewall: Azure Key Vault | Microsoft-Dokumentation'
description: Hier wird beschrieben, wie Sie aus einer Anwendung hinter einer Firewall auf Azure Key Vault zugreifen.
services: key-vault
author: amitbapat
manager: barbkess
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: ambapat
ms.openlocfilehash: bc6315f5ab264108369410b73a667fa1e07e1e44
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64689955"
---
# <a name="access-azure-key-vault-behind-a-firewall"></a>Zugreifen auf Azure Key Vault hinter einer Firewall

## <a name="what-ports-hosts-or-ip-addresses-should-i-open-to-enable-my-key-vault-client-application-behind-a-firewall-to-access-key-vault"></a>Welche Ports, Hosts oder IP-Adressen sollte ich öffnen, damit meine Key Vault-Clientanwendung hinter einer Firewall auf den Schlüsseltresor zugreifen kann?

Für den Zugriff auf einen Schlüsseltresor muss Ihre Schlüsseltresor-Clientanwendung auf mehrere Endpunkte für unterschiedliche Funktionalitäten zugreifen:

* Authentifizierung über Azure Active Directory (Azure AD)
* Verwaltung von Azure Key Vault Dies umfasst das Erstellen, Lesen, Aktualisieren, Löschen und Festlegen von Zugriffsrichtlinien per Azure Resource Manager.
* Der Zugriff auf Objekte (Schlüssel und Geheimnisse), die in Key Vault gespeichert sind, sowie deren Verwaltung werden über den Key Vault-spezifischen Endpunkt abgewickelt (Beispiel: [https://yourvaultname.vault.azure.net](https://yourvaultname.vault.azure.net)).  

Je nach Konfiguration und Umgebung gibt es verschiedene Varianten.

## <a name="ports"></a>Ports

Der gesamte Datenverkehr an einen Schlüsseltresor verläuft für alle drei Funktionen (Authentifizierung, Verwaltung und Datenebenenzugriff) über „HTTPS: Port 443“. Gelegentlich fällt aber auch HTTP-Datenverkehr (Port 80) für CRL an. Clients mit OCSP-Unterstützung sollten die Zertifikatsperrliste nicht erreichen, sondern nur gelegentlich [http://cdp1.public-trust.com/CRL/Omniroot2025.crl](http://cdp1.public-trust.com/CRL/Omniroot2025.crl).  

## <a name="authentication"></a>Authentication

Schlüsseltresor-Clientanwendungen müssen zur Authentifizierung auf Azure Active Directory-Endpunkte zugreifen. Der verwendete Endpunkt richtet sich nach der Azure AD-Mandantenkonfiguration, dem Typ von Prinzipal (Benutzerprinzipal oder Dienstprinzipal) und dem Kontotyp, z.B. Microsoft-Konto oder Geschäfts-, Schul- oder Unikonto.  

| Prinzipaltyp | Endpunkt:Port |
| --- | --- |
| Benutzer mit Microsoft-Konto<br> (Beispiel: user@hotmail.com) |**Global:**<br> login.microsoftonline.com:443<br><br> **Azure China:**<br> login.chinacloudapi.cn:443<br><br>**Azure US Government:**<br> login.microsoftonline.us:443<br><br>**Azure Deutschland:**<br> login.microsoftonline.de:443<br><br> und <br>login.live.com:443 |
| Benutzer- oder Dienstprinzipal mit einem Geschäfts-, Schul- oder Unikonto bei Azure AD (z.B. user@contoso.com) |**Global:**<br> login.microsoftonline.com:443<br><br> **Azure China:**<br> login.chinacloudapi.cn:443<br><br>**Azure US Government:**<br> login.microsoftonline.us:443<br><br>**Azure Deutschland:**<br> login.microsoftonline.de:443 |
| Benutzer- oder Dienstprinzipal mit einem Geschäfts-, Schul- oder Unikonto plus Active Directory-Verbunddienste (AD FS) oder einem anderen Verbundendpunkt (z.B. user@contoso.com) |Alle Endpunkte für ein Geschäfts-, Schul- oder Unikonto plus AD FS oder anderen Verbundendpunkten |

Es sind noch andere komplexe Szenarien möglich. Weitere Informationen finden Sie unter [Authentifizierungsszenarien für Azure AD](../active-directory/develop/authentication-scenarios.md), [Integrieren von Anwendungen in Azure Active Directory](../active-directory/develop/active-directory-how-to-integrate.md) und [Active Directory-Authentifizierungsprotokolle](https://msdn.microsoft.com/library/azure/dn151124.aspx).  

## <a name="key-vault-management"></a>Schlüsseltresorverwaltung

Für die Schlüsseltresorverwaltung (CRUD und Festlegen der Zugriffsrichtlinie) benötigt die Schlüsseltresor-Clientanwendung Zugriff auf einen Azure Resource Manager-Endpunkt.  

| Typ des Vorgangs | Endpunkt:Port |
| --- | --- |
| Vorgänge auf der Steuerungsebene des Schlüsseltresors<br> mit Azure Resource Manager |**Global:**<br> management.azure.com:443<br><br> **Azure China:**<br> management.chinacloudapi.cn:443<br><br> **Azure US Government:**<br> management.usgovcloudapi.net:443<br><br> **Azure Deutschland:**<br> management.microsoftazure.de:443 |
| Azure Active Directory Graph-API |**Global:**<br> graph.windows.net:443<br><br> **Azure China:**<br> graph.chinacloudapi.cn:443<br><br> **Azure US Government:**<br> graph.windows.net:443<br><br> **Azure Deutschland:**<br> graph.cloudapi.de:443 |

## <a name="key-vault-operations"></a>Schlüsseltresorvorgänge

Für alle Schlüsseltresorvorgänge in Bezug auf die Objektverwaltung (Schlüssel und Geheimnisse) und Kryptografie benötigt der Schlüsseltresorclient Zugriff auf den Schlüsseltresor-Endpunkt. Das DNS-Suffix des Endpunkts unterscheidet sich je nach Standort des Schlüsseltresors. Der Schlüsseltresor-Endpunkt hat das folgende Format: *Tresorname*.*regionsspezifisches DNS-Suffix*. Dies ist in der Tabelle unten beschrieben.  

| Typ des Vorgangs | Endpunkt:Port |
| --- | --- |
| Vorgänge, z.B. kryptografische Vorgänge für Schlüssel, Erstellen, Lesen, Aktualisieren und Löschen von Schlüsseln und Geheimnissen, Festlegen oder Abrufen von Tags und anderen Attributen für Sicherheitstresorobjekte (Schlüssel oder Geheimnisse) |**Global:**<br> &lt;Tresorname&gt;.vault.azure.net:443<br><br> **Azure China:**<br> &lt;Tresorname&gt;.vault.azure.cn:443<br><br> **Azure US Government:**<br> &lt;Tresorname&gt;.vault.usgovcloudapi.net:443<br><br> **Azure Deutschland:**<br> &lt;Tresorname&gt;.vault.microsoftazure.de:443 |

## <a name="ip-address-ranges"></a>IP-Adressbereiche

Der Key Vault-Dienst nutzt andere Azure-Ressourcen, z.B. die PaaS-Infrastruktur. Daher ist es nicht möglich, einen bestimmten Bereich mit IP-Adressen anzugeben, über die Endpunkte des Key Vault-Diensts zu bestimmten Zeiten verfügen. Wenn Ihre Firewall nur IP-Adressbereiche unterstützt, helfen Ihnen die Informationen im Dokument zu den [Microsoft Azure Datacenter-IP-Bereichen](https://www.microsoft.com/download/details.aspx?id=41653) weiter. Authentifizierung und Identität (Azure Active Directory) ist ein globaler Dienst und kann ohne vorherige Ankündigung ein Failover auf andere Regionen durchführen oder Datenverkehr verlagern. In diesem Szenario müssen alle [IP-Adressbereiche für Authentifizierung und Identität](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity_ip) der Firewall hinzugefügt werden.

## <a name="next-steps"></a>Nächste Schritte

Sollten Sie Fragen zu Key Vault haben, besuchen Sie die [Azure Key Vault-Foren](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault).
