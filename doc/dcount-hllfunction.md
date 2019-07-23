# dcount_hll()

Calculates the dcount from hll results (which was generated by [hll](hll-aggfunction.md) or [hll_merge](hll-merge-aggfunction.md)).

Read about the [underlying algorithm (*H*yper*L*og*L*og) and estimation accuracy](dcount-aggfunction.md#estimation-accuracy).

**Syntax**

`dcount_hll(`*Expr*`)`

**Arguments**

* *Expr*: Expression which was generated by [hll](hll-aggfunction.md) or [hll-merge](hll-merge-aggfunction.md)

**Returns**

The distinct count of each value in *Expr*

**Examples**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```
StormEvents
| summarize hllRes = hll(DamageProperty) by bin(StartTime,10m)
| summarize hllMerged = hll_merge(hllRes)
| project dcount_hll(hllMerged)
```

|dcount_hll_hllMerged|
|---|
|315|