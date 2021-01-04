---
title: Herstellen einer Verbindung zwischen Ihrem AWS-Konto und Azure Security Center
description: Überwachen von AWS-Ressourcen über Azure Security Center
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: quickstart
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 23609613cace2f9be90ad5c88d4d51bb1c2f1a9b
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96573509"
---
#  <a name="connect-your-aws-accounts-to-azure-security-center"></a>Herstellen einer Verbindung zwischen Ihren AWS-Konten und Azure Security Center

Cloudworkloads umfassen in der Regel mehrere Cloudplattformen, daher muss das auch für Cloudsicherheitsdienste gelten.

Azure Security Center schützt Workloads in Azure, Amazon Web Services (AWS) und Google Cloud Platform (GCP).

Beim Onboarding Ihres AWS-Kontos in Security Center werden AWS Security Hub und Azure Security Center integriert. Security Center bietet somit Sichtbarkeit und Schutz für diese beiden Cloudumgebungen und stellt so Folgendes bereit:

- Automatische Agent-Bereitstellung (Security Center verwendet [Azure Arc](../azure-arc/servers/overview.md) zum Bereitstellen des Log Analytics-Agents für die AWS-Instanzen)
- Richtlinienverwaltung
- Verwaltung von Sicherheitsrisiken
- Integrierte Endpunkterkennung und -antwort (Endpoint Detection and Response, EDR)
- Erkennung von Sicherheitsfehlkonfigurationen
- Eine einzelne Ansicht, die Security Center-Empfehlungen und AWS Security Hub-Ergebnisse anzeigt
- Einbinden Ihrer AWS-Ressourcen in die Sicherheitsbewertungsberechnungen von Security Center
- Bewerten der Einhaltung gesetzlicher Bestimmungen für Ihre AWS-Ressourcen

Im folgenden Screenshot sehen Sie AWS-Konten, die auf dem Übersichtsdashboard von Security Center angezeigt werden.

:::image type="content" source="./media/quickstart-onboard-aws/aws-account-in-overview.png" alt-text="3 GCP-Projekte, die auf dem Übersichtsdashboard von Security Center aufgeführt sind" lightbox="./media/quickstart-onboard-gcp/gcp-account-in-overview.png":::

## <a name="availability"></a>Verfügbarkeit

|Aspekt|Details|
|----|:----|
|Status des Release:|Vorschau<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)] |
|Preise:|Erfordert [Azure Defender für Server](defender-for-servers-introduction.md)|
|Erforderliche Rollen und Berechtigungen:|**Besitzer** oder **Mitwirkender** für das entsprechende Azure-Abonnement|
|Clouds:|![Ja](./media/icons/yes-icon.png) Kommerzielle Clouds<br>![Nein](./media/icons/no-icon.png) National/Sovereign (US Gov, China Gov, andere Gov)|
|||



## <a name="connect-your-aws-account"></a>Herstellen einer Verbindung mit Ihrem AWS-Konto

Führen Sie die folgenden Schritte aus, um Ihren AWS-Cloudconnector zu erstellen: 

### <a name="step-1-set-up-aws-security-hub"></a>Schritt 1: Einrichten von AWS Security Hub:

1. Um Sicherheitsempfehlungen für mehrere Regionen anzuzeigen, wiederholen Sie die folgenden Schritte für jede relevante Region.

    > [!IMPORTANT]
    > Wenn Sie ein AWS-Masterkonto verwenden, wiederholen Sie die folgenden drei Schritte, um das Masterkonto und alle verbundenen Mitgliedskonten in allen relevanten Regionen zu konfigurieren.

    1. Aktivieren Sie [AWS Config](https://docs.aws.amazon.com/config/latest/developerguide/gs-console.html).
    1. Aktivieren Sie [AWS Security Hub](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-settingup.html).
    1. Stellen Sie sicher, dass Daten an Security Hub übertragen werden.

        Wenn Sie Security Hub erstmalig aktivieren, kann es mehrere Stunden dauern, bis die Daten verfügbar sind.

### <a name="step-2-set-up-authentication-for-security-center-in-aws"></a>Schritt 2: Einrichten der Authentifizierung für Security Center in AWS

Es gibt zwei Möglichkeiten, um Security Center die Authentifizierung bei AWS zu erlauben:

- **Erstellen einer IAM-Rolle für Security Center**: Dies ist die sicherste Methode und wird empfohlen.
- **AWS-Benutzer für Security Center**: Diese Option ist weniger sicher und kann genutzt werden, wenn IAM nicht aktiviert ist.

#### <a name="create-an-iam-role-for-security-center"></a>Erstellen einer IAM-Rolle für Security Center
1. Wählen Sie in Ihrer Amazon Web Services-Konsole unter **Security, Identity & Compliance** (Sicherheit, Identität und Compliance) die Option **IAM** aus.
    :::image type="content" source="./media/quickstart-onboard-aws/aws-identity-and-compliance.png" alt-text="AWS-Services":::

1. Klicken Sie auf **Roles** (Rollen) und anschließend auf **Create role** (Rolle erstellen).
1. Wählen Sie **Another AWS account** (Ein anderes AWS-Konto) aus.
1. Geben Sie die folgenden Details ein:

    - **Account ID** (Konto-ID): Geben Sie die Microsoft-Konto-ID (**158177204117**) ein, wie sie auf der AWS-Connectorseite in Security Center angezeigt wird.
    - **Require External ID** (Externe ID erforderlich): Diese Option sollte ausgewählt werden.
    - **External ID** (Externe ID): Geben Sie die Abonnement-ID ein, wie sie auf der AWS-Connectorseite in Security Center angezeigt wird. 

1. Wählen Sie **Weiter** aus.
1. Wählen Sie im Abschnitt **Attach permission policies** (Berechtigungsrichtlinien anfügen) die folgenden Richtlinien aus:

    - SecurityAudit
    - AmazonSSMAutomationRole
    - AWSSecurityHubReadOnlyAccess

1. Fügen Sie optional Tags hinzu. Das Hinzufügen von Tags zum Benutzer wirkt sich nicht auf die Verbindung aus.
1. Wählen Sie **Weiter** aus.

1. Wählen Sie in der Liste der Rollen die von Ihnen erstellte Rolle aus.

1. Speichern Sie den Amazon Resource Name (ARN) für später. 

#### <a name="create-an-aws-user-for-security-center"></a>Erstellen eines AWS-Benutzers für Security Center 
1. Öffnen Sie die Registerkarte **Users** (Benutzer), und wählen Sie **Add user** (Benutzer hinzufügen) aus.
1. Geben Sie im Schritt **Details** einen Benutzernamen für Security Center ein, und wählen Sie **Programmatic access** (Programmgesteuerter Zugriff) für den AWS-Zugriffstyp aus. 
1. Wählen Sie **Next: Permissions** (Weiter: Berechtigungen) aus.
1. Wählen Sie **Attach existing policies directly** (Vorhandene Richtlinien direkt anfügen) aus, und wenden Sie die folgenden Richtlinien an:
    - SecurityAudit
    - AmazonSSMAutomationRole
    - AWSSecurityHubReadOnlyAccess
    
1. Klicken Sie auf **Weiter: Tags**. Fügen Sie optional Tags hinzu. Das Hinzufügen von Tags zum Benutzer wirkt sich nicht auf die Verbindung aus.
1. Wählen Sie **Review** (Überprüfen) aus.
1. Speichern Sie die CSV-Datei mit der automatisch generierten **Zugriffsschlüssel-ID** und dem **geheimen Zugriffsschlüssel** zur späteren Verwendung.
1. Überprüfen Sie die Zusammenfassung, und klicken Sie anschließend auf **Create user** (Benutzer erstellen).


### <a name="step-3-configure-the-ssm-agent"></a>Schritt 3: Konfigurieren des SSM-Agents

AWS Systems Manager ist für die Automatisierung von Aufgaben in Ihren AWS-Ressourcen erforderlich. Wenn Ihre EC2-Instanzen über keinen SSM-Agent verfügen, führen Sie die entsprechenden Anweisungen von Amazon aus:

- [Installieren und Konfigurieren des SSM-Agents auf Windows-Instanzen](https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-install-ssm-win.html)
- [Installieren und Konfigurieren des SSM-Agents auf Amazon EC2-Linux-Instanzen](https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-install-ssm-agent.html)


### <a name="step-4-complete-azure-arc-prerequisites"></a>Schritt 4: Einrichten der erforderlichen Azure Arc-Komponenten
1. Stellen Sie sicher, dass die entsprechenden [Azure-Ressourcenanbieter](../azure-arc/servers/agent-overview.md#register-azure-resource-providers) registriert sind:
    - Microsoft.HybridCompute
    - Microsoft.GuestConfiguration

1. Erstellen Sie einen Dienstprinzipal für Onboarding im großen Stil. Als **Besitzer** des Abonnements, das Sie für das Onboarding verwenden möchten, erstellen Sie einen Dienstprinzipal für das Azure Arc-Onboarding, wie unter [Erstellen eines Dienstprinzipals für flexibles Onboarding](../azure-arc/servers/onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) beschrieben.


### <a name="step-5-connect-aws-to-security-center"></a>Schritt 5. Herstellen einer Verbindung zwischen AWS und Security Center

1. Wählen Sie im Menü von Security Center **Multi cloud connectors** (Connectors mehrerer Clouds) aus.
1. Wählen Sie **Add AWS Account** (AWS-Konto hinzufügen) aus.
    :::image type="content" source="./media/quickstart-onboard-aws/add-aws-account.png" alt-text="Schaltfläche zum Hinzufügen eines AWS-Kontos auf der Seite für Connectors mehrerer Clouds in Security Center":::
1. Konfigurieren Sie die Optionen auf der Registerkarte **AWS authentication** (AWS-Authentifizierung):
    1. Geben Sie einen **Anzeigenamen** für den Connector ein.
    1. Überprüfen Sie, ob das Azure-Abonnement richtig ist. Dabei handelt es sich um das Abonnement, das die Empfehlungen für den Connector und den AWS Security Hub enthält.
    1. In Abhängigkeit von der Authentifizierungsoption, die Sie in [Schritt 2. Einrichten der Authentifizierung für Security Center in AWS](#step-2-set-up-authentication-for-security-center-in-aws) ausgewählt haben:
        - Wählen Sie **Assume Role** (Rolle übernehmen) aus, und fügen Sie den ARN aus [Erstellen einer IAM-Rolle für Security Center](#create-an-iam-role-for-security-center) ein.
            :::image type="content" source="./media/quickstart-onboard-aws/paste-arn-in-portal.png" alt-text="Einfügen der ARN-Dabei im entsprechenden Feld des AWS-Verbindungs-Assistenten im Azure-Portal":::

            oder

        - Wählen Sie **Anmeldeinformationen** aus, und fügen Sie den **Zugriffsschlüssel** und den **geheimen Schlüssel** aus der CSV-Datei ein, die Sie unter [Erstellen eines AWS-Benutzers für Security Center](#create-an-aws-user-for-security-center) gespeichert haben.
1. Wählen Sie **Weiter** aus.
1. Konfigurieren Sie die Optionen auf der Registerkarte **Azure Arc Configuration** (Azure Arc-Konfiguration):

    Security Center ermittelt die EC2-Instanzen im verbundenen AWS-Konto und verwendet SSM, um das Onboarding in Azure Arc durchzuführen. 

    > [!TIP]
    > Eine Liste der unterstützten Betriebssysteme finden Sie in den häufig gestellten Fragen unter [Welche Betriebssysteme werden für meine EC2-Instanzen unterstützt?](#what-operating-systems-for-my-ec2-instances-are-supported).

    1. Wählen Sie die **Ressourcengruppe** und die **Azure-Region** aus, für die das Onboarding der ermittelten AWS EC2-Instanzen im ausgewählten Abonnement durchgeführt werden soll.
    1. Geben Sie die **Dienstprinzipal-ID** und das **Clientgeheimnis des Dienstprinzipals** für Azure Arc ein, wie unter [Erstellen eines Dienstprinzipals für flexibles Onboarding](../azure-arc/servers/onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) beschrieben.
    1. Wenn der Computer die Internetverbindung über einen Proxyserver herstellt, geben Sie die IP-Adresse des Proxyservers oder den Namen und die Portnummer an, die für die Kommunikation mit dem Proxyserver verwendet werden. Geben Sie den Wert im Format ```http://<proxyURL>:<proxyport>``` ein.
    1. Klicken Sie auf **Überprüfen + erstellen**.

        Überprüfen der Zusammenfassungsinformationen

        Im Abschnitt „Tags“ werden alle Azure-Tags aufgelistet, die automatisch für jede EC2-Instanz, für die das Onboarding durchgeführt wurde, erstellt werden,mit den jeweiligen relevanten Details, damit sie in Azure leicht erkannt werden. 

        Unter [Verwenden von Tags zum Organisieren von Azure-Ressourcen und Verwaltungshierarchie](../azure-resource-manager/management/tag-resources.md) erfahren Sie mehr über Azure-Tags.

### <a name="step-6-confirmation"></a>Schritt 6. Bestätigung

Wenn der Connector erfolgreich erstellt und der AWS Security Hub ordnungsgemäß konfiguriert wurde:

- Security Center scannt die Umgebung auf AWS EC2-Instanzen, führt für sie ein Onboarding in Azure Arc durch und ermöglicht die Installation des Log Analytics-Agents sowie die Bereitstellung von Bedrohungsschutz und Sicherheitsempfehlungen. 
- Der ASC-Dienst sucht alle sechs Stunden nach neuen AWS EC2-Instanzen und führt der Konfiguration entsprechend automatisch ein Onboarding für sie durch.
- Der AWS-CIS-Standard wird auf dem Dashboard zur Einhaltung gesetzlicher Bestimmungen von Security Center angezeigt.
- Wenn die Security Hub-Richtlinie aktiviert ist, werden Empfehlungen im Security Center-Portal und im Dashboard für die gesetzliche Konformität 5–10 Minuten nach Abschluss der Integration angezeigt.
    :::image type="content" source="./media/quickstart-onboard-aws/aws-resources-in-recommendations.png" alt-text="AWS-Ressourcen und Empfehlungen auf der Empfehlungsseite in Security Center":::



## <a name="monitoring-your-aws-resources"></a>Überwachen Ihrer AWS-Ressourcen

Wie oben gezeigt, zeigt die Seite mit Sicherheitsempfehlungen in Azure Security Center Ihre AWS-Ressourcen in Verbindung mit Ihren Azure- und GCP-Ressourcen für eine echte Multi-Cloud-Ansicht an.

Zum Anzeigen aller aktiven Empfehlungen für Ihre Ressourcen nach Ressourcentyp verwenden Sie die Ressourcenbestandsseite von Security Center, und filtern Sie nach dem gewünschten AWS-Ressourcentyp:

:::image type="content" source="./media/quickstart-onboard-aws/aws-resource-types-in-inventory.png" alt-text="Ressourcentypfilter der Ressourcenbestandsseite mit den AWS-Optionen"::: 


## <a name="aws-in-security-center-faq"></a>AWS in Azure Security Center – häufig gestellte Fragen

### <a name="what-operating-systems-for-my-ec2-instances-are-supported"></a>Welche Betriebssysteme werden für meine EC2-Instanzen unterstützt?

Unterstützte Betriebssysteme für automatisches Onboarding in Azure Arc für AWS-Computer

- Ubuntu 16.04 – der SSM-Agent ist standardmäßig vorinstalliert.
- Ubuntu 18.04 – der SSM-Agent ist standardmäßig vorinstalliert.
- Windows-Server – der SSM-Agent ist standardmäßig vorinstalliert.
- CentOS Linux 7 – SSM muss manuell installiert werden, oder das Onboarding ist separat durchzuführen.
- SUSE Linux Enterprise Server (SLES) 15 (x64) – SSM muss manuell installiert werden, oder das Onboarding ist separat durchzuführen.
- Red Hat Enterprise Linux (RHEL) 7 (x64) – SSM muss manuell installiert werden, oder das Onboarding ist separat durchzuführen.


## <a name="next-steps"></a>Nächste Schritte

Das Herstellen einer Verbindung mit Ihrem AWS-Konto ist Teil der in Azure Security Center verfügbaren Multi-Cloud-Erfahrung. Zugehörige Informationen finden Sie auf der folgenden Seite:

- [Herstellen einer Verbindung zwischen Ihren GCP-Konten und Azure Security Center](quickstart-onboard-gcp.md)
