The Eyevinn Channel Engine is an NPM library that provides the functionality to generate "fake" live HLS stream by stitching HLS VOD's together. The library is provided as open source and this repository includes a basic reference implementation as a guide on how the library can be used.

## Usage

To use this library in your NodeJS project download and install the library in your project by running the following in your project folder.

```
$ npm install --save eyevinn-channel-engine
```

And to run the basic reference implementation included in this repository you run:

```
$ npm start
```

Then point your HLS video player to `http://localhost:8000/live/master.m3u8?channel=1` to start playing the linear live stream.

## Master manifest filtering

The engine supports a very simplistic and basic filtering of media playlists included in the master manifest. Currently supports to filter on video bandwidth and video height. To specify a filter provide the query parameter `filter` when loading the master manifest, e.g. `(type=="video"&&height>200)&&(type=="video"&&height<400)`. This needs to be URL encoded resulting in the following URL: `http://localhost:8000/live/master.m3u8?channel=1&filter=%28type%3D%3D%22video%22%26%26height%3E200%29%26%26%28type%3D%3D%22video%22%26%26height%3C400%29`

## API

Initiate and start the engine as below.

```
const ChannelEngine = require('eyevinn-channel-engine');

const engine = new ChannelEngine(myAssetManager, { channelManager: myChannelManager });
engine.start();
engine.listen(process.env.port || 8000);
```

where `myAssetManager` and `myChannelManager` are classes implementing the interfaces below.

```
class MyAssetManager {
  getNextVod({ sessionId, category, playlistId }) -> { id, title, uri, offset, timedMetadata? }
  handleError(err, vodResponse)
}

class MyChannelManager {
  getChannels() -> [ { id, name, slate?, closedCaptions?, profile?, options? } ]
}
```

Find a simplistic reference implementation for guidance in `./server.js`.

### Options

Available options when constructing the Channel Engine object are:

- `defaultSlateUri`: URI to an HLS VOD that can be inserted when a VOD for some reason cannot be loaded.
- `slateRepetitions`: Number of times the slate should be repeated.
- `redisUrl`: A Redis DB URL for storing states that can be shared between nodes.
- `heartbeat`: Path for heartbeat requests
- `channelManager`: A reference to a channel manager object.
- `cacheTTL`: Sets the cache-control header TTL. Default is 4 sec.
- `playheadDiffThreshold`: Sets the threshold when starting to adjust tick interval to compensate for playhead drift.
- `maxTickInterval`: The maximum interval for playhead tick interval. Default is 10000 ms.
- `cloudWatchMetrics`: Output CloudWatch JSON metrics on console log. Default is false.

## Demo

Visit [Consuo](https://consuo.tv/) for a demonstration of this concept. A demo and proof-of-concept built on top of this library.

If you want help to get started to build a service of your own based on this library you can hire an [Eyevinn Video-Dev Team](https://video-dev.team) to help you out.

## About Eyevinn Technology

Eyevinn Technology is an independent consultant firm specialized in video and streaming. Independent in a way that we are not commercially tied to any platform or technology vendor.

At Eyevinn, every software developer consultant has a dedicated budget reserved for open source development and contribution to the open source community. This give us room for innovation, team building and personal competence development. And also gives us as a company a way to contribute back to the open source community. 

Want to know more about Eyevinn and how it is to work here. Contact us at work@eyevinn.se!
