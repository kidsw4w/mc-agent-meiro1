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
 * めいろ ようの ブロック（さいしょうげん）
 * ざひょう・いどう・メッセージは ぜんぶ ワールドがわ（kw/mc_* の ファンクション）で おこなう。
 * ワールドは あいずを ここに おく：
 *   0 97 0 ダイヤモンド … ボタン（エージェントは ワールドが スタートへ もどしずみ）
 *   0 97 1 エメラルド   … スタートへ もどした あいず（むきだけ なおす）
 */
//% color="#2a9d4a" weight=200 block="めいろ"
namespace meiro {
    function faceNorth() {
        agent.teleport(agent.getPosition(), CompassDirection.North)
    }

    /**
     * めいろの ボタンを おしたときに うごく
     */
    //% block="ボタンを おしたら"
    export function onButton(handler: () => void) {
        player.execute("function kw/mc_ready")
        faceNorth()
        let n = 0
        loops.forever(function () {
            const reset = world(0, 97, 1)
            if (blocks.testForBlock(EMERALD_BLOCK, reset)) {
                blocks.place(STONE, reset)
                faceNorth()
                player.execute("function kw/mc_reset")
            }
            const go = world(0, 97, 0)
            if (blocks.testForBlock(DIAMOND_BLOCK, go)) {
                blocks.place(STONE, go)
                faceNorth()
                player.execute("function kw/mc_go")
                handler()
                player.execute("function kw/mc_done")
            }
            n++
            if (n % 100 == 0) player.execute("function kw/mc_beat")
            loops.pause(100)
        })
    }
}
```
