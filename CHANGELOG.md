# Changelog of new T-Hist and related utilities

#### Terms used in this document

- The term `old version` means `T-Hist` `0.30.alpha7` from 2003, and earlier versions.

- The term `parameter` means parameter in the configuration file.

- The terms `group`, `group of addresses` means an address defined in `Addr` parameter with at least one `#` character (macro).
Addresses that use the wildcards `*` but do not contain `#` are not groups.

- The terms `binary log` and `history file` are equivalent.

## [1.3.1] - 2026-09-16

### Changed

- Minor changes in formatting of statistics headers.

## [1.3.0] - 2026-09-14

### Added

- To control the wide-screen mode introduced in version `1.2.0`, the `WideScreen` parameter has been added with the following valid values:
  
  - `WideScreen Yes` &ensp;&ndash; enable wide-screen mode with no limit of the length of printed lines;
  
  - `WideScreen No` &emsp;&ndash; disable wide-screen mode; the length of all printed lines will not exceed 80 characters;
  
  - `WideScreen <N>` &ensp;&ndash; enable wide-screen mode and set the length of printed lines to no more than `N` characters (`N` is a positive integer;
  if you set `N` to less than 80, the wide-screen mode will be disabled and length of the lines will be limited to 80 characters).
  
  The default setting is `WideScreen Yes`.

- Extended valid values of the `-w` command line option:

  - `-w`, `-w+` &ndash; as `WideScreen Yes`;

  - `-w-` &emsp;&emsp;&ndash; as `WideScreen No`;
  
  - `-w<N>` &emsp;&ndash; as `WideScreen <N>`.

### Fixed

- Fixed an error in the average CPS calculating in `AdvancedCPS Yes` mode, leading to abnormally high values
in cases where the total duration of empty sessions exceeded the total duration of sessions with traffic.

### Changed

- Both `AdvancedCPS` and `BusyHist` parameters are now set to `Yes` by default. (Previously, the default value was `No`.)

## [1.2.2] - 2026-09-10

### Fixed

- Fixed incorrect calculation of the average CPS for traffic values exceeding 4 gigabytes.

### Changed

- When printing traffic values in whole kilobytes, megabytes, or gigabytes, rounding to the nearest integer is performed.

## [1.2.1] - 2026-09-09

### Fixed

- Fixed incorrect printing of traffic values exceeding 4 gigabytes.

- Corrected minor errors in the design of the displayed information.

### Changed

- Minor changes in the numbers output formats.

## [1.2.0] - 2026-09-01

### Added

- All utilities now have a wide-screen mode that allows you to display lines longer than 80 characters. This mode is enabled by default,
however, if you process the old-format binary logs (for example, Binkd logs) and do not use comments (descriptions) in the `Addr` parameters,
then the generated statistics will still be no more than 80 characters wide.

  But if at least one of the conditions is met:
  
  - the binary log contains information about the names of FTN systems
  (such binary logs have, for example, Internet Rex, FrontDoor, Bink/+, FhMail, BinkleyTerm-XE);
  
  - at least one of the parameters `Addr` has a comment (description),
  
  the FTN-system names (from binary log) or comments (from `Addr`  parameters) will be printed in wide-screen mode
  to the right of the load graph and to the right of the tables with statistics. If both the FTN-system name from the binary log and
  the comment (description) in the `Addr` parameter exist for the address, then the FTN-system name takes precedence.

- All utilities now have the `-w` command line option to control wide-screen mode:

  - `-w`, `-w+` &ndash; enable wide-screen mode (default);

  - `-w-` &emsp;&emsp;&ndash; disable wide-screen mode.

- Summary information about traffic and the number and duration of sessions is now printed after the sessions tables.
This is an exact copy of the information printed after the load graph and histograms and after the tables with links and groups statistics.

- The `DmpHist` utility print before table not only the name of the binary log file, but also information about its format.

### Changed

- Length of the comment (description) in the `Addr` parameters is not limited now. In previous versions, the comment was printed
to the left of the load graph (if there is a dividing lines enabled by `MiddLine` parameters) with a length of no more than 15 characters.
Now the comments (descriptions) of addresses are printed (only in the wide screen mode) to the right of the load graph
and to the right of the tables with statistics, where there is no length limit.

- If the group of addresses in `Addr` paramter have both macros `#` and wildcards `*`, then wildcards will be automatically replaced with macros.
Wildcards can now appear in non-group addresses only. For example, the address list:

	```
	Addr 2:5020/378.*
	Addr 2:*/#.*
	```

  will be transformed to

	```
	Addr 2:5020/378.*
	Addr 2:#/#.#
	```
  and the statistics will contain one common line for all points of node `2:5020/378` and separate lines for other addresses from zone 2,
  with which sessions were held.

- Minor changes in the design of tables with statistics and other displayed information.

## [1.1.0] - 2026-08-27

### Added

- On histograms, the load level that is greater than 0% but less than 3% is now displayed using the `_` character.
Previously, due to the lack of a suitable pseudo-graphic symbol, this load level was either displayed excessively large - as a level of 3-10%,
or not displayed at all.

- Summary information about traffic and the number and duration of sessions is now printed after the tables with links and groups statistics.
This is an exact copy of the information that printed after the load graph and histograms.

- Quick guide "How to use T-Hist to generate statistics for unsupported mailers" is created (see the `ADD-NEW-MAILER.md` file).

### Fixed

- Completely fixed an issue when sessions with addresses not specified in the `Addr` parameters were mistakenly print in sessions table and always have "aborted" mark.
In version `1.0.3`, this issue was fixed partially: only for explicitly excluded addresses. Now the sessions table contains only those addresses that correspond to the `Addr` parameters.

- Fixed the ignoring of the `ProtectSummary Yes` parameter. This parameter is used to print summary information about password-protected and unprotected sessions
for the mailers that write the mark of a password session in the binary log.

- Fixed an issue during binary log truncation (if the `CutHistory` parameter is specified) when the 0-second duration sessions were recorded with a duration of 1 second.

- Restored support for the mailers Internet Rex, FrontDoor, BinkleyTerm-XE, The Brake!, which was broken in versions `1.0.0` &ndash; `1.0.4`.

### Changed

- The order of adresses and groups of addresses in `Addr` parameters is no longer important. Regardless of the order, when generating statistics,
they will be sorted from less general to more general. And the excluded addresses and groups of addresses will be placed at the beginning of the adresses list,
simultaneously deleting all present addresses that match the excluded ones. Re-specifying the same addresses will be ignored.
For a more detailed description of the address sorting rules, see the `README.md` file.

- The algorithm of browsing of the list of addresses specified by the `Addr` parameters has been changed. Previously, browsing the address list ended after the first match
with the session address. Now, if the session address is not defined as excluded, the browsing will continue to the end of the list.
Such an algorithm takes into account sessions with a separately specified addresses also in the statistics for the groups to which the addresses belongs.
For a more detailed description of the address list browsing algorithm, see the `README.md` file.

- The `DmpHist` utility now ignores the `TimeShift` parameter and prints the start time of sessions exactly as it is written in the binary log, without any adjustments.

## [1.0.4] - 2026-08-21

### Changed

- Minor resizing the columns width of the links and groups tables.

- The `DmpHist` utility now print a more detailed description of sessions status bits at the end of the table.

## [1.0.3] - 2026-08-18

### Fixed

- Fixed processing of addresses excluded from statistics (specified with the character `!`, for example `Addr !2: 5020/378.1`).
Previously, sessions with such addresses were mistakenly printed in sessions table, and always as aborted. Now such addresses are excluded from all types of statistics.

### Changed

- Changing the format of the sessions tables. Now the session start time is printed with an accuracy of seconds (previously there was an accuracy of minutes).
The session end time is excluded due to redundancy &ndash; statistics show the duration of sessions.

- Aborted Binkd sessions now are marked in the sessions table with `A` character after address. Previously, they were not marked.

- Improved algorithm for calculating average CPS when specifying `AdvancedCPS Yes` in the configuration file.

## [1.0.2] - 2026-08-14

### Fixed

- Fixed incorrect handling of `ShowValue` parameter and `-s` command line option.

### Changed

- Minor changing of the diagnostic messages printed to `stderr`.

## [1.0.1] - 2026-08-13

### Fixed

- Fixed truncation of binary logs (if the `CutHistory` parameter is specified).

## [1.0.0] - 2026-08-12

First new version. Added Linux support. Switch to UTF-8 encoding.

**Attention!** This version truncate the binary logs incorrectly (if the `CutHistory` parameter is specified).

### Added

- Added Linux (x86_64 and i686) and Windows (x86_64 and i686) support.
Executable files for each of the Linux architectures are compiled in two variants: with dynamic and with static libraries.
Variants with static libraries are further compressed by [UPX 4.2.4](https://upx.github.io) executable packer to reduce their size.

- `LnkStat` and `DmpHist` utilities (but not `T-Hist`) now have the `-u` command line option to select the encoding of the printed information:

  - `-u`, `-u+` &ndash; use UTF-8 encoding (default);

  - `-u-` &emsp;&emsp;&ndash; use ASCII 7-bit encoding without pseudo-graphics.

### Changed

- All utilities now print information and create output text files in UTF-8 encoding.
To post statistics in FTN echo conferences or netmail, text files must be converted to the appropriate encoding. For example, for Russian-language echo conferences with CP866 encoding:

	```shell
	iconv -f UTF-8 -t CP866 in.txt -o out.txt
	```

- The `T-Hist` and `LnkStat` utilities assign a duration of 1 second to sessions read from a binary log with a duration of 0 seconds.
This allows you to correctly take into account short sessions lasting less than 1 second in statistics. The zero value of the duration of such sessions
occurs due to the time intervals in binary logs are recorded with an accuracy of a second.

  The `DmpHist` utility continues to work as before. It prints raw data from binary logs without any adjustment.
For sessions shorter than a second, the utility will print a duration of 0 seconds, as recorded in the binary log.

- The `NoDrawZero` parameter also affects the load histograms (previously this parameter only affected load graph).
Now the busy histogram always corresponds to the "summation" of all load graph lines.


### Removed

- DOS and OS/2 support removed. 

