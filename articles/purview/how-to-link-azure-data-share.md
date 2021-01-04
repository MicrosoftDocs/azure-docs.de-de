---
title: Herstellen einer Verbindung mit Azure Data Share
description: In diesem Artikel wird beschrieben, wie Sie ein Azure Data Share-Konto mit Azure Purview verbinden, um Ressourcen zu suchen und die Datenherkunft nachzuverfolgen.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/25/2020
ms.openlocfilehash: edea881d67d5a677339c6ff357c1ac45f5dfd420
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96551059"
---
# <a name="how-to-connect-azure-data-share-and-azure-purview"></a>Verbinden von Azure Data Share und Azure Purview

In diesem Artikel wird erläutert, wie Sie Ihr [Azure Data Share](../data-share/overview.md)-Konto mit Azure Purview verbinden und die freigegebenen Datasets (aus- und eingehend) in Ihrem Datenbestand verwalten. Verschiedene Personas zur Datengovernance können die Datenherkunft über Grenzen wie Organisationen, Abteilungen und sogar Rechenzentren hinweg ermitteln und nachverfolgen.

## <a name="common-scenarios"></a>Häufige Szenarios

Die Datenherkunft bei Data Share soll ausführliche Informationen für die Grundursachen- und Auswirkungsanalyse bereitstellen.

### <a name="scenario-1-360-view-of-datasets-shared-inout-for-a-partner-organization-or-internal-department"></a>Szenario 1: Umfassende Ansicht der ein- und ausgehenden Datasets, die für eine Partnerorganisation oder eine interne Abteilung freigegeben wurden

Datenverantwortliche können eine Liste aller Datasets anzeigen, die bidirektional für Partnerorganisationen freigegeben wurden. Sie können die Datasets nach dem Organisationsnamen durchsuchen und eine vollständige Ansicht aller aus- und eingehenden Freigaben anzeigen.

### <a name="scenario-2-root-cause-analysis---upstream-dependency-on-datasets-coming-into-organization-consumer-view-of-incoming-shares"></a>Szenario 2: Grundursachenanalyse – Upstreamabhängigkeit von in Ihre Organisation eingehenden Datasets (Consumeransicht eingehender Freigaben)

Ein Bericht enthält aufgrund von Problemen mit Upstreamdaten falsche Informationen zu einem externen Data Share-Konto. Die Datentechniker können die Upstreamfehler analysieren, sich über die Gründe informieren und den Besitzer der Freigabe kontaktieren, um die Probleme zu beheben, die zu den Datenabweichungen geführt haben.

### <a name="scenario-3-impact-analysis-on-datasets-going-outside-organization-provider-view-of-outgoing-shares"></a>Szenario 3: Auswirkungsanalyse für aus Ihrer Organisation ausgehende Datasets (Anbieteransicht ausgehender Freigaben)

Datenproducer möchten wissen, auf wen Änderungen an den Datasets Auswirkungen haben. Mithilfe der Datenherkunft kann ein Datenproducer ganz einfach die Auswirkungen auf nachgelagerte interne oder externe Partner erkennen, die seine Daten mit Azure Data Share nutzen.

## <a name="azure-data-share-and-purview-connected-experience"></a>Azure Data Share und Purview im Verbund

Gehen Sie folgendermaßen vor, um Ihre Azure Data Share-Instanz und Ihr Azure Purview-Konto miteinander zu verbinden:

1. Erstellen Sie ein Purview-Konto. Alle Herkunftsinformationen aus Data Share werden von einem Purview-Konto gesammelt. Sie können ein vorhandenes Purview-Konto auswählen oder ein neues erstellen.

1. Verbinden Sie Ihre Azure Data Share-Instanz mit Ihrem Purview-Konto.

    1. Navigieren Sie im Purview-Portal zum **Verwaltungscenter**, und verbinden Sie Ihre Azure Data Share-Instanz im Abschnitt **Externe Verbindungen**.
    1. Wählen Sie auf der Leiste oben **+ Neu** aus, suchen Sie Ihre Azure Data Share-Instanz auf der Popup-Seitenleiste, und fügen Sie das Data Share-Konto hinzu. Führen Sie nach dem Verbinden von Data Share mit Ihrem Purview-Konto einen Momentaufnahmeauftrag aus, damit die Data Share-Ressourcen und die Herkunftsinformationen in Purview angezeigt werden.

       :::image type="content" source="media/how-to-link-azure-data-share/connect-to-data-share.png" alt-text="Verwaltungscenter zum Verknüpfen mit Azure Data Share":::

1. Führen Sie die Momentaufnahme in Azure Data Share aus.

    - Nachdem die Azure Data Share-Verbindung mit Azure Purview hergestellt wurde, können Sie eine Momentaufnahme für Ihre vorhandenen Freigaben ausführen. 
    - Wenn Sie nicht über Freigaben verfügen, wechseln Sie zum Azure Data Share-Portal, um [Ihre Daten freizugeben](../data-share/share-your-data.md) und [eine Datenfreigabe zu abonnieren](../data-share/subscribe-to-data-share.md).
    - Nachdem die Momentaufnahme der Freigabe fertiggestellt wurde, können Sie zugehörige Data Share-Ressourcen und -Herkunftsdaten in Purview anzeigen.

1. Ermitteln Sie Data Share-Konten, und geben Sie Informationen in Ihrem Purview-Konto frei.

    - Wählen Sie auf der Startseite des Purview-Kontos **Nach Ressourcentyp durchsuchen** und dann die Kachel **Azure Data Share** aus. Sie können nach einem Kontonamen, einem Freigabenamen, einer Freigabemomentaufnahme oder einer Partnerorganisation suchen. Wenden Sie andernfalls auf der Suchergebnisseite Filter für den Kontonamen oder den Freigabetyp (gesendete oder empfangene Freigaben) an.

       :::image type="content" source="media/how-to-link-azure-data-share/azure-data-share-search-result-page.png" alt-text="Azure Data Share auf der Suchergebnisseite":::

    >[!Important]
    >Damit Data Share-Ressourcen in Purview angezeigt werden können, muss nach dem Verbinden Ihrer Data Share-Instanz mit Purview ein Momentaufnahmeauftrag ausgeführt werden.

1. Verfolgen Sie die Herkunftsdaten von Datasets nach, die mit Azure Data Share freigegeben wurden.

    - Wählen Sie auf der Suchergebnisseite von Purview die Momentaufnahme der Datenfreigabe (empfangen/gesendet) aus. Wählen Sie anschließend die Registerkarte **Herkunft** aus, um ein Herkunftsdiagramm mit Upstream- und Downstreamabhängigkeiten anzuzeigen.

    :::image type="content" source="media/how-to-link-azure-data-share/azure-data-share-lineage.png" alt-text="Herkunftsdaten von Datasets, die mit Azure Data Share freigegeben wurden":::

## <a name="next-steps"></a>Nächste Schritte

- [Benutzerhandbuch zur Katalogherkunft](catalog-lineage-user-guide.md)
- [Link zu Azure Data Factory für Herkunft](how-to-link-azure-data-factory.md)
