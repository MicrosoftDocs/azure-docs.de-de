---
title: Konfigurieren der automatischen Skalierung einer Azure API Management-Instanz | Microsoft-Dokumentation
description: In diesem Thema wird die Einrichtung der automatischen Skalierung für eine Azure API Management-Instanz beschrieben.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 06/20/2018
ms.author: apimpm
ms.openlocfilehash: 41a97c639d9148f2bff3e4f530363ea05a65a7b8
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96018245"
---
# <a name="automatically-scale-an-azure-api-management-instance"></a>Automatisches Skalieren einer Azure API Management-Instanz  

Eine Azure API Management-Dienstinstanz kann basierend auf einer Reihe von Regeln automatisch skaliert werden. Dieses Verhalten kann über Azure Monitor aktiviert und konfiguriert werden und wird nur in den Tarifen **Standard** und **Premium** des Azure API Management-Diensts unterstützt.

In diesem Artikel wird der Prozess des Konfigurierens der automatischen Skalierung erläutert und die optimale Konfiguration der Regeln zur automatischen Skalierung empfohlen.

> [!NOTE]
> Der API Management-Dienst im **Verbrauchstarif** wird automatisch basierend auf dem Datenverkehr skaliert, ohne dass eine zusätzliche Konfiguration erforderlich wäre.

## <a name="prerequisites"></a>Voraussetzungen

Damit Sie den Schritten in diesem Artikel folgen können, benötigen Sie folgende Komponenten:

+ Ein aktives Azure-Abonnement
+ Eine Azure API Management-Instanz Weitere Informationen finden Sie unter [Erstellen einer neuen Azure API Management-Dienstinstanz](get-started-create-service-instance.md).
+ Vertrautheit mit dem Konzept der [Kapazität einer Azure API Management-Instanz](api-management-capacity.md)
+ Vertrautheit mit dem [Prozess der manuellen Skalierung einer Azure API Management-Instanz](upgrade-and-scale.md), einschließlich den sich ergebenden Kosten

[!INCLUDE [premium-standard.md](../../includes/api-management-availability-premium-standard.md)]

## <a name="azure-api-management-autoscale-limitations"></a>Einschränkungen im Hinblick auf die automatische Skalierung von Azure API Management

Vor dem Konfigurieren des Verhaltens der automatischen Skalierung müssen bestimmte Einschränkungen sowie Auswirkungen der Skalierung beachtet werden.

+ Die automatische Skalierung kann nur für die Tarife **Standard** und **Premium** des Azure API Management-Diensts aktiviert werden.
+ Durch die Tarife wird außerdem die maximale Anzahl der Einheiten für eine Dienstinstanz vorgegeben.
+ Der Skalierungsprozess dauert mindestens 20 Minuten.
+ Wenn der Dienst durch einen anderen Vorgang gesperrt ist, treten bei der Skalierungsanforderung Fehler auf, und diese wird automatisch wiederholt.
+ Bei einem Dienst mit Bereitstellungen in mehreren Regionen können nur Einheiten im **primären Standort** skaliert werden. Einheiten in anderen Standorten können dagegen nicht skaliert werden.

## <a name="enable-and-configure-autoscale-for-azure-api-management-service"></a>Aktivieren und Konfigurieren der automatischen Skalierung für einen Azure API Management-Dienst

Führen Sie zum Konfigurieren der automatischen Skalierung für einen Azure API Management-Dienst die folgenden Schritte aus:

1. Navigieren Sie im Azure-Portal zu der **Monitor**-Instanz.

    ![Azure Monitor](media/api-management-howto-autoscale/01.png)

2. Wählen Sie im Menü links die Option **Automatisch skalieren** aus.

    ![Azure Monitor, Ressource für automatische Skalierung](media/api-management-howto-autoscale/02.png)

3. Suchen Sie mit den Filtern in den Dropdownmenüs Ihren Azure API Management-Dienst.
4. Wählen Sie die gewünschte Azure API Management-Dienstinstanz aus.
5. Klicken Sie im neu geöffneten Abschnitt auf die Schaltfläche **Automatische Skalierung aktivieren**.

    ![Azure Monitor, Aktivieren der automatischen Skalierung](media/api-management-howto-autoscale/03.png)

6. Klicken Sie im Abschnitt **Regeln** auf **+ Regel hinzufügen**.

    ![Azure Monitor, Hinzufügen einer Regel für die automatische Skalierung](media/api-management-howto-autoscale/04.png)

7. Definieren Sie eine neue Regel zum Aufskalieren.

   Mit einer Regel zum Aufskalieren kann beispielsweise das Hinzufügen einer Azure API Management-Einheit ausgelöst werden, wenn die durchschnittliche Kapazitätsmetrik in den letzten 30 Minuten 80 % überschreitet. Die folgende Tabelle enthält Angaben zur Konfiguration für eine solche Regel.

    | Parameter             | Wert             | Notizen                                                                                                                                                                                                                                                                           |
    |-----------------------|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Metrikquelle         | Aktuelle Ressource  | Definieren Sie die Regel basierend auf der Metrik der aktuellen Azure API Management-Ressource.                                                                                                                                                                                                     |
    | *Kriterien*            |                   |                                                                                                                                                                                                                                                                                 |
    | Zeitaggregation      | Average           |                                                                                                                                                                                                                                                                                 |
    | Metrikname           | Capacity          | Die Kapazitätsmetrik ist eine Azure API Management-Metrik, die die Verwendung der Ressourcen einer Azure API Management-Instanz angibt.                                                                                                                                                            |
    | Statistik zum Aggregationsintervall  | Average           |                                                                                                                                                                                                                                                                                 |
    | Operator              | Größer als      |                                                                                                                                                                                                                                                                                 |
    | Schwellenwert             | 80 %               | Der Schwellenwert für die durchschnittliche Kapazitätsmetrik.                                                                                                                                                                                                                                 |
    | Dauer (in Minuten) | 30                | Der Zeitraum zum Ermitteln des Durchschnitts der Kapazitätsmetrik ist spezifisch für Verwendungsmuster. Je länger der Zeitraum, desto gleichmäßiger die Reaktion – zeitweilige Spitzen haben weniger Auswirkungen auf die Festlegung für die horizontale Skalierung. Dennoch wird auch der Trigger für die horizontale Skalierung verzögert. |
    | *Aktion*              |                   |                                                                                                                                                                                                                                                                                 |
    | Vorgang             | Anzahl erhöhen um |                                                                                                                                                                                                                                                                                 |
    | Anzahl von Instanzen        | 1                 | Skalieren Sie die Azure API Management-Dienstinstanz um 1 Einheit auf.                                                                                                                                                                                                                          |
    | Abkühlen (Minuten)   | 60                | Die Aufskalierung des Azure API Management-Diensts dauert mindestens 20 Minuten. In den meisten Fällen wird durch einen Abkühlzeitraum von 60 Minuten vermieden, dass viele horizontale Skalierungen ausgelöst werden.                                                                                                  |

8. Klicken Sie auf **Hinzufügen** , um die Regel zu speichern.

    ![Azure Monitor, Regel zum Aufskalieren](media/api-management-howto-autoscale/05.png)

9. Klicken Sie erneut auf **+ Regel hinzufügen**.

    Nun muss eine Regel zum Abskalieren definiert werden. Mit dieser Regel wird sichergestellt, dass Ressourcen nicht vergeudet werden, wenn die Verwendung von APIs verringert wird.

10. Definieren Sie eine neue Regel zum Abskalieren.

    Mit einer Regel zum Abskalieren kann beispielsweise das Entfernen einer Azure API Management-Einheit ausgelöst werden, wenn die durchschnittliche Kapazitätsmetrik in den letzten 30 Minuten unter 35 % liegt. Die folgende Tabelle enthält Angaben zur Konfiguration für eine solche Regel.

    | Parameter             | Wert             | Notizen                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
    |-----------------------|-------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Metrikquelle         | Aktuelle Ressource  | Definieren Sie die Regel basierend auf der Metrik der aktuellen Azure API Management-Ressource.                                                                                                                                                                                                                                                                                                                                                                                                                         |
    | *Kriterien*            |                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Zeitaggregation      | Average           |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Metrikname           | Capacity          | Gleiche Metrik wie die für die Regel zum Aufskalieren.                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
    | Statistik zum Aggregationsintervall  | Average           |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Operator              | Kleiner als         |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Schwellenwert             | 35 %               | Ähnlich wie bei der Regel zum Aufskalieren hängt dieser Wert stark von den Verwendungsmustern von Azure API Management ab. |
    | Dauer (in Minuten) | 30                | Gleicher Wert wie der für die Regel zum Aufskalieren.                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
    | *Aktion*              |                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Vorgang             | Anzahl verringern um | Gegensätzlicher Wert zum für die Regel zum Aufskalieren verwendeten Wert.                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
    | Anzahl von Instanzen        | 1                 | Gleicher Wert wie der für die Regel zum Aufskalieren.                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
    | Abkühlen (Minuten)   | 90                | Das Abskalieren sollte konservativer als das Aufskalieren sein, daher sollte der Abkühlzeitraum länger sein.                                                                                                                                                                                                                                                                                                                                                                                                    |

11. Klicken Sie auf **Hinzufügen** , um die Regel zu speichern.

    ![Azure Monitor, Regel zum Abskalieren](media/api-management-howto-autoscale/06.png)

12. Legen Sie die **maximale** Anzahl von Azure API Management-Einheiten fest.

    > [!NOTE]
    > In Azure API Management ist die Anzahl der Einheiten, um die eine Instanz aufskaliert werden kann, beschränkt. Das Limit hängt von der jeweiligen Dienstebene ab.

    ![Screenshot mit Hervorhebung, wo Sie die maximale Anzahl von Azure API Management-Einheiten festlegen](media/api-management-howto-autoscale/07.png)

13. Klicken Sie auf **Speichern**. Die automatische Skalierung wurde damit konfiguriert.

## <a name="next-steps"></a>Nächste Schritte

- [Bereitstellen einer Azure API Management-Dienstinstanz für mehrere Azure-Regionen](api-management-howto-deploy-multi-region.md)
- [Ermitteln und Analysieren von Kosten mit der Kostenanalyse](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)