# NordShifts forecast ledger

Each line of `chain.csv` is one issue day of NordShifts' frozen day-ahead electricity price forecasts:

- `digest`: SHA-256 of every forecast row issued that day (model, zone, issue time, hour, p10, p50, p90)
- `chain`: SHA-256 of the previous day's chain value followed by this day's digest

The file is rewritten and committed every night by the NordShifts server. Git history is the outside record:
a past line that changed would show in the diff of that commit. To check the chain itself:

```python
import csv, hashlib
chain = ""
for row in csv.DictReader(open("chain.csv")):
    chain = hashlib.sha256((chain + row["digest"]).encode()).hexdigest()
    assert chain == row["chain"], row["day"]
print("chain consistent, head", chain)
```

If you saved a chain value from https://nordshifts.com/en/forecast-value earlier, it must still appear in
`chain.csv` on the same day. `stamps/*.ots` are OpenTimestamps proofs (verify with `ots verify`).

Research data, not investment advice.
