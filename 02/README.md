Repro for https://github.com/mfridman/tparse/issues/66

tparse version: v0.10.3 (latest at time of writing)

When a Go module has two packages, one of which has zero tests, and go test is run with the all paths option `./...`, then tparse completely hides the existence of the package without tests.

Plain `go test`:

```
$ go test -count=1 ./...
ok  	github.com/marco-m/tparse-bugs/02/a	0.136s
?   	github.com/marco-m/tparse-bugs/02/b	[no test files]   <= b is visible
```

Piping this to tparse:

```
$ go test -count=1 ./... -json | tparse
┌───────────────────────────────────────────────────────────────────────────────────────┐
│  STATUS │ ELAPSED │               PACKAGE               │ COVER │ PASS │ FAIL │ SKIP  │
│─────────┼─────────┼─────────────────────────────────────┼───────┼──────┼──────┼───────│
│  PASS   │ 0.19s   │ github.com/marco-m/tparse-bugs/02/a │ 0.0%  │    1 │    0 │    0  │
└───────────────────────────────────────────────────────────────────────────────────────┘
```

On the other hand, tparse is able to report correctly that package `b` has zero tests when `b` is passed explicitly:

```
$ go test -count=1 ./b -json -cover | tparse
┌───────────────────────────────────────────────────────────────────────────────────────┐
│  STATUS │ ELAPSED │               PACKAGE               │ COVER │ PASS │ FAIL │ SKIP  │
│─────────┼─────────┼─────────────────────────────────────┼───────┼──────┼──────┼───────│
│  NOTEST │ 0.00s   │ github.com/marco-m/tparse-bugs/02/b │ --    │ --   │ --   │ --    │
│         │         │ [no test files]                     │       │      │      │       │
└───────────────────────────────────────────────────────────────────────────────────────┘
```