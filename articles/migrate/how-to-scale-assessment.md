---
title: Skalieren von Ermittlung und Bewertung mit Azure Migrate | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie eine große Anzahl lokaler Computer mit dem Azure Migrate-Dienst bewerten.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: raynew
ms.openlocfilehash: fe86c758dbf05f91d53cb918b7794c12ab3f39bc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65518753"
---
# <a name="discover-and-assess-a-large-vmware-environment"></a>Ermitteln und Bewerten einer umfangreichen VMware-Umgebung

Bei Azure Migrate gilt eine Beschränkung von 1500 Computern pro Projekt. In diesem Artikel erfahren Sie, wie Sie eine große Anzahl lokaler virtueller Computer (VMs) mit [Azure Migrate](migrate-overview.md) bewerten.

> [!NOTE]
> Wir verfügen über eine Vorschauversion, die eine Ermittlung von bis zu 10.000 VMware-VMs in einem einzigen Projekt mit einer einzigen Instanz ermöglicht. Wenn Sie diese Version gerne testen möchten, melden Sie sich bitte [hier](https://aka.ms/migratefuture) an.

## <a name="prerequisites"></a>Voraussetzungen

- **VMware**: Die virtuellen Computer, die Sie migrieren möchten, müssen über die vCenter Server-Version 5.5, 6.0, 6.5 oder 6.7 verwaltet werden. Zusätzlich benötigen Sie einen ESXi-Host mit der Version 5.5 oder höher, um die Collector-VM bereitzustellen.
- **vCenter-Konto**: Sie benötigen ein schreibgeschütztes Konto, um auf vCenter Server zugreifen zu können. Dieses Konto wird in Azure Migrate zum Ermitteln der lokalen virtuellen Computer verwendet.
- **Berechtigungen:** In vCenter Server benötigen Sie Berechtigungen zum Erstellen eines virtuellen Computers durch Importieren einer Datei im OVA-Format.
- **Statistikeinstellungen**: Diese Anforderung gilt nur für das [Modell für einmalige Ermittlung](https://docs.microsoft.com/azure/migrate/concepts-collector), das nun veraltet ist. Für das Modell für einmalige Ermittlung sollten die Statistikeinstellungen für vCenter Server vor der Bereitstellung auf Ebene 3 festgelegt werden. Die Statistikebene muss für das Tages-, Wochen- und Monatssammlungsintervall jeweils auf 3 festgelegt werden. Bei einer niedrigeren Ebene für eines dieser Sammlungsintervalle wird die Bewertung zwar ausgeführt, die Leistungsdaten für den Speicher und das Netzwerk werden jedoch nicht erfasst. Die Größenempfehlungen werden dann auf der Grundlage von Leistungsdaten für CPU und Arbeitsspeicher sowie basierend auf den Konfigurationsdaten für Datenträger- und Netzwerkadapter erstellt.

> [!NOTE]
> Die Appliance zur einmaligen Ermittlung ist inzwischen veraltet, da diese Methode bei der Verfügbarkeit der Leistungsdatenpunkte von den Statistikeinstellungen von vCenter Server abhängig war und durchschnittliche Leistungsindikatoren erfasst hat, was zu unterdimensionierten virtuellen Computern für die Migration zu Azure führte.

### <a name="set-up-permissions"></a>Einrichten von Berechtigungen

Azure Migrate benötigt Zugriff auf VMware-Server, um automatisch virtuelle Computer für die Bewertung ermitteln zu können. Für das VMware-Konto sind die folgenden Berechtigungen erforderlich:

- Benutzertyp: Mindestens ein Benutzer mit Lesezugriff
- Berechtigungen: Data Center object (Rechenzentrenobjekt) –> Propagate to Child Object (An untergeordnetes Objekt weitergeben), role=Read-only (Rolle=schreibgeschützt)
- Details: Der Benutzer wird auf Datencenterebene zugewiesen und hat Zugriff auf alle Objekte im Datencenter.
- Um den Zugriff einzuschränken, weisen Sie den untergeordneten Objekten (vSphere-Hosts, Datenspeicher, VMs und Netzwerke) die Rolle „No access“ (Kein Zugriff) mit „Propagate to child object“ (An untergeordnetes Objekt weitergeben) zu.

Wenn Sie in einer Umgebung mit mehreren Mandanten bereitstellen und den Bereich für einen einzelnen Mandanten anhand des Ordners der VMs festlegen möchten, können Sie beim Festlegen des Bereichs einer Sammlung in Azure Migrate nicht direkt den VM-Ordner auswählen. Es folgen Anweisungen zum Festlegen des Ermittlungsbereichs anhand des VM-Ordners:

1. Erstellen Sie pro Mandant einen Benutzer, und weisen Sie allen VMs, die zu einem bestimmten Mandanten gehören, schreibgeschützte Zugriffsberechtigungen zu. 
2. Erteilen Sie diesem Benutzer schreibgeschützten Zugriff auf alle übergeordneten Objekte, in denen VMs gehostet sind. Alle übergeordneten Objekte – Host, Hostordner, Cluster, Clusterordner – in der Hierarchie bis hinauf zum Rechenzentrum müssen eingeschlossen werden. Sie brauchen die Berechtigungen nicht an alle untergeordneten Objekte weiterzugeben.
3. Verwenden Sie die Anmeldeinformationen für die Ermittlung, und wählen Sie das Rechenzentrum als *Sammlungsbereich* aus. Durch die eingerichtete rollenbasierte Zugriffssteuerung wird sichergestellt, dass der entsprechende vCenter-Benutzer nur Zugriff auf die mandantenspezifischen VMs hat.

## <a name="plan-your-migration-projects-and-discoveries"></a>Planen von Migrationsprojekten und Ermittlungen

Basierend auf der Anzahl von VMs, die Sie ermitteln möchten, können Sie mehrere Projekte erstellen und mehrere Appliances in Ihrer Umgebung bereitstellen. Eine Appliance kann mit einem einzelnen vCenter Server und einem einzelnen Projekt verbunden sein (außer Sie beenden die Ermittlung und starten neu).

Bei einmaliger Ermittlung (jetzt veraltet) gilt für die Ermittlung das Prinzip eines Fire-and-Forget-Modells, d. h., nach Abschluss einer Ermittlung kann derselbe Collector verwendet werden, um Daten einer anderen vCenter Server-Instanz zu sammeln oder sie an ein anderes Migrationsprojekt zu senden.

> [!NOTE]
> Die Appliance zur einmaligen Ermittlung ist inzwischen veraltet, da diese Methode bei der Verfügbarkeit der Leistungsdatenpunkte von den Statistikeinstellungen von vCenter Server abhängig war und durchschnittliche Leistungsindikatoren erfasst hat, was zu unterdimensionierten virtuellen Computern für die Migration zu Azure führte. Es wird empfohlen, zur Appliance mit kontinuierlicher Ermittlung zu wechseln.

Planen Sie Ihre Ermittlungen und Bewertungen basierend auf den folgenden Limits:

| **Entität** | **Limit für Computer** |
| ---------- | ----------------- |
| Project    | 1\.500             |
| Ermittlung  | 1\.500             |
| Bewertung | 1\.500             |

Berücksichtigen Sie bei der Planung Folgendes:

- Wenn Sie eine Ermittlung mit dem Azure Migrate-Collector durchführen, können Sie den Ermittlungsbereich auf einen vCenter Server-Order, ein Rechenzentrum, einen Cluster oder einen Host festlegen.
- Wenn Sie mehrere Ermittlungen aus demselben vCenter Server durchführen möchten, vergewissern Sie sich in vCenter Server, dass sich die zu ermittelnden VMs in Ordnern, Rechenzentren, Clustern oder Hosts befinden, die die Beschränkung auf 1.500 Computer unterstützen.
- Zur Bewertung sollten sich Computer mit Abhängigkeiten im gleichen Projekt und in der gleichen Bewertung befinden. Achten Sie daher in vCenter Server darauf, dass sich abhängige Computer zur Bewertung im gleichen Ordner, Rechenzentrum, Cluster oder Host befinden.

Je nach Szenario können Sie Ihre Ermittlungen wie folgt aufteilen:

### <a name="multiple-vcenter-servers-with-less-than-1500-vms"></a>Bei mehreren vCenter Server-Instanzen mit weniger als 1500 VMs
Wenn Ihre Umgebung mehrere vCenter-Server enthält und die Gesamtanzahl virtueller Computer unter 1.500 liegt, können Sie basierend auf Ihrem Szenario folgendermaßen vorgehen:

**Kontinuierliche Ermittlung**: Bei kontinuierlicher Ermittlung kann eine Appliance mit nur einem einzelnen Projekt verbunden werden. Sie müssen daher eine Appliance für jeden vCenter-Server bereitstellen und dann ein Projekt für jede Appliance erstellen und die Ermittlungen entsprechend auslösen.

**Einmalige Ermittlung (jetzt veraltet):** Sie können einen einzelnen Collector und ein einziges Migrationsprojekt zum Ermitteln der virtuellen Computer für alle vCenter-Server verwenden. Da der Collector bei einmaliger Ermittlung jeweils nur eine vCenter Server-Instanz ermittelt, können Sie denselben Collector nacheinander für alle vCenter Server-Instanzen ausführen und ihn auf dasselbe Migrationsprojekt verweisen. Nach Abschluss aller Ermittlungen lassen sich Bewertungen für die Computer erstellen.


### <a name="multiple-vcenter-servers-with-more-than-1500-vms"></a>Bei mehreren vCenter Server-Instanzen mit mehr als 1500 VMs

Wenn Sie mehrere vCenter Server-Instanzen mit weniger als 1.500 virtuellen Computern pro vCenter Server-Instanz, aber mehr als 1.500 virtuelle Computer über alle vCenter Server-Instanzen hinweg haben, müssen Sie mehrere Migrationsprojekte erstellen, da ein einzelnes Migrationsprojekt nur 1.500 virtuelle Computer enthalten kann. Hierzu können Sie ein Migrationsprojekt pro vCenter Server-Instanz erstellen und die Ermittlungen aufteilen.

**Kontinuierliche Ermittlung**: Sie müssen mehrere Collectorappliances (eine für jede vCenter Server-Instanz) erstellen und jede Einheit mit einem Projekt verbinden und die Ermittlung entsprechend auslösen.

**Einmalige Ermittlung (jetzt veraltet):** Sie können jede vCenter Server-Instanz (nacheinander) mithilfe eines einzigen Collectors ermitteln. Wenn Sie möchten, dass die Ermittlungen zeitgleich beginnen, lassen sie sich durch die Bereitstellung mehrerer Appliances parallel ausführen.

### <a name="more-than-1500-machines-in-a-single-vcenter-server"></a>Bei mehr als 1500 Computern in einer einzigen vCenter Server-Instanz

Wenn Sie mehr als 1500 virtuelle Computer in einer einzelnen vCenter Server-Instanz haben, müssen Sie die Ermittlungen auf mehrere Migrationsprojekte aufteilen. Zum Aufteilen der Ermittlungen können Sie in der Appliance das Feld „Bereich“ nutzen und die folgenden zu ermittelnden Daten angeben: Host, Cluster, Hostordner, Clusterordner oder Rechenzentrum. Angenommen, Sie haben zwei Ordner in der vCenter Server-Instanz – einen mit 1.000 VMs (Ordner1) und einen anderen mit 800 VMs (Ordner2). In diesem Fall können Sie mit dem Feld „Bereich“ die Ermittlung zwischen diesen Ordnern aufteilen.

**Kontinuierliche Ermittlung**: In diesem Fall müssen Sie zwei Collectorappliances erstellen. Geben Sie dann für den ersten Collector den Bereich als „Ordner1“ an, und verbinden Sie ihn mit dem ersten Migrationsprojekt. Parallel dazu können Sie die Ermittlung von Ordner2 mithilfe der zweiten Collectorappliance starten und sie mit dem zweiten Migrationsprojekt verbinden.

**Einmalige Ermittlung (jetzt veraltet):** Sie können mit demselben Collector beide Ermittlungen auslösen. Für die erste Ermittlung können Sie Ordner1 als Bereich festlegen und ihn auf das erste Migrationsprojekt verweisen. Nach Abschluss der ersten Ermittlung lässt sich derselbe Collector verwenden. Ändern Sie dazu seinen Bereich zu Ordner2, passen Sie die Details zum Migrationsprojekt an das zweite Migrationsprojekt an und führen Sie die zweite Ermittlung aus.

### <a name="multi-tenant-environment"></a>Umgebungen mit mehreren Mandanten

Wenn Sie bei einer von mehreren Mandanten gemeinsam genutzten Umgebung keine VMs eines Mandanten im Abonnement eines anderen ermitteln möchten, können Sie das Feld „Bereich“ in der Collectorappliance nutzen, um den Bereich der Ermittlung festzulegen. Sollten Mandanten Hosts gemeinsam nutzen, erstellen Sie Anmeldeinformationen, die nur auf diejenigen VMs einen schreibgeschützten Zugriff ermöglichen, die dem bestimmten Mandanten gehören. Verwenden Sie dann diese Anmeldeinformationen in der Collectorappliance und geben Sie den Bereich als Host für die Ermittlung an.

## <a name="discover-on-premises-environment"></a>Ermitteln der lokalen Umgebung

Ist Ihre Planung abgeschlossen, können Sie mit dem Ermitteln der lokalen virtuellen Computer beginnen:

### <a name="create-a-project"></a>Erstellen eines Projekts

Erstellen Sie ein für Ihre Anforderungen geeignetes Azure Migrate-Projekt:

1. Klicken Sie im Azure-Portal auf **Ressource erstellen**.
2. Suchen Sie nach **Azure Migrate**, und wählen Sie in den Suchergebnissen den Dienst **Azure Migrate** aus. Klicken Sie anschließend auf **Erstellen**.
3. Geben Sie einen Projektnamen und das Azure-Abonnement für das Projekt an.
4. Erstellen Sie eine neue Ressourcengruppe.
5. Geben Sie den Standort an, an dem das Projekt erstellt werden soll, und klicken Sie auf **Erstellen**. Beachten Sie, dass Sie Ihre VMs weiterhin für einen anderen Zielstandort bewerten können. Der für das Projekt angegebene Standort wird zum Speichern der Metadaten verwendet, die von den lokalen VMs erfasst werden.

### <a name="set-up-the-collector-appliance"></a>Einrichten der Collectorappliance

Azure Migrate erstellt einen lokalen virtuellen Computer, der als „Collectorappliance“ bezeichnet wird. Diese VM ermittelt lokale virtuelle VMware-Computer und sendet Metadaten zu diesen Computern an den Azure Migrate-Dienst. Zum Einrichten der Collectorappliance laden Sie eine OVA-Datei herunter und importieren sie auf die lokale vCenter Server-Instanz.

#### <a name="download-the-collector-appliance"></a>Herunterladen der Collectorappliance

Wenn Sie über mehrere Projekte verfügen, müssen Sie die Collectorappliance nur einmal auf die vCenter Server-Instanz herunterladen. Nach dem Herunterladen und Einrichten der Appliance müssen Sie diese für jedes Projekt ausführen und jeweils die eindeutige Projekt-ID und den Schlüssel angeben.

1. Klicken Sie im Azure Migrate-Projekt auf **Erste Schritte** > **Ermitteln und bewerten** > **Computer ermitteln**.
2. Klicken Sie unter **Computer ermitteln** auf **Herunterladen**, um die Appliance herunterzuladen.

    Die Azure Migrate-Appliance kommuniziert mit vCenter Server und erstellt kontinuierlich Profile der lokalen Umgebung, um in Echtzeit Nutzungsdaten für jeden virtuellen Computer zu erfassen. Sie erfasst Spitzenleistungsindikatoren für jede Metrik (CPU-Auslastung, Arbeitsspeicherauslastung usw.). Dieses Modell ist nicht von den Statistikeinstellungen von vCenter Server für die Sammlung von Leistungsdaten abhängig. Sie können die kontinuierliche Profilerstellung jederzeit von der Appliance aus beenden.

    > [!NOTE]
    > Die Appliance zur einmaligen Ermittlung ist inzwischen veraltet, da diese Methode bei der Verfügbarkeit der Leistungsdatenpunkte von den Statistikeinstellungen von vCenter Server abhängig war und durchschnittliche Leistungsindikatoren erfasst hat, was zu unterdimensionierten virtuellen Computern für die Migration zu Azure führte.

    **Sofortige Ergebnisse:** Mit der Appliance für die kontinuierliche Ermittlung können Sie nach Abschluss der Ermittlung (dauert je nach Anzahl von virtuellen Computern einige Stunden) sofort Bewertungen erstellen. Da die Erfassung von Leistungsdaten bereits beim Start der Ermittlung beginnt, empfiehlt es sich, das Größenkriterium in der Bewertung auf *Wie lokal* festzulegen, um sofortige Ergebnisse zu erzielen. Bei leistungsbasierten Bewertungen empfiehlt es sich, nach dem Start der Ermittlung mindestens einen Tag zu warten, um zuverlässige Größenempfehlungen zu erhalten.

    Beachten Sie, dass die Appliance nur Leistungsdaten kontinuierlich erfasst. Sie erkennt keine Konfigurationsänderungen in der lokalen Umgebung (etwa hinzugefügte VMs, Löschvorgänge, hinzugefügte Datenträger etc.). Wenn sich die Konfiguration in der lokalen Umgebung ändert, können Sie wie folgt vorgehen, damit die Änderungen im Portal berücksichtigt werden:

    - Hinzugefügte Elemente (virtuelle Computer, Datenträger, Kerne usw.): Damit diese Änderungen im Azure-Portal berücksichtigt werden, können Sie die Ermittlung über die Appliance beenden und anschließend wieder starten. Dadurch wird sichergestellt, dass die Änderungen im Azure Migrate-Projekt aktualisiert werden.

    - Gelöschte virtuelle Computer: Das Löschen von virtuellen Computern wird aufgrund des Entwurfs der Appliance auch dann nicht berücksichtigt, wenn Sie die Ermittlung beenden und wieder starten. Das liegt daran, dass Daten nachfolgender Ermittlungen älteren Ermittlungen angefügt und nicht überschrieben werden. In diesem Fall können Sie die VM im Portal einfach ignorieren, indem Sie sie aus Ihrer Gruppe entfernen und die Bewertung neu berechnen.

3. Kopieren Sie unter **Projektanmeldeinformationen kopieren** die ID und den Schlüssel für das Projekt. Diese Angaben benötigen Sie beim Konfigurieren des Collectors.


#### <a name="verify-the-collector-appliance"></a>Überprüfen der Collectorappliance

Überprüfen Sie, ob die OVA-Datei sicher ist, bevor Sie sie bereitstellen:

1. Öffnen Sie auf dem Computer, auf den Sie die Datei heruntergeladen haben, ein Administratorbefehlsfenster.

2. Führen Sie den folgenden Befehl aus, um den Hash für die OVA-Datei zu generieren:

   ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```

   Beispielverwendung: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```

3. Stellen Sie sicher, dass der generierte Hash mit den folgenden Einstellungen übereinstimmt.

#### <a name="continuous-discovery"></a>Kontinuierliche Ermittlung

Für OVA-Version 1.0.10.4

**Algorithmus** | **Hashwert**
--- | ---
MD5 | 2ca5b1b93ee0675ca794dd3fd216e13d
SHA1 | 8c46a52b18d36e91daeae62f412f5cb2a8198ee5
SHA256 | 3b3dec0f995b3dd3c6ba218d436be003a687710abab9fcd17d4bdc90a11276be

#### <a name="one-time-discovery-deprecated-now"></a>Einmalige Ermittlung (jetzt veraltet)

Für OVA-Version 1.0.9.15 (veröffentlicht am 23.10.2018)

**Algorithmus** | **Hashwert**
--- | ---
MD5 | e9ef16b0c837638c506b5fc0ef75ebfa
SHA1 | 37b4b1e92b3c6ac2782ff5258450df6686c89864
SHA256 | 8a86fc17f69b69968eb20a5c4c288c194cdcffb4ee6568d85ae5ba96835559ba

Für OVA-Version 1.0.9.14 (veröffentlicht am 24.8.2018)

**Algorithmus** | **Hashwert**
--- | ---
MD5 | 6d8446c0eeba3de3ecc9bc3713f9c8bd
SHA1 | e9f5bdfdd1a746c11910ed917511b5d91b9f939f
SHA256 | 7f7636d0959379502dfbda19b8e3f47f3a4744ee9453fc9ce548e6682a66f13c

Für OVA-Version 1.0.9.12

**Algorithmus** | **Hashwert**
--- | ---
MD5 | d0363e5d1b377a8eb08843cf034ac28a
SHA1 | df4a0ada64bfa59c37acf521d15dcabe7f3f716b
SHA256 | f677b6c255e3d4d529315a31b5947edfe46f45e4eb4dbc8019d68d1d1b337c2e

Für OVA-Version 1.0.9.8

**Algorithmus** | **Hashwert**
--- | ---
MD5 | b5d9f0caf15ca357ac0563468c2e6251
SHA1 | d6179b5bfe84e123fabd37f8a1e4930839eeb0e5
SHA256 | 09c68b168719cb93bd439ea6a5fe21a3b01beec0e15b84204857061ca5b116ff

Für OVA-Version 1.0.9.7

**Algorithmus** | **Hashwert**
--- | ---
MD5 | d5b6a03701203ff556fa78694d6d7c35
SHA1 | f039feaa10dccd811c3d22d9a59fb83d0b01151e
SHA256 | e5e997c003e29036f62bf3fdce96acd4a271799211a84b34b35dfd290e9bea9c

### <a name="create-the-collector-vm"></a>Erstellen der Collector-VM

Importieren Sie die heruntergeladene Datei auf die vCenter Server-Instanz:

1. Klicken Sie in der vSphere-Clientkonsole auf **Datei** > **OVF-Vorlage bereitstellen**.

    ![Bereitstellen der OVF-Vorlage](./media/how-to-scale-assessment/vcenter-wizard.png)

2. Geben Sie im Assistenten zum Bereitstellen der OVF-Vorlage unter **Quelle** den Speicherort der OVA-Datei an.
3. Geben Sie unter **Name** und **Speicherort** einen Anzeigenamen für den virtuellen Collectorcomputer und das Inventarobjekt an, in dem der virtuelle Computer gehostet wird.
4. Geben Sie unter **Host/Cluster** den Host oder Cluster an, auf dem der virtuelle Collectorcomputer ausgeführt wird.
5. Geben Sie im Speicher das Speicherziel für den virtuellen Collectorcomputer an.
6. Geben Sie unter **Datenträgerformat** den Typ und die Größe des Datenträgers an.
7. Geben Sie unter **Netzwerkzuordnung** das Netzwerk an, mit dem der virtuelle Collectorcomputer eine Verbindung herstellt. Das Netzwerk benötigt Internetkonnektivität, um Metadaten an Azure zu senden.
8. Überprüfen und bestätigen Sie die Einstellungen, und klicken Sie auf **Fertig stellen**.

### <a name="identify-the-id-and-key-for-each-project"></a>Ermitteln von ID und Schlüssel für jedes Projekt

Wenn Sie über mehrere Projekte verfügen, müssen Sie die ID und den Schlüssel für jedes der Projekte ermitteln. Der Schlüssel wird benötigt, wenn Sie den Collector ausführen, um die virtuellen Computer zu ermitteln.

1. Klicken Sie im Projekt auf **Erste Schritte** > **Ermitteln und bewerten** > **Computer ermitteln**.
2. Kopieren Sie unter **Projektanmeldeinformationen kopieren** die ID und den Schlüssel für das Projekt.
    ![Projektanmeldeinformationen kopieren](./media/how-to-scale-assessment/copy-project-credentials.png)

### <a name="run-the-collector-to-discover-vms"></a>Ausführen des Collectors zum Ermitteln virtueller Computer

Für die einzelnen Ermittlungen müssen Sie jeweils den Collector ausführen, um virtuelle Computer im erforderlichen Bereich zu ermitteln. Führen Sie die Ermittlungen nacheinander durch. Parallele Ermittlungen werden nicht unterstützt, und jede Ermittlung muss einen anderen Bereich umfassen.

1.  Klicken Sie in der vSphere-Clientkonsole mit der rechten Maustaste auf den virtuellen Computer, und klicken Sie auf **Konsole öffnen**.
2.  Geben Sie für die Appliance die Einstellungen für Sprache, Zeitzone und Kennwort an.
3.  Klicken Sie auf dem Desktop auf die Verknüpfung **Collector ausführen**.
4.  Öffnen Sie im Azure Migrate-Collector **Erforderliche Komponenten einrichten**, und gehen Sie dann wie folgt vor:

    a. Akzeptieren Sie die Lizenzbedingungen, und lesen Sie die Drittanbieterinformationen.

    Der Collector überprüft, ob der virtuelle Computer über Internetzugriff verfügt.

    b. Wenn der virtuelle Computer über einen Proxy auf das Internet zugreift, klicken Sie auf **Proxyeinstellungen**, und geben Sie die Proxyadresse und den Lauschport an. Geben Sie die Anmeldeinformationen an, wenn der Proxy eine Authentifizierung erfordert.

    Der Collector überprüft, ob der Collector-Dienst ausgeführt wird. Der Dienst wird standardmäßig auf dem virtuellen Collectorcomputer installiert.

    c. Laden Sie die VMware PowerCLI herunter, und installieren Sie sie.

5.  Nehmen Sie in **vCenter Server-Details angeben** die folgenden Einstellungen vor:
    - Geben Sie den Namen (FQDN) oder die IP-Adresse der vCenter Server-Instanz an.
    - Geben Sie unter **Benutzername** und **Kennwort** die Anmeldeinformationen für das schreibgeschützte Konto an, mit dem der Collector VMs in vCenter Server ermittelt.
    - Wählen Sie unter **Bereich auswählen** einen Bereich für die Ermittlung virtueller Computer aus. Der Collector kann nur virtuelle Computer innerhalb des angegebenen Bereichs ermitteln. Der Bereich kann auf einen bestimmten Ordner, ein Rechenzentrum oder einen Cluster festgelegt werden. Der Bereich sollte nicht mehr als 1,000 virtuelle Computer umfassen.

6.  Geben Sie unter **Migrationsprojekt angeben** die ID und den Schlüssel für das Projekt an. Wenn Sie diese Angaben nicht kopiert haben, öffnen Sie das Azure-Portal über die Collector-VM. Klicken Sie auf der Seite **Übersicht** des Projekts auf **Computer ermitteln**, und kopieren Sie die Werte.  
7.  Überwachen Sie in **Sammlungsfortschritt anzeigen** den Ermittlungsprozess, und vergewissern Sie sich, dass sich die von den VMs erfassten Metadaten innerhalb des zulässigen Bereichs befinden. Der Collector gibt eine ungefähre Ermittlungszeit an.

#### <a name="verify-vms-in-the-portal"></a>Überprüfen virtueller Computer im Portal

Der Collector erstellt kontinuierlich Profile für die lokale Umgebung und sendet stündlich Leistungsdaten. Sie können die Computer im Portal eine Stunde nach Start der Ermittlung überprüfen. Sie sollten mindestens einen Tag warten, bevor Sie leistungsbasierte Bewertungen für die virtuellen Computer erstellen.

1. Klicken Sie im Migrationsprojekt auf **Verwalten** > **Computer**.
2. Überprüfen Sie, ob die zu ermittelnden virtuellen Computer im Portal angezeigt werden.

### <a name="data-collected-from-on-premises-environment"></a>Aus der lokalen Umgebung erfasste Daten

Die Collectorappliance ermittelt die folgenden Konfigurationsdaten zu den ausgewählten virtuellen Computern.

1. Anzeigename des virtuellen Computers (in vCenter)
2. Inventurpfad des virtuellen Computers (Host/Ordner in vCenter)
3. IP-Adresse
4. MAC-Adresse
5. Betriebssystem
5. Anzahl der Kerne, Datenträger, NICs
6. Arbeitsspeichergröße, Datenträgergrößen
7. Und Leistungsindikatoren des virtuellen Computers, Datenträgers und Netzwerks wie in der folgenden Tabelle aufgeführt.

Die Collectorappliance sammelt die folgenden Leistungsindikatoren für jede VM vom ESXi-Host in einem Intervall von 20 Sekunden. Diese Leistungsindikatoren sind vCenter-Leistungsindikatoren und obwohl die Terminologie von Durchschnitt spricht, handelt es sich bei den 20-Sekunden-Stichproben um Echtzeit-Leistungsindikatoren. Die Appliance führt dann ein Rollup der 20-Sekunden-Stichprobe durch, um einen einzelnen Datenpunkt für je 15 Minuten in Form des Spitzenwerts der 20-Sekunden-Stichprobe zu erstellen, und sendet diesen an Azure. Die Leistungsdaten für die virtuellen Computer stehen im Portal zwei Stunden nach dem Starten der Ermittlung zur Verfügung. Bei leistungsbasierten Bewertungen wird dringend empfohlen, mindestens einen Tag mit deren Erstellung zu warten, um korrekte Größenempfehlungen zu erhalten. Wenn Sie sofortige Ergebnisse wünschen, können Sie Bewertungen erstellen, bei denen das Größenkriterium auf *Wie lokal* festgelegt ist, wodurch die Leistungsdaten für die korrekte Größenanpassung nicht berücksichtigt werden.

**Leistungsindikator** |  **Auswirkung auf die Bewertung**
--- | ---
cpu.usage.average | Empfohlene VM-Größe und -Kosten  
mem.usage.average | Empfohlene VM-Größe und -Kosten  
virtualDisk.read.average | Berechnet die Datenträgergröße, Speicherkosten und die VM-Größe
virtualDisk.write.average | Berechnet die Datenträgergröße, Speicherkosten und die VM-Größe
virtualDisk.numberReadAveraged.average | Berechnet die Datenträgergröße, Speicherkosten und die VM-Größe
virtualDisk.numberWriteAveraged.average | Berechnet die Datenträgergröße, Speicherkosten und die VM-Größe
net.received.average | Berechnet die VM-Größe                          
net.transmitted.average | Berechnet die VM-Größe     

> [!WARNING]
> Die Methode der einmaligen Ermittlung, die bei der Erfassung von Leistungsdaten die Statistikeinstellungen von vCenter Server verwendete, ist jetzt veraltet.

## <a name="next-steps"></a>Nächste Schritte

- Informieren Sie sich über das [Erstellen einer Gruppe](how-to-create-a-group.md) für die Bewertung.
- [Weitere Informationen](concepts-assessment-calculation.md) zur Berechnung von Bewertungen
