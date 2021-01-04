---
title: Verwalten von Migrationsprojekten im großen Maßstab mit Azure Migrate
description: Erfahren Sie, wie Sie Azure Migrate für delegierte Kundenressourcen effektiv verwenden können.
ms.date: 12/4/2020
ms.topic: how-to
ms.openlocfilehash: 16b92f3aa4dc3bfcb71eb232170c4df30348f8db
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97095388"
---
# <a name="manage-migration-projects-at-scale-with-azure-migrate"></a>Verwalten von Migrationsprojekten im großen Maßstab mit Azure Migrate

Als Dienstanbieter haben Sie möglicherweise mehrere Kundenmandanten in [Azure Lighthouse](../overview.md) integriert. Azure Lighthouse ermöglicht Dienstanbietern das gleichzeitige Ausführen von Vorgängen für mehrere Azure Active Directory-Mandanten (Azure AD) im großen Maßstab, wodurch Verwaltungsaufgaben effizienter werden.

[Azure Migrate](../../migrate/migrate-services-overview.md) verfügt über einen zentralisierten Hub zum Bewerten und Migrieren von lokalen Servern, Infrastrukturen, Anwendungen und Daten zu Azure. In der Regel müssen Partner, die Bewertungen und eine umfassende Migration für mehrere Kunden durchführen, auf jedes Kundenabonnement einzeln zugreifen, indem sie das [CSP-Abonnementmodell (Cloud Solution Provider)](/partner-center/customers-revoke-admin-privileges) verwenden oder [einen Gastbenutzer im Kundenmandanten erstellen](/azure/active-directory/external-identities/what-is-b2b).

Durch die Azure Lighthouse-Integration in Azure Migrate können Dienstanbieter Workloads für verschiedene Kunden umfassend ermitteln, bewerten und migrieren und gleichzeitig den Kunden eine vollständige Transparenz und Kontrolle ihrer Umgebungen ermöglichen. Dank der delegierten Azure-Ressourcenverwaltung verfügen Dienstanbieter über eine einzige Ansicht aller Azure Migrate-Projekte, die sie für mehrere Kundenmandanten verwalten.

> [!NOTE]
> Über Azure Lighthouse können Partner die Ermittlung, Bewertung und Migration für lokale VMware-VMs, Hyper-V-VMs, physische Server und AWS-/GCP-Instanzen durchführen. Für die [Migration von VMware-VMs](../../migrate/server-migrate-overview.md) sind zwei Optionen verfügbar. Derzeit kann bei der Arbeit an einem Migrationsprojekt in einem delegierten Kundenabonnement nur die Agent-basierte Migrationsmethode verwendet werden. Die Migration mithilfe der Replikation ohne Agent über den delegierten Zugriff auf den Bereich des Kunden wird derzeit nicht unterstützt.

Dieses Thema bietet einen Überblick über die Verwendung von [Azure Migrate](../../migrate/migrate-services-overview.md) im großen Maßstab.

> [!TIP]
> Zwar beziehen wir uns in diesem Thema auf Dienstanbieter und Kunden, doch gelten diese Anweisungen auch für [Unternehmen, die Azure Lighthouse zum Verwalten mehrerer Mandanten verwenden](../concepts/enterprise.md).

Abhängig von Ihrem Szenario möchten Sie möglicherweise das Azure Migrate-Projekt im Kundenmandanten oder in Ihrem Verwaltungsmandanten erstellen. Berücksichtigen Sie die folgenden Überlegungen, und bestimmen Sie, welches Modell für die Migrationsanforderungen Ihres Kunden am besten geeignet ist.

## <a name="create-an-azure-migrate-project-in-the-customer-tenant"></a>Erstellen eines Azure Migrate-Projekts im Kundenmandanten

Eine Option bei der Verwendung von Azure Lighthouse ist das Erstellen des Azure Migrate-Projekts im Kundenmandanten. Benutzer im Verwaltungsmandanten können dann das Kundenabonnement auswählen, wenn sie ein Migrationsprojekt erstellen. Über den Verwaltungsmandanten kann der Dienstanbieter die erforderlichen Migrationsvorgänge ausführen. Dies kann das Bereitstellen der Azure Migrate-Appliance zum Ermitteln der Workloads, das Bewerten von Workloads durch das Gruppieren von VMs und das Berechnen von cloudbezogenen Kosten, das Überprüfen der VM-Bereitschaft und das Durchführen der Migration umfassen.

In diesem Szenario werden keine Ressourcen im Verwaltungsmandanten erstellt und gespeichert, auch wenn die Ermittlungs- und Bewertungsschritte von diesem Mandanten initiiert und ausgeführt werden können. Alle Ressourcen (wie z. B. Migrationsprojekte, Bewertungsberichte für lokale Workloads und migrierte Ressourcen am Ziel) werden im Kundenabonnement bereitgestellt. Der Dienstanbieter kann jedoch über seinen eigenen Mandanten und über die eigene Portalbenutzeroberfläche auf alle Kundenprojekte zugreifen.

Dieser Ansatz minimiert den Kontextwechsel für Dienstanbieter, die kundenübergreifend arbeiten, und ermöglicht den Kunden die Beibehaltung all ihrer Ressourcen in ihren eigenen Mandanten.

Der Workflow für dieses Modell ähnelt dem folgenden Workflow:

1. Für den Kunden wird das [Onboarding in Azure Lighthouse durchgeführt](onboard-customer.md). Für die mit Azure Migrate verwendete Identität ist die integrierte Rolle „Mitwirkender“ erforderlich. Die Beispielvorlage [delegated-resource-management-azmigrate](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegated-resource-management-azmigrate) bietet ein Beispiel für die Verwendung dieser Rolle.
1. Der angegebene Benutzer meldet sich im Azure-Portal beim Verwaltungsmandanten an und wechselt dann zu Azure Migrate. Dieser Benutzer [erstellt ein Azure Migrate-Projekt](/azure/migrate/create-manage-projects) und wählt dabei das entsprechende delegierte Kundenabonnement aus.
1. Anschließend [führt der Benutzer Schritte zur Ermittlung und Bewertung aus](../../migrate/tutorial-discover-vmware.md).

   Bei VMware-VMs können Sie vor dem Konfigurieren der Appliance die Ermittlung auf vCenter Server-Rechenzentren, Cluster, einen Clusterordner, Hosts, einen Hostordner oder einzelne VMs beschränken. Um den Bereich festzulegen, weisen Sie dem Konto Berechtigungen zu, das die Appliance für den Zugriff auf die vCenter Server-Instanz verwendet. Dies ist nützlich, wenn die VMs mehrerer Kunden auf dem Hypervisor gehostet werden. Der Ermittlungsbereich von Hyper-V kann nicht eingeschränkt werden.

    > [!NOTE]
    > Sie können virtuelle VMware-Computer für die Migration mithilfe von Azure Migrate über den von Azure Lighthouse bereitgestellten delegierten Zugriff ermitteln und bewerten. Für die Migration von virtuellen VMware-Computern wird derzeit bei der Arbeit an einem Migrationsprojekt in einem delegierten Kundenabonnement nur die Agent-basierte Methode unterstützt.

1. Wenn das Zielkundenabonnement bereit ist, fahren Sie mit der Migration über den durch Azure Lighthouse gewährten Zugriff fort. Das Migrationsprojekt, das die Bewertungsergebnisse und die migrierten Ressourcen enthält, wird im Kundenmandanten unter dem Zielabonnement erstellt.

> [!TIP]
> Vor der Migration muss eine Zielzone bereitgestellt werden, um die grundlegenden Infrastrukturressourcen bereitzustellen und das Abonnement vorzubereiten, zu dem die virtuellen Computer migriert werden. Um auf einige Ressourcen in dieser Zielzone zugreifen oder diese erstellen zu können, ist möglicherweise die integrierte Rolle „Besitzer“ erforderlich, die in Azure Lighthouse derzeit nicht unterstützt wird. In solchen Szenarien muss der Kunde möglicherweise eine Rolle für den Gastzugriff bereitstellen oder den Administratorzugriff über das CSP-Abonnementmodell delegieren. Einen Ansatz zum Erstellen von mehrinstanzenfähigen Zielzonen finden Sie in der [Demolösung für Zielzonen mit mehreren Mandanten](https://github.com/Azure/Multi-tenant-Landing-Zones) auf GitHub.

## <a name="create-an-azure-migrate-project-in-the-managing-tenant"></a>Erstellen eines Azure Migrate-Projekts im Verwaltungsmandanten

In diesem Szenario werden migrationsbezogene Vorgänge wie die Ermittlung und Bewertung weiterhin von Benutzern im Verwaltungsmandanten ausgeführt. Das Migrationsprojekt und alle relevanten Ressourcen befinden sich jedoch im Partnermandanten, und der Kunde erhält keinen direkten Einblick in das Projekt (obwohl Bewertungen ggf. für Kunden freigegeben werden können). Das Migrationsziel für jeden Kunden ist das Abonnement des Kunden.

Bei diesem Ansatz können Dienstanbieter Migrationsermittlungs- und-bewertungsprojekte schnell starten und die ersten Schritte von Kundenabonnements und Mandanten abstrahieren.

Der Workflow für dieses Modell ähnelt dem folgenden Workflow:

1. Für den Kunden wird das [Onboarding in Azure Lighthouse durchgeführt](onboard-customer.md). Für die mit Azure Migrate verwendete Identität ist die integrierte Rolle „Mitwirkender“ erforderlich. Die Beispielvorlage [delegated-resource-management-azmigrate](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegated-resource-management-azmigrate) bietet ein Beispiel für die Verwendung dieser Rolle.
1. Der angegebene Benutzer meldet sich im Azure-Portal beim Verwaltungsmandanten an und wechselt dann zu Azure Migrate. Dieser Benutzer [erstellt ein Azure Migrate-Projekt](/azure/migrate/create-manage-projects) in einem Abonnement, das zum Verwaltungsmandanten gehört.
1. Anschließend [führt der Benutzer Schritte zur Ermittlung und Bewertung aus](../../migrate/tutorial-discover-vmware.md). Die lokalen VMs werden innerhalb des im Verwaltungsmandanten erstellten Migrationsprojekts ermittelt und bewertet und dann von dort migriert.

   Wenn Sie mehrere Kunden auf demselben Hyper-V-Host verwalten, können Sie alle Workloads gleichzeitig ermitteln. Kundenspezifische VMs können in derselben Gruppe ausgewählt werden. Dann kann eine Bewertung erstellt werden, und die Migration kann durchgeführt werden, indem das entsprechende Kundenabonnement als Ziel ausgewählt wird. Der Ermittlungsbereich muss nicht eingeschränkt werden, und Sie können einen vollständigen Überblick über alle Kundenworkloads in einem Migrationsprojekt erhalten.

1. Wenn Sie bereit sind, können Sie mit der Migration fortfahren, indem Sie das delegierte Kundenabonnement als Ziel für die Replikation und Migration der Workloads auswählen. Die neu erstellten Ressourcen sind im Kundenabonnement vorhanden, während die Bewertungsdaten und die Ressourcen für das Migrationsprojekt im Verwaltungsmandanten verbleiben.

HINWEIS: Sie müssen die Parameterdatei ändern, um Ihre Umgebung vor der Bereitstellung widerzuspiegeln (siehe https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegated-resource-management-azmigrate ).

## <a name="partner-recognition-for-customer-migrations"></a>Partnererkennung für Kundenmigrationen

Als Mitglied des [Microsoft Partner Network](https://partner.microsoft.com) können Sie Ihre Partner-ID mit den Anmeldeinformationen verknüpfen, die für die Verwaltung delegierter Kundenressourcen verwendet werden. Über Partner Admin Link (PAL) kann Microsoft Ihrer Organisation basierend auf den Aufgaben, die Sie für Kunden ausführen, einschließlich Migrationsprojekten, den Einfluss und den in Azure verbrauchten Umsatz zuordnen.

Weitere Informationen finden Sie im Thema zum [Verknüpfen der Partner-ID, um die Auswirkungen auf delegierte Ressourcen nachzuverfolgen](partner-earned-credit.md).

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über [Azure Migrate](../../migrate/migrate-services-overview.md).
- Erfahren Sie über [Mandantenübergreifende Verwaltungsmöglichkeiten](../concepts/cross-tenant-management-experience.md).

