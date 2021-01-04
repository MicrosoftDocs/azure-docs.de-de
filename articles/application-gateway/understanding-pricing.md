---
title: Grundlegendes zu Preisen – Azure Application Gateway
description: In diesem Artikel wird der Abrechnungsprozess für Azure Application Gateway und Web Application Firewall (sowohl v1- als auch v2-SKUs) beschrieben
services: application-gateway
author: azhar2005
ms.service: application-gateway
ms.topic: conceptual
ms.custom: references_regions
ms.date: 09/01/2020
ms.author: azhussai
ms.openlocfilehash: 89ba6b7a69c95951a083628f23be68d811c7768c
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/04/2020
ms.locfileid: "96601610"
---
# <a name="understanding-pricing-for-azure-application-gateway-and-web-application-firewall"></a>Grundlegendes zu Preisen für Azure Application Gateway und Web Application Firewall

>[!NOTE]
>Bei den in diesem Artikel gezeigten Preisen handelt es sich um Beispiele, die ausschließlich zur Veranschaulichung dienen. Aktuelle Preisinformationen für Ihre Region finden Sie auf der [Seite mit der Preisübersicht](https://azure.microsoft.com/pricing/details/application-gateway/).

Azure Application Gateway ist eine Layer-7-Lastenausgleichslösung, die eine skalierbare, hoch verfügbare und sichere Bereitstellung von Webanwendungen in Azure ermöglicht.

Bei Application Gateway fallen keine Vorab- oder Kündigungskosten an.
Ihnen werden ausschließlich die vorab bereitgestellten und genutzten Ressourcen in Rechnung gestellt. Grundlage für die Abrechnung ist die tatsächliche Nutzung pro Stunde. Die Kosten für Application Gateway lassen sich in zwei Kategorien untergliedern: Fixkosten und variable Kosten. Die tatsächlichen Kosten der jeweiligen Kategorie variieren abhängig von der verwendeten SKU.

In diesem Artikel werden die mit jeder SKU verknüpften Kosten beschreiben. Benutzer sollten dieses Dokument für die Planung und Verwaltung der Kosten im Zusammenhang mit Azure Application Gateway verwenden.

## <a name="v1-skus"></a>V1-SKUs

Application Gateway Standard- und WAF-V1-SKUs werden als Kombination der folgenden Kategorien abgerechnet:

* Feste Kosten

    Diese Kosten basieren auf der Zeitdauer, für die ein bestimmter Application Gateway-/WAF-Typ bereitgestellt und zur Verarbeitung von Anforderungen ausgeführt wird.
    Zur Berechnung der Fixkosten werden die folgenden Faktoren berücksichtigt:
    * Ebene: Application Gateway Standard oder WAF
    * Größe: klein, mittel und groß
    * Anzahl von Instanzen: Anzahl der bereitzustellenden Instanzen

    Für n Instanzen werden die Kosten wie folgt berechnet: n x die Kosten der Instanz einer bestimmten Ebene (Standard und WAF) plus Größe (klein, mittel und groß).

* Variable Kosten

    Diese Kosten basieren auf der von Application Gateway/WAF verarbeiteten Datenmenge. Für die Abrechnung werden sowohl die für Anforderungen als auch die für Antworten von Application Gateway verarbeiteten Bytes berücksichtigt.

Gesamtkosten = Fixkosten + Variable Kosten

### <a name="standard-application-gateway"></a>Application Gateway Standard

Fixkosten und variable Kosten werden gemäß dem Application Gateway-Typ berechnet.
Die in der folgenden Tabelle gezeigten Beispielpreise basieren auf möglicherweise nicht mehr aktuellen Preisen der Region „USA, Osten“ und dienen lediglich zur Veranschaulichung.

#### <a name="fixed-cost-east-us-region-pricing"></a>Fixkosten (Preise der Region „USA, Osten“)

|              Application Gateway-Typ             |  Kosten (USD/Std.)  |
| ------------------------------------------------- | ---------------|
|                     Klein                         |    0,025 USD      |
|                     Medium                        |    0,07 USD       |
|                     Groß                         |    0,32 USD       |

Die geschätzten monatlichen Preise basieren auf einer Nutzungsdauer von 730 Stunden pro Monat.

#### <a name="variable-cost-east-us-region-pricing"></a>Variable Kosten (Preise der Region „USA, Osten“)

|              Verarbeitete Daten             |  Klein (USD/GB)  |  Mittel (USD/GB) |  Groß (USD/GB) |
| --------------------------------------- | -------------- | -------------- | ------------- |
| Erste 10 TB/Monat                       |     0,008 US-Dollar     |      Kostenlos      |     Kostenlos      |
| Nächste 30 TB (10-40 TB)/Monat             |     0,008 US-Dollar     |     0,007 USD     |     Kostenlos      |
| Über 40 TB/Monat                        |     0,008 US-Dollar     |     0,007 USD     |     0,0035 USD   |

Weitere Preisinformationen für Ihre Region finden Sie auf der [Seite mit der Preisübersicht](https://azure.microsoft.com/pricing/details/application-gateway/).

### <a name="waf-v1"></a>WAF V1

Fixkosten und variable Kosten werden basierend auf dem bereitgestellten Application Gateway-Typ berechnet.

Die in der folgenden Tabelle gezeigten Beispielpreise basieren auf möglicherweise nicht mehr aktuellen Preisen der Region „USA, Osten“ und dienen lediglich zur Veranschaulichung.

#### <a name="fixed-cost-east-us-region-pricing"></a>Fixkosten (Preise der Region „USA, Osten“)

|              Application Gateway-Typ             |  Kosten (USD/Std.)  |
| ------------------------------------------------- | ---------------|
|                     Klein                         |       Nicht verfügbar       |
|                     Medium                        |     0,126 USD     |
|                     Groß                         |     0,448 USD     |

Die geschätzten monatlichen Preise basieren auf einer Nutzungsdauer von 730 Stunden pro Monat.

#### <a name="variable-cost-east-us-region-pricing"></a>Variable Kosten (Preise der Region „USA, Osten“)

|              Verarbeitete Daten             |  Klein (USD/GB)  |  Mittel (USD/GB) |  Groß (USD/GB) |
| --------------------------------------- | -------------- | -------------- | ------------- |
| Erste 10 TB/Monat                       |     0,008 US-Dollar     |      Kostenlos      |     Kostenlos      |
| Nächste 30 TB (10-40 TB)/Monat             |     0,008 US-Dollar     |     0,007 USD     |     Kostenlos      |
| Über 40 TB/Monat                        |     0,008 US-Dollar     |     0,007 USD     |     0,0035 USD   |

Weitere Preisinformationen für Ihre Region finden Sie auf der [Seite mit der Preisübersicht](https://azure.microsoft.com/pricing/details/application-gateway/).

> [!NOTE]
> Ausgehende Datenübertragungen: Ausgehende Daten, die über Anwendungsgateways aus Azure-Rechenzentren übertragen werden, werden basierend auf den standardmäßigen [Datenübertragungsraten](https://azure.microsoft.com/pricing/details/bandwidth/) in Rechnung gestellt.

### <a name="example-1-a--standard-application-gateway-with-1-instance-count"></a>Beispiel 1 (a) – Application Gateway Standard mit einer Instanz

Angenommen, Sie haben eine Standard-SKU von Application Gateway vom Typ „Mittel“ mit 1 Instanz bereitgestellt, die 500 GB pro Monat verarbeitet. Basierend auf den oben stehenden Preisinformationen würden Ihre Application Gateway-Kosten wie folgt berechnet:

Fixkosten = 0,07 USD x 730 (Stunden) = 51,1 USD. Die geschätzten monatlichen Kosten basieren auf einer Nutzungsdauer von 730 Stunden pro Monat.

Variable Kosten = keine (bei der Ebene „Mittel“ fallen für die ersten 10 TB, die pro Monat verarbeitet werden, keine Kosten an) Gesamtkosten = 51,1 USD + 0 = 51,1 USD 

> [!NOTE]
> Zur Unterstützung von Hochverfügbarkeitsszenarien müssen bei V1-SKUs mindestens zwei Instanzen eingerichtet werden. Siehe [SLA für Application Gateway](https://azure.microsoft.com/support/legal/sla/application-gateway/v1_2/).

### <a name="example-1-b--standard-application-gateway-with--1-instance-count"></a>Beispiel 1 (b) – Application Gateway Standard mit mehreren Instanzen

Angenommen, Sie haben eine Standard-SKU von Application Gateway vom Typ „Mittel“ mit fünf Instanzen bereitgestellt, die 500 GB pro Monat verarbeitet. Basierend auf den oben stehenden Preisinformationen würden Ihre Application Gateway-Kosten wie folgt berechnet:

Fixkosten = 5 (Anzahl von Instanzen) x 0,07 USD x 730 (Stunden) = 255,5 USD. Die geschätzten monatlichen Kosten basieren auf einer Nutzungsdauer von 730 Stunden pro Monat.

Variable Kosten = keine (bei der Ebene „Mittel“ fallen für die ersten 10 TB, die pro Monat verarbeitet werden, keine Kosten an) Gesamtkosten = 255,5 USD + 0 = 255,5 USD 

### <a name="example-2--waf-application-gateway"></a>Beispiel 2 – Application Gateway WAF

Angenommen, Sie haben für die ersten 15 Tage des Monats eine Standard-SKU von Application Gateway vom Typ „Klein“ und eine Application Gateway WAF-SKU vom Typ „Groß“ bereitgestellt. Innerhalb des aktiven Zeitfensters verarbeitet das kleine Anwendungsgateway 15 TB, das große WAF-Anwendungsgateway 100 TB. Basierend auf den oben stehenden Preisinformationen würden Ihre Application Gateway-Kosten wie folgt berechnet: 

###### <a name="small-instance-standard-application-gateway"></a>Kleine Standard-Instanz von Application Gateway

24 Stunden x 15 Tage = 360 Stunden

Fixkosten = 0,025 USD x 360 (Stunden) = 9 USD

Variable Kosten = 10 x 1.000 x 0,008 USD/GB + 5 x 1.000 x 0,008 USD/GB = 120 USD

Gesamtkosten = 9 USD + 120 USD = 129 USD

###### <a name="large-instance-waf-application-gateway"></a>Große WAF-Instanz von Application Gateway
24 Stunden x 15 Tage = 360 Stunden

Fixkosten = 0,448 USD x 360 (Stunden) = 161,28 USD

Variable Kosten = 60 x 1.000 x 0,0035 USD/GB  = 210 USD (bei der Ebene „Groß“ fallen für die ersten 40 TB, die pro Monat verarbeitet werden, keine Kosten an)

Gesamtkosten = 161,28 USD + 210 USD = 371,28 USD

## <a name="v2-skus"></a>V2-SKUs  

Application Gateway V2- und WAF V2-SKUs unterstützen die automatische Skalierung und garantieren standardmäßig Hochverfügbarkeit.

### <a name="key-terms"></a>Schlüsselbegriffe

##### <a name="capacity-unit"></a>Kapazitätseinheit 
Die Kapazitätseinheit ist die Messgröße der Kapazitätsauslastung eines Application Gateway, für die mehrere Parameter berücksichtigt werden.

Eine einzelne Kapazitätseinheit umfasst die folgenden Parameter:
* 2\.500 permanente Verbindungen
* 2,22 MBit/s Durchsatz
* 1 Compute-Einheit

Wenn einer dieser Parameter überschritten wird, werden n weitere Kapazitätseinheiten benötigt. Dies gilt auch dann, wenn die Grenzwerte der beiden anderen Parameter dieser Kapazitätseinheit nicht überschritten werden.
Der Parameter mit der höchsten Auslastung wird intern zur Berechnung der Kapazitätseinheiten verwendet, die wiederum in Rechnung gestellt werden.

##### <a name="compute-unit"></a>Compute-Einheit
Die Compute-Einheit ist die Messgröße der beanspruchten Computekapazität. Faktoren, die sich auf die Nutzung von Compute-Einheiten auswirken, sind TLS-Verbindungen/Sekunde, URL-Rewrite-Berechnungen und die Verarbeitung von WAF-Regeln. Die Anzahl von Anforderungen, die eine Compute-Einheit verarbeiten kann, hängt von verschiedenen Kriterien ab. Dazu zählen u. a. die Größe des TLS-Zertifikatschlüssels, der Schlüsselaustauschalgorithmus, das erneute Generieren von Headern und – im Fall von WAF – die Größe der eingehenden Anforderung.

Leitfaden für Compute-Einheit:
* Standard_v2: Jede Compute-Einheit kann ungefähr 50 Verbindungen pro Sekunde mit TLS-Zertifikat mit RSA-Schlüssel (2048 Bit) unterhalten.

* WAF_v2: Jede Compute-Einheit kann etwa 10 gleichzeitige Anforderungen pro Sekunde für eine 70/30%-Kombination des Datenverkehrs mit 70 % Anforderungen, weniger als 2 KB GET/POST und höher unterstützen. Die WAF-Leistung ist derzeit nicht von der Antwortgröße betroffen.

##### <a name="instance-count"></a>Anzahl der Instanzen 
Die Vorabbereitstellung von Ressourcen für Application Gateway-V2-SKUs basiert auf der Anzahl von Instanzen. Bezüglich der Verarbeitungskapazität werden mit jeder Instanz mindestens 10 Kapazitätseinheiten garantiert. Eine einzelne Instanz könnte potenziell mehr als 10 Kapazitätseinheiten für unterschiedliche Datenverkehrsmuster unterstützen (abhängig von den Parametern der Kapazitätseinheit).

Manuell definierte Skalierungs- und Grenzwerte für die automatische Skalierung (Mindest- oder Höchstwerte) werden für die Anzahl von Instanzen festgelegt. Bei manuell festgelegten Skalierungswerten für die Anzahl von Instanzen und bei der Mindestanzahl von Instanzen zur Konfiguration der Autoskalierung werden 10 Kapazitätseinheiten/Instanz reserviert. Diese reservierten Kapazitätseinheiten werden für die Zeitdauer in Rechnung gestellt, in der Application Gateway aktiv ist (unabhängig vom tatsächlichen Ressourcenverbrauch). Wenn der tatsächliche Ressourcenverbrauch den Schwellenwert von 10 Kapazitätseinheiten/Instanz überschreitet, werden bei den variablen Kosten weitere Kapazitätseinheiten in Rechnung gestellt.

V2-SKUs werden basierend auf der Nutzung abgerechnet. Die Abrechnung umfasst zwei Kategorien:

* Fixkosten

    Die Kosten, die auf der Zeitdauer basieren, in der Application Gateway V2/WAF V2 bereitgestellt und für die Verarbeitung von Anforderungen verfügbar ist. Durch diese Bereitstellung wird Hochverfügbarkeit sichergestellt. Dies gilt selbst dann, wenn keine Instanzen reserviert sind, indem für die Mindestinstanzanzahl bei der automatischen Skalierung der Wert 0 angegeben wird. 
    
    Die Fixkosten umfassen zudem die Kosten für die öffentliche IP-Adresse von Application Gateway.

    Die Anzahl von Instanzen, die zu einem beliebigen Zeitpunkt ausgeführt werden, werden bei V2-SKUs nicht als Faktor für die Fixkosten berücksichtigt. Die Fixkosten pro Stunde für die Ausführung einer Standard_V2-Instanz (oder WAF_V2-Instanz) bleiben unabhängig von der Anzahl von Instanzen gleich, die innerhalb derselben Azure-Region ausgeführt werden.

* Kosten für Kapazitätseinheiten 

    Die Kosten, die auf der Anzahl von Kapazitätseinheiten basieren, die entweder reserviert oder zusätzlich genutzt werden, um die eingehenden Anforderungen zu verarbeiten.  Verbrauchsbasierte Kosten werden pro Stunde berechnet.

Gesamtkosten = Fixkosten + Kosten für Kapazitätseinheiten

> [!NOTE]
> Eine angefangene Stunde wird als volle Stunde abgerechnet.

Die in der folgenden Tabelle gezeigten Beispielpreise basieren auf möglicherweise nicht mehr aktuellen Preisen der Region „USA, Osten“ und dienen lediglich zur Veranschaulichung.

#### <a name="fixed-costs-east-us-region-pricing"></a>Fixkosten (Preise der Region „USA, Osten“)

|              V2-SKU             |  Kosten (USD/Std.)  |
| ------------------------------- | ---------------|
|            Standard_V2          |     0,246 USD     |
|              WAF_V2             |     0,443 USD     |

Die geschätzten monatlichen Preise basieren auf einer Nutzungsdauer von 730 Stunden pro Monat.

#### <a name="variable-costs-east-us-region-pricing"></a>Variable Kosten (Preise der Region „USA, Osten“)

|        Kapazitätseinheit         |  Standard_V2 (USD/Std.)  |  WAF_V2 (USD/Std.) |
| ---------------------------- | -------------------- | -------------- |
|             1 Kapazitätseinheit             |        0,008 US-Dollar        |     0,0144 USD    |

Weitere Preisinformationen für Ihre Region finden Sie auf der [Seite mit der Preisübersicht](https://azure.microsoft.com/pricing/details/application-gateway/).

> [!NOTE]
> Ausgehende Datenübertragungen: Ausgehende Daten, die über Anwendungsgateways aus Azure-Rechenzentren übertragen werden, werden basierend auf den standardmäßigen [Datenübertragungsraten](https://azure.microsoft.com/pricing/details/bandwidth/) in Rechnung gestellt.

### <a name="example-1-a--manual-scaling"></a>Beispiel 1 (a) – Manuelle Skalierung 
Angenommen, Sie haben eine Standard_V2-Instanz von Application Gateway bereitgestellt und für die manuelle Skalierung 8 Instanzen für den gesamten Monat festgelegt. Während dieser Zeit werden durchschnittlich 88,8 MBit/s eingehende Daten übertragen.

Basierend auf den oben stehenden Preisinformationen würden Ihre Application Gateway-Kosten wie folgt berechnet:

Eine Kapazitätseinheit kann einen Durchsatz von 2,22 MBit/s bewältigen.

Benötigte Kapazitätseinheiten für 88,8 MBit/s = 88,8 / 2,22 = 40 Kapazitätseinheiten 

Vorab bereitgestellte Kapazitätseinheiten = 8 (Anzahl von Instanzen) x 10 = 80 

Da 80 (reservierte Kapazität) > 40 (erforderliche Kapazität), sind keine zusätzlichen Kapazitätseinheiten erforderlich. 

Fixkosten = 0,246 USD x 730 (Stunden) = 179,58 USD

Variable Kosten = 0,008 USD x 8 (Anzahl von Instanzen) x 10 (Kapazitätseinheiten) x 730 (Stunden) = 467,2 USD

Gesamtkosten = 179,58 USD + 467,2 USD = 646,78 USD

![Abbildung zur manuellen Skalierung 1.](./media/pricing/manual-scale-1.png)

### <a name="example-1-b--manual-scaling-with-traffic-going-beyond-provisioned-capacity"></a>Beispiel 1 (b) – Manuelle Skalierung mit Datenverkehr, der die bereitgestellte Kapazität überschreitet

Angenommen, Sie haben eine Standard_V2-Instanz von Application Gateway bereitgestellt und für die manuelle Skalierung 3 Instanzen für den gesamten Monat festgelegt. Während dieser Zeit werden durchschnittlich 88,8 MBit/s eingehende Daten übertragen.

Basierend auf den oben stehenden Preisinformationen würden Ihre Application Gateway-Kosten wie folgt berechnet:

Eine Kapazitätseinheit kann einen Durchsatz von 2,22 MBit/s bewältigen.

Benötigte Kapazitätseinheiten für 88,8 MBit/s = 88,8 / 2,22 = 40 

Vorab bereitgestellte Kapazitätseinheiten = 3 (Anzahl von Instanzen) x 10 = 30 

Da 40 (erforderliche Kapazität) > 30 (reservierte Kapazität), sind zusätzliche Kapazitätseinheiten erforderlich.
Die Anzahl der zusätzlich genutzten Kapazitätseinheiten hängt davon ab, wie viel freie Kapazität für die einzelnen Instanzen verfügbar ist.

Wenn Verarbeitungskapazität, die 10 zusätzlichen Kapazitätseinheiten entspricht, zur Verwendung innerhalb der 3 reservierten Instanzen verfügbar war.

Fixkosten = 0,246 USD x 730 (Stunden) = 179,58 USD

Variable Kosten = 0,008 USD x (3 (Instanzeinheiten) x 10 (Kapazitätseinheiten) + 10 (zusätzliche Kapazitätseinheiten)) x 730 (Stunden) = 233,6 USD

Gesamtkosten = 179,58 USD + 233,6 USD = 413,18 USD

Jedoch wenn Verarbeitungskapazität, die nur 7 zusätzlichen Kapazitätseinheiten entspricht, innerhalb der 3 reservierten Instanzen zur Verwendung verfügbar war.
In diesem Szenario wird die Application Gateway-Ressource unterskaliert, und dies kann möglicherweise zu einer Erhöhung der Latenz oder zum Löschen von Anforderungen führen.

Fixkosten = 0,246 USD x 730 (Stunden) = 179,58 USD

Variable Kosten = 0,008 USD x (3 (Instanzeinheiten) x 10 (Kapazitätseinheiten) + 7 (zusätzliche Kapazitätseinheiten)) x 730 (Stunden) = 216,08 USD

Gesamtkosten = 179,58 USD + 216,08 USD = 395,66 USD


![Abbildung zur manuellen Skalierung 2.](./media/pricing/manual-scale-2.png)

> [!NOTE]
> Bei der manuellen Skalierung können sich zusätzliche Anforderungen, die die maximale Verarbeitungskapazität der reservierten Instanzen überschreiten, nachteilig auf die Verfügbarkeit Ihrer Anwendung auswirken. Bei hoher Auslastung können reservierte Instanzen gegebenenfalls eine Verarbeitungskapazität von mehr als 10 Kapazitätseinheiten bereitstellen (abhängig von der Konfiguration und vom Typ eingehender Anforderungen). Es wird jedoch empfohlen, die Anzahl der Instanzen bereitzustellen, die die Anforderungen Ihres Datenverkehrs erfüllt.

### <a name="example-2--waf_v2-instance-with-autoscaling"></a>Beispiel 2 – WAF_V2-Instanz mit automatischer Skalierung

Angenommen, Sie haben eine WAF_V2-Instanz mit aktivierter automatischer Skalierung bereitgestellt und die Mindestinstanzanzahl auf 6 für den gesamten Monat festgelegt. Aufgrund der Anforderungslast wurde die WAF-Instanz aufskaliert, und es werden 65 Kapazitätseinheiten für den gesamten Monat verwendet (Aufskalierung von 5 Kapazitätseinheiten, wobei 60 Einheiten reserviert wurden).
Basierend auf den oben stehenden Preisinformationen würden Ihre Application Gateway-Kosten wie folgt berechnet:

Die geschätzten monatlichen Preise basieren auf einer Nutzungsdauer von 730 Stunden pro Monat.

Fixkosten = 0,443 USD x 730 (Stunden) = 323,39 USD

Variable Kosten = 0,0144 USD x 65 (Kapazitätseinheiten) x 730 (Stunden) = 683,28 USD

Gesamtkosten = 323,39 USD + 683,28 USD = 1.006,67 USD

![Abbildung der automatischen Skalierung 2.](./media/pricing/auto-scale-1.png)

> [!NOTE]
> Für den tatsächlichen Datenverkehr, der für Ihre Application Gateway-Instanz beobachtet wird, wäre das Datenverkehrsmuster wahrscheinlich nicht so konstant, und die beobachtete Last Ihrer Application Gateway-Instanz würde abhängig vom tatsächlichen Verbrauch variieren.

### <a name="example-3-a--waf_v2-instance-with-autoscaling-and-0-min-scale-config"></a>Beispiel 3 (a) – WAF_V2-Instanz mit automatischer Skalierung und dem Wert 0 für die Mindestanzahl von Skalierungseinheiten

Angenommen, Sie haben eine WAF_V2-Instanz mit aktivierter automatischer Skalierung bereitgestellt und die Mindestinstanzanzahl auf 0 für den gesamten Monat festgelegt. Die Anforderungslast der WAF-Instanz ist minimal, jedoch in jeder Stunde innerhalb des gesamten Monats vorhanden. Sie unterschreitet die Kapazität einer einzelnen Kapazitätseinheit.
Basierend auf den oben stehenden Preisinformationen würden Ihre Application Gateway-Kosten wie folgt berechnet:

Die geschätzten monatlichen Preise basieren auf einer Nutzungsdauer von 730 Stunden pro Monat.

Fixkosten = 0,443 USD x 730 (Stunden) = 323,39 USD

Variable Kosten = 0,0144 USD x 1 (Kapazitätseinheiten) x 730 (Stunden) = 10,512 USD

Gesamtkosten = 323,39 USD + 10,512 USD = 333,902 USD

### <a name="example-3-b--waf_v2-instance-with-autoscaling-with-0-min-instance-count"></a>Beispiel 3 (b) – WAF_V2-Instanz mit automatischer Skalierung und dem Wert 0 für die Mindestinstanzanzahl

Angenommen, Sie haben eine WAF_V2-Instanz mit aktivierter automatischer Skalierung bereitgestellt und die Mindestinstanzanzahl auf 0 für den gesamten Monat festgelegt. Innerhalb des gesamten Monats wird jedoch keinerlei Datenverkehr an die WAF-Instanz gesendet.
Basierend auf den oben stehenden Preisinformationen würden Ihre Application Gateway-Kosten wie folgt berechnet:

Fixkosten = 0,443 USD x 730 (Stunden) = 323,39 USD

Variable Kosten = 0,0144 USD x 0 (Kapazitätseinheiten) x 730 (Stunden) = 0 USD

Gesamtkosten = 323,39 USD + 0 USD = 323,39 USD

### <a name="example-3-c--waf_v2-instance-with-manual-scaling-set-to-1-instance"></a>Beispiel 3 (C) – WAF_V2-Instanz mit manueller Skalierung und Festlegung auf 1 Instanz

Angenommen, Sie haben eine WAF_V2-Instanz bereitgestellt und für diese manuelle Skalierung festgelegt, wobei Sie den akzeptablen Mindestwert auf 1 Instanz für den gesamten Monat festgelegt haben. Während des gesamten Monats wird jedoch keinerlei Datenverkehr an WAF gesendet.
Basierend auf den oben stehenden Preisinformationen würden Ihre Application Gateway-Kosten wie folgt berechnet:

Die geschätzten monatlichen Preise basieren auf einer Nutzungsdauer von 730 Stunden pro Monat.

Fixkosten = 0,443 USD x 730 (Stunden) = 323,39 USD

Variable Kosten = 0,0144 USD * 1 (Instanzanzahl) * 10 (Kapazitätseinheiten) * 730 (Stunden) = 105,12 USD

Gesamtkosten = 323,39 USD + 105,12 USD = 428,51 USD

### <a name="example-4--waf_v2-with-autoscaling-capacity-unit-calculations"></a>Beispiel 4 – WAF_V2 mit automatischer Skalierung, Berechnung der Kapazitätseinheiten

Angenommen, Sie haben eine WAF_V2-Instanz mit aktivierter automatischer Skalierung bereitgestellt und die Mindestinstanzanzahl auf 0 für den gesamten Monat festgelegt. Während dieses Zeitraums empfängt die Instanz 25 neue TLS-Verbindungen/Sekunde mit einer Datenübertragung von durchschnittlich 8,88 MBit/s.
Basierend auf den oben stehenden Preisinformationen würden Ihre Application Gateway-Kosten wie folgt berechnet:

Die geschätzten monatlichen Preise basieren auf einer Nutzungsdauer von 730 Stunden pro Monat.

Fixkosten = 0,443 USD x 730 (Stunden) = 323,39 USD

Variable Kosten = 0,0144 USD x 730 (Stunden) x {max. (25/50, 8,88/2,22)} = 23,36 USD (4 Kapazitätseinheiten für 8,88 MBit/s erforderlich)

Gesamtkosten = 323,39 USD + 23,36 USD = 346,75 USD

### <a name="example-5-a--standard_v2-with-autoscaling-time-based-calculations"></a>Beispiel 5 (a) – Standard_V2-Instanz mit automatischer Skalierung, zeitbasierte Berechnungen

Angenommen, Sie haben eine Standard_V2-Instanz mit aktivierter automatischer Skalierung bereitgestellt und die Mindestinstanzanzahl auf 0 festgelegt, und dieses Anwendungsgateway ist 2 Stunden lang aktiv.
Während der ersten Stunde wird Datenverkehr empfangen, der von 10 Kapazitätseinheiten verarbeitet werden kann. Für den Datenverkehr innerhalb der zweiten Stunde werden 20 Kapazitätseinheiten benötigt.
Basierend auf den oben stehenden Preisinformationen würden Ihre Application Gateway-Kosten wie folgt berechnet:

Fixkosten = 0,246 USD x 2 (Stunden) = 0,492 USD

Variable Kosten = 0,008 USD x 10 (Kapazitätseinheiten) x 1 (Stunden) + 0,008 USD x 20 (Kapazitätseinheiten) x 1 (Stunden) = 0,24 USD

Gesamtkosten = 0,492 USD + 0,24 USD = 0,732 USD


## <a name="monitoring-billed-usage"></a>Überwachen der abgerechneten Nutzung

Sie können die Nutzung für verschiedene Parameter (Compute-Einheit, Durchsatz und permanente Verbindungen) sowie die Kapazitätseinheiten, die als Teil der Application Gateway-Metriken genutzt werden, im Abschnitt **Überwachung** anzeigen.

![Abbildung des Abschnitts mit den Metriken.](./media/pricing/metrics-1.png)

### <a name="useful-metrics-for-cost-estimation"></a>Nützliche Metriken für die Kostenvorkalkulation

* Aktuelle Kapazitätseinheiten

    Anzahl von Kapazitätseinheiten, die zum Lastenausgleich von Datenverkehr für die drei Parameter (aktuelle Verbindungen, Durchsatz und Compute-Einheiten) genutzt werden

* Feste abrechenbare Kapazitätseinheiten

    Die Mindestanzahl von Kapazitätseinheiten, die gemäß der Einstellung für die Mindestinstanzanzahl (eine Instanz entspricht hierbei 10 Kapazitätseinheiten) in der Application Gateway-Konfiguration bereitgestellt bleiben.

* Geschätzte abgerechnete Kapazitätseinheiten

    Geschätzte abgerechnete Kapazitätseinheiten geben die Anzahl von Kapazitätseinheiten an, für die die Abrechnung geschätzt wird. Hierzu wird der größere Wert zwischen Aktuelle Kapazitätseinheiten (Kapazitätseinheiten, die für den Lastenausgleich des Datenverkehrs erforderlich sind) und Feste abrechenbare Kapazitätseinheiten (Mindestanzahl von Kapazitätseinheiten, die bereitgestellt bleiben) berechnet.

Darüber hinaus sind weitere Metriken (z. B. Durchsatz, aktuelle Verbindungen und Compute-Einheiten) verfügbar, um Engpässe zu erkennen und die erforderliche Anzahl von Kapazitätseinheiten zu bestimmen. Ausführliche Informationen finden Sie unter [Application Gateway-Metriken](application-gateway-metrics.md)

#### <a name="example---estimating-capacity-units-being-utilized"></a>Beispiel – Schätzen der genutzten Kapazitätseinheiten

**Beobachtete Metriken**:

* Compute-Einheiten = 17,38
* Durchsatz = 1,37 MB/s – 10,96 MBit/s
* Aktuelle Verbindungen = 123,08 K
* Berechnung der Kapazitätseinheiten = max. (17,38, 10,96/2,22; 123,08 K/2.500) = 49,232

Beobachtete Kapazitätseinheiten in Metriken = 49,23

## <a name="next-steps"></a>Nächste Schritte

Die folgenden Artikel enthalten weitere Informationen zu den Preisen für Azure Application Gateway:

* [Seite mit der Preisübersicht für Azure Application Gateway](https://azure.microsoft.com/pricing/details/application-gateway/)
* [Preisrechner für Azure Application Gateway](https://azure.microsoft.com/en-us/pricing/calculator/?service=application-gateway)
