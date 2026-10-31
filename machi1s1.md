# ステージ1 かわに はしを かけよう

### @unifiedToolbox true
### @explicitHints true

## ブロックを ならべよう

「**ボタンを おしたら**」の なかに、エージェントの ブロックを ならべよう。

かんせいしたら、▶ を おしてから、スタートの まえの **スイッチ** を **みぎクリック** しよう。スタートするよ！

かわの むこうの きんいろの ブロックまで いこう。
``||agent:おく||`` ブロックの むきを **した** に すると、エージェントの したに ブロックを おくよ。

## ヒント

まずは……

**まえに 1 ブロック いどう** して、**したに おく**。
これで はしが 1つ できるよ。かわは 4 マス あるね。

```blocks
meiro.onButton(function () {
    agent.move(SixDirection.Forward, 1)
    agent.place(DOWN)
})
```

## こたえ

せいかいは……

1. 「まえに 1 いどう → したに おく」を **4かい** くりかえす
2. さいごに **まえに 1 いどう** して むこうぎしへ

だよ。

```blocks
meiro.onButton(function () {
    for (let index = 0; index < 4; index++) {
        agent.move(SixDirection.Forward, 1)
        agent.place(DOWN)
    }
    agent.move(SixDirection.Forward, 1)
})
```

```template
meiro.onButton(function () {
})
```

```ghost
agent.move(SixDirection.Forward, 1)
agent.move(SixDirection.Up, 1)
agent.turn(TurnDirection.Right)
agent.turn(TurnDirection.Left)
agent.place(DOWN)
agent.till(FORWARD)
for (let index = 0; index < 4; index++) {
}
```

```customts
/**
 * まちを つくろう ようの ブロック
 * ワールドは あいずを ここに おく：
 *   0 97 0 ダイヤモンド … スイッチ（エージェントは ワールドが スタートへ もどしずみ）
 *   0 97 1 エメラルド   … スタートへ もどした あいず（むきだけ なおす）
 * エージェントには まるいし（COBBLESTONE）を もたせる。うごいている あいだは へったら たす。
 */
//% color="#2a9d4a" weight=200 block="まち"
namespace meiro {
    function faceNorth() {
        agent.teleport(agent.getPosition(), CompassDirection.North)
    }

    /**
     * スタートの スイッチを おしたときに うごく
     */
    //% block="ボタンを おしたら"
    export function onButton(handler: () => void) {
        player.execute("function kw/mc_ready")
        faceNorth()
        let busy = false
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
                agent.setItem(COBBLESTONE, 64, 1)
                agent.setSlot(1)
                player.execute("function kw/mc_go")
                busy = true
                handler()
                busy = false
                player.execute("function kw/mc_done")
            }
            n++
            if (n % 100 == 0) player.execute("function kw/mc_beat")
            loops.pause(100)
        })
        loops.forever(function () {
            if (busy && agent.getItemCount(1) < 32) {
                agent.setItem(COBBLESTONE, 64, 1)
            }
            loops.pause(500)
        })
    }
}
```
