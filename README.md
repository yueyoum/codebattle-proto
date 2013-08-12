#Codebattle Proto

*   [codebattle-server][20]
*   [codebattle-proto][21]
*   [codebattle-ai][22]
*   [codebattle-client][23]


CodeBattle 的网络通信协议 使用了 [google protubuf][1].

AI 与 Server 进行交互就是通过 `api.proto` 中定义的message进行的。
所以无论你使用什么语言，只要按照这些message encode, decode 数据，就可进行操作了。

现在有提供的 [AI例子][2] 供你参考，如果没有你喜欢的语言，欢迎发送pull request进行添加。 你在自己写encode, decode数据的时候只要关注 `api.proto`, `marine.proto` 即可。

## 发送数据 [api.proto -> Cmd]

所有的操作指令都是先填充Cmd message，然后将打包后的数据发送到server的。

    message Cmd {
        required CmdEnum cmd = 1;
        optional JoinRoom jrm = 2;
        optional CreateMarine cme = 3;
        optional MarineOperate opt = 4;
    }

cmd 为必填域，表明你要进行的是什么操作。
下面三个 optional 域只用填充你当前进行的操作即可。

比如，你要 加入一个房间，那么 填充 cmd 为 CmdEnum.joinroom，并且同时将 JoinRoom 的数据 填充到 jrm 域。

**注意**： 因为游戏模式为joinroom后自动创建marine，所以 CmdEnum.createmarine 不可用。

## 接收数据 [api.proto -> Message]

服务器会给你返回 Message 消息，

    message Message {
        required MessageEnum msg = 1;
        optional CmdResponse response = 2;
        optional SenceUpdate update = 3;
        optional EndBattle endbattle = 4;
    }

同样， 返回的Message第一项 msg 也用来表示这个消息的类型。

*   MessageEnum.cmdresponse 
    
    你发给server的所有Cmd的回应。
    除过MarineOperate没发生过错误的情况，因为marine操作会通过SenceUpdate返回。

*   MessageEnum.senceupdate

    整个场景中Marines的任何变动都会通过这个消息发送给你。而你的AI正是通过分析/计算这里面的数据，然后来决定如何操作自己Marine进行行动的。

*   MessageEnum.startbattle

    收到此消息表明，所有玩家都已经加入房间，可以开始对战

*   MessageEnum.endbattle

    战斗正常结束，或者某玩家失去连接。


## 游戏规则

上面只是数据的基本定义。玩法规则在 [这里][2]

[1]: https://developers.google.com/protocol-buffers/docs/overview
[2]: https://github.com/yueyoum/codebattle-ai


[20]: https://github.com/yueyoum/codebattle-server
[21]: https://github.com/yueyoum/codebattle-proto
[22]: https://github.com/yueyoum/codebattle-ai
[23]: https://github.com/yueyoum/codebattle-client