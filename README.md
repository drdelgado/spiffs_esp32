# SPIFFS with File Example

## summary

Will load the file stored inside the `storage` directory into the esp32 and print the contents to stdout

## pre-reqs

- esp-idf 3.3.1

## how to setup

```bash
$ git submodule update --recursive --init
cd mkspiffs
make dist BUILD_CONFIG_NAME="-esp-idf" CPPFLAGS="-DSPIFFS_OBJ_META_LEN=4"
```

Check that `SPIFFS_OBJ_META_LEN`:

```bash
$ ./mkspiffs --version
mkspiffs ver. 0.2.3-6-g983970e
Build configuration name: custom
SPIFFS ver. 0.3.7-5-gf5e26c4
Extra build flags: -DSPIFFS_OBJ_META_LEN=4
SPIFFS configuration:
  SPIFFS_OBJ_NAME_LEN: 32
  SPIFFS_OBJ_META_LEN: 4
  SPIFFS_USE_MAGIC: 1
  SPIFFS_USE_MAGIC_LENGTH: 1
  SPIFFS_ALIGNED_OBJECT_INDEX_TABLES: 0
```

your sdkconfig must contain the following, diff yours against this:

```c
#define poop
```

## how to run

Put some file inside `storage` or use the sweet one provided `grassfed.txt` you'll need to modify the source file to point to the correct file otherwise.


```bash
$ mkspiffs/mkspiffs -c storage -b 4096 -p 256 -s 0x100000 spiffs.bin
/grassfed.txt
```

Next run the esptool.py with the newly spiffs.bin file

```bash
$ esptool.py --chip esp32 --port /dev/ttyUSB0  write_flash -z 0x110000 spiffs.bin
esptool.py v2.8
Serial port /dev/ttyUSB0
Connecting.....
Chip is ESP32D0WDQ6 (revision 1)
Features: WiFi, BT, Dual Core, Coding Scheme None
Crystal is 40MHz
MAC: 30:ae:a4:24:9f:ac
Uploading stub...
Running stub...
Stub running...
Configuring flash size...
Auto-detected Flash size: 4MB
Compressed 1048576 bytes to 2417...
Wrote 1048576 bytes (2417 compressed) at 0x00110000 in 0.2 seconds (effective 39179.5 kbit/s)...
Hash of data verified.

Leaving...
Hard resetting via RTS pin...
```

then flash the code onto the board:

```bash
make flash monitor
```

The results should look similar to:

```bash
I (0) cpu_start: Starting scheduler on APP CPU.
I (307) example: Initializing SPIFFS
I (407) example: Partition size: total: 956561, used: 1255
I (407) example: size of file: 828
I (417) example: File contents:

I (417) example: 


           .        .
           \'.____.'/
          __'-.  .-'__                         .--.
          '_i:'oo':i_'---...____...----i"""-.-'.-"\\
            /._  _.\       :       /   '._   ;/    ;'-._
           (  o  o  )       '-.__.'       '. '.     '-."
            '-.__.-' _.--.                 '-.:
             : '-'  /     ;   _..--,  /       ;
             :      '-._.-'  ;     ; :       :
              :  `      .'    '-._.' :      /
               \  :    /    ____....--\    :
                '._\  :"""""    '.     !.   :
                 : |: :           'www'| \ '|
                 | || |              : |  | :
                 | || |             .' !  | |
                .' !| |            /__I   | |
               /__I.' !                  .' !
                  /__I                  /__I   moo



I (487) example: SPIFFS unmounted
```

the incorrect result looks like the following:

```bash
I (306) cpu_start: Starting scheduler on PRO CPU.
I (0) cpu_start: Starting scheduler on APP CPU.
I (307) example: Initializing SPIFFS
I (407) example: Partition size: total: 956561, used: 1255
I (407) example: size of file: 828
E (417) example: Error reading file I/O error
```