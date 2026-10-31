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
    const LAMP_X = [0, 40, 80, 120, 160]   // スタートの てまえの ランプ (y=101, z=1)
    const START_X = [0, 40, 80, 120, 160]   // スタート (y=101, z=0) きた むき
    const LIT_LAMP = 124                    // lit_redstone_lamp

    /**
     * めいろの ボタンを おしたときに うごく
     */
    //% block="ボタンを おしたら"
    export function onButton(h: () => void) {
        handler = h
    }

    loops.forever(function () {
        for (let i = 0; i < LAMP_X.length; i++) {
            const lamp = world(LAMP_X[i], 101, 1)
            if (blocks.testForBlock(LIT_LAMP, lamp)) {
                // ボタンが もどるまで まつ
                while (blocks.testForBlock(LIT_LAMP, lamp)) loops.pause(100)
                agent.teleport(world(START_X[i], 101, 0), CompassDirection.North)
                if (handler) handler()
            }
        }
        loops.pause(100)
    })
}
```
