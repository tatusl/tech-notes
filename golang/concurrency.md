# Concurrency
## Goroutines

- Goroutine is a function that runs independently of the function that started it
- Goroutine can be any function that's called after special keyword `go`
- Frequently used to run function in the "background" while the main part does something else
## Channels

- Pipeline for sending and receiving data
- Channels allows one goroutine to send structured data to another goroutine
- Channels can be used to block the main goroutine until all other goroutines finish their execution
- Like network socket, they can be unidirectional or bidirectional
- Can be short-live or long-lived
- Declared with the keyword `chan` along with a data type
- Default value of a channel is `nil`, so value needs to be assigned
	- Value can be assigned with the `make` function
- Writing and reading from channel is done with arrow syntax, where the arrow points the direction
- Sending and reading are blocking operations, meaning
	- when data is sent to channel using a goroutine, it will be blocked until data is consumed by another goroutine
	- when receiving data from channel using goroutine, it will be blocked until the data is available in the channel
- To avoid deadlocks, sender can close a channel. This means channel can't be communicated over
- Buffered channels can be used to create channels which don't block until channel capacity is exceeded
	- For example: `make(chan int, 100)`
## Waitgroups

- Waitgroup allows to block a specific code block to allow a set of of goroutines to complete execution
- The following are the main methods of waitgroups
	- `add` - waitgroup act as counter holding the number of goroutines or functions to wait for. When counter is 0. the waitgroup releases the goroutines
	- `wait` - blocks the execution of the application until the waitgroup counter becomes 0
	- `done` - decreases the waitgroup counter by a value of 1

## References

- [https://blog.knoldus.com/achieving-concurrency-in-go/](https://blog.knoldus.com/achieving-concurrency-in-go/)
- [https://www.scaler.com/topics/golang/waitgroup-in-golang/](https://www.scaler.com/topics/golang/waitgroup-in-golang/)
