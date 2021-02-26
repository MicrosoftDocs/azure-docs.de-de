---
title: Übersicht über Azure Disk Backup
description: Erfahren Sie mehr über die Azure Disk Backup-Lösung.
ms.topic: conceptual
ms.date: 01/07/2021
ms.openlocfilehash: 4db2a5f3f02322f18fcf9203c3560905cde86996
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98915507"
---
# <a name="overview-of-azure-disk-backup-in-preview"></a>Übersicht über Azure Disk Backup (Vorschau)

>[!IMPORTANT]
>Die Vorschauversion von Azure Disk Backup wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Informationen zur regionalen Verfügbarkeit finden Sie in der [Supportmatrix](disk-backup-support-matrix.md).
>
>[Füllen Sie dieses Formular aus](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR1vE8L51DIpDmziRt_893LVUNFlEWFJBN09PTDhEMjVHS05UWFkxUlUzUS4u), wenn Sie sich für die Vorschau registrieren möchten.

Azure Disk Backup ist eine native, cloudbasierte Sicherungslösung, die Ihre Daten auf verwalteten Datenträgern schützt. Es handelt sich um eine einfache, sichere und kostengünstige Lösung, mit der Sie den Schutz für verwaltete Datenträger in wenigen Schritten konfigurieren können. Damit wird sichergestellt, dass Sie Ihre Daten in einem Notfallszenario wiederherstellen können.

Azure Disk Backup bietet eine schlüsselfertige Lösung, die eine Verwaltung des Lebenszyklus von Momentaufnahmen für verwaltete Datenträger durch Automatisierung einer periodischen Erstellung von Momentaufnahmen und Aufbewahrung der Momentaufnahmen für eine konfigurierte Dauer unter Verwendung der Sicherungsrichtlinie bereitstellt. Sie können die Datenträgermomentaufnahmen ohne Infrastrukturkosten und ohne die Notwendigkeit von benutzerdefinierten Skripts oder jeglichem Verwaltungsaufwand verwalten. Es handelt sich um eine absturzsichere Sicherungslösung, die eine zeitpunktgenaue Sicherung eines verwalteten Datenträgers mit [inkrementellen Momentaufnahmen](../virtual-machines/disks-incremental-snapshots.md) mit Unterstützung für mehrere Sicherungen pro Tag vornimmt. Es handelt sich außerdem um eine Lösung ohne Agent, die die Leistung der Produktionsanwendung nicht beeinträchtigt. Sie unterstützt die Sicherung und Wiederherstellung der Betriebssystem- und regulären Datenträger (einschließlich gemeinsam genutzter Datenträger), unabhängig davon, ob sie derzeit mit einem ausgeführten virtuellen Azure-Computer verbunden sind oder nicht.

Wenn Sie eine anwendungskonsistente Sicherung des virtuellen Computers einschließlich der Datenträger oder eine Option zur Wiederherstellung einer gesamten VM aus der Sicherung, zur Wiederherstellung einer Datei oder eines Ordners oder zur Wiederherstellung in einer sekundären Region benötigen, dann verwenden Sie die Lösung [Azure VM Backup](backup-azure-vms-introduction.md). Azure Backup bietet parallele Unterstützung für die Sicherung verwalteter Datenträger mithilfe von Disk Backup zusätzlich zu [Azure VM Backup](./backup-azure-vms-introduction.md). Dies ist hilfreich, wenn Sie tägliche anwendungskonsistente Sicherungen von VMs und gleichzeitig häufigere absturzkonsistente Sicherungen des Betriebssystemdatenträgers oder eines bestimmten anderen Datenträgers durchführen möchten, ohne die Leistung der Produktionsanwendung zu beeinträchtigen.

Azure Disk Backup ist in das Backup Center integriert, wodurch eine **zentralisierte einheitliche Verwaltungsumgebung** in Azure bereitgestellt wird, mit der Unternehmen Sicherungen in großem Maßstab steuern, überwachen, betreiben und analysieren können.

## <a name="key-benefits-of-disk-backup"></a>Die wichtigsten Vorteile von Disk Backup

Azure Disk Backup ist eine absturzsichere Lösung ohne Agent, die [inkrementelle Momentaufnahmen](../virtual-machines/disks-incremental-snapshots.md) verwendet und die folgenden Vorteile bietet:

- Häufigere und schnellere Sicherungen ohne Unterbrechung des virtuellen Computers.
- Hat keinen Einfluss auf die Leistung der Produktionsanwendung.
- Keine Sicherheitsbedenken, da keine benutzerdefinierten Skripte ausgeführt oder Agents installiert werden müssen.
- Eine kostengünstige Lösung zur Sicherung bestimmter Datenträger im Vergleich zur Sicherung des gesamten virtuellen Computers.

Die Azure Disk Backup-Lösung ist in den folgenden Szenarien nützlich:

- Bedarf an häufigen Sicherungen pro Tag, ohne dass die Anwendung stillsteht
- Apps, die in einem Clusterszenario ausgeführt werden: sowohl Windows Server-Failovercluster als auch Linux-Cluster schreiben auf freigegebene Datenträger
- Spezifischer Bedarf an einer Sicherung ohne Agent aufgrund von Sicherheits- oder Leistungsbedenken bei der Anwendung
- Eine anwendungskonsistente Sicherung des virtuellen Computers ist nicht möglich, da Branchen-Apps keinen Volumeschattenkopie-Dienst (VSS) unterstützen.

Erwägen Sie Azure Disk Backup in Szenarien, in denen:

- eine geschäftskritische Anwendung auf einem virtuellen Azure-Computer ausgeführt wird, für den mehrere Sicherungen pro Tag erforderlich sind, um das RPO (Recovery Point Objective) zu erreichen, ohne jedoch die Produktionsumgebung oder die Anwendungsleistung zu beeinträchtigen
- Ihre Organisations- oder Branchenregelungen die Installation von Agents aufgrund von Sicherheitsbedenken einschränken
- das Ausführen von benutzerdefinierten Pre- und Post-Skripts und das Aufrufen von Skripts zum Einfrieren und Reaktivieren auf virtuellen Linux-Computern beim Erstellen anwendungskonsistenter Sicherungen die Verfügbarkeit der Produktionsworkloads durch übermäßigen Mehraufwand beeinträchtigt
- containerisierte Anwendungen, die auf Azure Kubernetes Service (AKS-Cluster) ausgeführt werden, verwaltete Datenträger als persistenten Speicher verwenden. Heutzutage müssen Sie den verwalteten Datenträger über Automatisierungsskripts sichern, die schwer zu verwalten sind.
- ein verwalteter Datenträger kritische Geschäftsdaten enthält, als Dateifreigabe verwendet wird oder Datenbanksicherungsdateien enthält, und Sie die Sicherungskosten optimieren möchten, indem Sie nicht in Azure VM-Backup investieren
- Sie verfügen über viele virtuelle Linux- und Windows-Computer mit einem einzelnen Datenträger (d. h. ein virtueller Computer mit nur einem Betriebssystem-Datenträger und ohne weitere angeschlossene Datenträger), auf dem Webserver- oder zustandslose Computer gehostet werden oder der als Stagingumgebung mit Anwendungskonfigurationseinstellungen dient, und Sie benötigen eine kostengünstige Sicherungslösung zum Schutz des Betriebssystem-Datenträgers. Um beispielsweise eine schnelle bedarfsgesteuerte Sicherung auszulösen, bevor ein Upgrade oder Patching der virtuellen Maschine durchgeführt wird,
- wird auf einem virtuellen Computer eine Betriebssystemkonfiguration ausgeführt, die von der Azure-VM-Sicherungslösung (z. B. Windows 2008 32-Bit-Server) nicht unterstützt wird.

## <a name="how-the-backup-and-restore-process-works"></a>Funktionsweise des Sicherungs- und Wiederherstellungsprozesses

- Der erste Schritt beim Konfigurieren der Sicherung für verwaltete Azure-Datenträger ist das Erstellen eines [Sicherungstresors](backup-vault-overview.md). Der Tresor bietet eine konsolidierte Ansicht der Sicherungen, die für unterschiedliche Workloads konfiguriert wurden.

- Erstellen Sie dann eine Sicherungsrichtlinie, mit der Sie die Sicherungshäufigkeit und die Aufbewahrungsdauer konfigurieren können.

- Um die Sicherung zu konfigurieren, wechseln Sie zum Sicherungstresor, weisen Sie eine Sicherungsrichtlinie zu, wählen Sie den verwalteten Datenträger aus, der gesichert werden muss, und geben Sie eine Ressourcengruppe an, in der die Momentaufnahmen gespeichert und verwaltet werden sollen. Azure Backup löst automatisch geplante Sicherungsaufträge aus, die entsprechend der Sicherungshäufigkeit inkrementelle Momentaufnahmen des Datenträgers erstellen. Ältere Momentaufnahmen werden entsprechend der in der Sicherungsrichtlinie festgelegten Aufbewahrungsdauer gelöscht.

- Azure Backup verwendet [inkrementelle Momentaufnahmen](../virtual-machines/disks-incremental-snapshots.md#restrictions) des verwalteten Datenträgers. Inkrementelle Momentaufnahmen sind eine kostengünstige, zeitpunktbezogene Sicherung von verwalteten Datenträgern, bei der die Delta-Änderungen am Datenträger seit der letzten Momentaufnahme berechnet werden. Sie werden immer auf dem kostengünstigsten Speicher, dem Standard-HDD-Speicher, gespeichert, unabhängig vom Speichertyp der übergeordneten Datenträger. Die erste Momentaufnahme des Datenträgers nimmt die genutzte Größe des Datenträgers ein, und die darauf folgenden inkrementellen Momentaufnahmen speichern Delta-Änderungen des Datenträgers seit der letzten Momentaufnahme.

- Sobald Sie die Sicherung eines verwalteten Datenträgers konfigurieren, wird eine Sicherungsinstanz im Sicherungstresor erstellt. Mit der Sicherungsinstanz können Sie den Zustand von Sicherungsvorgängen feststellen, bedarfsgesteuerte Sicherungen auslösen und Wiederherstellungsvorgänge durchführen. Sie können auch die Sicherungsintegrität über mehrere Tresore und Sicherungsinstanzen hinweg mithilfe von Backup Center anzeigen, das eine Ansicht in einer zentralisierten Benutzeroberfläche bietet.

- Zum Wiederherstellen wählen Sie einfach den Wiederherstellungspunkt aus, aus dem Sie den Datenträger wiederherstellen möchten. Geben Sie die Ressourcengruppe an, in der der wiederhergestellte Datenträger aus der Momentaufnahme erstellt werden soll. Azure Backup ermöglicht eine sofortige Wiederherstellung, da die Momentaufnahmen lokal in Ihrem Abonnement gespeichert werden.

- Backup Vault verwendet die verwaltete Identität für den Zugriff auf andere Azure-Ressourcen. Zum Konfigurieren der Sicherung eines verwalteten Datenträgers und zum Wiederherstellen aus einer vergangenen Sicherung benötigt die verwaltete Identität von Backup Vault eine Reihe von Berechtigungen für den Quelldatenträger, die Momentaufnahme-Ressourcengruppe, in der Momentaufnahmen erstellt und verwaltet werden, sowie die Zielressourcengruppe, in der Sie die Sicherung wiederherstellen möchten. Sie können der verwalteten Identität mithilfe der rollenbasierten Zugriffssteuerung von Azure (Azure RBAC) Berechtigungen erteilen. Eine verwaltete Identität ist ein spezieller Dienstprinzipal, der nur zusammen mit Azure-Ressourcen verwendet werden kann. Informieren Sie sich ausführlicher über [verwaltete Identitäten](../active-directory/managed-identities-azure-resources/overview.md).

- Derzeit unterstützt Azure Disk Backup operative Sicherungen von verwalteten Datenträgern, und kopiert die Sicherungen nicht in den Speicher des Sicherungstresors. Eine ausführliche Liste der unterstützten und nicht unterstützten Szenarien und regionalen Verfügbarkeit finden Sie in der [Supportmatrix](disk-backup-support-matrix.md).

## <a name="pricing"></a>Preise

Azure Backup bietet eine Lösung zur Verwaltung des Lebenszyklus von Momentaufnahmen zum Schutz von Azure-Datenträgern. Die von Azure Backup erstellten Datenträger-Momentaufnahmen werden in der Ressourcengruppe innerhalb Ihres Azure-Abonnements gespeichert und verursachen Gebühren für **Momentaufnahmenspeicherung**. Weitere Details zu den Preisen für Momentaufnahmen finden Sie unter [Preise für verwaltete Datenträger](https://azure.microsoft.com/pricing/details/managed-disks/). Da die Momentaufnahmen nicht in den Sicherungstresor kopiert werden, erhebt Azure Backup keine Gebühr für **geschützte Instanzen**, und es fallen keine Kosten für den **Sicherungsspeicher** an. Zusätzlich belegen inkrementelle Momentaufnahmen Delta-Änderungen seit der letzten Momentaufnahme und werden immer auf Standardspeicher gespeichert, unabhängig vom Speichertyp der übergeordneten verwalteten Datenträger, und werden entsprechend der Preise für Standardspeicher berechnet. Dadurch ist Azure Disk Backup eine kostengünstige Lösung.

## <a name="next-steps"></a>Nächste Schritte

- [Supportmatrix für die Azure Disk-Sicherung](disk-backup-support-matrix.md)
