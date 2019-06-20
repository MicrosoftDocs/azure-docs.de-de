---
title: Automatische Skalierung und zonenredundantes Application Gateway in Azure
description: In diesem Artikel werden die Standard_v2- und WAF_v2-SKU der Azure-Anwendung vorgestellt, die Autoskalierung und zonenredundante Features umfassen.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 5/22/2019
ms.author: victorh
ms.openlocfilehash: 8e17c5e34ec3e2397c3054b1d0e0d97dbf410db2
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65986871"
---
# <a name="autoscaling-and-zone-redundant-application-gateway"></a>Automatische Skalierung und zonenredundantes Application Gateway 

Application Gateway und Web Application Firewall (WAF) sind auch in einer Standard_v2- und WAF_v2-SKU verfügbar. Die v2-SKU bietet Leistungsverbesserungen und zusätzliche Unterstützung für wichtige neue Features wie automatische Skalierung und Zonenredundanz sowie Unterstützung für statische VIPs. Vorhandene Features in der Standard- and WAF-SKU werden in der neuen v2-SKU weiterhin unterstützt. Es gelten aber einige wenige Ausnahmen, die im Abschnitt mit dem [Vergleich](#differences-with-v1-sku) aufgeführt sind.

Die neue v2-SKU enthält die folgenden Verbesserungen:

- **Automatische Skalierung**: Für Application Gateway- oder WAF-Bereitstellungen unter der SKU mit automatischer Skalierung ist nun das zentrale Hochskalieren und Herunterskalieren je nach Veränderung der Netzwerkdatenverkehr-Auslastungsmuster möglich. Durch die automatische Skalierung entfällt auch die Notwendigkeit, während des Bereitstellens eine Bereitstellungsgröße oder eine Anzahl von Instanzen auszuwählen. Diese SKU bietet wahre Elastizität. In der Standard_v2- und WAF_v2-SKU kann Application Gateway sowohl mit fester Kapazität (automatische Skalierung deaktiviert) als auch mit aktivierter automatischer Skalierung betrieben werden. Der Modus mit fester Kapazität empfiehlt sich für Szenarien mit einheitlichen und vorhersagbaren Workloads. Der Modus mit automatischer Skalierung empfiehlt sich für Anwendungen, bei denen der Anwendungsdatenverkehr Schwankungen unterworfen ist.
- **Zonenredundanz**: Eine Application Gateway- oder WAF-Bereitstellung kann sich über mehrere Verfügbarkeitszonen erstrecken, sodass nicht mehr in jeder Zone mit einem Traffic Manager separate Application Gateway-Instanzen bereitgestellt werden müssen. Sie können eine einzelne Zone oder mehrere Zonen mit bereitgestellten Application Gateway-Instanzen auswählen, um für größere Stabilität gegenüber Zonenausfällen zu sorgen. Der Back-End-Pool für Anwendungen kann auf ähnliche Weise auf Verfügbarkeitszonen verteilt werden.

  Zonenredundanz ist nur dort verfügbar, wo auch Azure-Zonen verfügbar sind. In anderen Regionen werden alle anderen Features unterstützt. Weitere Informationen finden Sie unter [Was sind Verfügbarkeitszonen in Azure?](../availability-zones/az-overview.md#services-support-by-region)
- **Statische VIP**: Die Application Gateway v2-SKU unterstützt exklusiv den statischen VIP-Typ. Dadurch wird sichergestellt, dass die Application Gateway zugeordnete VIP während des Lebenszyklus der Bereitstellung unverändert bleibt, selbst nach einem Neustart.
- **Erneutes Generieren von Headern**: Application Gateway ermöglicht Ihnen das Hinzufügen, Entfernen oder Aktualisieren von HTTP-Anforderungs- und Antwortheadern mit dem v2-SKU. Weitere Informationen finden Sie unter [Erneutes Generieren von HTTP-Headern mit Application Gateway](rewrite-http-headers.md).
- **Key Vault-Integration (Vorschau)** : Application Gateway v2 unterstützt die Integration mit Key Vault (in der öffentlichen Vorschau) für Serverzertifikate, die an HTTPS-fähige Listener angefügt sind. Weitere Informationen finden Sie unter [SSL-Terminierung mit Key Vault-Zertifikaten](key-vault-certs.md).
- **Azure Kubernetes Service-Eingangscontroller (Vorschau)** : Der Application Gateway v2-Eingangscontroller ermöglicht die Verwendung von Azure Application Gateway als Eingang für einen Azure Kubernetes Service (AKS), der als AKS-Cluster bezeichnet wird. Weitere Informationen finden Sie auf der [Dokumentationsseite](https://azure.github.io/application-gateway-kubernetes-ingress/).
- **Leistungsverbesserungen**: Die v2-SKU bietet eine bis zu fünf Mal bessere SSL-Auslagerungsleistung verglichen mit der Standard/WAF-SKU.
- **Schnellere Bereitstellung und Aktualisierung**: Die v2-SKU ermöglicht verglichen mit der Standard/WAF-SKU eine schnellere Bereitstellung und Aktualisierung. Dies umfasst auch Änderungen an der WAF-Konfiguration.

![](./media/application-gateway-autoscaling-zone-redundant/application-gateway-autoscaling-zone-redundant.png)

## <a name="supported-regions"></a>Unterstützte Regionen

Die Standard_v2- und WAF_v2-SKU ist in den folgenden Regionen verfügbar: USA, Norden-Mitte, USA, Süden-Mitte, USA, Westen, USA, Westen 2, USA, Osten, USA, Osten 2, USA, Mitte, Nordeuropa, Westeuropa, Asien, Südosten, Frankreich, Mitte, Vereinigtes Königreich, Westen, Japan, Osten, Japan, Westen. Weitere Regionen werden im Laufe der Zeit hinzugefügt.

## <a name="pricing"></a>Preise

Bei der v2-SKU basiert das Preismodell auf der Nutzung und ist nicht mehr an die Anzahl oder Größe von Instanzen gebunden. Der Preis der v2-SKU setzt sich aus zwei Komponenten zusammen:

- **Festpreis**: Dies ist der Preis pro Stunde (oder Teil einer Stunde) für die Bereitstellung eines Standard_v2- oder WAF_v2-Gateways.
- **Preis nach Kapazitätseinheit**: Dies sind die nutzungsbasierten Kosten, die zusätzlich zu den festen Kosten berechnet werden. Die Gebühr für Kapazitätseinheiten wird ebenfalls für volle oder teilweise Stunden berechnet. Die Kapazitätseinheit setzt sich aus drei Größen zusammen: Compute-Einheit, permanente Verbindungen und Durchsatz. Die Compute-Einheit ein Maß für die genutzte Prozessorkapazität. Faktoren, die sich auf die Compute-Einheit auswirken, sind TLS-Verbindungen/Sekunde, URL-Rewrite-Berechnungen und Verarbeitung von WAF-Regeln. Permanente Verbindung ist ein Maß für eingerichtete TCP-Verbindungen mit Application Gateway in einem bestimmten Abrechnungsintervall. Der Durchsatz ist die durchschnittliche Anzahl von Megabits pro Sekunde, die vom System in einem bestimmten Abrechnungsintervall verarbeitet werden.

Jede Kapazitätseinheit setzt sich maximal zusammen aus: 1 Compute-Einheit oder 2500 permanente Verbindungen oder 2,22 MBit/s Durchsatz.

Leitfaden für Compute-Einheit:

- **Standard_v2**: Jede Compute-Einheit kann ungefähr 50 Verbindungen pro Sekunde mit TLS-Zertifikat mit RSA-Schlüssel (2048 Bit) unterhalten.
- **WAF_v2**: Jede Compute-Einheit kann etwa 10 gleichzeitige Anforderungen pro Sekunde für eine 70/30%-Kombination des Datenverkehrs mit 70 % Anforderungen, weniger als 2 KB GET/POST und höher unterstützen. Die WAF-Leistung ist derzeit nicht von der Antwortgröße betroffen.

> [!NOTE]
> Jede Instanz kann derzeit etwa 10 Kapazitätseinheiten unterstützen.
> Die Anzahl der Anforderungen, die eine Compute-Einheit verarbeiten kann, hängt von verschiedenen Kriterien wie Größe des TLS-Zertifikatschlüssels, Schlüsselaustauschalgorithmus, erneutes Generieren von Headern und – im Fall von WAF – Größe der eingehenden Anforderung ab. Es wird empfohlen, Anwendungstests auszuführen, um die Anforderungsrate pro Compute-Einheit zu ermitteln. Sowohl Kapazitätseinheit als auch Compute-Einheit werden vor Beginn der Abrechnung als Metrik zur Verfügung gestellt.

**Preise in der Region „USA, Osten“** :

|              SKU-Name                             | Festpreis ($/Std)  | Preis nach Kapazitätseinheit ($/KE-Std)   |
| ------------------------------------------------- | ------------------- | ------------------------------- |
| Standard_v2                                       |    0,20             | 0,0080                          |
| WAF_v2                                            |    0,36             | 0,0144                          |

Weitere Preisinformationen finden Sie in der [Preisübersicht](https://azure.microsoft.com/pricing/details/application-gateway/). Die Abrechnung soll planmäßig am 1. Juli 2019 beginnen.

**Beispiel 1**

Es wird ein Application Gateway Standard_v2 ohne automatische Skalierung im manuellen Skalierungsmodus mit fester Kapazität von fünf Instanzen bereitgestellt.

Festpreis = 744 (Stunden) * 0,20 $ = 148,8 $ <br>
Kapazitätseinheiten = 744 (Stunden) 10 Kapazitätseinheiten pro Instanz * fünf Instanzen * 0,008 $ pro Kapazitätseinheitsstunde = 297,6 $

Gesamtpreis = 148,8 $ + 297,6 $ = 446,4 $

**Beispiel 2**

Es wird ein Application Gateway Standard_v2 für einen Monat bereitgestellt, und es empfängt während dieser Zeit 25 neue SSL-Verbindungen/Sekunde, Datenübertragung von durchschnittlich 8,88 MBit/s. Bei kurzlebigen Verbindungen ist der Preis wie folgt:

Festpreis = 744 (Stunden) * 0,20 $ = 148,8 $

Preis nach Kapazitätseinheit = 744 (Stunden) * Max. (25/50 Compute-Einheit für Verbindungen/Sekunde, 8,88/2,22 Kapazitätseinheit für Durchsatz) * 0,008 $ = 744 * 4 * 0,008 = 23,81 $

Gesamtpreis = 148,8 $ + 23,81 $ = 172,61 $

**Beispiel 3**

Es wird ein Application Gateway WAF_v2 für einen Monat bereitgestellt. Während dieses Zeitraums empfängt es 25 neue SSL-Verbindungen/Sekunde, Datenübertragung von durchschnittlich 8,88 MBit/s, und stellt 80 Anforderungen pro Sekunde. Bei kurzlebigen Verbindungen und unter der Voraussetzung, dass die Berechnung der Compute-Einheiten für die Anwendung 10 RPS pro Compute-Einheit unterstützt, ist der Preis wie folgt:

Festpreis = 744 (Stunden) * 0,36 $ = 267,84 $

Preis nach Kapazitätseinheit = 744 (Stunden) * Max. (Compute-Einheit Max. (25/50 für Verbindungen/Sekunde, 80/10 WAF-RPS), 8,88/2,22 Kapazitätseinheit für Durchsatz) * 0,0144 $ = 744 * 8 * 0,0144 = 85,71 $

Gesamtpreis = 267,84 $ + 85,71 $ = 353,55 $

## <a name="scaling-application-gateway-and-waf-v2"></a>Skalierung von Application Gateway und WAF v2

Application Gateway und WAF können für die Skalierung in zwei Modi konfiguriert werden:

- **Automatische Skalierung**: Bei aktivierter automatischer Skalierung können Application Gateway und WAF v2-SKUs basierend auf den Datenverkehrsanforderungen der Anwendung zentral hoch- oder herunterskalieren. Dieser Modus bietet bessere Elastizität für Ihre Anwendung und Größe oder Anzahl der Instanzen von Application Gateway müssen nicht geschätzt werden. Außerdem ermöglicht dieser Modus Kosteneinsparungen, da es nicht erforderlich ist, Gateways mit bereitgestellter Spitzenkapazität für die erwartete maximale Auslastung auszuführen. Kunden müssen eine minimale und optional eine maximale Anzahl von Instanzen angeben. Durch eine Mindestkapazität wird sichergestellt, dass Application Gateway und WAF v2 nicht unter die angegebene Mindestanzahl von Instanzen abfallen, selbst wenn kein Datenverkehr vorhanden ist. Diese Mindestkapazität wird Ihnen auch bei nicht vorhandenem Datenverkehr in Rechnung gestellt. Sie können optional auch eine maximale Anzahl von Instanzen angeben, durch die sichergestellt wird, dass Application Gateway nicht über die angegebene Anzahl von Instanzen hinaus skaliert. Ihnen wird weiterhin die vom Gateway bereitgestellte Menge an Datenverkehr in Rechnung gestellt. Die Anzahl der Instanzen kann zwischen 0 und 125 liegen. Wenn kein Wert angegeben ist, liegt der Standardwert für die maximale Anzahl von Instanzen bei 20.
- **Manuell**: Sie können alternativ auch den manuellen Modus auswählen, in dem das Gateway nicht automatisch skaliert wird. Wenn in diesem Modus mehr Datenverkehr vorliegt, als von Application Gateway oder WAF verarbeitet werden kann, kann dies zu einem Datenverlust führen. Beim manuellen Modus ist die Angabe der Anzahl von Instanzen obligatorisch. Die Anzahl der Instanzen kann zwischen 1 und 125 variieren.

## <a name="feature-comparison-between-v1-sku-and-v2-sku"></a>Funktionsvergleich zwischen v1 SKU und v2 SKU

In der folgenden Tabelle werden die Features der einzelnen SKUs gegenübergestellt.

|                                                   | v1 SKU   | v2 SKU   |
| ------------------------------------------------- | -------- | -------- |
| Automatische Skalierung                                       |          | &#x2713; |
| Zonenredundanz                                   |          | &#x2713; |
| Statische VIP                                        |          | &#x2713; |
| AKS-Eingangscontroller (Azure Kubernetes Service) |          | &#x2713; |
| Azure-Schlüsseltresor-Integration                       |          | &#x2713; |
| Erneutes Generieren von HTTP(S)-Headern                           |          | &#x2713; |
| URL-basiertes Routing                                 | &#x2713; | &#x2713; |
| Hosten mehrerer Websites                             | &#x2713; | &#x2713; |
| Umleitung des Datenverkehrs                               | &#x2713; | &#x2713; |
| Web Application Firewall (WAF)                    | &#x2713; | &#x2713; |
| SSL-Beendigung (Secure Sockets Layer)            | &#x2713; | &#x2713; |
| End-to-End-SSL-Verschlüsselung                         | &#x2713; | &#x2713; |
| Sitzungsaffinität                                  | &#x2713; | &#x2713; |
| Benutzerdefinierte Fehlerseiten                                | &#x2713; | &#x2713; |
| WebSocket-Unterstützung                                 | &#x2713; | &#x2713; |
| HTTP/2-Unterstützung                                    | &#x2713; | &#x2713; |
| Verbindungsausgleich                               | &#x2713; | &#x2713; |

> [!NOTE]
> Die v2-SKU mit automatischer Skalierung unterstützt nun [Standardintegritätstests](application-gateway-probe-overview.md#default-health-probe), um die Integrität aller Ressourcen im Back-End-Pool automatisch zu überwachen und solche Back-End-Elemente zu markieren, die als fehlerhaft gelten. Der Standardintegritätstest wird automatisch für Back-Ends konfiguriert, die keine benutzerdefinierte Testkonfiguration aufweisen. Weitere Informationen finden Sie unter [Systemüberwachung des Application Gateways](application-gateway-probe-overview.md).

## <a name="differences-with-v1-sku"></a>Unterschiede im Vergleich zur v1-SKU

|Unterschied|Details|
|--|--|
|Authentifizierungszertifikat|Nicht unterstützt.<br>Weitere Informationen finden Sie unter [Übersicht über End-to-End-SSL mit Application Gateway](ssl-overview.md#end-to-end-ssl-with-the-v2-sku).|
|Mischen von Standard_v2 und Standardanwendungsgateway in demselben Subnetz|Nicht unterstützt|
|Benutzerdefinierte Route im Application Gateway-Subnetz|Nicht unterstützt|
|NSG für Eingangsportbereich| - 65200 bis 65535 für Standard_v2-SKU<br>- 65503 bis 65534 für Standard-SKU.<br>Weitere Informationen finden Sie in den [häufig gestellten Fragen](application-gateway-faq.md#are-network-security-groups-supported-on-the-application-gateway-subnet).|
|Leistungsprotokolle in Azure-Diagnose|Nicht unterstützt.<br>Es sollten Azure-Metriken verwendet werden.|
|Abrechnung|Die Abrechnung soll planmäßig am 1. Juli 2019 beginnen.|
|FIPS-Modus|Diese werden derzeit nicht unterstützt.|
|Reiner ILB-Modus|Dies wird derzeit nicht unterstützt. Öffentlicher und ILB-Modus kombiniert werden unterstützt.|
|NetWatcher-Integration|Nicht unterstützt.|
|Azure Support Center-Integration|Noch nicht verfügbar.

## <a name="next-steps"></a>Nächste Schritte

- [Schnellstart: Weiterleiten von Webdatenverkehr per Azure Application Gateway – Azure-Portal](quick-create-portal.md)
- [Erstellen Sie mit Azure PowerShell ein zonenredundantes Application Gateway mit automatischer Skalierung und einer reservierten virtuellen IP-Adresse.](tutorial-autoscale-ps.md)
- Erfahren Sie mehr über [Application Gateway](overview.md).
- Erfahren Sie mehr über [Azure Firewall](../firewall/overview.md).