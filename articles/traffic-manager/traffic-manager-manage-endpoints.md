---
title: Verwalten von Endpunkten in Azure Traffic Manager | Microsoft Docs
description: Dieser Artikel enthält Informationen zum Hinzufügen, Entfernen, Aktivieren und Deaktivieren von Endpunkten in Azure Traffic Manager.
services: traffic-manager
documentationcenter: ''
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/08/2017
ms.author: duau
ms.openlocfilehash: fc6e8bcda17f9d788a430ed763bd9f8702eaf250
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96003547"
---
# <a name="add-disable-enable-or-delete-endpoints"></a>Hinzufügen, Deaktivieren, Aktivieren oder Löschen von Endpunkten

Das Web-Apps-Feature in Azure App Service stellt bereits unabhängig vom Websitemodus Failover- und Roundrobin-Funktionen für das Routing von Datenverkehr für Websites in einem Datencenter zur Verfügung. Azure Traffic Manager ermöglicht das Angeben des Failover- und Roundrobin-Routings von Datenverkehr für Websites und Clouddienste in verschiedenen Datencentern. Der erste erforderliche Schritt zum Bereitstellen dieser Funktionalität ist das Hinzufügen die Funktionen des Cloud-Dienst- oder Website-Endpunkts zu Traffic Manager.

Sie können auch einzelne Endpunkte deaktivieren, die Teil eines Traffic Manager-Profils sind. Bei Deaktivieren eines Endpunkts bleibt er Teil des Profils, doch das Profil verhält sich so, als wäre der Endpunkt nicht vorhanden. Diese Aktion ist nützlich zum zeitweiligen Entfernen eines Endpunkts, der sich im Wartungsmodus befindet oder erneut bereitgestellt werden soll. Sobald der Endpunkt wieder betriebsbereit ist, kann er aktiviert werden.

> [!NOTE]
> Das Deaktivieren eines gehosteten Diensts hat nichts mit dessen Bereitstellungsstatus in Azure zu tun. Ein fehlerfrei funktionierender Endpunkt bleibt in Betrieb und kann Datenverkehr empfangen, selbst wenn er in Traffic Manager deaktiviert ist. Das Deaktivieren eines Endpunkts in einem Profil wirkt sich darüber hinaus nicht auf seinen Status in einem anderen Profil aus.

## <a name="to-add-a-cloud-service-or-an-app-service-endpoint-to-a-traffic-manager-profile"></a>So fügen Sie einem Traffic Manager-Profil einen Clouddienst- oder App-Dienstendpunkt hinzu

1. Melden Sie sich im Browser beim [Azure-Portal](https://portal.azure.com) an.
2. Suchen Sie über die Suchleiste des Portals nach dem Namen des **Traffic Manager-Profils**, das Sie ändern möchten, und klicken Sie in den angezeigten Ergebnissen auf das Traffic Manager-Profil.
3. Klicken Sie auf dem Blatt **Traffic Manager-Profil** im Abschnitt **Einstellungen** auf **Endpunkte**.
4. Klicken Sie auf dem dann angezeigten Blatt **Endpunkte** auf **Hinzufügen**.
5. Füllen Sie das Blatt **Endpunkt hinzufügen** wie folgt aus:
    1. Klicken Sie als **Typ** auf **Azure-Endpunkt**.
    2. Geben Sie einen **Namen** an, den Sie zur Bezeichnung des Endpunkts verwenden möchten.
    3. Wählen Sie als **Zielressourcentyp** in der Dropdownliste den entsprechenden Ressourcentyp aus.
    4. Klicken Sie für **Zielressource** auf den Selektor **Auswählen...** , um Ressourcen unter dem gleichen Abonnement auf dem Blatt **Ressourcen** aufzulisten. Wählen Sie auf dem angezeigten Blatt **Ressource** den Dienst aus, den Sie als ersten Endpunkt hinzufügen möchten.
    5. Legen Sie als **Priorität** den Wert **1** fest. Dies bewirkt, dass der gesamte Datenverkehr an diesen Endpunkt geleitet wird, sofern sein Status intakt ist.
    6. Lassen Sie **Als deaktiviert hinzufügen** deaktiviert.
    7. Klicken Sie auf **OK**
6.  Wiederholen Sie Schritte 4 und 5, um den nächsten Azure-Endpunkt hinzuzufügen. Achten Sie darauf, ihn mit einem auf **2** festgelegten Wert für **Priorität** hinzuzufügen.
7.  Wenn Sie das Hinzufügen beider Endpunkte abgeschlossen haben, werden diese auf dem Blatt **Traffic Manager-Profil** zusammen mit ihrem Überwachungsstatus als **Online** angezeigt.

> [!NOTE]
> Nachdem Sie in einem Profil einen Endpunkt mit der Datenverkehr-Routingmethode *Failover* hinzugefügt oder entfernt haben, ist die Failoverprioritätsliste ggf. nicht mehr wie gewünscht sortiert. Sie können die Reihenfolge der Failoverprioritätsliste auf der Seite „Konfiguration“ anpassen. Weitere Informationen finden Sie unter [Konfigurieren des Routings für Failoverdatenverkehr](traffic-manager-configure-failover-routing-method.md).

## <a name="to-disable-an-endpoint"></a>So deaktivieren Sie einen Endpunkt

1. Melden Sie sich im Browser beim [Azure-Portal](https://portal.azure.com) an.
2. Suchen Sie auf der Suchleiste des Portals nach dem Namen des **Traffic Manager-Profils**, das Sie ändern möchten, und klicken Sie in den angezeigten Ergebnissen auf das Traffic Manager-Profil.
3. Klicken Sie auf dem Blatt **Traffic Manager-Profil** im Abschnitt **Einstellungen** auf **Endpunkte**. 
4. Klicken Sie auf den Endpunkt, den Sie deaktivieren möchten.
5. Ändern Sie auf dem Blatt **Endpunkt** den Endpunktstatus in **Deaktiviert**, und klicken Sie dann auf **Speichern**.
6. Clients senden während der Gültigkeitsdauer (Time-to-Live, TTL) weiterhin Datenverkehr an den Endpunkt. Sie können die Gültigkeitsdauer auf der Seite „Konfiguration“ des Traffic Manager-Profils ändern.

## <a name="to-enable-an-endpoint"></a>So aktivieren Sie einen Endpunkt

1. Melden Sie sich im Browser beim [Azure-Portal](https://portal.azure.com) an.
2. Suchen Sie auf der Suchleiste des Portals nach dem Namen des **Traffic Manager-Profils**, das Sie ändern möchten, und klicken Sie in den angezeigten Ergebnissen auf das Traffic Manager-Profil.
3. Klicken Sie auf dem Blatt **Traffic Manager-Profil** im Abschnitt **Einstellungen** auf **Endpunkte**. 
4. Klicken Sie auf den Endpunkt, den Sie aktivieren möchten.
5. Ändern Sie auf dem Blatt **Endpunkt** den Endpunktstatus in **Aktiviert**, und klicken Sie dann auf **Speichern**.
6. Clients senden während der Gültigkeitsdauer (Time-to-Live, TTL) weiterhin Datenverkehr an den Endpunkt. Sie können die Gültigkeitsdauer auf der Seite „Konfiguration“ des Traffic Manager-Profils ändern.

## <a name="to-delete-an-endpoint"></a>So löschen Sie einen Endpunkt

1. Melden Sie sich im Browser beim [Azure-Portal](https://portal.azure.com) an.
2. Suchen Sie auf der Suchleiste des Portals nach dem Namen des **Traffic Manager-Profils**, das Sie ändern möchten, und klicken Sie in den angezeigten Ergebnissen auf das Traffic Manager-Profil.
3. Klicken Sie auf dem Blatt **Traffic Manager-Profil** im Abschnitt **Einstellungen** auf **Endpunkte**. 
4. Klicken Sie auf den Endpunkt, den Sie löschen möchten.
5. Klicken Sie auf dem Blatt **Endpunkt** auf **Löschen**.


## <a name="next-steps"></a>Nächste Schritte

* [Verwalten von Traffic Manager-Profilen](traffic-manager-manage-profiles.md)
* [Konfigurieren von Routingmethoden](traffic-manager-configure-routing-method.md)
* [Problembehandlung beim Status „Heruntergestuft“ in Azure Traffic Manager](traffic-manager-troubleshooting-degraded.md)
* [Leistungsüberlegungen zu Traffic Manager](traffic-manager-performance-considerations.md)
* [Vorgänge für Traffic Manager (REST-API-Referenz)](https://go.microsoft.com/fwlink/p/?LinkID=313584)

