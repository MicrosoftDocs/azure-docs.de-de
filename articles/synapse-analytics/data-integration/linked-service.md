---
title: Schützen eines verknüpften Diensts
description: Hier erfahren Sie, wie Sie einen verknüpften Dienst per verwaltetem VNET bereitstellen und schützen.
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: fb3c7dd7b3d7e8e42e230eedcd0d1b43af104327
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96452525"
---
# <a name="secure-a-linked-service-with-private-links"></a>Schützen eines verknüpften Diensts mit Private Link

In diesem Artikel erfahren Sie, wie Sie einen verknüpften Dienst in Synapse mit einem privaten Endpunkt schützen.

## <a name="prerequisites"></a>Voraussetzungen

* **Azure-Abonnement**: Wenn Sie über kein Azure-Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
* **Azure Storage-Konto**: Sie verwenden Azure Data Lake Gen 2 als *Quelldatenspeicher*. Wenn Sie kein Speicherkonto haben, finden Sie unter [Erstellen eines Azure Storage-Kontos](../../storage/blobs/data-lake-storage-quickstart-create-account.md) die erforderlichen Schritte zum Erstellen eines solchen Kontos. Stellen Sie sicher, dass es beim Speicherkonto die Synapse Studio-IP-Filterung für den Zugriff darauf gibt und dass Sie nur **ausgewählten Netzwerken** erlauben, auf das Speicherkonto zuzugreifen. Die Einstellung auf dem Blatt **Firewalls und virtuelle Netzwerke** sollte wie in der Abbildung unten aussehen.

![Geschütztes Speicherkonto](./media/secure-storage-account.png)

## <a name="create-a-linked-service-with-private-links"></a>Erstellen eines verknüpften Diensts mit Private Link

In Azure Synapse Analytics definieren Sie in einem verknüpften Dienst Ihre Verbindungsinformationen für andere Dienste. In diesem Abschnitt fügen Sie Azure Synapse Analytics und Azure Data Lake Gen 2 als verknüpfte Dienste hinzu.

1. Öffnen Sie Azure Synapse Studio, und wechseln Sie zur Registerkarte **Verwalten**.
1. Wählen Sie unter **Externe Verbindungen** die Option **Verknüpfte Dienste** aus.
1. Klicken Sie auf **Neu**, um einen verknüpften Dienst hinzuzufügen.
1. Wählen Sie in der Liste die Kachel „Azure Data Lake Storage Gen2“ und dann **Weiter** aus.
1. Aktivieren Sie unbedingt **Interactive Authoring** (Interaktive Dokumenterstellung). Die Aktivierung kann ungefähr 1 Minute dauern. 
1. Geben Sie Ihre Anmeldeinformationen für die Authentifizierung ein. Derzeit werden die Authentifizierungstypen „Kontoschlüssel“, „Dienstprinzipal“ und „Verwaltete Identität“ unterstützt. Klicken Sie auf „Verbindung testen“, um Ihre Anmeldeinformationen zu überprüfen.
1. Wählen Sie **Verbindung testen** aus. Dann sollte ein Verbindungsfehler auftreten, weil ohne die Erstellung und Genehmigung eines privaten Endpunkts nicht auf das Speicherkonto zugegriffen werden kann. In der Fehlermeldung sollte ein Link zum Erstellen eines **privaten Endpunkts** angezeigt werden, dem Sie folgen können, um zum nächsten Teil zu gelangen. Wenn Sie diesem Link folgen, überspringen Sie den nächsten Teil.
1. Wenn Sie fertig sind, klicken Sie auf **Erstellen**.

## <a name="create-a-managed-private-endpoint"></a>Erstellen eines verwalteten privaten Endpunkts

Sollten Sie beim Testen der oben genannten Verbindung nicht auf den Link geklickt haben, folgen Sie dem nachstehenden Pfad. Erstellen Sie einen verwalteten privaten Endpunkt, den Sie mit dem zuvor erstellten verknüpften Dienst verbinden werden.

1. Wechseln Sie zur Registerkarte **Verwalten**.
1. Wechseln Sie zum Abschnitt **Verwaltete virtuelle Netzwerke**.
1. Wählen Sie unter „Verwalteter privater Endpunkt“ **+ Neu** aus.
1. Wählen Sie in der Liste die Kachel „Azure Data Lake Storage Gen2“ und dann **Weiter** aus.
1. Geben Sie den Namen des zuvor erstellten Speicherkontos ein.
1. Klicken Sie auf **Erstellen**
1. Nach einigen Sekunden Wartezeit sollten Sie informiert werden, dass für den erstellten privaten Link eine Genehmigung erforderlich ist.

## <a name="private-link-approval"></a>Private Link-Genehmigung
1. Wählen Sie den zuvor erstellten privaten Endpunkt aus. Es wird ein Link angezeigt, über den Sie den privaten Endpunkt auf Speicherkontoebene genehmigen können. *Eine Alternative besteht darin, direkt zum Speicherkonto im Azure-Portal und dann zum Blatt **Private Endpunktverbindungen** zu wechseln.*
1. Aktivieren Sie den privaten Endpunkt, den Sie in Studio erstellt haben, und wählen Sie **Genehmigen** aus.
1. Fügen Sie eine Beschreibung hinzu, und wählen Sie **Ja** aus.
1. Wechseln Sie auf der Registerkarte **Verwalten** unter dem Abschnitt **Verwaltete virtuelle Netzwerke** zurück zu Synapse Studio.
1. Es sollte ungefähr 1 Minute dauern, bis die Genehmigung für Ihren privaten Endpunkt angezeigt wird.

## <a name="check-the-connection-works"></a>Überprüfen, ob die Verbindung funktioniert
1. Wechseln Sie zur Registerkarte **Verwalten**, und wählen Sie den erstellten verknüpften Dienst aus.
1. Vergewissern Sie sich, dass **Interactive Authoring** (Interaktive Dokumenterstellung) aktiviert ist.
1. Klicken Sie auf **Verbindung testen**. Es sollte angezeigt werden, dass die Verbindung erfolgreich hergestellt wurde.

Jetzt haben Sie eine sichere und private Verbindung zwischen Synapse und Ihrem verknüpften Dienst hergestellt!

## <a name="next-steps"></a>Nächste Schritte


Lesen Sie den Artikel [Verwaltete private Endpunkte](../security/synapse-workspace-managed-private-endpoints.md), um ein noch besseres Verständnis für verwaltete private Endpunkte in Azure Synapse Analytics zu entwickeln.


Weitere Informationen zur Datenintegration bei Azure Synapse Analytics finden Sie im Artikel [Erfassen von Daten in einem Data Lake](data-integration-data-lake.md).
