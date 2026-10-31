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
    /**
     * めいろの ボタンを おしたときに うごく
     * （プログラムの じゅんばんに えいきょう されないよう、ぜんぶ この なかで じゅんびする）
     */
    //% block="ボタンを おしたら"
    export function onButton(handler: () => void) {
        // スタート (y=101, z=0) は きた むき。しまの なか (y=98) に ワールドが あいずを おく
        //   z=1 の ダイヤモンド … ボタン（スタートに もどして プログラムを うごかす）
        //   z=2 の エメラルド   … スタートに もどすだけ（ステージ かいし・あんないにん）
        const xs = [0, 40, 80, 120, 160]
        const debug = true   // false に すると チャットの デバッグ じょうほうが きえる
        player.say("[MakeCode] めいろ プログラム ver.7 じゅんび かいし")

        if (debug) {
            const gold = blocks.testForBlock(GOLD_BLOCK, world(0, 100, -3))
            player.say("[MakeCode] テスト: ステージ1の きんブロック → " + (gold ? "みつけた (OK)" : "みつからない (NG)"))
        }

        // ▶ を おしたら、いま いる しまの スタートに エージェントを よぶ
        let here = Math.round(player.position().toWorld().getValue(Axis.X) / 40)
        if (here < 0) here = 0
        if (here > 4) here = 4
        agent.teleport(world(xs[here], 101, 0), CompassDirection.North)
        if (debug) {
            const p = agent.getPosition().toWorld()
            player.say("[MakeCode] エージェントを ステージ" + (here + 1) + "の スタートへ: "
                + p.getValue(Axis.X) + " " + p.getValue(Axis.Y) + " " + p.getValue(Axis.Z))
        }

        let n = 0
        loops.forever(function () {
            n++
            if (debug && n % 100 == 1) player.say("[MakeCode] あいずを みはり ちゅう (" + n + ")")
            for (let i = 0; i < xs.length; i++) {
                const reset = world(xs[i], 98, 2)
                if (blocks.testForBlock(EMERALD_BLOCK, reset)) {
                    blocks.place(DIRT, reset)
                    if (debug) player.say("[MakeCode] ステージ" + (i + 1) + ": エメラルドの あいずを うけとった")
                    agent.teleport(world(xs[i], 101, 0), CompassDirection.North)
                }
                const go = world(xs[i], 98, 1)
                if (blocks.testForBlock(DIAMOND_BLOCK, go)) {
                    blocks.place(DIRT, go)
                    if (debug) player.say("[MakeCode] ステージ" + (i + 1) + ": ダイヤの あいずを うけとった → プログラム スタート")
                    agent.teleport(world(xs[i], 101, 0), CompassDirection.North)
                    handler()
                    if (debug) player.say("[MakeCode] プログラム おわり")
                }
            }
            loops.pause(100)
        })
    }
}
```
