---
title: Notfallwiederherstellung von virtuellen VMware-Computern für mehrere Mandanten mit Azure Site Recovery
description: Dieser Artikel enthält eine Übersicht über die Azure Site Recovery-Unterstützung für die VMware-Notfallwiederherstellung in Azure in einer Umgebung mit mehreren Mandanten über das CSP-Programm.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: mayg
ms.openlocfilehash: 64e40341ec56a2e1c561b2bcbb5e584830c14015
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/01/2020
ms.locfileid: "93145581"
---
# <a name="overview-of-multi-tenant-support-for-vmware-disaster-recovery-to-azure-with-csp"></a>Übersicht über die Unterstützung mehrerer Mandanten für die VMware-Notfallwiederherstellung in Azure mit CSP

[Azure Site Recovery](site-recovery-overview.md) unterstützt für Mandantenabonnements Umgebungen mit mehreren Mandanten. Unterstützt wird außerdem die Mehrmandantenfähigkeit für Mandantenabonnements, die über das Microsoft Cloud Solution Provider-Programm (CSP) erstellt und verwaltet werden.

Dieser Artikel enthält eine Übersicht über die Implementierung und Verwaltung von VMware-zu-Azure-Replikationen mit mehreren Mandanten.

## <a name="multi-tenant-environments"></a>Umgebungen mit mehreren Mandanten

Es gibt drei wesentliche Modelle mit mehreren Mandanten:

* **Anbieter von gemeinsam genutzten Hostingdiensten** : Der Partner besitzt die physische Infrastruktur und arbeitet mit gemeinsam genutzten Ressourcen (vCenter, Rechenzentren, physischem Speicher usw.), um die VMs mehrerer Mandanten in derselben Infrastruktur zu hosten. Der Partner kann die Verwaltung der Notfallwiederherstellung als verwalteten Dienst bereitstellen, oder der Mandant kann die Notfallwiederherstellung als Self-Service-Lösung betreiben.

* **Anbieter von dedizierten Hostingdiensten** : Der Partner besitzt die physische Infrastruktur, verwendet aber dedizierte Ressourcen (mehrere vCenter-Server, physische Datenspeicher usw.), um die virtuellen Computer der einzelnen Mandanten jeweils in einer separaten Infrastruktur zu hosten. Der Partner kann die Verwaltung der Notfallwiederherstellung als verwalteten Dienst bereitstellen, oder der Mandant kann diese als Self-Service-Lösung betreiben.

* **Anbieter verwalteter Dienste** : Der Kunde besitzt die physische Infrastruktur, in der die VMs gehostet werden, während der Partner für die Aktivierung und Verwaltung der Notfallwiederherstellung zuständig ist.

## <a name="shared-hosting-services-provider-hsp"></a>Shared Hosting Services Provider (SHSP)

Die anderen beiden Szenarien sind eine Teilmenge des SHSP-Szenarios und folgen denselben Prinzipien. Die Unterschiede werden am Ende der SHSP-Anleitung beschrieben.

Die grundlegende Anforderung in einem Szenario mit mehreren Mandanten ist deren Isolation. Ein Mandant darf nicht wahrnehmen, was ein anderer Mandant hostet. In einer von einem Partner verwalteten Umgebung ist diese Anforderung nicht so wichtig wie für eine Self-Service-Umgebung, in der sie kritisch sein kann. In diesem Artikel wird davon ausgegangen, dass die Mandantenisolation erforderlich ist.

Die Architektur ist in der folgenden Abbildung dargestellt:

![SHSP mit einem vCenter](./media/vmware-azure-multi-tenant-overview/shared-hosting-scenario.png)  

**SHSP mit vCenter Server**

In der Abbildung hat jeder Kunde einen eigenen Verwaltungsserver. Diese Konfiguration beschränkt den Mandantenzugriff auf mandantenspezifische VMs und ermöglicht die Isolation von Mandanten. Bei der Replikation von VMware-VMs wird der Konfigurationsserver verwendet, um VMs zu ermitteln und Agents zu installieren. Die gleichen Prinzipien gelten für Umgebungen mit mehreren Mandanten, wobei die VM-Ermittlung mit der vCenter-Zugriffssteuerung zusätzlich eingeschränkt wird.

Aufgrund der Datenisolationsanforderung dürfen gegenüber den Mandanten keine sensiblen Infrastrukturinformationen (wie etwa Anmeldeinformationen für den Zugriff) offengelegt werden. Aus diesem Grund wird empfohlen, dass alle Komponenten des Verwaltungsservers unter der alleinigen Kontrolle des Partners verbleiben. Der Verwaltungsserver hat die folgenden Komponenten:

* Konfigurationsserver
* Prozessserver
* Masterzielserver

Ein horizontal skalierter Verarbeitungsserver wird ebenfalls vom Partner gesteuert.

## <a name="configuration-server-accounts"></a>Konfigurationsserverkonten

Jeder Konfigurationsserver im Szenario mit mehreren Mandanten verwendet zwei Konten:

- **vCenter-Zugriffskonto** : Dieses Konto dient zum Ermitteln von Mandanten-VMs. Diesem sind vCenter-Zugriffsberechtigungen zugewiesen. Um Zugriffslücken zu vermeiden, wird empfohlen, dass Partner diese Anmeldeinformationen selbst in das Konfigurationstool eingeben.

- **VM-Zugriffskonto** : Dieses Konto dient zum Installieren des Mobilitätsdienst-Agents auf den VMs des Mandanten mittels automatischer Übertragung mithilfe von Push. Hierbei handelt es sich in der Regel um ein Domänenkonto, das ein Mandant ggf. für einen Partner bereitstellt, oder ein Konto, das der Partner direkt verwaltet. Wenn ein Mandant die Details nicht direkt an den Partner weitergeben möchte, kann die Eingabe der Anmeldeinformationen im Rahmen eines zeitlich begrenzten Zugriffs auf den Konfigurationsserver ermöglicht werden. Alternativ kann der Mandant die Mobility Service-Agents in Zusammenarbeit mit dem Partner manuell installieren.

## <a name="vcenter-account-requirements"></a>Anforderungen an das vCenter-Konto

Konfigurieren Sie den Konfigurationsserver mit einem Konto, dem eine besondere Rolle zugewiesen ist.

- Diese Rollenzuweisung muss für das vCenter-Zugriffskonto für jedes vCenter-Objekt erfolgen und darf nicht an die untergeordneten Objekte weitergegeben werden. Diese Konfiguration stellt die Mandantenisolation sicher, da die Weitergabe von Zugangsdaten zu einem versehentlichen Zugriff auf andere Objekte führen kann.

    ![Die Option „An untergeordnete Objekte weitergeben“](./media/vmware-azure-multi-tenant-overview/assign-permissions-without-propagation.png)

- Die alternative Vorgehensweise besteht darin, das Benutzerkonto und die Rolle dem Rechenzentrumsobjekt zuzuweisen und diese an die untergeordneten Objekte weiterzugeben. Weisen Sie anschließend dem Konto die Rolle **Kein Zugriff** für alle Objekte zu (z.B. VMs, die anderen Mandanten gehören), auf die ein bestimmter Mandant keinen Zugriff haben soll. Diese Konfiguration ist mühsam. Dabei besteht das Risiko einer versehentlichen Preisgabe von Zugangsdaten, da jedem neuen untergeordneten Objekt auch automatisch die vom übergeordneten Objekt geerbten Zugangsrechte erteilt werden. Aus diesem Grund wird empfohlen, die erste Methode zu verwenden.

### <a name="create-a-vcenter-account"></a>Erstellen eines vCenter-Kontos

1. Erstellen Sie eine neue Rolle durch Klonen der vordefinierten Rolle *Schreibgeschützt* , und geben Sie ihr einen passenden Namen (z.B. „Azure_Site_Recovery“ wie in diesem Beispiel).
2. Weisen Sie dieser Rolle die folgenden Berechtigungen zu:

   * **Datenspeicher** : Speicherplatz zuordnen, Datenspeicher durchsuchen, Low-Level-Dateivorgänge, Datei entfernen, Dateien virtueller Computer aktualisieren
   * **Netzwerk:** Netzwerk zuweisen
   * **Ressource:** VM zu Ressourcenpool zuweisen, ausgeschaltete VM migrieren, eingeschaltete VM migrieren
   * **Tasks** : Aufgabe erstellen, Aufgabe aktualisieren
   * **VM – Konfiguration** : All
   * **VM – Interaktion** > Frage beantworten, Geräteverbindung, CD-Medien konfigurieren, Diskettenmedien konfigurieren, Ausschalten, Einschalten, VMware-Tools installieren
   * **VM – Bestand** > Aus vorhandenen erstellen, Neu erstellen, Registrieren, Registrierung aufheben
   * **VM – Bereitstellung** > Download virtueller Computer zulassen, Upload von Dateien virtueller Computer zulassen
   * **VM – Momentaufnahmeverwaltung** > Momentaufnahmen entfernen

       ![Das Dialogfeld „Rolle bearbeiten“](./media/vmware-azure-multi-tenant-overview/edit-role-permissions.png)

3. Weisen Sie dem vCenter-Konto (das im Konfigurationsserver des Mandanten verwendet wird) für verschiedene Objekte Zugriffsebenen wie folgt zu:

>| Object | Role | Bemerkungen |
>| --- | --- | --- |
>| vCenter | Schreibgeschützt | Nur erforderlich, um vCenter den Zugriff zum Verwalten verschiedener Objekten zu gewähren. Sie können diese Berechtigung aufheben, wenn das Konto keinem Mandanten zur Verfügung gestellt wird oder nicht für Verwaltungsvorgänge in vCenter verwendet wird. |
>| Datacenter | Azure_Site_Recovery |  |
>| Host und Hostcluster | Azure_Site_Recovery | Stellt erneut sicher, dass der Zugriff auf Objektebene erfolgt, sodass es nur Zugriff auf Hosts gibt, die vor dem Failover und nach dem Failback über Mandanten-VMs verfügen. |
>| Datenspeicher und Datenspeichercluster | Azure_Site_Recovery | Siehe oben. |
>| Netzwerk | Azure_Site_Recovery |  |
>| Verwaltungsserver | Azure_Site_Recovery | Schließt den Zugriff auf alle Komponenten (Konfigurationsserver, Prozessserver und Masterzielserver) außerhalb des Konfigurationsservercomputers ein. |
>| Mandanten-VMs | Azure_Site_Recovery | Stellt sicher, dass alle neuen VMs eines bestimmten Mandanten auch diesen Zugriff erhalten, da sie andernfalls nicht im Azure-Portal ermittelt werden können. |

Der vCenter-Kontozugriff ist nun eingerichtet. Mit diesem Schritt ist die Anforderung an die Mindestberechtigungen zum Durchführen von Failbackvorgängen erfüllt. Diese Zugriffsberechtigungen können auch mit Ihren vorhandenen Richtlinien verwendet werden. Ändern Sie Ihre vorhandenen Berechtigungen lediglich so, dass die unter Punkt 2 zuvor genannten Rollenberechtigungen hinzugefügt werden.

### <a name="failover-only"></a>Nur Failover
Um Notfallwiederherstellungsvorgänge ausschließlich auf das Failover zu beschränken (d.h. ohne Failbackfunktionen), verwenden Sie die vorherige Vorgehensweise mit folgenden Ausnahmen:

- Anstatt die Rolle *Azure_Site_Recovery* dem vCenter-Zugriffskonto zuzuweisen, weisen Sie diesem Konto nur die Rolle *Schreibgeschützt* zu. Dieser Berechtigungssatz lässt die Replikation und ein Failover von VMs, aber kein Failback zu.
- Alles andere im vorhergehenden Prozess bleibt unverändert. Berechtigungen werden weiterhin nur auf Objektebene zugewiesen und nicht an untergeordnete Objekte weitergegeben, um die Mandantenisolation sicherzustellen und die VM-Ermittlung einzuschränken.

### <a name="deploy-resources-to-the-tenant-subscription"></a>Bereitstellen von Ressourcen für das Mandantenabonnement

1. Erstellen Sie im Azure-Portal eine Ressourcengruppe, und stellen Sie dann einen Recovery Services-Tresor wie üblich bereit.
2. Laden Sie den Tresorregistrierungsschlüssel herunter.
3. Registrieren Sie den Konfigurationsserver des Mandanten mithilfe des Tresorregistrierungsschlüssels.
4. Geben Sie die Anmeldeinformationen für die beiden Zugriffskonten (das Konto für den Zugriff auf den vCenter-Server und das Konto für den Zugriff auf den virtuellen Computer) ein.

    ![Verwalten von Konfigurationsserverkonten](./media/vmware-azure-multi-tenant-overview/config-server-account-display.png)

### <a name="register-servers-in-the-vault"></a>Registrieren von Servern im Tresor

1. Registrieren Sie den vCenter-Server im Azure-Portal in dem zuvor erstellten Tresor auf dem Konfigurationsserver. Verwenden Sie dazu das erstellte vCenter-Konto.
2. Beenden Sie den Prozess zur Vorbereitung der Infrastruktur für Site Recovery wie üblich.
3. Die VMs können jetzt repliziert werden. Vergewissern Sie sich, dass in **Replizieren** > **Virtuelle Computer auswählen** nur die VMs des Mandanten angezeigt werden.

## <a name="dedicated-hosting-solution"></a>Dedizierte Hostinglösung (DHPS)

Wie im folgenden Diagramm dargestellt, ist der architektonische Unterschied bei einer dedizierten Hostinglösung, dass die Infrastruktur jedes Mandanten allein für den jeweiligen Mandanten eingerichtet ist.

![Diagramm des architektonischen Unterschieds bei einer dedizierten Hostinglösung, bei der die Infrastruktur jedes Mandanten allein für den jeweiligen Mandanten eingerichtet wird](./media/vmware-azure-multi-tenant-overview/dedicated-hosting-scenario.png)  
**Dediziertes Hostingszenario mit mehreren vCentern**

## <a name="managed-service-solution"></a>MSP-Lösung (Managed Services Provider, Managed Services Provider)

Wie im folgenden Diagramm dargestellt, ist der architektonische Unterschiede bei einer MSP-Lösung, dass die Infrastruktur jedes Mandanten auch physisch von der Infrastruktur anderen Mandanten getrennt ist. Dieses Szenario liegt in der Regel vor, wenn der Mandant die Infrastruktur besitzt und lediglich möchte, dass sich ein Lösungsanbieter um die Notfallwiederherstellung kümmert.

![Architektur beim SHPS-Modell](./media/vmware-azure-multi-tenant-overview/managed-service-scenario.png)  
**MSP-Szenario mit mehreren vCentern**

## <a name="next-steps"></a>Nächste Schritte
- Erfahren Sie mehr zur [rollenbasierten Zugriffssteuerung in Site Recovery](site-recovery-role-based-linked-access-control.md).
- Machen Sie sich mit dem [Einrichten der Notfallwiederherstellung in Azure für lokale VMware-VMs](vmware-azure-tutorial.md) vertraut.
- Informieren Sie sich über [Mehrinstanzenfähigkeit mit CSP für VMWare-VMs](vmware-azure-multi-tenant-csp-disaster-recovery.md).
