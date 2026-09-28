# 制作例の参考コード

この資料は、参加者の希望に合った新しいゲームを作るための技術参照です。画像のbase64とゲームエンジン本体を除き、見本の設定・アプリ側コードをまとめています。コードや素材の再利用は可能ですが、見本と同じ世界・機能数・設定項目だけに制作を限定しません。

これは実行用パッケージではありません。`shell.html` の置換用プレースホルダー、元の相対import、画像名などは見本の組み立て構造を示します。この文書をそのままCanvasへ貼るだけでは動きません。作るゲームに合わせて画像・ライブラリー・HTML要素を接続してください。子どもにファイルの分割・ビルドを求める資料ではありません。

使用エンジンは Phaser（MIT）。ライブラリー本体と既存のライセンス全文・著作権表示は、対応する制作例HTMLに内包されています。ライブラリーを再利用する際は通知を維持してください。画像本体はHTMLの中にあり、この資料に画像を取得できたという保証は含みません。アプリ側コード・画像に新しい包括ライセンスを付けたものではありません。

見本の入力チェックや固定の個数は、その見本用です。新しい機能や構成を作るときは関連するルール・描画・検証を合わせて変更できます。

## game-settings.json

```json
{
  "version": 1,
  "identity": {
    "id": "moonlit-template",
    "title": "月灯りの庭",
    "subtitle": "MOONLIT GARDEN",
    "wordmark": "MOONLIT GARDEN",
    "edition": "A LITTLE JOURNEY THROUGH THE NIGHT",
    "kicker": "A SMALL FOX. A QUIET NIGHT.",
    "introLines": [
      "もう一度、跳べる。",
      "その先に、小さな光がある。"
    ],
    "startLabel": "庭へ、出かける",
    "note": "空中でもう一度。光を集めて、月の門へ。",
    "portraitCaption": "THE LIGHT KEEPER"
  },
  "copy": {
    "pauseMessage": "庭は、このまま待っています。",
    "endKicker": "THE GARDEN AWAKENS",
    "endTitle": "夜に、光が戻った。",
    "endAll": "すべての月のかけらが、庭を照らしています。",
    "endPartial": "まだ見つけていない光が、どこかで待っています。",
    "endNoCollectibles": "小さな冒険、おかえりなさい。",
    "replayLabel": "もう一度、庭へ",
    "shardName": "月のかけら",
    "moteName": "集めた光",
    "footerNote": "光のかけらは、寄り道に。",
    "startHint": "← → で移動。スペースでジャンプ。",
    "hitHint": "赤いトゲに触れた。灯りから、もう一度。",
    "fallHint": "大丈夫。集めた光は、そのまま。",
    "checkpointHint": "灯りがともった。ここから再開できる。"
  },
  "theme": {
    "cream": "#f8e7ba",
    "gold": "#eec478",
    "muted": "#8dabae",
    "night": "#08161d",
    "heroGlow": "#b8f3d5",
    "shardGlow": "#ffd393",
    "mote": "#a4ffda",
    "overlay": "#06252c",
    "overlayAlpha": 0.22
  },
  "images": {
    "background": null,
    "hero": null,
    "props": null
  },
  "hero": {
    "displayWidth": 112,
    "originX": 0.65,
    "faces": "right",
    "frames": null,
    "animationFps": 11
  },
  "propsFrames": null,
  "background": {
    "scrollFactor": 0.047
  },
  "physics": {
    "speed": 330,
    "acceleration": 2600,
    "braking": 3100,
    "gravity": 1600,
    "jumpSpeed": 675,
    "airJumpSpeed": 650,
    "releaseSpeed": 265,
    "maxFallSpeed": 1050,
    "coyoteSeconds": 0.105,
    "jumpBufferSeconds": 0.13,
    "bodyWidth": 32,
    "bodyHeight": 44,
    "respawnDelayMs": 580,
    "invincibleSeconds": 1.5
  },
  "audio": {
    "volume": 0.32,
    "autoStart": true
  },
  "level": {
    "width": 7700,
    "spawn": {
      "x": 160,
      "y": 550
    },
    "goal": {
      "x": 7380,
      "y": 565
    },
    "platforms": [
      {
        "x": -60,
        "y": 590,
        "w": 880
      },
      {
        "x": 940,
        "y": 555,
        "w": 400
      },
      {
        "x": 1510,
        "y": 510,
        "w": 390
      },
      {
        "x": 2050,
        "y": 590,
        "w": 700
      },
      {
        "x": 2860,
        "y": 525,
        "w": 210,
        "move": {
          "axis": "x",
          "range": 75,
          "period": 4
        }
      },
      {
        "x": 3290,
        "y": 450,
        "w": 390
      },
      {
        "x": 3900,
        "y": 580,
        "w": 550
      },
      {
        "x": 4620,
        "y": 510,
        "w": 240
      },
      {
        "x": 5070,
        "y": 450,
        "w": 320
      },
      {
        "x": 5570,
        "y": 475,
        "w": 200,
        "move": {
          "axis": "y",
          "range": 60,
          "period": 3.8
        }
      },
      {
        "x": 5940,
        "y": 570,
        "w": 620
      },
      {
        "x": 6750,
        "y": 565,
        "w": 900
      },
      {
        "x": 510,
        "y": 410,
        "w": 180,
        "upper": true
      },
      {
        "x": 1050,
        "y": 330,
        "w": 200,
        "upper": true
      },
      {
        "x": 1645,
        "y": 285,
        "w": 190,
        "upper": true
      },
      {
        "x": 2380,
        "y": 370,
        "w": 190,
        "upper": true
      },
      {
        "x": 4160,
        "y": 350,
        "w": 190,
        "upper": true
      },
      {
        "x": 5250,
        "y": 235,
        "w": 190,
        "upper": true
      }
    ],
    "shards": [
      {
        "x": 600,
        "y": 365
      },
      {
        "x": 1150,
        "y": 285
      },
      {
        "x": 1740,
        "y": 240
      },
      {
        "x": 2475,
        "y": 325
      },
      {
        "x": 4255,
        "y": 305
      },
      {
        "x": 5345,
        "y": 190
      }
    ],
    "checkpoints": [
      {
        "x": 2200,
        "y": 590
      },
      {
        "x": 4040,
        "y": 580
      },
      {
        "x": 6100,
        "y": 570
      }
    ],
    "enemies": [
      {
        "x": 1190,
        "y": 555,
        "min": 1100,
        "max": 1270,
        "speed": 57
      },
      {
        "x": 1710,
        "y": 510,
        "min": 1605,
        "max": 1810,
        "speed": 72
      },
      {
        "x": 2490,
        "y": 590,
        "min": 2370,
        "max": 2610,
        "speed": 68
      },
      {
        "x": 3470,
        "y": 450,
        "min": 3380,
        "max": 3590,
        "speed": 82
      },
      {
        "x": 5170,
        "y": 450,
        "min": 5140,
        "max": 5310,
        "speed": 65
      },
      {
        "x": 6400,
        "y": 570,
        "min": 6280,
        "max": 6480,
        "speed": 84
      },
      {
        "x": 7080,
        "y": 565,
        "min": 6950,
        "max": 7220,
        "speed": 74
      }
    ],
    "motes": [
      {
        "x": 345,
        "y": 545
      },
      {
        "x": 402,
        "y": 545
      },
      {
        "x": 459,
        "y": 545
      },
      {
        "x": 516,
        "y": 545
      },
      {
        "x": 573,
        "y": 545
      },
      {
        "x": 807,
        "y": 465
      },
      {
        "x": 853,
        "y": 423.98780669118025
      },
      {
        "x": 899,
        "y": 407
      },
      {
        "x": 945,
        "y": 423.98780669118025
      },
      {
        "x": 991,
        "y": 465
      },
      {
        "x": 1350,
        "y": 440
      },
      {
        "x": 1394,
        "y": 410.301515190165
      },
      {
        "x": 1438,
        "y": 398
      },
      {
        "x": 1482,
        "y": 410.301515190165
      },
      {
        "x": 1526,
        "y": 440
      },
      {
        "x": 2780,
        "y": 440
      },
      {
        "x": 2837,
        "y": 404.6446609406726
      },
      {
        "x": 2894,
        "y": 390
      },
      {
        "x": 2951,
        "y": 404.6446609406726
      },
      {
        "x": 3008,
        "y": 440
      },
      {
        "x": 3660,
        "y": 390
      },
      {
        "x": 3722,
        "y": 353.2304473782995
      },
      {
        "x": 3784,
        "y": 338
      },
      {
        "x": 3846,
        "y": 353.2304473782995
      },
      {
        "x": 3908,
        "y": 390
      },
      {
        "x": 4450,
        "y": 460
      },
      {
        "x": 4496,
        "y": 419.6949134723668
      },
      {
        "x": 4542,
        "y": 403
      },
      {
        "x": 4588,
        "y": 419.6949134723668
      },
      {
        "x": 4634,
        "y": 460
      },
      {
        "x": 4880,
        "y": 383
      },
      {
        "x": 4924,
        "y": 351.8873016277919
      },
      {
        "x": 4968,
        "y": 339
      },
      {
        "x": 5012,
        "y": 351.8873016277919
      },
      {
        "x": 5056,
        "y": 383
      },
      {
        "x": 5380,
        "y": 352
      },
      {
        "x": 5426,
        "y": 326.5441558772843
      },
      {
        "x": 5472,
        "y": 316
      },
      {
        "x": 5518,
        "y": 326.5441558772843
      },
      {
        "x": 5564,
        "y": 352
      },
      {
        "x": 6550,
        "y": 466
      },
      {
        "x": 6605,
        "y": 434.8873016277919
      },
      {
        "x": 6660,
        "y": 422
      },
      {
        "x": 6715,
        "y": 434.8873016277919
      },
      {
        "x": 6770,
        "y": 466
      },
      {
        "x": 7280,
        "y": 516
      },
      {
        "x": 7325,
        "y": 516
      },
      {
        "x": 7370,
        "y": 516
      },
      {
        "x": 7415,
        "y": 516
      },
      {
        "x": 7460,
        "y": 516
      }
    ],
    "fallY": 820,
    "chapters": [
      {
        "x": 0,
        "title": "01 / 月の庭"
      },
      {
        "x": 2200,
        "title": "02 / 風渡りの小径"
      },
      {
        "x": 4040,
        "title": "03 / 月へ続く道"
      }
    ],
    "hints": [
      {
        "x": 490,
        "y": 630,
        "text": "空中でも、もう一度。",
        "size": 18,
        "color": "#bed8cf"
      },
      {
        "x": 1202,
        "y": 612,
        "text": "赤いトゲには気をつけて",
        "size": 15,
        "color": "#e0b8a8"
      },
      {
        "x": 2535,
        "y": 651,
        "text": "金色の足場は、ゆっくり動く。",
        "size": 16,
        "color": "#c7d7bc"
      }
    ],
    "notices": [
      {
        "x": 720,
        "text": "スペースを離して、もう一度。空中でも跳べる。",
        "seconds": 4
      },
      {
        "x": 2750,
        "text": "動く足場を見て、飛び移ろう。",
        "seconds": 3.4
      }
    ]
  }
}
```

## atlas.json

```json
{
  "fox": [
    {
      "x": 28,
      "y": 87,
      "w": 387,
      "h": 263
    },
    {
      "x": 471,
      "y": 115,
      "w": 388,
      "h": 236
    },
    {
      "x": 912,
      "y": 93,
      "w": 389,
      "h": 258
    },
    {
      "x": 1360,
      "y": 90,
      "w": 389,
      "h": 234
    },
    {
      "x": 32,
      "y": 524,
      "w": 368,
      "h": 282
    },
    {
      "x": 477,
      "y": 463,
      "w": 370,
      "h": 306
    },
    {
      "x": 941,
      "y": 487,
      "w": 360,
      "h": 303
    },
    {
      "x": 1363,
      "y": 575,
      "w": 385,
      "h": 225
    }
  ],
  "props": {
    "island": {
      "x": 25,
      "y": 56,
      "w": 893,
      "h": 326,
      "surface": 56
    },
    "small": {
      "x": 1067,
      "y": 80,
      "w": 603,
      "h": 308,
      "surface": 39
    },
    "gate": {
      "x": 94,
      "y": 403,
      "w": 724,
      "h": 463
    },
    "beetle": {
      "x": 1115,
      "y": 496,
      "w": 512,
      "h": 338
    }
  }
}
```

## shell.html

```html
<!doctype html>
<html lang="ja"><head><meta charset="utf-8"><meta name="viewport" content="width=device-width,initial-scale=1"><meta name="theme-color" content="#08161d"><title>月灯りの庭 — Moonlit Garden</title><style>__STYLE__</style></head>
<body>
<main id="app">
 <header class="masthead"><a class="wordmark" href="#" aria-label="月灯りの庭">MOONLIT <span>GARDEN</span></a><span class="edition" data-copy="identity.edition">A LITTLE JOURNEY THROUGH THE NIGHT</span><div class="header-actions"><button id="audio" aria-label="音をオンにする" title="音のオン・オフ（M）">音 OFF</button><button id="pause" aria-label="一時停止" disabled>Ⅱ</button><button id="full" aria-label="全画面にする" title="全画面">⛶</button></div></header>
 <section id="stage" aria-label="月灯りの庭 ゲーム画面">
  <div id="game" tabindex="0" role="application" aria-label="左右キーで移動、スペースで二段ジャンプ。Escapeで一時停止。"></div>
  <div id="hud" hidden><div class="hud-left"><span class="chapter" id="chapter">01 / 月の庭</span><span class="lamp-progress" id="lamps">◇ ◇ ◇</span></div><div class="hud-right"><span class="shards"><b>☾</b> <span id="shards">0</span><small id="shard-total">/ 6</small></span><span class="motes"><b>✦</b> <span id="motes">0</span></span></div></div>
  <div id="toast" role="status" aria-live="polite"></div>
  <div id="intro" class="screen intro">
   <div class="intro-copy"><p class="kicker" data-copy="identity.kicker">A SMALL FOX. A QUIET NIGHT.</p><h1 data-copy="identity.title">月灯りの庭</h1><p class="sub-title" data-copy="identity.subtitle">MOONLIT GARDEN</p><div class="hairline"></div><p class="story">もう一度、跳べる。<br>その先に、小さな光がある。</p><button id="start" class="primary" disabled><span id="load-label">庭を準備しています…</span><span aria-hidden="true">→</span></button><div class="howto"><span><kbd>←</kbd><kbd>→</kbd> 移動</span><span><kbd>SPACE</kbd> ジャンプ</span></div><p class="intro-note" data-copy="identity.note">空中でもう一度。光を集めて、月の門へ。</p></div>
   <div class="hero-portrait" aria-hidden="true"><div class="hero-halo"></div><div class="portrait-sprite"></div><span class="portrait-caption" data-copy="identity.portraitCaption">THE LIGHT KEEPER</span></div>
  </div>
  <div id="paused" class="screen veil" hidden><div class="pause-copy"><p class="kicker">TAKE A BREATH</p><h2>ひと休み。</h2><p data-copy="copy.pauseMessage">庭は、このまま待っています。</p><button id="resume" class="primary">つづける <span>→</span></button><button id="restart" class="text-button">はじめから</button></div></div>
  <div id="ending" class="screen veil ending" hidden><div class="end-copy"><p class="kicker" data-copy="copy.endKicker">THE GARDEN AWAKENS</p><h2 data-copy="copy.endTitle">夜に、光が戻った。</h2><p id="end-note">小さな冒険、おかえりなさい。</p><div class="results"><div><span data-copy="copy.shardName">月のかけら</span><strong id="end-shards"></strong></div><div><span data-copy="copy.moteName">集めた光</span><strong id="end-motes"></strong></div><div><span>旅の時間</span><strong id="end-time"></strong></div></div><p id="record"></p><button id="again" class="primary"><span data-copy="copy.replayLabel">もう一度、庭へ</span> <span>↗</span></button></div></div>
  <div id="error" class="screen veil" hidden><div class="pause-copy"><h2>読み込みを確認してください</h2><p id="error-message"></p><button id="reload" class="primary">読み込み直す</button></div></div>
  <div id="touch" aria-label="タッチ操作"><div><button data-key="ArrowLeft" aria-label="左へ">←</button><button data-key="ArrowRight" aria-label="右へ">→</button></div><button data-key="Space" class="touch-jump" aria-label="ジャンプ">跳ぶ</button></div>
 </section>
 <footer><span>左右移動 ＋ 二段ジャンプ</span><span class="footer-center" data-copy="copy.footerNote">光のかけらは、寄り道に。</span><span><kbd>ESC</kbd> 休けい <i>·</i> <kbd>M</kbd> 音</span></footer>
</main>
<!-- EDITABLE: change this JSON to customize the game. Keep the asset and engine blocks intact. -->
<script id="game-settings" type="application/json">__SETTINGS__</script>
<!-- RESOURCE BLOCKS: embedded original assets and engine; do not ask an AI to retype their base64. -->
<script id="game-assets" type="application/json">__ASSETS__</script>
<script id="game-atlas" type="application/json">__ATLAS__</script>
<script>__ENGINE__</script>
<script>__CONFIG__
window.GARDEN_SETUP=prepareGarden();
if(window.GARDEN_SETUP){
__RUNTIME__
}
</script></body></html>
```

## style.css

```css
:root{color-scheme:dark;--cream:#f8e7ba;--gold:#eec478;--muted:#8dabae;--night:#08161d}*{box-sizing:border-box}html,body{margin:0;min-height:100%;background:#061219;color:var(--cream)}body{font-family:"Hiragino Kaku Gothic ProN","Yu Gothic",sans-serif;background:radial-gradient(ellipse at 50% 30%,#16333d,#061219 70%);display:grid;place-items:center;min-height:100dvh}button,a{-webkit-tap-highlight-color:transparent}button{font:inherit;cursor:pointer}button:focus-visible,a:focus-visible{outline:2px solid var(--gold);outline-offset:5px}button:disabled{cursor:wait;opacity:.55}#app{width:min(1440px,100%);padding:0 32px}.masthead{height:76px;display:flex;align-items:center;justify-content:space-between;gap:15px}.wordmark{color:var(--cream);text-decoration:none;font-family:Georgia,serif;font-size:16px;letter-spacing:.12em;white-space:nowrap}.wordmark span{font-size:13px;font-weight:normal;letter-spacing:.22em;margin-left:5px}.edition{color:#718f94;letter-spacing:.21em;font-size:9px}.header-actions{display:flex;gap:9px;align-items:center}.header-actions button{background:none;color:#bdc8be;border:1px solid #ffffff18;min-width:34px;height:30px;border-radius:3px;font-size:14px;padding:0 8px}.header-actions button:hover{background:#ffffff10;color:var(--cream)}#audio{font-size:10px;letter-spacing:.06em}#stage{position:relative;isolation:isolate;width:min(100%,calc((100dvh - 127px)*16/9));margin:0 auto;aspect-ratio:16/9;background:#101f2a;overflow:hidden;box-shadow:0 24px 80px #0007,0 0 0 1px #d3d4a822}#game,#game canvas{display:block;width:100%;height:100%;outline:none}#game{position:absolute;inset:0}#hud{position:absolute;inset:0;pointer-events:none;padding:2.5% 3.2%;display:flex;justify-content:space-between;align-items:flex-start;z-index:2;background:linear-gradient(#03141a90,transparent 110px)}#hud[hidden]{display:none}.hud-left,.hud-right{display:flex;align-items:center;gap:22px}.chapter{font-size:clamp(10px,1.12vw,15px);letter-spacing:.13em;color:#e1e7d2}.lamp-progress{color:#f5cc82;letter-spacing:7px;font-size:20px}.hud-right{color:#f0e4bf;font-variant-numeric:tabular-nums}.shards{font-size:23px}.shards b{font-size:26px;color:#eecb83;font-weight:normal}.shards small{font-size:13px;color:#b6c7bc;margin-left:5px}.motes{font-size:16px;color:#b9d8d1}.motes b{color:#8edfcf;font-size:19px}#toast{position:absolute;bottom:5%;width:100%;text-align:center;pointer-events:none;font-size:clamp(12px,1.3vw,17px);color:#f9f0d5;text-shadow:0 2px 6px #000,0 0 20px #000;letter-spacing:.07em;opacity:0;transition:opacity .3s;z-index:3}#toast.show{opacity:1}.screen{position:absolute;inset:0;z-index:4}.screen[hidden]{display:none}.intro{background:linear-gradient(90deg,#06151de6 0%,#081d28ad 40%,#081d2810 75%)}.intro-copy{position:absolute;top:13%;left:8%;width:48%;z-index:2}.kicker{font-size:clamp(8px,.8vw,11px);letter-spacing:.27em;color:#b9c9ba;margin:0 0 21px}.intro h1{font-family:"Yu Mincho","Hiragino Mincho ProN",serif;font-weight:500;letter-spacing:.15em;font-size:clamp(36px,4.9vw,69px);line-height:1.3;margin:0 0 11px;text-shadow:0 2px 32px #06121d}.sub-title{font-family:Georgia,serif;font-size:clamp(10px,1.05vw,15px);letter-spacing:.49em;color:#e6c78b;margin:0}.hairline{width:52px;height:1px;background:#c6a15c;margin:26px 0}.story{font-family:"Yu Mincho","Hiragino Mincho ProN",serif;line-height:1.95;font-size:clamp(14px,1.5vw,20px);letter-spacing:.06em;color:#cedbd1;margin:0 0 27px}.primary{border:1px solid #ead098;background:#f3d9a4;color:#173b40;display:flex;align-items:center;justify-content:space-between;gap:28px;padding:14px 22px;font-weight:700;letter-spacing:.09em;font-size:clamp(13px,1.2vw,16px);min-width:225px;box-shadow:0 4px 20px #0002;transition:background .2s,transform .2s}.primary:hover{background:#ffebc0;transform:translateY(-2px)}.primary span:last-child{font-size:24px;font-weight:normal;line-height:1}.howto{display:flex;gap:20px;font-size:clamp(9px,.85vw,12px);color:#bbcfcb;margin-top:23px}.howto span{white-space:nowrap}kbd{font:inherit;border:1px solid #ffffff32;padding:2px 5px;margin-right:4px;border-radius:2px}.intro-note{font-size:clamp(9px,.85vw,12px);color:#8ba8ac;letter-spacing:.04em;margin-top:14px}.hero-portrait{position:absolute;left:56%;top:30%;width:40%;height:56%;pointer-events:none}.hero-halo{position:absolute;width:90%;height:95%;background:radial-gradient(ellipse,#95f5cb26,transparent 68%);filter:blur(18px)}.portrait-sprite{position:absolute;inset:0;background-repeat:no-repeat;filter:drop-shadow(0 10px 25px #00131980);animation:breathe 4s ease-in-out infinite;background-size:400% 200%;background-position:0% 100%}.portrait-caption{position:absolute;bottom:3%;width:100%;text-align:center;font-size:8px;letter-spacing:.35em;color:#b5c4b0}@keyframes breathe{50%{transform:translateY(-5px)}}.veil{background:#06161ccd;display:grid;place-items:center;backdrop-filter:blur(5px)}.pause-copy,.end-copy{text-align:center;max-width:680px;padding:25px}.veil h2{font-family:"Yu Mincho","Hiragino Mincho ProN",serif;font-size:clamp(28px,3.3vw,45px);font-weight:500;letter-spacing:.1em;margin:10px 0 22px}.veil p{color:#b4c8c5;font-size:14px;line-height:1.8}.veil .primary{margin:25px auto 0}.text-button{background:none;border:0;color:#9fb4b3;padding:18px;font-size:12px}.ending{background:linear-gradient(#102324c9,#091b23e6)}.results{display:flex;gap:42px;justify-content:center;border-block:1px solid #bed1ba30;margin-top:30px;padding:22px 15px}.results div{display:flex;flex-direction:column;gap:12px}.results span{font-size:11px;color:#91b0ac;letter-spacing:.08em}.results strong{font-family:Georgia,serif;font-size:31px;color:#efd5a2;font-weight:normal}#record{font-size:11px;min-height:20px}footer{height:51px;display:flex;align-items:center;justify-content:space-between;gap:15px;color:#769196;font-size:10px;letter-spacing:.05em}footer i{margin:0 10px;font-style:normal}.footer-center{letter-spacing:.2em;color:#8daba8}#touch{display:none;position:absolute;bottom:20px;left:20px;right:20px;justify-content:space-between;pointer-events:none;z-index:3}#touch>div{display:flex;gap:12px}#touch button{pointer-events:auto;background:#173b4599;border:1px solid #c6dfd65e;color:#fce6b9;width:64px;height:58px;border-radius:50%;font-size:24px;touch-action:none;user-select:none}#touch .touch-jump{font-size:15px}#stage:fullscreen{width:100%;height:100%;aspect-ratio:auto;background:#05131b}#stage:fullscreen #game{position:relative}#stage:fullscreen .intro-copy{top:15%}@media(pointer:coarse){#touch{display:flex}}@media(max-width:750px){#app{padding:0 8px}.masthead{height:49px}.edition,.footer-center{display:none}.wordmark{font-size:11px}.wordmark span{font-size:10px}.header-actions{gap:4px}#stage{width:100%}.intro-copy{top:8%;left:6%}.kicker{margin-bottom:8px}.hairline{margin:12px 0}.story{margin-bottom:13px;font-size:12px}.intro h1{font-size:32px}.primary{padding:8px 12px;min-width:155px;font-size:12px}.howto{margin-top:13px;gap:10px}.intro-note{display:none}.results{gap:22px;margin-top:13px;padding:13px}.veil h2{margin:10px 0}.veil p{font-size:11px}.results strong{font-size:23px}.hud-left{gap:12px}.lamp-progress{font-size:13px}.shards{font-size:16px}.motes{font-size:12px}footer{font-size:8px;height:37px}}@media(prefers-reduced-motion:reduce){.portrait-sprite{animation:none}.primary{transition:none}}
@media(max-width:520px){.intro .kicker,.intro .sub-title,.intro .hairline,.portrait-caption{display:none}.intro-copy{top:9%;width:52%}.intro h1{font-size:27px;margin-bottom:8px}.story{font-size:11px;line-height:1.55;margin-bottom:11px}.intro .primary{min-width:148px;font-size:11px}.howto{font-size:8px;margin-top:10px;gap:6px}.hero-portrait{top:22%;left:54%;width:44%;height:65%}.header-actions button{height:28px;min-width:28px;padding:0 6px}#touch{bottom:7px;left:10px;right:10px}#touch button{width:44px;height:40px;font-size:19px}#touch .touch-jump{font-size:12px}#toast{bottom:23%;font-size:10px}.hud-left{gap:8px}.hud-right{gap:10px}.chapter{font-size:8px;letter-spacing:0}.shards,.shards b{font-size:15px}.shards small{font-size:10px}.motes,.motes b{font-size:12px}.veil h2{font-size:24px}.pause-copy,.end-copy{padding:12px}.veil .primary{margin-top:10px}.veil p{margin:5px 0}.results{padding:8px;margin-top:10px;gap:22px}.results strong{font-size:19px}.results span{font-size:9px}.results div{gap:5px}#record{font-size:8px;min-height:12px}}
```

## config.js

```javascript
// Shared by the build and browser. A bad edit is reported, never silently ignored.
function validateGardenSettings(s){
 const errors=[];
 const obj=(v,p)=>{if(!v||typeof v!=='object'||Array.isArray(v)){errors.push(p+' はオブジェクトにしてください');return false;}return true;};
 const number=(v,p,min,max)=>{if(!Number.isFinite(v)||v<min||v>max)errors.push(p+' は '+min+'〜'+max+' の数値にしてください');};
 const text=(v,p)=>{if(typeof v!=='string'||!v.trim())errors.push(p+' は空でない文字列にしてください');};
 const color=(v,p)=>{if(typeof v!=='string'||!/^#[0-9a-f]{6}$/i.test(v))errors.push(p+' は #RRGGBB 形式にしてください');};
 const frames=(list,p)=>{if(!Array.isArray(list)||![1,8].includes(list.length)){errors.push(p+' は 1 枚または 8 枚の切り出し範囲にしてください');return;}list.forEach((f,i)=>{if(obj(f,p+i)){for(const k of ['x','y'])number(f[k],p+i+'.'+k,0,20000);for(const k of ['w','h'])number(f[k],p+i+'.'+k,1,20000);}});};
 if(!obj(s,'設定'))return errors;
 if(s.version!==1)errors.push('version は 1 にしてください');
 for(const group of ['identity','copy','theme','images','hero','background','physics','audio','level'])obj(s[group],group);
 if(errors.length)return errors;
 for(const key of ['id','title','subtitle','wordmark','edition','kicker','startLabel','note','portraitCaption'])text(s.identity[key],'identity.'+key);
 if(!/^[a-z0-9][a-z0-9-]{0,63}$/.test(s.identity.id))errors.push('identity.id は半角英数字とハイフン（64文字以内）にしてください');
 if(!Array.isArray(s.identity.introLines)||!s.identity.introLines.length)errors.push('identity.introLines に説明文を入れてください');else s.identity.introLines.forEach((v,i)=>text(v,'identity.introLines.'+i));
 for(const key of ['pauseMessage','endKicker','endTitle','endAll','endPartial','endNoCollectibles','replayLabel','shardName','moteName','footerNote','startHint','hitHint','fallHint','checkpointHint'])text(s.copy[key],'copy.'+key);
 for(const key of ['cream','gold','muted','night','heroGlow','shardGlow','mote','overlay'])color(s.theme[key],'theme.'+key);
 number(s.theme.overlayAlpha,'theme.overlayAlpha',0,1);number(s.background.scrollFactor,'background.scrollFactor',0,.3);
 for(const key of ['background','hero','props'])if(s.images[key]!==null&&typeof s.images[key]!=='string')errors.push('images.'+key+' は null、画像パス、または data URL にしてください');
 number(s.hero.displayWidth,'hero.displayWidth',24,320);number(s.hero.originX,'hero.originX',0,1);number(s.hero.animationFps,'hero.animationFps',1,30);
 if(!['left','right'].includes(s.hero.faces))errors.push('hero.faces は left / right にしてください');
 if(s.hero.frames!==null)frames(s.hero.frames,'hero.frames');
 if(s.images.props&&!s.propsFrames)errors.push('足場などの画像を変える場合は propsFrames の切り出し範囲も指定してください');
 if(s.propsFrames!==null){if(obj(s.propsFrames,'propsFrames'))for(const key of ['island','small','gate','beetle']){const f=s.propsFrames[key];frames([f],'propsFrames.'+key);if(f&&['island','small'].includes(key))number(f.surface,'propsFrames.'+key+'.surface',0,f.h);}}
 const ranges={speed:[30,900],acceleration:[100,10000],braking:[100,15000],gravity:[100,4000],jumpSpeed:[100,1300],airJumpSpeed:[100,1300],releaseSpeed:[50,1300],maxFallSpeed:[100,2500],coyoteSeconds:[0,.3],jumpBufferSeconds:[0,.3],bodyWidth:[8,120],bodyHeight:[12,160],respawnDelayMs:[100,3000],invincibleSeconds:[0,10]};
 for(const [key,[min,max]]of Object.entries(ranges))number(s.physics[key],'physics.'+key,min,max);
 if(s.physics.releaseSpeed>Math.min(s.physics.jumpSpeed,s.physics.airJumpSpeed))errors.push('releaseSpeed は jumpSpeed / airJumpSpeed 以下にしてください');
 number(s.audio.volume,'audio.volume',0,1);if(typeof s.audio.autoStart!=='boolean')errors.push('audio.autoStart は true / false にしてください');
 const l=s.level;number(l.width,'level.width',1280,50000);number(l.fallY,'level.fallY',721,1600);
 const point=(v,p,maxY=720)=>{if(obj(v,p)){number(v.x,p+'.x',0,l.width);number(v.y,p+'.y',0,maxY);}};
 point(l.spawn,'level.spawn');point(l.goal,'level.goal');
 for(const key of ['platforms','shards','motes','enemies','checkpoints','chapters','hints','notices'])if(!Array.isArray(l[key]))errors.push('level.'+key+' は配列にしてください');
 if(errors.length)return errors;
 if(!l.platforms.length)errors.push('足場は少なくとも 1 個必要です');
 l.platforms.forEach((p,i)=>{const k='level.platforms.'+i;if(!obj(p,k))return;number(p.x,k+'.x',-1280,l.width);number(p.y,k+'.y',50,700);number(p.w,k+'.w',50,l.width+1280);if(p.move){if(!['x','y'].includes(p.move.axis))errors.push(k+'.move.axis は x / y にしてください');number(p.move.range,k+'.move.range',1,500);number(p.move.period,k+'.move.period',.5,30);}});
 for(const key of ['shards','motes','checkpoints'])l[key].forEach((p,i)=>point(p,'level.'+key+'.'+i));
 l.enemies.forEach((e,i)=>{const k='level.enemies.'+i;point(e,k);if(!e)return;number(e.min,k+'.min',0,l.width);number(e.max,k+'.max',0,l.width);number(e.speed,k+'.speed',0,500);if(e.min>e.x||e.x>e.max)errors.push(k+' の x は min〜max にしてください');});
 if(!l.chapters.length||l.chapters[0]?.x!==0)errors.push('level.chapters の最初は x: 0 にしてください');
 l.chapters.forEach((p,i)=>{if(obj(p,'chapters.'+i)){number(p.x,'chapters.'+i+'.x',0,l.width);text(p.title,'chapters.'+i+'.title');if(i&&p.x<=l.chapters[i-1]?.x)errors.push('chapters は x の小さい順にしてください');}});
 l.hints.forEach((p,i)=>{point(p,'hints.'+i);if(p){text(p.text,'hints.'+i+'.text');number(p.size,'hints.'+i+'.size',10,36);color(p.color,'hints.'+i+'.color');}});
 l.notices.forEach((p,i)=>{if(obj(p,'notices.'+i)){number(p.x,'notices.'+i+'.x',0,l.width);text(p.text,'notices.'+i+'.text');number(p.seconds,'notices.'+i+'.seconds',1,15);}});
 return errors;
}
function validateGardenFrames(frames,width,height,name){
 for(const f of frames)if(!f||f.x<0||f.y<0||f.w<=0||f.h<=0||f.x+f.w>width||f.y+f.h>height)throw Error(name+' の切り出し範囲が画像サイズを超えています');
}
function gardenRecordKey(s){let hash=2166136261;for(const ch of JSON.stringify([s.level,s.physics])){hash^=ch.charCodeAt(0);hash=Math.imul(hash,16777619);}return 'garden:'+s.identity.id+':'+(hash>>>0).toString(16)+':best';}
function prepareGarden(){
 try{
  const settings=JSON.parse(document.querySelector('#game-settings').textContent),errors=validateGardenSettings(settings);
  if(errors.length)throw Error(errors.join(' / '));
  for(const value of Object.values(settings.images))if(value!==null&&!/^data:image\/(png|jpeg|webp);base64,[A-Za-z0-9+/=\r\n]+$/.test(value))throw Error('単体 HTML の画像には data URL が必要です。画像パスを変えた場合は build.mjs で再生成してください。');
  document.title=settings.identity.title+' — '+settings.identity.subtitle;
  document.querySelector('#stage').setAttribute('aria-label',settings.identity.title+' ゲーム画面');
  const wordmark=document.querySelector('.wordmark'),words=settings.identity.wordmark.split(' ');wordmark.textContent=words.shift();if(words.length){wordmark.append(' ');const span=document.createElement('span');span.textContent=words.join(' ');wordmark.append(span);}wordmark.setAttribute('aria-label',settings.identity.title);
  for(const el of document.querySelectorAll('[data-copy]')){const [group,key]=el.dataset.copy.split('.');el.textContent=settings[group][key];}
  const story=document.querySelector('.story');story.replaceChildren();settings.identity.introLines.forEach((line,i)=>{if(i)story.append(document.createElement('br'));story.append(document.createTextNode(line));});
  document.querySelector('#shard-total').textContent='/ '+settings.level.shards.length;
  document.querySelector('.shards').hidden=settings.level.shards.length===0;
  for(const key of ['cream','gold','muted','night'])document.documentElement.style.setProperty('--'+key,settings.theme[key]);
  // Leave the approved palette intact unless a palette setting is changed.
  const paletteRules=[];
  if(settings.theme.gold!=='#eec478')paletteRules.push('.primary{background:var(--gold);border-color:var(--gold)}.primary:hover{background:color-mix(in srgb,var(--gold) 80%,white)}.sub-title,.shards b,.lamp-progress,.results strong{color:var(--gold)}.hairline{background:var(--gold)}');
  if(settings.theme.muted!=='#8dabae')paletteRules.push('.edition,.intro-note,.howto,footer,.footer-center,.veil p,.results span{color:var(--muted)}');
  if(settings.theme.night!=='#08161d')paletteRules.push('html,body{background:var(--night)}body{background:radial-gradient(ellipse at 50% 30%,color-mix(in srgb,var(--night) 75%,white),var(--night) 70%)}.veil{background:color-mix(in srgb,var(--night) 90%,transparent)}');
  const palette=document.createElement('style');palette.id='game-palette';palette.textContent=paletteRules.join('\n');document.head.append(palette);
  window.GARDEN_ASSETS=JSON.parse(document.querySelector('#game-assets').textContent);
  for(const [name,key]of Object.entries({background:'garden',hero:'fox',props:'props'}))if(settings.images[name])window.GARDEN_ASSETS[key]=settings.images[name];
  window.GARDEN_ATLAS=JSON.parse(document.querySelector('#game-atlas').textContent);
  if(settings.propsFrames)window.GARDEN_ATLAS.props=settings.propsFrames;
  return {settings,recordKey:gardenRecordKey(settings)};
 }catch(error){document.querySelector('#error').hidden=false;document.querySelector('#error-message').textContent='設定を確認してください：'+error.message;document.querySelector('#reload').onclick=()=>location.reload();return null;}
}
if(typeof module!=='undefined')module.exports={validateGardenSettings,validateGardenFrames,gardenRecordKey};
```

## input.js

```javascript
// Keep press edges until the next game frame, even if keyup arrives first.
// Movement taps get a short minimum pulse; a held key still releases normally.
class GardenControls{
 constructor(){this.reset();}
 reset(){this.sources=new Map();this.pulse={left:0,right:0};this.jumpPressed=false;}
 held(action){return [...this.sources.values()].includes(action);}
 down(action,source=action){
  if(this.sources.has(source))return;
  const wasHeld=this.held(action);this.sources.set(source,action);
  if(action==='jump'){if(!wasHeld)this.jumpPressed=true;}
  else this.pulse[action]=.06;
 }
 up(source){this.sources.delete(source);}
 sample(dt){
  const state={left:this.held('left')||this.pulse.left>0,right:this.held('right')||this.pulse.right>0,jump:this.held('jump'),jumpPressed:this.jumpPressed};
  this.jumpPressed=false;for(const action of ['left','right'])this.pulse[action]=Math.max(0,this.pulse[action]-dt);
  return state;
 }
}
function gardenAction(event){
 const map={ArrowLeft:'left',KeyA:'left',a:'left',A:'left',ArrowRight:'right',KeyD:'right',d:'right',D:'right',Space:'jump',' ':'jump',Spacebar:'jump',ArrowUp:'jump',KeyW:'jump',w:'jump',W:'jump'};
 return map[event.code]||map[event.key]||null;
}
if(typeof module!=='undefined')module.exports={GardenControls,gardenAction};
```

## audio.js

```javascript
// Original synthesised score and effects: no third-party sound assets.
class GardenAudio{
 constructor(volume=.32){this.volume=volume;this.on=false;this.ctx=null;this.step=0;this.timer=null;this.next=0;}
 unlock(){if(!this.ctx){const C=window.AudioContext||window.webkitAudioContext;if(!C)return;this.ctx=new C();this.master=this.ctx.createGain();this.master.gain.value=this.volume;const compressor=this.ctx.createDynamicsCompressor();compressor.threshold.value=-18;compressor.ratio.value=5;this.master.connect(compressor);compressor.connect(this.ctx.destination);
 const delay=this.ctx.createDelay(.8),feedback=this.ctx.createGain(),wet=this.ctx.createGain();delay.delayTime.value=.31;feedback.gain.value=.24;wet.gain.value=.18;delay.connect(feedback);feedback.connect(delay);delay.connect(wet);wet.connect(this.master);this.delay=delay;
 }this.ctx.resume().catch(()=>{});}
 set(on){this.unlock();this.on=on;if(this.master)this.master.gain.setTargetAtTime(on?this.volume:0,this.ctx.currentTime,.05);if(on&&!this.timer){this.next=this.ctx.currentTime+.05;this.timer=setInterval(()=>this.schedule(),120);}document.querySelector('#audio').textContent=on?'音 ON':'音 OFF';document.querySelector('#audio').setAttribute('aria-label',on?'音をオフにする':'音をオンにする');}
 tone(freq,time=.25,gain=.1,type='sine',when=null,end=null){if(!this.ctx||!this.on)return;const t=when??this.ctx.currentTime,o=this.ctx.createOscillator(),g=this.ctx.createGain();o.type=type;o.frequency.setValueAtTime(freq,t);if(end)o.frequency.exponentialRampToValueAtTime(end,t+time);g.gain.setValueAtTime(.0001,t);g.gain.exponentialRampToValueAtTime(gain,t+.012);g.gain.exponentialRampToValueAtTime(.0001,t+time);o.connect(g);g.connect(this.master);g.connect(this.delay);o.start(t);o.stop(t+time+.05);}
 note(midi,t,dur,gain){this.tone(440*2**((midi-69)/12),dur,gain,'sine',t);this.tone(440*2**((midi+12-69)/12),dur*.55,gain*.13,'sine',t);}
 schedule(){if(!this.on||document.hidden){this.next=this.ctx.currentTime+.1;return;}const notes=[74,null,81,78,null,76,69,null,74,81,null,83,81,null,78,null,71,null,78,74,null,76,69,null,73,76,null,81,78,null,76,null];while(this.next<this.ctx.currentTime+.3){const i=this.step%32,base=[50,47,43,45][Math.floor(i/8)];if(i%8===0){this.note(base,this.next,3.5,.07);this.note(base+19,this.next,3.1,.03);}if(notes[i]!==null)this.note(notes[i],this.next,1.25,.032);this.next+=.34;this.step++;}}
 jump(double=false){this.tone(double?690:380,.16,.09,'sine',null,double?1100:620);if(double)this.tone(1380,.32,.025);}
 land(){this.tone(115,.09,.07,'triangle',null,55);}
 mote(combo){this.note([81,83,86,88,90][combo%5],this.ctx?.currentTime,.6,.07);}
 shard(){[74,78,81,86].forEach((n,i)=>this.note(n,this.ctx?.currentTime+i*.08,.85,.09));}
 hurt(){this.tone(155,.25,.08,'triangle',null,55);}
 checkpoint(){[62,69,74,78,81].forEach((n,i)=>this.note(n,this.ctx?.currentTime+i*.12,1.6,.07));}
 finish(){[62,66,69,74,78,81,86].forEach((n,i)=>this.note(n,this.ctx?.currentTime+i*.15,2,.085));}
}
```

## game.js

```javascript
const $=s=>document.querySelector(s), S=window.GARDEN_SETUP.settings, P=S.physics, COPY=S.copy, W=1280,H=720,L=S.level, audio=new GardenAudio(S.audio.volume);
const tint=hex=>parseInt(hex.slice(1),16),recordKey=window.GARDEN_SETUP.recordKey;
let garden,mode='loading',mutedByUser=false;
const reduced=matchMedia('(prefers-reduced-motion: reduce)').matches;
function label(s,v){const e=$(s);if(e.textContent!==String(v))e.textContent=v;}
function announce(text,seconds=3.2){$('#toast').textContent=text;$('#toast').classList.add('show');clearTimeout(announce.timer);announce.timer=setTimeout(()=>$('#toast').classList.remove('show'),seconds*1000);}
const fmt=s=>Math.floor(s/60)+':'+String(Math.floor(s%60)).padStart(2,'0');
class Garden extends Phaser.Scene{
 constructor(){super('garden');}
 preload(){for(const [key,src]of Object.entries(window.GARDEN_ASSETS))this.load.image(key,src);this.load.on('progress',n=>label('#load-label','庭を準備しています '+Math.floor(n*100)+'%'));this.load.on('loaderror',()=>fail('画像を読み込めませんでした。ページを開き直してください。'));}
 create(){
  garden=this;this.sectionIndex=0;this.clock=0;this.elapsed=0;this.jumps=0;this.coyote=0;this.jumpBuffer=0;this.releaseJump=false;this.facing=1;this.misses=0;this.checkpoint=-1;this.collected=new Set();this.collectedMotes=new Set();this.invincible=0;this.respawning=false;this.landTimer=0;this.wasGround=false;this.lastJump=false;this.combo=0;this.lastMote=-10;this.groundPlatform=null;this.hintFlags=new Set();this.fx=[];
  const tex=this.textures.get('fox'),heroImage=tex.getSourceImage();
  this.heroFrames=S.hero.frames||(S.images.hero?[{x:0,y:0,w:heroImage.width,h:heroImage.height}]:GARDEN_ATLAS.fox);
  try{validateGardenFrames(this.heroFrames,heroImage.width,heroImage.height,'主人公');const source=this.textures.get('props').getSourceImage();validateGardenFrames(Object.values(GARDEN_ATLAS.props),source.width,source.height,'足場・門・敵');}catch(error){fail(error.message);return;}
  for(let i=0;i<8;i++){const f=this.heroFrames[i%this.heroFrames.length];if(!tex.has('f'+i))tex.add('f'+i,0,f.x,f.y,f.w,f.h);}
  const propTex=this.textures.get('props');for(const [key,f]of Object.entries(GARDEN_ATLAS.props))if(!propTex.has(key))propTex.add(key,0,f.x,f.y,f.w,f.h);
  this.makeTextures();
  const bgWidth=Math.max(1610,W+(L.width-W)*S.background.scrollFactor+20),bgSource=this.textures.get('garden').getSourceImage();
  this.bg=this.add.image(-10,-98,'garden').setOrigin(0).setDisplaySize(bgWidth,bgWidth*bgSource.height/bgSource.width).setScrollFactor(S.background.scrollFactor).setDepth(-20);
  this.add.rectangle(W/2,H/2,W,H,tint(S.theme.overlay),S.theme.overlayAlpha).setScrollFactor(0).setDepth(-18);
  this.fog=[];for(let i=0;i<5;i++)this.fog.push(this.add.image(i*460-160,565+i%2*72,'fog').setDisplaySize(830,170).setAlpha(.10).setScrollFactor(.15).setDepth(-10));
  this.farFireflies=Array.from({length:52},(_,i)=>this.add.image((i*173)%1850,120+(i*67)%520,'spark').setScale(.1+(i%4)*.05).setAlpha(.13+i%5*.06).setScrollFactor(.12).setDepth(-8));
  this.platformGroup=this.physics.add.staticGroup();this.platforms=L.platforms.map((p,i)=>{
   const key=p.w<350?'small':'island',f=GARDEN_ATLAS.props[key],width=p.w+22,scale=width/f.w;
   const art=this.add.image(p.x-11,p.y-f.surface*scale,'props',key).setOrigin(0).setScale(scale).setDepth(p.upper?2:1);
   const body=this.add.rectangle(p.x+p.w/2,p.y+15,p.w,30,0xffffff,0);this.physics.add.existing(body,true);this.platformGroup.add(body);body.body.updateFromGameObject();
   const line=this.add.rectangle(p.x+p.w/2,p.y+1,p.w-16,2,p.move?0xffd790:0x83dcc0,p.move?.55:.19).setDepth(3);
   const data={...p,id:i,nowX:p.x,nowY:p.y,dx:0,dy:0,art,body,line,scale,surface:f.surface};body.platform=data;
   if(p.move){data.rune=this.add.image(p.x+p.w/2,p.y+42,'rune').setScale(.7).setAlpha(.5).setDepth(3);}
   return data;
  });
  this.player=this.add.rectangle(L.spawn.x,L.spawn.y,P.bodyWidth,P.bodyHeight,0xffffff,0);this.physics.add.existing(this.player);this.player.body.setSize(P.bodyWidth,P.bodyHeight).setMaxVelocity(P.speed,P.maxFallSpeed).setGravityY(P.gravity).setDragX(P.braking);
  this.player.body.setCollideWorldBounds(false);
  this.hero=this.add.image(L.spawn.x,L.spawn.y,'fox','f4').setOrigin(.65,1).setDepth(10);
  this.heroGlow=this.add.image(L.spawn.x,L.spawn.y,'glow').setTint(tint(S.theme.heroGlow)).setAlpha(.2).setDisplaySize(185,185).setBlendMode(Phaser.BlendModes.ADD).setDepth(8);
  this.shadow=this.add.ellipse(L.spawn.x,590,48,10,0x00161e,.38).setDepth(4);
  this.doubleRing=this.add.image(L.spawn.x,L.spawn.y,'rune').setScale(.5).setAlpha(0).setDepth(9);
  this.physics.add.collider(this.player,this.platformGroup,(hero,platform)=>{this.groundPlatform=platform.platform;},(hero,platform)=>hero.body.velocity.y>=-10&&hero.body.bottom-hero.body.velocity.y*this.frameDelta<=platform.platform.nowY+16,this);
  this.shards=L.shards.map((p,i)=>{const glow=this.add.image(p.x,p.y,'glow').setDisplaySize(115,115).setTint(tint(S.theme.shardGlow)).setAlpha(.38).setBlendMode(Phaser.BlendModes.ADD);const spr=this.add.image(p.x,p.y,'moon').setScale(.54).setDepth(5);return {...p,i,glow,spr};});
  this.motes=L.motes.map((p,i)=>({ ...p,i,spr:this.add.image(p.x,p.y,'spark').setScale(.32).setTint(tint(S.theme.mote)).setDepth(5)}));
  this.enemies=L.enemies.map((e,i)=>({...e,i,dir:i%2?1:-1,art:this.add.image(e.x,e.y+6,'props','beetle').setOrigin(.5,1).setDisplaySize(91,54).setDepth(7)}));
  this.checkpoints=L.checkpoints.map((c,i)=>{const g=this.add.graphics().setDepth(4);g.fillStyle(0x23494d).fillRoundedRect(c.x-8,c.y-63,16,63,4);g.fillStyle(0x6c8f83).fillRoundedRect(c.x-18,c.y-69,36,10,3);const flame=this.add.image(c.x,c.y-90,'spark').setScale(.6).setTint(0x809d9c).setAlpha(.35).setDepth(5);const glow=this.add.image(c.x,c.y-90,'glow').setDisplaySize(140,140).setAlpha(.08).setBlendMode(Phaser.BlendModes.ADD).setDepth(4);return {...c,i,flame,glow};});
  this.portalGlow=this.add.image(L.goal.x,L.goal.y-128,'glow').setDisplaySize(270,360).setTint(0xffcf79).setBlendMode(Phaser.BlendModes.ADD).setAlpha(.3).setDepth(4);
  this.gate=this.add.image(L.goal.x,L.goal.y+6,'props','gate').setOrigin(.5,1).setDisplaySize(310,233).setDepth(6);
  this.gateRune=this.add.image(L.goal.x,L.goal.y-126,'rune').setScale(1.13).setAlpha(.55).setTint(0xffdea5).setDepth(5);
  this.worldHints=L.hints.map(h=>this.worldText(h.x,h.y,h.text,h.size,tint(h.color)));
  this.sectionTitle=this.add.text(72,123,'',{fontFamily:'Georgia, serif',fontSize:'39px',color:'#f6e8c7',letterSpacing:3}).setScrollFactor(0).setDepth(30).setAlpha(0);
  this.cameras.main.setBounds(0,0,L.width,H);this.cameras.main.setBackgroundColor('#081c29');this.cameras.main.startFollow(this.player,false,.075,.06,-275,0);this.cameras.main.setDeadzone(0,720);this.cameras.main.scrollX=0;
  this.keys=this.input.keyboard.addKeys({left:'LEFT',right:'RIGHT',a:'A',d:'D',space:'SPACE',up:'UP',w:'W'});this.input.keyboard.addCapture(['LEFT','RIGHT','UP','SPACE']);
  this.controls=new GardenControls();this.input.keyboard.on('keydown-ESC',()=>togglePause());this.input.keyboard.on('keydown-M',()=>{mutedByUser=true;audio.set(!audio.on);});
  this.physics.pause();mode='ready';$('#start').disabled=false;label('#load-label',S.identity.startLabel);
  const portrait=$('.portrait-sprite');if(!S.images.hero&&!S.hero.frames){portrait.style.backgroundImage='url('+GARDEN_ASSETS.fox+')';}else{const f=this.heroFrames[4%this.heroFrames.length],canvas=document.createElement('canvas');canvas.width=f.w;canvas.height=f.h;canvas.style.cssText='width:100%;height:100%;object-fit:contain';canvas.getContext('2d').drawImage(heroImage,f.x,f.y,f.w,f.h,0,0,f.w,f.h);portrait.style.backgroundImage='none';portrait.replaceChildren(canvas);}
  this.publicState();
 }
 makeTextures(){
  if(this.textures.exists('moon'))return;
  const radial=(key,size,stops)=>{const t=this.textures.createCanvas(key,size,size),c=t.context,g=c.createRadialGradient(size/2,size/2,0,size/2,size/2,size/2);stops.forEach(s=>g.addColorStop(...s));c.fillStyle=g;c.fillRect(0,0,size,size);t.refresh();};
  radial('glow',128,[[0,'#fffbd8'],[.18,'#ffeab480'],[.55,'#a8e5bd20'],[1,'#c6e9d000']]);
  radial('spark',64,[[0,'#ffffff'],[.14,'#f5ffe8'],[.32,'#ddffcd90'],[1,'#c6e9d000']]);
  const f=this.textures.createCanvas('fog',512,128),c=f.context,grad=c.createRadialGradient(256,64,0,256,64,256);grad.addColorStop(0,'#9cd4d880');grad.addColorStop(1,'#9cd4d800');c.scale(1,.25);c.fillStyle=grad;c.fillRect(0,0,512,512);f.refresh();
  const g=this.add.graphics();g.lineStyle(2,0xe6c881,.65);g.strokeCircle(48,48,35);g.lineStyle(1,0xf9e8b9,.35);g.strokeCircle(48,48,41);for(let i=0;i<8;i++){const a=i*Math.PI/4;g.lineBetween(48+Math.cos(a)*29,48+Math.sin(a)*29,48+Math.cos(a)*41,48+Math.sin(a)*41);}g.generateTexture('rune',96,96);g.clear();
  g.fillStyle(0xf7d590);g.fillCircle(32,32,21);g.fillStyle(0x304a52);g.fillCircle(42,25,18);g.lineStyle(1,0xffedc0,.7);g.strokeCircle(32,32,25);g.generateTexture('moon',64,64);g.destroy();
 }
 worldText(x,y,text,size,color){return this.add.text(x,y,text,{fontFamily:'"Yu Mincho",serif',fontSize:size+'px',color:'#'+color.toString(16).padStart(6,'0'),shadow:{offsetY:2,color:'#041119',blur:8,fill:true}}).setOrigin(.5).setDepth(15);}
 begin(){if(mode==='ready'){mode='playing';$('#intro').hidden=true;$('#hud').hidden=false;$('#pause').disabled=false;this.physics.resume();if(!mutedByUser&&S.audio.autoStart)audio.set(true);else audio.unlock();this.showSection(L.chapters[0].title);announce(COPY.startHint,4);$('#game').focus();this.publicState();}}
 showSection(text){this.sectionTitle.setText(text).setAlpha(0);this.tweens.add({targets:this.sectionTitle,alpha:.85,duration:550,hold:1800,yoyo:true});}
 burst(x,y,color,count=15,force=130){for(let i=0;i<count;i++){const a=i*2.3999,r=force*(.3+(i%7)/8);const image=this.add.image(x,y,'spark').setScale(.10+(i%3)*.055).setTint(color).setBlendMode(Phaser.BlendModes.ADD).setDepth(18);this.fx.push({image,x,y,vx:Math.cos(a)*r,vy:Math.sin(a)*r-30,age:0,life:.5+(i%5)*.09});}}
 update(t,ms){
  if(!this.player)return;const dt=Math.min(ms/1000,.035);this.frameDelta=dt;
  if(mode==='paused'||mode==='loading')return;this.clock+=dt;
  this.fx=this.fx.filter(p=>{p.age+=dt;if(p.age>p.life){p.image.destroy();return false;}p.x+=p.vx*dt;p.y+=p.vy*dt;p.vy+=150*dt;p.image.setPosition(p.x,p.y).setAlpha(1-p.age/p.life);return true;});
  for(let i=0;i<this.farFireflies.length;i++){const p=this.farFireflies[i];p.y+=Math.sin(this.clock*.45+i)*dt*5;p.setAlpha(.12+.18*(Math.sin(this.clock*.8+i)+1)/2);}
  this.fog.forEach((f,i)=>f.x=i*460-160+Math.sin(this.clock*.04+i)*130);
  this.portalGlow.setAlpha(.32+Math.sin(this.clock*1.3)*.08);this.gateRune.rotation=this.clock*.13;
  this.motes.forEach(m=>{if(!this.collectedMotes.has(m.i)){m.spr.y=m.y+Math.sin(this.clock*2+m.i)*4;m.spr.alpha=.65+Math.sin(this.clock*2.6+m.i)*.25;}});
  this.shards.forEach(s=>{if(!this.collected.has(s.i)){s.spr.y=s.y+Math.sin(this.clock*1.8+s.i)*6;s.spr.rotation=Math.sin(this.clock+s.i)*.1;s.glow.y=s.spr.y;s.glow.setAlpha(.26+Math.sin(this.clock*1.4+s.i)*.1);}});
  if(mode!=='playing'){this.renderHero(dt);this.publicState();return;}
  this.elapsed+=dt;this.invincible=Math.max(0,this.invincible-dt);this.landTimer=Math.max(0,this.landTimer-dt);
  if(this.respawning){this.renderHero(dt);this.publicState();return;}
  const b=this.player.body;
  for(const p of this.platforms){const oldX=p.nowX,oldY=p.nowY;if(p.move){const m=p.move,offset=Math.sin(this.elapsed*Math.PI*2/m.period)*m.range;p.nowX=p.x+(m.axis==='x'?offset:0);p.nowY=p.y+(m.axis==='y'?offset:0);}p.dx=p.nowX-oldX;p.dy=p.nowY-oldY;if(p.move){p.body.setPosition(p.nowX+p.w/2,p.nowY+15);p.body.body.updateFromGameObject();p.art.setPosition(p.nowX-11,p.nowY-p.surface*p.scale);p.line.setPosition(p.nowX+p.w/2,p.nowY+1);p.rune.setPosition(p.nowX+p.w/2,p.nowY+43);p.rune.rotation=this.clock*.24;}}
  const grounded=b.blocked.down||b.touching.down;
  if(grounded&&this.groundPlatform?.move){this.player.x+=this.groundPlatform.dx;this.player.y+=this.groundPlatform.dy;}
  if(grounded){this.coyote=P.coyoteSeconds;this.jumps=0;if(!this.wasGround){this.landTimer=.12;audio.land();this.burst(this.player.x,b.bottom,0x91ddba,9,90);}}else this.coyote=Math.max(0,this.coyote-dt);
  this.wasGround=grounded;
  const {left,right,jump,jumpPressed}=this.controls.sample(dt);
  if(jumpPressed)this.jumpBuffer=P.jumpBufferSeconds;else this.jumpBuffer=Math.max(0,this.jumpBuffer-dt);
  if(!jump&&this.lastJump&&b.velocity.y<-P.releaseSpeed)b.setVelocityY(-P.releaseSpeed);
  this.lastJump=!!(jump||jumpPressed);
  const dir=Number(!!right)-Number(!!left);b.setAccelerationX(dir*P.acceleration);b.setDragX(dir?0:P.braking);if(dir)this.facing=dir;
  if((jumpPressed||this.jumpBuffer>0)&&(grounded||this.coyote>0||this.jumps<2)){
   const air=!grounded&&this.coyote<=0;this.jumps=air?2:1;this.coyote=0;this.jumpBuffer=0;b.setVelocityY(air?-P.airJumpSpeed:-P.jumpSpeed);this.wasGround=false;this.groundPlatform=null;audio.jump(air);this.burst(this.player.x,b.bottom,air?0xffe1a0:0xbee8d6,air?20:10,air?170:110);
   if(air){this.doubleRing.setPosition(this.player.x,this.player.y+14).setAlpha(.9).setScale(.25);this.tweens.add({targets:this.doubleRing,scale:1.4,alpha:0,duration:420});}
  }
  this.player.x=Phaser.Math.Clamp(this.player.x,30,L.width-45);
  for(const e of this.enemies){e.x+=e.speed*e.dir*dt;if(e.x<e.min||e.x>e.max){e.x=Phaser.Math.Clamp(e.x,e.min,e.max);e.dir*=-1;}e.art.setPosition(e.x,e.y+5+Math.sin(this.clock*12+e.i)*1.3).setFlipX(e.dir>0).setAngle(Math.sin(this.clock*9+e.i)*1.2);if(!this.invincible&&Math.abs(this.player.x-e.x)<43&&b.bottom>e.y-37&&b.top<e.y-3){this.failRun(COPY.hitHint);break;}}
  if(!this.respawning&&this.player.y>L.fallY)this.failRun(COPY.fallHint);
  for(const m of this.motes){if(!this.collectedMotes.has(m.i)&&Math.hypot(this.player.x-m.x,this.player.y-m.spr.y)<40){this.collectedMotes.add(m.i);this.combo=this.elapsed-this.lastMote<.65?this.combo+1:0;this.lastMote=this.elapsed;m.spr.setVisible(false);audio.mote(this.combo);this.burst(m.x,m.y,0xa3f5d0,7,65);}}
  for(const s of this.shards){if(!this.collected.has(s.i)&&Math.hypot(this.player.x-s.x,this.player.y-s.spr.y)<47){this.collected.add(s.i);s.spr.setVisible(false);s.glow.setVisible(false);audio.shard();this.burst(s.x,s.y,0xffdc91,32,185);announce(COPY.shardName+'　'+this.collected.size+' / '+L.shards.length,2.7);if(!reduced)this.cameras.main.flash(150,235,203,131,false);}}
  for(const c of this.checkpoints){if(c.i>this.checkpoint&&Math.abs(this.player.x-c.x)<55&&Math.abs(b.bottom-c.y)<85){this.checkpoint=c.i;c.flame.setTint(0xffda90).setAlpha(1);c.glow.setTint(0xffd991).setAlpha(.4);this.burst(c.x,c.y-90,0xffd890,35,180);audio.checkpoint();announce(COPY.checkpointHint,3);}}
  for(let i=this.sectionIndex+1;i<L.chapters.length;i++)if(this.player.x>=L.chapters[i].x){this.sectionIndex=i;this.showSection(L.chapters[i].title);}
  L.notices.forEach((notice,i)=>{if(this.player.x>notice.x&&!this.hintFlags.has(i)){this.hintFlags.add(i);announce(notice.text,notice.seconds);}});
  if(this.player.x>L.goal.x-28&&b.bottom>L.goal.y-110&&!this.respawning)this.finish();
  this.renderHero(dt);this.publicState();
 }
 renderHero(dt){
  const b=this.player.body,ground=b.blocked.down||b.touching.down;let f=4;if(mode==='playing'&&!this.respawning){if(!ground)f=b.velocity.y<-50?5:6;else if(Math.abs(b.velocity.x)>35)f=Math.floor(this.clock*S.hero.animationFps)%4;else if(this.landTimer>0)f=7;}
  this.hero.setFrame('f'+f);const frame=this.heroFrames[f%this.heroFrames.length],scale=S.hero.displayWidth/frame.w,squash=this.landTimer>0?Math.sin(this.landTimer/.12*Math.PI)*.12:0;
  const flip=S.hero.faces==='right'?this.facing<0:this.facing>0;
  this.hero.setScale(scale*(1+squash),scale*(1-squash)).setFlipX(flip).setOrigin(flip?1-S.hero.originX:S.hero.originX,1).setPosition(this.player.x,b.bottom+3);
  this.hero.setAlpha(this.respawning?0:this.invincible>0&&Math.floor(this.clock*12)%2?.38:1);
  this.heroGlow.setPosition(this.player.x,this.player.y-10).setAlpha(this.respawning?0:.13+Math.sin(this.clock*2)*.025);
  let nearest=780;for(const p of this.platforms)if(this.player.x>p.nowX&&this.player.x<p.nowX+p.w&&p.nowY>=b.bottom-4)nearest=Math.min(nearest,p.nowY);
  const d=Math.max(0,nearest-b.bottom);this.shadow.setPosition(this.player.x+3,nearest+4).setScale(Math.max(.35,1-d/300),1).setAlpha(nearest<740?.26*Math.max(0,1-d/340):0);
 }
 failRun(message){
  if(this.respawning||mode!=='playing')return;this.respawning=true;this.misses++;audio.hurt();this.burst(this.player.x,this.player.y,0xffb59d,30,200);if(!reduced)this.cameras.main.shake(140,.0025);this.player.body.stop();this.player.body.enable=false;announce(message,2.8);
  this.time.delayedCall(P.respawnDelayMs,()=>{const c=this.checkpoint>=0?L.checkpoints[this.checkpoint]:null;this.player.body.enable=true;this.player.body.reset(c?c.x:L.spawn.x,c?c.y-P.bodyHeight/2-8:L.spawn.y);this.player.body.setAccelerationX(0);this.jumps=0;this.coyote=0;this.jumpBuffer=0;this.lastJump=false;this.wasGround=false;this.groundPlatform=null;this.invincible=P.invincibleSeconds;this.respawning=false;this.hero.setAlpha(1);this.cameras.main.scrollX=Math.max(0,this.player.x-365);if(mode==='paused')this.physics.pause();});
 }
 finish(){
  mode='finishing';this.player.body.stop();this.physics.pause();$('#pause').disabled=true;audio.finish();this.burst(L.goal.x,L.goal.y-110,0xffe8a8,75,290);this.portalGlow.setAlpha(.7);this.tweens.add({targets:this.portalGlow,scaleX:this.portalGlow.scaleX*2,scaleY:this.portalGlow.scaleY*2,alpha:.7,duration:1100});if(!reduced)this.cameras.main.flash(1000,249,225,164,false);
  this.time.delayedCall(1250,()=>{mode='complete';$('#ending').hidden=false;label('#end-shards',this.collected.size+' / '+L.shards.length);label('#end-motes',this.collectedMotes.size+' / '+L.motes.length);label('#end-time',fmt(this.elapsed));label('#end-note',!L.shards.length?COPY.endNoCollectibles:this.collected.size===L.shards.length?COPY.endAll:COPY.endPartial);let record='';try{const last=JSON.parse(localStorage.getItem(recordKey)||'null');if(!last||this.collected.size>last.shards||(this.collected.size===last.shards&&this.elapsed<last.time)){localStorage.setItem(recordKey,JSON.stringify({shards:this.collected.size,time:this.elapsed}));record='このブラウザのベスト記録を更新';}else record='ベスト：'+COPY.shardName+' '+last.shards+' / '+L.shards.length+'　・　'+fmt(last.time);}catch{}label('#record',record);$('#again').focus();this.publicState();});
 }
 publicState(){const d=$('#game').dataset;d.templateVersion=S.version;d.gameId=S.identity.id;d.heroKind=this.heroFrames.length===1?'single':'animated';d.shardTotal=L.shards.length;d.state=mode;d.x=Math.round(this.player.x);d.y=Math.round(this.player.y);d.vx=Math.round(this.player.body.velocity.x);d.vy=Math.round(this.player.body.velocity.y);d.grounded=String(this.player.body.blocked.down||this.player.body.touching.down);d.jumps=this.jumps;d.shards=this.collected.size;d.motes=this.collectedMotes.size;d.checkpoint=this.checkpoint;d.misses=this.misses;d.time=this.elapsed.toFixed(2);d.respawning=String(this.respawning);d.audio=audio.ctx?.state||'not-started';label('#shards',this.collected.size);label('#motes',this.collectedMotes.size);label('#lamps',L.checkpoints.map((_,i)=>i<=this.checkpoint?'◆':'◇').join(' '));label('#chapter',L.chapters[this.sectionIndex].title);}
}
function fail(message){$('#error').hidden=false;label('#error-message',message);}
function clearInput(){if(!garden)return;garden.controls?.reset();garden.lastJump=false;garden.jumpBuffer=0;garden.input.keyboard.resetKeys();if(garden.player?.body)garden.player.body.setAccelerationX(0);}
function togglePause(){if(!garden)return;if(mode==='playing'){mode='paused';clearInput();garden.physics.pause();garden.tweens.pauseAll();garden.time.paused=true;$('#paused').hidden=false;label('#pause','▶');$('#pause').setAttribute('aria-label','再開');$('#resume').focus();}else if(mode==='paused'){mode='playing';garden.time.paused=false;garden.tweens.resumeAll();garden.physics.resume();$('#paused').hidden=true;label('#pause','Ⅱ');$('#pause').setAttribute('aria-label','一時停止');$('#game').focus();}garden.publicState();}
function restart(){clearInput();clearTimeout(announce.timer);$('#toast').classList.remove('show');label('#toast','');$('#ending').hidden=true;$('#paused').hidden=true;$('#intro').hidden=false;$('#hud').hidden=true;$('#pause').disabled=true;label('#pause','Ⅱ');$('#pause').setAttribute('aria-label','一時停止');garden.time.paused=false;mode='loading';garden.scene.restart();}
$('#start').onclick=()=>garden?.begin();$('#pause').onclick=togglePause;$('#resume').onclick=togglePause;$('#restart').onclick=restart;$('#again').onclick=restart;$('#reload').onclick=()=>location.reload();$('.wordmark').onclick=e=>{e.preventDefault();if(mode==='playing')togglePause();};
$('#audio').onclick=()=>{mutedByUser=true;audio.set(!audio.on);$('#game').focus();};
$('#full').onclick=()=>{if(document.fullscreenElement)document.exitFullscreen?.();else $('#stage').requestFullscreen?.().catch(()=>{});};
window.addEventListener('blur',()=>{if(mode==='playing')togglePause();});document.addEventListener('visibilitychange',()=>{if(document.hidden&&mode==='playing')togglePause();});
for(const button of document.querySelectorAll('[data-key]')){const key=button.dataset.key,source='pointer:'+key;button.addEventListener('pointerdown',e=>{e.preventDefault();button.setPointerCapture(e.pointerId);if(mode==='playing')garden?.controls.down(gardenAction({code:key}),source);});for(const name of ['pointerup','pointercancel','lostpointercapture'])button.addEventListener(name,()=>garden?.controls.up(source));}
$('#game').addEventListener('pointerdown',()=>$('#game').focus({preventScroll:true}));
window.addEventListener('keydown',e=>{const action=gardenAction(e);if(!action||!['playing','paused'].includes(mode))return;e.preventDefault();if(mode==='playing')garden?.controls.down(action,e.code||e.key);},{capture:true});
window.addEventListener('keyup',e=>{if(gardenAction(e))garden?.controls.up(e.code||e.key);},{capture:true});
if(typeof Phaser==='undefined')fail('ゲームエンジンを読み込めませんでした。');
else new Phaser.Game({type:Phaser.AUTO,parent:'game',width:W,height:H,backgroundColor:'#091d29',transparent:false,antialias:true,roundPixels:false,physics:{default:'arcade',arcade:{gravity:{y:0},debug:false,fps:120,fixedStep:true}},scale:{mode:Phaser.Scale.FIT,autoCenter:Phaser.Scale.CENTER_BOTH},audio:{noAudio:true},scene:Garden,banner:false,render:{powerPreference:'high-performance'},fps:{target:60,min:30,smoothStep:true}});
```

