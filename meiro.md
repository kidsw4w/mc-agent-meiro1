# めいろで エージェント

### @unifiedToolbox true
### @explicitHints true

## めいろを クリアしよう

「**ボタンを おしたら**」の なかに、エージェントの ブロックを ならべよう。

- ``||agent:まえに すすむ||``
- ``||agent:みぎ・ひだりを むく||``

ならべたら ▶ を おしてから、めいろの **ボタン** を おしてね。
エージェントが スタートに もどって、きみの プログラムの とおりに うごくよ！

```template
meiro.onButton(function () {
})
```

```ghost
agent.move(SixDirection.Forward, 1)
agent.turn(TurnDirection.Right)
agent.turn(TurnDirection.Left)
for (let index = 0; index < 4; index++) {
}
```

```customts
/**
 * めいろ ようの ブロック
 */
//% color="#2a9d4a" weight=200 block="めいろ"
namespace meiro {
    let handler: () => void = null
    // スタート (y=101, z=0) は きた むき。しまの なか (y=98) に ワールドが あいずを おく
    //   z=1 の ダイヤモンド … ボタン（スタートに もどして プログラムを うごかす）
    //   z=2 の エメラルド   … スタートに もどすだけ（ステージ かいし・あんないにん）
    const START_X = [0, 40, 80, 120, 160]

    /**
     * めいろの ボタンを おしたときに うごく
     */
    //% block="ボタンを おしたら"
    export function onButton(h: () => void) {
        handler = h
    }

    function toStart(i: number) {
        agent.teleport(world(START_X[i], 101, 0), CompassDirection.North)
    }

    // ▶ を おしたら、いま いる しまの スタートに エージェントを よぶ
    player.say("めいろ プログラム ver.5")
    let here = Math.round(player.position().toWorld().getValue(Axis.X) / 40)
    if (here < 0) here = 0
    if (here > START_X.length - 1) here = START_X.length - 1
    toStart(here)

    loops.forever(function () {
        for (let i = 0; i < START_X.length; i++) {
            const reset = world(START_X[i], 98, 2)
            if (blocks.testForBlock(EMERALD_BLOCK, reset)) {
                blocks.place(DIRT, reset)
                toStart(i)
            }
            const go = world(START_X[i], 98, 1)
            if (blocks.testForBlock(DIAMOND_BLOCK, go)) {
                blocks.place(DIRT, go)
                toStart(i)
                if (handler) handler()
            }
        }
        loops.pause(100)
    })
}
```
