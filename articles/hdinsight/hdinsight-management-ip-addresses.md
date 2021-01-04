---
title: Azure HDInsight-Verwaltungs-IP-Adressen
description: Erfahren Sie, von welchen IP-Adressen Sie eingehenden Datenverkehr zulassen müssen, um Netzwerksicherheitsgruppen und benutzerdefinierte Routen für virtuelle Netzwerke mit Azure HDInsight richtig zu konfigurieren.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 08/11/2020
ms.openlocfilehash: 9fa38e045bbe29e5d45587adf0d277c1414fee4c
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96549037"
---
# <a name="hdinsight-management-ip-addresses"></a>HDInsight-Verwaltungs-IP-Adressen

Dieser Artikel enthält die IP-Adressen, die von den Integritäts- und Verwaltungsdiensten von Azure HDInsight verwendet werden. Wenn Sie Netzwerksicherheitsgruppen (NSGs) oder benutzerdefinierte Routen (User Defined Routes, UDRs) verwenden, müssen Sie möglicherweise einige dieser IP-Adressen der Liste der zulässigen Adressen für eingehenden Netzwerkdatenverkehr hinzufügen.

## <a name="introduction"></a>Einführung
 
> [!Important]
> In den meisten Fällen können Sie jetzt [Diensttags](hdinsight-service-tags.md) für Netzwerksicherheitsgruppen verwenden, statt IP-Adressen manuell hinzuzufügen. IP-Adressen werden für neue Azure-Regionen nicht veröffentlicht und nur veröffentlichte Azure-Diensttags aufweisen. Die statischen IP-Adressen für Verwaltungs-IP-Adressen werden nach und nach als veraltet markiert.

Wenn Sie Netzwerksicherheitsgruppen (NSG) oder benutzerdefinierte Routen (User-Defined Routes, UDRs) zum Steuern von eingehendem Datenverkehr auf Ihrem HDInsight-Cluster verwenden, müssen Sie sicherstellen, dass der Cluster mit kritischen Integritäts- und Verwaltungsdiensten von Azure kommunizieren kann.  Einige der IP-Adressen für diese Dienste sind regionsspezifisch, und einige gelten für alle Azure-Regionen. Unter Umständen müssen Sie auch Datenverkehr über den Azure DNS-Dienst zulassen, wenn Sie kein benutzerdefiniertes DNS verwenden.

Wenn Sie IP-Adressen für eine hier nicht aufgeführte Region benötigen, können Sie die [Ermittlungs-API für Diensttags](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview) verwenden, um IP-Adressen für Ihre Region zu finden. Wenn Sie die API nicht verwenden können, laden Sie die [JSON-Datei mit Diensttags](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) herunter, und suchen Sie nach der gewünschten Region.

HDInsight führt die Überprüfung für diese Regeln mit Clustererstellung und -skalierung durch, um weitere Fehler zu vermeiden. Wenn die Überprüfung nicht bestanden wird, tritt bei Erstellung und Skalierung ein Fehler auf.

In den folgenden Abschnitten werden die spezifischen IP-Adressen erläutert, die zugelassen werden müssen.

## <a name="azure-dns-service"></a>Azure DNS-Dienst

Wenn Sie den von Azure bereitgestellten DNS-Dienst verwenden, müssen Sie den Zugriff auf __168.63.129.16__ an Port 53 sowohl für TCP als auch UDP zulassen. Weitere Informationen finden Sie im Dokument [Namensauflösung für virtuelle Computer und Rolleninstanzen](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md). Überspringen Sie diesen Schritt, wenn Sie ein benutzerdefiniertes DNS verwenden.

## <a name="health-and-management-services-all-regions"></a>Integritäts- und Verwaltungsdienste: Alle Regionen

Lassen Sie Datenverkehr von den folgenden IP-Adressen für Integritäts- und Verwaltungsdienste von Azure HDInsight zu, die für alle Azure-Regionen gelten:

| Quell-IP-Adresse | Destination  | Direction |
| ---- | ----- | ----- |
| 168.61.49.99 | \*:443 | Eingehend |
| 23.99.5.239 | \*:443 | Eingehend |
| 168.61.48.131 | \*:443 | Eingehend |
| 138.91.141.162 | \*:443 | Eingehend |

## <a name="health-and-management-services-specific-regions"></a>Integritäts- und Verwaltungsdienste: Bestimmte Regionen

Lassen Sie Datenverkehr von den IP-Adressen zu, die für Azure HDInsight-Integritäts- und -Verwaltungsdienste in der jeweiligen Azure-Region angegeben sind, in der sich Ihre Ressourcen befinden:

> [!IMPORTANT]  
> Wenn die von Ihnen verwendete Azure-Region nicht aufgeführt ist, verwenden Sie das Feature [Diensttag](hdinsight-service-tags.md) für Netzwerksicherheitsgruppen.

| Country | Region | Zulässige Quell-IP-Adressen | Zulässiges Ziel | Direction |
| ---- | ---- | ---- | ---- | ----- |
| Asia | Asien, Osten | 23.102.235.122</br>52.175.38.134 | \*:443 | Eingehend |
| &nbsp; | Asien, Südosten | 13.76.245.160</br>13.76.136.249 | \*:443 | Eingehend |
| Australien | Australien (Osten) | 104.210.84.115</br>13.75.152.195 | \*:443 | Eingehend |
| &nbsp; | Australien, Südosten | 13.77.2.56</br>13.77.2.94 | \*:443 | Eingehend |
| Brasilien | Brasilien Süd | 191.235.84.104</br>191.235.87.113 | \*:443 | Eingehend |
| Canada | Kanada, Osten | 52.229.127.96</br>52.229.123.172 | \*:443 | Eingehend |
| &nbsp; | Kanada, Mitte | 52.228.37.66</br>52.228.45.222 |\*: 443 | Eingehend |
| China | China, Norden | 42.159.96.170</br>139.217.2.219</br></br>42.159.198.178</br>42.159.234.157 | \*:443 | Eingehend |
| &nbsp; | China, Osten | 42.159.198.178</br>42.159.234.157</br></br>42.159.96.170</br>139.217.2.219 | \*:443 | Eingehend |
| &nbsp; | China, Norden 2 | 40.73.37.141</br>40.73.38.172 | \*:443 | Eingehend |
| &nbsp; | China, Osten 2 | 139.217.227.106</br>139.217.228.187 | \*:443 | Eingehend |
| Europa | Nordeuropa | 52.164.210.96</br>13.74.153.132 | \*:443 | Eingehend |
| &nbsp; | Europa, Westen| 52.166.243.90</br>52.174.36.244 | \*:443 | Eingehend |
| Frankreich | Frankreich, Mitte| 20.188.39.64</br>40.89.157.135 | \*:443 | Eingehend |
| Deutschland | Deutschland, Mitte | 51.4.146.68</br>51.4.146.80 | \*:443 | Eingehend |
| &nbsp; | Deutschland, Nordosten | 51.5.150.132</br>51.5.144.101 | \*:443 | Eingehend |
| Indien | Indien, Mitte | 52.172.153.209</br>52.172.152.49 | \*:443 | Eingehend |
| &nbsp; | Indien (Süden) | 104.211.223.67<br/>104.211.216.210 | \*:443 | Eingehend |
| Japan | Japan, Osten | 13.78.125.90</br>13.78.89.60 | \*:443 | Eingehend |
| &nbsp; | Japan, Westen | 40.74.125.69</br>138.91.29.150 | \*:443 | Eingehend |
| Korea | Korea, Mitte | 52.231.39.142</br>52.231.36.209 | \*:443 | Eingehend |
| &nbsp; | Korea, Süden | 52.231.203.16</br>52.231.205.214 | \*:443 | Eingehend
| United Kingdom | UK, Westen | 51.141.13.110</br>51.141.7.20 | \*:443 | Eingehend |
| &nbsp; | UK, Süden | 51.140.47.39</br>51.140.52.16 | \*:443 | Eingehend |
| USA | USA (Mitte) | 13.89.171.122</br>13.89.171.124 | \*:443 | Eingehend |
| &nbsp; | East US | 13.82.225.233</br>40.71.175.99 | \*:443 | Eingehend |
| &nbsp; | USA Nord Mitte | 157.56.8.38</br>157.55.213.99 | \*:443 | Eingehend |
| &nbsp; | USA, Westen-Mitte | 52.161.23.15</br>52.161.10.167 | \*:443 | Eingehend |
| &nbsp; | USA (Westen) | 13.64.254.98</br>23.101.196.19 | \*:443 | Eingehend |
| &nbsp; | USA, Westen 2 | 52.175.211.210</br>52.175.222.222 | \*:443 | Eingehend |
| &nbsp; | Vereinigte Arabische Emirate, Norden | 65.52.252.96</br>65.52.252.97 | \*:443 | Eingehend |
| &nbsp; | VAE, Mitte | 20.37.76.96</br>20.37.76.99 | \*:443 | Eingehend |

Informationen zu den IP-Adressen, die für Azure Government verwendet werden, finden Sie im Dokument [Azure Government Intelligence + Analytics](../azure-government/compare-azure-government-global-azure.md) (Azure Government – Daten und Analyse)

Weitere Informationen finden Sie unter [Steuern des Netzwerkdatenverkehrs](./control-network-traffic.md).

Wenn Sie benutzerdefinierte Routen (User-Defined Routes, UDRs) verwenden, sollten Sie eine Route angeben und ausgehenden Datenverkehr vom virtuellen Netzwerk an die oben genannten IP-Adressen zulassen und den nächsten Hop dabei auf „Internet“ festlegen.

## <a name="next-steps"></a>Nächste Schritte

* [Erstellen von virtuellen Netzwerken für Azure HDInsight-Cluster](hdinsight-create-virtual-network.md)
* [Netzwerksicherheitsgruppen-Diensttags (NSG) für Azure HDInsight](hdinsight-service-tags.md)
