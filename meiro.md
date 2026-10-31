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
    // スタート (y=101, z=0) は きた むき。ボタンを おすと、ワールドが
    // しまの なか (y=98, z=1) に ダイヤモンドブロックの あいずを おく
    const START_X = [0, 40, 80, 120, 160]

    /**
     * めいろの ボタンを おしたときに うごく
     */
    //% block="ボタンを おしたら"
    export function onButton(h: () => void) {
        handler = h
    }

    loops.forever(function () {
        for (let i = 0; i < START_X.length; i++) {
            const signal = world(START_X[i], 98, 1)
            if (blocks.testForBlock(DIAMOND_BLOCK, signal)) {
                blocks.place(DIRT, signal)          // あいずを けす
                agent.teleport(world(START_X[i], 101, 0), CompassDirection.North)
                if (handler) handler()
            }
        }
        loops.pause(100)
    })
}
```
