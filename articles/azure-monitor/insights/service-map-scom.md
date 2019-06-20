---
title: Integration von Service Map in System Center Operations Manager | Microsoft Docs
description: Service Map ist eine Lösung in Azure, die Anwendungskomponenten auf Windows- und Linux-Systemen automatisch ermittelt und die Kommunikation zwischen Diensten abbildet. Dieser Artikel erläutert die Verwendung von Service Map zum automatischen Erstellen von Diagrammen der verteilten Anwendungen in Operations Manager.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: e8614a5a-9cf8-4c81-8931-896d358ad2cb
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2017
ms.author: magoedte
ms.openlocfilehash: 40e6d6ff6ea8748b525642e5507c80590b322b7a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60402539"
---
# <a name="service-map-integration-with-system-center-operations-manager"></a>Integration von Service Map in System Center Operations Manager

Service Map ermittelt automatisch Anwendungskomponenten auf Windows- und Linux-Systemen und stellt die Kommunikation zwischen Diensten dar. In Service Map können Sie die Server Ihrer Vorstellung gemäß anzeigen – als verbundene Systeme, die wichtige Dienste bereitstellen. Service Map zeigt Verbindungen zwischen Servern, Prozessen und Ports über die gesamte TCP-Verbindungsarchitektur an. Außer der Installation eines Agents ist keine weitere Konfiguration erforderlich. Weitere Informationen finden Sie in der [Dokumentation zu Service Map]( service-map.md).

Mit dieser Integration zwischen Service Map und System Center Operations Manager können Sie basierend auf dynamischen Abhängigkeitszuordnungen in Service Map automatisch Diagramme der verteilten Anwendungen in Operations Manager erstellen.

## <a name="prerequisites"></a>Voraussetzungen
* Eine Operations Manager-Verwaltungsgruppe (2012 R2 oder höher), die eine Gruppe von Servern verwaltet.
* Ein Log Analytics-Arbeitsbereich mit aktivierter Service Map-Lösung.
* Eine Gruppe von Servern (mindestens eine), die über Operations Manager verwaltet werden und Daten an Service Map senden. Es werden Windows- und Linux-Server unterstützt.
* Ein Dienstprinzipal mit Zugriff auf das Azure-Abonnement, das dem Log Analytics-Arbeitsbereich zugeordnet ist. Weitere Informationen finden Sie unter [Erstellen eines Dienstprinzipals](#create-a-service-principal).

## <a name="install-the-service-map-management-pack"></a>Installieren des Service Map Management Pack
Sie aktivieren die Integration zwischen Operations Manager und Service Map durch Importieren des Management Pack-Pakets Microsoft.SystemCenter.ServiceMap (Microsoft.SystemCenter.ServiceMap.mpb). Sie können das Management Pack-Paket aus dem [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=55763) herunterladen. Das Paket enthält die folgenden Management Packs:
* Microsoft Service Map Application Views
* Microsoft System Center Service Map Internal
* Microsoft System Center Service Map Overrides
* Microsoft System Center Service Map

## <a name="configure-the-service-map-integration"></a>Konfigurieren der Integration von Service Map
Nach der Installation des Service Map Management Packs wird im Bereich **Verwaltung** unter **Operations Management Suite** der neue Knoten **Service Map** angezeigt.

>[!NOTE]
>[Operations Management Suite war eine Sammlung von Diensten](https://github.com/MicrosoftDocs/azure-docs-pr/pull/azure-monitor/azure-monitor-rebrand.md#retirement-of-operations-management-suite-brand), zu der auch Log Analytics, das jetzt Teil von [Azure Monitor](https://github.com/MicrosoftDocs/azure-docs-pr/pull/azure-monitor/overview.md) ist, gehörte.

Um die Service Map-Integration zu konfigurieren, führen Sie folgende Schritte aus:

1. Um den Konfigurations-Assistenten zu öffnen, klicken Sie im Bereich **Service Map Overview** (Service Map-Übersicht) auf **Arbeitsbereich hinzufügen**.  

    ![Bereich „Service Map Overview“ (Service Map-Übersicht)](media/service-map-scom/scom-configuration.png)

2. Geben Sie im Fenster **Verbindungskonfiguration** den Mandantennamen oder die Mandanten-ID, die Anwendungs-ID (auch als Benutzername bzw. clientID bezeichnet) und das Kennwort des Dienstprinzipals ein, und klicken Sie auf **Weiter**. Weitere Informationen finden Sie unter „Erstellen eines Dienstprinzipals“.

    ![Das Fenster „Verbindungskonfiguration“](media/service-map-scom/scom-config-spn.png)

3. Wählen Sie im Fenster **Abonnementauswahl** das Azure-Abonnement, die Azure-Ressourcengruppe (diejenige, die den Log Analytics-Arbeitsbereich enthält) und den Log Analytics-Arbeitsbereich, und klicken Sie dann auf **Weiter**.

    ![Der Operations Manager-Konfigurationsarbeitsbereich](media/service-map-scom/scom-config-workspace.png)

4. Wählen Sie im Fenster **Computergruppenauswahl**, welche Service Map-Computergruppen Sie mit Operations Manager synchronisieren möchten. Klicken Sie auf **Computergruppen hinzufügen/entfernen**, wählen Sie Gruppen aus der Liste der **Verfügbaren Computergruppen**, und klicken Sie auf **Hinzufügen**.  Klicken Sie nach Auswahl der Gruppen auf **OK**, um den Vorgang abzuschließen.

    ![Die Operations Manager-Konfigurationscomputergruppen](media/service-map-scom/scom-config-machine-groups.png)

5. Konfigurieren Sie im Fenster **Serverauswahl** die „Service Map-Servergruppe“ mit den Servern, die Sie zwischen Operations Manager und Service Map synchronisieren möchten. Klicken Sie auf **Server hinzufügen/entfernen**.   

    Damit die Integration ein Diagramm der verteilten Anwendung für einen Server erstellen kann, muss der Server folgende Voraussetzungen erfüllen:

   * Verwaltet von Operations Manager
   * Verwaltet von Service Map
   * In der „Service Map-Servergruppe“ aufgelistet

     ![Die Operations Manager-Konfigurationsgruppe](media/service-map-scom/scom-config-group.png)

6. Optional: Wählen Sie den Ressourcenpool „Verwaltungsserver“ für die Kommunikation mit Log Analytics aus, und klicken Sie auf **Arbeitsbereich hinzufügen**.

    ![Der Operations Manager-Konfigurationsressourcenpool](media/service-map-scom/scom-config-pool.png)

    Es kann eine Minute dauern, den Log Analytics-Arbeitsbereich zu konfigurieren und registrieren. Nach Abschluss der Konfiguration wird in Operations Manager die erste Service Map-Synchronisierung initiiert.

    ![Der Operations Manager-Konfigurationsressourcenpool](media/service-map-scom/scom-config-success.png)


## <a name="monitor-service-map"></a>Überwachen von Service Map
Nachdem die Verbindung mit dem Log Analytics-Arbeitsbereichs hergestellt wurde, wird ein neuer Ordner „Service Map“ im Bereich **Überwachung** der Operations Manager-Konsole angezeigt.

![Operations Manager-Bereich „Überwachung“](media/service-map-scom/scom-monitoring.png)

Der Ordner „Service Map“ umfasst vier Knoten:
* **Aktive Warnungen**: Listet alle aktiven Warnungen zur Kommunikation zwischen Operations Manager und Service Map auf.  Beachten Sie, dass diese Warnungen nicht die Log Analytics-Warnungen sind, die mit Operations Manager synchronisiert werden.

* **Server**: Hier wird die Liste der überwachten Server angezeigt, die für die Synchronisierung mit Service Map konfiguriert sind.

    ![Der Serverüberwachungsbereich von Operations Manager](media/service-map-scom/scom-monitoring-servers.png)

* **Computerabhängigkeitsansichten**: Listet alle Computergruppen auf, die von Service Map aus synchronisiert werden. Sie können auf eine beliebige Gruppe klicken, um das entsprechende Diagramm der verteilten Anwendungen anzuzeigen.

    ![Das Diagramm der verteilten Anwendungen im Operations Manager](media/service-map-scom/scom-group-dad.png)

* **Serverabhängigkeitsansichten**: Listet alle Server auf, die von Service Map aus synchronisiert werden. Sie können auf einen beliebigen Server klicken, um das entsprechende Diagramm der verteilten Anwendungen anzuzeigen.

    ![Das Diagramm der verteilten Anwendungen im Operations Manager](media/service-map-scom/scom-dad.png)

## <a name="edit-or-delete-the-workspace"></a>Bearbeiten oder Löschen des Arbeitsbereichs
Sie können den konfigurierten Arbeitsbereich im Bereich der **Service Map-Übersicht** (Bereich **Verwaltung** > **Operations Management Suite** > **Service Map**) bearbeiten oder löschen.

>[!NOTE]
>[Operations Management Suite war eine Sammlung von Diensten](https://github.com/MicrosoftDocs/azure-docs-pr/pull/azure-monitor/azure-monitor-rebrand.md#retirement-of-operations-management-suite-brand), zu der auch Log Analytics, das jetzt Teil von [Azure Monitor](https://github.com/MicrosoftDocs/azure-docs-pr/pull/azure-monitor/overview.md) ist, gehörte.

Aktuell können Sie nur einen Log Analytics-Arbeitsbereich konfigurieren.

![Der Bereich zum Bearbeiten des Arbeitsbereichs im Operations Manager](media/service-map-scom/scom-edit-workspace.png)

## <a name="configure-rules-and-overrides"></a>Konfigurieren von Regeln und Außerkraftsetzungen
Die Regel _Microsoft.SystemCenter.ServiceMapImport.Rule_ wird erstellt, um regelmäßig Informationen von Service Map abzurufen. Um die Zeitsteuerungen der Synchronisierung zu ändern, können Sie Außerkraftsetzungen der Regel (Bereich **Erstellung** > **Regeln** > **Microsoft.SystemCenter.ServiceMapImport.Rule**) konfigurieren.

![Das Operations Manager-Fenster für Eigenschaften von Außerkraftsetzungen](media/service-map-scom/scom-overrides.png)

* **Enabled**: Aktivieren oder Deaktivieren der automatischen Updates.
* **IntervalMinutes**: Zurücksetzen der Zeitspanne zwischen Updates. Das Standardintervall beträgt eine Stunde. Wenn Sie Serverzuordnungen in kürzeren Abständen synchronisieren möchten, können Sie den Wert ändern.
* **TimeoutSeconds**: Zurücksetzen der Zeitspanne bis zum Timeout der Anforderung.
* **TimeWindowMinutes**: Zurücksetzen des Zeitfensters zum Abfragen von Daten. Standardmäßig ist ein Fenster von 60 Minuten festgelegt. Der von Service Map zugelassene Maximalwert beträgt 60 Minuten.

## <a name="known-issues-and-limitations"></a>Bekannte Probleme und Einschränkungen

Das aktuelle Design weist folgende Probleme und Einschränkungen auf:
* Sie können nur eine Verbindung mit einem einzigen Log Analytics-Arbeitsbereich herstellen.
* Sie können im Bereich **Erstellen** der „Service Map-Servergruppe“ zwar manuell Server hinzufügen, aber die Zuordnungen für diese Server werden nicht sofort synchronisiert.  Sie werden beim nächsten Synchronisierungszyklus von Service Map aus synchronisiert.
* Wenn Sie Änderungen an den vom Management Pack erstellten Diagrammen der verteilten Anwendungen vornehmen, werden diese Änderungen wahrscheinlich bei der nächsten Synchronisierung mit Service Map überschrieben.

## <a name="create-a-service-principal"></a>Erstellen eines Dienstprinzipals
Die offizielle Azure-Dokumentation zum Erstellen eines Dienstprinzipals finden Sie in den folgenden Dokumenten:
* [Erstellen eines Dienstprinzipals für den Zugriff auf Ressourcen mithilfe von Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal)
* [Erstellen eines Dienstprinzipals für den Zugriff auf Ressourcen mithilfe der Azure-Befehlszeilenschnittstelle](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal-cli)
* [Erstellen einer Azure Active Directory-Anwendung und eines Dienstprinzipals mit Ressourcenzugriff mithilfe des Portals](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal)

### <a name="feedback"></a>Feedback
Haben Sie Feedback für uns zu Service Map oder dieser Dokumentation? Besuchen Sie unsere [UserVoice-Webseite](https://feedback.azure.com/forums/267889-log-analytics/category/184492-service-map), auf der Sie Features vorschlagen oder vorhandene Vorschläge unterstützen können.
