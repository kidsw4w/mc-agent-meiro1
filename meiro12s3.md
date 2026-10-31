# ステージ3 かんあつばんで おじゃまいしを けそう

### @unifiedToolbox true
### @explicitHints true

## ブロックを ならべよう

「**ボタンを おしたら**」の なかに、エージェントの ブロックを ならべよう。

かんせいしたら、▶ を おしてから、スタートの まえの **スイッチ** を **みぎクリック** しよう。スタートするよ！

おじゃまいしが みちを ふさいでいるよ。
エージェントが **かんあつばん** の うえを とおると、おじゃまいしが きえるよ。

**まえに 3 ブロック** すすむ ところが たくさん あるよ。**くりかえし** も つかってみよう！

## ヒント 1

まずは……

**まえに 3 ブロック いどう** を **2かい くりかえし** て、**みぎを むく** よ。

```blocks
meiro.onButton(function () {
    for (let index = 0; index < 2; index++) {
        agent.move(SixDirection.Forward, 3)
    }
    agent.turn(TurnDirection.Right)
})
```

## ヒント 2

つぎは……

**まえに 3 ブロック いどう** して **みぎを むく**。
もう いちど **まえに 3 ブロック いどう** すると、とちゅうで かんあつばんの うえを とおるよ！

```blocks
meiro.onButton(function () {
    for (let index = 0; index < 2; index++) {
        agent.move(SixDirection.Forward, 3)
    }
    agent.turn(TurnDirection.Right)
    agent.move(SixDirection.Forward, 3)
    agent.turn(TurnDirection.Right)
    agent.move(SixDirection.Forward, 3)
})
```

## こたえ

せいかいは……

1. まえに **3** ブロック いどう を **2かい** くりかえす
2. **みぎ** を むく
3. まえに **3** ブロック いどう
4. **みぎ** を むく
5. まえに **3** ブロック いどう（とちゅうで かんあつばん）
6. **みぎ** を むく
7. まえに **3** ブロック いどう を **2かい** くりかえす

だよ。

```blocks
meiro.onButton(function () {
    for (let index = 0; index < 2; index++) {
        agent.move(SixDirection.Forward, 3)
    }
    agent.turn(TurnDirection.Right)
    agent.move(SixDirection.Forward, 3)
    agent.turn(TurnDirection.Right)
    agent.move(SixDirection.Forward, 3)
    agent.turn(TurnDirection.Right)
    for (let index = 0; index < 2; index++) {
        agent.move(SixDirection.Forward, 3)
    }
})
```

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
