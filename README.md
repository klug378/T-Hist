[README на русском языке](README-ru.md)

This software is intended for use in the `FidoNet` computer network and other FTN-style networks

---
<div align="center">

![T-Hist](./doc/t-hist.png)

[![Visitors](https://visitor-badge.laobi.icu/badge?page_id=klug378.T-Hist)](https://github.com/klug378/T-Hist)
[![GitHub Release](https://img.shields.io/github/v/release/klug378/T-Hist)](https://github.com/klug378/T-Hist)
[![Github All Releases](https://img.shields.io/github/downloads/klug378/T-Hist/total.svg)](https://github.com/klug378/T-Hist)
[![Github Latest Release](https://img.shields.io/github/downloads/klug378/T-Hist/latest/total.svg)](https://github.com/klug378/T-Hist)
[![GitHub Last commit](https://img.shields.io/github/last-commit/klug378/T-Hist.svg?style=dark)](https://github.com/klug378/T-Hist)

# T-Hist – History utility for FTN mailers
Supported:<br>[Binkd](https://github.com/pgul/binkd), [qico 0.60.2+](https://github.com/glasslike/qico), T-Mail, Argus,
Internet Rex, FrontDoor, Bink/+, FhMail,<br>BinkleyTerm-XE, The Brake!, KittenMail, DVMmail, XMail32, BasicMail<br>
and any other FTN-mailers using the T-Hist or T-Mail binary log formats<br><br>
Version 1.8.2<br><br>
Linux (x86_64, i686, arm64) and Windows (Win64, Win32) executables
</div>

---

## Table of Contents

- [Purpose of T-Hist](#purpose-of-t-hist)
- [Structure of distributed ZIP archive with utilities](#structure-of-distributed-zip-archive-with-utilities)
- [Content of generated statistics](#content-of-generated-statistics)
  - [Load graph and histograms example](#load-graph-and-histograms-example)
  - [Link statistics example](#link-statistics-example)
  - [Session statistics example](#session-statistics-example)
  - [Summary information example](#summary-information-example)
- [Wide-screen mode](#wide-screen-mode)
- [Configuration file. Parameters. Command-line options](#configuration-file-parameters-command-line-options)
- [Native binary log format. Format сonverter](#native-binary-log-format-format-сonverter)
- [LnkStat utility for creating multi-day link statistics](#lnkstat-utility-for-creating-multi-day-link-statistics)
- [DmpHist binary Log dumper](#dmphist-binary-log-dumper)
- [Gratitudes](#gratitudes)


## Purpose of T-Hist

The `T-Hist utility` is designed to create text files with statistics on the operation of one or more FTN mailers during a given interval of a given
date day for given FTN addresses. `T-Hist` receives information from binary logs, also called history files.

Development of the `T-Hist` began in 1996, initially only to get T-Mail mailer statistics. Subsequently, the list of supported mailers and binary log
formats has expanded. The latest version of `T-Hist` for DOS, OS/2, NT (win32) – `0.30.alpha7` – was released in 2003. After that, the development process
stopped for 23 years.

In 2026, work on the project was resumed. Released new versions of `T-Hist` and related utilities for Linux and Windows with new features. Support for
older operating systems has been removed.

Throughout the document, the term `old versions` means version `0.30.alpha7` and earlier versions of the utility, and the term `new versions` means
versions `1.x.x`.


## Structure of distributed ZIP archive with utilities

The utilities are distributed as a ZIP archive containing following directories:
- `linux` – Linux executables (x86_64, i686 and arm64 architectures);
- `linux-static` – Linux executables with static libraries (x86_64, i686 and arm64 architectures);
- `windows` – Windows 64-bit and 32-bit executables.
- `doc` – documentation;
- `binkd_patch` – Binkd patches that add support for `T-Hist` binary log format.

Linux executables with static libraries are compressed by [UPX 4.2.4](https://upx.github.io) executable packer to reduce their size.

The documentation catalog contains:
- file [T-HIST-ru.md](./doc/T-HIST-ru.md) with a description of `T-Hist` and related utilities (so far only in Russian);
- sample configuration file [t-hist.ctl](t-hist.ctl) with detailed description of parameters (so far only in Russian);
- files [FORMATS.md](./doc/FORMATS.md) and [FORMATS-ru.md](./doc/FORMATS-ru.md) with a description of binary log formats, including several versions
of the native `T-Hist` format (in English and in Russian);
- old documentation file [t-hist-old.txt](./doc/t-hist-old.txt) from version `0.30.alpha7` (in Russian) for reference.

## Content of generated statistics

`T-Hist` creates three text files:
- file with graph and histograms of FTN-system loading for one or several simultaneously operating mailers;
- file of link statistics with a table containing for each FTN-address included in the statistics, the number of sessions, their total duration
and total traffic during a given time interval;
- a file of session statistics with a table containing for each session performed during a given time interval, its duration and values of
received/sent traffic.

At the end of each files, the summary information on the number and duration of all incoming and outgoing sessions, the total number of sessions
and their duration, the volume of received and sent traffic and the average CPS are printed. (CPS is "chars per second" or "bytes per second".)
If the mailer marks password-protected sessions, then the values for password and non-password sessions are also printed.

All files are UTF-8 encoded. To post statistics in FTN echo conferences or netmail, text files must be converted to the appropriate encoding.
For example, for Russian-language echo conferences with CP866 encoding:
```shell
iconv -f UTF-8 -t CP866 in.txt -o out.txt
```

Below are examples of fragments of statistics files from the node of Alex Barinov (2:5020/715).


### Load graph and histograms example
```text
              0   2   4   6   8  10  12  14  16  18  20  22  24               
               ├─┴─┼─┴─┼─┴─┼─┴─┼─┴─┼─┴─┼─┴─┼─┴─┼─┴─┼─┴─┼─┴─┼─┴─┤Ses  Rcvd  Sent
2:5020/101     ▐ ▐ █▌  │█ █▐▐█▐▐█▐▐▐█▐█▐█▐▐▐▐▐▐█▐█▐▐█▐▐▐ █▐▐▐▐▐│277  3.7K  202K
2:5020/113     │▌ ▌▌▌  ▌ █ ▐ ▐ ▐ ▐ ▐ ▐ ▐ ▐ ▐ ▐ ▐ ▐ ▐ ▐ ▐ ▐ ▐ ▐▐│ 28 14.9K 53.4K
2:5020/378     │   │   │   │▌  │  ▌│ ▐ │▌  │   ▌  ▌▌▌  ▌  ▌│ ▐ │ 11  2.4M  142K
2:5020/556     │   █   ▐▌  │█  │▐  │▐▌ │ ▌ │ █ │ ▐▌│  ▌│  █│  ▐▌1098    0     0
2:5020/715.100 │   │   │   │ ▌ │   │   │  ▐│   │   │   │   │   │  2     0  285K
2:5020/830     │▌  │▌  │▌  │▌  │▌  │▌  │███▐█ ▐▐▌█ ▐█ ▐│▌██▐▌▐▐│ 50  300K  1.2M
2:5020/1200    │   │   │   │   │   │   │   │  ▐│   │ ▌ ▌   ▐▐▐ │  6 1290M  5.7M
2:5020/1477    │█  │▐▌ │ █▐▐▐▐▐▐▐▐▐▐▐▐▐▐▐▐▐▐▐▐▐▐▐▐▐▐▐█▐█▐▐▐▐▐▐▐│365     0  333M
2:5020/1668    │   │   │   │  ▐│   │▐  │▐ ▐│   │▐  │  ▐│   │▐  │  8     0 90.1K
2:5020/3452    ▐▌  │▌  │█  │▐  │   │▐  │   │  ▐│ █ ▐   │   │   │306     0 55.3K
2:5020/4096    ▐▌▐ ▐   │ ▐▌▐▌▐ ▐ ▐ ▐▌▐▌▐▌▐ ▐ ▐▐▐ █ ▐▌▐▐▐ █▐▐ ▐ │138     0  161K
2:5020/9696    │▐▌ │ ▌ │ ▌ │ █▐▐ ▐▌▌▌ ▌████▐██▐▐█▐▌▐██▐▌███▐█▌▐│256     0  794M

[...]

                                 Busy Histogram
           100%┬                                                  Rcvd:   1319M
               ▐▌   ▌    ▌                                        Sent:   10.9G
   Sessions    ▐▌   █   ▐▌                           ▌            ─────────────
               ▐▌▐ ▐█   █▌                           ▌            Average      
 Total: 19860  ▐▌▐ ▐█   █▌                           ▌            CPS:   706.4K
 ────────────  ▐▌▐ ▐█   █▌  ▐                        █ ▌          ─────────────
 In:     2563  ▐▌▐ ▐█   █▌  ▐                        █ ▌   ▐      Time Online: 
 Out:   17297  ▐▌▐▌▐█ ▐ █▌  ▐                        █ █   ▐           17:28:12
 ────────────  ▐██▌████▐██  █                        █ █   ▐      ─────────────
 Aborted:12970 ▐██▌████▐██▌▐█       ▐    ▌   ▐   ▐   █▐█  ▌▐      In:   1:51:38
 Protect:7420  ▐██▌████▐███▐█▄▄▐█▐▄▐███████▐███▐███████████████▌  Out: 15:36:34
             0%┼─┬─┬─┬─┬─┬─┬─┬─┬─┬─┬─┬─┬─┬─┬─┬─┬─┬─┬─┬─┬─┬─┬─┬─┐  ─────────────
               0   2   4   6   8  10  12  14  16  18  20  22  24  Usage:  14.6%

```

### Link statistics example
```text
╔══════════════════╦═══════════════════════════╦═════════╦═════════════╦══════╗
║                  ║      Sessions / Time      ║  Time   ║   Traffic   ║  CPS ║
║     Address      ╠═════════════╦═════════════╣ online  ╠══════╦══════╣ bytes║
║pwd               ║   Incoming  ║   Outgoing  ║ In+Out  ║ Rcvd ║ Sent ║ /sec ║
╠│═════════════════╬═══╤═════════╬═══╤═════════╬═════════╬══════╬══════╬══════╣
║√ 2:5020/101      ║ 43│ 0:02:10 ║234│ 0:13:11 ║ 0:15:21 ║  3.7K║202.2K║  1.2K║
║√ 2:5020/113      ║ 28│ 0:04:49 ║  -│ -:--:-- ║ 0:04:49 ║ 14.9K║ 53.4K║   999║
║√ 2:5020/378      ║ 11│ 0:00:18 ║  -│ -:--:-- ║ 0:00:18 ║  2.4M║142.4K║191.8K║
║√ 2:5020/556      ║  -│ -:--:-- ║1098 0:33:59 ║ 0:33:59 ║     0║     0║     0║
║√ 2:5020/715.100  ║  2│ 0:00:03 ║  -│ -:--:-- ║ 0:00:03 ║     0║284.9K║ 94.9K║
║√ 2:5020/830      ║  -│ -:--:-- ║ 50│ 0:03:26 ║ 0:03:26 ║299.6K║  1.2M║ 68.0K║
║√ 2:5020/1200     ║  4│ 0:09:09 ║  2│ 0:00:02 ║ 0:09:11 ║ 1290M║  5.7M║  2.3M║
║√ 2:5020/1477     ║ 39│ 0:01:08 ║326│ 0:17:30 ║ 0:18:38 ║     0║333.1M║  1.7M║
║√ 2:5020/1668     ║  8│ 0:00:41 ║  -│ -:--:-- ║ 0:00:41 ║     0║ 90.1K║ 22.5K║
║√ 2:5020/3452     ║  -│ -:--:-- ║306│ 0:12:20 ║ 0:12:20 ║     0║ 55.3K║ 13.8K║
║√ 2:5020/4096     ║ 19│ 0:00:19 ║119│ 0:10:40 ║ 0:10:59 ║     0║160.5K║  2.8K║
║√ 2:5020/9696     ║  -│ -:--:-- ║256│ 0:18:03 ║ 0:18:03 ║     0║793.7M║  1.3M║
```

### Session statistics example
```text
╔════════════╤═════╤════════════════════╤══════════╤═════════════════╤════════╗
║  Session   │ Dir │      Address       │   Time   │     Traffic     │   CPS  ║
║   start    │     │pwd             abrt│  Online  │  Rcvd     Sent  │ bytes/s║
╠════════════╪═════╪│══════════════════│╪══════════╪════════╤════════╪════════╣
║  00:00:04  │ Out │√  2:301/1          │  0:00:12 │   5773 │      0 │    481 ║
║  00:00:18  │ Out │   2:5020/5452     A│  0:00:01 │      0 │      0 │      0 ║
║  00:00:18  │ Out │√  2:5001/100       │  0:00:01 │      0 │   1462 │   1.4K ║
║  00:00:18  │ Out │√  2:5051/44        │  0:00:01 │      0 │   1459 │   1.4K ║
║  00:06:03  │ Out │√  2:46/49          │  0:00:02 │      0 │   2207 │   1.0K ║
║  00:05:52  │ Out │√  2:301/1         A│  0:00:43 │      0 │   5102 │    118 ║
║  00:10:17  │ Out │√  2:5030/115       │  0:00:01 │      0 │    678 │    678 ║
║  00:09:40  │ Out │√  2:5020/2992     A│  0:01:07 │      0 │  11353 │    169 ║
║  00:16:15  │ Out │√  2:6078/1         │  0:00:27 │      0 │  21312 │    789 ║
║  00:16:37  │ Out │√  2:5030/1115      │  0:00:42 │      0 │  22459 │    534 ║
║  00:17:24  │ Out │√  2:5020/4096      │  0:00:07 │      0 │  22448 │   3.1K ║
║  00:17:34  │ Out │√  2:5090/138       │  0:00:16 │      0 │  23130 │   1.4K ║
║  00:17:44  │ Out │√  2:5001/100       │  0:00:36 │      0 │  58762 │   1.5K ║
```

### Summary information example
```text
┌──────────────────────────┬───────────────────────┬──────────────────────────┐
│ Total Received :  276.9M │ Total Ses In  :  3299 │ Time In      :   1:09:05 │
│ Total Sent     :   2066M │ Total Ses Out :  8821 │ Time Out     :   3:33:57 │
│ Total Traffic  :   2343M │ Total Sessions: 12129 │ Time Online  :   4:43:32 │
├──────────────────────────┼───────────────────────┼──────────────────────────┤
│ Protected Rcvd :  276.9M │ Prot Ses In   :  3296 │ Protected In :   1:09:02 │
│ Protected Sent :   2066M │ Prot Ses Out  :  5609 │ Protected Out:   2:39:17 │
│ Protected Total:   2343M │ Prot Ses Total:  8905 │ Protected Tot:   3:48:19 │
├──────────────────────────┼───────────────────────┼──────────────────────────┤
│ UnProt Received:       0 │ UnProt Ses In :     3 │ UnProt In    :   0:00:03 │
│ UnProt Sent    :       0 │ UnProt Ses Out:  3212 │ UnProt Out   :   0:54:40 │
│ UnProt Total   :       0 │ UnProt Ses Tot:  3224 │ UnProt Total :   0:55:13 │
└──────────────────────────┴───────────────────────┴──────────────────────────┘
  K is KiB (1024 bytes), M is MiB (1048576 bytes), G is GiB (1073741824 bytes)
```


## Wide-screen mode

Old versions of `T-Hist` and related utilities created statistics files with lines that did not exceed 80 characters. New versions of utilities
can create the statistics in the wide-screen mode, which is enabled by default. In this mode, the text information about FTN systems from binary logs
or from descriptions in the `Addr` parameters can be printed to the right of the load graph and to the right of the tables with statistics.
Below are examples of fragments of statistics files created in wide-screen mode.

```text
               0   2   4   6   8  10  12  14  16  18  20  22  24
               ├─┴─┼─┴─┼─┴─┼─┴─┼─┴─┼─┴─┼─┴─┼─┴─┼─┴─┼─┴─┼─┴─┼─┴─┤Ses  Rcvd  Sent   FTN-system info
2:5020/101     ▐ ▐ █▌  │█ █▐▐█▐▐█▐▐▐█▐█▐█▐▐▐▐▐▐█▐█▐▐█▐▐▐ █▐▐▐▐▐│277  3.7K  202K │ n5020.ru, akf, Moscow, USSR
2:5020/113     │▌ ▌▌▌  ▌ █ ▐ ▐ ▐ ▐ ▐ ▐ ▐ ▐ ▐ ▐ ▐ ▐ ▐ ▐ ▐ ▐ ▐ ▐▐│ 28 14.9K 53.4K │ Minas Anor, Moscow, Russia
2:5020/378     │   │   │   │▌  │  ▌│ ▐ │▌  │   ▌  ▌▌▌  ▌  ▌│ ▐ │ 11  2.4M  142K │ 92.36.120.111, klug, Moscow, Russia
2:5020/556     │   █   ▐▌  │█  │▐  │▐▌ │ ▌ │ █ │ ▐▌│  ▌│  █│  ▐▌1098    0     0 │ fido.lushnikov.net, Moscow, Russia
2:5020/715.100 │   │   │   │ ▌ │   │   │  ▐│   │   │   │   │   │  2     0  285K │ FidoMailMobile, Zheleznodorozhny
2:5020/830     │▌  │▌  │▌  │▌  │▌  │▌  │███▐█ ▐▐▌█ ▐█ ▐│▌██▐▌▐▐│ 50  300K  1.2M │ 830.ru, TITANIC Station IP, Moscow, Russia
2:5020/1200    │   │   │   │   │   │   │   │  ▐│   │ ▌ ▌   ▐▐▐ │  6 1290M  5.7M │ MetNet Information Center, Moscow, Russia
2:5020/1477    │█  │▐▌ │ █▐▐▐▐▐▐▐▐▐▐▐▐▐▐▐▐▐▐▐▐▐▐▐▐▐▐▐█▐█▐▐▐▐▐▐▐│365     0  333M │ m0d3m's_station, Moscow--Russia
2:5020/1668    │   │   │   │  ▐│   │▐  │▐ ▐│   │▐  │  ▐│   │▐  │  8     0 90.1K │ First Encounters/Binkp, CCCP, Moscow, Russia
2:5020/3452    ▐▌  │▌  │█  │▐  │   │▐  │   │  ▐│ █ ▐   │   │   │306     0 55.3K │ fido.babon.ru, Panzer station II, Moscow, Russia
2:5020/4096    ▐▌▐ ▐   │ ▐▌▐▌▐ ▐ ▐ ▐▌▐▌▐▌▐ ▐ ▐▐▐ █ ▐▌▐▐▐ █▐▐ ▐ │138     0  161K │ fido.pochek.net, 4K_System, Moscow, Russia
2:5020/9696    │▐▌ │ ▌ │ ▌ │ █▐▐ ▐▌▌▌ ▌████▐██▐▐█▐▌▐██▐▌███▐█▌▐│256     0  794M │ skovpen.org, SDS, Moscow, Russia
[...]
```

```text
╔══════════════════╦═══════════════════════════╦═════════╦═════════════╦══════╗
║                  ║      Sessions / Time      ║  Time   ║   Traffic   ║  CPS ║
║     Address      ╠═════════════╦═════════════╣ online  ╠══════╦══════╣ bytes║
║pwd               ║   Incoming  ║   Outgoing  ║ In+Out  ║ Rcvd ║ Sent ║ /sec ║
╠│═════════════════╬═══╤═════════╬═══╤═════════╬═════════╬══════╬══════╬══════╣
║√ 2:5020/101      ║ 43│ 0:02:10 ║234│ 0:13:11 ║ 0:15:21 ║  3.7K║202.2K║  1.2K║ n5020.ru, akf, Moscow, USSR                     
║√ 2:5020/113      ║ 28│ 0:04:49 ║  -│ -:--:-- ║ 0:04:49 ║ 14.9K║ 53.4K║   999║ Minas Anor, Moscow, Russia                      
║√ 2:5020/378      ║ 11│ 0:00:18 ║  -│ -:--:-- ║ 0:00:18 ║  2.4M║142.4K║191.8K║ 92.36.120.111, klug, Moscow, Russia             
║√ 2:5020/556      ║  -│ -:--:-- ║1098 0:33:59 ║ 0:33:59 ║     0║     0║     0║ fido.lushnikov.net, Moscow, Russia              
║√ 2:5020/715.100  ║  2│ 0:00:03 ║  -│ -:--:-- ║ 0:00:03 ║     0║284.9K║ 94.9K║ FidoMailMobile, Zheleznodorozhny                
║√ 2:5020/830      ║  -│ -:--:-- ║ 50│ 0:03:26 ║ 0:03:26 ║299.6K║  1.2M║ 68.0K║ 830.ru, TITANIC Station IP, Moscow, Russia      
║√ 2:5020/1200     ║  4│ 0:09:09 ║  2│ 0:00:02 ║ 0:09:11 ║ 1290M║  5.7M║  2.3M║ MetNet Information Center, Moscow, Russia       
║√ 2:5020/1477     ║ 39│ 0:01:08 ║326│ 0:17:30 ║ 0:18:38 ║     0║333.1M║  1.7M║ m0d3m's_station, Moscow--Russia                 
║√ 2:5020/1668     ║  8│ 0:00:41 ║  -│ -:--:-- ║ 0:00:41 ║     0║ 90.1K║ 22.5K║ First Encounters/Binkp, CCCP, Moscow, Russia    
║√ 2:5020/3452     ║  -│ -:--:-- ║306│ 0:12:20 ║ 0:12:20 ║     0║ 55.3K║ 13.8K║ fido.babon.ru, Panzer station II, Moscow, Russia
║√ 2:5020/4096     ║ 19│ 0:00:19 ║119│ 0:10:40 ║ 0:10:59 ║     0║160.5K║  2.8K║ fido.pochek.net, 4K_System, Moscow, Russia      
║√ 2:5020/9696     ║  -│ -:--:-- ║256│ 0:18:03 ║ 0:18:03 ║     0║793.7M║  1.3M║ skovpen.org, SDS, Moscow, Russia                
[...]
```

```text
╔════════════╤═════╤════════════════════╤══════════╤═════════════════╤════════╗
║  Session   │ Dir │      Address       │   Time   │     Traffic     │   CPS  ║
║   start    │     │pwd             abrt│  Online  │  Rcvd     Sent  │ bytes/s║
╠════════════╪═════╪│══════════════════│╪══════════╪════════╤════════╪════════╣
║  00:00:04  │ Out │√  2:301/1          │  0:00:12 │   5773 │      0 │    481 ║ fnthub.abad1dea.to, Bufferzone, Zug, Switzerlan
║  00:00:18  │ Out │   2:5020/5452     A│  0:00:01 │      0 │      0 │      0 ║ fido.huba715.ru, New World
║  00:00:18  │ Out │√  2:5001/100       │  0:00:01 │      0 │   1462 │   1.4K ║ f100.5001.ru, Postmortem, Kemerovo
║  00:00:18  │ Out │√  2:5051/44        │  0:00:01 │      0 │   1459 │   1.4K ║ fido.pdp11.ru, Digital, Simbirsk--Russia
║  00:06:03  │ Out │√  2:46/49          │  0:00:02 │      0 │   2207 │   1.0K ║ fidotallinn.ddns.net, EEStation, Tallinn, Estonia
║  00:05:52  │ Out │√  2:301/1         A│  0:00:43 │      0 │   5102 │    118 ║ fnthub.abad1dea.to, Bufferzone, Zug, Switzerlan
║  00:10:17  │ Out │√  2:5030/115       │  0:00:01 │      0 │    678 │    678 ║ f115.spb.ru, EdgeCity, St.Petersburg, Russia
║  00:09:40  │ Out │√  2:5020/2992     A│  0:01:07 │      0 │  11353 │    169 ║ 2992.mooo.com, Donskoy, Haifa, Israel
║  00:16:15  │ Out │√  2:6078/1         │  0:00:27 │      0 │  21312 │    789 ║ Techno Music BBS, Kaliningrad, Russia
║  00:16:37  │ Out │√  2:5030/1115      │  0:00:42 │      0 │  22459 │    534 ║ f1115.n5030.z2.binkp.net, EdgeCity II, St.Petersburg, Russia
║  00:17:24  │ Out │√  2:5020/4096      │  0:00:07 │      0 │  22448 │   3.1K ║ fido.pochek.net, 4K_System, Moscow, Russia
║  00:17:34  │ Out │√  2:5090/138       │  0:00:16 │      0 │  23130 │   1.4K ║ f138.n5090.krnet.ru, SkyStation, Krasnoyarsk--Russia
║  00:17:44  │ Out │√  2:5001/100       │  0:00:36 │      0 │  58762 │   1.5K ║ f100.5001.ru, Postmortem, Kemerovo, Russia
[...]
```

Not all mailers store additional information in binary logs. For example, there is no such information in the binary logs of the Binkd mailer.
If you do not use descriptions in the `Addr` parameters, then the statistics for Binkd will be no more than 80 characters wide even in wide-screen mode.

But some of the supported mailers contain in binary logs information about the FTN systems, or about the protocol of connection, or diagnostic messages
(such mailers are: qico 0.60.2+, Internet Rex, FrontDoor, Bink/+, FhMail, BinkleyTerm-XE, KittenMail, The Brake!, some mailers with binary logs in the
T-Mail new format, and mailers using binary logs of the native `T-Hist` format). For these mailers, text information will be printed in wide-screen mode.

To control the wide screen mode, use the `WideScreen` parameter in the configuration file or the `-w` command-line option.


## Configuration file. Parameters. Command-line options

The operation of `T-Hist` and related utilities is controlled by parameters whose values can be set in the configuration file. Some parameters can also
be specified using command-line options. By default, the configuration file is named `t-hist.ctl` and is located in the current directory.
Use the `-c` command-line option to specify a different configuration file or to specify to work without a configuration file.

Parameter names and their values are case-insensitive, with the exception of file names in Linux. Comments can start with `#` and `;`.

If any of the parameters are specified both in the configuration file and using the command-line option, then the value from the command line will be used.

The descriptions of all parameters and all command-line options are given in the sample configuration file [t-hist.ctl](./doc/t-hist.ctl)
and in the documentation file [T-HIST-ru.md](./doc/T-HIST-ru.md) (so far only in Russian), that are in the [doc](./doc) directory.


## Native binary log format. Format сonverter

The disadvantage of the binary log formats of all mailers supported by old versions of `T-Hist` is using 32-bit values to store information about traffic.
In such binary logs, it is impossible to correctly store information if more than 4 gigabytes have been sent or received.

This formats were developed in the dial-up era, when it was almost impossible to transfer more than 4 gigabytes in one session.
But in the era of Fido-over-IP and high-speed networks, a session with more than 4 gigabytes of traffic is common, especially on large FTN hubs.

The new versions of `T-Hist` implement support for several versions of the own binary log format (native `T-Hist` format) with 64-bit traffic values
and 64-bit timestamps, which is proposed to be used by FTN mailer developers. In addition to correctly storing data about sessions with large volumes
of traffic, the format allows you to save text strings that `T-Hist` will be printed in wide-screen mode. Also, the native `T-Hist` format allow to save
the `Listed` mark for sessions, which FTN-address is present in the nodelists specified in the mailer configuration. In detail, all versions of the native
`T-Hist` binary log format are considered in the file [FORMATS.md](./doc/FORMATS.md) in the [doc](./doc) directory.

At the time of the release of the `T-Hist` version `1.8.2`, binary log of the native `T-Hist` format is used in the qico mailer version 0.60.2
([GitHub](https://github.com/glasslike/qico)) by Alexey Matrosov (2: 203/910).

It is possible to add support for new binary log format to Binkd by patching file [binlog.c](https://github.com/pgul/binkd/blob/master/binlog.c)
from [Binkd repository](https://github.com/pgul/binkd). The patches are located in the [binkd_patch](./binkd_patch) directory.
For more information see [BINKD-PATCH.md](./binkd_patch/BINKD-PATCH.md).

For the convenience of working with different binary log formats, the `HistConv` converter has been created. The `HistConv` utility creates for a specified
binary log file the same log in a different format. The source file can be the binary log file of any mailer supported by `T-Hist`. The format of the source
binary log is determined automatically. To select a target format, use the `-f` command-line option to specify one of the following formats:
  - native `T-Hist` format of any versions;
  - format of T-Mail since version 2603 (T-Mail new format);
  - format of T-Mail before version 2603 (T-Mail old format);
  - Binkd format (T-Mail old format with inverted mark of session direction).

Use the program help to get valid target format names:
```shell
histconv --help
```

## LnkStat utility for creating multi-day link statistics

The `T-Hist` package includes the `LnkStat` utility for creating multi-day link statistics.
Unlike `T-Hist`, which creates statistics for time intervals not exceeding 24 hours, `LnkStat` creates statistics for the date range specified by
the command-line options (by default for the date range of all sessions with specified addresses – from the date of the oldest session to the date of
the newest session inclusive).

The result of `LnkStat` is a table similar to the table of link statistics generated by `T-Hist`. By default, `LnkStat` write statistics to `stdout`,
but you can use the `-o` command-line option to specify a file name to write statistics.

Like `T-Hist`, the `LnkStat` utility can work in wide-screen mode, printing text information from binary logs or address descriptions from the `Addr`
parameters to the right of the table lines.

Below is an example of a fragment of multi-day link statistics created by the `LnkStat` utility on the Alex Barinov's node (2:5020/715) for the date
range specified in the statistics header.
```text
                   From: 00:05:07, 07 September 2026, Monday
                   To  : 00:05:15, 14 September 2026, Monday
╔════════════════╦═════════════════════════════╦═════════╦═════════════╦══════╗
║                ║       Sessions / Time       ║  Time   ║   Traffic   ║  CPS ║
║    Address     ╠══════════════╦══════════════╣ online  ╠══════╦══════╣ bytes║
║                ║   Incoming   ║   Outgoing   ║ In+Out  ║ Rcvd ║ Sent ║ /sec ║
╠════════════════╬════╤═════════╬════╤═════════╬═════════╬══════╬══════╬══════╣
║ 2:5020/101     ║ 336│ 0:05:36 ║ 373│ 0:06:13 ║ 0:11:49 ║   747║570.4K║  2.7K║
║ 2:5020/113     ║ 193│ 0:03:31 ║   -│ -:--:-- ║ 0:03:31 ║109.1K║337.8K║  6.4K║
║ 2:5020/378     ║  59│ 0:01:28 ║   -│ -:--:-- ║ 0:01:28 ║   518║526.4K║  8.4K║
║ 2:5020/715.100 ║  60│ 0:01:04 ║   -│ -:--:-- ║ 0:01:04 ║  4.2K║651.8K║ 21.8K║
║ 2:5020/830     ║   -│ -:--:-- ║ 160│ 0:02:42 ║ 0:02:42 ║     0║415.5K║  6.2K║
║ 2:5020/1200    ║  28│ 0:04:08 ║  52│ 0:00:55 ║ 0:05:03 ║822.5M║  3.1M║  3.0M║
║ 2:5020/1477    ║ 336│ 0:05:36 ║ 104│ 0:02:02 ║ 0:07:38 ║     0║261.8M║  3.4M║
║ 2:5020/1668    ║  61│ 0:05:04 ║   -│ -:--:-- ║ 0:05:04 ║     0║357.6K║  2.3K║
║ 2:5020/3452    ║   -│ -:--:-- ║ 103│ 0:01:43 ║ 0:01:43 ║     0║274.4K║  4.7K║
║ 2:5020/4096    ║ 167│ 0:02:47 ║ 332│ 0:05:32 ║ 0:08:19 ║     0║589.3K║  3.3K║
║ 2:5020/9696    ║   3│ 0:00:04 ║ 689│ 0:20:06 ║ 0:20:10 ║     0║504.9M║481.8K║
[...]
```

## DmpHist binary Log dumper

The `T-Hist` package includes the `DmpHist` utility, which prints the table with all records from binary log to `stdout`. The table contains:
- FTN-address of the session (`0:0/0.0` for sessions with failed handshake);
- session timestamp;
- session duration in seconds;
- received and sent traffic;
- session status bits.

Like `T-Hist` and `LnkStat`, the `DmpHist` utility can work in wide-screen mode, printing text information from binary logs or address descriptions from
the `Addr` to the right of the table lines.

Below is an example of a fragment of Binkd binary log dump created by the `DmpHist` utility on the Alex Barinov's node (2:5020/715).
```text
File   : /var/log/fido/binkd.sts
Format : Binkd (T-Mail old)
┌───────────────────┬──────────────────────────┬───────┬────────┬────────┬────┐
│      Address      │     Session timestamp    │Time(s)│Received│  Sent  │PNOI│
├───────────────────┼──────────────────────────┼───────┼────────┼────────┼────┤
│ 2:5095/20.0       │ Thu Aug  6 06:00:01 2026 │     0 │      0 │      0 │0010│
│ 2:5020/570.0      │ Thu Aug  6 06:00:02 2026 │     4 │   1289 │      0 │0010│
│ 2:5020/1146.0     │ Thu Aug  6 06:00:07 2026 │     1 │      0 │   1273 │0001│
│ 2:5001/100.0      │ Thu Aug  6 06:00:07 2026 │    13 │      0 │   1275 │0001│
│ 2:5020/290.0      │ Thu Aug  6 06:00:11 2026 │    12 │      0 │      0 │0010│
│ 0:0/0.0           │ Thu Aug  6 05:59:58 2026 │    57 │      0 │      0 │0011│
│ 2:5020/921.0      │ Thu Aug  6 06:01:02 2026 │     3 │      0 │      0 │0010│
[...]
│ 2:463/68.0        │ Fri Aug  7 00:15:39 2026 │     1 │   2323 │      0 │0010│
│ 2:5051/44.0       │ Fri Aug  7 00:15:41 2026 │     0 │      0 │   2328 │0001│
│ 2:5019/40.0       │ Fri Aug  7 00:15:46 2026 │     0 │      0 │      0 │0010│
└───────────────────┴──────────────────────────┴───────┴────────┴────────┴────┘
               Password protected session; not all mailers mark it ───────┘│││
               No-error session (not aborted); not all mailers mark it ────┘││
                                                      Outgoing session ─────┘│
                                                      Incoming session ──────┘
```


## Gratitudes

I would like to thank Andy Elkin (2:5030/15) for the T-Mail mailer; Slawa Olhovchenkov (2:5030/57.570) and Jury Fradkin (2:5030/339) for the T-Mail API;
Mikel Lavrentyev (2:5020/35) for the bink/+ links statistics utility, which was the prototype for load graph; John Gladkih (2:5051/16) for the source code
of The Brake! binary log processing; Eugine Tukin (2:5020/324) for the BTSV utility, which was the prototype for link statistics; Alex Barinov (2:5020/715)
for testing the new versions of `T-Hist` on its node; Alexey Matrosov (2:203/910) for binary log format suggestions, help with timestapms processing and
new ideas for `T-Hist`; as well as everyone whose ideas, suggestions and tests allowed me to create and develop this project.

---

Mikhail Markovskiy (klug), 2:5020/378
