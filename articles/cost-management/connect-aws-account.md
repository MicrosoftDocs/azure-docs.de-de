---
title: Herstellen einer Verbindung eines Amazon Web Services-Kontos mit Cloudyn in Azure| Microsoft-Dokumentation
description: Stellen Sie eine Verbindung eines Amazon Web Services-Kontos her, um Kosten und Nutzungsdaten in Cloudyn-Berichten anzuzeigen.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/21/2019
ms.topic: conceptual
ms.service: cost-management
manager: benshy
ms.custom: seodec18
ms.openlocfilehash: b39296e18b38180e1081866d6e8197973dc782b1
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/22/2019
ms.locfileid: "66002158"
---
# <a name="connect-an-amazon-web-services-account"></a>Herstellen einer Verbindung mit einem Amazon Web Services-Konto

Sie haben zwei Optionen zum Herstellen einer Verbindung Ihres Amazon Web Services-Kontos (AWS) mit Cloudyn. Sie können eine Verbindung mit einer IAM-Rolle oder einem IAM-Benutzerkonto ohne Schreibzugriff herstellen. Die IAM-Rolle wird empfohlen, da sie Ihnen ermöglicht, den Zugriff mit definierten Berechtigungen an vertrauenswürdige Personen zu delegieren. Die IAM-Rolle setzt nicht voraus, dass Sie langfristige Zugriffsschlüssel freigeben. Nachdem Sie eine Verbindung Ihres AWS-Kontos mit Cloudyn hergestellt haben, sind Kosten und Verwendungsdaten in Cloudyn-Berichten verfügbar. Dieses Dokument führt Sie durch beide Optionen.

Weitere Informationen zu AWS-IAM-Identitäten finden Sie unter [Identities (Users, Groups, and Roles) (Identitäten [Benutzer, Gruppen und Rollen])](https://docs.aws.amazon.com/IAM/latest/UserGuide/id.html).

Außerdem aktivieren Sie detaillierte AWS-Abrechnungsberichte und speichern die Informationen in einem AWS S3-Bucket (Simple Storage Service). Detaillierte Abrechnungsberichte enthalten Abrechnungskosten mit Informationen zu Tags und Ressourcen auf Stundenbasis. Die Speicherung der Berichte ermöglicht Cloudyn, diese aus Ihrem Bucket abzurufen und die Informationen in den zugehörigen Berichten anzuzeigen.


## <a name="aws-role-based-access"></a>Rollenbasierter AWS-Zugriff

Die folgenden Abschnitte führen Sie durch das Erstellen einer IAM-Rolle ohne Schreibzugriff, um Zugriff auf Cloudyn zu ermöglichen.

### <a name="get-your-cloudyn-account-external-id"></a>Abrufen Ihrer externen Cloudyn-Konto-ID

Im ersten Schritt rufen Sie die eindeutige Verbindungspassphrase im Cloudyn-Portal ab. Sie dient in AWS als **External ID (Externe ID)**.

1. Öffnen Sie im Azure-Portal das Cloudyn-Portal, oder navigieren Sie zu [https://azure.cloudyn.com](https://azure.cloudyn.com), und melden Sie sich an.
2. Klicken Sie auf das Zahnradsymbol, und wählen Sie dann **Cloud Accounts (Cloudkonten)** aus.
3. Wählen Sie unter „Accounts Management (Kontenverwaltung)“ die Registerkarte **AWS Accounts (AWS-Konten)** aus, und klicken Sie dann auf **Add new + (Neu hinzufügen)**.
4. Kopieren Sie im Dialogfeld **Add AWS Account (AWS-Konto hinzufügen)** die **External ID (Externe ID)**, und speichern Sie den Wert für die Schritte zum Erstellen der AWS-Rolle im nächsten Abschnitt. Die externe ID ist für Ihr Konto eindeutig. In der folgenden Abbildung lautet beispielsweise die externe ID _Contoso_ gefolgt von einer Zahl. Ihre ID lautet anders.  
    ![Externe ID, die im Feld „Add AWS Account“ (AWS-Konto hinzufügen) angezeigt wird](./media/connect-aws-account/external-id.png)

### <a name="add-aws-read-only-role-based-access"></a>Hinzufügen des rollenbasierten schreibgeschützten AWS-Zugriffs

1. Melden Sie sich bei der AWS-Konsole unter https://console.aws.amazon.com/iam/home an, und wählen Sie **Roles (Rollen)** aus.
2. Klicken Sie auf **Create Role (Rolle erstellen)**, und wählen Sie dann **Another AWS account (Anderes AWS-Konto)**.
3. Fügen Sie `432263259397` in das Feld **Account ID (Konto-ID)** ein. Diese Konto-ID ist das Cloudyn-Datensammlerkonto, das dem Cloudyn-Dienst von AWS zugewiesen wurde. Verwenden Sie exakt die angezeigte Konto-ID.
4. Aktivieren Sie neben **Options (Optionen)** den Befehl **Require external ID (Externe ID anfordern)**. Fügen Sie Ihren eindeutigen Wert ein, den Sie zuvor aus dem Feld **External ID (Externe ID)** in Cloudyn kopiert haben. Klicken Sie anschließend auf **Next: Permissions** (Weiter: Berechtigungen).  
    ![Fügen Sie die externe ID von Cloudyn auf der Seite „Create role“ (Rolle erstellen) ein.](./media/connect-aws-account/create-role01.png)
5. Geben Sie unter **Attach permissions policies** (Richtlinien für Berechtigungen anfügen) im Filtersuchfeld **Policy type** (Richtlinientyp) `ReadOnlyAccess` ein, wählen Sie **ReadOnlyAccess** aus, und klicken Sie dann auf **Next: Review** (Weiter: Überprüfen).  
    ![Wählen Sie in der Liste der Richtliniennamen den schreibgeschützten Zugriff aus.](./media/connect-aws-account/readonlyaccess.png)
6. Stellen Sie auf der Seite „Review“ (Überprüfen) sicher, dass Ihre Auswahl richtig ist, und geben Sie einen **Role name (Rollennamen)** ein. Beispiel: *Azure-Cost-Mgt*. Geben Sie eine **Role description (Rollenbeschreibung)** ein. Beispiel: _Rollenzuweisung für Cloudyn_, und klicken Sie dann auf **Rolle erstellen**.
7. Klicken Sie in der Liste **Roles (Rollen)** auf die Rolle, die Sie erstellt haben, und kopieren Sie den Wert von **Role ARN (Rollen-ARN)** aus der Seite „Summary“ (Zusammenfassung). Verwenden Sie später den Wert von „Rollen-ARN“ (Amazon Resource Name), wenn Sie Ihre Konfiguration in Cloudyn registrieren.  
    ![Kopieren Sie den Rollen-ARN aus der Zusammenfassungsseite.](./media/connect-aws-account/role-arn.png)

### <a name="configure-aws-iam-role-access-in-cloudyn"></a>Konfigurieren des AWS-IAM-Rollenzugriffs in Cloudyn

1. Öffnen Sie im Azure-Portal das Cloudyn-Portal, oder navigieren Sie zu https://azure.cloudyn.com/, und melden Sie sich an.
2. Klicken Sie auf das Zahnradsymbol, und wählen Sie dann **Cloud Accounts (Cloudkonten)** aus.
3. Wählen Sie unter „Accounts Management (Kontenverwaltung)“ die Registerkarte **AWS Accounts (AWS-Konten)** aus, und klicken Sie dann auf **Add new + (Neu hinzufügen)**.
4. Geben Sie in **Account Name (Kontoname)** einen Namen für das Konto ein.
5. Wählen Sie neben **Access Type (Zugriffstyp)** die Option **IAM Role (IAM-Rolle)**.
6. Fügen Sie im Feld **Role ARN (Rollen-ARN)** den Wert ein, den Sie zuvor kopiert haben, und klicken Sie dann auf **Speichern**.  
    ![Fügen Sie den Rollen-ARN im Feld „Add AWS Account“ (AWS-Konto hinzufügen) ein.](./media/connect-aws-account/add-aws-account-box.png)


Ihr AWS-Konto wird in der Liste der Konten angezeigt. Die aufgelistete **Owner ID (Besitzer-ID)** entspricht Ihrem Wert für „Role ARN“ (Rollen-ARN). Ihr **Kontostatus** sollte mit einem grünen Häkchen versehen sein, das anzeigt, dass Cloudyn auf Ihr AWS-Konto zugreifen kann. Bis Sie die detaillierte AWS-Abrechnung aktivieren, wird Ihr Konsolidierungsstatus als **Eigenständig** angezeigt.

![Auf der Seite „Accounts Management“ (Kontoverwaltung) gezeigter AWS-Kontostatus](./media/connect-aws-account/aws-account-status01.png)

Cloudyn startet das Sammeln der Daten und Auffüllen von Berichten. Als Nächstes [aktivieren Sie detaillierte AWS-Abrechnung](#enable-detailed-aws-billing).


## <a name="aws-user-based-access"></a>Zugriff auf AWS-Benutzerbasis

Die folgenden Abschnitte führen Sie durch das Erstellen eines Benutzers ohne Schreibzugriff, um Zugriff auf Cloudyn zu ermöglichen.

### <a name="add-aws-read-only-user-based-access"></a>Hinzufügen des benutzerbasierten schreibgeschützten AWS-Zugriffs

1. Melden Sie sich bei der AWS-Konsole unter https://console.aws.amazon.com/iam/homean, und wählen Sie **Users (Benutzer)** aus.
2. Klicken Sie auf **Benutzer hinzufügen**.
3. Geben Sie in das Feld **User name (Benutzername)** einen Benutzernamen ein.
4. Wählen Sie für **Access type** (Zugriffstyp) die Option **Programmatic access** (Programmgesteuerter Zugriff), und klicken Sie auf **Next: Permissions** (Weiter: Berechtigungen).  
    ![Geben Sie einen Benutzernamen auf der Seite „Add user“ (Benutzer hinzufügen) ein.](./media/connect-aws-account/add-user01.png)
5. Wählen Sie für Berechtigungen **Attach existing policies directly** (Vorhandene Richtlinien direkt anfügen).
6. Geben Sie unter **Attach permissions policies** (Richtlinien für Berechtigungen anfügen) im Filtersuchfeld **Policy type** (Richtlinientyp) `ReadOnlyAccess` ein, wählen Sie **ReadOnlyAccess** aus, und klicken Sie dann auf **Next: Review** (Weiter: Überprüfen).  
    ![Wählen Sie ReadOnlyAccess zum Festlegen von Berechtigungen für den Benutzer.](./media/connect-aws-account/set-permission-for-user.png)
7. Stellen Sie auf der Seite „Review“ (Überprüfen) sicher, dass Ihre Auswahl richtig ist, und klicken Sie dann auf **Create user (Benutzer erstellen)**.
8. Auf der Seite „Complete“ (Fertig stellen) werden Ihre Zugriffsschlüssel-ID und der geheime Zugriffsschlüssel angezeigt. Sie können diese Informationen zum Konfigurieren der Registrierung in Cloudyn verwenden.
9. Klicken Sie auf **Download .csv (CSV-Datei herunterladen)**, und speichern Sie die credentials.csv-Datei an einem sicheren Ort.  
    ![Klicken Sie auf „Download .csv“ (CSV herunterladen), um die Anmeldeinformationen zu speichern.](./media/connect-aws-account/download-csv.png)

### <a name="configure-aws-iam-user-based-access-in-cloudyn"></a>Konfigurieren des Zugriffs auf AWS-IAM-Benutzerbasis in Cloudyn

1. Öffnen Sie im Azure-Portal das Cloudyn-Portal, oder navigieren Sie zu https://azure.cloudyn.com/, und melden Sie sich an.
2. Klicken Sie auf das Zahnradsymbol, und wählen Sie dann **Cloud Accounts (Cloudkonten)** aus.
3. Wählen Sie unter „Accounts Management (Kontenverwaltung)“ die Registerkarte **AWS Accounts (AWS-Konten)** aus, und klicken Sie dann auf **Add new + (Neu hinzufügen)**.
4. Geben Sie für **Account Name (Kontoname)** einen Kontonamen ein.
5. Wählen Sie neben **Access Type (Zugriffstyp)** die Option **IAM User (IAM-Benutzer)**.
6. Fügen Sie in **Access Key (Zugriffsschlüssel)** den Wert **Access key ID (Zugriffsschlüssel-ID)** aus der Datei „credentials.csv“ ein.
7. Fügen Sie in **Secret Key (Geheimschlüssel)** den Wert **Secret access key (Geheimer Zugriffsschlüssel)** aus der Datei „credentials.csv“ ein, und klicken Sie dann auf **Save (Speichern)**.  

Ihr AWS-Konto wird in der Liste der Konten angezeigt. Ihr **Account Status (Kontostatus)** müsste mit einem grünen Häkchensymbol versehen sein.

Cloudyn startet das Sammeln der Daten und Auffüllen von Berichten. Als Nächstes [aktivieren Sie detaillierte AWS-Abrechnung](#enable-detailed-aws-billing).

## <a name="enable-detailed-aws-billing"></a>Aktivieren der detaillierten AWS-Abrechnung

Führen Sie die folgenden Schritte aus, um Ihren AWS-Rollen-ARN abzurufen. Sie verwenden den Rollen-ARN, um einem Abrechnungsbucket Leseberechtigungen zu erteilen.

1. Melden Sie sich unter https://console.aws.amazon.com bei der AWS-Konsole an, und klicken Sie auf **Services** (Dienste).
2. Geben Sie in das Feld „Service Search (Dienstsuche) *IAM* ein, und wählen Sie diese Option aus.
3. Wählen Sie im linken Menü die Option **Roles (Rollen)** aus.
4. Wählen Sie in der Liste der Rollen die Rolle aus, die Sie für den Cloudyn-Zugriff angelegt haben.
5. Klicken Sie auf der Seite „Roles Summary (Rollenübersicht)“, um den **Role ARN (Rollen-ARN)** zu kopieren. Halten Sie den Rollen-ARN für spätere Schritte bereit.

### <a name="create-an-s3-bucket"></a>Erstellen eines S3-Buckets

Sie erstellen einen S3-Bucket zum Speichern detaillierter Abrechnungsinformationen.

1. Melden Sie sich unter https://console.aws.amazon.com bei der AWS-Konsole an, und klicken Sie auf **Services** (Dienste).
2. Geben Sie in das Feld „Service Search (Dienstsuche) *S3* ein, und wählen Sie **S3** aus.
3. Klicken Sie auf der Amazon S3-Seite auf **Create bucket (Bucket erstellen)**.
4. Wählen Sie im Assistenten zum Erstellen von Buckets einen Bucketnamen and eine Region aus, und klicken Sie auf **Next (Weiter)**.  
    ![Beispielinformationen auf der Seite „Create bucket“ (Bucket erstellen)](./media/connect-aws-account/create-bucket.png)
5. Behalten Sie auf der Seite **Set properties (Eigenschaften festlegen)** die Standardwerte bei, und klicken Sie auf **Next (Weiter)**.
6. Klicken Sie auf der Seite „Review (Überprüfen) auf **Create bucket (Bucket erstellen)**. Die Liste Ihrer Buckets wird angezeigt.
7. Klicken Sie auf den Bucket, den Sie erstellt haben, und wählen Sie die Registerkarte **Permissions (Berechtigungen)** und dann **Bucket Policy (Bucketrichtlinie)** aus. Der Editor für Bucketrichtlinien wird geöffnet.
8. Kopieren Sie das folgende JSON-Beispiel, und fügen Sie es in den Editor für Bucketrichtlinien ein.
   - Ersetzen Sie `<BillingBucketName>` durch den Namen Ihres S3-Buckets.
   - Ersetzen Sie `<ReadOnlyUserOrRole>` durch die Rolle oder den Benutzer-ARN, die/den Sie zuvor kopiert haben.

   ```json
   {
    "Version": "2012-10-17",
    "Id": "Policy1426774604000",
    "Statement": [
        {
            "Sid": "Stmt1426774604000",
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::386209384616:root"
            },
            "Action": [
                "s3:GetBucketAcl",
                "s3:GetBucketPolicy"
            ],
            "Resource": "arn:aws:s3:::<BillingBucketName>"
        },
        {
            "Sid": "Stmt1426774604001",
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::386209384616:root"
            },
            "Action": "s3:PutObject",
            "Resource": "arn:aws:s3:::<BillingBucketName>/*"
        },
        {
            "Sid": "Stmt1426774604002",
            "Effect": "Allow",
            "Principal": {
                "AWS": "<ReadOnlyUserOrRole>"
            },
            "Action": [
                "s3:List*",
                "s3:Get*"
            ],
            "Resource": "arn:aws:s3:::<BillingBucketName>/*"
        }
    ]
   }
   ```

9. Klicken Sie auf **Speichern**.  
    ![Klicken Sie im Editor für Bucketrichtlinien auf „Save“ (Speichern).](./media/connect-aws-account/bucket-policy-editor.png)


### <a name="enable-aws-billing-reports"></a>Aktivieren von AWS-Abrechnungsberichten

Nachdem Sie den S3-Bucket erstellt und konfiguriert haben, navigieren Sie in der AWS-Konsole zu [Billing Preferences (Abrechnungseinstellungen)](https://console.aws.amazon.com/billing/home?#/preference).

1. Wählen Sie auf der Seite „Preferences (Einstellungen)“ **Receive Billing Reports (Abrechnungsberichte empfangen)** aus.
2. Geben Sie unter **Receive Billing Reports (Abrechnungsberichte empfangen)** den Namen des Buckets ein, den Sie erstellt haben, und klicken Sie dann auf **Verify (Überprüfen)**.  
3. Wählen Sie alle vier Optionen für die Granularität von Berichten aus, und klicken Sie dann auf **Save preferences (Einstellungen speichern)**.  
    ![Wählen Sie die Granularität zum Aktivieren von Berichten aus.](./media/connect-aws-account/enable-reports.png)

Cloudyn ruft detaillierte Abrechnungsinformationen aus Ihrem S3-Bucket ab und füllt Berichte aus, nachdem die detaillierte Abrechnung aktiviert wurde. Es kann bis zu 24 Stunden dauern, bis detaillierte Abrechnungsdaten in der Cloudyn-Konsole sichtbar werden. Wenn detaillierte Abrechnungsdaten verfügbar sind, wird Ihr Kontenkonsolidierungsstatus als **Consolidated (Konsolidiert)** angezeigt. Der „Account Status“ (Kontostatus) lautet **Completed (Abgeschlossen)**.

![Konsolidierungsstatus auf der Registerkarte „AWS Accounts“ (AWS-Konten)](./media/connect-aws-account/consolidated-status.png)

Einige der Optimierungsberichte können einige Tage benötigen, um eine ausreichend große Datenstichprobe für genaue Empfehlungen abzurufen.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu Cloudyn finden Sie im Tutorial [Überprüfen der Nutzung und der Kosten](tutorial-review-usage.md) für Cloudyn.
