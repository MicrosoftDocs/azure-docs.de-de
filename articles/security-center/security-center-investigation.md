---
title: Untersuchen von Vorfällen und Warnungen in Azure Security Center | Microsoft-Dokumentation
description: Dieses Dokument enthält hilfreiche Informationen zur Untersuchung von Sicherheitsvorfällen und -warnungen mithilfe des Untersuchungsfeatures in Azure Security Center.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: a8e894a9-8781-4749-ae8f-8c8e01658566
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/29/2018
ms.author: rkarlin
ms.openlocfilehash: 55def52d248babd439227cdcd4170c0a3ca91bb1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64569825"
---
# <a name="investigate-incidents-and-alerts-in-azure-security-center-preview"></a>Untersuchen von Vorfällen und Warnungen in Azure Security Center (Vorschauversion)
Dieses Dokument enthält hilfreiche Informationen zur Untersuchung von Sicherheitsvorfällen und -warnungen mithilfe des Untersuchungsfeatures in Azure Security Center.

> [!NOTE]
> Die Funktion zur Untersuchung von Warnungen und Vorfällen wird am 31. Juli 2019 eingestellt. Weitere Informationen und alternative Dienste finden Sie unter [Auslaufen von Security Center-Funktionen (Juli 2019)](security-center-features-retirement-july2019.md#menu_investigate).

## <a name="what-is-investigation-in-security-center"></a>Was ist das Untersuchungsfeature in Security Center?
Mit dem Untersuchungsfeature in Security Center können Sie einen potenziellen [Sicherheitsvorfall](https://docs.microsoft.com/azure/security-center/security-center-incident) selektieren, sich ein Bild vom Umfang machen und der Ursache auf den Grund gehen.

Durch die Verknüpfung sämtlicher Entitäten ([Sicherheitswarnungen](https://docs.microsoft.com/azure/security-center/security-center-alerts-type), Benutzer, Computer und Vorfälle), die mit dem untersuchten Vorfall in Zusammenhang stehen, soll der Untersuchungsprozess vereinfacht werden.  Hierzu kann Security Center relevante Daten mit allen beteiligten Entitäten korrelieren und diese Korrelation in einem Livediagramm darstellen, das die Navigation durch die Objekte und die Visualisierung relevanter Informationen erleichtert.


> [!NOTE]
> * [Benutzerdefinierte Warnungen](security-center-custom-alert.md) werden im Untersuchungsfeature von Security Center nicht unterstützt.
> * Eine Untersuchung wird nur für Warnungen basierend auf Daten unterstützt, die von Windows-Servern gesammelt werden.


## <a name="how-investigation-works"></a>Funktionsweise der Untersuchung
Die Untersuchung besteht aus einem Diagramm, das den zentralen Bereich des Untersuchungsdashboards einnimmt. Das Diagramm ist immer auf eine bestimmte Entität fokussiert und stellt die Entitäten dar, die mit ihr in Verbindung stehen. Bei der Entität kann es sich beispielsweise um eine Sicherheitswarnung oder um einen Benutzer, Computer oder Vorfall handeln.

![Map](./media/security-center-investigation/security-center-investigation-fig1.png)

Der Benutzer kann im Diagramm auf eine Entität klicken, um zwischen den Entitäten zu navigieren. Das Diagramm wird automatisch auf die ausgewählte Entität und die dazugehörigen Entitäten zentriert. Nicht mehr relevante Entitäten werden ggf. aus dem Diagramm entfernt.

### <a name="investigation-path"></a>Untersuchungspfad
Während der Navigation zu verschiedenen Entitäten kann der Benutzer dank des Untersuchungspfads den Untersuchungskontext nachvollziehen und blitzschnell navigieren. Der Vorfall mit den Untersuchungsergebnissen befindet sich immer im äußerst linken Vorfall des Untersuchungspfads.

![`Path`](./media/security-center-investigation/security-center-investigation-fig2.png)

### <a name="general-information"></a>Allgemeine Informationen
Wenn eine Entität im Diagramm dargestellt wird, stehen auf den Registerkarten zusätzliche Informationen zu dieser Entität zur Verfügung. Auf der Registerkarte **Informationen** werden allgemeine Informationen zu der Entität aus verschiedenen Quellen angezeigt.

![Allgemeine Informationen](./media/security-center-investigation/security-center-investigation-fig3.png)

Auf der Registerkarte „Informationen“ befinden sich relevante Informationen zum in der Zuordnung ausgewählten Vorfall. Bei einem Vorfall handelt es sich um einen Container, der die Ergebnisse einer Untersuchung enthält. Jede Untersuchung findet im Kontext eines Vorfalls statt.

Ein Vorfall wird nur erstellt, wenn ein Benutzer bei einer bestimmten Warnung auf die Schaltfläche **Untersuchung starten** klickt. Der Prüfer kann grundsätzlich Entitäten wie etwa Benutzer, Computer oder Warnungen markieren. Wenn eine Entität als zugehörig markiert wird, wird ein Grund angegeben. Daraufhin wird die Entität im Diagramm und in der Vorfallsentitätenliste direkt unter dem Vorfall angezeigt.

### <a name="entities"></a>Entitäten

Auf der Registerkarte **Entitäten** werden alle verknüpften Entitäten nach Typ gruppiert. Dies ist in zwei Fällen hilfreich: wenn das Diagramm zu viele Entitäten enthält und wenn die Entitätsnamen zu lang sind und es daher einfacher ist, sie in einer tabellarischen Darstellung zu untersuchen.

![Entitäten](./media/security-center-investigation/security-center-investigation-fig4.png)

### <a name="search"></a>Suchen,

Auf der Registerkarte **Suche** werden alle für die Entität verfügbaren Protokolltypen angezeigt. Für die einzelnen Protokolltypen ist jeweils die Anzahl verfügbarer Datensätze angegeben. Durch Klicken auf einen Protokolltyp gelangen Sie zum Suchbildschirm. Im Suchbildschirm können Sie Ihre Suche eingrenzen und verschiedenen Suchfunktionen verwenden, um beispielsweise Benachrichtigungen festzulegen. In der aktuellen Version ist die Registerkarte „Suchen“ nur für Benutzer- und Computerentitäten verfügbar.

![Suchen,](./media/security-center-investigation/security-center-investigation-fig5.png)

### <a name="exploration"></a>Durchsuchen

Auf der Registerkarte **Durchsuchen** kann der Prüfer Daten in Verbindung mit verschiedenen Problemen untersuchen, die mit der Entität im Zusammenhang stehen. Bei der Untersuchung eines Computers steht auf der Registerkarte „Durchsuchen“ beispielsweise die Liste mit den Prozessen zur Verfügung, die auf dem Computer ausgeführt wurden. Manchmal werden auf der Registerkarte „Durchsuchen“ Daten angezeigt, die auf ein verdächtiges Problem hinweisen. Der Prüfer kann die Daten auf der Registerkarte untersuchen oder sie im Suchbildschirm öffnen, um große Datenmengen zu untersuchen und erweiterte Suchoptionen wie Filter und den Export in Excel zu nutzen.

![Durchsuchen](./media/security-center-investigation/security-center-investigation-fig6.png)

### <a name="timeline"></a>Zeitachse

Die meisten Daten im Diagramm und auf den verschiedenen Registerkarten sind für einen bestimmten Zeitraum relevant. Dieser Zeitbereich wird mithilfe der entsprechenden Auswahl (links oben im Diagramm) festgelegt. Der Zeitbereich kann auf unterschiedliche Weise ausgewählt werden.

![Zeitachse](./media/security-center-investigation/security-center-investigation-fig7.png)

Der Zeitbereich hat Auswirkungen auf Folgendes:

- Beziehung zwischen Benutzer und Computer im Diagramm: Nur Benutzer, die sich innerhalb des Zeitbereichs bei dem Computer angemeldet haben, werden angezeigt.
- Angezeigte Warnungen bei der Untersuchung von Computern und Benutzern: Nur Warnungen innerhalb des Zeitbereichs werden angezeigt.
- Für die Registerkarte „Entitäten“ gilt die gleiche Logik wie für das Diagramm.

Folgende Elemente werden unabhängig vom ausgewählten Zeitbereich angezeigt:

- Wenn eine Warnung angezeigt wird, werden immer alle darin enthaltenen Entitäten (etwa Benutzer und Computer) angezeigt.
- Wenn ein Vorfall eine Entität enthält, wird sie angezeigt.

> [!NOTE]
> Auf den Registerkarten **Suche** und **Durchsuchen** werden nur Datensätze aus dem Zeitbereich angezeigt.

## <a name="how-to-perform-an-investigation"></a>Vorgehensweise für eine Untersuchung

Sie können die Untersuchung abhängig von Ihren Anforderungen entweder über einen Sicherheitsvorfall oder über eine Warnung starten. In den folgenden Schritten wird eine Untersuchung über eine Warnung gestartet:

1.  Öffnen Sie das Dashboard **Security Center**.
2.  Klicken Sie auf **Sicherheitswarnungen**, und wählen Sie den Vorfall aus, den Sie untersuchen möchten.
3.  Klicken Sie auf der Vorfallseite auf die Schaltfläche **Untersuchung starten**. Das Dashboard **Untersuchung** wird angezeigt.

    ![Warnung](./media/security-center-investigation/security-center-investigation-fig8.png)

4. Auf diesem Dashboard können Sie in der Zuordnung die Entität auswählen. Die relevanten Informationen zu dieser Entität werden daraufhin im rechten Bildschirmbereich angezeigt.

    ![Untersuchungsdashboard](./media/security-center-investigation/security-center-investigation-fig9.png)

Von hier aus können Sie die an dem Vorfall beteiligten Entitäten untersuchen und weitere Details zu den einzelnen Entitäten ermitteln.

## <a name="see-also"></a>Weitere Informationen
In diesem Dokument haben Sie erfahren, wie Sie das Untersuchungsfeature in Security Center verwenden. Weitere Informationen zu Security Center finden Sie in den folgenden Quellen:

* [Verwalten von und Reagieren auf Sicherheitswarnungen in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Hier erfahren Sie, wie Sie Warnungen verwalten und auf Sicherheitsvorfälle in Security Center reagieren.
* [Überwachen der Sicherheitsintegrität in Azure Security Center](security-center-monitoring.md). Hier erfahren Sie, wie Sie die Integrität Ihrer Azure-Ressourcen überwachen.
* [Verstehen der Sicherheitswarnungen in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Hier finden Sie Informationen zu den unterschiedlichen Arten von Sicherheitswarnungen.
* [Azure Security Center – Handbuch zur Problembehandlung](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Hier erfahren Sie, wie Sie allgemeine Probleme in Security Center behandeln.
* [Azure Security Center – häufig gestellte Fragen](security-center-faq.md)festgelegt ist. Enthält häufig gestellte Fragen zur Verwendung des Diensts.
* [Azure Security-Blog](https://blogs.msdn.com/b/azuresecurity/). Hier finden Sie Blogbeiträge zur Sicherheit und Compliance von Azure.
