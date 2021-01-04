---
title: Hinzufügen der Azure-Portal-URLs zu einer Liste sicherer Adressen für Ihre Firewall oder Ihren Proxyserver
description: Hinzufügen dieser URL zu einer Proxyserverumgehung für die Kommunikation mit dem Azure-Portal und dessen Dienste
ms.date: 04/10/2020
ms.topic: conceptual
ms.openlocfilehash: 7d9c8222ee85c0c16ec1e1926335ac06e0389797
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2020
ms.locfileid: "96745875"
---
# <a name="safelist-the-azure-portal-urls-on-your-firewall-or-proxy-server"></a>Hinzufügen der Azure-Portal-URLs zu einer Liste sicherer Adressen für Ihre Firewall oder Ihren Proxyserver

Sie können lokale Sicherheitsgeräte so konfigurieren, dass Sicherheitseinschränkungen für die Azure-Portal-URLs umgangen werden. Diese Konfiguration kann die Leistung und Konnektivität zwischen Ihrem lokalen Netzwerk oder einem WAN und der Azure-Cloud verbessern.

Netzwerkadministratoren stellen häufig Proxyserver, Firewalls oder andere Geräte bereit. Diese Geräte helfen dabei, die Art und Weise zu steuern, wie Benutzer auf das Internet zugreifen. Regeln, die Benutzer schützen sollen, können gerechtfertigten und geschäftsbezogenen Internetdatenverkehr blockieren oder verlangsamen. Dies betrifft z. B. auch Ihre Kommunikation mit Azure. Zur Optimierung der Konnektivität zwischen Ihrem Netzwerk und dem Azure-Portal und dessen Diensten wird empfohlen, Azure-Portal-URLS zu Ihrer Liste mit sicheren Adressen hinzuzufügen.

## <a name="azure-portal-urls-for-proxy-bypass"></a>Azure-Portal-URLs für Proxyumgehung

Die für das Azure-Portal in die Liste mit sicheren Adressen aufzunehmenden URL-Endpunkte sind für die Azure-Cloud spezifisch, in der Ihre Organisation bereitgestellt wird. Um dem Netzwerkdatenverkehr an diese Endpunkte zu erlauben, Einschränkungen zu umgehen, wählen Sie Ihre Cloud aus. Fügen Sie dann die Liste der URLs Ihrem Proxyserver oder der Firewall hinzu.

#### <a name="public-cloud"></a>[Öffentliche Cloud](#tab/public-cloud)

```
*.aadcdn.microsoftonline-p.com
*.aka.ms
*.applicationinsights.io
*.azure.com
*.azure.net
*.azurefd.net
*.azure-api.net
*.azuredatalakestore.net
*.azureedge.net
*.loganalytics.io
*.microsoft.com
*.microsoftonline.com
*.microsoftonline-p.com
*.msauth.net
*.msftauth.net
*.trafficmanager.net
*.visualstudio.com
*.windows.net
*.windows-int.net
```

#### <a name="us-government-cloud"></a>[US- Government Cloud](#tab/us-government-cloud)

```
*.applicationinsights.us
*.azure.us
*.loganalytics.us
*.microsoft.us
*.microsoftonline.us
*.msauth.net
*.usgovcloudapi.net
*.usgovtrafficmanager.net
*.windowsazure.us
```

#### <a name="china-government-cloud"></a>[China-Government Cloud](#tab/china-government-cloud)

```
*.azure.cn
*.microsoft.cn
*.microsoftonline.cn
*.chinacloudapi.cn
*.trafficmanager.cn
*.chinacloudsites.cn
*.windowsazure.cn
```
---

> [!NOTE]
> Der Datenverkehr für diese Endpunkte verwendet TCP-Standardports für HTTP (80) und HTTPS (443).
>
>
