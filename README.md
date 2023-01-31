# pvi_qsrv_tags

How we might to pvi tags in databases to serve the structure over PVA

## To run
Do `softIocPVA -d test.db` then in another terminal:

```
# pvget EXAMPLE:PVI
EXAMPLE:PVI structure 
    structure record
        structure _options
            uint queueSize 0
            boolean atomic true
    structure pvi
        structure Go
            string x EXAMPLE:Go
        structure Position
            string r EXAMPLE:Position_RBV
            string w EXAMPLE:Position
        structure Record
            string rw EXAMPLE:Record
```

## On the other side

- Get the `pvi` substructure (there's a `record` element at the top level)
- For each k, v in it:
  - If it has an `x` it is an `EpicsSignalX` with `value` of the default record type
  - If it has an `r` and a `w` or an `rw` it is an `EpicsSignalRW`
  - If it has only an `r` it is an `EpicsSignalR`
  - If it has only a `w` it is an `EpicsSignalW`