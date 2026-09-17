[README на русском языке](README-ru.md)

This software is intended for use in the `FidoNet` computer network and other FTN-style networks (FTN &ndash; FidoNet Technology Network)

---

# T-Hist for Linux x86_64/i686 and Windows 64/32

The `T-Hist` utility creates text files with graphs and histograms of the FTN node load and with sessions and links statistics
based on information stored in the mailer's binary logs (history files). The `T-Hist` supports binary logs of the following mailers:
T-Mail, Binkd, Argus, Internet Rex, FrontDoor, Bink/+, FhMail, BinkleyTerm-XE, The Brake!, KittenMail, DVMmail, XMail32, BasicMail.

Development of the `T-Hist` began in 1996. The latest version of `T-Hist` 0.30.alpha7 for DOS, OS/2, NT (win32) was released in 2003.
After 23 years, the new `T-Hist` (and related utilities `LnkStat` and `DmpHist`) were released for Linux and Windows.

The utilities are distributed as a ZIP archive containing executable files in the following directories:
- `linux`&ensp;&ndash; Linux executables (x86_64 and i686 architectures);
- `linux-static`&ensp;&ndash; Linux executables with static libraries (x86_64 and i686 architectures);
- `windows`&ensp;&ndash; Windows 64-bit and 32-bit executables.

The complete documentation for the new version is not yet ready. As a basis, you can use the documentation (in Russian only, UTF-8 encoded)
and an example of a configuration file from the last older version `0.30.alpha7`, which are located in the `doc_old_2003` directory.

The differences between new and older versions will be discussed below.

## Terms used in this document

- The term `old versions` means `T-Hist` `0.30.alpha7` from 2003, and earlier versions.

- The term `parameter` means parameter in the configuration file.

- The terms `group`, `group of addresses` means an address defined in `Addr` parameter with at least one `#` character (macro).
Addresses that use the wildcards `*` are not groups.

- The terms `binary log` and `history file` are equivalent.

## Main features of the new `T-Hist` and related utilities

- All utilities print information and create output text files in UTF-8 encoding.
To post statistics in FTN echo conferences or netmail, text files must be converted to the appropriate encoding.
For example, for Russian-language echo conferences with CP866 encoding:

	```shell
	iconv -f UTF-8 -t CP866 in.txt -o out.txt
	```

  The `LnkStat` and `DmpHist` utilities (but not `T-Hist`) have the `-u` command line option to select the encoding of the printed information:

  - `-u`, `-u+`&ensp;&ndash; use UTF-8 encoding (default);

  - `-u-`&ensp;&emsp;&emsp;&ndash; use ASCII 7-bit encoding without pseudo-graphics.

- All utilities have a wide-screen mode that allows you to display lines longer than 80 characters. This mode is enabled by default,
however, if you process the old-format binary logs (for example, Binkd logs) and do not use comments (descriptions) in the `Addr` parameters,
then the generated statistics will still be no more than 80 characters wide.

  But if at least one of the conditions is met:
  
  - the binary log contains information about the names of FTN systems
  (such binary logs have, for example, Internet Rex, FrontDoor, Bink/+, FhMail, BinkleyTerm-XE);
  
  - at least one of the parameters `Addr` has a comment (description),
  
  the FTN-system names (from binary log) or comments (from `Addr`  parameters) will be printed in wide-screen mode
  to the right of the load graph and to the right of the tables with statistics. If both the FTN-system name from the binary log and
  the comment (description) in the `Addr` parameter exist for the address, then the FTN-system name takes precedence.

  To control the wide-screen mode, the `WideScreen` parameter has been added with the following valid values:
  
  - `WideScreen Yes` &ensp;&ndash; enable wide-screen mode with no limit of the length of printed lines;
  
  - `WideScreen No` &emsp;&ndash; disable wide-screen mode; the length of all printed lines will not exceed 80 characters;
  
  - `WideScreen <N>` &ensp;&ndash; enable wide-screen mode and set the length of printed lines to no more than `N` characters (`N` is a positive integer;
  if you set `N` to less than 80, the wide-screen mode will be disabled and length of the lines will be limited to 80 characters).
  
  The default setting is `WideScreen Yes`.

  Also all utilities have the `-w` command line option to control wide-screen mode:

  - `-w`, `-w+` &ndash; as `WideScreen Yes`;

  - `-w-` &emsp;&emsp;&ndash; as `WideScreen No`;
  
  - `-w<N>` &emsp;&ndash; as `WideScreen <N>`.

- On histograms, the load level that is greater than 0% but less than 3% is displayed using the `_` character.
In the older versions, this load level was either displayed excessively large &ndash; as a level of 3-10%, or not displayed at all due to the lack of a suitable pseudo-graphic symbol.

- The order of adresses and groups of addresses in `Addr` parameters is no longer important. Regardless of the order, when generating statistics,
they will be sorted from less general to more general. And the excluded addresses and groups of addresses will be placed at the beginning of the adresses list,
simultaneously deleting all present addresses that match the excluded ones. Re-specifying the same addresses will be ignored. For example, if in the configuration file specified:

	```
	Addr 2:5020/#.#
	Addr 2:5020/378.0
	Addr #:#/#.#
	Addr 2:5020/378.0
	Addr 2:#/#.#
	Addr 2:5020/1132.0
	Addr 2:5020/408.0
	Addr !2:5020/1132.*
	Addr !*:*/*.666
	Addr 2:5020/715.#
	Addr 2:5020/1132.0
	Addr !2:5020/408.0
	```

	then the following address list will be used:

	```
	Addr !2:5020/408.0
	Addr !2:5020/1132.*
	Addr !*:*/*.666
	Addr 2:5020/378.0
	Addr 2:5020/715.#
	Addr 2:5020/#.#
	Addr 2:#/#.#
	Addr #:#/#.#
	```

- If the group of addresses in `Addr` paramter have both macros `#` and wildcards `*`, then wildcards will be automatically replaced with macros.
Wildcards can appear in non-group addresses only. For example, the address list:

	```
	Addr 2:5020/378.*
	Addr 2:*/#.*
	```

  will be transformed to

	```
	Addr 2:5020/378.*
	Addr 2:#/#.#
	```
  and the statistics will contain one common line for all points of node `2:5020/378` and separate lines for other addresses from zone&nbsp;2,
  with which sessions were held.

- New algorithm of browsing of the list of addresses specified by the `Addr` parameters. In the older versions, browsing the address list ended after the first match
with the session address. Now, if the session address is not defined as excluded, the browsing will continue to the end of the list. Such an algorithm takes into account
sessions with a separately specified addresses also in the statistics for the groups to which the addresses belongs. For example, for the address list:

	```
	Addr !2:5020/1132.0
	Addr 2:5020/378.0
	Addr 2:5020/#.#
	Addr 2:#/#.#
	Addr #:#/#.#
	```

	the sessions with address `2:5020/378.0` will be taken into account not only in separate statistics for this address, but also in group statistics of sessions
	with addresses of the net `2:5020`, in group statistics of sessions with addresses of the zone `2` and in group statistics of sessions with any addresses.
	And sessions with address `2:5020/1132.0` will be ignored.

- Fixed processing of addresses excluded from statistics (for example, `Addr !2:5020/1132.0`).
In the older versions, sessions with such addresses were mistakenly printed in sessions table, and always as aborted. Now such addresses are excluded from all types of statistics.

- The `T-Hist` and `LnkStat` utilities assign a duration of 1 second to sessions read from a binary log with a duration of 0 seconds.
This allows you to correctly take into account short sessions lasting less than 1 second in statistics. The zero value of the duration of such sessions
occurs due to the time intervals in binary logs are recorded with an accuracy of a second.

  The `DmpHist` utility continues to work as before. It prints raw data from binary logs without any adjustment.
For sessions shorter than a second, the utility will print a duration of 0 seconds, as recorded in the binary log.

- Format of the sessions table is changed. The session start time is printed with an accuracy of seconds (the older versions print it with an accuracy of minutes).
The session end time is excluded due to redundancy &ndash; the table contains the duration of sessions.

- Aborted Binkd sessions are marked in the sessions table with `A` character after address. The older versions were not place this mark for the aborted Binkd sessions.

- Summary information about traffic and the number and duration of sessions is now printed after the sessions tables and after tables with links and groups statistics.
This is an exact copy of the information that printed after the load graph and histograms.

- The `DmpHist` utility prints the start time of sessions exactly as it is written in the binary log, without any adjustments. The `TimeShift` parameter is ignored.

- The `DmpHist` utility prints before table not only the name of the binary log file, but also information about its format.
And after the tables, `DmpHist` prints a more detailed description of sessions status bits.

- Parameter names and their values in the configuration file are case-insensitive, with the exception of file names in Linux.

- Linux executables with static libraries are compressed by [UPX 4.2.4](https://upx.github.io) executable packer to reduce their size.

## How to add support of a new mailer

To use `T-Hist` with unsupported mailers, you need to convert the mailer logs to one of the binary log formats supported by `T-Hist`.
The easiest way is converting either to binary log of `Binkd` and `T-Mail` before version 2603 (old format)
or to binary log of `T-Mail` since version 2603 (new format). Read more in the file `ADD-NEW-MAILER.md`.

## Gratitudes

I would like to thank Alex Barinov `2:5020/715`, `2:50/0` for testing the new versions of `T-Hist` on its FTN-system.
I also thank everyone whose ideas, suggestions and help in testing allowed me to create and develop this project.
The names of some of these people are listed in the old documentation file.

---

Mikhail Markovskiy (klug), 2:5020/378

https://klug-photo.dreamwidth.org/709558.html

https://klug.livejournal.com/1102966.html
