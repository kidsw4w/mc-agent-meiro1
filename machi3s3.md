# ステージ3 はたけを つくろう

### @unifiedToolbox true
### @explicitHints true

## ブロックを ならべよう

「**ボタンを おしたら**」の なかに、エージェントの ブロックを ならべよう。

かんせいしたら、▶ を おしてから、スタートの まえの **スイッチ** を **みぎクリック** しよう。スタートするよ！

みずの まわりの つちを ぐるっと たがやそう。
``||agent:たがやす||`` ブロックは、エージェントの **まえ** の つちを はたけに するよ。

## ヒント

まずは……

「たがやす → まえに 1 いどう」を **2かい** して、**みぎを むく** よ。
これで 1つの へんが できるよ。

```blocks
meiro.onButton(function () {
    for (let index = 0; index < 2; index++) {
        agent.till(FORWARD)
        agent.move(SixDirection.Forward, 1)
    }
    agent.turn(TurnDirection.Right)
})
```

## こたえ

せいかいは……

ヒントの ブロックを **4かい** くりかえす！

しかくは 4つの へんで できているね。

```blocks
meiro.onButton(function () {
    for (let index2 = 0; index2 < 4; index2++) {
        for (let index = 0; index < 2; index++) {
            agent.till(FORWARD)
            agent.move(SixDirection.Forward, 1)
        }
        agent.turn(TurnDirection.Right)
    }
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
 * エージェントには スタートの たびに まるいし（COBBLESTONE）を 64こ もたせる。
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
                agent.setItem(COBBLESTONE, 64, 1)
                agent.setSlot(1)
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
