---
author: vhorne
ms.service: application-gateway
ms.topic: include
ms.date: 6/5/2019
ms.author: victorh
ms.openlocfilehash: 592e1973344b231693077f8286a41dfd67a8d188
ms.sourcegitcommit: 6932af4f4222786476fdf62e1e0bf09295d723a1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/05/2019
ms.locfileid: "66689116"
---
| Resource | Standard/maximales Limit | Hinweis |
| --- | --- | --- |
| Azure Application Gateway |1\.000 pro Abonnement | |
| Front-End-IP-Konfigurationen |2 |1 öffentliche und 1 private |
| Front-End-Ports |100<sup>1</sup> | |
| Back-End-Adresspools |100<sup>1</sup> | |
| Back-End-Server pro Pool |1\.200 | |
| HTTP-Listener |100<sup>1</sup> | |
| HTTP-Lastenausgleichsregeln |100<sup>1</sup> | |
| Back-End-HTTP-Einstellungen |100<sup>1</sup> | |
| Instanzen pro Gateway |32 | |
| SSL-Zertifikate |100<sup>1</sup> |1 pro HTTP-Listener |
| Maximale SSL-Zertifikatgröße |V1 SKU – 10 KB<br>V2 SKU – 25 KB| |
| Authentifizierungszertifikate |100 | |
| Vertrauenswürdige Stammzertifikate |100 | |
| Minimales Anforderungstimeout |1 Sekunde | |
| Maximales Anforderungstimeout |24 Stunden | |
| Anzahl der Websites |100<sup>1</sup> |1 pro HTTP-Listener |
| URL-Zuordnungen pro Listener |1 | |
| Maximale Anzahl pfadbasierter Regeln pro URL-Zuordnung|100||
| Umleitungskonfiguration |100<sup>1</sup>| |
| Gleichzeitige WebSocket-Verbindungen |Mittelgroße Gateways 20K<br> Große Gateways 50K| |
| Maximale URL-Länge|8\.000||
| Maximale Dateiuploadgröße (Standard) |2 GB | |
| Maximale Dateiuploadgröße (WAF) |Mittlere WAF-Gateways, 100 MB<br>Große WAF-Gateways, 500 MB| |
| WAF-Textgrößenbeschränkung (ohne Dateien)|128 KB||
|Maximale benutzerdefinierte WAF-Regeln|100||

<sup>1</sup> Bei WAF-fähigen SKUs sollten Sie für optimale Leistung die Anzahl der Ressourcen auf 40 beschränken.
