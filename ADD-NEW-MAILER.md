[ADD-NEW-MAILER на русском языке](ADD-NEW-MAILER-ru.md)

# How to use T-Hist to generate statistics for unsupported mailers

To use `T-Hist` with unsupported mailers, you need to convert the mailer logs to one of the binary log formats supported by `T-Hist`.
This document reviews the following formats:

  - `Binkd` binary log format, which is also the format of `T-Mail` before version 2603 (`T-Mail` old format);
  - binary log format of `T-Mail` since version 2603 (`T-Mail` new format).
  - native `T-Hist` format of binary log with 64-bit traffic values and the ability to store multiple text strings.

  A comparison of the capabilities provided by each of the formats is given in the Table.
  
|                                                 | `Binkd` format<br>(`T-Mail` old format) | `T-Mail` new format | `T-Hist` format |
| :---------------------------------------------- | :-------------------------------------: | :------------------:| :-------------: |
| Size of one record                              |                 28 bytes                |      100 bytes      |    256 bytes    |
| Ability to store traffic values<br>greater than 4 GiB |            **No**                 |       **No**        |     **Yes**     |
| Mark password protected sessions                |                  **No**                 |       **Yes**       |     **Yes**     |
| Mark aborted sessions     | **Yes**<br>(`Binkd` loss of session<br>direction information) |       **Yes**       |     **Yes**     |
| Ability to store text strings                   |  **No**  | **Yes**<br>(up to 63 characters) | **Yes**<br>(up to 206 characters) |
| Ratio of the size of log file<br>to the size of `Binkd` log file<br>with the same number of records | 1 : 1 | 3.57 : 1 | 9.14 : 1 |

Each of the binary log formats will be reviewed in detail below.

## Binkd binary log format (T-Mail old format)

The `Binkd` binary log file consists of 28 byte records. There is no signature at the beginning of the file.
Each record has the following structure:

```C
struct {
    u16    fZone;      // FTN-address Zone number
    u16    fNet;       // FTN-address Net number
    u16    fNode;      // FTN-address Node number
    u16    fPoint;     // FTN-address Point number
    u32    fSTime;     // Unix-timestamp of sesion start
    u32    fLTime;     // session duration in seconds
    u32    fBReceive;  // number of bytes received
    u32    fBSent;     // number of bytes sent
    u8     fFReceive;  // number of files received (not used by T-Hist)
    u8     fFSent;     // number of files sent (not used by T-Hist)
    u16    fStatus;    // session status
} TS;

// Data types:
//  u8  - unsigned char 8 bit
//  u16 - unsigned int 16 bit
//  u32 - unsigned int 32 bit

// fStatus - session status as a number:
//   1 - successfully outgoing session
//   2 - successfully incoming session
//   3 - any failed session (for example, aborted)
```

The structure of binary log taken from `Binkd` repository on [GitHub](https://github.com/pgul/binkd), file https://github.com/pgul/binkd/blob/master/binlog.c

The session status in the old format does not contain information about whether the session was password protected or not.
Therefore, when processing binary logs of the old format, `T-Hist` does not generate statistics on protected and unprotected sessions.

Below is an example of a dump of `Binkd` binary log file containing one 28 byte record with information about the session with node 2:5020/378.0,
started at 2026-08-06 23:06:43, with a duration of 1 second, during which 6788 bytes were sent in one file. The session was incoming and ended normally.

```
0000000000: 02 00 9C 13 7A 01 00 00 │ 83 13 75 6A 01 00 00 00  ....z.....uj....
0000000010: 00 00 00 00 84 1A 00 00 │ 00 01 02 00              ............
```

The session direction indicated in the status (incoming or outgoing session) for `Binkd` is opposite to that indicated by `T-Mail`.
Therefore, you must use the `SwapInOut Yes` parameter in the configuration file when processing the `Binkd` binary log.
If you set the session status as follows:

```C
// fStatus - session status as a number:
//   1 - successfully incoming session
//   2 - successfully outgoing session
//   3 - any failed session (for example, aborted)
```

then the `SwapInOut Yes` parameter is not needed.

## T-Mail new binary log format

The `T-Mail` new binary log contains more session information than the old format. In particular, the format allows you to mark
whether the session was password protected or not, allowing `T-Hist` to generate statistics on protected and unprotected sessions.
Also, the format allows you to mark aborted sessions without losing information about their direction. Finally, the format allows you
to store text information that `T-Hist` can print in wide-screen mode.

The first two bytes of `T-Mail` new format binary log contain the signature `0x00F1` (or in bytes: `0xF1`, `0x00`).
Next are records 100 bytes long. Each record has the following structure:

```C
struct {
    u16    fZone;      // FTN-address Zone number
    u16    fNet;       // FTN-address Net number
    u16    fNode;      // FTN-address Node number
    u16    fPoint;     // FTN-address Point number
    u32    fSTime;     // Unix-timestamp of sesion start
    u32    fLTime;     // session duration in seconds
    u32    fBReceive;  // number of bytes received
    u32    fBSent;     // number of bytes sent
    u8     fFReceive;  // number of files received (not used by T-Hist)
    u8     fFSent;     // number of files sent (not used by T-Hist)
    u16    fStatus;    // session status
    u8     unused[8];  // not used by T-Hist
    u8     fNamelen;   // length of the string in the following bytes (not more than 63),
                       // or 0 if the record does not contain text information
    char   sName[63];  // array containing a string with information about FTN-system
                       // or other text information
} TS;

// Data types:
//  u8  - unsigned char 8 bit
//  u16 - unsigned int 16 bit
//  u32 - unsigned int 32 bit

// fStatus - session status as a bit set:
//  Bit 0 is set for incoming session
//  Bit 1 is set for outgoing session
//  Bit 2 is set for correctly terminated session (not aborted)
//  Bit 3 is set for password protected session
```

`T-Hist` considers the new format of binary log wider than it is defined in `T-Mail`.
`T-Mail` itself, saving records of 100 bytes, does not use the last 64 bytes and does not save any text strings.
Therefore, it is possible, while keep compatibility with `T-Mail`, store an additional data in the unused by `T-Mail` bytes
(for example, string with information about FTN-system or other text information).

Below is an example of a dump of `T-Mail` new format binary log file containing one record. At the beginning of the file are
two bytes of the signature: `0xF1`, `0x00`. Next is a 100 byte record with information about the session with node 2:5020/378.0,
started at 2026-09-07 00:07:35, with a duration of 1 second, during which 31628 bytes were sent in one file. The session was
incoming, password protected, and ended normally. The record also contains a 34-character text string with the IP address,
the name of FTN-system and its location.

```
0000000000: F1 00 02 00 9C 13 7A 01 │ 00 00 47 00 9E 6A 01 00  ......z...G..j..
0000000010: 00 00 00 00 00 00 8C 7B │ 00 00 00 01 0D 00 00 00  .......{........
0000000020: 00 00 00 00 00 00 22 39 │ 32 2E 33 36 2E 33 37 2E  ......"92.36.37.
0000000030: 32 30 39 3B 20 6B 6C 75 │ 67 3B 20 4D 6F 73 63 6F  209; klug; Mosco
0000000040: 77 2C 20 52 75 73 73 69 │ 61 00 00 00 00 00 00 00  w, Russia.......
0000000050: 00 00 00 00 00 00 00 00 │ 00 00 00 00 00 00 00 00  ................
0000000060: 00 00 00 00 00 00       │                          ......
```

## Native `T-Hist` format

The disadvantage of the binary log formats of all mailers supported by `T-Hist` is that information about traffic is recorded as 32-bit values.
In such binary logs, it is impossible to correctly store information if more than 4 gigabytes have been sent or received.

Formats of the binary logs were developed in the dial-up era, when it was almost impossible to transfer more than 4 gigabytes in one session.
But in the era of Fido-over-IP and high-speed networks, a session with more than 4 gigabytes of traffic is common, especially on large FTN hubs.

Analysis of the `Binkd` code in the [GitHub](https://github.com/pgul/binkd) repository shows that 64-bit versions of `Binkd` process traffic data
using 64-bit variables. But when writing to a binary log, `Binkd` is forced to accept 32-bit format restrictions and discard the higher bytes.

Therefore, the development and use of new binary log formats with 64-bit traffic values is a relevant task.

`T-Hist`, starting with version `1.4.0`, implements support for its own binary log format with 64-bit traffic values,
which is proposed to be used by FTN mailer developers.

The first five bytes of `T-Hist` format binary log contain the signature consisting of four characters `H`, `I`, `S`, `T` and one byte with
the format version number (currently format version is 1). Signature as a sequence of hexadecimal values: `0x48`, `0x49`, `0x53`, `0x54`, `0x01`.
  
Next are records 256 bytes long. Each record has the following structure:

```C
struct {
    uint16_t  Zone;         // FTN-address Zone number
    uint16_t  Net;          // FTN-address Net number
    uint16_t  Node;         // FTN-address Node number
    uint16_t  Point;        // FTN-address Point number
    uint64_t  SessionStart; // Unix-timestamp of sesion start
    uint64_t  BytesRcv;     // number of bytes received
    uint64_t  BytesSnt;     // number of bytes sent
    uint32_t  FilesRcv;     // number of files received (not used by T-Hist)
    uint32_t  FilesSnt;     // number of files sent (not used by T-Hist)
    uint32_t  SessionTime;  // session duration in seconds
    uint16_t  Status;       // session status
    uint8_t   Index[4];     // array of indexes of strings
    char      Strings[206]; // array containing strings
} TH;

// Data types:
//  uint8_t  - unsigned char 8 bit
//  uint16_t - unsigned int 16 bit
//  uint32_t - unsigned int 32 bit
//  uint64_t - unsigned int 64 bit

// Status - session status as a bit set:
//  Bit 0 is set for incoming session
//  Bit 1 is set for outgoing session
//  Bit 2 is set for correctly terminated session (not aborted)
//  Bit 3 is set for password protected session

// Index and Strings arrays allow you to store up to 5 null-terminated
// strings with total size of no more than 206 bytes
//  string 0 - starts with Strings[0]
//  string 1 - starts with Strings[Index[0]] if Index[0] < 206, otherwise absent
//  string 2 - starts with Strings[Index[1]] if Index[1] < 206, otherwise absent
//  string 3 - starts with Strings[Index[2]] if Index[2] < 206, otherwise absent
//  string 4 - starts with Strings[Index[3]] if Index[3] < 206, otherwise absent

```

The strings can contain text information about FTN system. For example, string 0 &ndash; IP address, string 1 &ndash; domain name,
string 2 &ndash; name of FTN system, string 3 &ndash; location of FTN system, string 4 &ndash; sysop name.

Below is an example of a dump of `T-Hist` format binary log file containing one record. At the beginning of the file are
five bytes of the signature: `0x48`, `0x49`, `0x53`, `0x54`, `0x01`. Next is a 256 byte record with information about the session
with node 2:5020/715.0, started at 2026-09-14 22:15:06, with a duration of 4 seconds, during which 2528361 bytes were received
by three files and 336 bytes were sent by one file. The session was ougoing, password protected, and ended normally. 
The record also contains 5 null-terminated strings with IP address, domain name, name of FTN system, location of FTN system and sysop name.

```
0000000000: 48 49 53 54 01 02 00 9C ¦ 13 CB 02 00 00 EA 71 A8  HIST..........q.
0000000010: 6A 00 00 00 00 69 94 26 ¦ 00 00 00 00 00 50 01 00  j....i.&.....P..
0000000020: 00 00 00 00 00 03 00 00 ¦ 00 01 00 00 00 04 00 00  ................
0000000030: 00 0E 00 0F 1F 29 35 39 ¦ 35 2E 31 34 33 2E 31 30  .....)595.143.10
0000000040: 39 2E 31 35 32 00 66 69 ¦ 64 6F 2E 68 75 62 61 37  9.152.fido.huba7
0000000050: 31 35 2E 72 75 00 4E 65 ¦ 77 20 57 6F 72 6C 64 00  15.ru.New World.
0000000060: 4D 6F 73 63 6F 77 2C 55 ¦ 53 53 52 00 41 6C 65 78  Moscow,USSR.Alex
0000000070: 20 42 61 72 69 6E 6F 76 ¦ 00 00 00 00 00 00 00 00   Barinov........
0000000080: 00 00 00 00 00 00 00 00 ¦ 00 00 00 00 00 00 00 00  ................
0000000090: 00 00 00 00 00 00 00 00 ¦ 00 00 00 00 00 00 00 00  ................
00000000A0: 00 00 00 00 00 00 00 00 ¦ 00 00 00 00 00 00 00 00  ................
00000000B0: 00 00 00 00 00 00 00 00 ¦ 00 00 00 00 00 00 00 00  ................
00000000C0: 00 00 00 00 00 00 00 00 ¦ 00 00 00 00 00 00 00 00  ................
00000000D0: 00 00 00 00 00 00 00 00 ¦ 00 00 00 00 00 00 00 00  ................
00000000E0: 00 00 00 00 00 00 00 00 ¦ 00 00 00 00 00 00 00 00  ................
00000000F0: 00 00 00 00 00 00 00 00 ¦ 00 00 00 00 00 00 00 00  ................
0000000100: 00 00 00 00 00          ¦                          .....
```

As a drawback of `T-Hist` format, a noticeable increase in the size of the binary log file should be noted:
  - more than 2 and a half times compared to `T-Mail` new format (256 bytes per record versus 100 bytes per record);
  - more than 9 times compared to `Binkd` format (256 bytes per record versus 28 bytes per record).

---

Mikhail Markovskiy (klug), 2:5020/378
