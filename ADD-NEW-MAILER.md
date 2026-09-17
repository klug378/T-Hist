# How to use T-Hist to generate statistics for unsupported mailers

To use `T-Hist` with unsupported mailers, you need to convert the mailer logs to one of the binary log formats supported by `T-Hist`.
The easiest way is converting either to binary log of `Binkd` and `T-Mail` before version 2603 (old format)
or to binary log of `T-Mail` since version 2603 (new format).

## Old format

The binary log file of `Binkd` consists of 28 byte records. Each record has the following structure:

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

## New format

New format of binary log allows `T-Hist` to generate statistics on protected and unprotected sessions,
as well as print the names of FTN systems or other text information recorded in the binary log.

The first two bytes of the new format binary log contain the signature `0x00F1`. Next are records 100 bytes long.
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
    u8     unused[8];  // not used by T-Hist
    u8     fNamelen;   // length of the string in the following bytes (not more than 63),
                       // or 0 if the record does not contain text information
    char   sName[63];  // array containing a string with the name of FTN system
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
`T-Mail` itself, saving records of 100 bytes, does not use the last 64 bytes and does not save the names of FTN systems.
Therefore, it is possible, while keep compatibility with `T-Mail`, store an additional information in the unused by `T-Mail` bytes.
