---
title: Steuerung von HANA in Azure (große Instanzen) über das Azure-Portal | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie Sie sich über das Portal bei HANA in Azure (große Instanzen) identifizieren und damit interagieren können.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/15/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 550e22ac861b92994f2695594d09fc2935d273d1
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94967753"
---
# <a name="azure-hana-large-instances-control-through-azure-portal"></a>Steuerung von HANA in Azure (große Instanzen) über das Azure-Portal
Dieses Dokument beschreibt, wie [HANA (große Instanzen)](./hana-overview-architecture.md) im [Azure-Portal](https://portal.azure.com) dargestellt wird und welche Aktivitäten Sie über das Azure-Portal mit den für Sie bereitgestellten Einheiten von HANA (große Instanzen) ausführen können. Die Sichtbarkeit von HANA (große Instanzen) im Azure-Portal wird über einen Azure-Ressourcenanbieter für HANA (große Instanzen) bereitgestellt. Diese Funktion befindet sich derzeit in der öffentlichen Vorschau.

## <a name="register-hana-large-instance-resource-provider"></a>Registrieren des Ressourcenanbieters für HANA (große Instanzen)
In Ihrem Azure-Abonnement, das Sie für Bereitstellungen von HANA (große Instanzen) verwendet haben, ist der Ressourcenanbieter für HANA (große Instanzen) in der Regel registriert. Wenn jedoch keine bereitgestellten HANA-Einheiten (große Instanzen) angezeigt werden, sollten Sie den Ressourcenanbieter in Ihrem Azure-Abonnement registrieren. Für die Registrierung des Ressourcenanbieters für HANA (große Instanzen) stehen zwei Möglichkeiten zur Verfügung.

### <a name="register-through-cli-interface"></a>Registrierung über die Befehlszeilenschnittstelle
Sie müssen über die Azure-Befehlszeilenschnittstelle bei dem Azure-Abonnement angemeldet sein, das für die Bereitstellung von HANA (große Instanzen) verwendet wird. Sie können den Ressourcenanbieter für HANA (große Instanzen) mit dem folgenden Befehl registrieren oder erneut registrieren:
    
```azurecli
az provider register --namespace Microsoft.HanaOnAzure
```

Weitere Informationen finden Sie im Artikel [Ressourcenanbieter und -typen](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-cli).


### <a name="register-through-azure-portal"></a>Registrierung über das Azure-Portal
Sie können den Ressourcenanbieter für HANA (große Instanzen) über das Azure-Portal registrieren oder erneut registrieren. Sie müssen Ihr Abonnement, das für die Bereitstellung der HANA-Einheiten (große Instanzen) verwendet wurde, im Azure-Portal anzeigen und darauf doppelklicken. Wählen Sie wie unten gezeigt auf der Übersichtsseite Ihres Abonnements die Option „Ressourcenanbieter“ aus, und geben Sie „HANA“ im Suchfenster ein. 

![Registrieren des Ressourcenanbieters für HANA (große Instanzen) über das Azure-Portal](./media/hana-li-portal/portal-register-hli-rp.png)

Im abgebildeten Screenshot wurde der Ressourcenanbieter bereits registriert. Für den Fall, dass der Ressourcenanbieter noch nicht registriert ist, klicken Sie auf „Erneut registrieren“ oder „Registrieren“.

Weitere Informationen finden Sie im Artikel [Ressourcenanbieter und -typen](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-powershell).


## <a name="display-of-hana-large-instance-units-in-the-azure-portal"></a>Anzeige der Einheiten von HANA (große Instanzen) im Azure-Portal
Wenn Sie eine Bereitstellungsanforderung für HANA (große Instanzen) übermitteln, werden Sie aufgefordert, das Azure-Abonnement anzugeben, das mit HANA (große Instanzen) verknüpft werden soll. Es wird empfohlen, dasselbe Abonnement zu verwenden, das Sie zum Bereitstellen der SAP-Anwendungsschicht einsetzen, die den Einheiten von HANA (große Instanzen) entgegenwirkt.
Bei der ersten Bereitstellung von HANA (große Instanzen) wird eine neue [Azure-Ressourcengruppe](../../../azure-resource-manager/management/manage-resources-portal.md) in dem Azure-Abonnement erstellt, das Sie in der Bereitstellungsanforderung für HANA (große Instanzen) übermittelt haben.  In der neuen Ressourcengruppe werden alle im jeweiligen Abonnement bereitgestellten Einheiten von HANA (große Instanzen) aufgelistet.

Um die neue Azure-Ressourcengruppe zu finden, listen Sie die Ressourcengruppe in Ihrem Abonnement auf, indem Sie im linken Navigationsbereich des Azure-Portals navigieren.

![Screenshot, auf dem die Option „Ressourcengruppe“ hervorgehoben ist](./media/hana-li-portal/portal-resource-group.png)

In der von Ihnen angezeigten Liste der Ressourcengruppen können Sie nach dem Abonnement filtern, das Sie zur Bereitstellung von HANA (große Instanzen) verwendet haben.

![Filtern von Ressourcengruppen im Azure-Portal](./media/hana-li-portal/portal-filtering-subscription.png)

Nach dem Filtern nach dem richtigen Abonnement verbleibt möglicherweise noch eine lange Liste von Ressourcengruppen. Suchen Sie nach einer mit dem Postfix **-Txxx**, wobei „xxx“ drei Ziffern sind, z.B. **-T050**. 

Wenn Sie die Ressourcengruppe gefunden haben, zeigen Sie deren Details an. Die Liste, die Sie daraufhin erhalten, sieht in etwa folgendermaßen aus:

![HLI-Liste im Azure-Portal](./media/hana-li-portal/portal-hli-units-list.png)

Alle aufgeführten Einheiten stellen eine einzelne Einheit von HLI (HANA Large Instances, große Instanzen) dar, die in Ihrem Abonnement bereitgestellt wurde. In diesem Fall betrachten wir acht verschiedene Einheiten von HANA (große Instanzen), die in Ihrem Abonnement bereitgestellt wurden.

Wenn Sie mehrere Mandanten für HANA (große Instanzen) unter demselben Azure-Abonnement bereitgestellt haben, werden mehrere Azure-Ressourcengruppen angezeigt. 


## <a name="look-at-attributes-of-single-hli-unit"></a>Anzeigen von Attributen einer einzelnen HLI-Einheit
In der Liste der Einheiten von HANA (große Instanzen) können Sie auf eine einzelne Einheit klicken und zu den Details der einzelnen Einheit von HANA (große Instanzen) gelangen. 

Auf der Übersichtsseite erhalten Sie nach Klicken auf „Mehr anzeigen“ eine Darstellung der Einheit, die in etwa folgendermaßen aussieht:

![Anzeigen einer Übersicht über eine HLI-Einheit](./media/hana-li-portal/portal-show-overview.png)

Wenn Sie die verschiedenen dargestellten Attribute betrachten, unterscheiden sich diese Attribute nicht wesentlich von Azure-VM-Attributen. Auf der linken Seite des Headers werden die Ressourcengruppe, die Azure-Region, der Abonnementname und die ID sowie einige von Ihnen hinzugefügte Tags angezeigt. Standardmäßig ist den Einheiten von HANA (große Instanzen) kein Tag zugewiesen. Auf der rechten Seite des Headers wird der Name der Einheit so aufgeführt, wie er bei der Bereitstellung zugewiesen wurde. Außerdem werden das Betriebssystem und die IP-Adresse angezeigt. Wie bei VMs wird auch der Einheitentyp von HANA (große Instanzen) mit der Anzahl von CPU-Threads und dem Arbeitsspeicher aufgeführt. Weitere Informationen zu den verschiedenen Einheiten von HANA (große Instanzen) finden Sie hier:

- [Verfügbare SKUs für HLI](./hana-available-skus.md)
- [Speicherarchitektur von SAP HANA (große Instanzen)](./hana-storage-architecture.md) 

Weitere Daten auf der rechten unteren Seite sind die Revision des Stamps von SAP HANA (große Instanzen). Mögliche Werte:

- Revision 3
- Revision 4

Revision 4 ist die neueste Architektur von SAP HANA (große Instanzen) mit wesentlichen Verbesserungen in der Netzwerklatenz zwischen Azure-VMs und Einheiten von SAP HANA (große Instanzen), die in Revision 4-Stamps oder -Reihen bereitgestellt werden.
Eine weitere sehr wichtige Information finden Sie in der rechten unteren Ecke der Übersicht mit dem Namen der Azure-Näherungsplatzierungsgruppe, die automatisch für jede bereitgestellte Einheit von SAP HANA (große Instanzen) erstellt wird. Diese Näherungsplatzierungsgruppe muss bei der Bereitstellung der Azure-VMs referenziert werden, die die SAP-Anwendungsschicht hosten. Durch die Verwendung der [Azure-Näherungsplatzierungsgruppe](../../linux/co-location.md), die der Einheit von SAP HANA (große Instanzen) zugeordnet ist, stellen Sie sicher, dass die Azure-VMs in unmittelbarer Nähe der Einheit von SAP HANA (große Instanzen) bereitgestellt werden. Die Art und Weise, wie Näherungsplatzierungsgruppen verwendet werden können, um die SAP-Anwendungsschicht im gleichen Azure-Rechenzentrum wie die als Revision 4 gehosteten Einheiten von SAP HANA (große Instanzen) unterzubringen, ist in [Azure-Näherungsplatzierungsgruppen für optimale Netzwerklatenz mit SAP-Anwendungen](sap-proximity-placement-scenarios.md) beschrieben.

Ein zusätzliches Feld in der rechten Spalte des Headers informiert Sie über den Energiezustand der Einheit von HANA (große Instanzen).

> [!NOTE]
> Der Energiezustand beschreibt, ob die Hardwareeinheit ein- oder ausgeschaltet ist. Er gibt keinen Aufschluss darüber, ob das Betriebssystem ausgeführt wird. Wenn Sie HANA (große Instanzen) neu starten, dauert es einen Augenblick, bis der Zustand der Einheit von **Wird gestartet** zu **Gestartet** wechselt. Der Zustand **Gestartet** weist darauf hin, dass das Betriebssystem gerade gestartet wird oder bereits vollständig gestartet wurde. Daher können Sie sich nach einem Neustart der Einheit möglicherweise nicht sofort bei der Einheit anmelden, sobald der Zustand zu **Gestartet** wechselt.
> 

Wenn Sie auf „Mehr anzeigen“ klicken, werden zusätzliche Informationen angezeigt. Eine zusätzliche Information zeigt die Revision des Stamps von SAP HANA (große Instanzen), wo die Einheit bereitgestellt wurde. Informationen zu den verschiedenen Revisionen der Stamps von SAP HANA (große Instanzen) finden Sie im Artikel [Was ist SAP HANA in Azure (große Instanzen)?](./hana-overview-architecture.md).

## <a name="check-activities-of-a-single-hana-large-instance-unit"></a>Überprüfen von Aktivitäten einer einzelnen Einheit von HANA (große Instanzen) 
Neben einem Überblick über HANA (große Instanzen) erhalten Sie auch die Möglichkeit, Aktivitäten der jeweiligen Einheit zu überprüfen. Ein Aktivitätsprotokoll könnte wie folgt aussehen:

![Navigationsbereich im Azure-Portal](./media/hana-li-portal/portal-activity-list.png)

Eine der wichtigsten aufgezeichneten Aktivitäten ist ein Neustart einer Einheit. Die aufgelisteten Daten umfassen den Status der Aktivität, den Zeitstempel der Aktivitätsauslösung, die Abonnement-ID, von der aus die Aktivität ausgelöst wurde, und den Azure-Benutzer, der die Aktivität ausgelöst hat. 

Eine weitere Aktivität, die aufgezeichnet wird, sind Änderungen an der Einheit in den Azure-Metadaten. Neben dem initiierten Neustart sehen Sie die Aktivität **Write HANAInstances**. Bei diesem Aktivitätstyp werden an HANA (große Instanzen) selbst keine Änderungen durchgeführt, aber es werden Änderungen an den Metadaten der Einheit in Azure dokumentiert. Im aufgeführten Fall haben wir ein Tag hinzugefügt und gelöscht (siehe nächster Abschnitt).

## <a name="add-and-delete-an-azure-tag-to-a-hana-large-instance-unit"></a>Hinzufügen und Löschen eines Azure-Tags zu bzw. aus einer Einheit von HANA (große Instanzen)
Eine weitere Möglichkeit besteht darin, einer Einheit von HANA (große Instanzen) ein [Tag](../../../azure-resource-manager/management/tag-resources.md) hinzuzufügen. Diese Art der Tagzuweisung unterscheidet sich nicht von der Zuweisung von Tags zu virtuellen Computern. Wie bei virtuellen Computern befinden sich die Tags in den Azure-Metadaten und besitzen für HANA (große Instanzen) dieselben Einschränkungen wie Tags für virtuelle Computer.

Das Löschen von Tags funktioniert auf dieselbe Weise wie bei VMs. Beide Aktivitäten, das Anwenden und das Löschen eines Tags, werden im Aktivitätsprotokoll der jeweiligen Einheit von HANA (große Instanzen) aufgeführt.

## <a name="check-properties-of-a-hana-large-instance-unit"></a>Überprüfen von Eigenschaften einer Einheit von HANA (große Instanzen)
Der Abschnitt **Eigenschaften** enthält wichtige Informationen, die Sie bei Übergabe der Instanzen erhalten. In diesem Abschnitt erhalten Sie alle Informationen, die Sie möglicherweise für Supportfälle oder beim Einrichten einer Speichermomentaufnahme-Konfiguration benötigen. Dementsprechend ist dieser Abschnitt eine Sammlung von Daten rund um Ihre Instanz, die Konnektivität der Instanz mit Azure und das Speicher-Back-End. Der obere Bereich des Abschnitt sieht wie folgt aus:


![Oberer Teil der HLI-Eigenschaften im Azure-Portal](./media/hana-li-portal/portal-properties-top.png)

Die ersten paar Datenelemente haben Sie bereits im Übersichtsbildschirm gesehen. Ein wichtiger Bestandteil der Daten ist jedoch die ID der ExpressRoute-Verbindung, die Sie bei der Übergabe der ersten bereitgestellten Einheiten erhalten haben. In einigen Supportfällen werden Sie möglicherweise nach diesen Daten gefragt. Ein wichtiger Dateneintrag wird im unteren Teil des Screenshot gezeigt. Dabei handelt es sich um die IP-Adresse des NFS-Speicherheads, die Ihren Speicher im Stapel von HANA (große Instanzen) auf Ihren **Mandanten** isoliert. Diese IP-Adresse ist auch erforderlich, wenn Sie die [Konfigurationsdatei für Sicherungen von Speichermomentaufnahmen](./hana-backup-restore.md#set-up-storage-snapshots) bearbeiten. 

Während Sie im Eigenschaftenbereich nach unten scrollen, erhalten Sie zusätzliche Daten wie eine eindeutige Ressourcen-ID für Ihre Einheit für HANA (große Instanzen) oder die Abonnement-ID, die der Bereitstellung zugewiesen wurde.

## <a name="restart-a-hana-large-instance-unit-through-azure-portal"></a>Neustart von HANA (große Instanzen) über das Azure-Portal
Beim Initiieren eines Neustarts des Linux-Betriebssystems gab es verschiedene Situationen, in denen das Betriebssystem einen Neustart nicht erfolgreich abschließen konnte. Um einen Neustart zu erzwingen, mussten Sie einen Service Request erstellen, damit Microsoft Operations einen Neustart der Einheit von HANA (große Instanzen) durch Aus- und Wiedereinschalten durchführt. Die Funktionalität eines solchen Neustarts einer Einheit von HANA (große Instanzen) ist jetzt im Azure-Portal integriert. Im Übersichtsteil der Einheit von HANA (große Instanzen) befindet sich die Schaltfläche für den Neustart oben im Datenbereich.

![Schritt 1 zum Neustart im Azure-Portal](./media/hana-li-portal/portal-restart-first-step.png)

Wenn Sie auf die Schaltfläche „Neu starten“ klicken, werden Sie gefragt, ob Sie die Einheit neu starten möchten. Wenn Sie den Vorgang durch Klicken auf „Ja“ bestätigen, wird die Einheit neu gestartet.

> [!NOTE]
> Während des Neustartvorgangs dauert es einen Augenblick, bis der Zustand der Einheit von **Wird gestartet** zu **Gestartet** wechselt. Der Zustand **Gestartet** weist darauf hin, dass das Betriebssystem gerade gestartet wird oder bereits vollständig gestartet wurde. Daher können Sie sich nach einem Neustart der Einheit möglicherweise nicht sofort bei der Einheit anmelden, sobald der Zustand zu **Gestartet** wechselt.

> [!IMPORTANT]
> Abhängig von der Speichermenge in Ihrer HANA-Einheit (große Instanzen) kann der Neustart der Hardware und des Betriebssystems bis zu einer Stunde dauern.


## <a name="open-a-support-request-for-hana-large-instances"></a>Öffnen einer Supportanfrage für HANA (große Instanzen)
Im Azure-Portal können Sie in der Ansicht der Einheiten von HANA (große Instanzen) Supportanfragen speziell für eine Einheit von HANA (große Instanzen) erstellen. Folgen Sie dem Link **Neue Supportanfrage**. 

![Schritt 1 zum Initiieren eines Service Requests im Azure-Portal](./media/hana-li-portal/portal-initiate-support-request.png)

Damit der Dienst von SAP HANA (große Instanzen) im nächsten Bildschirm aufgeführt wird, müssen Sie ggf. „Alle Dienste“ auswählen, wie unten dargestellt.

![Auswählen aller Dienste im Azure-Portal](./media/hana-li-portal/portal-create-service-request.png)

In der Liste der Dienste finden Sie den Dienst **SAP HANA (große Instanzen)** . Wenn Sie diesen Dienst auswählen, können Sie, wie dargestellt, bestimmte Problemtypen auswählen:


![Auswählen der Problemklasse im Azure-Portal](./media/hana-li-portal/portal-select-problem-class.png)

Unter jedem der verschiedenen Problemtypen wird Ihnen eine Auswahl von Problemuntertypen angeboten, die Sie zur weiteren Eingrenzung des Problems auswählen müssen. Nach Auswahl des Untertyps können Sie jetzt den Betreff benennen. Sobald Sie mit dem Auswahlprozess fertig sind, können Sie mit dem nächsten Erstellungsschritt fortfahren. Im Abschnitt **Lösungen** werden Sie auf Dokumentation zu HANA (große Instanzen) verwiesen. Dort finden Sie möglicherweise Hinweise auf eine Lösung Ihres Problems. Wenn Sie in der vorgeschlagenen Dokumentation keine Lösung für Ihr Problem finden, wechseln Sie zum nächsten Schritt. Im nächsten Schritt werden Sie gefragt, ob das Problem bei VMs oder bei Einheiten von HANA (große Instanzen) auftritt. Anhand dieser Angaben wird die Supportanfrage an die richtigen Experten weitergeleitet. 

![Details einer Supportanfrage im Azure-Portal](./media/hana-li-portal/portal-support-request-details.png)

Wenn Sie die Fragen beantwortet und zusätzliche Informationen bereitgestellt haben, können Sie zum nächsten Schritt wechseln, um die Supportanfrage zu prüfen und zu senden.

## <a name="next-steps"></a>Nächste Schritte

- [Überwachen von SAP HANA in Azure (große Instanzen)](./troubleshooting-monitoring.md)
- [Überwachung und Problembehandlung auf HANA-Seite](./hana-monitor-troubleshoot.md)
