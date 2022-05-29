Repro for https://github.com/mfridman/tparse/issues/54

The examples below assume the bash shell (or a similar enough shell)

With the code in this repo,

`go test` by itself returns with a non-zero status code:

```
$ go test -count=1  ./... ; echo status code: $?
# github.com/marco-m/tparse-bugs [github.com/marco-m/tparse-bugs.test]
./a_test.go:6:2: undefined: hello
FAIL    github.com/marco-m/tparse-bugs [build failed]
ok      github.com/marco-m/tparse-bugs/b        0.107s
FAIL
status code: 2
```

Piping the command to tparse as shown in the tparse README will hide the error by returning a zero status code:

```
$ go test -count=1  ./... -json | tparse  ; echo status code: $?
# github.com/marco-m/tparse-bugs [github.com/marco-m/tparse-bugs.test]
./a_test.go:6:2: undefined: hello

+--------+---------+----------------------------------+-------+------+------+------+
| STATUS | ELAPSED |             PACKAGE              | COVER | PASS | FAIL | SKIP |
+--------+---------+----------------------------------+-------+------+------+------+
| PASS   | 0.11s   | github.com/marco-m/tparse-bugs/b | 0.0%  |    1 |    0 |    0 |
+--------+---------+----------------------------------+-------+------+------+------+
status code: 0
```

While explicitly setting pipefail will report the error in the status code:

```
$ set -o pipefail && go test -count=1  ./... -json | tparse  ; echo status code: $?
# github.com/marco-m/tparse-bugs [github.com/marco-m/tparse-bugs.test]
./a_test.go:6:2: undefined: hello

+--------+---------+----------------------------------+-------+------+------+------+
| STATUS | ELAPSED |             PACKAGE              | COVER | PASS | FAIL | SKIP |
+--------+---------+----------------------------------+-------+------+------+------+
| PASS   | 0.13s   | github.com/marco-m/tparse-bugs/b | 0.0%  |    1 |    0 |    0 |
+--------+---------+----------------------------------+-------+------+------+------+
status code: 2
```
