---
title: Einrichten von Project Lead The Way-Labs mit Azure Lab Services
description: Hier erfahren Sie, wie Sie Labs einrichten, um Project Lead The Way-Kurse zu unterrichten.
ms.topic: article
ms.date: 10/28/2020
ms.openlocfilehash: ca4fdae2372895c17c4a98dd3959935108846744
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95024618"
---
# <a name="set-up-labs-for-project-lead-the-way-classes"></a>Einrichten von Labs für Project Lead The Way-Kurse

[Project Lead The Way (PLTW)](https://www.pltw.org/) ist eine gemeinnützige Organisation, die in den USA einen PreK&ndash;12-Lehrplan für Informatik, Engineering und biomedizinische Wissenschaft anbietet.  In jedem PLTW-Kurs verwenden die Kursteilnehmer eine Reihe von Softwareanwendungen als Teil ihrer praktischen Lernerfahrung.  Für viele Softwareanwendungen ist entweder eine schnelle CPU oder in einigen Fällen eine GPU erforderlich.  In diesem Artikel erfahren Sie, wie Sie Labs für die folgenden PLTW-Kurse einrichten, die in der Regel für Kursteilnehmer der Klassen 9 bis 12 angeboten werden:

- **Introduction to Engineering Design (Einführung in technische Konstruktion)**

    Die Kursteilnehmer erhalten eine Einführung in den Prozess der technischen Konstruktion, wozu auch die Verwendung der [CAD-Software (Computer-Aided Design) Autodesk Inventor](https://www.autodesk.com/products/inventor/new-features) für die 3D-Modellierung gehört.

- **Principles of Engineering (Grundlagen der Konstruktion)**
    
    Die Kursteilnehmer werden mit Konstruktionsmechanismen, Strukturfestigkeit, Materialstärke und Automatisierung vertraut gemacht.  In diesem Kurs wird Software wie [MD Solids](https://s3.amazonaws.com/support-downloads.pltw.org/2020-21/MD+Solids/MD+Solids+Software+Installation+Guide.pdf), [West Point Bridge Designer](https://s3.amazonaws.com/support-downloads.pltw.org/2020-21/West+Point+Bridge+Builder/Installation+Guide+for+West+Point+Bridge+Designer.pdf) und die [America‘s Army-Simulation](https://s3.amazonaws.com/support-downloads.pltw.org/2020-21/America's+Army/Installation+Guide+for+Americas+Army+Simulation+17-18.pdf) verwendet.

- **Civil Engineering and Architecture(Bauwesen und Architektur)**

    Die Kursteilnehmer werden mithilfe der Architekturdesignsoftware [Autodesk Revit](https://www.autodesk.com/products/revit/overview) für 3D-Gebäudeinformationsmodellierung (Building Information Modeling, BIM) in der Bau- und Standortplanung sowie -entwicklung unterrichtet.

- **Computer Integrated Manufacturing (CIM)**

    Die Kursteilnehmer untersuchen moderne Fertigungsprozesse, die Robotik und Automatisierung einbeziehen.   In diesem Kurs verwenden die Kursteilnehmer die [Autodesk Inventor CAD](https://www.autodesk.com/products/inventor/new-features)- und [Autodesk Inventor CAM](https://www.autodesk.com/products/inventor-cam/overview)-Software. 

- **Digital Electronics (Digitale Elektronik)**

    Die Kursteilnehmer untersuchen mit [Multisim von National Instruments](https://www.ni.com/en-us/shop/electronic-test-instrumentation/application-software-for-electronic-test-and-instrumentation-category/what-is-multisim.html), einer Software für die Simulation und den Schaltungsentwurf, elektronische Logikschaltungen und -bausteine.

- **Engineering Design and Development (Konstruktionsdesign und -entwicklung)**

    Die Kursteilnehmer wirken durch Kombination von Forschung, Entwurf und Tests an der Entwicklung einer End-to-End-Lösung mit, die sie einem Gremium von Ingenieuren vorstellen.  In diesem Kurs verwenden die Kursteilnehmer die [CAD-Software Autodesk Inventor](https://www.autodesk.com/products/inventor/new-features).

- **Computer Science Essentials (Grundlagen der Informatik)**

    Die Kursteilnehmer werden in Informatikkonzepte und -tools eingeführt.  Sie beginnen mit blockbasierter Programmierung und fahren dann mit textbasierter Codierung mithilfe von Codierungsumgebungen wie [VEXcode V5 Blocks](https://s3.amazonaws.com/support-downloads.pltw.org/2020-21/VEXcode+V5+Blocks/VexCode+V5+Blocks+Installation+Guide.pdf) fort.

- **Computer Science Principles (Prinzipien der Informatik)**
    
    Die Kursteilnehmer erweitern ihre Programmierkenntnisse mit [Python](https://www.python.org/) unter Verwendung der [Visual Studio Code-Entwicklungsumgebung von Microsoft](https://code.visualstudio.com/). 

- **Computer Science A (Informatik A)**

    In diesem Kurs erweitern die Kursteilnehmer ihre Programmierkenntnisse, indem sie die Entwicklung mobiler Apps erlernen.  Sie verwenden die [Visual Studio Code-Entwicklungsumgebung von Microsoft](https://code.visualstudio.com/), um sich mit [Java](https://www.java.com/) vertraut zu machen.  Außerdem verwenden die Kursteilnehmer einen Emulator, der Ihnen das Ausführen und Testen Ihres Codes für mobile Apps ermöglicht.  Informationen zum Einrichten eines Emulators in Azure Lab Services erhalten Sie vom [Azure Lab Services-Support](mailto:AzLabsCOVIDSupport@microsoft.com).

Eine vollständige Liste der Software für die einzelnen Kurse finden Sie auf der [PLTW-Website](https://www.pltw.org/pltw-software).

## <a name="lab-configuration"></a>Labkonfiguration

Zum Einrichten von Labs für PLTW benötigen Sie zunächst ein Azure-Abonnement und ein Lab-Konto. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen. 

Sobald Sie ein Azure-Abonnement haben, können Sie ein neues Lab-Konto in Azure Lab Services erstellen. Weitere Informationen zum Erstellen eines neuen Lab-Kontos finden Sie unter [Einrichten eines Lab-Kontos](./tutorial-setup-lab-account.md). Sie können auch ein vorhandenes Lab-Konto verwenden.

Nachdem Sie ein Lab-Konto eingerichtet haben, sollten Sie für jede PLTW-Unterrichtssitzung, die Ihre Bildungseinrichtung anbietet, ein separates Lab erstellen.  Außerdem wird empfohlen, separate Images für jeden Typ von PLTW-Kurs zu erstellen.  Weitere Informationen zum Strukturieren Ihrer Labs und Images finden Sie im Blogbeitrag zur [Umstellung von einem physischen Lab auf Azure Lab Services](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931).

### <a name="lab-account-settings"></a>Lab-Kontoeinstellungen

Aktivieren Sie die Einstellungen Ihres Lab-Kontos wie in der folgenden Tabelle beschrieben. Weitere Informationen zum Aktivieren von Azure Marketplace-Images finden Sie unter [Angeben der für Lab-Ersteller verfügbaren Azure Marketplace-Images](./specify-marketplace-images.md).

| Lab-Kontoeinstellungen | Instructions |
| -------------------- | ----- |
| Marketplace-Image | Aktivieren Sie das Windows 10 Pro-Image zur Verwendung in Ihrem Lab-Konto. |

<br>

### <a name="lab-settings"></a>Lab-Einstellungen
Die für PLTW-Kurse empfohlene VM-Größe hängt von der Art der Workloads ab, die Ihre Kursteilnehmer im Kurs verwenden.  Für die oben genannten Kurse empfehlen wir die Verwendung der VM-Größen „Kleine GPU (Visualisierung)“ und „Groß“. Halten Sie sich beim Einrichten der Labs für Ihre PLTW-Kurse an die Empfehlungen in der folgenden Tabelle:

| Lab-Einrichtung | Wert und Beschreibung | Kursempfehlung |
| ------------ | ------------------ | --- |
| VM-Größe | **Kleine GPU (Visualisierung)**<br>Eignet sich am besten für Remotevisualisierung, Streaming, Gaming und die Codierung mit Frameworks wie beispielsweise OpenGL und DirectX. | Es wird empfohlen, diese Größe für die folgenden PLTW-Kurse zu verwenden: Civil Engineering and Architecture (Bauwesen und Architektur), Digital Electronics (Digitale Elektronik), Computer Integrated Manufacturing (CIM) und Engineering Design and Development (Konstruktionsdesign und -entwicklung).
| VM-Größe | **Groß**<br>Eignet sich am besten für Anwendungen, die schnellere CPUs, eine höhere lokale Datenträgerleistung sowie große Datenbanken und Caches erfordern. | Es wird empfohlen, diese Größe für die folgenden PLTW-Kurse zu verwenden: Introduction to Engineering Design (Einführung in technische Konstruktion), Principles of Engineering (Grundlagen der Konstruktion), Computer Science Essentials (Grundlagen der Informatik), Computer Science Principles (Prinzipien der Informatik) und Computer Science A (Informatik A). |

<br>

### <a name="license-server"></a>Lizenzserver
Der Großteil der Software, die in den oben genannten PLTW-Kursen verwendet wird, erfordert *keinen* Zugriff auf einen Lizenzserver.  Sie müssen jedoch auf einen Lizenzserver zugreifen, wenn Sie beabsichtigen, das Netzwerklizenzierungsmodell von Autodesk für die folgende Software zu verwenden:
-   Revit
-   Inventor
-   Inventor CAM

Für die Verwendung der Netzwerklizenzierung mit Software von Autodesk sind [detaillierte Schritte von PLTW](https://www.pltw.org/pltw-software) zur Installation von Autodesk Network License Manager auf Ihrem Lizenzserver verfügbar.  Dieser Lizenzserver wird in der Regel in Ihrem lokalen Netzwerk platziert oder auf einer Azure-VM in einem virtuellen Azure-Netzwerk gehostet.

Nach dem Einrichten des Lizenzservers müssen Sie ein [Peering des virtuellen Netzwerks](./how-to-connect-peer-virtual-network.md) mit Ihrem [Lab-Konto](./tutorial-setup-lab-account.md) erstellen. Das Netzwerkpeering muss *vor* dem Erstellen des Labs durchgeführt werden, damit Ihre Lab-VMs auf den Lizenzserver zugreifen können und umgekehrt.

Von Autodesk generierte Lizenzdateien betten die MAC-Adresse des Lizenzservers ein.  Falls Sie Ihren Lizenzserver auf einer Azure-VM hosten möchten, müssen Sie sicherstellen, dass sich die MAC-Adresse des Lizenzservers nicht ändert. Wenn sich die MAC-Adresse ändert, müssen Sie die Lizenzdateien erneut generieren. Gehen Sie wie folgt vor, um zu verhindern, dass sich die MAC-Adresse ändert:

- Legen Sie für die Azure-VM, die Ihren Lizenzserver hostet, eine [statische private IP- und MAC-Adresse](./how-to-create-a-lab-with-shared-resource.md#static-private-ip-and-mac-address) fest.
- Richten Sie sowohl Ihr Lab-Konto als auch das virtuelle Netzwerk des Lizenzservers in einer Region bzw. an einem Standort mit ausreichender VM-Kapazität ein, damit Sie diese Ressourcen nicht zu einem späteren Zeitpunkt in eine neue Region oder an einen neuen Standort verschieben müssen.

Weitere Informationen finden Sie unter [Einrichten eines Lizenzservers als freigegebene Ressource](./how-to-create-a-lab-with-shared-resource.md).

### <a name="template-machine"></a>Vorlagencomputer
Einige der Installationsdateien, die Sie für PLTW benötigen, sind groß. Wenn Sie die Dateien auf eine Vorlagen-VM für Labs herunterladen, kann das Kopieren sehr lange dauern.

Anstatt Installationsdateien auf den Vorlagencomputer herunterzuladen und dort alles zu installieren, empfehlen wir, die PLTW-Images in Ihrer physischen Umgebung zu erstellen.  Anschließend können Sie die benutzerdefinierten Images in Shared Image Gallery importieren, sodass Sie sie zum Erstellen Ihrer Labs verwenden können.  Weitere Informationen finden Sie unter [Hochladen eines benutzerdefinierten Images in Shared Image Gallery](./upload-custom-image-shared-image-gallery.md).

Zum Einrichten eines Labs führen Sie unter Berücksichtigung dieser Empfehlung die folgenden Hauptaufgaben aus:

1. Erstellen Sie in ihrer physischen Umgebung das Image für den Kurs.

    a.  Befolgen Sie die detaillierten Schritte von PLTW zum Herunterladen der Installationsdateien und Installieren der erforderlichen Software.

    > [!NOTE]    
    > Der Computer, auf dem Sie die Autodesk-Anwendungen installieren, muss bei der Installation mit dem Lizenzserver kommunizieren können. Der Installations-Assistent von Autodesk fordert Sie auf, den Computernamen des Computers anzugeben, auf dem der Lizenzserver gehostet wird.  Wenn Sie Ihren Lizenzserver auf einer Azure-VM hosten, müssen Sie möglicherweise mit der Installation von Autodesk auf der Vorlagen-VM des Labs warten, damit der Installations-Assistent auf Ihren Lizenzserver zugreifen kann.

    b.  [Installieren und konfigurieren Sie OneDrive](./how-to-prepare-windows-template.md#install-and-configure-onedrive) oder andere Sicherungsoptionen, die Ihre Bildungseinrichtung verwendet.
    
    c.  [Installieren und konfigurieren Sie Windows-Updates](./how-to-prepare-windows-template.md#install-and-configure-updates).

1.  Laden Sie das benutzerdefinierte Image in die [Shared Image Gallery-Instanz hoch, die an Ihr Lab-Konto angefügt ist](./how-to-attach-detach-shared-image-gallery.md).

1.  Erstellen Sie ein Lab, und wählen Sie dann das benutzerdefinierte Image aus, das Sie im vorherigen Schritt hochgeladen haben.

1.  Starten Sie nach dem Erstellen des Labs die Vorlagen-VM, und stellen Sie eine Verbindung mit dieser her, um zu überprüfen, ob das Image erwartungsgemäß funktioniert.

1.  Veröffentlichen Sie zum Schluss die Vorlagen-VM, um die VMs der Kursteilnehmer zu erstellen.

## <a name="student-devices"></a>Kursteilnehmergeräte
Kursteilnehmer können mit Windows- und Mac-Computern sowie Chromebooks eine Verbindung mit ihren Lab-VMs herstellen. Anweisungen dazu finden Sie unter:

- [Herstellen einer Verbindung aus Windows](./how-to-use-classroom-lab.md#connect-to-the-vm)
- [Verbindungsherstellung über Mac](./connect-virtual-machine-mac-remote-desktop.md)
- [Verbindungsherstellung über Chromebook](./connect-virtual-machine-chromebook-remote-desktop.md)

## <a name="cost"></a>Kosten
Sehen wir uns eine Beispielkostenschätzung für die oben genannten PLTW-Kurse an.  Die Kosten für die Ausführung eines Lizenzservers oder die Verwendung von Shared Image Gallery sind nicht in dieser Schätzung enthalten. Angenommen, Sie haben einen Kurs mit 25 Kursteilnehmern, für die jeweils 20 Stunden Kurszeit geplant sind.  Zudem erhält jeder Kursteilnehmer ein Kontingent von 10 zusätzlichen Stunden für Hausaufgaben und Arbeitsaufträge außerhalb der geplanten Kurszeit.  In diesem Fall lautet die Kostenschätzung wie folgt:

- **Große VM**

    25 Kursteilnehmer &times; (20 geplante Stunden + 10 Kontingentstunden) &times; 70 Lab-Einheiten &times; 0,01 USD pro Stunde = 525,00 USD

- **Kleine GPU (Visualisierung)**

    25 Kursteilnehmer &times; (20 geplante Stunden + 10 Kontingentstunden) &times; 160 Lab-Einheiten &times; 0,01 USD pro Stunde = 1.200,00 USD

> [!IMPORTANT] 
> Diese Kostenschätzung dient ausschließlich zu Beispielzwecken.  Aktuelle Informationen zu Preisen finden Sie unter [Azure Lab Services – Preise](https://azure.microsoft.com/pricing/details/lab-services/).

> [!NOTE] 
> Viele der PLTW-Kurse verwenden Anwendungen, auf die über einen Browser zugegriffen wird, z. B. MIT App Inventor.  Diese browserbasierten Anwendungen erfordern keine schnelle CPU oder GPU und können auf jedem Gerät aufgerufen werden, das über eine Internetverbindung verfügt.  Wenn Kursteilnehmer solche Anwendungen verwenden, empfehlen wir, dass sie anstelle des Browsers auf der Lab-VM den Browser auf ihrem physischen Gerät verwenden. Kursteilnehmer können zur Senkung der Kosten beitragen, indem sie ihre Lab-VMs nur für Anwendungen nutzen, die eine schnelle CPU oder GPU erfordern.

## <a name="next-steps"></a>Nächste Schritte

Lesen Sie folgende Artikel zum Einrichten des Labs:

- [Hinzufügen von Benutzern](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Festlegen von Kontingenten](how-to-configure-student-usage.md#set-quotas-for-users)
- [Zeitplan festlegen](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [Registrierungslinks per E-Mail an Kursteilnehmer senden](how-to-configure-student-usage.md#send-invitations-to-users) 
