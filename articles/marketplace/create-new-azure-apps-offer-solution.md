---
title: Konfigurieren eines Plans für eine Lösungsvorlage
description: Erfahren Sie, wie Sie einen Plan für eine Lösungsvorlage für Ihr Azure-Anwendungsangebot in Partner Center konfigurieren.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: 8469cad02009d054bd8ba97fb4aabfdae84ef842
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2020
ms.locfileid: "96744617"
---
# <a name="configure-a-solution-template-plan"></a>Konfigurieren eines Plans für eine Lösungsvorlage

Dieser Artikel bezieht sich nur auf Pläne für Lösungsvorlagen in Azure-Anwendungsangeboten. Wenn Sie einen Plan für eine verwaltete Anwendung konfigurieren, lesen Sie [Konfigurieren eines Plans für verwaltete Anwendungen](create-new-azure-apps-offer-managed.md).

## <a name="choose-who-can-see-your-plan"></a>Auswählen der Sichtbarkeit des Plans

Sie können festlegen, dass jeder Plan für alle Benutzer oder nur für eine bestimmte Zielgruppe angezeigt werden soll. Der Zugriff für eine private Zielgruppe wird über Azure-Abonnement-IDs zugewiesen. Dabei kann eine Beschreibung für jede zugewiesene Abonnement-ID angegeben werden. Sie können maximal 10 Abonnement-IDs manuell eingeben oder über eine CSV-Datei bis zu 10.000 Abonnement-IDs angeben. Azure-Abonnement-IDs werden als GUIDs dargestellt. Buchstaben müssen in Kleinbuchstaben angegeben werden.

> [!NOTE]
> Wenn Sie einen privaten Plan veröffentlichen, können Sie die Sichtbarkeit später in „Öffentlich“ ändern. Wenn Sie hingegen einen öffentlichen Plan veröffentlichen, können Sie dessen Sichtbarkeit nicht in „Privat“ ändern.

Führen Sie auf der Registerkarte **Verfügbarkeit** unter **Sichtbarkeit des Plans** einen der folgenden Schritte aus:

- Um den Plan öffentlich zu machen, wählen Sie das Optionsfeld **Öffentlich** aus.
- Um den Plan als privat zu kennzeichnen, wählen Sie das Optionsfeld **Privat** aus, und fügen Sie die Azure-Abonnement-IDs manuell oder über eine CSV-Datei hinzu.

    > [!NOTE]
    > Eine private oder eingeschränkte Zielgruppe unterscheidet sich von der auf der Registerkarte **Vorschau** definierten Vorschauzielgruppe. Eine Vorschauzielgruppe kann auf das Angebot zugreifen, bevor es in den Marketplaces live veröffentlicht wird. Die Auswahl der privaten Zielgruppe gilt nur für einen bestimmten Plan, aber die private Zielgruppe kann alle Pläne (private und nicht private Pläne) zu Validierungszwecken anzeigen.

### <a name="manually-add-azure-subscription-ids-for-a-private-plan"></a>Manuelles Hinzufügen von Azure-Abonnement-IDs zu einem privaten Plan

1. Wählen Sie unter **Sichtbarkeit des Plans** das Optionsfeld **Privat** aus.
1. Geben Sie im eingeblendeten Feld **Azure-Abonnement-ID** die Azure-Abonnement-ID der Zielgruppe ein, der Sie Zugriff auf diesen privaten Plan gewähren möchten. Es muss mindestens eine Abonnement-ID angegeben werden.
1. (Optional) Geben Sie im Feld **Beschreibung** eine Beschreibung der Zielgruppe ein.
1. Wählen Sie zum Hinzufügen einer weiteren Abonnement-ID den Link **ID hinzufügen (max. 10)** aus, und wiederholen Sie die Schritte 2 und 3.

## <a name="use-a-csv-file-to-add-azure-subscription-ids-for-a-private-plan"></a>Hinzufügen von Azure-Abonnement-IDs zu einem privaten Plan mithilfe einer CSV-Datei

1. Wählen Sie unter **Sichtbarkeit des Plans** das Optionsfeld **Privat** aus.
1. Wählen Sie den Link **Zielgruppe exportieren (CSV)** aus.
1. Öffnen Sie die CSV-Datei, und fügen Sie die Azure-Abonnement-IDs, denen Sie Zugriff auf das private Angebot gewähren möchten, in der Spalte **ID** hinzu.
1. (Optional) Geben Sie in der Spalte **Description** eine Beschreibung für jede Zielgruppe ein.
1. Fügen Sie in der Spalte **Type** (Typ) für jede Zeile mit einer Abonnement-ID die Zeichenfolge „SubscriptionId“ hinzu.
1. Speichern Sie die CSV-Datei.
1. Wählen Sie auf der Registerkarte **Verfügbarkeit** unter **Sichtbarkeit des Plans** den Link **Zielgruppe importieren (CSV)** aus.
1. Wählen Sie im angezeigten Dialogfeld **Ja** aus.
1. Wählen Sie die CSV-Datei und dann **Öffnen** aus. In einer Meldung wird angezeigt, dass die CSV-Datei erfolgreich importiert wurde.

### <a name="hide-your-plan"></a>Ausblenden des Plans

Soll die Lösungsvorlage nur indirekt, also nur bei Verweisen über eine andere Lösungsvorlage oder eine verwaltete Anwendung, bereitgestellt werden, aktivieren Sie das Kontrollkästchen unter **Plan ausblenden**, um Ihre Lösungsvorlage zu veröffentlichen, aber für Kunden auszublenden, die direkt danach suchen.

Wählen Sie **Entwurf speichern** aus, bevor Sie mit dem nächsten Abschnitt fortfahren: „Definieren der technischen Konfiguration“.

## <a name="define-the-technical-configuration"></a>Definieren der technischen Konfiguration

Auf der Registerkarte **Technische Konfiguration** können Sie das Bereitstellungspaket hochladen, mit dem Kunden Ihren Plan bereitstellen können, und eine Versionsnummer für das Paket angeben.

> [!NOTE]
> Diese Registerkarte wird nicht angezeigt, wenn Sie auf der Registerkarte **Plansetup** festgelegt haben, dass Pakete aus einem anderen Plan wiederverwendet werden. Fahren Sie in diesem Fall mit [Anzeigen der Pläne](#view-your-plans) fort.

### <a name="assign-a-version-number-for-the-package"></a>Zuweisen einer Versionsnummer für das Paket

Geben Sie im Feld **Version** die aktuelle Version der technischen Konfiguration an. Erhöhen Sie diese Version jedes Mal, wenn Sie eine Änderung auf dieser Seite veröffentlichen. Die Versionsnummer muss folgendes Format aufweisen: „Integer.Integer.Integer“. Beispiel: `1.0.2`.

### <a name="upload-a-package-file"></a>Hochladen einer Paketdatei

Ziehen Sie unter **Paketdatei (ZIP)** die Paketdatei in das graue Feld, oder wählen Sie den Link zum **Suchen Ihrer Dateien** aus.

> [!NOTE]
> Wenn beim Hochladen von Dateien ein Problem auftritt, vergewissern Sie sich, dass das lokale Netzwerk den von Partner Center verwendeten Dienst `https://upload.xboxlive.com` nicht blockiert.

### <a name="previously-published-packages"></a>Zuvor veröffentlichte Pakete

Nachdem Sie Ihr Angebot veröffentlicht haben, wird auf der Seite **Technische Konfiguration** die Unterregisterkarte **Zuvor veröffentlichte Pakete** angezeigt. Auf dieser Registerkarte werden alle zuvor veröffentlichten Versionen Ihrer technischen Konfiguration aufgelistet.

## <a name="view-your-plans"></a>Anzeigen der Pläne

- Wählen Sie **Entwurf speichern** und dann in der linken oberen Ecke der Seite **Planübersicht** aus, um zur Seite **Planübersicht** zurückzukehren.

Nachdem Sie mindestens einen Plan erstellt haben, werden auf der Registerkarte **Planübersicht** der Planname, die Plan-ID, der Plantyp, die Verfügbarkeit (öffentlich oder privat), der aktuelle Veröffentlichungsstatus und alle verfügbaren Aktionen angezeigt.

Die Aktionen, die in der Spalte **Aktion** der Registerkarte **Planübersicht** verfügbar sind, hängen vom Status Ihres Plans ab und können Folgendes umfassen:

- Wenn der Planstatus **Entwurf** lautet, wird in der Spalte **Aktion** die Option **Entwurf löschen** angezeigt.
- Wenn der Planstatus **Live** lautet, wird in der Spalte **Aktion** entweder **Verkauf des Plans einstellen** oder **Private Zielgruppe synchronisieren** angezeigt. Über den Link **Sync private audience** (Private Zielgruppe synchronisieren) werden nur die Änderungen an Ihren privaten Zielgruppen veröffentlicht, ohne dass weitere Aktualisierungen veröffentlicht werden, die Sie möglicherweise am Angebot vorgenommen haben.
- Um einen weiteren Plan für dieses Angebot zu erstellen, wählen Sie im oberen Bereich der Registerkarte **Planübersicht** die Option **+ Neuen Plan erstellen** aus. Wiederholen Sie dann die Schritte unter [Erstellen von Plänen für Ihr Azure-Anwendungsangebot](create-new-azure-apps-offer-plans.md). Wenn Sie das Erstellen von Plänen abgeschlossen haben, fahren Sie mit dem nächsten Abschnitt „Nächste Schritte“ fort.

## <a name="next-steps"></a>Nächste Schritte

- [Testen und Veröffentlichen eines Azure-Anwendungsangebots](create-new-azure-apps-offer-test-publish.md)
- Erfahren Sie, wie Sie die Programme „Co-Selling mit Microsoft“ und „Verkaufen über CSPs“ für das [Vermarkten Ihres Azure-Anwendungsangebots](create-new-azure-apps-offer-marketing.md) nutzen.
