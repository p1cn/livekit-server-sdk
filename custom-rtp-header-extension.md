发送自定义 RTP Header Extension 的方法

当前 SDK 支持自定义的 Header Extension 的版本 `v2.3.2` 以上。

```
go get github.com/p1cn/livekit-server-sdk/v2@v2.3.2
```

# Step 1
在加入房间时，通过 `WithPublisherAudioRTPHeaderExtensions` 和 `WithSubscriberAudioRTPHeaderExtensions` 指定要自定义的 Header Extension URI.

```golang
room, err := lksdk.ConnectToRoomWithToken(
	config.Get().Service.LiveKit.Conn.Url,
	token,
	roomCallback,
	lksdk.WithAutoSubscribe(true),
	lksdk.WithPublisherAudioRTPHeaderExtensions([]string{seqid.URI, receiverid.URI, scene.URI}),
	lksdk.WithSubscriberAudioRTPHeaderExtensions([]string{seqid.URI, receiverid.URI, scene.URI}),
)
```

# Step 2
在自定义的 Provider 中生成 `media.Sample` 时，通过 `sample.Metadata` 字段来设置 Header Extension 的值。

```golang
media.Sample{
    Data:     data,
    Duration: duration,
    Metadata: &rtpext.Metadata{
    	Extensions: []rtpext.HeaderExtension{
    		sceneext.NewSceneIDExtension(1001),
    		seqidext.NewSeqIDExtension(1002),
    		recvidext.NewReceiverIDExtension(1003),
    	},
    },
}
```

注意：`Sample` 的定义如下，其中 Metadata 为 interface{}。

```golang
type Sample struct {
	Data               []byte
	Timestamp          time.Time
	Duration           time.Duration
	PacketTimestamp    uint32
	PrevDroppedPackets uint16
	Metadata           interface{}
}
```
如果要设置自定义 Header Extension，一定要使用 `*rtpext.Metadata` 类型
