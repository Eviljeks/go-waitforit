# Installtion
`go get github.com/Eviljeks/go-waitforit`
# Usage
Simple wait-for-it functionality. You can use it in this way:
```go
package somepkg

import (
	"context"
	"strings"
	"time"

	"github.com/streadway/amqp"
	"github.com/Eviljeks/go-waitforit/waitforit"
)

func connect(ctx context.Context, connString string, tick time.Duration, numTicks uint8) (*amqp.Connection, error) {
	var conn *amqp.Connection

	err := waitforit.NewWaiter(tick, numTicks).Wait(ctx, func(ctx context.Context) error {
		var err error
		conn, err = amqp.Dial(connString)
		if err != nil {
			if strings.Contains(err.Error(), "dial tcp") {
				return waitforit.ErrNotReadyYet
			}

			return err
		}

		if err != nil {
			return err
		}
		return nil
	})
	if err != nil {
		return nil, err
	}

	return conn, nil
}

// 10 ticks each of 1 second duration
func AMQPConnect(amqpURL string) error {
    return Connect(ctx, amqpURL, time.Second, uint8(10))
}
```

# TODO
- Tests
- Additional ticks strategies - non linear approach, such as exponential, etc.
- Better README.md