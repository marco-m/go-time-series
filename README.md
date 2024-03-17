# go-time-series

[![Go Reference](https://pkg.go.dev/badge/github.com/marco-m/go-time-series.svg)](https://pkg.go.dev/github.com/marco-m/go-time-series)
[![Build Status](https://github.com/marco-m/go-time-series/actions/workflows/ci.yaml/badge.svg?branch=master)](https://github.com/marco-m/go-time-series/actions/)

Time series implementation in Go.

**NOTE**: this is a fork of [codesuki/go-time-series](https://github.com/codesuki/go-time-series).

The time series supports storing counts at different granularities, e.g. seconds, minutes, hours, ...

* Simple interface
* Store time series data at different granularities
* Use your own clock implementation, e.g. for testing or similar

## Examples

### Creating a time series with default settings
The default settings use `time.Now()` as clock and `time.Second * 60`, `time.Minute * 60` and `time.Hour * 24` as granularities.

```go
import "github.com/marco-m/go-time-series"

...

ts, err := timeseries.NewTimeSeries()
if err != nil {
    // handle error
}
```

### Creating a customized time series
You can specify the clock and/or granularities to use. A clock must implement the `timeseries.Clock` interface.

```go
import "github.com/marco-m/go-time-series"

...
type clock struct {}
func (c *clock) Now() {
    return time.Time{} // always returns the zero time
}
var myClock clock
...

ts, err := timeseries.NewTimeSeries(
    timeseries.WithGranularities(
        []timeseries.Granularity{
            {Granularity: time.Second, Count: 60},
            {Granularity: time.Minute, Count: 60},
            {Granularity: time.Hour, Count: 24},
            {Granularity: time.Hour * 24, Count: 7},
        }),
    timeseries.WithClock(&myClock),
)
if err != nil {
    // handle error
}
```

### Filling the time series
To fill the time series with counts, e.g. events, you can use two different functions.

```go
import "github.com/marco-m/go-time-series"

...

ts, err := timeseries.NewTimeSeries()
if err != nil {
    // handle error
}

ts.Increase(2) // adds 2 to the counter at the current time
ts.IncreaseAtTime(3, time.Now().Add(-2 * time.Minute)) // adds 3 to the counter 2 minutes ago
```

### Querying the time series
The `Range()` function takes 2 arguments, i.e. the start and end of a time span.
`Recent()` is a small helper function that just uses `clock.Now()` as `end` in `Range`.
Please refer to the [documentation](https://pkg.go.dev/github.com/marco-m/go-time-series) for how `Range()` works exactly. There are some details depending on what range you query and what range is available.

```go
import "github.com/marco-m/go-time-series"

...

ts, err := timeseries.NewTimeSeries()
if err != nil {
    // handle error
}

ts.Increase(2) // adds 2 to the counter at the current time
// 1s passes
ts.Increase(3)
// 1s passes

ts.Recent(5 * time.Second) // returns 5

ts.Range(time.Now().Add(-5 * time.Second), time.Now()) // returns 5
```

## Documentation

See the [![Go Reference](https://pkg.go.dev/badge/github.com/marco-m/go-time-series.svg)](https://pkg.go.dev/github.com/marco-m/go-time-series)

## License

go-time-series is [MIT licensed](./LICENSE).
