---
title: Schützen von virtuellen Azure-Hubs mithilfe von Check Point Cloudguard Connect
description: Erfahren Sie mehr über Check Point CloudGuard Connect zum Schützen von virtuellen Azure-Hubs.
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 10/30/2020
ms.author: victorh
ms.openlocfilehash: 3c61dc689d19e1a7d6f9b6dbefae846e9458d750
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/01/2020
ms.locfileid: "93146857"
---
# <a name="secure-virtual-hubs-using-check-point-cloudguard-connect"></a>Schützen von virtuellen Hubs mithilfe von Check Point Cloudguard Connect

Check Point CloudGuard Connect ist ein vertrauenswürdiger Sicherheitspartner in Azure Firewall Manager. Es schützt weltweit verteilte Verbindungen von Zweigstellen zum Internet (B2I) oder Verbindungen von einem virtuellen Netzwerk zum Internet (V2I) mit verbesserter Abwehr von Bedrohungen. 

Mit einer einfachen Konfiguration im Azure Firewall Manager können Sie Verbindungen von Branch-Hubs und virtuellen Netzwerken mit dem Internet über SECaaS (CloudGuard Connect Security as a Service) leiten. Der Datenverkehr wird bei der Übertragung von Ihrem Hub zum Check Point-Clouddienst in IPsec VPN-Tunneln geschützt.

Wenn Sie die automatische Synchronisierung im Check Point-Portal aktivieren, wird jede im Azure-Portal als *geschützt* markierte Ressource automatisch gesichert. Sie müssen Ihre Ressourcen nicht zweimal verwalten. Sie entscheiden sich einfach dafür, sie einmal im Azure-Portal zu schützen.

Check Point vereinigt mehrere Sicherheitsdienste unter einem Dach. Der integrierte Sicherheitsdatenverkehr wird einmal entschlüsselt und in einem einzigen Durchgang überprüft. Anwendungssteuerung, URL-Filterung und Content Awareness (DLP) erzwingen eine sichere Webverwendung und schützen Ihre Daten. IPS und Antivirus schützen Benutzer vor bekannten netzwerkbasierten Exploits. Anti-Bot blockiert Verbindungen zu Command-and-Control-Servern und warnt Sie, wenn ein Host infiziert ist.

Threat Emulation (Sandboxing) schützt Benutzer vor unbekannten und Zero-Day-Bedrohungen. Check Point SandBlast Zero-Day Protection ist eine in der Cloud gehostete Sandbox-Technologie, bei der Dateien schnell unter Quarantäne gestellt und inspiziert werden können. Es wird in einer virtuellen Sandbox ausgeführt, um schädliches Verhalten zu erkennen, bevor es in Ihr Netzwerk gelangt. Es verhindert Bedrohungen, bevor Schaden angerichtet wird, um dem Personal wertvolle Zeit bei der Reaktion auf Bedrohungen zu sparen. 

## <a name="deployment-example"></a>Beispiel für die Bereitstellung

Sehen Sie sich das folgende Video an, um zu erfahren, wie Check Point CloudGuard Connect als vertrauenswürdiger Azure-Sicherheitspartner bereitgestellt wird.

> [!VIDEO https://www.youtube.com/embed/C8AuN76DEmU]

## <a name="next-steps"></a>Nächste Schritte

- [Bereitstellen eines Sicherheitspartneranbieters](deploy-trusted-security-partner.md)