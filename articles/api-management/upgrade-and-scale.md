---
title: Aktualisieren und Skalieren einer Azure API Management-Instanz | Microsoft-Dokumentation
description: In diesem Thema wird beschrieben, wie Sie eine Azure API Management-Instanz aktualisieren und skalieren.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 04/20/2020
ms.author: apimpm
ms.openlocfilehash: 466af29a3f073f73d4a0fc62d74709cd59f82425
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96183786"
---
# <a name="upgrade-and-scale-an-azure-api-management-instance"></a>Aktualisieren und Skalieren einer Azure API Management-Instanz  

Kunden können eine Azure API Management-Instanz skalieren, indem sie Einheiten hinzufügen und entfernen. Eine **Einheit** umfasst dedizierte Azure-Ressourcen und verfügt über eine bestimmte Lastkapazität, die als Anzahl von API-Aufrufen pro Monat ausgedrückt wird. Diese stellt nicht die maximale Anzahl von Aufrufen, sondern eher einen maximalen Durchsatzwert für die grobe Kapazitätsplanung dar. Der tatsächliche Durchsatz und die Latenz variieren aufgrund der folgenden Faktoren erheblich: Anzahl und Übertragungsrate von parallelen Verbindungen, Anzahl und Art von konfigurierten Richtlinien, Umfang von Anforderungen und Antworten sowie Back-End-Latenzzeiten.

Die Kapazität und der Preis der einzelnen Einheiten richtet sich nach dem **Tarif** der Einheit. Sie können zwischen vier Tarifen auswählen: **Developer**, **Basic**, **Standard** und **Premium**. Wenn Sie die Kapazität für einen Dienst eines Tarifs erhöhen müssen, sollten Sie eine Einheit hinzufügen. Falls der Tarif, der in Ihrer API Management-Instanz derzeit ausgewählt ist, das Hinzufügen von weiteren Einheiten nicht zulässt, müssen Sie ein Upgrade auf einen höheren Tarif durchführen.

Der Preis jeder Einheit und die verfügbaren Features (z. B. Bereitstellung in mehreren Regionen) richten sich nach dem Tarif, den Sie für Ihre API Management-Instanz gewählt haben. Im Artikel zu den [Preisdetails](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) erfahren Sie, welchen Preis pro Einheit und welche Features Sie im jeweiligen Tarif erhalten. 

>[!NOTE]
>Im Artikel zu den [Preisdetails](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) sind ungefähre Werte für die Einheitenkapazität der einzelnen Tarife angegeben. Um noch genauere Werte zu erhalten, sollten Sie sich ein realistisches Szenario für Ihre APIs ansehen. Weitere Informationen finden Sie im Artikel [Kapazität einer Azure API Management-Instanz](api-management-capacity.md).

## <a name="prerequisites"></a>Voraussetzungen

Damit Sie den Schritten in diesem Artikel folgen können, benötigen Sie folgende Komponenten:

+ Ein aktives Azure-Abonnement

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Eine API Management-Instanz. Weitere Informationen finden Sie unter [Erstellen einer neuen Azure API Management-Dienstinstanz](get-started-create-service-instance.md).

+ Vertrautheit mit dem Konzept der [Kapazität einer Azure API Management-Instanz](api-management-capacity.md)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="upgrade-and-scale"></a>Aktualisieren und Skalieren  

Sie können zwischen vier Tarifen auswählen: **Developer**, **Basic**, **Standard** und **Premium**. Der Tarif **Developer** ist zum Evaluieren des Diensts und nicht zur Verwendung in der Produktion bestimmt. Der Tarif **Developer** verfügt nicht über eine Vereinbarung zum Servicelevel (SLA) und ist nicht skalierbar (Einheiten hinzufügen/entfernen). 

Bei **Basic**, **Standard** und **Premium** handelt es sich um Tarife für die Produktion, die über eine Vereinbarung zum Servicelevel (SLA) verfügen und skaliert werden können. Der Tarif **Basic** ist der kostengünstigste Tarif mit einer SLA, und er kann auf bis zu zwei Einheiten skaliert werden, während der Tarif **Standard** auf bis zu vier Einheiten skaliert werden kann. Im Tarif **Premium** können Sie eine beliebige Anzahl von Einheiten hinzufügen.

Bei Verwendung des Tarifs **Premium** können Sie eine einzelne Azure API Management-Instanz auf eine beliebige Anzahl von Azure-Regionen verteilen. Wenn Sie einen Azure API Management-Dienst erstellen, enthält die Instanz nur eine Einheit und ist nur in einer Azure-Region angeordnet. Diese erste Region wird als **primäre** Region bezeichnet. Zusätzliche Regionen können leicht hinzugefügt werden. Beim Hinzufügen einer Region geben Sie die Anzahl von Einheiten an, die Sie zuordnen möchten. Beispielsweise können Sie eine Einheit in der **primären** Region und fünf Einheiten in einer anderen Region verwenden. Sie können die Anzahl der Einheiten an den Datenverkehr anpassen, die Sie in jeder Region verzeichnen. Weitere Informationen finden Sie unter [Bereitstellen einer Azure API Management-Dienstinstanz für mehrere Azure-Regionen](api-management-howto-deploy-multi-region.md).

Sie können für alle Tarife ein Upgrade oder ein Downgrade durchführen. Beim Upgrade oder Downgrade können ggf. einige Features wegfallen, z. B. VNETs oder die Bereitstellung in mehreren Regionen bei Downgrades vom Premium-Tarif auf den Standard- oder Basic-Tarif.

> [!NOTE]
> Es kann zwischen 15 und 45 Minuten dauern, bis der Upgrade- bzw. Skalierungsprozess abgeschlossen ist. Sie werden nach Abschluss des Prozesses benachrichtigt.

> [!NOTE]
> Der API Management-Dienst im **Verbrauchstarif** wird automatisch basierend auf dem Datenverkehr skaliert.

## <a name="scale-your-api-management-service"></a>Skalieren Ihres API Management-Diensts

![Skalieren des API Management-Diensts im Azure-Portal](./media/upgrade-and-scale/portal-scale.png)

1. Navigieren Sie zu Ihrem API Management-Dienst im [Azure-Portal](https://portal.azure.com/).
2. Wählen Sie im Menü **Standorte** aus.
3. Klicken Sie auf die Zeile mit dem Standort, den Sie skalieren möchten.
4. Geben Sie die Anzahl der **Einheiten** an – verwenden Sie den Schieberegler, oder geben Sie die Anzahl ein.
5. Klicken Sie auf **Anwenden**.

## <a name="change-your-api-management-service-tier"></a>Ändern der Ebene Ihres API Management-Diensts

1. Navigieren Sie zu Ihrem API Management-Dienst im [Azure-Portal](https://portal.azure.com/).
2. Klicken Sie im Menü auf **Tarif**.
3. Wählen Sie im Dropdown die gewünschte Dienstebene aus. Verwenden Sie den Schieberegler, um die Skalierung Ihres API Management-Diensts nach der Änderung anzugeben.
4. Klicken Sie auf **Speichern**.

## <a name="downtime-during-scaling-up-and-down"></a>Ausfallzeiten beim zentralen Hoch- und Herunterskalieren
Wenn Sie die Skalierung von oder auf den Developer-Tarif durchführen, kommt es zu Ausfallzeiten. Andernfalls treten keine Ausfallzeiten auf. 

## <a name="compute-isolation"></a>Computeisolation
Wenn Ihre Sicherheitsanforderungen [Compute-Isolation](../azure-government/azure-secure-isolation-guidance.md#compute-isolation) umfassen, können Sie den Tarif **Isoliert** verwenden. Dieser Tarif stellt sicher, dass Computeressourcen einer API Management-Dienstinstanz den gesamten physischen Host nutzen, und bietet damit die erforderliche Isolationsstufe, die für die Unterstützung von z. B. Workloads gemäß IL5 (Impact Level 5) des US-Verteidigungsministeriums erforderlich ist. Um Zugriff auf den Tarif „Isoliert“ zu erhalten, [erstellen Sie ein Supportticket](../azure-portal/supportability/how-to-create-azure-support-request.md). 



## <a name="next-steps"></a>Nächste Schritte

- [Bereitstellen einer Azure API Management-Dienstinstanz für mehrere Azure-Regionen](api-management-howto-deploy-multi-region.md)
- [Automatisches Skalieren einer Azure API Management-Instanz](api-management-howto-autoscale.md)
- [Ermitteln und Analysieren von Kosten mit der Kostenanalyse](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)