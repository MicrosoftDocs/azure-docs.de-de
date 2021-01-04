---
title: 'Tutorial: Konfigurieren von Apache Ambari-E-Mail-Benachrichtigungen in Azure HDInsight'
description: In diesem Artikel wird beschrieben, wie Sie SendGrid mit Apache Ambari für E-Mail-Benachrichtigungen verwenden.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: tutorial
ms.date: 03/10/2020
ms.openlocfilehash: bdce735bdacbe7ff0752650c6949fdb361342c73
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92542555"
---
# <a name="tutorial-configure-apache-ambari-email-notifications-in-azure-hdinsight"></a>Tutorial: Konfigurieren von Apache Ambari-E-Mail-Benachrichtigungen in Azure HDInsight

In diesem Tutorial konfigurieren Sie Apache Ambari-E-Mail-Benachrichtigungen mithilfe von SendGrid. [Apache Ambari](./hdinsight-hadoop-manage-ambari.md) vereinfacht die Verwaltung und Überwachung von HDInsight-Clustern durch die Bereitstellung einer benutzerfreundlichen Webbenutzeroberfläche und REST-API. Ambari ist in HDInsight-Clustern enthalten und wird verwendet, um den Cluster zu überwachen und Konfigurationsänderungen vorzunehmen. [SendGrid](https://sendgrid.com/solutions/) ist ein kostenloser cloudbasierter E-Mail-Dienst, der zuverlässige transaktionale E-Mail-Übermittlung, Skalierbarkeit und Echtzeitanalysen mit flexiblen APIs bietet, die die benutzerdefinierte Integration erleichtern. Kunden von Azure können monatlich 25.000 kostenlose E-Mails freischalten.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Abrufen des Sendgrid-Benutzernamens
> * Konfigurieren von Apache Ambari-E-Mail-Benachrichtigungen

## <a name="prerequisites"></a>Voraussetzungen

* SendGrid-E-Mail-Konto. Entsprechende Anweisungen finden Sie unter [Senden von E-Mails in Azure mit SendGrid](../sendgrid-dotnet-how-to-send-email.md).

* Einen HDInsight-Cluster Siehe [Erstellen von Apache Hadoop-Clustern im Azure-Portal](./hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="obtain-sendgrid-username"></a>Abrufen des SendGrid-Benutzernamens

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrer SendGrid-Ressource.

1. Wählen Sie auf der Übersichtsseite **Verwalten** aus, um zur SendGrid-Webseite für Ihr Konto zu navigieren.

    ![SendGrid-Übersicht im Azure-Portal](./media/apache-ambari-email/azure-portal-sendgrid-manage.png)

1. Navigieren Sie im Menü auf der linken Seite zu Ihrem Kontonamen und dann zu **Kontodetails** .

    ![Navigation auf dem SendGrid-Dashboard](./media/apache-ambari-email/sendgrid-dashboard-navigation.png)

1. Notieren Sie auf der Seite **Kontodetails** den Wert unter **Benutzername** .

    ![SendGrid-Kontodetails](./media/apache-ambari-email/sendgrid-account-details.png)

## <a name="configure-ambari-e-mail-notification"></a>Konfigurieren einer Ambari-E-Mail-Benachrichtigung

1. Navigieren Sie in einem Webbrowser zu `https://CLUSTERNAME.azurehdinsight.net/#/main/alerts`, wobei `CLUSTERNAME` der Name Ihres Clusters ist.

1. Wählen Sie im Dropdownmenü **Aktionen** den Eintrag **Manage Notifications** (Benachrichtigungen verwalten) aus.

1. Wählen Sie im Fenster **Manage Alert Notifications** (Warnungsbenachrichtigungen verwalten) das Symbol **+** aus.

    ![Screenshot: Dialogfeld „Manage Alert Notifications“ (Warnungsbenachrichtigungen verwalten)](./media/apache-ambari-email/azure-portal-create-notification.png)

1. Geben Sie im Dialogfenster **Create Alert Notification** (Warnungsbenachrichtigung erstellen) die folgenden Informationen an:

    |Eigenschaft |BESCHREIBUNG |
    |---|---|
    |Name|Geben Sie einen Namen für die Benachrichtigung ein.|
    |Gruppen|Nehmen Sie die Konfiguration nach Bedarf vor.|
    |severity|Nehmen Sie die Konfiguration nach Bedarf vor.|
    |BESCHREIBUNG|Optional.|
    |Methode|Übernehmen Sie die Option **E-MAIL** .|
    |Email To (E-Mail an)|Geben Sie E-Mail-Adressen getrennt durch Komma ein, an die Benachrichtigungen gesendet werden sollen.|
    |SMTP-Server|`smtp.sendgrid.net`|
    |SMTP-Port|25 oder 587 (für unverschlüsselte Verbindungen oder TLS-Verbindungen)|
    |Email from (E-Mail von)|Geben Sie eine E-Mail-Adresse an. Die Adresse muss nicht echt sein.|
    |Verwenden der Authentifizierung|Aktivieren Sie dieses Kontrollkästchen.|
    |Username|Geben Sie den SendGrid-Benutzernamen an.|
    |Kennwort|Geben Sie das Kennwort an, das Sie beim Erstellen der SendGrid-Ressource in Azure verwendet haben.|
    |Kennwortbestätigung|Geben Sie das Kennwort erneut ein.|
    |Start TLS (TLS starten)|Aktivieren Sie dieses Kontrollkästchen.|

    ![Screenshot: Create Alert Notification (Warnungsbenachrichtigung erstellen)](./media/apache-ambari-email/ambari-create-alert-notification.png)

    Wählen Sie **Speichern** aus. Sie kehren zum Fenster **Manage Alert Notifications** (Warnungsbenachrichtigungen verwalten) zurück.

1. Wählen Sie im Fenster **Manage Alert Notifications** (Warnungsbenachrichtigungen verwalten) die Option **Schließen** aus.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie gelernt, wie Sie Apache Ambari-E-Mail-Benachrichtigungen mithilfe von SendGrid konfigurieren. In den folgenden Artikeln erhalten Sie weitere Informationen zu Apache Ambari:

* [Verwalten von HDInsight-Clustern mithilfe der Apache Ambari-Webbenutzeroberfläche](./hdinsight-hadoop-manage-ambari.md)

* [Erstellen einer Warnungsbenachrichtigung](https://docs.cloudera.com/HDPDocuments/Ambari-latest/managing-and-monitoring-ambari/content/amb_create_an_alert_notification.html)