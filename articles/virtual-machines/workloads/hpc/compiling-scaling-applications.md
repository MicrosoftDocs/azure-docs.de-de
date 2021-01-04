---
title: Skalieren von HPC-Anwendungen – Azure Virtual Machines | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie HPC-Anwendungen auf virtuellen Azure-Computern skalieren.
author: vermagit
ms.service: virtual-machines
ms.subservice: workloads
ms.topic: article
ms.date: 05/15/2019
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 7e05d64420cd920242a887b206fd38cd4a655509
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94967005"
---
# <a name="scaling-hpc-applications"></a>Skalieren von HPC-Anwendungen

Um beim zentralen und horizontalen Skalieren von HPC-Anwendungen in Azure eine optimale Leistung zu erzielen, müssen für die betreffende Workload Experimente zur Leistungsoptimierung durchgeführt werden. In diesem Abschnitt sowie auf den spezifischen Seiten der jeweiligen VM-Serie finden Sie allgemeine Informationen zum Skalieren Ihrer Anwendungen.

## <a name="compiling-applications"></a>Kompilieren von Anwendungen

Anwendungen müssen zwar nicht unbedingt mit entsprechenden Optimierungsflags kompiliert werden, diese Vorgehensweise bietet jedoch die beste Leistung beim zentralen Hochskalieren von virtuellen Computern der HB- und HC-Serie.

### <a name="amd-optimizing-cc-compiler"></a>AMD Optimizing C/C++ Compiler

Das AOCC-Compilersystem (AMD Optimizing C/C++ Compiler) zeichnet sich durch umfangreiche erweiterte Optimierungen, Multithreading und eine Prozessorunterstützung aus, die sowohl die globale Optimierung als auch Vektorisierung, prozessübergreifende Analysen, Schleifentransformationen und Codegenerierung umfasst. AOCC-Binärdateien eignen sich für Linux-Systeme mit der GNU C-Bibliothek (glibc) ab Version 2.17. Die Compilersuite besteht aus einem C/C++-Compiler (Clang), einem Fortran-Compiler (FLANG) und einem Fortran-Front-End für Clang (DragonEgg).

### <a name="clang"></a>Clang

Clang ist ein C-, C++- und Objective-C-Compiler für die Verarbeitung, Analyse, Optimierung, Codegenerierung, Assembly und Verknüpfung. Clang unterstützt das Flag `-march=znver1`, um die bestmögliche Codegenerierung und Optimierung für die Zen-basierte x86-Architektur von AMD zu ermöglichen.

### <a name="flang"></a>FLANG

Der FLANG-Compiler wurde der AOCC erst vor Kurzem (im April 2018) hinzugefügt und kann derzeit von Entwicklern als Vorabversion heruntergeladen und getestet werden. AMD erweitert die GitHub-Version von FLANG (https://github.com/flang-compiler/flang) auf der Grundlage von Fortran 2008. Der FLANG-Compiler unterstützt alle Clang-Compileroptionen sowie zahlreiche FLANG-spezifische Compileroptionen.

### <a name="dragonegg"></a>DragonEgg

DragonEgg ist ein GCC-Plug-In, das die GCC-Optimierer und -Codegeneratoren durch Optimierer und Codegeneratoren aus dem LLVM-Projekt ersetzt. DragonEgg ist in AOCC enthalten. Es kann mit gcc-4.8.x verwendet werden, wurde für Ziele vom Typ x86-32/x86-64 getestet und bereits erfolgreich auf verschiedenen Linux-Plattformen eingesetzt.

GFortran ist das eigentliche Front-End für Fortran-Programme und dient zur Vorverarbeitung und (semantischen) Analyse beim Generieren der GCC GIMPLE-IR (Intermediate Representation). DragonEgg ist ein GNU-Plug-In für den GFortran-Kompilierungsfluss. Es implementiert die GNU-Plug-In-API. Mit der Plug-In-Architektur wird DragonEgg zum Compilertreiber, der die verschiedenen Kompilierungsphasen steuert.  Nach Ausführung der Download- und Installationsanweisungen kann DragonEgg wie folgt aufgerufen werden: 

```bash
$ gfortran [gFortran flags] 
   -fplugin=/path/AOCC-1.2-Compiler/AOCC-1.2-     
   FortranPlugin/dragonegg.so [plugin optimization flags]     
   -c xyz.f90 $ clang -O3 -lgfortran -o xyz xyz.o $./xyz
```
   
### <a name="pgi-compiler"></a>PGI-Compiler
Version 17 der PGI Community Edition kann mit AMD EPYC verwendet werden. Eine PGI-kompilierte Version von STREAM stellt die vollständige Speicherbandbreite der Plattform bereit. Die neuere Community Edition 18.10 (November 2018) sollte ebenso problemlos funktionieren. Hier sehen Sie ein CLI-Beispiel zur optimalen Kompilierung mit dem Intel-Compiler:

```bash
pgcc $(OPTIMIZATIONS_PGI) $(STACK) -DSTREAM_ARRAY_SIZE=800000000 stream.c -o stream.pgi
```

### <a name="intel-compiler"></a>Intel-Compiler
Version 18 des Intel-Compilers kann mit AMD EPYC verwendet werden. Hier sehen Sie ein CLI-Beispiel zur optimalen Kompilierung mit dem Intel-Compiler:

```bash
icc -o stream.intel stream.c -DSTATIC -DSTREAM_ARRAY_SIZE=800000000 -mcmodel=large -shared-intel -Ofast –qopenmp
```

### <a name="gcc-compiler"></a>GCC-Compiler 
Für HPC empfiehlt AMD mindestens die Version 7.3 des GCC-Compilers. Ältere Versionen (etwa die in RHEL/CentOS 7.4 enthaltene Version 4.8.5) werden nicht empfohlen. Mit GCC 7.3 und neueren Versionen wird bei HPL-, HPCG- und DGEMM-Tests eine deutlich höhere Leistung erzielt.

```bash
gcc $(OPTIMIZATIONS) $(OMP) $(STACK) $(STREAM_PARAMETERS) stream.c -o stream.gcc
```

## <a name="scaling-applications"></a>Skalieren von Anwendungen 

Die folgenden Vorschläge dienen zur Optimierung der Skalierungseffizienz, Leistung und Konsistenz von Anwendungen:

* Ordnen Sie Prozesse fest den Kernen 0 bis 59 zu, und verwenden Sie dazu eine sequenzielle Zuordnung (anstelle eines automatischen Ausgleichs). 
* Die Bindung nach NUMA/Kern/HW-Thread ist besser als die Standardbindung.
* Verwenden Sie bei parallelen Hybridanwendungen (OpenMP + MPI) vier Threads und einen MPI-Rang pro CCX.
* Experimentieren Sie bei reinen MPI-Anwendungen mit einem bis vier MPI-Rängen pro CCX, um eine optimale Leistung zu erzielen.
* Anwendungen, die in puncto Speicherbandbreite besonders empfindlich sind, profitieren ggf. von der Verwendung einer geringeren Anzahl von Kernen pro CCX. Bei diesen Anwendungen lässt sich durch die Verwendung von drei oder zwei Kernen pro CCX unter Umständen das Ringen um Speicherbandbreite minimieren und in der Praxis eine höhere Leistung oder eine höhere Skalierungskonsistenz erzielen. Davon kann insbesondere MPI Allreduce profitieren.
* Bei deutlich umfangreicheren Ausführungen empfiehlt sich die Verwendung von UD-Transporten oder Hybridtransporten (RC + UD). Dies wird von vielen MPI-/Runtimebibliotheken intern gehandhabt (beispielsweise bei UCX oder MVAPICH2). Überprüfen Sie Ihre Transportkonfigurationen für umfangreiche Ausführungen.

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich ausführlicher über [HPC](/azure/architecture/topics/high-performance-computing/) in Azure.
