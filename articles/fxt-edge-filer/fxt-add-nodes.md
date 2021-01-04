---
title: 'Tutorial: Hinzufügen von Knoten zu einem Azure FXT Edge Filer-Cluster'
description: Hier erfahren Sie, wie Sie dem Azure FXT Edge Filer-Speichercache Clusterknoten hinzufügen und das Feature für Hochverfügbarkeit (High Availability, HA) aktivieren.
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.openlocfilehash: 85ad78eeb095b427b1a6334f57c351e926022dff
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96021876"
---
# <a name="tutorial-add-cluster-nodes-to-an-azure-fxt-edge-filer-cluster"></a>Tutorial: Hinzufügen von Clusterknoten zu einem Azure FXT Edge Filer-Cluster

Ein neuer Azure FXT Edge Filer-Cluster wird mit nur einem Knoten erstellt. Es empfiehlt sich, vor der weiteren Konfiguration des Clusters noch mindestens zwei Knoten hinzuzufügen und Hochverfügbarkeit zu aktivieren.

In diesem Tutorial wird erläutert, wie Sie Clusterknoten hinzufügen und das Feature für Hochverfügbarkeit (High Availability, HA) aktivieren.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
>
> * Hinzufügen von Knoten zum FXT-Cluster
> * Aktivieren von Hochverfügbarkeit

Es dauert ungefähr 45 Minuten, die Schritte in diesem Tutorial auszuführen.

Schalten Sie die hinzuzufügenden Knoten ein, und [legen Sie ihr anfängliches Kennwort fest](fxt-node-password.md), bevor Sie mit diesem Tutorial beginnen.

## <a name="1-load-the-cluster-nodes-page"></a>1. Laden der Seite „Clusterknoten“

Öffnen Sie die Einstellungen des Clusters in einem Webbrowser, und melden Sie sich als Administrator an. (Ausführliche Informationen hierzu finden Sie im Übersichtsartikel unter [Open the Settings pages](fxt-cluster-create.md#open-the-settings-pages) (Öffnen der Einstellungsseiten).)

In den Einstellungen wird beim Öffnen das **Dashboard** angezeigt. 

![Dashboard in den Einstellungen (erste Registerkarte)](media/fxt-cluster-config/dashboard-1-node.png)

In dieser Abbildung ist das Dashboard eines neu erstellten Clusters mit einem einzelnen Knoten dargestellt.

## <a name="2-locate-the-node-to-add"></a>2. Suchen des hinzuzufügenden Knotens

Klicken Sie zum Hinzufügen von Knoten auf die Registerkarte **Einstellungen**, und wählen Sie im Abschnitt **Cluster** die Seite **FXT-Knoten** aus.

![Registerkarte „Einstellungen“ in den Einstellungen (zweite Registerkarte): „Cluster“ > „FXT-Knoten“](media/fxt-cluster-config/settings-fxt-nodes.png)

Die Liste **FXT Nodes - Unjoined** (FXT-Knoten – nicht verbunden) enthält alle nicht zugewiesenen FXT-Knoten. (In den meisten Rechenzentren gibt es davon nur wenige.) Suchen Sie die FXT-Knoten, die Sie dem Cluster hinzufügen möchten.

> [!Tip]
> Falls Sie den gewünschten Knoten nicht in der Liste **Unjoined** (Nicht verbunden) finden, vergewissern Sie sich, dass er die folgenden Voraussetzungen erfüllt:
>
> * Er ist eingeschaltet, und für ihn wurde ein [Stammkennwort festgelegt](fxt-node-password.md).
> * Er ist mit einem Netzwerk verbunden, auf das Sie Zugriff haben. Bei Verwendung von VLANs muss er sich im selben VLAN wie der Cluster befinden.
> * Er kann mit dem Bonjour-Protokoll erkannt werden.
>
>   Einige Firewalleinstellungen blockieren die von Bonjour verwendeten TCP-/UDP-Ports. Dadurch wird verhindert, dass das FXT-Betriebssystem die Knoten automatisch erkennt.
>
> Ist der Knoten, den Sie hinzufügen möchten, nicht in der Liste enthalten, probieren Sie die folgenden Lösungen aus:
>
> * Klicken Sie auf die Schaltfläche **Manual Discover** (Manuelle Ermittlung), um den Knoten anhand der IP-Adresse zu suchen.
>
> * Weisen Sie manuell vorübergehende IP-Adressen zu. Dieser Fall ist selten, aber unter Umständen erforderlich, wenn Sie markierte VLANs verwenden und sich die Knoten nicht im richtigen Netzwerk befinden oder wenn Ihr Netzwerk keine selbst zugewiesenen IP-Adressen zulässt. Befolgen Sie die Anweisungen in der älteren Version dieses Dokuments, um [eine statische IP-Adresse manuell festzulegen](https://azure.github.io/Avere/legacy/create_cluster/4_8/html/static_ip.html).

Knotenname, IP-Adresse, Softwareversion und Berechtigungsstatus werden in der Liste angezeigt. In der Regel wird in der Spalte **Status** entweder „Wants to join“ (Beitritt angefordert) oder eine Beschreibung eines System- oder Hardwareproblems angezeigt, aufgrund dessen der Knoten nicht zum Cluster hinzugefügt werden kann.

Die Spalte **Aktionen** enthält Schaltflächen, über die Sie den Knoten zum Cluster hinzufügen oder seine Software aktualisieren können. Mit der Schaltfläche zum Aktualisieren wird automatisch die Softwareversion installiert, die mit den bereits im Cluster vorhandenen Knoten übereinstimmt.

Alle Knoten in einem Cluster müssen die gleiche Betriebssystemversion verwenden, Sie müssen Software vor dem Hinzufügen eines Knoten jedoch nicht aktualisieren. Wenn Sie auf die Schaltfläche **Allow To Join** (Beitritt zulassen) klicken, führt der Cluster eine automatische Überprüfung aus und installiert die passende Betriebssystemsoftware für die Version im Cluster.

Weitere Informationen zu den Optionen auf dieser Seite finden Sie in der Anleitung zur Clusterkonfiguration unter [**Cluster** > **FXT Nodes**](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_fxt_nodes.html) (FXT-Knoten).

## <a name="3-click-the-allow-to-join-button"></a>3. Klicken auf die Schaltfläche „Allow to Join“ (Beitritt zulassen)

Klicken Sie für den Knoten, den Sie hinzufügen möchten, in der Spalte *Aktionen** auf die Schaltfläche **Allow To Join** (Beitritt zulassen).

Nachdem Sie auf die Schaltfläche geklickt haben, ändert sich der Status möglicherweise, wenn seine Software als Vorbereitung zum Hinzufügen zum Cluster aktualisiert wird.

Die folgende Abbildung zeigt einen Knoten, der gerade zum Cluster hinzugefügt wird. (Höchstwahrscheinlich wird sein Betriebssystem vor dem Hinzufügen aktualisiert.) In der Spalte **Aktionen** werden keine Schaltflächen für Knoten angezeigt, die dem Cluster gerade hinzugefügt werden.

![Eine Zeile der Knotentabelle mit dem Namen, der IP-Adresse und der Softwareversion des Knotens sowie der Meldung „Allowed to join“ (Beitritt zugelassen) und einer leeren letzten Spalte](media/fxt-cluster-config/node-join-in-process.png)

Nach einigen Augenblicken sollte der neue Knoten in der Liste der Clusterknoten oben auf der Einstellungsseite **FXT-Knoten** angezeigt werden.

Wiederholen Sie diesen Vorgang, um die anderen Knoten zum Cluster hinzuzufügen. Sie müssen nicht warten, bis ein Knoten vollständig zum Cluster hinzugefügt wurde, bevor Sie den Vorgang für einen anderen Knoten starten.

## <a name="enable-high-availability"></a>Aktivieren der Hochverfügbarkeit

Nachdem Sie dem Cluster einen zweiten Knoten hinzugefügt haben, wird unter Umständen eine Warnmeldung auf dem Dashboard in den Einstellungen angezeigt, dass das Feature für Hochverfügbarkeit nicht konfiguriert ist.

Hochverfügbarkeit ermöglicht es den Clusterknoten, sich gegenseitig auszugleichen, wenn ein Knoten ausfällt. Die Hochverfügbarkeit ist nicht standardmäßig aktiviert.

![Registerkarte „Dashboard“ mit der Meldung „The cluster has more than one node, but HA is not enabled ...“ (Der Cluster enthält mehrere Knoten, Hochverfügbarkeit ist jedoch nicht aktiviert.) in der Tabelle „Bedingungen“](media/fxt-cluster-config/no-ha-2-nodes.png)

> [!Note]
> Aktivieren Sie Hochverfügbarkeit erst, wenn der Cluster mindestens drei Knoten enthält.

Gehen Sie zum Aktivieren der Hochverfügbarkeit wie folgt vor:

1. Laden Sie auf der Registerkarte **Einstellungen** im Abschnitt **Cluster** die Seite **Hochverfügbarkeit**.

   ![Seite zum Konfigurieren von Hochverfügbarkeit (Cluster > Hochverfügbarkeit) Das Kontrollkästchen „Enable HA“ (Hochverfügbarkeit aktivieren) befindet sich oben und die Schaltfläche „Übermitteln“ unten auf der Seite.](media/fxt-cluster-config/enable-ha.png)

2. Klicken Sie auf das Kontrollkästchen **Enable HA** (Hochverfügbarkeit aktivieren) und auf die Schaltfläche **Übermitteln**.

Auf dem **Dashboard** wird die Benachrichtigung angezeigt, dass Hochverfügbarkeit aktiviert ist.

![Dashboardtabelle mit der Meldung „HA is now fully configured“ (Hochverfügbarkeit ist nun vollständig konfiguriert.)](media/fxt-cluster-config/ha-configured-alert.png)

## <a name="next-steps"></a>Nächste Schritte

Nach dem Hinzufügen aller Knoten in Ihrem Cluster setzen Sie das Setup fort, indem Sie den langfristigen Speicher des Clusters konfigurieren.

> [!div class="nextstepaction"]
> [Tutorial: Add back-end storage and configure the virtual namespace](fxt-add-storage.md) (Tutorial: Hinzufügen von Back-End-Speicher und Einrichten des virtuellen Namespace)