---
title: Visualisieren von Netzwerk-Datenverkehrsmustern mithilfe von Azure Network Watcher und Open Source-Tools | Microsoft-Dokumentation
description: Auf dieser Seite wird beschrieben, wie Sie die Network Watcher-Paketerfassung mit CapAnalysis zum Visualisieren von Mustern im eingehenden und ausgehenden VM-Datenverkehr verwenden.
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: 936d881b-49f9-4798-8e45-d7185ec9fe89
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: kumud
ms.openlocfilehash: 3a0ae782d3fe97752ca8b9e786c3c2672f554277
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64936021"
---
# <a name="visualize-network-traffic-patterns-to-and-from-your-vms-using-open-source-tools"></a>Visualisieren von Mustern im eingehenden und ausgehenden VM-Datenverkehr mithilfe von Open Source-Tools

Paketerfassungen enthalten Netzwerkdaten, die Ihnen das Durchführen von Maßnahmen zur Netzwerkforensik und eingehende Paketuntersuchungen ermöglichen. Sie können viele verschiedene Open Source-Tools zum Analysieren von Paketerfassungen verwenden, um Erkenntnisse zu Ihrem Netzwerk zu gewinnen. Ein Open Source-Tool dieser Art, mit dem die Paketerfassung visualisiert werden kann, ist CapAnalysis. Die Visualisierung von Paketerfassungsdaten ist eine nützliche Möglichkeit, um sich schnell einen Überblick über Muster und Anomalien in Ihrem Netzwerk zu verschaffen. Außerdem ist es mit Visualisierungen möglich, Erkenntnisse dieser Art auf einfache Weise zu teilen.

Mit Network Watcher von Azure können Sie Daten erfassen, indem Sie im Netzwerk Paketerfassungen durchführen. Dieser Artikel enthält eine exemplarische Vorgehensweise dazu, wie Sie Erkenntnisse aus Paketerfassungen visualisieren und gewinnen, indem Sie CapAnalysis mit Network Watcher verwenden.

## <a name="scenario"></a>Szenario

Sie haben eine einfache Webanwendung auf einem virtuellen Computer in Azure bereitgestellt und möchten Open Source-Tools zum Visualisieren des Netzwerkdatenverkehrs verwenden, um Datenflussmuster und etwaige Anomalien schnell zu identifizieren. Mit Network Watcher können Sie eine Paketerfassung für Ihre Netzwerkumgebung durchführen und direkt in Ihrem Speicherkonto speichern. Mit CapAnalysis kann die Paketerfassung dann direkt aus dem Speicherblob erfasst und der Inhalt visualisiert werden.

![Szenario][1]

## <a name="steps"></a>Schritte

### <a name="install-capanalysis"></a>Installieren von CapAnalysis

Zum Installieren von CapAnalysis auf einem virtuellen Computer finden Sie die offizielle Anleitung unter https://www.capanalysis.net/ca/how-to-install-capanalysis.
Um per Remoteverbindung auf CapAnalysis zuzugreifen, muss Port 9877 auf Ihrer VM geöffnet werden, indem eine neue Sicherheitsregel für eingehenden Datenverkehr hinzugefügt wird. Weitere Informationen zur Erstellung von Regeln in Netzwerksicherheitsgruppen finden Sie unter [Erstellen von Regeln in einer vorhandenen NSG](../virtual-network/manage-network-security-group.md#create-a-security-rule). Nachdem die Regel erfolgreich hinzugefügt wurde, sollten Sie über `http://<PublicIP>:9877` auf CapAnalysis zugreifen können.

### <a name="use-azure-network-watcher-to-start-a-packet-capture-session"></a>Verwenden von Azure Network Watcher zum Starten einer Paketerfassungssitzung

Mit Network Watcher können Sie Pakete erfassen, um den eingehenden und ausgehenden Datenverkehr eines virtuellen Computers nachzuverfolgen. Unter [Verwalten von Paketerfassungen mit Azure Network Watcher über das Portal](network-watcher-packet-capture-manage-portal.md) finden Sie eine Anleitung zum Starten einer Paketerfassungssitzung. Eine Paketerfassung kann in einem Speicherblob gespeichert werden, auf das der Zugriff mit CapAnalysis möglich ist.

### <a name="upload-a-packet-capture-to-capanalysis"></a>Hochladen einer Paketerfassung in CapAnalysis
Sie können eine mit Network Watcher erstellte Paketerfassung direkt hochladen, indem Sie die Registerkarte „Aus URL importieren” verwenden und einen Link zu dem Speicherblob angeben, in dem die Paketerfassung gespeichert ist.

Achten Sie beim Angeben eines Links zu CapAnalysis darauf, ein SAS-Token an die Speicherblob-URL anzufügen.  Navigieren Sie hierzu vom Speicherkonto zu „Shared Access Signature“, geben Sie die zulässigen Berechtigungen an, und klicken Sie auf die Schaltfläche „SAS generieren“, um ein Token zu erstellen. Anschließend können Sie das SAS-Token an die Speicherblob-URL der Paketerfassung anfügen.

Die sich ergebende URL sieht dann in etwa wie folgt aus: http://storageaccount.blob.core.windows.net/container/location?addSASkeyhere


### <a name="analyzing-packet-captures"></a>Analysieren von Paketerfassungen

CapAnalysis umfasst verschiedene Optionen zum Visualisieren Ihrer Paketerfassung, bei denen die Analyse jeweils aus einer anderen Perspektive durchgeführt wird. Anhand dieser visuellen Übersichten können Sie Ihre Datenverkehrstrends für das Netzwerk verstehen und ungewöhnliche Aktivitäten schnell erkennen. Einige Funktionen sind in der folgenden Liste dargestellt:

1. Datenflusstabellen

    Diese Tabelle enthält die Liste der Datenflüsse in den Paketdaten, den Zeitstempel, der den Datenflüssen zugeordnet ist, und die unterschiedlichen Protokolle des Datenflusses sowie die Quell- und Ziel-IP-Adresse.

    ![Seite „Flows“ (Datenflüsse) von CapAnalysis][5]

1. Protokollübersicht

    In diesem Bereich können Sie sich schnell einen Überblick über die Verteilung des Netzwerkdatenverkehrs auf die verschiedenen Protokolle und geografischen Bereiche verschaffen.

    ![CapAnalysis-Protokollübersicht][6]

1. Statistiken

    In diesem Bereich können Sie die Statistiken zum Netzwerkdatenverkehr anzeigen: gesendete und empfangene Bytes von Quell- und Ziel-IP-Adressen, Datenflüsse für die einzelnen Quell- und Ziel-IP-Adressen, das für verschiedene Datenflüsse verwendete Protokoll und die Dauer der Datenflüsse.

    ![CapAnalysis-Statistiken][7]

1. GeoMAP

    Dieser Bereich enthält eine Kartenansicht für Ihren Netzwerkdatenverkehr, wobei die Farben jeweils das Datenverkehrsvolumen für die einzelnen Länder/Regionen angeben. Sie können hervorgehobene Länder/Regionen auswählen, um weitere Datenflussstatistiken anzuzeigen, z. B. den Anteil der Daten, die von IP-Adressen in diesem Land bzw. in dieser Region gesendet bzw. empfangen werden.

    ![GeoMAP][8]

1. Filter

    CapAnalysis umfasst eine Reihe von Filtern zur schnellen Analyse spezifischer Pakete. Beispielsweise können Sie die Daten nach Protokoll filtern, um bestimmte Erkenntnisse zur jeweiligen Teilmenge des Datenverkehr zu gewinnen.

    ![Filter][11]

    Besuchen Sie [https://www.capanalysis.net/ca/#about](https://www.capanalysis.net/ca/#about), um mehr zu den CapAnalysis-Funktionen zu erfahren.

## <a name="conclusion"></a>Zusammenfassung

Mit der Paketerfassungsfunktion von Network Watcher können Sie die Daten erfassen, die zum Durchführen von Maßnahmen zur Netzwerkforensik und zum besseren Verständnis Ihres Netzwerkdatenverkehrs dienen. In diesem Szenario wurde veranschaulicht, wie Paketerfassungen per Network Watcher leicht in Open Source-Visualisierungstools integriert werden können. Mit Open Source-Tools, wie beispielsweise CapAnalysis zum Visualisieren von Paketerfassungen, können Sie eingehende Paketuntersuchungen durchführen und Trends für Ihren Netzwerkdatenverkehr schnell identifizieren.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu NSG-Datenflussprotokollen finden Sie unter [Introduction to flow logging for Network Security Groups](network-watcher-nsg-flow-logging-overview.md) (Einführung in die Datenflussprotokollierung für Netzwerksicherheitsgruppen).

Erfahren Sie unter [Visualisieren der Datenflussprotokolle von Netzwerksicherheitsgruppen mit Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md), wie Sie Ihre NSG-Datenflussprotokolle mit Power BI visualisieren.
<!--Image references-->

[1]: ./media/network-watcher-using-open-source-tools/figure1.png
[2]: ./media/network-watcher-using-open-source-tools/figure2.png
[3]: ./media/network-watcher-using-open-source-tools/figure3.png
[4]: ./media/network-watcher-using-open-source-tools/figure4.png
[5]: ./media/network-watcher-using-open-source-tools/figure5.png
[6]: ./media/network-watcher-using-open-source-tools/figure6.png
[7]: ./media/network-watcher-using-open-source-tools/figure7.png
[8]: ./media/network-watcher-using-open-source-tools/figure8.png
[9]: ./media/network-watcher-using-open-source-tools/figure9.png
[10]: ./media/network-watcher-using-open-source-tools/figure10.png
[11]: ./media/network-watcher-using-open-source-tools/figure11.png
