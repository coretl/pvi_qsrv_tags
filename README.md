# pvi_qsrv_tags

How we might to pvi tags in databases to serve the structure over PVA

## To run
Do `softIocPVA -d test.db` then in another terminal:
```
# pvget PANDAQSRV:PVI
PANDAQSRV:PVI structure 
    structure record
        structure _options
            uint queueSize 0
            boolean atomic true
    structure pvi
        structure pulse1
            string d PANDAQSRV:PULSE1:PVI
        structure seq1
            string d PANDAQSRV:SEQ1:PVI
```
This says that PandA has 2 blocks, PULSE1 and SEQ1, with their pvs.

```
# pvget PANDAQSRV:PULSE1:PVI
PANDAQSRV:PULSE1:PVI structure 
    structure record
        structure _options
            uint queueSize 0
            boolean atomic true
    structure pvi
        structure delay
            string rw PANDAQSRV:PULSE1:DELAY
        structure width
            string rw PANDAQSRV:PULSE1:WIDTH
```
Getting the first, we see the Pulse block has 2 read-write fields.

```
# pvget PANDAQSRV:SEQ1:PVI
PANDAQSRV:SEQ1:PVI structure 
    structure record
        structure _options
            uint queueSize 0
            boolean atomic true
    structure pvi
        structure table
            string rw PANDAQSRV:SEQ1:TABLE
```
Getting the second we see a single read-write field.

```
# pvget -v PANDAQSRV:SEQ1:TABLE
PANDAQSRV:SEQ1:TABLE epics:nt/NTTable:1.0 
    structure record
        structure _options
            uint queueSize 0
            boolean atomic true
    string[] labels [Repeats, Trigger, Position, Time1, OutA1, OutB1, OutC1, OutD1, OutE1, OutF1, Time2, OutA2, OutB2, OutC2, OutD2, OutE2, OutF2]
    alarm_t alarm INVALID DRIVER UDF 
        int severity 3
        int status 2
        string message UDF
    time_t timeStamp <undefined>              
        long secondsPastEpoch 631152000
        int nanoseconds 0
        int userTag 0            
    structure value
        ushort[] repeats [1,1,1,32]
        string[] trigger [POSA>=POSITION, POSA<=POSITION, Immediate, Immediate]
        int[] position [3222,-565,0,0]
        uint[] time1 [5,0,10,10]
        ubyte[] outa1 [1,0,0,1]
        ubyte[] outb1 [0,0,1,1]
        ubyte[] outc1 [0,1,1,0]
        ubyte[] outd1 [1,1,0,1]
        ubyte[] oute1 [1,0,1,0]
        ubyte[] outf1 [1,0,0,0]
        uint[] time2 [0,10,10,11]
        ubyte[] outa2 [1,0,0,1]
        ubyte[] outb2 [0,0,1,1]
        ubyte[] outc2 [0,1,1,0]
        ubyte[] outd2 [1,1,0,1]
        ubyte[] oute2 [1,0,1,0]
        ubyte[] outf2 [1,0,0,0]
```
Getting this field we see an NTTable field. Note that the labels don't match the value structure order at the moment, this will change after https://github.com/epics-base/pva2pva/issues/53

## On the other side

- Get the `pvi` substructure (there's a `record` element at the top level)
- For each k, v in it:
  - If it has an `x` it is an `EpicsSignalX` with `value` of the default record type
  - If it has an `r` and a `w` or an `rw` it is an `EpicsSignalRW`
  - If it has only an `r` it is an `EpicsSignalR`
  - If it has only a `w` it is an `EpicsSignalW`