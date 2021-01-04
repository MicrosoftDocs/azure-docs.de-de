---
title: Netzwerksicherheitsgruppen-Diensttags (NSG) für Azure HDInsight
description: Verwenden Sie HDInsight-Diensttags, um eingehenden Datenverkehr von Integritäts- und Verwaltungsdienstknoten zu Ihrem Cluster zuzulassen, ohne dass Ihren Netzwerksicherheitsgruppen IP-Adressen hinzugefügt werden.
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 08/11/2020
ms.openlocfilehash: 1a90bc6636dcb3aa81f09b0489850c1a95b3256d
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96022750"
---
# <a name="nsg-service-tags-for-azure-hdinsight"></a>Diensttags von Netzwerksicherheitsgruppen für Azure HDInsight

Azure HDInsight-Diensttags für Netzwerksicherheitsgruppen (NSGs) sind Gruppen von IP-Adressen für Integritäts- und Verwaltungsdienste. Diese Gruppen reduzieren die Komplexität beim Erstellen von Sicherheitsregeln. [Diensttags](../virtual-network/network-security-groups-overview.md#service-tags) lassen eingehendem Datenverkehr von bestimmten IP-Adressen zu, ohne dass alle [Verwaltungs-IP-Adressen](hdinsight-management-ip-addresses.md) in Ihre Netzwerksicherheitsgruppe eingegeben werden müssen.

Der HDInsight-Dienst verwaltet diese Diensttags. Sie können kein eigenes Diensttag erstellen oder ein vorhandenes Tag ändern. Microsoft verwaltet die Adresspräfixe, die dem Diensttag entsprechen, und aktualisiert das Diensttag automatisch, wenn sich die Adressen ändern.

Wenn Sie eine bestimmte Region verwenden möchten und das Diensttag noch nicht auf dieser Seite dokumentiert ist, können Sie mithilfe der [Ermittlungs-API für Diensttags](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview) Ihr Diensttag finden. Sie können auch die [JSON-Datei mit Diensttags](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) herunterladen und nach Ihrer gewünschten Region durchsuchen.

## <a name="get-started-with-service-tags"></a>Erste Schritte mit Diensttags

Sie haben zwei Optionen für die Verwendung von Diensttags in Ihrer Netzwerksicherheitsgruppe:

- **Verwenden eines einzelnen globalen HDInsight-Diensttags**: Diese Option öffnet Ihr virtuelles Netzwerk für alle IP-Adressen, die der HDInsight-Dienst zur Überwachung von Clustern in allen Regionen verwendet. Diese Option ist die einfachste Methode, sie ist jedoch möglicherweise nicht geeignet, wenn Sie über restriktive Sicherheitsanforderungen verfügen.

- **Verwenden von mehreren regionalen Diensttags**: Diese Option öffnet Ihr virtuelles Netzwerk nur für die IP-Adressen, die HDInsight in dieser bestimmten Region verwendet. Wenn Sie jedoch mehrere Regionen verwenden, müssen Sie Ihrem virtuellen Netzwerk mehrere Diensttags hinzufügen.

## <a name="use-a-single-global-hdinsight-service-tag"></a>Verwenden eines einzelnen globalen HDInsight-Diensttags

Die einfachste Möglichkeit, Diensttags mit Ihrem HDInsight-Cluster zu verwenden, besteht darin, das globale Tag `HDInsight` zu einer Netzwerksicherheitsgruppenregel hinzuzufügen.

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) Ihre Netzwerksicherheitsgruppe aus.

1. Wählen Sie unter **Einstellungen** die Option **Eingangssicherheitsregeln** und dann **+ Hinzufügen** aus.

1. Wählen Sie in der Dropdownliste **Quelle** den Eintrag **Diensttag** aus.

1. Wählen Sie in der Dropdownliste **Quelldiensttag** den Eintrag **HDInsight** aus.

    ![Hinzufügen eines Diensttags aus dem Azure-Portal](./media/hdinsight-service-tags/azure-portal-add-service-tag.png)

Dieses Tag enthält die IP-Adressen von Integritäts- und Verwaltungsdiensten für alle Regionen, in denen HDInsight verfügbar ist. Das Tag stellt sicher, dass Ihr Cluster mit den erforderlichen Integritäts- und Verwaltungsdiensten kommunizieren kann, unabhängig davon, wo er erstellt wurde.

## <a name="use-regional-hdinsight-service-tags"></a>Verwenden von regionalen HDInsight-Diensttags

Wenn die globale Tagoption nicht funktioniert, weil Sie restriktivere Berechtigungen benötigen, können Sie nur die für Ihre Region anwendbaren Diensttags zulassen. Abhängig von der Region, in der der Cluster erstellt wird, sind möglicherweise mehrere Diensttags anwendbar.

Informationen dazu, welche Diensttags Sie für Ihre Region hinzufügen müssen, finden Sie in den folgenden Abschnitten des Artikels.

### <a name="use-a-single-regional-service-tag"></a>Verwenden eines einzelnen regionalen Diensttags

Wenn sich Ihr Cluster in einer Region befindet, die in dieser Tabelle aufgelistet ist, müssen Sie Ihrer NSG nur ein einzelnes regionales Diensttag hinzufügen.

| Country | Region | Diensttag |
| ---- | ---- | ---- |
| Australien | Australien (Osten) | HDInsight.AustraliaEast |
| &nbsp; | Australien, Südosten | HDInsight.AustraliaSoutheast |
| &nbsp; | Australien, Mitte | HDInsight.AustraliaCentral |
| China | China, Osten 2 | HDInsight.ChinaEast2 |
| &nbsp; | China, Norden 2 | HDInsight.ChinaNorth2 |
| USA | USA Nord Mitte | HDInsight.NorthCentralUS |
| &nbsp; | USA, Westen 2 | HDInsight.WestUS2 |
| &nbsp; | USA, Westen-Mitte | HDInsight.WestCentralUS |
| Canada | Kanada, Osten | HDInsight.CanadaEast |
| Brasilien | Brasilien Süd | HDInsight.BrazilSouth |
| Korea | Korea, Mitte | HDInsight.KoreaCentral |
| &nbsp; | Korea, Süden | HDInsight.KoreaSouth |
| Indien | Indien, Mitte | HDInsight.CentralIndia |
| &nbsp; | Indien (Süden) | HDInsight.SouthIndia |
| Japan | Japan, Westen | HDInsight.JapanWest |
| Frankreich | Frankreich, Mitte| HDInsight.FranceCentral |
| UK | UK, Süden | HDInsight.UKSouth |
| Azure Government | USDoD, Mitte | HDInsight.USDoDCentral |
| &nbsp; | US Gov Texas | HDInsight.USGovTexas |
| &nbsp; | USDoD, Osten | HDInsight.USDoDEast |
| &nbsp; | US Gov Arizona | HDInsight.USGovArizona |

### <a name="use-multiple-regional-service-tags"></a>Verwenden von mehreren regionalen Diensttags

Wenn die Region, in der Ihr Cluster erstellt wurde, in der vorhergehenden Tabelle nicht aufgeführt war, müssen Sie mehrere regionale Diensttags zulassen. Die Notwendigkeit, mehr als eines zu verwenden, ist auf Unterschiede bei der Anordnung von Ressourcenanbietern für die verschiedenen Regionen zurückzuführen.

Die verbleibenden Regionen sind abhängig von den verwendeten regionalen Diensttags in Gruppen unterteilt.

#### <a name="group-1"></a>Gruppe 1

Wenn Ihr Cluster in einer der Regionen in der folgenden Tabelle erstellt wird, lassen Sie die Diensttags `HDInsight.WestUS` und `HDInsight.EastUS` zu. Außerdem wird das regionale Diensttag aufgeführt. Für Regionen in diesem Abschnitt sind drei Diensttags erforderlich.

Wenn Ihr Cluster z. B. in der Region `East US 2` erstellt wird, müssen Sie Ihrer Netzwerksicherheitsgruppe die folgenden Diensttags hinzufügen:

- `HDInsight.EastUS2`
- `HDInsight.WestUS`
- `HDInsight.EastUS`

| Country | Region | Diensttag |
| ---- | ---- | ---- |
| USA | USA (Ost) 2 | HDInsight.EastUS2 |
| &nbsp; | USA (Mitte) | HDInsight.CentralUS |
| &nbsp; | USA, Norden-Mitte | HDInsight. NorthCentralUS |
| &nbsp; | USA Süd Mitte | HDInsight.SouthCentralUS |
| &nbsp; | East US | HDInsight.EastUS |
| &nbsp; | USA (Westen) | HDInsight.WestUS |
| Japan | Japan, Osten | HDInsight.JapanEast |
| Europa | Nordeuropa | HDInsight.NorthEurope |
| &nbsp; | Europa, Westen| HDInsight.WestEurope |
| Asia | Asien, Osten | HDInsight.EastAsia |
| &nbsp; | Asien, Südosten | HDInsight.SoutheastAsia |
| Australien | Australien (Osten) | HDInsight.AustraliaEast |

#### <a name="group-2"></a>Gruppe 2

Cluster in den Regionen *China, Norden* und *China, Osten* müssen zwei Diensttags zulassen: `HDInsight.ChinaNorth` und `HDInsight.ChinaEast`.

#### <a name="group-3"></a>Gruppe 3

Cluster in den Regionen *US Gov Iowa* und *US Gov Virginia* müssen zwei Diensttags zulassen: `HDInsight.USGovIowa` und `HDInsight.USGovVirginia`.

#### <a name="group-4"></a>Gruppe 4

Cluster in den Regionen *Deutschland, Mitte* und *Deutschland, Nordosten* müssen zwei Diensttags zulassen: `HDInsight.GermanyCentral` und `HDInsight.GermanyNortheast`.

## <a name="next-steps"></a>Nächste Schritte

- [Netzwerksicherheitsgruppen: Diensttags](../virtual-network/network-security-groups-overview.md#security-rules)
- [Erstellen von virtuellen Netzwerken für Azure HDInsight-Cluster](hdinsight-create-virtual-network.md)