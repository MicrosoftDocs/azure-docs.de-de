---
title: Erstellen von Warnungen für Azure Cosmos DB mithilfe von Azure Monitor
description: Erfahren Sie mehr über das Einrichten von Warnungen für Azure Cosmos DB mithilfe von Azure Monitor.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: how-to
ms.date: 07/16/2020
ms.openlocfilehash: e29db7e31438bc7f6ac609384d0d9b92c275e813
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93339545"
---
# <a name="create-alerts-for-azure-cosmos-db-using-azure-monitor"></a>Erstellen von Warnungen für Azure Cosmos DB mithilfe von Azure Monitor
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Mithilfe von Warnungen werden wiederkehrende Tests zum Überwachen der Verfügbarkeit und Reaktionsfähigkeit Ihrer Azure Cosmos DB-Ressourcen eingerichtet. Durch Warnungen kann Ihnen eine Benachrichtigung in Form einer E-Mail gesendet oder eine Azure-Funktion ausgeführt werden, sobald eine Ihrer Metriken den Schwellenwert erreicht oder ein bestimmtes Ereignis im Aktivitätsprotokoll erfasst wird.

Sie können eine Warnung auf Grundlage der Metriken oder der Aktivitätsprotokollereignisse in Ihrem Azure Cosmos-Konto empfangen:

* **Metriken** : Die Warnung wird ausgelöst, wenn der Wert für eine bestimmte Metrik einen von Ihnen definierten Schwellenwert überschreitet. Wenn z. B. die Gesamtanzahl der verbrauchten Anforderungseinheiten 1000 RU/s übersteigt. Diese Warnung wird sowohl ausgelöst, wenn die Bedingung erstmals erfüllt wird, als auch danach, wenn diese Bedingung nicht mehr erfüllt wird. Informationen zu den verschiedenen Metriken, die in Azure Cosmos DB verfügbar sind, finden Sie im Artikel [Überwachen von Daten – Referenz](monitor-cosmos-db-reference.md#metrics).

* **Aktivitätsprotokollereignisse** : Diese Warnung wird ausgelöst, wenn ein bestimmtes Ereignis eintritt. Wenn beispielsweise auf die Schlüssel Ihres Azure Cosmos-Kontos zugegriffen wird oder diese aktualisiert werden.

Sie können Warnungen über den Azure Cosmos DB-Bereich oder den Azure Monitor-Dienst im Azure-Portal einrichten. Beide Schnittstellen bieten die gleichen Optionen. In diesem Artikel erfahren Sie, wie Sie Warnungen für Azure Cosmos DB mithilfe von Azure Monitor einrichten können.

## <a name="create-an-alert-rule"></a>Erstellen einer Warnungsregel

In diesem Abschnitt wird gezeigt, wie Sie eine Warnung erstellen, wenn Sie den HTTP-Statuscode 429 empfangen, der dann ausgegeben wird, wenn für die Anforderungen eine Ratenbegrenzung besteht. Sie möchten beispielsweise eine Warnung erhalten, wenn 100 oder mehr Anforderungen mit einer Ratenbegrenzung vorhanden sind. In diesem Artikel erfahren Sie, wie Sie mithilfe des HTTP-Statuscodes eine Warnung für ein solches Szenario konfigurieren. Mit ähnlichen Schritten können Sie auch andere Warnungstypen konfigurieren. Dabei müssen Sie lediglich je nach Anforderung eine andere Bedingung auswählen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

1. Wählen Sie in der Navigationsleiste auf der linken Seite die Option **Monitor** und dann **Warnungen** aus.

1. Wählen Sie die Schaltfläche „Neue Warnungsregel“ aus, um den Bereich „Warnungsregel erstellen“ zu öffnen.  

1. Füllen Sie den Abschnitt **Bereich** aus:

   * Öffnen Sie den Bereich **Ressource auswählen** , und konfigurieren Sie Folgendes:

   * Wählen Sie den Namen Ihres **Abonnements** aus.

   * Wählen Sie als **Ressourcentyp** die Option **Azure Cosmos DB-Konten** aus.

   * Den **Speicherort** Ihres Azure Cosmos-Kontos.

   * Nachdem Sie die Details ausgefüllt haben, wird eine Liste der Azure Cosmos-Konten im ausgewählten Bereich angezeigt. Wählen Sie das Konto aus, für das Sie Warnungen konfigurieren möchten, und wählen Sie dann **Fertig** aus.

1. Füllen Sie den Abschnitt **Bedingung** aus:

   * Öffnen Sie den Bereich **Bedingung auswählen** , um die Seite **Signallogik konfigurieren** zu öffnen, und konfigurieren Sie Folgendes:

   * Wählen Sie ein Signal aus. Der **Signaltyp** kann eine **Metrik** oder ein **Aktivitätsprotokoll** sein. Wählen Sie für dieses Szenario die Option **Metriken** aus, da Sie eine Warnung erhalten möchten, wenn Probleme mit der Ratenbegrenzung für die Metrik „Anforderungseinheiten gesamt“ auftreten.

   * Wählen Sie für **Monitor-Dienst** die Option **Alle** aus.

   * Wählen Sie einen **Signalnamen** aus. Um eine Warnung für HTTP-Statuscodes zu erhalten, wählen Sie das Signal **Anforderungseinheiten gesamt** aus.

   * Auf der nächsten Registerkarte können Sie die Logik zum Auslösen einer Warnung definieren und das Diagramm verwenden, um Trends Ihres Azure Cosmos-Kontos anzuzeigen. Die Metrik **Anforderungseinheiten gesamt** unterstützt Dimensionen. Diese Dimensionen ermöglichen Ihnen das Filtern nach der Metrik. Wenn Sie keine Dimension auswählen, wird dieser Wert ignoriert.

   * Wählen Sie als **Dimensionsname** die Option **StatusCode** aus. Wählen Sie **Benutzerdefinierten Wert hinzufügen** aus, und legen Sie den Statuscode auf 429 fest.

   * Legen Sie unter **Warnungslogik** den **Schwellenwert** auf **Statisch** fest. Der statische Schwellenwert verwendet einen benutzerdefinierten Schwellenwert zum Auswerten der Regel, während die dynamischen Schwellenwerte integrierte Machine Learning-Algorithmen verwenden, um fortlaufend Muster im Metrikverhalten zu erlernen und die Schwellenwerte automatisch zu berechnen.

   * Legen Sie den **Operator** auf **Größer als** , den **Aggregationstyp** auf **Gesamt** und den **Schwellenwert** auf **100** fest. Mit dieser Logik wird die Warnung dann ausgelöst, wenn der Client mehr als 100 Anforderungen mit dem Statuscode 429 erkennt. Sie können auch den Aggregationstyp, die Aggregationsgranularität und die Häufigkeit der Auswertung entsprechend Ihren Anforderungen konfigurieren.

   * Nachdem Sie das Formular ausgefüllt haben, wählen Sie **Fertig** aus. Der folgende Screenshot zeigt die Details der Warnungslogik:

     :::image type="content" source="./media/create-alerts/configure-alert-logic.png" alt-text="Konfigurieren der Logik zum Empfangen von Warnungen für Anforderungen mit Ratenbeschränkung/Statuscode 429":::

1. Füllen Sie den Abschnitt **Aktionsgruppe** aus:

   * Wählen Sie im Bereich **Regel erstellen** eine vorhandene Aktionsgruppe aus, oder erstellen Sie eine neue Aktionsgruppe. Mithilfe einer Aktionsgruppe können Sie die Aktion definieren, die erfolgen soll, wenn eine Warnungsbedingung eintritt. Erstellen Sie für dieses Beispiel eine neue Aktionsgruppe, um bei Auslösen der Warnung eine E-Mail-Benachrichtigung zu erhalten. Öffnen Sie den Bereich **Aktionsgruppe hinzufügen** , und füllen Sie die folgenden Details aus:

   * **Aktionsgruppenname** : Der Name der Aktionsgruppe muss innerhalb einer Ressourcengruppe eindeutig sein.

   * **Kurzname** : Der Kurzname der Aktionsgruppe. Dieser Wert ist in E-Mail- und SMS-Benachrichtigungen enthalten, um anzugeben, aus welcher Aktionsgruppe die Benachrichtigung stammt.

   * Wählen Sie das Abonnement und die Ressourcengruppe aus, in der diese Aktionsgruppe erstellt werden soll.  

   * Geben Sie einen Namen für die Aktion an, und wählen Sie als **Aktionstyp** die Option **E-Mail/SMS/Push/Sprachanruf** aus. Der folgende Screenshot zeigt die Details des Aktionstyps:

     :::image type="content" source="./media/create-alerts/configure-alert-action-type.png" alt-text="Konfigurieren des Aktionstyps (z. B. E-Mail-Benachrichtigung) zum Empfangen der Warnung":::

1. Füllen Sie den Abschnitt **Details zur Warnungsregel** aus:

   * Legen Sie einen Namen für die Regel fest, und geben Sie eine optionale Beschreibung und den Schweregrad der Warnung an. Wählen Sie, ob die Regel bei ihrer Erstellung aktiviert werden soll, und wählen Sie dann **Regelwarnung erstellen** aus, um die Metrikregelwarnung zu erstellen.

Nachdem die Warnung erstellt wurde, wird sie innerhalb von zehn Minuten aktiv.

## <a name="common-alerting-scenarios"></a>Häufige Warnungsszenarien

Es folgen einige Szenarien, in denen Sie Warnungen verwenden können:

* Wenn die Schlüssel eines Azure Cosmos-Kontos aktualisiert werden.
* Wenn die Daten- oder Indexnutzung eines Containers, einer Datenbank oder einer Region eine bestimmte Anzahl von Bytes überschreitet.
* Wenn der normalisierte RU/s-Verbrauch höher als ein bestimmter Prozentsatz ist. Die Metrik „Normalisierter RU-Verbrauch“ gibt den maximalen Durchsatzverbrauch innerhalb einer Replikatgruppe an. Weitere Informationen finden Sie im Artikel [Überwachen von normalisierten RU/s](monitor-normalized-request-units.md).  
* Wenn eine Region hinzugefügt, entfernt oder offline geschaltet wird.
* Wenn eine Datenbank oder ein Container erstellt, gelöscht oder aktualisiert wird.
* Wenn der Durchsatz der Datenbank oder des Containers geändert wird.

## <a name="next-steps"></a>Nächste Schritte

* [Überwachen von normalisierten RU/s](monitor-normalized-request-units.md) für einen Azure Cosmos-Container
* [Überwachen der Nutzung des Durchsatzes oder der Anforderungseinheiten](monitor-request-unit-usage.md) für einen Vorgang in Azure Cosmos DB