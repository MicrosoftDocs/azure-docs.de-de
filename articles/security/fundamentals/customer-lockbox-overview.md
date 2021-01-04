---
title: Kunden-Lockbox für Microsoft Azure
description: Technische Übersicht über Kunden-Lockbox für Microsoft Azure, ein Feature, das Kontrolle über den Cloudanbieterzugriff für Fälle bietet, in denen Microsoft Zugriff auf Kundendaten benötigt.
author: TerryLanfear
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 09/15/2020
ms.openlocfilehash: 01232aa101e2964354acfbeb6cea341a0da33ca6
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96489887"
---
# <a name="customer-lockbox-for-microsoft-azure"></a>Kunden-Lockbox für Microsoft Azure

> [!NOTE]
> Um dieses Feature verwenden zu können, benötigt Ihre Organisation einen [Azure-Supportplan](https://azure.microsoft.com/support/plans/) mit der Mindestebene **Developer**.

Kunden-Lockbox für Microsoft Azure bietet eine Oberfläche, auf der Kunden Anforderungen nach Zugriff auf Kundendaten prüfen und dann genehmigen oder ablehnen können. Das Feature wird in Fällen verwendet, in denen ein Microsoft-Techniker während einer Supportanfrage auf Kundendaten zugreifen muss.

Dieser Artikel behandelt, wie Kunden-Lockbox-Anforderungen initiiert, nachverfolgt und für spätere Überprüfungen und Audits gespeichert werden.

Kunden-Lockbox ist jetzt allgemein verfügbar und derzeit für Remotedesktopzugriff auf virtuelle Computer aktiviert.

## <a name="supported-services-and-scenarios-in-preview"></a>Unterstützte Dienste und Szenarien während der Vorschau

Die folgenden Dienste sind derzeit für Kunden-Lockbox als Vorschauversion verfügbar:

- API Management
- Azure App Service
- Cognitive Services
- Container Registry
- Azure Database for MySQL
- Azure Databricks
- Azure Data Box
- Azure-Daten-Explorer
- Azure Data Factory
- Azure Database for PostgreSQL
- Azure Functions
- HDInsight
- Azure Kubernetes Service
- Azure Monitor
- Azure Storage
- Azure SQL-Datenbank
- Azure-Abonnementübertragung
- Azure Synapse Analytics
- Virtuelle Computer (jetzt auch mit Zugriff auf Speicherabbilder und verwaltete Datenträger)

Wenn Sie Kunden-Lockbox für diese Vorschauangebote für Ihre Organisation aktivieren möchten, registrieren Sie sich für dir [Public Preview von Kunden-Lockbox für Azure](https://aka.ms/customerlockbox/insiderprogram).

## <a name="supported-services-and-scenarios-in-general-availability"></a>Unterstützte Dienste und Szenarien bei allgemeiner Verfügbarkeit

Die folgenden Dienste und Szenarien sind derzeit für Kunden-Lockbox allgemein verfügbar.

### <a name="remote-desktop-access-to-virtual-machines"></a>Remotedesktopzugriff auf virtuelle Computer

Kunden-Lockbox ist derzeit für Remotedesktop-Zugriffsanforderungen auf virtuelle Computer aktiviert. Die folgenden Workloads werden unterstützt:
- Platform-as-a-Service (PaaS): Azure Cloud Services (Webrolle und Workerrolle)
- Infrastructure-as-a-Service (IaaS) – Windows und Linux (nur Azure Resource Manager)
- VM-Skalierungsgruppen – Windows und Linux

> [!NOTE]
> Klassische IaaS-Instanzen werden von Kunden-Lockbox nicht unterstützt. Wenn keine Ihrer Workloads auf klassischen IaaS-Instanzen ausgeführt werden, empfehlen wir, sie vom klassischen zum Resource Manager-Bereitstellungsmodell zu migrieren. Anweisungen hierzu finden Sie unter [Plattformgestützte Migration von IaaS-Ressourcen vom klassischen zum Azure Resource Manager-Bereitstellungsmodell](../../virtual-machines/migration-classic-resource-manager-overview.md).

#### <a name="detailed-audit-logs"></a>Ausführliche Überwachungsprotokolle

In Szenarien mit Remotedesktopzugriff können Sie die vom Microsoft-Techniker ausgeführten Aktionen mithilfe von Windows-Ereignisprotokollen überprüfen. Ziehen Sie in Erwägung, die Ereignisprotokolle mit Azure Security Center zu sammeln und die Daten für die Analyse in Ihren Arbeitsbereich zu kopieren. Weitere Informationen finden Sie unter [Datensammlung in Azure Security Center](../../security-center/security-center-enable-data-collection.md).

## <a name="workflow"></a>Workflow

Die folgenden Schritte beschreiben einen typischen Workflow für eine Kunden-Lockbox-Anforderung.

1. Eine Person in einer Organisation hat ein Problem mit ihrer Azure-Workload.

2. Nachdem diese Person das Problem behandelt hat, es aber nicht beheben kann, öffnet sie ein Supportticket über das [Azure-Portal](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac). Das Ticket wird einem Azure-Kundensupporttechniker zugewiesen.

3. Ein Azure-Supporttechniker überprüft den Service Request und ermittelt die nächsten Schritte zur Behebung des Problems.

4. Wenn der Supporttechniker das Problem nicht mithilfe von Standardtools und Telemetriedaten beheben kann, besteht der nächste Schritt darin, über einen JIT-Zugriffsdienst (Just-In-Time) erhöhte Berechtigungen anzufordern. Diese Anforderung kann vom ursprünglichen Supportmitarbeiter stammen. Sie kann auch von einem anderen Techniker stammen, wenn das Problem an das Azure DevOps-Team eskaliert wurde.

5. Nachdem die Zugriffsanforderung vom Azure-Techniker übermittelt wurde, wertet der JIT-Dienst die Anforderung unter Berücksichtigung von Faktoren wie den folgenden aus:
    - Bereich der Ressource
    - Ob die anfordernde Person eine isolierte Identität aufweist oder mehrstufige Authentifizierung ausführt
    - Berechtigungsebenen

    Basierend auf der JIT-Regel kann diese Anforderung auch eine Genehmigung von internen genehmigenden Personen bei Microsoft enthalten. Die genehmigende Person kann z. B. der Kundensupportleiter oder der DevOps-Manager sein.

6. Wenn für die Anforderung direkter Zugriff auf Kundendaten erforderlich ist, wird eine Kunden-Lockbox-Anforderung initiiert. Beispiel: Remotedesktopzugriff auf einen virtuellen Computer des Kunden.

    Die Anforderung befindet sich nun im Zustand **Kunde benachrichtigt** und wartet auf die Zustimmung des Kunden, bevor Zugriff gewährt wird.

7. Auf der Seite der Kundenorganisation erhält der Benutzer mit der [Rolle „Besitzer“](../../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) für das Azure Abonnement eine E-Mail von Microsoft, in der er über die ausstehende Anforderung benachrichtigt wird. Für Kunden-Lockbox-Anforderungen ist diese Person die festgelegte genehmigende Person.

    Beispiel-E-Mail:

    ![Azure Kunden-Lockbox – E-Mail-Benachrichtigung](./media/customer-lockbox-overview/customer-lockbox-email-notification.png)

8. Die E-Mail-Benachrichtigung enthält einen Link zum Blatt **Kunden-Lockbox** im Azure-Portal. Über diesen Link meldet sich die festgelegte genehmigende Person beim Azure-Portal an, um ausstehende Anforderungen anzuzeigen, die bei ihrer Organisation für Kunden-Lockbox vorliegen:

    ![Azure Kunden-Lockbox – Startseite](./media/customer-lockbox-overview/customer-lockbox-landing-page.png)

   Die Anforderung verbleibt vier Tage lang in der Kundenwarteschlange. Anschließend läuft die Zugriffsanforderung automatisch ab, und den Microsoft-Technikern wird kein Zugriff gewährt.

9. Um die Details der ausstehenden Anforderung abzurufen, kann die festgelegte genehmigende Person die Lockbox-Anforderung unter **Ausstehende Anforderungen** auswählen:

    ![Azure Kunden-Lockbox – Anzeigen der ausstehenden Anforderung](./media/customer-lockbox-overview/customer-lockbox-pending-requests.png)

10. Der festgelegte genehmigende Person kann auch die **DIENSTANFORDERUNGS-ID** auswählen, um die vom ursprünglichen Benutzer erstellte Supportticketanforderung anzuzeigen. Diese Informationen bieten einen Kontext für den Grund der Beteiligung des Microsoft-Supports und den Hintergrund des gemeldeten Problems. Beispiel:

    ![Azure Kunden-Lockbox – Anzeigen der Supportticketanforderung](./media/customer-lockbox-overview/customer-lockbox-support-ticket.png)

11. Nach dem Überprüfen der Anforderung wählt die festgelegte genehmigende Person **Genehmigen** oder **Verweigern** aus:

    ![Azure Kunden-Lockbox – Auswählen von „Genehmigen“ oder „Verweigern“](./media/customer-lockbox-overview/customer-lockbox-approval.png)

    Ergebnis der Auswahl:
    - **Genehmigen:**  Dem Microsoft-Techniker wird Zugriff gewährt. Der Zugriff gilt für eine Dauer von acht Stunden.
    - **Verweigern:** Die Anforderung nach erhöhten Zugriffsrechten durch den Microsoft-Techniker wird abgelehnt, und es wird keine weitere Aktion ausgeführt.

Für Überwachungszwecke werden die in diesem Workflow ausgeführten Aktionen in [Kunden-Lockbox-Anforderungsprotokollen](#auditing-logs) protokolliert.

## <a name="auditing-logs"></a>Überwachungsprotokolle

Kunden-Lockbox-Protokolle werden in Aktivitätsprotokollen gespeichert. Wählen Sie im Azure-Portal **Aktivitätsprotokolle** aus, um Überwachungsinformationen im Zusammenhang mit Kunden-Lockbox-Anforderungen anzuzeigen. Sie können nach bestimmten Aktionen filtern, z. B.:
- **Deny Lockbox Request** (Lockbox-Anforderung verweigern)
- **Create Lockbox Request** (Lockbox-Anforderung erstellen)
- **Approve Lockbox Request** (Lockbox-Anforderung genehmigen)
- **Lockbox Request Expiry** (Ablauf einer Lockbox-Anforderung)

Beispiel:

![Azure Kunden-Lockbox – Aktivitätsprotokolle](./media/customer-lockbox-overview/customer-lockbox-activitylogs.png)

## <a name="customer-lockbox-integration-with-azure-security-benchmark"></a>Kunden-Lockbox-Integration im Azure-Sicherheitsvergleichstest

Wir haben dem Azure-Sicherheitsvergleichstest eine neue Baselinekontrolle ([3.13](../benchmarks/security-control-identity-access-control.md#313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios)) für Kunden-Lockbox hinzugefügt. Kunden können nun den Vergleichstest nutzen, um die Anwendbarkeit von Kunden-Lockbox für einen Dienst zu überprüfen.

## <a name="exclusions"></a>Ausschlüsse

In den folgenden Szenarien mit technischem Support werden keine Kunden-Lockbox-Anforderungen ausgelöst:

- Ein Microsoft-Techniker muss eine Aktivität ausführen, die außerhalb der Standardbetriebsvorgänge liegt. Beispiel: Wiederherstellen von Diensten in unerwarteten oder unvorhersehbaren Szenarien.

- Ein Microsoft-Techniker greift im Rahmen der Problembehandlung auf die Azure-Plattform zu und hat versehentlich Zugriff auf Kundendaten. Beispiel: Das Azure-Team führt eine Problembehandlung durch, die zur Erfassung eines Pakets auf einem Netzwerkgerät führt. Wenn der Kunde die Daten jedoch während der Übertragung verschlüsselt hat, kann der Techniker die Daten nicht lesen.

## <a name="next-steps"></a>Nächste Schritte

Kunden-Lockbox steht für alle Kunden, die über einen [Azure-Supportplan](https://azure.microsoft.com/support/plans/) mit der Mindestebene **Developer** verfügen, automatisch zur Verfügung.

Wenn Sie über einen entsprechenden Supportplan verfügen, ist keine Aktion von Ihnen erforderlich, um Kunden-Lockbox zu aktivieren. Kunden-Lockbox-Anforderungen werden von einem Microsoft-Techniker initiiert, wenn diese Aktion zum Fortfahren mit einem Supportticket, das von einer anderen Person in Ihrer Organisation eröffnet wurde, erforderlich ist.