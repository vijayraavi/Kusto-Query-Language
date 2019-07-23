# percentile_tdigest()

Calculates the percentile result from tdigest results (which was generated by [tdigest](tdigest-aggfunction.md) or [merge-tdigests](merge-tdigests-aggfunction.md))

**Syntax**

`percentile_tdigest(`*Expr*`,` *Percentile1* [`,` *typeLiteral*]`)`

`percentiles_array_tdigest(`*Expr*`,` *Percentile1* [`,` *Percentile2*] ...[`,` *PercentileN*]`)`

`percentiles_array_tdigest(`*Expr*`,` *Dynamic array*`)`

**Arguments**

* *Expr*: Expression which was generated by [tdigest](tdigest-aggfunction.md) or [merge_tdigests](merge-tdigests-aggfunction.md)
* *Percentile* is a double constant that specifies the percentile.
* *typeLiteral*: An optional type literal (e.g., `typeof(long)`). If provided, the result set will be of this type. 
* *Dynamic array*: list of percentiles in a dynamic array of integer or floating point numbers

**Returns**

The percentiles/percentilesw value of each value in *Expr*.

**Tips**

1) The function must receive at least one percent (and maybe more, see the syntax above: *Percentile1* [`,` *Percentile2*] ...[`,` *PercentileN*]) and the result will be
  a dynamic array which includes the results. (such like [`percentiles()`](percentiles-aggfunction.md))
  
2) if only one percent was provided and the type was provided also then the result will be a column of the same type provided with the results of that percent (all tdigests must be of that type in this case).

3) if *Expr* includes tdigests of different types, then don't provide the type and the result will be of type dynamic. (see examples below).

**Examples**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```
StormEvents
| summarize tdigestRes = tdigest(DamageProperty) by State
| project percentile_tdigest(tdigestRes, 100, typeof(int))
```

|percentile_tdigest_tdigestRes|
|---|
|0|
|62000000|
|110000000|
|1200000|
|250000|


<!-- csl: https://help.kusto.windows.net:443/Samples -->
```
StormEvents
| summarize tdigestRes = tdigest(DamageProperty) by State
| project percentiles_array_tdigest(tdigestRes, range(0, 100, 50), typeof(int))
```

|percentile_tdigest_tdigestRes|
|---|
|[0,0,0]|
|[0,0,62000000]|
|[0,0,110000000]|
|[0,0,1200000]|
|[0,0,250000]|


<!-- csl: https://help.kusto.windows.net:443/Samples -->
```
StormEvents
| summarize tdigestRes = tdigest(DamageProperty) by State
| union (StormEvents | summarize tdigestRes = tdigest(EndTime) by State)
| project percentile_tdigest(tdigestRes, 100)
```

|percentile_tdigest_tdigestRes|
|---|
|[0]|
|[62000000]|
|["2007-12-20T11:30:00.0000000Z"]|
|["2007-12-31T23:59:00.0000000Z"]|