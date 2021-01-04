---
title: Verbinden Ihres GCP-Kontos mit Azure Security Center
description: Überwachen von GCP-Ressourcen über Azure Security Center
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: quickstart
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 1095222f332e7a66f684feddf1fdab17704ca658
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96573543"
---
#  <a name="connect-your-gcp-accounts-to-azure-security-center"></a>Verbinden Ihrer GCP-Konten mit Azure Security Center

Cloudworkloads umfassen in der Regel mehrere Cloudplattformen, daher muss das auch für Cloudsicherheitsdienste gelten.

Azure Security Center schützt Workloads in Azure, Amazon Web Services (AWS) und Google Cloud Platform (GCP).

Beim Onboarding Ihres GCP-Kontos in Security Center werden GCP Security Command und Azure Security Center integriert. Security Center bietet somit Sichtbarkeit und Schutz für diese beiden Cloudumgebungen und stellt so Folgendes bereit:

- Erkennung von Sicherheitsfehlkonfigurationen
- Eine einzelne Ansicht, die Security Center-Empfehlungen und GCP Security Command Center-Ergebnisse anzeigt
- Einbinden Ihrer GCP-Ressourcen in die Berechnung von Sicherheitsbewertungen durch Security Center
- Integration der auf dem CIS-Standard basierenden GCP Security Command Center-Empfehlungen in das Dashboard zur Einhaltung gesetzlicher Bestimmungen von Security Center

Im nachstehenden Screenshot sehen Sie GCP-Projekte, die auf dem Übersichtsdashboard von Security Center angezeigt werden.

:::image type="content" source="./media/quickstart-onboard-gcp/gcp-account-in-overview.png" alt-text="3 GCP-Projekte, die auf dem Übersichtsdashboard von Security Center aufgeführt sind" lightbox="./media/quickstart-onboard-gcp/gcp-account-in-overview.png":::


## <a name="availability"></a>Verfügbarkeit

|Aspekt|Details|
|----|:----|
|Status des Release:|Vorschau<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)] |
|Preise:|Erfordert [Azure Defender für Server](defender-for-servers-introduction.md)|
|Erforderliche Rollen und Berechtigungen:|**Besitzer** oder **Mitwirkender** für das entsprechende Azure-Abonnement|
|Clouds:|![Ja](./media/icons/yes-icon.png) Kommerzielle Clouds<br>![Nein](./media/icons/no-icon.png) National/Sovereign (US Gov, China Gov, andere Gov)|
|||

## <a name="connect-your-gcp-account"></a>Herstellen einer Verbindung mit Ihrem GCP-Konto

Führen Sie die folgenden Schritte aus, um Ihren GCP-Cloudconnector zu erstellen: 

### <a name="step-1-set-up-gcp-security-command-center-with-security-health-analytics"></a>Schritt 1: Einrichten von GCP Security Command Center mit Security Health Analytics

Für alle GCP-Projekte in Ihrer Organisation müssen Sie außerdem folgende Schritte ausführen:

1. Richten Sie **GCP Security Command Center** mithilfe [dieser Anweisungen aus der GCP-Dokumentation](https://cloud.google.com/security-command-center/docs/quickstart-scc-setup) ein.
1. Aktivieren Sie **Security Health Analytics** mithilfe [dieser Anweisungen aus der GCP-Dokumentation](https://cloud.google.com/security-command-center/docs/how-to-use-security-health-analytics).
1. Vergewissern Sie sich, dass Daten an Security Command Center übertragen werden.

Die Anweisungen zum Verbinden Ihrer GCP-Umgebung für die Sicherheitskonfiguration entsprechen den Empfehlungen von Google zur Sicherheitskonfiguration. Die Integration nutzt das Google Security Command Center sowie weitere Ressourcen, die sich auf Ihre Abrechnung auswirken können.

Wenn Sie Security Health Analytics erstmalig aktivieren, kann es mehrere Stunden dauern, bis die Daten verfügbar sind.


### <a name="step-2-enable-gcp-security-command-center-api"></a>Schritt 2: Aktivieren der GCP Security Command Center-API

1. Wählen Sie in der **Cloud Console-API-Bibliothek** von Google das Projekt aus, das Sie mit Azure Security Center verbinden möchten.
1. Suchen Sie in der API-Bibliothek nach **Security Command Center API**, und wählen Sie diesen Eintrag aus.
1. Wählen Sie auf der Seite der API **ENABLE** (Aktivieren) aus.

Erfahren Sie mehr über die [Security Command Center-API](https://cloud.google.com/security-command-center/docs/reference/rest/).


### <a name="step-3-create-a-dedicated-service-account-for-the-security-configuration-integration"></a>Schritt 3: Erstellen eines dedizierten Dienstkontos für die Integration der Sicherheitskonfiguration

1. Wählen Sie in der **GCP-Konsole** das Projekt aus, das Sie mit Security Center verbinden möchten.
1. Wählen Sie im **Navigationsmenü** unter **IAM & admin** die Option **Service accounts** (Dienstkonten) aus.
1. Wählen Sie **CREATE SERVICE ACCOUNT** (Dienstkonto erstellen) aus.
1. Geben Sie einen Kontonamen ein, und wählen Sie **Create** (Erstellen) aus.
1. Geben Sie für **Role** (Rolle) **Security Center Admin Viewer** an, und wählen Sie **Continue** (Weiter) aus.
1. Der Abschnitt **Grant users access to this service account** (Benutzern Zugriff auf dieses Dienstkonto gewähren) ist optional. Wählen Sie **Fertig** aus.
1. Kopieren Sie den Wert **E-Mail** des erstellten Dienstkontos, und speichern Sie ihn für die spätere Verwendung.
1. Wählen Sie im **Navigationsmenü** unter **IAM & admin** die Option **IAM** aus.
    1. Wechseln Sie zur Organisationsebene.
    1. Wählen Sie **ADD** (Hinzufügen) aus.
    1. Fügen Sie im Feld **New members** (Neue Mitglieder) den Wert von **E-Mail** ein, den Sie zuvor kopiert haben.
    1. Geben Sie als Rolle **Security Center Admin Viewer** an, und wählen Sie „Save“ (Speichern) aus.
        :::image type="content" source="./media/quickstart-onboard-gcp/iam-settings-gcp-permissions-admin-viewer.png" alt-text="Festlegen der relevanten GCP-Berechtigungen":::


### <a name="step-4-create-a-private-key-for-the-dedicated-service-account"></a>Schritt 4: Erstellen eines privaten Schlüssels für das dedizierte Dienstkonto
1. Wechseln Sie zur Projektebene.
1. Wählen Sie im **Navigationsmenü** unter **IAM & admin** die Option **Service accounts** (Dienstkonten) aus.
1. Öffnen Sie das dedizierte Dienstkonto, und wählen Sie „Edit“ (Bearbeiten) aus.
1. Wählen Sie im Abschnitt **Keys** (Schlüssel) die Option **ADD KEY** (Schlüssel hinzufügen) und dann **Create new key** (Neuen Schlüssel erstellen) aus.
1. Wählen Sie auf dem Bildschirm „Create private key“ (Privaten Schlüssel erstellen) die Option **JSON** und dann **CREATE** (Erstellen) aus.
1. Speichern Sie diese JSON-Datei für die spätere Verwendung.


### <a name="step-5-connect-gcp-to-security-center"></a>Schritt 5. Verbinden von GCP mit Security Center 
1. Wählen Sie im Menü von Security Center **Cloud connectors** aus.
1. Wählen Sie „add GCP account“ (GCP-Konto hinzufügen) aus.
1. Führen Sie auf der Seite „Onboarding“ die folgenden Schritte aus, und wählen Sie dann **Next** (Weiter) aus.
    1. Überprüfen Sie das ausgewählte Abonnement.
    1. Geben Sie im Feld **Display name** (Anzeigename) einen Anzeigenamen für den Connector ein.
    1. Geben Sie im Feld **Organization ID** (Organisations-ID) die ID Ihrer Organisation ein. Wenn sie Ihnen nicht bekannt ist, finden Sie weitere Informationen unter [Creating and managing organizations](https://cloud.google.com/resource-manager/docs/creating-managing-organization) (Erstellen und Verwalten von Organisationen).
    1. Navigieren Sie im Dateifeld **Private Key** (Privater Schlüssel) zu der JSON-Datei, die Sie in [Schritt 4: Erstellen eines privaten Schlüssels für das dedizierte Dienstkonto](#step-4-create-a-private-key-for-the-dedicated-service-account) heruntergeladen haben.


### <a name="step-6-confirmation"></a>Schritt 6. Bestätigung

Wenn der Connector erstellt und GCP Security Command Center ordnungsgemäß konfiguriert wurde:

- Der GCP-CIS-Standard wird auf dem Dashboard zur Einhaltung gesetzlicher Bestimmungen von Security Center angezeigt.
- 5–10 Minuten nach Abschluss des Onboardings werden Sicherheitsempfehlungen für Ihre GCP-Ressourcen im Security Center-Portal und im Dashboard zur Einhaltung gesetzlicher Bestimmungen angezeigt:   :::image type="content" source="./media/quickstart-onboard-gcp/gcp-resources-in-recommendations.png" alt-text="GCP-Ressourcen und Empfehlungen auf der Empfehlungsseite in Security Center":::


## <a name="monitoring-your-gcp-resources"></a>Überwachen Ihrer GCP-Ressourcen

Wie oben dargestellt, werden auf der Seite mit Sicherheitsempfehlungen in Azure Security Center Ihre GCP-Ressourcen in Verbindung mit Ihren Azure- und AWS-Ressourcen für eine echte cloudübergreifende Ansicht angezeigt.

Zum Anzeigen aller aktiven Empfehlungen für Ihre Ressourcen nach Ressourcentyp verwenden Sie die Ressourcenbestandsseite von Security Center, und filtern Sie nach dem gewünschten GCP-Ressourcentyp:

:::image type="content" source="./media/quickstart-onboard-gcp/gcp-resource-types-in-inventory.png" alt-text="Ressourcentypfilter der Seite für den Ressourcenbestand mit den GCP-Optionen"::: 


## <a name="next-steps"></a>Nächste Schritte

Das Herstellen einer Verbindung mit Ihrem GCP-Konto ist Teil der in Azure Security Center verfügbaren Multi-Cloud-Funktionen. Zugehörige Informationen finden Sie auf der folgenden Seite:

- [Verbinden Ihrer AWS-Konten mit Azure Security Center](quickstart-onboard-aws.md)
