[BINKD-PATCH на русском языке](BINKD-PATCH-ru.md)

# Adding support for new binary log formats to Binkd by patching `binlog.c` file from Binkd source

It is possible to add support for new binary log formats to Binkd by patching file https://github.com/pgul/binkd/blob/master/binlog.c
from Binkd repository on [GitHub](https://github.com/pgul/binkd). At the same time, the functioning of the native Binkd binary log specified by
`binlog` parameter will not be affected.

In the sample of Binkd configuration file, there are commented lines next to the `binlog` parameter:
```
# Uncomment if you want FrontDoor-style binary log
#
#fdinhist in.his
#fdouthist out.his
```

These parameters are usually not used, so you can change the code in the `binlog.c` file so that the log specified by
the `fdouthist`/`fdinhist` parameters is written in a different format with extended capabilities.

The [binkd_patch](../binkd_patch) directory of `T-Hist` repository contains two patches for `binlog.c`:

  - [binlog-tmail.patch](binlog-tmail.patch) &ndash; use T-Mail new format for `fdouthist`/`fdinhist` binary log;
  - [binlog-thist.patch](binlog-thist.patch) &ndash; use native `T-Hist` format for `fdouthist`/`fdinhist` binary log.

Both of these formats have advanced capabilities to compare with the Binkd binary log format. In particular, the formats allows you to mark
whether the session was password protected or not, allowing `T-Hist` to generate statistics on protected and unprotected sessions.
Also, the formats allows you to mark aborted sessions without losing information about their direction. Finally, the formats allows you
to store text information that `T-Hist` can print in wide-screen mode.
For more information on this binary log formats, see [ADD-NEW-MAILER.md](../ADD-NEW-MAILER.md).

Patches only modify the `binlog.c` file and do not affect any other Binkd source files. With the patches applied,
Binkd successfully compiles for Linux. The ability to compile for other systems has not been tested.

### Select the patch

Use a patch with the native `T-Hist` format if:

  - you have sessions with more than 4 gigabytes sent or received (native `T-Hist` format has 64-bit values for incoming and outgoing traffic);

  - you want to see more text information about FTN systems that `T-Hist` can printed in wide-screen mode.

Use a patch with the T-Mail new format if:

  - you need no more than 63 characters to display information about FTN systems, or you disable wide-screen mode in `T-Hist`;

  - you want to have a smaller binary log than log in the `T-Hist` format.

### Using the patch

1. Clone the Binkd repo:

    `git clone https://github.com/pgul/binkd`

2. Change into the new binkd source directory:

    `cd binkd`
	
3. Copy the selected patch here.

4. Apply the patch

    `git apply binlog-thist.patch`

    or

    `git apply binlog-tmail.patch`
	
5. Compile Binkd. To compile under Linux, run:

    `cp mkfls/unix/* .`

    `./configure`

    `make`

    To compile for other systems, see the instructions in the Binkd repository.
	
6. In the Binkd configuration file, uncomment one of the two parameters `fdouthist`/`fdinhist` (no matter which one)
and specify the file name of the new binary log. If both parameters are uncomment, then the file name from `fdouthist` will be used.

    Now, if you have not commented out the `binlog` parameter, Binkd will write the session history to both the native binary log and the new one.
	
7. Specify a new binary log in the `T-Hist` configuration file to create statistics on it.
