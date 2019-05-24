---
title: Übersicht über die Sicherheit – Azure App Service | Microsoft-Dokumentation
description: Hier erfahren Sie, wie App Service zum Schutz Ihrer App beiträgt und wie Sie Ihre App noch besser vor Bedrohungen schützen.
keywords: Azure App Service, Web-App, mobile App, API-App, Funktions-App, Sicherheit, sicher, geschützt, Compliance, Konformität, konform, Zertifikat, Zertifikate, HTTPS, FTPS, TLS, Vertrauen, Verschlüsselung, Verschlüsseln, verschlüsselt, IP-Einschränkung, Authentifizierung, Autorisierung, authentifizieren, autorisieren, MSI, verwaltete Dienstidentität, verwaltete Identität, Geheimnisse, Geheimnis, Patchen, Patch, Patches, Version, Isolation, Netzwerkisolation, DDoS, MITM
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/24/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 374fd700f3ac99c00b922f4fca330fee9acfd704
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65955743"
---
# <a name="security-in-azure-app-service"></a>Sicherheit in Azure App Service

In diesem Artikel erfahren Sie, wie [Azure App Service](overview.md) zum Schutz Ihrer Web-App, Ihres Mobile App-Back-Ends, Ihrer API-App sowie Ihrer [Funktions-App](/azure/azure-functions/) beiträgt. Außerdem erfahren Sie, wie Sie Ihre App mithilfe der integrierten App Service-Features noch besser schützen können.

Die Plattformkomponenten von App Service werden aktiv geschützt und gehärtet. Zu diesen Komponenten zählen unter anderem virtuelle Azure-Computer, Speicher, Netzwerkverbindungen und Webframeworks sowie Verwaltungs- und Integrationsfeatures. Die Konformität von App Service wird kontinuierlich und streng geprüft, um Folgendes zu gewährleisten:

- Ihre App-Ressourcen sind zu ihrem [Schutz](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox) von den Azure-Ressourcen anderer Kunden isoliert.
- [VM-Instanzen und Laufzeitsoftware werden regelmäßig aktualisiert](overview-patch-os-runtime.md), um auf neu entdeckte Sicherheitsrisiken zu reagieren. 
- Die Weitergabe von Geheimnissen (etwa Verbindungszeichenfolgen) zwischen Ihrer App und anderen Azure-Ressourcen (etwa [SQL-Datenbank](https://azure.microsoft.com/services/sql-database/)) erfolgt innerhalb von Azure und überschreitet keine Netzwerkgrenzen. Gespeicherte Geheimnisse werden immer verschlüsselt.
- Die gesamte Kommunikation über die App Service-Verbindungsfeatures (beispielsweise [Hybridverbindung](app-service-hybrid-connections.md)) wird verschlüsselt. 
- Verbindungen mit Remoteverwaltungstools wie Azure PowerShell, Azure CLI, Azure SDKs und REST-APIs werden jeweils verschlüsselt.
- Das Bedrohungsmanagement schützt die Infrastruktur und die Plattform rund um die Uhr vor Schadsoftware sowie vor DDoS-Angriffen (Distributed Denial of Service), MITM-Angriffen (Man in the Middle) und anderen Bedrohungen.

Weitere Informationen zur Infrastruktur und Plattform-Sicherheit in Azure finden Sie unter [Azure Trust Center](https://azure.microsoft.com/overview/trusted-cloud/).

In den folgenden Abschnitten wird gezeigt, wie Sie Ihre App Service-App noch besser vor Bedrohungen schützen.

## <a name="https-and-certificates"></a>HTTPS und Zertifikate

App Service ermöglicht den Schutz von Apps mit [HTTPS](https://wikipedia.org/wiki/HTTPS). Der Standarddomänenname (\<App-Name>.azurewebsites.net) Ihrer App ist nach der App-Erstellung bereits über HTTPS zugänglich. Wenn Sie [eine benutzerdefinierte Domäne für Ihre App konfigurieren](app-service-web-tutorial-custom-domain.md), sollten Sie sie auch [mit einem benutzerdefinierten Zertifikat schützen](app-service-web-tutorial-custom-ssl.md), damit Clientbrowser sichere HTTPS-Verbindungen mit Ihrer benutzerdefinierten Domäne herstellen können. Dafür gibt es zwei Möglichkeiten:

- **App Service-Zertifikat:** Erstellen Sie ein Zertifikat direkt in Azure. Das Zertifikat wird sicher in [Azure Key Vault](/azure/key-vault/) aufbewahrt und kann in Ihre App Service-App importiert werden. Weitere Informationen finden Sie unter [Kaufen und Konfigurieren eines SSL-Zertifikats für Ihren Azure App Service](web-sites-purchase-ssl-web-site.md).
- **Drittanbieterzertifikat:** Laden Sie ein benutzerdefiniertes SSL-Zertifikat hoch, das Sie bei einer vertrauenswürdigen Zertifizierungsstelle erworben haben, und binden Sie es an Ihre App Service-App. App Service unterstützt sowohl Zertifikate für eine einzelne Domäne als auch Platzhalterzertifikate. Zu Testzwecken werden auch selbstsignierte Zertifikate unterstützt. Weitere Informationen finden Sie im Tutorial [Binden eines vorhandenen benutzerdefinierten SSL-Zertifikats an Azure App Service](app-service-web-tutorial-custom-ssl.md).

## <a name="insecure-protocols-http-tls-10-ftp"></a>Unsichere Protokolle (HTTP, TLS 1.0, FTP)

Mit App Service können Sie mit nur einem Klick HTTPS erzwingen, um Ihre App vor allen unverschlüsselten Verbindungen (HTTP) zu schützen. Unsichere Anforderungen werden abgelehnt, bevor sie Ihren Anwendungscode erreichen. Weitere Informationen finden Sie unter [Erzwingen von HTTPS](app-service-web-tutorial-custom-ssl.md#enforce-https).

[TLS 1.0](https://wikipedia.org/wiki/Transport_Layer_Security) ist laut Branchenstandards wie [PCI-DSS](https://wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard) nicht mehr sicher. App Service ermöglicht die Deaktivierung veralteter Protokolle durch [Erzwingung von TLS 1.1/1.2](app-service-web-tutorial-custom-ssl.md#enforce-tls-versions).

Für die Dateibereitstellung unterstützt App Service sowohl FTP als auch FTPS. Nach Möglichkeit sollte jedoch immer FTPS anstelle von FTP verwendet werden. Wenn Sie eines dieser Protokolle (oder beide) nicht verwenden, sollten Sie die nicht verwendeten Protokolle [deaktivieren](deploy-ftp.md#enforce-ftps).

## <a name="static-ip-restrictions"></a>Einschränkungen für statische IP-Adressen

Standardmäßig akzeptiert Ihre App Service-App Anforderungen von allen IP-Adressen aus dem Internet, der Zugriff kann aber auf eine kleine Teilmenge von IP-Adressen beschränkt werden. In App Service unter Windows können Sie eine Liste mit IP-Adressen definieren, die auf Ihre App zugreifen dürfen. Die Liste mit den zulässigen IP-Adressen kann einzelne IP-Adressen oder einen durch eine Subnetzmaske definierten IP-Adressbereich enthalten. Weitere Informationen finden Sie unter [Statische Azure App Service-IP-Einschränkungen](app-service-ip-restrictions.md).

Für App Service unter Windows können Sie IP-Adressen auch dynamisch einschränken, indem Sie _web.config_ konfigurieren. Weitere Informationen finden Sie unter [Dynamische IP-Sicherheit\<dynamicIpSecurity>](https://docs.microsoft.com/iis/configuration/system.webServer/security/dynamicIpSecurity/).

## <a name="client-authentication-and-authorization"></a>Clientauthentifizierung und -autorisierung

Azure App Service bietet eine vorgefertigte Authentifizierung und Autorisierung von Benutzern und Client-Apps. Im aktivierten Zustand kann dieses Feature Benutzer und Client-Apps mit wenig oder ganz ohne Anwendungscode anmelden. Sie können Ihre eigene Authentifizierungs- und Autorisierungslösung implementieren oder App Service mit der Authentifizierung und Autorisierung betrauen. Das Authentifizierungs- und Autorisierungsmodul verarbeitet Webanforderungen vor der Weitergabe an Ihren Anwendungscode und lehnt nicht autorisierte Anforderungen ab, bevor sie Ihren Code erreichen.

Von der App Service-Authentifizierung und -Autorisierung werden mehrere Authentifizierungsanbieter unterstützt. Hierzu zählen beispielsweise Azure Active Directory, Microsoft-Konten, Facebook, Google und Twitter. Weitere Informationen finden Sie unter [Authentifizierung und Autorisierung in Azure App Service](overview-authentication-authorization.md).

## <a name="service-to-service-authentication"></a>Dienst-zu-Dienst-Authentifizierung

Für die Authentifizierung bei einem Back-End-Dienst bietet App Service zwei unterschiedliche bedarfsspezifische Mechanismen:

- **Dienstidentität:** Melden Sie sich unter Verwendung der Identität der App bei der Remoteressource an. Mit App Service können Sie ganz einfach eine [verwaltete Identität](overview-managed-identity.md) erstellen und für die Authentifizierung bei anderen Diensten (beispielsweise [Azure SQL-Datenbank](/azure/sql-database/) oder [Azure Key Vault](/azure/key-vault/)) verwenden. Ein umfassendes Tutorial für diesen Ansatz finden Sie unter [Tutorial: Schützen der SQL-Datenbank-Verbindung mittels verwalteter Identität](app-service-web-tutorial-connect-msi.md).
- **Im Namen von (on behalf of, OBO):** Greifen Sie unter Verwendung von delegiertem Zugriff im Namen des Benutzers auf Remoteressourcen zu. Mit Azure Active Directory als Authentifizierungsanbieter kann Ihre App Service-App eine delegierte Anmeldung bei einem Remotedienst durchführen – etwa bei der [Azure Active Directory Graph-API](../active-directory/develop/active-directory-graph-api.md) oder bei einer Remote-API-App in App Service. Ein umfassendes Tutorial für diesen Ansatz finden Sie unter [Tutorial: Umfassendes Authentifizieren und Autorisieren von Benutzern in Azure App Service](app-service-web-tutorial-auth-aad.md).

## <a name="connectivity-to-remote-resources"></a>Konnektivität mit Remoteressourcen

Es gibt drei Arten von Remoteressourcen, auf die Ihre App ggf. Zugriff benötigt: 

- [Azure-Ressourcen](#azure-resources)
- [Ressourcen in einer Azure Virtual Network-Instanz](#resources-inside-an-azure-virtual-network)
- [Lokale Ressourcen](#on-premises-resources)

In jedem dieser Fälle bietet App Service eine Möglichkeit zur Herstellung sicherer Verbindungen. Nichtsdestotrotz sollten Sie sich an die bewährten Methoden für die Sicherheit halten. Verwenden Sie also beispielsweise immer verschlüsselte Verbindungen, auch wenn die Back-End-Ressource nicht verschlüsselte Verbindungen zulässt. Achten Sie zudem darauf, dass Ihr Azure-Back-End-Dienst den Mindestsatz an IP-Adressen zulässt. Die ausgehenden IP-Adressen für Ihre App finden Sie unter [Ein- und ausgehende IP-Adressen in Azure App Service](overview-inbound-outbound-ips.md).

### <a name="azure-resources"></a>Azure-Ressourcen

Wenn Ihre App eine Verbindung mit Azure-Ressourcen (etwa mit [SQL-Datenbank](https://azure.microsoft.com/services/sql-database/) und [Azure Storage](/azure/storage/)) herstellt, bleibt die Verbindung innerhalb von Azure und überschreitet keine Netzwerkgrenzen. Die Verbindung durchläuft jedoch die gemeinsam genutzten Netzwerke in Azure. Stellen Sie daher sicher, dass Ihre Verbindung verschlüsselt ist. 

Wenn Ihre App in einer [App Service-Umgebung](environment/intro.md) gehostet wird, sollten Sie [über Virtual Network-Dienstendpunkte eine Verbindung mit unterstützten Azure-Diensten herstellen](../virtual-network/virtual-network-service-endpoints-overview.md).

### <a name="resources-inside-an-azure-virtual-network"></a>Ressourcen in einer Azure Virtual Network-Instanz

Ihre App kann mittels [Virtual Network-Integration](web-sites-integrate-with-vnet.md) auf Ressourcen in einer [Azure Virtual Network-Instanz](/azure/virtual-network/) zugreifen. Die Virtual Network-Integration wird über ein Point-to-Site-VPN implementiert. Die App kann daraufhin unter Verwendung der privaten IP-Adressen auf die Ressourcen in der Virtual Network-Instanz zugreifen. Die Point-to-Site-Verbindung durchläuft allerdings die gemeinsam genutzten Netzwerke in Azure. 

Wenn Sie die Ressourcenkonnektivität vollständig von den gemeinsam genutzten Netzwerken in Azure isolieren möchten, müssen Sie Ihre App in einer [App Service-Umgebung](environment/intro.md) erstellen. Da eine App Service-Umgebung immer in einer dedizierten Virtual Network-Instanz bereitgestellt wird, ist die Konnektivität zwischen Ihrer App und den Ressourcen innerhalb der Virtual Network-Instanz vollständig isoliert. Informationen zu anderen Aspekten der Netzwerksicherheit in einer App Service-Umgebung finden Sie unter [Netzwerkisolation](#network-isolation).

### <a name="on-premises-resources"></a>Lokale Ressourcen

Für den sicheren Zugriff auf lokale Ressourcen (beispielsweise Datenbanken) stehen drei Optionen zur Verfügung: 

- [Hybridverbindungen:](app-service-hybrid-connections.md) Bei dieser Option wird über einen TCP-Tunnel eine Point-to-Point-Verbindung mit Ihrer Remoteressource hergestellt. Für den TCP-Tunnel wird TLS 1.2 mit SAS-Schlüsseln (Shared Access Signature) verwendet.
- [Virtual Network-Integration](web-sites-integrate-with-vnet.md) mit Site-to-Site-VPN: Diese Option entspricht der Beschreibung unter [Ressourcen in einer Azure Virtual Network-Instanz](#resources-inside-an-azure-virtual-network), die Virtual Network-Instanz kann allerdings über ein [Site-to-Site-VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) mit Ihrem lokalen Netzwerk verbunden werden. In dieser Netzwerktopologie kann Ihre App auf die gleiche Weise eine Verbindung mit lokalen Ressourcen herstellen wie mit anderen Ressourcen in der Virtual Network-Instanz.
- [App Service-Umgebung](environment/intro.md) mit Site-to-Site-VPN: Diese Option entspricht der Beschreibung unter [Ressourcen in einer Azure Virtual Network-Instanz](#resources-inside-an-azure-virtual-network), die Virtual Network-Instanz kann allerdings über ein [Site-to-Site-VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) mit Ihrem lokalen Netzwerk verbunden werden. In dieser Netzwerktopologie kann Ihre App auf die gleiche Weise eine Verbindung mit lokalen Ressourcen herstellen wie mit anderen Ressourcen in der Virtual Network-Instanz.

## <a name="application-secrets"></a>Anwendungsgeheimnisse

Speichern Sie Anwendungsgeheimnisse wie Datenbank-Anmeldeinformationen, API-Token und private Schlüssel nicht in Ihrem Code oder in Konfigurationsdateien. Für den Zugriff auf diese Geheimnisse hat es sich bewährt, [Umgebungsvariablen](https://wikipedia.org/wiki/Environment_variable) mit dem Standardmuster in der Sprache Ihrer Wahl zu verwenden. In App Service werden Umgebungsvariablen über [App-Einstellungen](configure-common.md#configure-app-settings) (und speziell für .NET-Anwendungen über [Verbindungszeichenfolgen](configure-common.md#configure-connection-strings)) definiert. App-Einstellungen und Verbindungszeichenfolgen werden in Azure verschlüsselt gespeichert und erst entschlüsselt, wenn sie beim Start der App in ihren Prozessspeicher eingefügt werden. Die Verschlüsselungsschlüssel werden regelmäßig rotiert.

Wenn Sie eine erweiterte Geheimnisverwaltung benötigen, können Sie Ihre App Service-App auch in [Azure Key Vault](/azure/key-vault/) integrieren. Durch den [Zugriff auf Key Vault mit einer verwalteten Identität](../key-vault/tutorial-web-application-keyvault.md) kann Ihre App Service-App sicher auf die benötigten Geheimnisse zugreifen.

## <a name="network-isolation"></a>Netzwerkisolation

Mit Ausnahme des Tarifs **Isoliert** wird Ihre App bei allen Tarifen in der gemeinsam genutzten Netzwerkinfrastruktur in App Service ausgeführt. So werden beispielsweise die öffentlichen IP-Adressen und Front-End-Lastenausgleichsmodule gemeinsam mit anderen Mandanten genutzt. Im Tarif **Isoliert** werden Ihre Apps in einer dedizierten [App Service-Umgebung](environment/intro.md) ausgeführt, wodurch eine vollständige Netzwerkisolation erreicht wird. Eine App Service-Umgebung wird in Ihrer eigenen Instanz von [Azure Virtual Network](/azure/virtual-network/) betrieben. Dadurch haben Sie folgende Möglichkeiten: 

- Sie können den Netzwerkdatenverkehr mithilfe von [Netzwerksicherheitsgruppen](../virtual-network/virtual-networks-dmz-nsg.md) einschränken. 
- Sie können Ihre Apps über einen dedizierten öffentlichen Endpunkt mit dedizierten Front-Ends bereitstellen.
- Sie können interne Anwendungen unter Verwendung eines internen Lastenausgleichsmoduls (internal load balancer, ILB) bereitstellen, wodurch der Zugriff nur innerhalb Ihrer Azure Virtual Network-Instanz möglich ist. Das interne Lastenausgleichsmodul besitzt eine IP-Adresse aus Ihrem privaten Subnetz, sodass Ihre Apps vollständig vom Internet isoliert sind.
- Sie können [ein internes Lastenausgleichsmodul hinter einer Web Application Firewall (WAF) verwenden](environment/integrate-with-application-gateway.md). Die WAF bietet professionellen Schutz für Ihre öffentlichen Anwendungen – beispielsweise DDoS-Schutz, URI-Filterung und Verhinderung der Einschleusung von SQL-Befehlen.

Weitere Informationen finden Sie in der [Einführung in die App Service-Umgebungen](environment/intro.md). 
