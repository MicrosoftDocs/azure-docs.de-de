---
title: Lokale Profilerstellung eines Clouddiensts im Serveremulator | Microsoft-Dokumentation
services: cloud-services
description: Untersuchen von Leistungsproblemen in Clouddiensten mit der Visual Studio-Profilerstellung
documentationcenter: ''
author: mikejo
manager: jillfra
editor: ''
tags: ''
ms.assetid: 25e40bf3-eea0-4b0b-9f4a-91ffe797f6c3
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/18/2016
ms.author: mikejo
ms.openlocfilehash: 6b5707405879c462a1d919e04730d368332ba68c
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2020
ms.locfileid: "92077154"
---
# <a name="testing-the-performance-of-a-cloud-service-locally-in-the-azure-compute-emulator-using-the-visual-studio-profiler"></a>Lokales Testen der Leistung eines Clouddiensts im Azure-Serveremulator mithilfe des Visual Studio-Profiler
Für das Testen der Leistung von Clouddiensten stehen zahlreiche Tools und Techniken zur Verfügung.
Wenn Sie einen Clouddienst in Azure veröffentlichen, können Sie Visual Studio zum Erfassen von Profildaten und zum lokalen Analysieren dieser Daten verwenden, wie in [Erstellen eines Profils für eine Azure-Anwendung][1] beschrieben.
Sie können zudem Diagnosen verwenden, um unterschiedliche Leistungsindikatoren nachzuverfolgen, wie in [Verwenden von Leistungsindikatoren in Azure][2] beschrieben.
Sie können Ihre Anwendung auch lokal im Serveremulator profilieren, bevor Sie sie in der Cloud bereitstellen.

Dieser Artikel behandelt die CPU-Sampling-Methode der Profilerstellung, welche lokal im Emulator erfolgen kann. CPU-Sampling ist eine wenig intrusive Profilerstellungsmethode. Zu einem festgelegten Sampling-Intervall erstellt der Profiler eine Momentaufnahme der Aufrufliste. Die Daten werden über einen bestimmte Zeitraum hinweg erfasst und in einem Bericht angezeigt. Diese Profilerstellungsmethode gibt vor allem an, wo in einer rechenintensiven Anwendung die meiste CPU-Arbeit erfolgt.  Dadurch können Sie sich auf den "Hot Path" konzentrieren, wo Ihre Anwendung die meiste Zeit verbringt.

## <a name="1-configure-visual-studio-for-profiling"></a>1: Konfigurieren von Visual Studio für die Profilerstellung
Es gibt wenige Visual Studio-Konfigurationsoptionen, die für die Profilerstellung hilfreich sind. Um Profilberichte sinnvoll zu nutzen, benötigen Sie sowohl Symbole (.pdb-Dateien) für die Anwendung als auch Symbole für Systembibliotheken. Sie möchten möglicherweise überprüfen, dass Sie auf die verfügbaren Symbolserver verweisen. Wählen Sie dazu im Menü **Extras** in Visual Studio **Optionen**, und wählen Sie dann **Debugging** und anschließend **Symbole**. Überprüfen Sie, dass Microsoft Symbol Servers unter **Speicherorte für Symboldateien (.pdb)** aufgelistet ist.  Sie können auch unter https://referencesource.microsoft.com/symbols nachschauen, wo Sie ggf. weitere Symboldateien finden.

![Symboloptionen][4]

Sie können die Berichte, welche der Profiler erstellt, vereinfachen, indem Sie "Nur eigenen Code" einstellen. Wenn Sie "Nur eigenen Code" aktiviert haben, sind die Funktionsanruflisten vereinfacht, und Aufrufe innerhalb der Bibliotheken und .NET Framework werden nicht in den Berichten angezeigt. Wählen Sie **Optionen** im Menü **Extras**. Erweitern Sie dann den Knoten **Leistungstools**, und wählen Sie **Allgemein**. Aktivieren Sie dann das Kontrollkästchen für **"Nur eigenen Code" für Profilerberichte aktivieren**.

![Optionen für „Nur mein Code“][17]

Sie können diese Anweisungen mit einem vorhandenen Projekt oder einem neuen Projekt verwenden.  Wenn Sie ein neues Projekt erstellen, um die unten beschriebenen Techniken auszuprobieren, wählen Sie ein C#-**Azure Cloud Services**-Projekt, und wählen Sie eine **Webrolle** und eine **Workerrolle** aus.

![Rollen im Azure-Clouddienstprojekt][5]

Fügen Sie zum Beispiel Code zu Ihrem Projekt hinzu, der viel Zeit in Anspruch nimmt und zu offenslichtlichen Leistungsproblemen führt. Fügen Sie zum Beispiel folgenden Code zu einem Workerrollenprojekt hinzu:

```csharp
public class Concatenator
{
    public static string Concatenate(int number)
    {
        int count;
        string s = "";
        for (count = 0; count < number; count++)
        {
            s += "\n" + count.ToString();
        }
        return s;
    }
}
```

Rufen Sie diesen Code von der RunAsync-Methode in der RoleEntryPoint-abgeleiteten Klasse der Workerrolle ab. (Ignorieren Sie die Warnung über die gleichzeitig ausgeführte Methode.)

```csharp
private async Task RunAsync(CancellationToken cancellationToken)
{
    // TODO: Replace the following with your own logic.
    while (!cancellationToken.IsCancellationRequested)
    {
        Trace.TraceInformation("Working");
        Concatenator.Concatenate(10000);
    }
}
```

Erstellen Sie den Clouddienst und führen Sie ihn lokal ohne Debuggen (Strg+F5) aus, wobei die Lösungskonfiguration auf **Release** festgelegt ist. Dadurch wird sichergestellt, dass alle Dateien und Ordner für das lokale Ausführen der Anwendung erstellt wurden und der Emulator gestartet wurde. Starten Sie die Serveremulator-Benutzeroberfläche über die Taskleiste, um zu überprüfen, ob die Workerrolle aktiv ist.

## <a name="2-attach-to-a-process"></a>2: Anfügen an einen Prozess
Anstatt die Profilerstellung der Anwendung über die Visual Studio 2010-IDE-Schnittstelle zu starten, müssen Sie den Profiler an einen laufenden Prozess anfügen. 

Um den Profiler an einen Prozess anzufügen, wählen Sie im Menü **Analysieren** die Option **Profiler** und **Anfügen/Trennen**.

![Option zum Hinzufügen eines Profils][6]

Suchen Sie für eine Workerrolle den Prozess "WaWorkerHost.exe".

![WaWorkerHost-Prozess][7]

Wenn sich der Projektordner auf einem Netzwerklaufwerk befindet, fragt Sie der Profiler nach einem anderen Speicherort für die Speicherung der Profilberichte.

 Sie können auch eine Webrolle anfügen, indem Sie diese zu "WaIISHost.exe" hinzufügen.
Wenn es mehrere Workerrollenprozesse in Ihrer Anwendung gibt, müssen Sie die "processID" verwenden, um sie zu unterscheiden. Sie können die "processID" programmgesteuert abfragen, indem Sie auf das Prozessobjekt zugreifen. Wenn Sie diesen Code zum Beispiel zur Ausführungsmethode der RoleEntryPoint-abgeleiteten Klasse in einer Rolle hinzufügen, können Sie sich das Protokoll in der Serveremulator-Benutzeroberfläche ansehen, um herauszufinden, mit welchem Prozess eine Verbindung hergestellt werden muss.

```csharp
var process = System.Diagnostics.Process.GetCurrentProcess();
var message = String.Format("Process ID: {0}", process.Id);
Trace.WriteLine(message, "Information");
```

Starten Sie die Serveremulator-Benutzeroberfläche, um das Protokoll anzuzeigen.

![Serveremulator-Benutzeroberfläche starten][8]

Öffnen Sie das Konsolenfenster des Workerrollenprotokolls in der Serveremulator-Benutzeroberfläche, indem Sie auf die Titelleiste des Konsolenfensters klicken. Sie sehen die Prozess-ID im Protokoll.

![Prozess-ID anzeigen][9]

Führen Sie (falls erforderlich) nach dem Anfügen die Schritte in der Benutzeroberfläche der Anwendung aus, um das Szenario zu reproduzieren.

Wenn Sie die Profilerstellung beenden möchten, wählen Sie den Link **Beenden Sie die Profilerstellung** aus.

![Option zum Beenden der Profilerstellung][10]

## <a name="3-view-performance-reports"></a>3: Anzeigen der Leistungsberichte
Der Leistungsbericht für Ihre Anwendung wird angezeigt.

Zu diesem Zeitpunkt stoppt der Profiler die Ausführung, speichert die Daten in einer .vsp-Datei, und zeigt einen Bericht an, der eine Analyse dieser Daten enthält.

![Profilerbericht][11]

Wenn Sie "String.wstrcpy" im Hot Path sehen, klicken Sie auf "Nur eigenen Code", um die Ansicht so zu ändern, dass nur Benutzercode angezeigt wird.  Wenn Sie "String.Concat" sehen, sollten Sie auf "Gesamten Code anzeigen" drücken.

Sie werden sehen, dass die Verkettungsmethode und "String.Concat" einen großen Teil der Ausführungszeit in Anspruch nimmt.

![Analyse des Berichts][12]

Wenn Sie den Zeichenkettenverkettungscode in diesem Artikel hinzugefügt haben, sollten Sie dafür eine Warnung in der Aufgabenliste sehen. Sie sehen möglicherweise auch eine Warnung bezüglich einer sehr großen Menge an Garbage Collection, was an der Anzahl der erstellten und entsorgten Zeichenketten liegt.

![Leistungswarnungen][14]

## <a name="4-make-changes-and-compare-performance"></a>4: Vornehmen von Änderungen und Vergleichen der Leistung
Sie können auch die Leistung vor und nach einer Codeänderung vergleichen.  Beenden Sie den laufenden Prozess, und bearbeiten Sie den Code, um den Zeichenkettenverkettungsvorgang durch die Verwendung von StringBuilder zu ersetzen:

```csharp
public static string Concatenate(int number)
{
    int count;
    System.Text.StringBuilder builder = new System.Text.StringBuilder("");
    for (count = 0; count < number; count++)
    {
        builder.Append("\n" + count.ToString());
    }
    return builder.ToString();
}
```

Führen Sie eine andere Leistungsausführung durch, und vergleichen Sie dann die Leistung. Im Leistungs-Explorer können Sie, wenn die Ausführungen sich in derselben Sitzung befinden, nur beide Berichte auswählen, das Kurzwahlmenü öffnen und **Leistungsberichte vergleichen** auswählen. Wenn Sie dies mit einer Ausführung in einer anderen Leistungssitzung vergleichen möchten, öffnen Sie das Menü **Analysieren**, und wählen Sie **Leistungsberichte vergleichen**. Geben Sie beide Dateien im Dialogfeld ein, das angezeigt wird.

![Option „Leistungsberichte vergleichen“][15]

Der Bericht zeigt die Unterschiede zwischen den beiden Ausführungen an.

![Vergleichsbericht][16]

Glückwunsch! Sie haben die ersten Schritte mit dem Profiler geschafft.

## <a name="troubleshooting"></a>Problembehandlung
* Stellen Sie sicher, dass Sie eine Releaseversion profilieren, und starten Sie ohne Debugging.
* Führen Sie den Leistungsassistenten aus, wenn die Anfügen/Trennen-Option nicht im Profilermenü aktiviert ist.
* Verwenden Sie die Serveremulator-Benutzeroberfläche, um den Status der Anwendung anzuzeigen. 
* Wenn Sie Probleme beim Starten der Anwendungen im Emulator oder beim Anfügen an den Profiler haben, sollten Sie den Serveremulator herunterfahren und neu starten. Wenn das Problem dadurch nicht behoben wird, sollten Sie einen Neustart versuchen. Das Problem kann auftreten, wenn Sie den Serveremulator verwenden, um laufende Bereitstellungen anzuhalten und zu entfernen.
* Wenn Sie Profilbefehle aus der Befehlszeile verwendet haben, insbesondere in den globalen Einstellungen, sollten Sie sicherstellen, dass "VSPerfClrEnv /globaloff" aufgerufen und "VsPerfMon.exe" beendet wurde.
* Wenn beim Sampling die Meldung „PRF0025: Es wurden keine Daten gesammelt“ angezeigt wird, müssen Sie sicherstellen, dass der Prozess, den Sie als Anhangsanker verwendet haben, über CPU-Aktivität verfügt. Anwendungen, die keine Berechnungen vornehmen, produzieren möglicherweise keine Samplingdaten.  Es ist auch möglich, dass der Vorgang beendet wurde, bevor das Sampling erfolgte. Überprüfen Sie, dass die Ausführungsmethode für eine Rolle, für die Sie ein Profil erstellen, nicht bendet wurde.

## <a name="next-steps"></a>Nächste Schritte
Die Instrumentierung von Azure-Binärdateien im Emulator wird im Visual Studio-Profiler nicht unterstützt, aber wenn Sie die Speicherzuteilung testen möchten, können Sie diese Option bei der Profilerstellung auswählen. Sie können zudem die Parallelitätsprofilerstellung auswählen, mit der Sie bestimmen können, ob Threads beim Kampf um Sperren Zeit verschwenden, oder die Profilerstellung für Ebeneninteraktion, mit der Sie Leistungsprobleme bei der Interaktion zwischen den Ebenen einer Anwendung aufspüren können, meistens zwischen der Datenebene und einer Workerrolle.  Sie können die Datenbankabfragen, welche die Anwendung generiert, anzeigen sowie die Profilerstellungsdaten zur Verbesserung der Datenbankverwendung nutzen. Informationen zur Profilerstellung für Ebeneninteraktionen finden Sie im Blogbeitrag [Walkthrough: Using the Tier Interaction Profiler in Visual Studio Team System 2010][3] (Exemplarische Vorgehensweise: Verwenden der Profilerstellung für Ebeneninteraktionen in Visual Studio Team System 2010).

[1]: ../azure-monitor/app/profiler.md
[2]: /previous-versions/azure/hh411542(v=azure.100)
[3]: /archive/blogs/habibh/walkthrough-using-the-tier-interaction-profiler-in-visual-studio-team-system-2010
[4]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally09.png
[5]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally10.png
[6]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally02.png
[7]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally05.png
[8]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally010.png
[9]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally07.png
[10]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally06.png
[11]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally03.png
[12]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally011.png
[14]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally04.png 
[15]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally013.png
[16]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally012.png
[17]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally08.png