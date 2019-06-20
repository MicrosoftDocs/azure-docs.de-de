---
title: Verwalten von Sicherheitswarnungen in Azure Security Center | Microsoft-Dokumentation
description: In diesem Dokument erfahren Sie, wie Sie Azure Security Center-Funktionen verwenden, um Sicherheitswarnungen zu verwalten und auf diese zu reagieren.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: b88a8df7-6979-479b-8039-04da1b8737a7
ms.service: security-center
ms.topic: conceptual
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/22/2018
ms.author: rkarlin
ms.openlocfilehash: 582912160c8ed514401be3522e52dcc6eb45d263
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65235772"
---
# <a name="managing-and-responding-to-security-alerts-in-azure-security-center"></a>Verwalten von und Reagieren auf Sicherheitswarnungen in Azure Security Center
In diesem Dokument erfahren Sie, wie Sie Azure Security Center verwenden, um Sicherheitswarnungen zu verwalten und auf diese zu reagieren.

> [!NOTE]
> Führen Sie ein Upgrade auf Azure Security Center Standard durch, um erweiterte Erkennungsfunktionen zu aktivieren. Eine kostenlose Testversion ist verfügbar. Wenn Sie ein Upgrade durchführen möchten, wählen Sie in der [Sicherheitsrichtlinie](tutorial-security-policy.md)die Tarifoption aus. Weitere Informationen finden Sie unter [Azure Security Center Preise](security-center-pricing.md).
>
>

## <a name="what-are-security-alerts"></a>Was sind Sicherheitswarnungen?
Security Center erfasst, analysiert und vereinigt automatisch Protokolldaten von Ihren Azure-Ressourcen, vom Netzwerk und von verbundenen Partnerlösungen, z.B. Lösungen zum Schutz von Firewalls und Endpunkten, um echte Bedrohungen zu erkennen und falsch positive Ergebnisse zu reduzieren. Eine Liste mit priorisierten Sicherheitswarnungen wird im Security Center zusammen mit den Informationen angezeigt, die Sie zum schnellen Untersuchen des Problems benötigen. Außerdem sind Empfehlungen zum Reagieren auf einen Angriff vorhanden.


> [!NOTE]
> Weitere Informationen zur Funktionsweise der Security Center-Erkennungsfunktionen finden Sie unter [Azure Security Center-Erkennungsfunktionen](security-center-detection-capabilities.md).
>
>

## <a name="managing-security-alerts"></a>Verwalten von Sicherheitswarnungen
Aktuelle Warnungen können Sie auf der Kachel **Sicherheitswarnungen** prüfen. Gehen Sie wie folgt vor, wenn Sie weitere Details zu den einzelnen Warnungen anzeigen möchten:

1. Auf dem Security Center-Dashboard befindet sich die Kachel **Sicherheitswarnungen**.

    ![Kachel „Sicherheitswarnungen“ in Security Center](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig1-ga.png)

2. Klicken Sie auf die Kachel, um die **Sicherheitswarnungen** zu öffnen und weitere Details zu den Warnungen anzuzeigen.

   ![Die Sicherheitswarnungen in Security Center](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig2-ga.png)

Im unteren Bereich dieser Seite werden Details zu den einzelnen Warnungen angezeigt. Zum Sortieren klicken Sie auf die Spalte, nach der Sie sortieren möchten. Die Spalten sind wie folgt definiert:

* **Beschreibung:** Eine kurze Erläuterung der Warnung.
* **Anzahl:** Eine Liste mit allen Warnungen dieses speziellen Typs, die an einem bestimmten Tag erkannt wurden.
* **Erkannt von:** Der Dienst, der die Warnung ausgelöst hat.
* **Datum:** Das Datum, an dem das Ereignis aufgetreten ist.
* **Zustand:** Der aktuelle Zustand für diese Warnung. Es gibt zwei Arten von Zuständen:
  * **Aktiv:** Die Sicherheitswarnung wurde erkannt.
  * **Verworfen:** Die Sicherheitswarnung wurde vom Benutzer verworfen. Dieser Status wird üblicherweise für Warnungen verwendet, die untersucht und behoben oder als harmlos eingestuft wurden.
* **Schweregrad:** Der Schweregrad („Hoch“, „Mittel“ oder „Niedrig“).

> [!NOTE]
> Von Security Center generierte Sicherheitswarnungen werden auch im Azure-Aktivitätsprotokoll angezeigt. Weitere Informationen zum Zugreifen auf das Azure-Aktivitätsprotokoll finden Sie unter [Anzeigen von Aktivitätsprotokollen, um Aktionen an Ressourcen zu überwachen](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
>


### <a name="alert-severity"></a>Schweregrad der Warnung

-   **Hoch**: Ihre Ressource wurde mit hoher Wahrscheinlichkeit kompromittiert. Sie sollten dies sofort überprüfen. Von Security Center werden sowohl die böswillige Absicht als auch die ermittelten Ergebnisse zur Ausgabe der Warnung als hoch eingestuft. Ein Beispiel hierfür ist eine Warnung, bei der die Ausführung eines bekannten schädlichen Tools wie Mimikatz erkannt wird, das häufig für den Diebstahl von Anmeldeinformationen verwendet wird. 
-   **Mittel:** Eine potenziell verdächtige Aktivität, die möglicherweise auf die Kompromittierung einer Ressource hindeutet.
Security Center stuft die Analyse oder die ermittelten Ergebnisse als „Mittel“ und die schädliche Absicht als „Mittel“ bis „Hoch“ ein. Hierbei handelt es sich normalerweise um Erkennungen, die auf maschinellem Lernen oder Anomalien basieren. Ein Beispiel hierfür ist ein Anmeldeversuch, der von einem ungewöhnlichen Standort aus durchgeführt wird.
-   **Niedrig**: Hierbei kann es sich um ein unschädliches positives Ergebnis oder um einen blockierten Angriff handeln. 
    - Security Center stuft die Absicht nicht als schädlich ein, und die Aktivität ist vermutlich harmlos. Das Löschen eines Protokolls ist beispielsweise eine Aktion, die ggf. von einem Angreifer durchgeführt wird, um Spuren zu verwischen. Häufig handelt es sich aber um einen Routinevorgang eines Administrators.
    - Security Center teilt Ihnen normalerweise nicht mit, wenn Angriffe blockiert wurden. Eine Ausnahme sind interessante Fälle, bei denen wir Ihnen raten, dass Sie sich diese ansehen. 
-   **Informativ:** Warnungen vom Typ „Information“ werden nur angezeigt, wenn Sie für einen Sicherheitsincident einen Drilldown ausführen oder die REST-API mit einer bestimmten Warnungs-ID verwenden. Ein Incident besteht normalerweise aus mehreren Warnungen, von denen einige gesondert als „Information“ angezeigt werden, die aber im Zusammenhang mit anderen Warnungen durchaus interessant sein können und untersucht werden sollten.  

> [!NOTE]
> Bei Verwendung der API-Version **2015-06-01-preview** gibt es in Hinsicht darauf, welche Schweregrade von Alarmen auf welche Szenarien angewendet werden, Abweichungen gegenüber der oben aufgeführten Liste.  

### <a name="filtering-alerts"></a>Filtern von Warnungen
Sie können Warnungen nach Datum, Status und Schweregrad filtern. Das Filtern von Warnungen kann nützlich für Szenarien sein, in denen Sie den Bereich der angezeigten Warnungen einschränken müssen. Es könnte beispielsweise sein, dass Sie während der Untersuchung einer möglichen Sicherheitsverletzung im System die Sicherheitswarnungen überprüfen möchten, die in den letzten 24 Stunden aufgetreten sind.

1. Klicken Sie unter **Sicherheitswarnungen** auf **Filter**. Der **Filter** wird geöffnet. Hier können Sie die gewünschten Werte für Datum, Zustand und Schweregrad auswählen.

    ![Filtern von Warnungen in Security Center](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig3-2017.png)

### <a name="respond-to-security-alerts"></a>Reagieren auf Sicherheitswarnungen
Wählen Sie eine Sicherheitswarnung aus, um weitere Informationen zu den Ereignissen zu erhalten, die die Warnung ausgelöst haben, sowie zu den Schritten, die Sie als Reaktion auf den Angriff ausführen müssen (falls zutreffend). Sicherheitswarnungen werden nach Typ und Datum gruppiert. Wenn Sie auf eine Sicherheitswarnung klicken, öffnet sich eine Seite mit einer Liste der gruppierten Warnungen.

![Reagieren auf Sicherheitswarnungen in Azure Security Center](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig5-ga.png)

In diesem Fall bezieht sich die ausgelöste Warnung auf eine verdächtige RDP-Aktivität (Remote Desktop Protocol). Die erste Spalte zeigt, welche Ressourcen angegriffen wurden. In der zweiten Spalte sehen Sie , wie häufig die Ressource angegriffen wurde. Die dritte Spalte enthält die Uhrzeit des Angriffs. Die vierte Spalte zeigt den Status der Warnung und die fünfte Spalte den Schweregrad des Angriffs. Klicken Sie nach dem Überprüfen dieser Informationen auf die Ressource, die angegriffen wurde.

![Vorschläge für die Bearbeitung von Sicherheitswarnungen in Azure Security Center](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig6-ga.png)

Das Feld **Beschreibung** enthält weitere Informationen zu diesem Ereignis. Dank dieser zusätzlichen Details erhalten Sie Informationen dazu, wodurch die Sicherheitswarnung ausgelöst wurde, sowie die Zielressource, die IP-Quelladresse (falls zutreffend) und Empfehlungen zur Lösung.  In einigen Fällen ist die IP-Quelladresse leer (nicht verfügbar), da nicht alle Windows-Sicherheitsereignisprotokolle die IP-Adresse enthalten.

Die von Security Center vorgeschlagene Wiederherstellung variiert je nach Sicherheitshinweis. In einigen Fällen müssen Sie möglicherweise weitere Azure-Funktionen verwenden, um die empfohlenen Lösungen zu implementieren. Beispielsweise kann die Abhilfe für diesen Angriff darin bestehen, die IP-Adresse, die den Angriff generiert, durch eine [Netzwerk-ACL](../virtual-network/virtual-networks-acl.md) oder eine Regel für die [Netzwerksicherheitsgruppe](../virtual-network/security-overview.md#security-rules) auf eine Negativliste zu setzen. Weitere Informationen zu den verschiedenen Arten von Warnungen finden Sie unter [Sicherheitswarnungen nach Typ in Azure Security Center](security-center-alerts-type.md).

> [!NOTE]
> Für Security Center wurde als eingeschränkte Vorschauversion ein neuer Satz von Erkennungen veröffentlicht, die anhand von AuditD-Datensätzen (einem allgemeinen Überwachungsframework) schädliches Verhalten auf Linux-Computern erkennen. Wenn Sie die Vorschauversion ausprobieren möchten, senden Sie uns eine [E-Mail](mailto:ASC_linuxdetections@microsoft.com) mit Ihren Abonnement-IDs.


## <a name="see-also"></a>Weitere Informationen
In diesem Dokument haben Sie erfahren, wie Sie Sicherheitsrichtlinien in Security Center konfigurieren können. Weitere Informationen zu Security Center finden Sie in den folgenden Quellen:

* [Behandeln von Sicherheitswarnungen in Azure Security Center](security-center-incident.md)
* [Azure Security Center-Erkennungsfunktionen](security-center-detection-capabilities.md)
* [Planungs- und Betriebshandbuch für Azure Security Center](security-center-planning-and-operations-guide.md)
* [Azure Security Center – Häufig gestellte Fragen:](security-center-faq.md) Hier finden Sie häufig gestellte Fragen zur Verwendung des Diensts.
* [Azure Security Blog](https://blogs.msdn.com/b/azuresecurity/) (Blog zur Azure-Sicherheit): Hier finden Sie Blogbeiträge zur Azure-Sicherheit und -Compliance.
