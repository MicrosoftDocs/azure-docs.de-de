---
title: Einrichten und Konfigurieren der Integration von AWS-Kosten- und Nutzungsberichten in Azure Cost Management
description: Dieser Artikel erläutert das Einrichten und Konfigurieren der Integration von AWS-Kosten- und Nutzungsberichten in Azure Cost Management.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/21/2019
ms.topic: conceptual
ms.service: cost-management
manager: ormaoz
ms.custom: ''
ms.openlocfilehash: 951178a82e0975f5f2af71bd48cf0f931246ae37
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/22/2019
ms.locfileid: "66002124"
---
# <a name="set-up-and-configure-aws-cost-and-usage-report-integration"></a>Einrichten und Konfigurieren der Integration von AWS-Kosten- und Nutzungsberichten

Mit der Integration der Kosten- und Nutzungsberichte (Cost and Usage Report, CUR) von Amazon Web Services (AWS) können Sie Ihre AWS-Ausgaben in Azure Cost Management überwachen und steuern. Die Integration bietet Ihnen die Möglichkeit, Ausgaben für Azure und AWS zentral im Azure-Portal zu überwachen und zu steuern. In diesem Artikel wird erläutert, wie Sie die Integration einrichten und so konfigurieren, dass Sie die Azure Cost Management-Funktionen zur Kostenanalyse und Budgetüberprüfung nutzen können.

Cost Management verarbeitet den in einem S3-Bucket gespeicherten AWS-Kosten- und Nutzungsbericht, indem es Ihre AWS-Anmeldeinformationen verwendet, um Berichtsdefinitionen abzurufen und GZIP-CSV-Berichtsdateien herunterzuladen.

## <a name="create-a-cost-and-usage-report-in-aws"></a>Erstellen eines Kosten- und Nutzungsberichts in AWS

AWS empfiehlt zur Erfassung und Verarbeitung von AWS-Kosten die Verwendung eines Kosten- und Nutzungsberichts. Weitere Informationen finden Sie in der Dokumentation zum [AWS Cost and Usage Report](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/billing-reports-costusage.html) (AWS-Kosten- und Nutzungsbericht).

Verwenden Sie die Seite **Cost & Usage Reports** (Kosten- und Nutzungsberichte) der Billing and Cost Management Console in AWS, um mit den folgenden Schritten einen Kosten- und Nutzungsbericht zu erstellen:

1. Melden Sie sich bei der AWS Management Console an, und öffnen Sie die [Billing and Cost Management Console](https://console.aws.amazon.com/billing) (Abrechnungs- und Kostenverwaltungskonsole).
2. Wählen Sie im Navigationsbereich **Cost & Usage Reports** aus.
3. Wählen Sie **Create report** (Bericht erstellen) aus.
4. Geben Sie für **Report name** (Berichtsname) einen Namen für den Bericht ein.
5. Wählen Sie für **Additional report details** (Zusätzliche Berichtsdetails) **Include Resource IDs** (Ressourcen-IDs einbeziehen) aus, um die IDs der einzelnen Ressourcen in den Bericht einzubeziehen.
6. Wählen Sie für **Data refresh settings** (Datenaktualisierungseinstellungen) aus, ob der AWS-Kosten- und Nutzungsbericht aktualisiert werden soll, wenn AWS nach der abschließenden Bearbeitung Ihrer Rechnung Rückerstattungen, Gutschriften oder Supportgebühren auf Ihrem Konto verbucht. Wenn ein Bericht aktualisiert wird, wird ein neuer Bericht auf Amazon S3 hochgeladen. Sie sollten diese Einstellung aktiviert lassen.
7. Klicken Sie auf **Weiter**.
8. Wählen Sie für **S3 bucket** (S3-Bucket) die Option **Configure** (Konfigurieren) aus.
9. Führen Sie im Dialogfeld „Configure S3 Bucket“ (S3-Bucket konfigurieren) eine der folgenden Aktionen aus:
    1. Wählen Sie aus der Dropdownliste einen vorhandenen Bucket aus, und wählen Sie dann **Next** (Weiter) aus.
    2. Geben Sie einen Bucketnamen und die Region ein, in der Sie einen neuen Bucket erstellen möchten, und wählen Sie dann **Next** aus.
10. Wählen Sie „I have confirmed that this policy is correct“ (Ich habe bestätigt, dass diese Richtlinie richtig ist) und dann „Save“ (Speichern) aus.
11. (Optional) Geben Sie unter „Report path prefix“ (Berichtspfadpräfix) das Berichtspfadpräfix ein, das dem Namen Ihres Berichts vorangestellt werden soll.
Wenn Sie kein Präfix angeben, wird das Standardpräfix aus dem von Ihnen in Schritt 4 für den Bericht angegebenen Namen und dem Datumsbereich für den Bericht gebildet, in folgendem Format: `/report-name/date-range/`
12. Für **Zeiteinheit** wählen Sie **Stündlich** aus.
13. Wählen Sie für **Report versioning** (Berichtsversionsverwaltung) aus, ob jede neue Berichtsversion die vorherige Berichtsversion überschreiben oder zusätzlich zu den vorherigen Versionen zur Verfügung gestellt werden soll.
14. Für **Enable data integration for** (Datenintegration aktivieren für) ist keine Auswahl erforderlich.
15. Für **Komprimierung** wählen Sie die Option **GZIP** aus.
16. Klicken Sie auf **Weiter**.
17. Nachdem Sie die Einstellungen für Ihren Bericht überprüft haben, wählen Sie **Review and Complete** (Überprüfen und Fertigstellen) aus.

    Notieren Sie sich den Berichtsnamen. Sie werden ihn später benötigen.

Es kann bis zu 24 Stunden dauern, bis AWS mit der Übermittlung von Berichten an Ihren Amazon S3-Bucket beginnt. Nach dem Start der Übermittlung aktualisiert AWS mindestens einmal täglich die Dateien für den AWS-Kosten- und Nutzungsbericht. Sie können Ihre AWS-Umgebung weiter konfigurieren, ohne auf den Start der Übermittlung zu warten.

## <a name="create-a-role-and-policy-in-aws"></a>Erstellen einer Rolle und Richtlinie in AWS

Azure Cost Management greift mehrmals täglich auf den S3-Bucket zu, in dem sich der Kosten- und Nutzungsbericht befindet. Der Dienst benötigt Zugriff auf Anmeldeinformationen, um zu überprüfen, ob neue Daten vorliegen. Sie erstellen eine Rolle und eine Richtlinie in AWS, um Cost Management den Zugriff darauf zu erlauben.

Um den rollenbasierten Zugriff auf ein AWS-Konto im Cost Management zu ermöglichen, wird die Rolle in der AWS-Konsole angelegt. Sie benötigen _Rollen-ARN_ und _externe ID_ aus der AWS-Konsole. Diese Angaben benötigen Sie später auf der Seite **AWS-Connector erstellen** in Azure Cost Management.

Verwenden Sie den Assistenten „Create a New Role“ (Eine neue Rolle erstellen):

1. Melden Sie sich bei Ihrer AWS-Konsole an, und wählen Sie **Services** (Dienste) aus.
2. Wählen Sie in der Liste der Dienste **IAM** aus.
3. Wählen Sie **Roles** (Rollen) und dann **Create Role** (Rolle erstellen) aus.
4. Wählen Sie auf der nächsten Seite **Another AWS account** (Ein anderes AWS-Konto) aus.
5. Geben Sie als **Account ID** (Konto-ID) **432263259397** ein.
6. Wählen Sie unter **Optionen** **Externe ID erfordern (Best Practice ist, wenn ein Dritter diese Rolle übernimmt)** .
7. Geben Sie in **External ID** (Externe ID) die externe ID ein. Die externe ID ist ein gemeinsames Kennwort für die AWS-Rolle und Azure Cost Management. Die gleiche externe ID wird auch auf der Seite **Neuer Connector** in Cost Management verwendet. Eine externe ID wäre beispielsweise _Companyname1234567890123_.

    > [!NOTE]
    > Ändern Sie nicht die Auswahl für **Require MFA** (MFA anfordern). Dieses Feld sollte leer bleiben.
8. Klicken Sie auf **Weiter: Permissions** (Weiter: Berechtigungen).
9. Wählen Sie **Create policy** (Richtlinie erstellen) aus. Im Browser wird eine neue Registerkarte geöffnet. Dort erstellen Sie eine Richtlinie.
10. Wählen Sie **Choose a service** (Dienst auswählen) aus.

Konfigurieren Sie die Berechtigung für den Kosten- und Nutzungsbericht:

1. Geben Sie **Cost and Usage Report** (Kosten- und Nutzungsbericht) ein.
2. Wählen Sie **Access level** (Zugriffsebene) > **Read** (Lesen) > **DescribeReportDefinitions** aus. Dieser Schritt ermöglicht Cost Management zu lesen, welche CUR-Berichte definiert sind, und festzustellen, ob sie der Voraussetzung für die Berichtsdefinition entsprechen.
3. Wählen Sie **Add additional permissions** (Zusätzliche Berechtigungen hinzufügen) aus.

Konfigurieren Sie die Berechtigung für Ihren S3-Bucket und Objekte:

1. Wählen Sie **Choose a service** (Dienst auswählen) aus.
2. Geben Sie **S3** ein.
3. Wählen Sie **Access level** > **List** (Liste) > **ListBucket** aus. Diese Aktion ruft die Liste der Objekte im S3-Bucket ab.
4. Wählen Sie **Access level** > **Read** > **GetObject** aus. Diese Aktion ermöglicht das Herunterladen der Abrechnungsdateien.
5. Wählen Sie **Ressourcen** aus.
6. Wählen Sie **Bucket – ARN hinzufügen** aus.
7. Geben Sie unter **Bucketname** den Bucket ein, der zum Speichern der CUR-Dateien verwendet wird.
8. Wählen Sie **Objekt – ARN hinzufügen** aus.
9. Geben Sie unter **Bucketname** den Bucket ein, der zum Speichern der CUR-Dateien verwendet wird.
10. Wähle Sie unter **Objektname** die Option **Alle**.
11. Wählen Sie **Add additional permissions** (Zusätzliche Berechtigungen hinzufügen) aus.

Konfigurieren Sie die Berechtigung für Cost Explorer:

1. Wählen Sie **Choose a service** (Dienst auswählen) aus.
2. Geben Sie **Cost Explorer Service** ein.
3. Wählen Sie **Alle Cost Explorer Service-Aktionen (ce:\*) aus**. Diese Aktion überprüft, ob die Sammlung richtig ist.
4. Wählen Sie **Add additional permissions** (Zusätzliche Berechtigungen hinzufügen) aus.

Fügen Sie die Berechtigung für AWS-Organisationen hinzu:

1. Geben Sie **Organizations** (Organisationen) ein.
2. Wählen Sie **Access level** > **List** > **ListAccounts** aus. Diese Aktion ruft die Namen der Konten ab.
3. Geben Sie unter **Überprüfungsrichtlinie** einen Namen für die neue Richtlinie ein. Überprüfen Sie, ob Sie die richtigen Informationen eingegeben haben, und wählen Sie dann **Create Policy** (Richtlinie erstellen) aus.
4. Gehen Sie zur vorherigen Registerkarte zurück, und aktualisieren Sie die Webseite Ihres Browsers. Suchen Sie über die Suchleiste nach der neuen Richtlinie.
5. Wählen Sie **Weiter: Überprüfung**.
6. Geben Sie einen Namen für die neue Rolle ein. Überprüfen Sie, ob Sie die richtigen Informationen eingegeben haben, und wählen Sie dann **Create Role** (Rolle erstellen) aus.

    Notieren Sie sich die Rollen-ARN und die externe ID, die in den vorherigen Schritten beim Erstellen der Rolle verwendet wurden. Sie werden diese später verwenden, wenn Sie den Azure Cost Management-Connector einrichten.

Der JSON-Code der Richtlinie sollte wie im folgenden Beispiel aussehen: Ersetzen Sie _bucketname_ durch den Namen Ihres S3-Buckets.

```JSON
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "VisualEditor0",
            "Effect": "Allow",
            "Action": [
"organizations:ListAccounts",
            "ce:*",
            "cur:DescribeReportDefinitions"
            ],
            "Resource": "*"
        },
        {
            "Sid": "VisualEditor1",
            "Effect": "Allow",
            "Action": [
                "s3:GetObject",
                "s3:ListBucket"
            ],
            "Resource": [
                "arn:aws:s3:::bucketname",
                "arn:aws:s3:::bucketname/*"
            ]
        }
    ]
}
```

## <a name="set-up-a-new-aws-connector-in-azure"></a>Einrichten eines neuen AWS-Connectors in Azure

Verwenden Sie die folgenden Informationen, um einen AWS-Connector zu erstellen und mit der Überwachung Ihrer AWS-Kosten zu beginnen:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Navigieren Sie zu **Cost Management + Abrechnung** > **Cost Management**.
3. Wählen Sie unter **Einstellungen** die Option **Cloudconnectors (Vorschau)** aus.  
    ![Beispiel für die Einstellung „Cloudconnectors (Vorschau)“](./media/aws-integration-setup-configure/cloud-connectors-preview01.png).
4. Wählen Sie **+ Hinzufügen** oben auf der Seite aus, um einen Connector zu erstellen.
5. Geben Sie auf der Seite **AWS-Connector erstellen** einen **Anzeigenamen** ein, um Ihren Connector zu benennen.  
    ![Beispiel für die Seite zum Erstellen eines AWS-Connectors](./media/aws-integration-setup-configure/create-aws-connector01.png)
6. Wählen Sie optional die Standardverwaltungsgruppe aus. Darin werden alle erkannten verknüpftem Konten gespeichert. Sie können sie später einrichten.
7. Wählen Sie im Abschnitt **Abrechnung** die Option **1% bei allgemeiner Verfügbarkeit automatisch abrechnen** aus, wenn Sie den kontinuierlichen Betrieb nach Ablauf der Vorschau sicherstellen möchten. Wenn Sie die automatische Option auswählen, müssen Sie ein Abrechnungsabonnement auswählen.
8. Geben Sie für **ARN-Rolle** den Wert ein, den Sie beim Einrichten der Rolle in AWS verwendet haben.
9. Geben Sie für **Externe ID** den Wert ein, den Sie beim Einrichten der Rolle in AWS verwendet haben.
10. Geben Sie für **Berichtsnamen** den Namen ein, den Sie in AWS erstellt haben.
11. Wählen Sie **Weiter** und anschließend **Erstellen** aus.

Es kann einige Stunden dauern, bis die neuen AWS-Bereiche, das konsolidierte AWS-Konto und die verknüpften AWS-Konten sowie deren Kostendaten angezeigt werden.

Nachdem Sie den Connector erstellt haben, sollten Sie dem Connector eine Zugriffssteuerung zuweisen. Benutzern werden Berechtigungen für die neu erkannten Bereiche zugewiesen: Konsolidiertes AWS-Konto und verknüpfte AWS-Konten. Der Benutzer, der den Connector erstellt, ist der Besitzer des Connectors, des konsolidierten Kontos und aller verknüpften Konten.

Wenn den Benutzern nach dem Erkennen Connectorberechtigungen zugewiesen werden, werden den vorhandenen AWS-Bereichen keine Berechtigungen zugewiesen. Stattdessen werden nur neu verknüpften Konten Berechtigungen zugewiesen.

## <a name="take-additional-steps"></a>Durchführen zusätzlicher Schritte

- [Richten Sie Verwaltungsgruppen ein](../governance/management-groups/index.md#initial-setup-of-management-groups), sofern das noch nicht geschehen ist.
- Stellen Sie sicher, dass Ihrer Bereichsauswahl neue Bereiche hinzugefügt wurden. Wählen Sie **Aktualisieren** aus, um die neuesten Daten anzuzeigen.
- Wählen Sie auf der Seite **Cloudconnectors** Ihren Connector aus, und klicken Sie auf **Zu Abrechnungskonto wechseln**, um das verknüpfte Konto Verwaltungsgruppen zuzuweisen.

## <a name="manage-cloud-connectors"></a>Verwalten von Cloudconnectors

Wenn Sie auf der Seite **Cloudconnectors** einen Connector auswählen, haben Sie folgende Möglichkeiten:

- Wählen Sie **Zu Abrechnungskonto wechseln** aus, um Informationen zum konsolidierten AWS-Konto anzuzeigen.
- Wählen Sie **Zugriffssteuerung** aus, um die Rollenzuweisung für den Connector zu verwalten.
- Wählen Sie **Bearbeiten** aus, um den Connector zu aktualisieren. Sie können die AWS-Kontonummer nicht ändern, weil sie in der Rollen-ARN angezeigt wird. Sie können jedoch einen neuen Connector erstellen.
- Wählen Sie **Überprüfen** aus, um den Überprüfungstest erneut auszuführen, um sicherzustellen, dass Cost Management Daten mithilfe der Connectoreinstellungen erfassen kann.

![Beispielliste der erstellten AWS-Connectors](./media/aws-integration-setup-configure/list-aws-connectors.png)

## <a name="set-up-azure-management-groups"></a>Einrichten von Azure-Verwaltungsgruppen

Um einen zentralen Ort für die Anzeige von Cloudanbieterinformationen zu erstellen, müssen Sie Ihre Azure-Abonnements und verknüpften AWS-Konten in derselben Verwaltungsgruppe einordnen. Wenn Sie Ihre Azure-Umgebung nicht bereits mit Verwaltungsgruppen konfiguriert haben, finden Sie unter [Erstmalige Einrichtung von Verwaltungsgruppen](../governance/management-groups/index.md#initial-setup-of-management-groups) weitere Informationen.

Wenn Sie die Kosten trennen möchten, können Sie eine Verwaltungsgruppe anlegen, die nur die mit verknüpften AWS-Konten enthält.

## <a name="set-up-an-aws-consolidated-account"></a>Einrichten eines konsolidierten AWS­-Kontos

Im konsolidierten AWS-Konto sind Abrechnungen und Zahlungen für mehrere AWS-Konten kombiniert. Es fungiert außerdem als verknüpftes AWS-Konto.

![Beispieldetails für ein konsolidiertes AWS-Konto](./media/aws-integration-setup-configure/aws-consolidated-account01.png)

Auf der Seite haben Sie folgende Möglichkeiten:

- Wählen Sie **Aktualisieren** aus, um eine Massenaktualisierung der Zuordnung verknüpfter AWS-Konten zu einer Verwaltungsgruppe durchzuführen.
- Wählen Sie **Zugriffssteuerung** aus, um die Rollenzuweisung für den Bereich einzurichten.

### <a name="permissions-for-an-aws-consolidated-account"></a>Berechtigungen für ein konsolidiertes AWS­-Konto

Standardmäßig werden die Berechtigungen für ein konsolidiertes AWS-Konto beim Erstellen des Kontos basierend auf den Berechtigungen des AWS-Connectors festgelegt. Der Ersteller des Connectors ist der Besitzer.

Sie verwalten die Zugriffsebene, indem Sie die Seite **Zugriffsebene** des konsolidierten AWS-Kontos verwenden. Verknüpfte AWS-Konten erben jedoch keine Berechtigungen für das konsolidierte AWS-Konto.

## <a name="set-up-an-aws-linked-account"></a>Einrichten eines verknüpften AWS-Kontos

Im verknüpften AWS-Konto werden AWS-Ressourcen erstellt und verwaltet. Ein verknüpftes Konto fungiert auch als Sicherheitsgrenze.

Auf der Seite haben Sie folgende Möglichkeiten:

- Wählen Sie **Aktualisieren** aus, um eine Aktualisierung der Zuordnung eines verknüpften AWS-Kontos zu einer Verwaltungsgruppe durchzuführen.
- Wählen Sie **Zugriffssteuerung** aus, um eine Rollenzuweisung für den Bereich einzurichten.

![Beispiel für die Seite „Verknüpftes AWS-Konto“](./media/aws-integration-setup-configure/aws-linked-account01.png)

### <a name="permissions-for-an-aws-linked-account"></a>Berechtigungen für ein verknüpftes AWS-Konto

Standardmäßig werden die Berechtigungen für ein verknüpftes AWS-Konto basierend auf den Berechtigungen des AWS-Connectors beim Erstellen festgelegt. Der Ersteller des Connectors ist der Besitzer. Sie verwalten die Zugriffsebene, indem Sie die Seite **Zugriffsebene** des verknüpften AWS-Kontos verwenden. Verknüpfte AWS-Konten erben jedoch keine Berechtigungen von einem konsolidierten AWS-Konto.

Verknüpfte AWS-Konten erben immer Berechtigungen von der Verwaltungsgruppe, zu der sie gehören.

## <a name="next-steps"></a>Nächste Schritte

- Nachdem Sie nun die Integration des AWS-Kosten- und Nutzungsberichts eingerichtet und konfiguriert haben, fahren Sie mit [Verwalten von AWS-Kosten und -Nutzung](aws-integration-manage.md) fort.
- Wenn Sie mit der Kostenanalyse nicht vertraut sind, lesen Sie den Schnellstart [Ermitteln und Analysieren von Kosten mit der Kostenanalyse](quick-acm-cost-analysis.md).
- Wenn Sie mit Budgets in Azure nicht vertraut sind, lesen Sie [Erstellen und Verwalten von Azure-Budgets](tutorial-acm-create-budgets.md).
