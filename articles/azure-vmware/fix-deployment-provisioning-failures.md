---
title: Unterstützung für die Bereitstellung von Azure VMware Solution und bei Bereitstellungsfehlern
description: Erfahren Sie, wie Sie die Informationen in Ihrer privaten Azure VMware Solution-Cloud finden, die für das Erstellen einer Supportanfrage zu einer Bereitstellung von Azure VMware Solution und bei Bereitstellungsfehlern erforderlich sind.
ms.topic: how-to
ms.date: 10/28/2020
ms.openlocfilehash: 27b645f4ca225fdd74bca6499b6581b3803e41a4
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94542404"
---
# <a name="open-a-support-request-for-an-azure-vmware-solution-deployment-or-provisioning-failure"></a>Erstellen einer Supportanfrage zu einer Bereitstellung von Azure VMware Solution und bei Bereitstellungsfehlern

In diesem Artikel erfahren Sie, wie Sie eine [Supportanfrage](https://rc.portal.azure.com/#create/Microsoft.Support) erstellen und wichtige Informationen zu einer Bereitstellung von Azure VMware Solution und bei Bereitstellungsfehlern zur Verfügung stellen. 

Wenn es in Ihrer privaten Cloud zu einem Ausfall kommt, müssen Sie im Azure-Portal eine Supportanfrage eröffnen. Um eine Supportanfrage zu eröffnen, beschaffen Sie sich zunächst im Azure-Portal einige wichtige Informationen:

- Korrelations-ID
- ID der Azure ExpressRoute-Leitung
- Fehlermeldungen

## <a name="get-the-correlation-id"></a>Abrufen der Korrelations-ID
 
Wenn Sie in Azure eine private Cloud oder beliebige Ressource erstellen, wird eine Korrelations-ID dafür automatisch generiert. Fügen Sie die Korrelations-ID der privaten Cloud in Ihre Supportanfrage ein, damit diese schneller eröffnet und gelöst werden kann.

Im Azure-Portal können Sie die Korrelations-ID für eine Ressource auf zwei Weisen abrufen:

* Bereich **Übersicht**
* Bereitstellungsprotokolle
 
 ### <a name="get-the-correlation-id-from-the-resource-overview"></a>Abrufen der Korrelations-ID aus der Ressourcenübersicht

Hier ist ein Beispiel für die Vorgangsdetails einer fehlgeschlagenen Bereitstellung einer privaten Cloud mit ausgewählter Korrelations-ID:

:::image type="content" source="media/fix-deployment-provisioning-failures/failed-private-cloud-deployment.png" alt-text="Screenshot einer fehlerhaften Bereitstellung einer privaten Cloud mit ausgewählter Korrelations-ID.":::

So greifen Sie im Bereich **Übersicht** einer privaten Cloud auf Bereitstellungsergebnisse zu

1. Wählen Sie im Azure-Portal Ihre private Cloud aus.

1. Wählen Sie im Menü auf der linken Seite **Übersicht** aus.

Nachdem eine Bereitstellung eingeleitet wurde, werden die Ergebnisse der Bereitstellung im Bereich **Übersicht** der privaten Cloud gezeigt.

Kopieren und speichern Sie die Korrelations-ID der Bereitstellung der privaten Cloud zwecks Einbeziehung in die Supportanfrage.

### <a name="get-the-correlation-id-from-the-deployment-log"></a>Abrufen der Korrelations-ID aus dem Bereitstellungsprotokoll

Sie können die Korrelations-ID einer fehlgeschlagenen Bereitstellung ermitteln, indem Sie im Azure-Portal das Protokoll zur Bereitstellungsaktivität durchsuchen.

So greifen Sie auf das Bereitstellungsprotokoll zu

1. Wählen Sie im Azure-Portal Ihre private Cloud und dann das Benachrichtigungssymbol aus.

   :::image type="content" source="media/fix-deployment-provisioning-failures/open-notifications.png" alt-text="Screenshot mit dem Benachrichtigungssymbol im Azure-Portal.":::

1. Wählen Sie im Bereich **Benachrichtigungen** die Option **Weitere Ereignisse im Aktivitätsprotokoll** aus:

    :::image type="content" source="media/fix-deployment-provisioning-failures/more-events-in-activity-log.png" alt-text="Screenshot des Bereichs „Benachrichtigungen“ mit dem ausgewählten Link „Weitere Ereignisse im Aktivitätsprotokoll“":::

1. Um die fehlgeschlagene Bereitstellung und ihre Korrelations-ID zu finden, suchen Sie nach dem Namen der Ressource oder nach anderen Informationen, die Sie zur Erstellung der Ressource verwendet haben. 

    Im folgenden Beispiel werden die Suchergebnisse für eine private Cloudressource namens pc03 gezeigt.
 
    :::image type="content" source="media/fix-deployment-provisioning-failures/find-past-deployments.png" alt-text="Screenshot der Suchergebnisse für ein Beispiel einer Ressource in einer privaten Cloud und dem Bereich „Private Cloud erstellen oder aktualisieren“.":::
 
1. Wählen Sie in den Suchergebnissen im Bereich **Aktivitätsprotokoll** den Vorgangsnamen der fehlgeschlagenen Bereitstellung aus.

1. Wählen Sie im Bereich **Eine private Cloud erstellen oder aktualisieren** die Registerkarte **JSON** aus, und suchen Sie im eingeblendeten Protokoll nach `correlationId`. Kopieren Sie den Wert von `correlationId` in Ihre Supportanfrage. 
 
## <a name="copy-error-messages"></a>Kopieren von Fehlermeldungen

Um das Bereitstellungsproblem zu lösen, fügen Sie alle Fehlermeldungen ein, die im Azure-Portal gezeigt werden. Wählen Sie eine Warnmeldung aus, um eine Zusammenfassung der Fehler einzusehen:
 
:::image type="content" source="media/fix-deployment-provisioning-failures/summary-of-errors.png" alt-text="Screenshot mit den Fehlerdetails auf der Registerkarte „Zusammenfassung“ im Bereich „Fehler“ mit ausgewähltem Symbol „Kopieren“.":::

Um die Fehlermeldung zu kopieren, wählen Sie das Symbol „Kopieren“ aus. Speichern Sie die kopierte Nachricht, die Ihrer Supportanfrage hinzugefügt werden soll.
 
## <a name="get-the-expressroute-id-uri"></a>Abrufen der ExpressRoute-ID (URI)
 
Angenommen, Sie versuchen, eine Skalierung oder ein Peering für eine vorhandene private Cloud mit der ExpressRoute-Leitung für die private Cloud durchzuführen, was jedoch fehlschlägt. In diesem Szenario müssen Sie Ihrer Supportanfrage die ExpressRoute-ID hinzufügen.

So kopieren Sie die ExpressRoute-ID

1. Wählen Sie im Azure-Portal Ihre private Cloud aus.
1. Wählen Sie im Menü auf der linken Seite unter **Verwalten** die Option **Konnektivität** aus. 
1. Wählen Sie im rechten Bereich die Registerkarte **ExpressRoute** aus.
1. Wählen Sie für **ExpressRoute-ID** das Symbol „Kopieren“ aus, und speichern Sie den Wert, der Ihrer Supportanfrage hinzugefügt werden soll.
 
:::image type="content" source="media/fix-deployment-provisioning-failures/expressroute-id.png" alt-text="Kopieren Sie die ExpressRoute-ID in die Zwischenablage."::: 
 
## <a name="pre-validation-failures"></a>Fehler vor der Überprüfung

Wenn die Überprüfung Ihrer privaten Cloud bereits vor deren Bereitstellung fehlgeschlagen ist, wurde keine Korrelations-ID generiert. In diesem Szenario können Sie Ihrer Supportanfrage die folgenden Informationen hinzufügen:

- Meldungen zu Fehlern und Ausfällen. Diese Melden können in vielerlei Hinsicht hilfreich sein, z. B. bei Problemen mit dem Kontingent. Es ist wichtig, diese Meldungen zu kopieren und der Supportanfrage hinzuzufügen, wie in diesem Artikel beschrieben.
- Informationen, die Sie zur Erstellung der privaten Azure VMware Solution-Cloud verwendet haben, wie z. B.:
  - Standort
  - Resource group
  - Ressourcenname

## <a name="create-your-support-request"></a>Erstellen einer Supportanfrage

Allgemeine Informationen zum Erstellen einer Supportanfrage finden Sie unter [Erstellen einer Azure-Supportanfrage](../azure-portal/supportability/how-to-create-azure-support-request.md). 

So erstellen Sie eine Supportanfrage zu einer Bereitstellung von Azure VMware Solution und bei Bereitstellungsfehlern

1. Wählen Sie im Azure-Portal das Symbol **Hilfe** und dann **Neue Supportanfrage** aus.

    :::image type="content" source="media/fix-deployment-provisioning-failures/open-sr-on-avs.png" alt-text="Screenshot des Bereichs „Neue Supportanfrage“ in Azure-Portal.":::

1. Geben Sie die erforderlichen Informationen ein, oder wählen Sie sie aus:

   1. Gehen Sie auf der Registerkarte **Grundlagen** wie folgt vor:

      1. Wählen Sie bei **Problemtyp** die Option **Configuration and Setup Issues** (Konfigurations- und Setupprobleme) aus.

      1. Wählen Sie bei **Problemuntertyp** die Option **Private Cloud bereitstellen** aus.

   1. Auf der Registerkarte **Details**:

      1. Geben Sie die erforderlichen Informationen ein, oder wählen Sie sie aus.

      1. Fügen Sie Ihre Korrelations-ID oder ExpressRoute-ID dort ein, wo diese Informationen angefordert werden. Wenn Sie kein spezielles Textfeld für diese Werte sehen, fügen Sie sie in das Textfeld **Geben Sie Details zum Problem an** ein.

    1. Fügen Sie sämtliche Fehlerdetails, einschließlich der von Ihnen kopierten Meldungen zu Fehlern und Ausfällen, in das Textfeld **Geben Sie Details zum Problem an** ein.

1. Überprüfen Sie Ihre Eingaben, und wählen Sie dann **Erstellen** aus, um Ihre Supportanfrage zu erstellen.
