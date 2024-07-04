# Bugreport

The implementation of the write method is missing a boolean return value indicating the need of `drain` https://github.com/CodeGenieApp/serverless-express/blob/mainline/src/response.js.

See https://nodejs.org/api/stream.html#writablewritechunk-encoding-callback

'The return value is true if the internal buffer is less than the highWaterMark configured when the stream was created after admitting chunk. If false is returned, further attempts to write data to the stream should stop until the 'drain' event is emitted.'

With newer versions of trpc this leads to a hang. The trpc middleware interprets void as false and waits for the 'drain' event that is never showing up. The lambda runtime is shutting down the event handling with an internal server error.

See https://github.com/trpc/trpc/blob/fbb4a2fb4b71d2f5ab7ee97617d951f3a92517c8/packages/server/src/adapters/node-http/nodeHTTPRequestHandler.ts#L85

In response.js the write method should return true.

See `writable.spec.ts`in this repo for a testcase.
