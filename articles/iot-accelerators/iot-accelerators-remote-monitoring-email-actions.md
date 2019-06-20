---
title: 'E-Mail-Aktion innerhalb der Remoteüberwachung: Azure | Microsoft-Dokumentation'
description: In dieser Schrittanleitung erfahren Sie, wie Sie einer neuen oder vorhandenen Regel eine E-Mail-Aktion hinzufügen.
author: asdonald
manager: hegate
ms.author: asdonald
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/12/2018
ms.topic: conceptual
ms.openlocfilehash: fbb5f92258ff31dd7077bb1ade7fa7e5644c8bac
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65466935"
---
# <a name="add-an-email-action"></a>Hinzufügen einer E-Mail-Aktion

Mithilfe von E-Mail-Aktionen können Sie sicherstellen, dass Sie keine Warnungen verpassen. Sie können eine E-Mail-Aktion zu einer vorhandenen Regel oder beim Erstellen einer neuen Regel hinzufügen.

Für diese Schrittanleitung benötigen Sie eine bereitgestellte Instanz des Solution Accelerators für die Remoteüberwachung in Ihrem Azure-Abonnement.

Zum Erstellen oder Ändern einer Regel müssen Sie [**Administrator** sein oder über die korrekten Berechtigungen verfügen](iot-accelerators-remote-monitoring-rbac.md).

## <a name="edit-an-existing-rule"></a>Bearbeiten einer vorhandenen Regel

Führen Sie zum Hinzufügen einer E-Mail-Aktion zu einer vorhandenen Regel die folgenden Schritte aus:

1. Navigieren Sie zu Ihrer Remoteüberwachungslösung.

1. Navigieren Sie auf dem **Dashboard** zur Seite **Regeln**:

    ![Seite „Regeln“](./media/iot-accelerators-remote-monitoring-email-actions/rules-email.png)

1. Aktivieren Sie das Kontrollkästchen neben der vorhandenen Regel, die Sie ändern möchten, und klicken Sie dann oben auf **Bearbeiten**. Ein bearbeitbarer Bereich für die **Regel** wird angezeigt.

1. Schalten Sie im Abschnitt **Aktion** die Option **Email enabled** (E-Mail aktiviert) auf **Ein** um.

1. Bei der ersten Aktivierung einer E-Mail-Aktion im Solution Accelerator müssen Sie sich [bei Outlook anmelden](#outlook).

1. Geben Sie im Feld für den E-Mail-Empfänger eine E-Mail-Adresse ein, und drücken Sie für jede hinzuzufügende E-Mail-Adresse die **EINGABETASTE**:

    ![Adresseintrag](./media/iot-accelerators-remote-monitoring-email-actions/address-email.png)

1. Geben Sie einen Betreff für die E-Mail ein.

1. Geben Sie zusätzliche Hinweise für die E-Mail-Empfänger als Nur-Text ein. Sie können HTML-Formatierung verwenden, wenn Sie die [E-Mail-Vorlage bearbeiten](#htmledit).

1. Vergewissern Sie sich, dass der **Regelstatus** auf **Aktiviert** festgelegt ist.

1. Klicken Sie auf **Anwenden**.

## <a name="create-a-new-rule"></a>Neue Regel erstellen

Führen Sie die folgenden Schritte aus, um beim Erstellen einer neuen Regel eine E-Mail-Aktion hinzuzufügen:

1. Navigieren Sie zu Ihrer Remoteüberwachungslösung.

1. Navigieren Sie auf dem **Dashboard** zur Seite **Regeln**:

    ![Seite „Regeln“](./media/iot-accelerators-remote-monitoring-email-actions/rules-email.png)

1. Führen Sie die Schritte im Abschnitt [Erstellen einer Regel](iot-accelerators-remote-monitoring-automate.md#create-a-rule) aus. Führen Sie anschließend die Schritte im Abschnitt [Erstellen einer erweiterten Regel](iot-accelerators-remote-monitoring-automate.md#create-an-advanced-rule) bis zu dem Punkt aus, in dem Sie einen **Schweregrad** festlegen. Klicken Sie noch nicht auf **Übernehmen**.

1. Schalten Sie im Abschnitt **Aktion** die Option **Email enabled** (E-Mail aktiviert) auf **Ein** um.

1. Bei der ersten Aktivierung einer E-Mail-Aktion im Solution Accelerator müssen Sie sich [bei Outlook anmelden](#outlook).

1. Geben Sie im Feld für den E-Mail-Empfänger eine E-Mail-Adresse ein, und drücken Sie für jede hinzuzufügende E-Mail-Adresse die **EINGABETASTE**:

    ![Adresseintrag](./media/iot-accelerators-remote-monitoring-email-actions/address-email.png)

1. Geben Sie einen Betreff für die E-Mail ein.

1. Geben Sie zusätzliche Hinweise für die E-Mail-Empfänger als Nur-Text ein. Sie können HTML-Formatierung verwenden, wenn Sie die [E-Mail-Vorlage bearbeiten](#htmledit).

1. Vergewissern Sie sich, dass der **Regelstatus** auf **Aktiviert** festgelegt ist.

1. Klicken Sie auf **Anwenden**.

Ihre Regel mit einer E-Mail-Aktion ist jetzt aktiviert. Bei jeder Auslösung der Aktion wird eine neue E-Mail an die Empfänger gesendet.

## Anmelden bei Outlook <a name="outlook"></a>

Bei der ersten Aktivierung einer E-Mail-Aktion im Solution Accelerator müssen Sie sich bei Outlook anmelden. Mit dieser Aktion wird das E-Mail-Konto eingerichtet, das die E-Mail-Benachrichtigungen sendet.

> [!NOTE]
> Es wird empfohlen, ein spezifisches Outlook-Konto nur für Solution Accelerator-Benachrichtigungen zu erstellen und dieses Konto beim Aktivieren Ihrer ersten E-Mail-Aktion zu verwenden.

### <a name="contributor-role-outlook-setup"></a>Outlook-Einrichtung für die Rolle „Mitwirkender“

Falls ein Benutzer mit der Rolle **Mitwirkender** im Abonnement den Solution Accelerator bereitgestellt hat, besitzt die Anwendung keine ausreichenden Berechtigungen zum Einrichten und Überprüfen von E-Mail-Aktionen über die Webbenutzeroberfläche.

Erstellen Sie ein Outlook-Konto zum Senden von E-Mail-Benachrichtigungen über den Solution Accelerator, bevor Sie beginnen.

In den folgenden Schritten wird gezeigt, wie Sie die E-Mail-Aktionen manuell einrichten und überprüfen:

1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com).

1. Navigieren Sie zu der Ressourcengruppe für Ihren Solution Accelerator.

1. Klicken Sie auf **office365-connector**:

    ![APIConnection](./media/iot-accelerators-remote-monitoring-email-actions/apiconnector1.png)

1. Klicken Sie auf das Banner, um die Autorisierung zu starten:

    ![Autorisieren](./media/iot-accelerators-remote-monitoring-email-actions/connector1.png)

1. Klicken Sie auf **Autorisieren**. Sie werden zur Anmeldung aufgefordert. Bei dem für die Anmeldung verwendeten Konto muss es sich um die E-Mail-Adresse handeln, die von der Anwendung zum Senden von E-Mail-Benachrichtigungen verwendet wird:

    ![Schaltfläche „Autorisieren“](./media/iot-accelerators-remote-monitoring-email-actions/authorize.png)

1. Klicken Sie am unteren Rand auf **Speichern**. Die Autorisierung war erfolgreich, wenn das Banner nicht mehr angezeigt wird.

1. Wenn Sie die E-Mail-Adresse ändern möchten, von der die Benachrichtigungen gesendet werden, klicken Sie auf **API-Verbindung bearbeiten**.

    ![Ändern der E-Mail-Adresse](./media/iot-accelerators-remote-monitoring-email-actions/editemail1.png)

### <a name="owner-role-outlook-setup"></a>Outlook-Einrichtung für die Rolle „Besitzer“

Falls ein Benutzer mit der Rolle **Besitzer** im Abonnement den Solution Accelerator bereitgestellt hat, kann die Anwendung über die Webbenutzeroberfläche überprüfen, ob E-Mail-Aktionen korrekt eingerichtet wurden.

Erstellen Sie ein Outlook-Konto zum Senden von E-Mail-Benachrichtigungen über den Solution Accelerator, bevor Sie beginnen.

Mit den folgenden Schritten können Sie sich anmelden und E-Mail-Aktionen einrichten:

1. Klicken Sie, um sich bei Outlook anzumelden. Sie gelangen zum Azure-Portal:

   ![Anmelden bei Outlook](./media/iot-accelerators-remote-monitoring-email-actions/owneroutlook-email.png)

1. Klicken Sie auf **Autorisieren**. Sie werden zur Anmeldung aufgefordert. Bei dem für die Anmeldung verwendeten Konto muss es sich um die E-Mail-Adresse handeln, die von der Anwendung zum Senden von E-Mail-Benachrichtigungen verwendet wird:

1. Klicken Sie auf **Speichern**. Navigieren Sie zurück zum Solution Accelerator, und aktualisieren Sie die Seite.

1. Wurde die E-Mail-Benachrichtigung erfolgreich konfiguriert, wird die folgende Meldung angezeigt:

   ![Erfolgreiche Outlook-Anmeldung](./media/iot-accelerators-remote-monitoring-email-actions/success-email.png)

## Anpassen der E-Mail-HTML <a name="htmledit"></a>

Der vorkonfigurierte Solution Accelerator für die Remoteüberwachung stellt eine einfache HTML-Vorlage für Aktions-E-Mails bereit. In der E-Mail-Vorlage werden Werte aus den Einstellungen der E-Mail-Aktionen verwendet. Hier sehen Sie eine Beispiel-E-Mail:

![E-Mail-Beispiel](./media/iot-accelerators-remote-monitoring-email-actions/emailtemplate1.png)

In den folgenden Schritte wird gezeigt, wie Sie die HTML-E-Mail-Vorlage bearbeiten. Sie können beispielsweise weitere Informationen aufnehmen oder benutzerdefinierte Bilder hinzufügen:

1. Klonen Sie das GitHub-Repository für die Remoteüberwachung mit Java oder .NET:

1. Navigieren Sie zum Speicherort der E-Mail-Vorlage:
  
    `Dotnet: device-telemetry\ActionsAgent\data\EmailTemplate.html`
  
    `Java device-telemetry/app/resources/data/EmailTemplate.html`

1. Zum Anpassen der Nachricht können Sie in dieser Vorlage Parameter hinzufügen oder entfernen. Sie können auch nach Bedarf Aufrufe hinzufügen, entfernen oder ersetzen:

    Beispiel für den .NET-Code: `emailTemplate = emailTemplate.Replace("${subject}", emailAction.GetSubject());`

    Beispiel für den Java-Code: `this.emailTemplate.replace("${subject}", emailAction.GetSubject());`

1. Parameter in der Vorlage haben das Format `${...}`. Um einen Parameter zu löschen, löschen Sie die erforderliche Zeile. Um einen Parameter hinzuzufügen, fügen Sie eine Zeile mit dem einzufügenden Wert hinzu.

1. Wenn Sie Bilder oder benutzerdefinierten Text hinzufügen möchten, aktualisieren Sie direkt die Datei „EmailTemplate.HTML“.

## <a name="throttling"></a>Drosselung

Der Solution Accelerator für die Remoteüberwachung nutzt Outlook zum Senden von E-Mail-Benachrichtigungen. Outlook beschränkt die Anzahl gesendeter E-Mails auf [30 E-Mails pro Minute](https://docs.microsoft.com/office365/servicedescriptions/exchange-online-service-description/exchange-online-limits#receiving-and-sending-limits). E-Mail-Clients, die die E-Mails empfangen, drosseln unter Umständen auch die Anzahl der pro Minute empfangenen E-Mails. Informieren Sie sich über die Einschränkungen Ihres spezifischen E-Mail-Clients. Wenn Sie eine E-Mail-Benachrichtigung für eine Regel einrichten, sollte die Regel keine Sofortwerte verwenden, sondern Durchschnittswerte für einen Zeitraum von mindestens einer Minute berechnen:

![Durchschnittsberechnung](./media/iot-accelerators-remote-monitoring-email-actions/calculation-email.png)

## <a name="next-steps"></a>Nächste Schritte

In diesem Handbuch wurde gezeigt, wie Sie in einer Remoteüberwachungslösung einer neuen oder vorhandenen Regel eine E-Mail-Aktion hinzufügen. Darüber hinaus haben Sie erfahren, wie die Sie HTML bearbeiten, die das Nachrichtenformat bestimmt.

Als nächster Schritt wird empfohlen, sich mit dem [Verwenden von Warnungen und Beheben von Geräteproblemen](iot-accelerators-remote-monitoring-maintain.md) vertraut zu machen.
