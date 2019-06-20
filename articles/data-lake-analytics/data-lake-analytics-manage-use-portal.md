---
title: Verwalten von Azure Data Lake Analytics mithilfe des Azure-Portals
description: In diesem Artikel wird beschrieben, wie Sie das Azure-Portal zum Verwalten von Data Lake Analytics-Konten, -Datenquellen, -Benutzern und -Aufträgen verwenden.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: a0e045f1-73d6-427f-868d-7b55c10f811b
ms.topic: conceptual
ms.date: 12/05/2016
ms.openlocfilehash: 8b2f16f45be1d095e9be8042611de328af36f064
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60813438"
---
# <a name="manage-azure-data-lake-analytics-using-the-azure-portal"></a>Verwalten von Azure Data Lake Analytics mithilfe des Azure-Portals
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

In diesem Artikel wird beschrieben, wie Sie Azure Data Lake Analytics-Konten, -Datenquellen, -Benutzer und -Aufträge mithilfe des Azure-Portals verwalten.


<!-- ################################ -->
<!-- ################################ -->

## <a name="manage-data-lake-analytics-accounts"></a>Verwalten von Data Lake Analytics-Konten

### <a name="create-an-account"></a>Erstellen eines Kontos

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Klicken Sie auf **Ressource erstellen** > **Intelligence + Analyse** > **Data Lake Analytics**.
3. Wählen Sie Werte für die folgenden Elemente: 
   1. **Name**: Der Name des Data Lake Analytics-Kontos.
   2. **Abonnement**: Das für das Konto verwendete Azure-Abonnement.
   3. **Ressourcengruppe**: Der Name der Azure-Ressourcengruppe, in der das Konto erstellt werden soll. 
   4. **Standort**: Das Azure-Rechenzentrum für das Data Lake Analytics-Konto. 
   5. **Data Lake Store**: Der Standardspeicher, der für das Data Lake Analytics-Konto verwendet werden soll. Das Data Lake Store-Konto und das Data Lake Analytics-Konto müssen sich am selben Ort befinden.
4. Klicken Sie auf **Create**. 

### <a name="delete-a-data-lake-analytics-account"></a>Löschen eines Data Lake Analytics-Kontos

Bevor Sie ein Data Lake Analytics-Konto löschen, löschen Sie dessen Data Lake Store-Konto.

1. Gehen Sie im Azure-Portal zu Ihrem Data Lake Analytics-Konto.
2. Klicken Sie auf **Löschen**.
3. Geben Sie den Kontonamen ein.
4. Klicken Sie auf **Löschen**.

<!-- ################################ -->
<!-- ################################ -->

## <a name="manage-data-sources"></a>Verwalten von Datenquellen

Data Lake Analytics unterstützt die folgenden Datenquellen:

* Data Lake Store
* Azure Storage

Sie können den Daten-Explorer zum Durchsuchen von Datenquellen und zum Durchführen von grundlegenden Dateiverwaltungsvorgängen verwenden. 

### <a name="add-a-data-source"></a>Hinzufügen einer Datenquelle

1. Gehen Sie im Azure-Portal zu Ihrem Data Lake Analytics-Konto.
2. Klicken Sie auf **Datenquellen**.
3. Klicken Sie auf **Datenquelle hinzufügen**.
    
   * Zum Hinzufügen eines Data Lake Store-Kontos benötigen Sie den Kontonamen und Zugriff auf das Konto, um eine Abfrage durchführen zu können.
   * Zum Hinzufügen von Azure Blob Storage benötigen Sie das Speicherkonto und den Kontoschlüssel. Diese finden Sie im Speicherkonto im Portal.

## <a name="set-up-firewall-rules"></a>Einrichten von Firewallregeln

Sie können Data Lake Analytics verwenden, um den Zugriff auf Ihr Data Lake Analytics-Konto auf Netzwerkebene weiter zu sperren. Sie können eine Firewall aktivieren, eine IP-Adresse angeben oder einen IP-Adressbereich für Ihre vertrauenswürdigen Clients definieren. Nachdem Sie diese Measures aktiviert haben, können nur Clients, die über die IP-Adressen innerhalb des angegebenen Bereichs verfügen, eine Verbindung mit dem Speicher herstellen.

Wenn andere Azure-Dienste wie Azure Data Factory oder VMs eine Verbindung mit dem Data Lake Analytics-Konto herstellen, stellen Sie sicher, dass **Allow Azure Services** (Azure-Dienste zulassen) **aktiviert** ist. 

### <a name="set-up-a-firewall-rule"></a>Einrichten einer Firewallregel

1. Gehen Sie im Azure-Portal zu Ihrem Data Lake Analytics-Konto.
2. Klicken Sie im Menü auf der linken Seite auf **Firewall**.

## <a name="add-a-new-user"></a>Hinzufügen eines neuen Benutzers

Sie können den **Assistent für das Hinzufügen von Benutzern** verwenden, um einfach neue Data Lake-Benutzer bereitzustellen.

1. Gehen Sie im Azure-Portal zu Ihrem Data Lake Analytics-Konto.
2. Klicken Sie auf der linken Seite unter **Erste Schritte** auf **Assistent zum Hinzufügen von Benutzern**.
3. Wählen Sie einen Benutzer aus, und klicken Sie dann auf **Auswählen**.
4. Wählen Sie eine Rolle aus, und klicken Sie dann auf **Auswählen**. Um einen neuen Entwickler für die Verwendung von Azure Data Lake einzurichten, wählen Sie die Rolle **Data Lake Analytics-Entwickler** aus.
5. Wählen Sie die Zugriffssteuerungslisten (ACLs) für die U-SQL-Datenbanken aus. Wenn Sie mit Ihrer Auswahl zufrieden sind, klicken Sie auf **Auswählen**.
6. Wählen Sie die ACLs für Dateien aus. Ändern Sie für den Standardspeicher nicht die ACLs für das Stammverzeichnis „/“ und für den Systemordner. Klicken Sie auf **Auswählen**.
7. Überprüfen Sie alle ausgewählten Änderungen, und klicken Sie dann auf **Ausführen**.
8. Wenn der Assistent abgeschlossen ist, klicken Sie auf **Fertig**.

## <a name="manage-role-based-access-control"></a>Verwalten der rollenbasierten Zugriffssteuerung

Wie andere Azure-Dienste können Sie die rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC) verwenden, um zu steuern, wie Benutzer mit dem Dienst interagieren.

Die standardmäßigen RBAC-Rollen haben die folgenden Funktionen:
* **Besitzer**: Kann Aufträge übermitteln, Aufträge überwachen, Aufträge von beliebigen Benutzern abbrechen und das Konto konfigurieren.
* **Mitwirkender**: Kann Aufträge übermitteln, Aufträge überwachen, Aufträge von beliebigen Benutzern abbrechen und das Konto konfigurieren.
* **Leser**: Kann Aufträge überwachen.

Verwenden Sie die Rolle „Data Lake Analytics-Entwickler“, um U-SQL-Entwicklern das Verwenden des Data Lake Analytics-Diensts zu ermöglichen. Sie können die Rolle „Data Lake Analytics-Entwickler“ für Folgendes verwenden:
* Übermitteln von Aufträgen.
* Überwachen des Auftragsstatus und des Fortschritts von Aufträgen, die von jedem Benutzer übermittelt werden.
* Anzeigen der U-SQL-Skripts von Aufträgen, die von einem beliebigen Benutzer übermittelt wurden.
* Abbrechen ausschließlich Ihrer eigenen Aufträge.

### <a name="add-users-or-security-groups-to-a-data-lake-analytics-account"></a>Hinzufügen von Benutzern oder Sicherheitsgruppen zu einem Data Lake Analytics-Konto

1. Gehen Sie im Azure-Portal zu Ihrem Data Lake Analytics-Konto.
2. Klicken Sie auf **Zugriffssteuerung (IAM)**  > **Rollenzuweisung hinzufügen**.
3. Wählen Sie eine Rolle aus.
4. Fügen Sie einen Benutzer hinzu.
5. Klicken Sie auf **OK**.

>[!NOTE]
>Wenn ein Benutzer oder eine Sicherheitsgruppe Aufträge übermitteln muss, benötigt er oder sie auch die Berechtigung für das Speicherkonto. Weitere Informationen finden Sie unter [Schützen von Daten, die im Azure Data Lake Store gespeichert sind](../data-lake-store/data-lake-store-secure-data.md).
>

<!-- ################################ -->
<!-- ################################ -->

## <a name="manage-jobs"></a>Verwalten von Aufträgen

### <a name="submit-a-job"></a>Übermitteln eines Auftrags

1. Gehen Sie im Azure-Portal zu Ihrem Data Lake Analytics-Konto.

2. Klicken Sie auf **Neuer Auftrag**. Konfigurieren Sie für jeden Auftrag Folgendes:

    1. **Auftragsname**: Der Name des Auftrags.
    2. **Priorität**: Niedrigere Zahlen stehen für eine höhere Priorität. Wenn sich zwei Aufträge in der Warteschlange befinden, wird zuerst der Auftrag mit dem niedrigeren Prioritätswert ausgeführt.
    3. **Parallelität**: Die maximale Anzahl von Computevorgängen, die für diesen Auftrag reserviert werden sollen.

3. Klicken Sie auf **Auftrag senden**.

### <a name="monitor-jobs"></a>Überwachen von Aufträgen

1. Gehen Sie im Azure-Portal zu Ihrem Data Lake Analytics-Konto.
2. Klicken Sie auf **Alle Aufträge anzeigen**. Eine Liste mit allen aktiven und kürzlich abgeschlossenen Aufträgen im Konto wird angezeigt.
3. Klicken Sie optional auf **Filtern**, um die Aufträge nach den Werten **Zeitbereich**, **Auftragsname** und **Ersteller** zu suchen. 

### <a name="monitoring-pipeline-jobs"></a>Überwachen von Pipelineaufträgen
Aufträge, die Teil einer Pipeline sind, arbeiten zusammen – in der Regel sequenziell –, um ein bestimmtes Szenario zu erfüllen. Sie können beispielsweise eine Pipeline für Bereinigung, Extraktion, Transformation und Aggregation des Verbrauchs verwenden, um Einblicke in das Kundenverhalten zu gewinnen. Pipelineaufträge können über die Pipeline-Eigenschaft identifiziert werden, wenn der Auftrag übermittelt wurde. Mit ADF V2 geplanten Aufträgen wird diese Eigenschaft automatisch hinzugefügt. 

So zeigen Sie eine Liste der U-SQL-Aufträge an, die zu Pipelines gehören 

1. Navigieren Sie im Azure-Portal zu Ihrem Data Lake Analytics-Konto.
2. Klicken Sie auf **Einblick in Aufträge**. Standardmäßig wird die Registerkarte „Alle Aufträge“ angezeigt, die eine Liste der Aufträge enthält, die ausgeführt werden, sich in der Warteschlange befinden oder beendet wurden.
3. Klicken Sie auf die Registerkarte **Pipelineaufträge**. Eine Liste der Pipelineaufträge wird zusammen mit aggregierten Statistiken für jede Pipeline angezeigt.

### <a name="monitoring-recurring-jobs"></a>Überwachen von wiederkehrenden Aufträgen
Ein wiederkehrender Auftrag besitzt die gleiche Geschäftslogik, verwendet jedoch bei jeder Ausführung unterschiedliche Eingabedaten. Im Idealfall sollten wiederkehrende Aufträge immer erfolgreich und in einer relativ einheitlichen Ausführungszeit ausgeführt werden. Durch die Überwachung dieses Verhaltens stellen Sie sicher, dass der Auftrag fehlerfrei ist. Wiederkehrende Aufträge können mithilfe der Recurrence-Eigenschaft identifiziert werden. Mit ADF V2 geplanten Aufträgen wird diese Eigenschaft automatisch hinzugefügt.

So zeigen Sie eine Liste der wiederkehrenden U-SQL-Aufträge an 

1. Navigieren Sie im Azure-Portal zu Ihrem Data Lake Analytics-Konto.
2. Klicken Sie auf **Einblick in Aufträge**. Standardmäßig wird die Registerkarte „Alle Aufträge“ angezeigt, die eine Liste der Aufträge enthält, die ausgeführt werden, sich in der Warteschlange befinden oder beendet wurden.
3. Klicken Sie auf die **Wiederholte Aufträge**. Eine Liste der wiederkehrenden Aufträge wird zusammen mit aggregierten Statistiken zu jedem wiederkehrenden Auftrag angezeigt.

## <a name="next-steps"></a>Nächste Schritte

* [Azure Data Lake Analytics – Übersicht](data-lake-analytics-overview.md)
* [Verwalten von Azure Data Lake Analytics mithilfe von Azure PowerShell](data-lake-analytics-manage-use-powershell.md)
* [Verwalten von Azure Data Lake Analytics mit Richtlinien](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-policies)
