# telemetry

A simple golang telemetry library

## Getting Started

```go
func main() {
	ctx, cancel := signal.NotifyContext(context.Background(), os.Interrupt)
	defer cancel()

	shutdown, err := telemetry.InstrumentAll(ctx, os.Getenv("OTEL_EXPORTER_OTLP_ENDPOINT"))
	log := telemetry.DefaultLogger()
	if errors.Is(err, telemetry.NoEndpointError) {
		log.Error(err, "skipping instrumentation")
		err = nil
	} else {
		defer shutdown(context.Background())
	}
	assert(err)

	log.Info("staring up", "name", telemetry.NAME, "version", telemetry.VERSION)

	ctx = telemetry.ContextWithLogger(ctx)

	<-ctx.Done()
	cancel()
}

func assert(err error) {
	if err == nil {
		return
	}

	log := telemetry.DefaultLogger()

	log.Error(err, "assertion failed")
	os.Exit(1)
}
```
