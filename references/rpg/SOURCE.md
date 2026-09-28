# 制作例の参考コード

この資料は、参加者の希望に合った新しいゲームを作るための技術参照です。画像のbase64とゲームエンジン本体を除き、見本の設定・アプリ側コードをまとめています。コードや素材の再利用は可能ですが、見本と同じ世界・機能数・設定項目だけに制作を限定しません。

これは実行用パッケージではありません。`shell.html` の置換用プレースホルダー、元の相対import、画像名などは見本の組み立て構造を示します。この文書をそのままCanvasへ貼るだけでは動きません。作るゲームに合わせて画像・ライブラリー・HTML要素を接続してください。子どもにファイルの分割・ビルドを求める資料ではありません。

使用エンジンは Phaser（MIT）。ライブラリー本体と既存のライセンス全文・著作権表示は、対応する制作例HTMLに内包されています。ライブラリーを再利用する際は通知を維持してください。画像本体はHTMLの中にあり、この資料に画像を取得できたという保証は含みません。アプリ側コード・画像に新しい包括ライセンスを付けたものではありません。

見本の入力チェックや固定の個数は、その見本用です。新しい機能や構成を作るときは関連するルール・描画・検証を合わせて変更できます。

## settings.json

```json
{
  "version": 2,
  "identity": {"title":"ひかりの森の魔法使い","titleLines":["ひかりの森の","魔法使い"],"subtitle":"THE LAST LITTLE LIGHT","heroName":"見習い魔法使い","intro":"小さな魔法で、森をもう一度。","ending":"大樹に、光が帰ってきた。","endingLines":["大樹に、","光が帰ってきた。"]},
  "hero": {"hp":60,"speed":250,"battleHeight":310,"mapHeight":96,"faces":"right"},
  "spells": {"fire":{"name":"炎の魔法","damage":12},"water":{"name":"水の魔法","damage":12},"grass":{"name":"草の魔法","damage":12},"guard":{"name":"まもる","damageMultiplier":0.25},"heal":{"name":"回復","amount":30,"uses":3}},
  "combat": {"weakMultiplier":1.5,"resistMultiplier":0.7,"enemyGuardMultiplier":0.65},
  "theme": {"accent":"#efd596","magic":"#ffe9a3","water":"#7be4e4"},
  "images": {"hero":null,"heroMap":null,"forestEnemy":null,"waterEnemy":null,"boss":null,"battle":null,"mapEntrance":null,"mapPond":null,"mapTree":null},
  "areas": [
    {"name":"森の入口","landmark":"古い石の門","note":"門の向こうで、影が動いた。","inspect":["炎は草に、草は水に、水は炎に強い。","大技が来る前には「まもる」。体力が減ったら「回復」も使おう。"],"inspectAt":{"x":255,"y":390},"obstacles":[],"enemy":{"id":"forest","name":"森の影の魔物","element":"grass","hp":54,"x":720,"y":445,"height":205,"pattern":[{"kind":"attack","name":"影のひっかき","damage":5},{"kind":"focus","name":"飛びかかる準備","damage":12},{"kind":"attack","name":"影のひっかき","damage":5},{"kind":"guard","name":"影をまとう","damage":3}]}},
    {"name":"池の周辺","landmark":"水辺の石碑","note":"水面に、もうひとつの影。","inspect":["水の魔物には、草の魔法がよく効く。","バリアの間は回復するチャンス。大波は「まもる」で小さくできる。"],"inspectAt":{"x":285,"y":525},"obstacles":[{"x":410,"y":200,"w":400,"h":165}],"enemy":{"id":"water","name":"池の水の魔物","element":"water","hp":84,"x":790,"y":462,"height":275,"pattern":[{"kind":"guard","name":"水のバリア","damage":3},{"kind":"attack","name":"水しぶき","damage":5},{"kind":"focus","name":"大波の準備","damage":15},{"kind":"attack","name":"水しぶき","damage":5}]}},
    {"name":"大樹の広場","landmark":"大樹の根もと","note":"この影を越えれば、光は戻る。","inspect":["大きな影は、草・水・炎へと属性を変える。弱点を見てみよう。","間違えても、あわてなくていい。「まもる」と「回復」で立て直そう。"],"inspectAt":{"x":500,"y":355},"obstacles":[{"x":520,"y":180,"w":235,"h":115}],"enemy":{"id":"boss","name":"大樹を覆う巨大な影","element":"grass","phases":[{"at":0.6,"element":"water"},{"at":0.3,"element":"fire"}],"hp":150,"x":765,"y":432,"height":295,"pattern":[{"kind":"attack","name":"根のムチ","damage":7},{"kind":"guard","name":"闇のよろい","damage":4},{"kind":"focus","name":"闇の嵐の準備","damage":16},{"kind":"attack","name":"影の爪","damage":8},{"kind":"focus","name":"大きな闇の準備","damage":18}]}}
  ]
}
```

## atlas.json

```json
{
 "hero":{"idle":{"x":14,"y":12,"w":555,"h":621},"cast":{"x":587,"y":50,"w":657,"h":579},"down":{"x":75,"y":644,"w":465,"h":592},"up":{"x":655,"y":643,"w":552,"h":594}},
 "enemies":{"forest":{"x":8,"y":12,"w":686,"h":534},"water":{"x":703,"y":8,"w":543,"h":544},"boss":{"x":3,"y":537,"w":674,"h":710},"hurt":{"x":672,"y":559,"w":578,"h":688}},
 "maps":{"entrance":{"x":0,"y":0,"w":966,"h":542},"pond":{"x":0,"y":543,"w":966,"h":543},"tree":{"x":0,"y":1087,"w":966,"h":541}}
}
```

## shell.html

```html
<!doctype html>
<html lang="ja"><head><meta charset="utf-8"><meta name="viewport" content="width=device-width,initial-scale=1"><meta name="theme-color" content="#111f22"><title>ひかりの森の魔法使い</title><style>__STYLE__</style></head>
<body><main id="app"><header><div class="wordmark"><span class="mark">✦</span><span id="brand">ひかりの森の魔法使い</span></div><div class="tools"><button id="sound" aria-label="音をオンにする">音 OFF</button><button id="pause" disabled aria-label="一時停止">Ⅱ</button><button id="full" aria-label="全画面にする">⛶</button></div></header>
<section id="stage" aria-label="RPGゲーム画面"><div id="game" tabindex="0" role="application" aria-label="矢印で移動・魔法を選択。スペースで決定。Escapeで一時停止。"></div><div id="interface">
 <section id="intro" class="screen intro"><div class="intro-copy"><p class="eyebrow" id="subtitle">THE LAST LITTLE LIGHT</p><h1 id="title">ひかりの森の<br>魔法使い</h1><div class="rule"></div><p id="intro-note">小さな魔法で、森をもう一度。</p><p class="intro-story">影をこえて、大樹に光を。<br>使う魔法は、あなたが決める。</p><button id="start" class="primary" disabled>森を準備しています…</button><div class="controls"><span><kbd>↑ ↓ ← →</kbd> 移動</span><span><kbd>SPACE</kbd> 決定・調べる</span></div><p class="subtle">戦闘は、ゆっくり考えて大丈夫。</p></div></section>
 <div id="map-hud" hidden><div class="place"><span class="eyebrow" id="area-number"></span><h2 id="area-name"></h2><p id="area-note"></p></div><div class="journey"><span>大樹に光を</span><div id="journey-dots"></div></div><div id="exit-left" class="exit left" hidden>← 戻る</div><div id="exit-right" class="exit right">次のエリア →</div><div id="map-tip" class="map-tip"></div></div>
 <div id="battle-hud" hidden><div class="battle-top"><div><span class="eyebrow" id="battle-location"></span><p id="turn-number"></p></div><div class="enemy-status"><div class="status-label"><h2 id="enemy-name"></h2><span id="enemy-hp-text"></span></div><div class="bar enemy-bar"><i id="enemy-hp"></i></div><div class="affinity-row"><span id="enemy-element"></span><span id="enemy-weakness"></span></div><p id="phase-hint"></p></div><div id="intent" class="intent"><b id="intent-label"></b><span id="intent-hint"></span></div></div>
 <div id="phase-notice" role="status" aria-live="polite"></div>
 <div id="battle-message" role="status" aria-live="polite"></div>
 <div class="command-panel"><div class="hero-status"><span class="eyebrow">あなた</span><h2 id="hero-name"></h2><div class="status-label"><span>HP</span><strong id="hero-hp-text"></strong></div><div class="bar"><i id="hero-hp"></i></div><p>回復 <span id="heals-left"></span></p><small>攻撃は何度でも使える</small></div><div class="spell-list"><p class="choose-label" id="choose-label">どうする？ <small>矢印で選ぶ ／ SPACE 決定</small></p><div class="spell-grid" role="group" aria-label="戦闘コマンド">
 <button class="spell selected" id="fire"><span class="spell-symbol"></span><span><b id="fire-name">炎の魔法</b><small id="fire-detail"></small></span></button>
 <button class="spell" id="water"><span class="spell-symbol"></span><span><b id="water-name">水の魔法</b><small id="water-detail"></small></span></button>
 <button class="spell" id="grass"><span class="spell-symbol"></span><span><b id="grass-name">草の魔法</b><small id="grass-detail"></small></span></button>
 <button class="spell support" id="guard"><span class="spell-symbol"></span><span><b id="guard-name">まもる</b><small>ダメージを小さく</small></span></button>
 <button class="spell support" id="heal"><span class="spell-symbol"></span><span><b id="heal-name">回復</b><small id="heal-detail"></small></span></button>
 </div></div></div></div>
 <div id="dialogue" class="dialogue" hidden><span id="dialogue-speaker" class="eyebrow"></span><p id="dialogue-text"></p><span class="dialogue-next">SPACE つづける ▸</span></div>
 <div id="result" class="screen result" hidden><div class="result-copy"><p class="eyebrow" id="result-kicker"></p><h2 id="result-title"></h2><p id="result-note"></p><button id="result-next" class="primary"></button></div></div>
 <div id="ending" class="screen ending" hidden><div class="end-copy"><p class="eyebrow">THE FOREST AWAKENS</p><h2 id="ending-title"></h2><p>あなたの選んだ魔法が、<br>この森の明日を照らした。</p><div class="end-stats" id="end-stats"></div><button id="replay" class="primary">もう一度、森へ <span>→</span></button></div></div>
 <div id="paused" class="screen paused" hidden><div><p class="eyebrow">TAKE YOUR TIME</p><h2>ひと休み。</h2><p>森も、魔物も待っています。</p><button id="resume" class="primary">つづける <span>→</span></button></div></div>
 <div id="error" class="screen paused" hidden><div><h2>読み込みを確認してください</h2><p id="error-message"></p><button id="reload" class="primary">読み込み直す</button></div></div>
 <div id="toast" role="status" aria-live="polite"></div>
</div></section><footer><span><kbd>矢印</kbd> 移動・魔法を選ぶ　<kbd>SPACE</kbd> 決定</span><span>戦闘ごとに、体力と魔法は全回復。</span><span><kbd>ESC</kbd> ひと休み</span></footer>
<nav id="touch-controls" aria-label="タッチ操作"><div><button data-key="ArrowLeft">←</button><button data-key="ArrowUp">↑</button><button data-key="ArrowDown">↓</button><button data-key="ArrowRight">→</button></div><button data-key="Space">決定・調べる</button></nav></main>
<!-- EDIT HERE: editable settings. The following embedded assets and engine are shared by example and template. -->
<script type="application/json" id="game-settings">__SETTINGS__</script><script type="application/json" id="game-assets">__ASSETS__</script><script type="application/json" id="game-atlas">__ATLAS__</script><script>__ENGINE__</script><script>__RUNTIME__</script></body></html>
```

## style.css

```css
:root{--ink:#111f22;--cream:#faf1d8;--accent:#efd596;--muted:#a6beb4;--green:#91d5b0}*{box-sizing:border-box}html,body{margin:0;min-height:100%;background:#101b1d;color:var(--cream);font-family:"Hiragino Kaku Gothic ProN","Yu Gothic",sans-serif}body{background:radial-gradient(ellipse at 55% 15%,#243a38,#101b1d 75%)}button{font:inherit;color:inherit;cursor:pointer}button:focus-visible{outline:3px solid #fff4a6;outline-offset:4px}button:disabled{cursor:default}button,input{-webkit-tap-highlight-color:transparent}[hidden]{display:none!important}#app{width:min(1328px,calc((100dvh - 118px)*16/9),calc(100% - 40px));margin:0 auto}header{height:66px;display:flex;align-items:center;justify-content:space-between;gap:20px}.wordmark{display:flex;align-items:center;gap:12px;font-size:15px;font-weight:600;letter-spacing:.12em}.mark{color:var(--accent);font-size:26px}.tools{display:flex;gap:8px}.tools button{background:transparent;border:1px solid #597267;border-radius:3px;height:34px;min-width:36px;font-size:14px}.tools button:hover{border-color:var(--cream)}.tools button:disabled{opacity:.3}#sound{min-width:76px}#stage{position:relative;aspect-ratio:16/9;overflow:hidden;background:#17292c;box-shadow:0 16px 70px #0006;border:1px solid #5d736440;border-radius:3px}#game{position:absolute;inset:0;outline:none}#game canvas{display:block}#interface{position:absolute;top:0;left:0;width:1280px;height:720px;transform-origin:0 0;pointer-events:none}#interface button{pointer-events:auto}.screen{position:absolute;inset:0}.eyebrow{font-size:14px;letter-spacing:.2em;font-weight:600;color:var(--accent);margin:0 0 14px}.intro{background:linear-gradient(90deg,#0b2227f5 0%,#0b2227c7 38%,transparent 73%)}.intro-copy{position:absolute;top:96px;left:76px;max-width:570px}h1{font-family:"Hiragino Mincho ProN","Yu Mincho",serif;font-size:62px;line-height:1.4;font-weight:600;letter-spacing:.06em;margin:20px 0}h2,p{margin:0}.rule{width:55px;height:2px;background:var(--accent);margin:24px 0}.intro-copy>p:not(.eyebrow):not(.subtle){font-size:22px;line-height:1.9}.intro-copy .intro-story{font-size:18px!important;color:#bbcdc3;margin-top:12px;line-height:1.9}.primary{min-height:60px;min-width:248px;display:inline-flex;gap:50px;align-items:center;justify-content:space-between;padding:15px 25px;color:#17322e;background:var(--accent);border:1px solid #ffffb580;box-shadow:0 6px 20px #0003;border-radius:3px;font-size:20px;font-weight:700;letter-spacing:.06em}.primary:hover{background:#fff1c4;transform:translateY(-1px)}.primary:disabled{opacity:.5}.intro .primary{margin:26px 0 21px}.controls{display:flex;gap:22px;font-size:14px;color:#c5d2c8}kbd{font-family:inherit;font-size:.85em;border:1px solid #94ac9d80;border-radius:3px;padding:3px 6px;color:#e1e9d7}.subtle{font-size:14px;color:#98b7ae;margin-top:16px}.place{position:absolute;left:42px;top:36px;padding:0 0 8px;text-shadow:0 2px 12px #000}.place h2{font-family:"Hiragino Mincho ProN","Yu Mincho",serif;font-size:31px;letter-spacing:.06em;font-weight:600}.place .eyebrow{margin-bottom:7px}.place p{margin-top:10px;color:#e2e6cd;font-size:17px}.journey{position:absolute;right:40px;top:35px;display:flex;align-items:center;gap:22px;text-shadow:0 2px 10px #000;font-size:17px}.journey div{letter-spacing:9px;font-size:25px;color:var(--accent)}.exit{position:absolute;top:346px;font-size:18px;letter-spacing:.05em;padding:14px 18px;background:#13312bc4;border:1px solid #dcdfb140;color:#f1e9c9}.exit.left{left:0;border-left:0}.exit.right{right:0;border-right:0}.map-tip{position:absolute;bottom:35px;left:50%;transform:translateX(-50%);padding:14px 28px;background:#0e282bdc;border:1px solid #9ab5a760;font-size:18px;white-space:nowrap;border-radius:3px}.battle-top{position:absolute;left:38px;right:38px;top:26px;display:flex;justify-content:space-between}.battle-top>div:first-child{padding-top:8px;text-shadow:0 2px 8px #000}.battle-top .eyebrow{margin-bottom:8px}#turn-number{font-size:18px}.enemy-status{width:415px;background:#0b2423d9;padding:19px 23px 16px;border:1px solid #738b7959;border-radius:3px}.status-label{display:flex;align-items:center;justify-content:space-between;gap:10px;font-size:16px}.status-label h2{font-size:22px;letter-spacing:.02em}.bar{height:9px;background:#0007;overflow:hidden;border-radius:2px;margin:12px 0 0}.bar i{display:block;width:100%;height:100%;background:#97d4b0;transition:width .35s ease}.enemy-bar i{background:#f4c894}.intent{margin-top:14px;padding-top:12px;border-top:1px solid #9fb6a638}.intent b,.intent span{display:block}.intent b{font-size:19px;color:#f8deaa}.intent span{font-size:16px;line-height:1.5;color:#c9dacc;margin-top:4px}.intent.focus b{color:#ffbf86}.intent.focus{border-top-color:#dfa86d80}.intent.guard b{color:#9ce4e0}#battle-message{position:absolute;left:330px;right:350px;top:222px;text-align:center;font-size:23px;font-weight:700;color:#fff2cb;text-shadow:0 2px 8px #00211c,0 0 24px #041c1b;line-height:1.5}.command-panel{position:absolute;bottom:0;left:0;right:0;height:172px;background:linear-gradient(100deg,#102728f5,#172d2cf7);border-top:1px solid #9aab8160;display:flex;gap:33px;padding:23px 36px}.hero-status{width:240px;flex:none;border-right:1px solid #91a6914d;padding-right:29px}.hero-status .eyebrow{font-size:12px;margin-bottom:3px}.hero-status h2{font-size:21px;letter-spacing:.02em;margin-bottom:12px}.hero-status .bar{margin-top:6px;height:8px}.hero-status p{font-size:14px;margin-top:10px;color:#bfd0bf}.hero-status p span{font-size:20px;letter-spacing:5px;margin-left:10px;color:var(--accent)}.spell-list{flex:1;min-width:0}.choose-label{font-size:18px;margin-bottom:12px;display:flex;justify-content:space-between;align-items:center}.choose-label small{font-size:14px;color:#aabfb0;font-weight:400}.spell-row{display:flex;gap:16px}.spell{position:relative;flex:1;display:flex;align-items:center;text-align:left;gap:18px;height:86px;background:#152e30;border:1px solid #91a59170;border-radius:3px;padding:15px 18px;color:#dce9d9;transition:background .12s,border-color .12s}.spell.selected{border:2px solid var(--accent);background:#34453a;box-shadow:inset 0 0 20px #e6cb7120}.spell.selected:before{content:'▸';position:absolute;left:-11px;top:30px;color:#fbe3a6;font-size:22px}.spell:hover:not(:disabled){background:#3d5142}.spell b{display:block;font-size:24px;font-weight:700;letter-spacing:.02em}.spell small{display:block;margin-top:7px;font-size:15px;color:#bacebd}.spell-symbol{font-size:43px;font-family:serif;color:var(--accent)}.spell i{margin-left:auto;font-size:12px;writing-mode:vertical-rl;font-style:normal;letter-spacing:.15em;color:#b8c6b1}.spell:disabled{opacity:.45}.dialogue{position:absolute;bottom:34px;left:130px;right:130px;padding:25px 33px 42px;border:1px solid #abbd8f;background:#112b2cef;box-shadow:0 10px 35px #001b19b3}.dialogue .eyebrow{font-size:16px;display:block;margin-bottom:10px}.dialogue p{font-size:23px;line-height:1.6}.dialogue-next{position:absolute;right:28px;bottom:15px;font-size:13px;letter-spacing:.1em;color:#b6c9b2}.result{background:linear-gradient(90deg,#092422df,#0c22265e 62%,transparent);display:flex;align-items:center;padding-left:76px}.result-copy{max-width:600px;margin-top:-25px}.result h2,.paused h2{font-family:"Hiragino Mincho ProN","Yu Mincho",serif;font-size:52px;margin:15px 0 20px;font-weight:500}.result p:not(.eyebrow){font-size:20px;line-height:1.8;color:#d3dfcf;white-space:pre-line}.result .primary{margin-top:28px}.paused{background:#06191ce8;display:grid;place-items:center;text-align:center;pointer-events:auto}.paused p{font-size:20px;color:#bbcbbb;margin:20px 0 30px}.ending{background:linear-gradient(90deg,#122b28ed,transparent 75%)}.end-copy{position:absolute;left:66px;top:157px;max-width:630px}.end-copy h2{font-family:"Hiragino Mincho ProN","Yu Mincho",serif;font-size:48px;line-height:1.6;font-weight:500;max-width:550px}.end-copy>p:not(.eyebrow){font-size:22px;line-height:1.9;margin-top:20px}.end-stats{font-size:17px;line-height:1.9;color:#d8dfba;margin:22px 0}#toast{position:absolute;left:50%;bottom:198px;transform:translateX(-50%);background:#143030ef;border:1px solid #d7d2a785;padding:12px 24px;color:#fff0c9;font-size:19px;border-radius:3px;opacity:0;transition:opacity .15s;white-space:nowrap}#toast.visible{opacity:1}footer{display:flex;align-items:center;justify-content:space-between;gap:15px;color:#9db1a4;font-size:11px;height:44px}footer kbd{font-size:10px}#touch-controls{display:none}#stage:fullscreen{border:0;border-radius:0;margin:auto;width:min(100vw,calc(100vh*16/9));height:min(100vh,calc(100vw*9/16));aspect-ratio:16/9}#stage::backdrop{background:#0b191a}#error-message{max-width:850px;font-size:20px;overflow-wrap:anywhere}@media(max-width:850px){#app{width:calc(100% - 18px)}header{height:50px}.wordmark{font-size:11px;letter-spacing:.01em}.mark{font-size:21px}.tools{gap:4px}.tools button{height:30px;font-size:12px}footer{font-size:9px;height:38px}footer>span:nth-child(2){display:none}#touch-controls{display:flex;justify-content:space-between;gap:10px;padding-bottom:15px}#touch-controls>div{display:flex;gap:6px}#touch-controls button{background:#28423b;border:1px solid #758e79;border-radius:4px;min-width:46px;height:45px;touch-action:none;user-select:none}}@media(prefers-reduced-motion:reduce){*{scroll-behavior:auto!important}.bar i,.spell,#toast{transition:none}.primary:hover{transform:none}}
/* Five commands: attack above, survival below. Keep the painted battle stage. */
.command-panel{height:190px;padding:13px 30px;gap:24px}
.hero-status{width:226px;padding-right:24px;padding-top:2px}
.hero-status h2{font-size:20px;margin-bottom:8px}
.hero-status p{margin-top:8px}
.hero-status p span{font-size:19px;letter-spacing:0;margin-left:8px}
.hero-status>small{display:block;font-size:14px;color:#bacebd;margin-top:6px}
.choose-label{margin-bottom:8px;font-size:19px}
.spell-grid{display:grid;grid-template-columns:repeat(6,minmax(0,1fr));gap:8px 12px}
.spell-grid .spell{grid-column:span 2;height:61px;min-width:0;gap:12px;padding:6px 14px}
.spell-grid .support{grid-column:span 3}
.spell-grid .spell b{font-size:21px;line-height:1.2;letter-spacing:0}
.spell-grid .spell small{font-size:15px;margin-top:3px;line-height:1.2}
.spell-grid .spell-symbol{display:flex;width:29px;flex:none;color:var(--element)}
.spell-symbol svg{width:29px;height:29px}
.spell-grid .spell.selected{border-color:#fff0b1;background:#3d493c;box-shadow:inset 0 0 0 1px #fff0b1}
.spell-grid .spell.selected:before{top:18px}
.spell-grid .spell[data-affinity=weak] small{color:var(--element);font-weight:700}
.spell-grid .spell:disabled{opacity:.45}
.affinity-row{display:flex;align-items:center;justify-content:space-between;gap:20px;margin-top:12px}
.affinity-row span{display:inline-flex;align-items:center;gap:8px;font-size:20px}
.affinity-row svg{width:23px;height:23px}
#phase-hint{color:#c1d2c7;font-size:15px;margin-top:7px;min-height:0}
#phase-hint:empty{display:none}
.battle-top>.intent{position:absolute;left:0;top:60px;width:385px;margin:0;padding:14px 18px;background:#0b2423eb;border:1px solid #738b7959;border-radius:3px}
.battle-top>.intent b{font-size:20px}
.battle-top>.intent span{font-size:17px;line-height:1.55}
.enemy-status{width:440px}
#battle-message{top:218px;left:400px;right:340px}
#phase-notice{position:absolute;top:192px;left:50%;transform:translateX(-50%);display:flex;align-items:center;gap:10px;padding:12px 20px;background:#102827f2;border:1px solid currentColor;font-size:22px;white-space:nowrap;opacity:0;transition:opacity .2s}
#phase-notice.visible{opacity:1}
#phase-notice svg{width:27px;height:27px}
#phase-notice small{font-size:17px;color:#f5edce}
#toast{bottom:213px}
```

## core.mjs

```javascript
// Pure rules: renderer, input, audio and elapsed real time never decide a battle.
export const elements=['fire','water','grass'];
export const commands=[...elements,'guard','heal'];
export const elementInfo={fire:{name:'炎',color:'#ffb07c'},water:{name:'水',color:'#8addf4'},grass:{name:'草',color:'#a5e498'}};
export const weaknessOf={fire:'water',water:'grass',grass:'fire'};
export function affinity(attack,enemy){return weaknessOf[enemy]===attack?'weak':weaknessOf[attack]===enemy?'resist':'neutral';}
export function enemyElement(s,b){const e=s.areas[b.area].enemy;return (e.phases??[]).filter(p=>b.enemyHp/e.hp<=p.at).at(-1)?.element??e.element;}
export function nextSelection(index,key){
 if(key==='ArrowRight')return (index+1)%commands.length;
 if(key==='ArrowLeft')return (index+commands.length-1)%commands.length;
 if(key==='ArrowDown')return [3,3,4,0,2][index];
 if(key==='ArrowUp')return [3,3,4,0,2][index];
 return index;
}
export function validateSettings(s){
 const errors=[];
 const obj=v=>v&&typeof v==='object'&&!Array.isArray(v);
 const num=(v,k,min,max,integer=false)=>{if(!Number.isFinite(v)||v<min||v>max||(integer&&!Number.isInteger(v)))errors.push(`${k}: ${min}〜${max}${integer?'の整数':''}`);};
 const str=(v,k,max=60)=>{if(typeof v!=='string'||!v.trim()||v.length>max)errors.push(`${k}: 1〜${max}文字`);};
 if(!obj(s))return ['設定はオブジェクトにしてください'];
 if(s.version!==2)errors.push('version: 2 が必要です（5コマンド版）');
 for(const k of ['identity','hero','spells','combat','theme','images'])if(!obj(s[k]))errors.push(k+' が必要です');
 if(errors.length)return errors;
 for(const k of ['title','subtitle','heroName','intro','ending'])str(s.identity[k],'identity.'+k,k==='title'?24:60);
 if(!Array.isArray(s.identity.titleLines)||s.identity.titleLines.length<1||s.identity.titleLines.length>3)errors.push('identity.titleLines は1〜3行');else s.identity.titleLines.forEach(v=>str(v,'identity.titleLines',12));
 if(!Array.isArray(s.identity.endingLines)||s.identity.endingLines.length<1||s.identity.endingLines.length>3)errors.push('identity.endingLines は1〜3行');else s.identity.endingLines.forEach(v=>str(v,'identity.endingLines',12));
 num(s.hero.hp,'hero.hp',1,999,true);num(s.hero.speed,'hero.speed',60,600);num(s.hero.battleHeight,'hero.battleHeight',120,360);num(s.hero.mapHeight,'hero.mapHeight',40,125);
 if(!['left','right'].includes(s.hero.faces))errors.push('hero.faces は left / right');
 for(const k of ['frame','mapFrame'])if(s.hero[k]!=null){const f=s.hero[k];if(!obj(f))errors.push('hero.'+k+' は切り出し範囲');else for(const n of ['x','y','w','h'])num(f[n],'hero.'+k+'.'+n,n==='x'||n==='y'?0:1,16000,true);}
 for(const k of commands){
  if(!obj(s.spells[k])){errors.push('spells.'+k+' が必要です');continue;}
  str(s.spells[k].name,'spells.'+k+'.name',12);
  if(elements.includes(k))num(s.spells[k].damage,'spells.'+k+'.damage',1,999,true);
 }
 if(obj(s.spells.heal)){num(s.spells.heal.amount,'spells.heal.amount',1,999,true);num(s.spells.heal.uses,'spells.heal.uses',1,9,true);}
 if(obj(s.spells.guard))num(s.spells.guard.damageMultiplier,'spells.guard.damageMultiplier',.05,.75);
 num(s.combat.weakMultiplier,'combat.weakMultiplier',1.1,3);num(s.combat.resistMultiplier,'combat.resistMultiplier',.1,1);num(s.combat.enemyGuardMultiplier,'combat.enemyGuardMultiplier',.1,1);
 for(const k of ['accent','magic','water'])if(!/^#[0-9a-f]{6}$/i.test(s.theme[k]))errors.push('theme.'+k+': #RRGGBB');
 for(const k of ['hero','heroMap','forestEnemy','waterEnemy','boss','battle','mapEntrance','mapPond','mapTree'])if(s.images[k]!==null&&(typeof s.images[k]!=='string'||!s.images[k]))errors.push('images.'+k+': null または画像パス');
 if(!Array.isArray(s.areas)||s.areas.length!==3)return [...errors,'areas は3エリア必要です。エリア数の拡張は描画・進行コードも変更してください'];
 const ids=new Set();
 s.areas.forEach((a,i)=>{
  const p='areas.'+i;if(!obj(a)){errors.push(p+' が必要');return;}
  for(const k of ['name','landmark','note'])str(a[k],p+'.'+k,35);
  if(!Array.isArray(a.inspect)||!a.inspect.length)errors.push(p+'.inspect が必要');else a.inspect.forEach(v=>str(v,p+'.inspect',80));
  if(!obj(a.inspectAt))errors.push(p+'.inspectAt が必要');else {num(a.inspectAt.x,p+'.inspectAt.x',90,1170);num(a.inspectAt.y,p+'.inspectAt.y',180,600);}
  if(!Array.isArray(a.obstacles))errors.push(p+'.obstacles が必要');else a.obstacles.forEach(o=>{if(!obj(o)){errors.push('obstacle');return;}for(const k of ['x','y','w','h'])num(o[k],p+'.obstacles.'+k,0,k==='x'||k==='w'?1280:720);});
  const e=a.enemy;if(!obj(e)){errors.push(p+'.enemy が必要');return;}
  str(e.id,p+'.enemy.id',32);if(ids.has(e.id))errors.push('敵idは重複できません');ids.add(e.id);
  str(e.name,p+'.enemy.name',24);num(e.hp,p+'.enemy.hp',1,999,true);num(e.x,p+'.enemy.x',200,1080);num(e.y,p+'.enemy.y',320,540);num(e.height,p+'.enemy.height',100,360);
  if(!elements.includes(e.element))errors.push(p+'.enemy.element: fire / water / grass');
  if(e.phases!==undefined){
   if(!Array.isArray(e.phases))errors.push(p+'.enemy.phases は配列');
   else {let last=1;for(const phase of e.phases){if(!obj(phase)){errors.push(p+'.enemy.phases');continue;}num(phase.at,p+'.enemy.phases.at',.01,.99);if(phase.at>=last)errors.push('属性変化のHP割合は降順・重複なし');last=phase.at;if(!elements.includes(phase.element))errors.push('属性変化のelement: fire / water / grass');}}
  }
  if(!Array.isArray(e.pattern)||!e.pattern.length)errors.push(p+'.pattern が必要');else e.pattern.forEach(t=>{if(!obj(t)){errors.push('pattern');return;}if(!['attack','guard','focus'].includes(t.kind))errors.push('攻撃種類は attack / guard / focus');str(t.name,p+'.pattern.name',18);num(t.damage,p+'.pattern.damage',0,999,true);});
 });return errors;
}
export function createRun(){return {area:0,x:160,y:355,defeated:[],battles:0,retries:0,turns:0,weakHits:0};}
export function createBattle(s,area){const e=s.areas[area].enemy;return {area,heroHp:s.hero.hp,enemyHp:e.hp,heals:s.spells.heal.uses,turn:0,result:null};}
export function intentFor(s,b){const p=s.areas[b.area].enemy.pattern;return p[b.turn%p.length];}
export function resolveTurn(s,b,spell){
 if(b.result)throw Error('終了した戦闘です');
 if(!commands.includes(spell))throw Error('不明なコマンドです');
 if(spell==='heal'&&(b.heals<=0||b.heroHp>=s.hero.hp))throw Error(b.heals<=0?'回復の残り回数がありません':'体力は満タンです');
 const intent=intentFor(s,b),element=enemyElement(s,b),attack=elements.includes(spell),match=attack?affinity(spell,element):null;
 const guarded=attack&&intent.kind==='guard';
 const multiplier=match==='weak'?s.combat.weakMultiplier:match==='resist'?s.combat.resistMultiplier:1;
 const damage=attack?Math.max(1,Math.round(s.spells[spell].damage*multiplier*(guarded?s.combat.enemyGuardMultiplier:1))):0;
 const healed=spell==='heal'?Math.min(s.spells.heal.amount,s.hero.hp-b.heroHp):0;
 const next={...b,heals:b.heals-(spell==='heal'?1:0),enemyHp:Math.max(0,b.enemyHp-damage),turn:b.turn+1};
 const enemyDamage=next.enemyHp===0?0:Math.round(intent.damage*(spell==='guard'?s.spells.guard.damageMultiplier:1));
 next.heroHp=Math.max(0,b.heroHp+healed-enemyDamage);next.result=next.enemyHp===0?'victory':next.heroHp===0?'defeat':null;
 const nextElement=enemyElement(s,next);
 return {next,spell,intent,damage,enemyDamage,healed,match,guarded,element,nextElement,elementChanged:!next.result&&element!==nextElement};
}
export function finishBattle(run,b){
 if(b.result!=='victory')throw Error('勝利前に敵を消すことはできません');
 return {...run,defeated:[...new Set([...run.defeated,b.area])],battles:run.battles+1};
}
export function canTravel(run,direction){const next=run.area+direction;return next>=0&&next<3&&(direction<0||run.defeated.includes(run.area));}
export function insidePolygon(x,y,points){let inside=false;for(let i=0,j=points.length-1;i<points.length;j=i++){const [xi,yi]=points[i],[xj,yj]=points[j];if((yi>y)!==(yj>y)&&x<(xj-xi)*(y-yi)/(yj-yi)+xi)inside=!inside;}return inside;}
export function movePoint(x,y,dx,dy,seconds,speed,obstacles=[],walkable=null){
 const len=Math.hypot(dx,dy)||1,dt=Math.min(Math.max(seconds,0),.05),step=speed*dt;
 const blocked=(px,py)=>(walkable&&!insidePolygon(px,py,walkable))||obstacles.some(o=>px+12>o.x&&px-12<o.x+o.w&&py+7>o.y&&py-7<o.y+o.h);
 const clamp=(v,min,max)=>Math.max(min,Math.min(max,v));
 let nx=clamp(x+dx/len*step,70,1210),ny=clamp(y+dy/len*step,195,604);
 if(blocked(nx,y))nx=x;if(blocked(nx,ny))ny=y;return {x:nx,y:ny};
}
```

## world.mjs

```javascript
// Walkable silhouettes trace the painted paths. These are editable level geometry,
// separate from the artwork; a new map should update both together.
export const world=[
 {left:{x:100,y:335},right:{x:1155,y:258},walkable:[[65,305],[250,296],[420,339],[600,345],[820,320],[1050,245],[1240,155],[1240,320],[1060,385],[970,408],[1090,492],[1150,568],[1090,600],[935,528],[824,477],[650,468],[430,452],[235,404],[65,382]]},
 {left:{x:100,y:344},right:{x:1155,y:280},walkable:[[60,277],[240,371],[411,459],[578,470],[780,438],[977,338],[1205,220],[1240,326],[1072,422],[894,501],[718,559],[585,606],[398,556],[225,466],[60,390]]},
 {left:{x:100,y:365},right:{x:1020,y:430},walkable:[[60,294],[215,282],[392,288],[484,297],[589,319],[775,290],[1010,357],[1120,449],[1090,528],[950,591],[760,622],[523,601],[402,519],[260,429],[60,404]]}
];
```

## icons.mjs

```javascript
// Small original inline vector marks: no emoji font or external icon dependency.
const paths={
 fire:'<path d="M13 2c2 6-3 6-1 11 2-1 3-3 3-5 5 4 7 8 4 12-3 4-10 4-13 0-4-6 0-10 3-13-1 5 1 5 1 6 1-5 4-5 3-11Z"/>',
 water:'<path d="M12 2C9 7 4 11 4 16a8 8 0 0 0 16 0c0-5-5-9-8-14Z"/><path d="M8 16c0 3 2 4 4 4" fill="none" stroke="var(--ink,#153038)" stroke-width="2"/>',
 grass:'<path d="M21 3C9 1 3 6 4 14c1 7 10 8 14 2 2-3 3-8 3-13Z"/><path d="m3 23 13-15" fill="none" stroke="currentColor" stroke-width="2"/>',
 guard:'<path d="m12 2 9 4v7c0 5-5 8-9 11-4-3-9-6-9-11V6Z" fill="none" stroke="currentColor" stroke-width="2.5"/><path d="m7 12 3 3 7-7" fill="none" stroke="currentColor" stroke-width="2"/>',
 heal:'<path d="M9 3h6v6h6v6h-6v6H9v-6H3V9h6Z"/>'
};
export function icon(name){return `<svg viewBox="0 0 26 26" aria-hidden="true" focusable="false" fill="currentColor">${paths[name]??''}</svg>`;}
```

## audio.mjs

```javascript
// Self-contained Web Audio: no downloaded sound files or autoplay dependency.
export class ForestAudio{
 constructor(){this.enabled=false;this.ctx=null;this.nodes=new Set();this.beat=0;this.elapsed=0;}
 async toggle(){this.enabled=!this.enabled;if(this.enabled){this.ctx??=new (window.AudioContext||window.webkitAudioContext)();await this.ctx.resume();}else this.stop();return this.enabled;}
 stop(){for(const n of this.nodes){try{n.stop();}catch{}}this.nodes.clear();}
 tone(freq,duration=.22,type='sine',volume=.06,delay=0,end=null){if(!this.enabled||!this.ctx||this.ctx.state!=='running')return;const now=this.ctx.currentTime+delay,o=this.ctx.createOscillator(),g=this.ctx.createGain();o.type=type;o.frequency.setValueAtTime(freq,now);if(end)o.frequency.exponentialRampToValueAtTime(end,now+duration);g.gain.setValueAtTime(0,now);g.gain.linearRampToValueAtTime(volume,now+.014);g.gain.exponentialRampToValueAtTime(.0001,now+duration);o.connect(g);g.connect(this.ctx.destination);o.start(now);o.stop(now+duration+.03);this.nodes.add(o);o.onended=()=>{this.nodes.delete(o);o.disconnect();g.disconnect();};}
 cue(name){
  if(name==='select')this.tone(650,.06,'sine',.025);
  if(name==='fire'){this.tone(220,.35,'triangle',.05,0,880);this.tone(440,.3,'sine',.04,.12,1100);}
  if(name==='water'){[523,784,1046].forEach((f,i)=>this.tone(f,.25,'sine',.045,i*.09));}
  if(name==='grass'){[392,494,587].forEach((f,i)=>this.tone(f,.45,'triangle',.04,i*.08));}
  if(name==='heal'){[523,659,784,1046].forEach((f,i)=>this.tone(f,.55,'sine',.04,i*.12));}
  if(name==='guard'){this.tone(260,.45,'sine',.05);this.tone(520,.45,'triangle',.03);}
  if(name==='change'){[392,523,784].forEach((f,i)=>this.tone(f,.6,'triangle',.04,i*.1));}
  if(name==='hit')this.tone(160,.14,'triangle',.07,0,55);
  if(name==='break'){[880,1174,1568].forEach((f,i)=>this.tone(f,.35,'sine',.07,i*.065));}
  if(name==='victory'||name==='ending'){[392,523,659,784,1046].forEach((f,i)=>this.tone(f,.75,'triangle',.06,i*.12));}
  if(name==='defeat')this.tone(170,.8,'triangle',.06,0,80);
 }
 update(dt,battle){if(!this.enabled)return;this.elapsed+=dt;if(this.elapsed<.42)return;this.elapsed=0;const notes=battle?[146.8,220,293.6,349.2,293.6,220,174.6,220]:[196,293.6,392,440,392,293.6,220,293.6];this.tone(notes[this.beat++%notes.length],1.25,'sine',.018);if(this.beat%8===0)this.tone(battle?73.4:98,2.8,'sine',.03);}
}
```

## main.mjs

```javascript
import {validateSettings,createRun,createBattle,intentFor,resolveTurn,finishBattle,canTravel,movePoint,elements,commands,elementInfo,weaknessOf,enemyElement,affinity,nextSelection} from './core.mjs';
import {icon} from './icons.mjs';
import {ForestAudio} from './audio.mjs';
import {world} from './world.mjs';
const $=id=>document.getElementById(id),text=(id,value)=>$(id).textContent=value;
const s=JSON.parse($('game-settings').textContent),assets=JSON.parse($('game-assets').textContent),atlas=JSON.parse($('game-atlas').textContent);
const faults=validateSettings(s),keys=new Set(),audio=new ForestAudio();
const reduced=matchMedia('(prefers-reduced-motion: reduce)').matches;
let scene,run=createRun(),battle=null,mode='loading',paused=false,selection=0,dialogueIndex=0,loaded=false;
const controls=['ArrowLeft','ArrowRight','ArrowUp','ArrowDown','Space','Enter','Escape','KeyM'];
function fail(message){text('error-message',message);$('error').hidden=false;$('start').disabled=true;mode='error';}
function focusGame(){$('game').focus({preventScroll:true});}
function show(...ids){for(const id of ['intro','map-hud','battle-hud','dialogue','result','ending'])$(id).hidden=!ids.includes(id);}
function audit(){const element=battle?enemyElement(s,battle):s.areas[run.area].enemy.element;Object.assign($('game').dataset,{mode,paused:String(paused),area:String(run.area),x:run.x.toFixed(1),y:run.y.toFixed(1),defeated:run.defeated.join(','),turn:String(battle?.turn??0),heroHp:String(battle?.heroHp??s.hero.hp),enemyHp:String(battle?.enemyHp??0),heals:String(battle?.heals??s.spells.heal.uses),element,weakness:weaknessOf[element],intent:battle&&!battle.result?intentFor(s,battle).kind:'',selection:commands[selection],retries:String(run.retries),loaded:String(loaded)});}
function select(index){selection=index;for(const [i,id]of commands.entries()){$(id).classList.toggle('selected',i===index);$(id).setAttribute('aria-pressed',String(i===index));}audit();}
function toast(message){text('toast',message);$('toast').classList.add('visible');scene.time.delayedCall(2300,()=>$('toast').classList.remove('visible'));}
function pause(value){if(!loaded||paused===value||['intro','clear','error'].includes(mode))return;paused=value;keys.clear();$('paused').hidden=!paused;audio.stop();if(paused)scene.scene.pause();else {scene.scene.resume();focusGame();}audit();}
function action(code){
 if(code==='KeyM'){toggleSound();return;}if(code==='Escape'){pause(!paused);return;}if(paused)return;
 if(mode==='intro'&&(code==='Space'||code==='Enter')){start();return;}
 if(mode==='map'&&(code==='Space'||code==='Enter')){const a=s.areas[run.area];if(Math.hypot(run.x-a.inspectAt.x,run.y-a.inspectAt.y)<130){dialogueIndex=0;mode='dialogue';show('map-hud','dialogue');text('dialogue-speaker',a.landmark);text('dialogue-text',a.inspect[0]);keys.clear();audit();}else toast('魔物に近づくと、戦闘が始まります。');return;}
 if(mode==='dialogue'&&(code==='Space'||code==='Enter')){const lines=s.areas[run.area].inspect;dialogueIndex++;if(dialogueIndex>=lines.length){mode='map';show('map-hud');}else text('dialogue-text',lines[dialogueIndex]);audit();return;}
 if(mode==='choose'){if(code.startsWith('Arrow')){select(nextSelection(selection,code));audio.cue('select');}if(code==='Space'||code==='Enter')cast(commands[selection]);return;}
 if((mode==='victory'||mode==='defeat')&&(code==='Space'||code==='Enter'))resultNext();
}
function start(){if(!loaded)return;run=createRun();battle=null;keys.clear();$('pause').disabled=false;scene.renderMap();focusGame();}
async function toggleSound(){try{const on=await audio.toggle();text('sound',on?'音 ON':'音 OFF');$('sound').setAttribute('aria-label',on?'音をオフにする':'音をオンにする');}catch{toast('このブラウザでは音を開始できませんでした。');}}
function syncBattle(){
 const e=s.areas[battle.area].enemy,intent=intentFor(s,battle),element=enemyElement(s,battle),weak=weaknessOf[element];
 text('battle-location',s.areas[run.area].name);text('turn-number',`TURN ${battle.turn+1}`);text('hero-name',s.identity.heroName);text('enemy-name',e.name);
 text('hero-hp-text',`${battle.heroHp} / ${s.hero.hp}`);text('enemy-hp-text',`${battle.enemyHp} / ${e.hp}`);$('hero-hp').style.width=battle.heroHp/s.hero.hp*100+'%';$('enemy-hp').style.width=battle.enemyHp/e.hp*100+'%';
 text('heals-left',`あと${battle.heals}回`);text('heal-detail',battle.heals===0?'使い切った':battle.heroHp===s.hero.hp?'体力は満タン':`HP +${s.spells.heal.amount} ／ あと${battle.heals}回`);
 $('enemy-element').innerHTML=icon(element)+`<b>${elementInfo[element].name}属性</b>`;$('enemy-element').style.color=elementInfo[element].color;
 $('enemy-weakness').innerHTML=`弱点 ${icon(weak)}<b>${elementInfo[weak].name}</b>`;$('enemy-weakness').style.color=elementInfo[weak].color;
 const upcoming=(e.phases??[]).find(p=>battle.enemyHp/e.hp>p.at);text('phase-hint',upcoming?`HP ${Math.round(upcoming.at*100)}%で ${elementInfo[upcoming.element].name}属性へ`:'');
 const busy=mode!=='choose';for(const id of commands){$(id).disabled=busy||(id==='heal'&&(battle.heals===0||battle.heroHp===s.hero.hp));if(elements.includes(id)){const match=affinity(id,element);$(id).dataset.affinity=match;text(id+'-detail',match==='weak'?'弱点！ よく効く':match==='resist'?'効きにくい':'いつもの威力');}}
 text('choose-label',busy?'コマンドを実行中…':'どうする？');if(!busy){const small=document.createElement('small');small.textContent='矢印で選ぶ ／ SPACE 決定';$('choose-label').append(small);}
 $('intent').className='intent '+intent.kind;
 text('intent-label',`${intent.kind==='focus'?'！':intent.kind==='guard'?'◇':'›'} 次の動き：${intent.name}`);
 text('intent-hint',intent.kind==='focus'?`大技 ${intent.damage}ダメージ。「まもる」で ${Math.round(intent.damage*s.spells.guard.damageMultiplier)}に。`:intent.kind==='guard'?`攻撃が弱まる。回復もチャンス。反撃 ${intent.damage}。`:`このあと ${intent.damage} ダメージの攻撃。`);audit();
}
function encounter(){if(mode!=='map')return;mode='transition';keys.clear();audit();scene.cameras.main.fadeOut(230,9,24,27);scene.time.delayedCall(240,()=>{battle=createBattle(s,run.area);scene.renderBattle();scene.cameras.main.fadeIn(380,9,24,27);});}
function cast(spell){
 if(paused||mode!=='choose')return;
 if(spell==='heal'&&(battle.heals===0||battle.heroHp===s.hero.hp)){toast(battle.heals===0?'回復は使い切った。攻撃と「まもる」は何度でも使える。':'体力は満タン。回復の回数は減らない。');return;}
 const result=resolveTurn(s,battle,spell);mode='casting';keys.clear();syncBattle();text('battle-message',s.spells[spell].name);scene.heroPose('cast');
 if(spell==='guard'||spell==='heal'){
  scene.supportEffect(result);audio.cue(spell);
  scene.time.delayedCall(350,()=>{if(result.healed){battle.heroHp+=result.healed;battle.heals=result.next.heals;syncBattle();scene.number(307,302,'+'+result.healed,'#b8f7c1','回復');}});
  scene.time.delayedCall(800,()=>{scene.heroPose('idle');text('battle-message',result.intent.name);scene.enemyAttack(result,()=>completeTurn(result));});return;
 }
 const powerful=result.match==='weak',color=Phaser.Display.Color.HexStringToColor(elementInfo[spell].color).color,sx=395,sy=322,tx=930,ty=374;
 const casting=scene.add.image(sx,sy,'glow').setTint(color).setBlendMode(Phaser.BlendModes.ADD).setAlpha(.7).setDepth(30).setScale(.2);
 scene.tweens.add({targets:casting,scale:powerful?2.3:1.1,alpha:1,duration:powerful?460:220,ease:'Cubic.Out'});audio.cue(spell);
 if(powerful){scene.ring(307,519,130,color,560);scene.burst(350,400,color,20,130);}
 scene.time.delayedCall(powerful?440:210,()=>{
  scene.tweens.add({targets:casting,x:tx,y:ty,duration:powerful?280:240,ease:'Cubic.In',onUpdate:()=>{const p=scene.add.image(casting.x,casting.y,'spark').setTint(color).setScale(powerful?1.6:.7).setDepth(29);scene.tweens.add({targets:p,alpha:0,scale:0,duration:300,onComplete:()=>p.destroy()});},onComplete:()=>{
   casting.destroy();scene.impact(result);battle.enemyHp=result.next.enemyHp;syncBattle();
  }});
 });
 const impactTime=powerful?720:450;
 scene.time.delayedCall(impactTime+360,()=>{
  scene.heroPose('idle');
  if(result.next.enemyHp===0){completeTurn(result);return;}
  text('battle-message',result.intent.name);scene.enemyAttack(result,()=>completeTurn(result));
 });
}
function completeTurn(result){
 battle=result.next;run.turns++;if(result.match==='weak')run.weakHits++;text('battle-message','');scene.guardShield?.destroy();scene.guardShield=null;
 if(battle.result){mode=battle.result;syncBattle();keys.clear();scene.endFight(battle.result);}
 else {mode='choose';syncBattle();scene.enemyAura();if(result.elementChanged){scene.elementShift(result.nextElement);}}
 audit();
}
function resultNext(){
 if(paused)return;
 if(mode==='defeat'){run.retries++;battle=createBattle(s,run.area);scene.renderBattle();focusGame();}
 else if(mode==='victory'){
  // The encounter position is already walkable. Keep both coordinates: moving
  // only x to the far side of an enemy can strand the hero outside a bent path.
  run=finishBattle(run,battle);
  if(run.area===2)scene.restoreForest();else scene.renderMap();
  focusGame();
 }
}
class ForestScene extends Phaser.Scene{
 constructor(){super('Forest');this.ambient=[];this.walkTime=0;this.mapCooldown=0;}
 preload(){for(const [name,src]of Object.entries(assets))this.load.image(name,src);for(const [name,src]of Object.entries(s.images))if(src)this.load.image('custom-'+name,src);this.load.on('loaderror',file=>fail('画像が読み込めません：'+file.key));}
 create(){
  scene=this;if(mode==='error')return;
  for(const [sheet,frames]of Object.entries(atlas))for(const [name,f]of Object.entries(frames)){const texture=this.textures.get(sheet),image=texture.getSourceImage();if(f.x+f.w>image.width||f.y+f.h>image.height){fail(sheet+' の切り出し範囲を確認してください');return;}texture.add(name,0,f.x,f.y,f.w,f.h);}
  for(const [name,key]of [['hero','frame'],['heroMap','mapFrame']])if(s.images[name]&&s.hero[key]){const f=s.hero[key],texture=this.textures.get('custom-'+name),image=texture.getSourceImage();if(f.x+f.w>image.width||f.y+f.h>image.height){fail('主人公画像の切り出し範囲が画像サイズを超えています');return;}texture.add('custom-frame',0,f.x,f.y,f.w,f.h);}
  const glow=this.textures.createCanvas('glow',128,128),ctx=glow.context,g=ctx.createRadialGradient(64,64,0,64,64,64);g.addColorStop(0,'rgba(255,255,255,1)');g.addColorStop(.16,'rgba(255,255,255,.75)');g.addColorStop(.55,'rgba(255,255,255,.18)');g.addColorStop(1,'rgba(255,255,255,0)');ctx.fillStyle=g;ctx.fillRect(0,0,128,128);glow.refresh();
  const spark=this.add.graphics().fillStyle(0xffffff).fillCircle(5,5,5);spark.generateTexture('spark',10,10);spark.destroy();
  loaded=true;mode='intro';this.renderIntro();$('start').disabled=false;text('start','冒険をはじめる →');text('brand',s.identity.title);$('title').replaceChildren();s.identity.titleLines.forEach((line,i)=>{if(i)$('title').append(document.createElement('br'));$('title').append(document.createTextNode(line));});text('subtitle',s.identity.subtitle);text('intro-note',s.identity.intro);
  for(const id of commands){text(id+'-name',s.spells[id].name);$(id).querySelector('.spell-symbol').innerHTML=icon(id);$(id).style.setProperty('--element',elementInfo[id]?.color??(id==='heal'?'#b6edc5':'#dfdfc0'));}
  document.title=s.identity.title;document.documentElement.style.setProperty('--accent',s.theme.accent);audit();
 }
 clear(){this.tweens.killAll();this.time.removeAllEvents();this.children.removeAll(true);this.ambient=[];this.hero=null;this.enemy=null;this.aura=null;this.marker=null;this.heroShadow=null;this.enemyShadow=null;this.guardShield=null;this.cameras.main.resetFX();$('toast').classList.remove('visible');$('phase-notice').classList.remove('visible');}
 picture(x,y,key,frame,height){const pic=this.add.image(x,y,key,frame).setOrigin(.5,1);pic.setScale(height/pic.height);return pic;}
 background(key,frame){return this.add.image(640,360,key,frame).setDisplaySize(1280,720).setDepth(-10);}
 heroTexture(map=false,pose='idle'){const custom=s.images[map?'heroMap':'hero'];if(custom)return ['custom-'+(map?'heroMap':'hero'),s.hero[map?'mapFrame':'frame']?'custom-frame':undefined];if(map&&s.images.hero)return ['custom-hero',s.hero.frame?'custom-frame':undefined];return ['hero',map?'down':pose];}
 enemyTexture(hurt=false){const id=['forestEnemy','waterEnemy','boss'][run.area];return s.images[id]?['custom-'+id,undefined]:['enemies',hurt?'hurt':['forest','water','boss'][run.area]];}
 heroPose(pose){const h=this.hero;if(!h)return;const height=s.hero.battleHeight;h.setTexture(...this.heroTexture(false,pose));h.setScale(height/h.height);h.setOrigin(.5,1);}
 ambience(count=32){for(let i=0;i<count;i++){const p=this.add.image(Phaser.Math.Between(25,1250),Phaser.Math.Between(140,590),'glow').setTint(0xf5dea0).setBlendMode(Phaser.BlendModes.ADD).setScale(Phaser.Math.FloatBetween(.035,.105)).setDepth(15);this.ambient.push({p,x:p.x,y:p.y,phase:i*1.76,rate:.25+Math.random()*.4});}}
 renderIntro(){this.clear();mode='intro';show('intro');this.background(s.images.battle?'custom-battle':'battle');this.heroShadow=this.add.ellipse(945,636,260,36,0x001916,.48);this.hero=this.picture(955,653,...this.heroTexture(false),466).setDepth(5).setFlipX(Boolean(s.images.hero)&&s.hero.faces==='left');this.ambience(34);audit();}
 renderMap(){
  this.clear();mode='map';show('map-hud');keys.clear();battle=null;this.mapCooldown=.65;
  const a=s.areas[run.area],mapKey=['mapEntrance','mapPond','mapTree'][run.area];
  this.background(s.images[mapKey]?'custom-'+mapKey:'maps',s.images[mapKey]?undefined:['entrance','pond','tree'][run.area]);
  this.add.rectangle(640,75,1280,150,0x061e22,.28).setDepth(-8);
  this.heroShadow=this.add.ellipse(run.x,run.y,59,18,0x001915,.5).setDepth(2);this.hero=this.picture(run.x,run.y,...this.heroTexture(true),s.hero.mapHeight).setDepth(8);
  if(!run.defeated.includes(run.area)){this.enemyShadow=this.add.ellipse(a.enemy.x,a.enemy.y+3,95,23,0x0c1827,.5).setDepth(2);this.enemy=this.picture(a.enemy.x,a.enemy.y,...this.enemyTexture(),run.area===2?145:run.area===1?103:83).setDepth(7);this.enemyBaseY=a.enemy.y;this.enemyBaseX=a.enemy.x;this.enemyBaseScale=this.enemy.scale;this.add.image(a.enemy.x,a.enemy.y-30,'glow').setTint(run.area===1?0x4bcadd:0xbe9449).setScale(1.2).setAlpha(.12).setBlendMode(Phaser.BlendModes.ADD).setDepth(3);}
  this.marker=this.add.text(a.inspectAt.x,a.inspectAt.y-35,'✧',{fontSize:'25px',color:'#fff0b4',shadow:{color:'#112f2c',blur:8,fill:true}}).setOrigin(.5).setDepth(10);
  text('area-number',`0${run.area+1} / 小さな森の旅`);text('area-name',a.name);text('area-note',run.defeated.includes(run.area)?'この場所に、小さな光が戻った。':a.note);text('journey-dots',[0,1,2].map(i=>run.defeated.includes(i)?'✦':'◇').join(' '));$('exit-left').hidden=run.area===0;$('exit-right').hidden=run.area===2;text('exit-right',run.defeated.includes(run.area)?'次のエリア →':'影の向こうへ →');
  this.ambience(22);audit();
 }
 renderBattle(){
  this.clear();mode='choose';show('battle-hud');selection=0;select(0);text('battle-message','');
  this.background(s.images.battle?'custom-battle':'battle');
  if(run.area===1)this.add.rectangle(640,300,1280,600,0x12657c,.12).setDepth(-8);
  if(run.area===2)this.add.rectangle(640,300,1280,600,0x061d36,.2).setDepth(-8);
  this.heroShadow=this.add.ellipse(310,520,230,34,0x001918,.55).setDepth(1);this.enemyShadow=this.add.ellipse(936,516,run.area===2?285:220,38,0x001215,.7).setDepth(1);
  this.hero=this.picture(305,526,...this.heroTexture(false),s.hero.battleHeight).setDepth(5).setFlipX(Boolean(s.images.hero)&&s.hero.faces==='left');this.enemy=this.picture(937,514,...this.enemyTexture(),s.areas[run.area].enemy.height).setDepth(5);this.enemyBaseY=514;this.enemyBaseX=937;this.enemyBaseScale=this.enemy.scale;
  this.ambience(25);this.enemyAura();syncBattle();audit();
 }
 enemyAura(){if(this.aura)this.aura.destroy();const kind=intentFor(s,battle).kind,color=Phaser.Display.Color.HexStringToColor(elementInfo[enemyElement(s,battle)].color).color;this.aura=this.add.image(937,398,'glow').setTint(color).setScale(kind==='attack'?2:3).setAlpha(kind==='attack'?.1:.3).setBlendMode(Phaser.BlendModes.ADD).setDepth(4);}
 elementShift(element){
  const info=elementInfo[element],color=Phaser.Display.Color.HexStringToColor(info.color).color;
  this.ring(937,500,190,color,700);this.burst(937,400,color,34,170);audio.cue('change');
  $('phase-notice').innerHTML=icon(element)+`${info.name}属性に変わった！ <small>弱点は${elementInfo[weaknessOf[element]].name}</small>`;
  $('phase-notice').style.color=info.color;$('phase-notice').classList.add('visible');this.time.delayedCall(2400,()=>$('phase-notice').classList.remove('visible'));
 }
 supportEffect(r){
  if(r.spell==='guard'){
   this.guardShield=this.add.ellipse(309,388,265,305,0x9bdaf1,.13).setStrokeStyle(4,0xc5f0f4,.85).setDepth(31);
   this.tweens.add({targets:this.guardShield,alpha:.6,duration:350,yoyo:true,repeat:2});this.ring(307,519,135,0xc5f0f4,600);
  }else{
   this.ring(307,519,120,0xb7f5b0,750);this.burst(307,440,0xb7f5b0,25,110);
   const glow=this.add.image(307,399,'glow').setTint(0xb7f5b0).setScale(3).setAlpha(.55).setBlendMode(Phaser.BlendModes.ADD).setDepth(30);
   this.tweens.add({targets:glow,y:300,alpha:0,duration:850,onComplete:()=>glow.destroy()});
  }
 }
 burst(x,y,color,count=20,radius=100){for(let i=0;i<count;i++){const a=Math.PI*2*i/count+Math.random()*.15,d=radius*(.3+Math.random()*.7),p=this.add.image(x,y,'spark').setTint(color).setScale(.5+Math.random()*1.4).setBlendMode(Phaser.BlendModes.ADD).setDepth(40);this.tweens.add({targets:p,x:x+Math.cos(a)*d,y:y+Math.sin(a)*d,alpha:0,scale:0,duration:400+Math.random()*450,ease:'Cubic.Out',onComplete:()=>p.destroy()});}}
 ring(x,y,r,color,duration=500){const g=this.add.graphics().lineStyle(3,color,.85).strokeCircle(0,0,r).setPosition(x,y).setDepth(35).setScale(.2,.1);this.tweens.add({targets:g,scaleX:1.25,scaleY:.4,alpha:0,duration,ease:'Cubic.Out',onComplete:()=>g.destroy()});}
 number(x,y,value,color='#fff1c5',label=''){const t=this.add.text(x,y,String(value),{fontFamily:'Georgia, serif',fontSize:'58px',fontStyle:'bold',color,stroke:'#17302a',strokeThickness:6}).setOrigin(.5).setDepth(70);this.tweens.add({targets:t,y:y-45,alpha:0,delay:300,duration:650,ease:'Cubic.Out',onComplete:()=>t.destroy()});if(label){const l=this.add.text(x,y+46,label,{fontFamily:'sans-serif',fontSize:'21px',color,stroke:'#17302a',strokeThickness:5}).setOrigin(.5).setDepth(70);this.tweens.add({targets:l,y:y+15,alpha:0,delay:500,duration:500,onComplete:()=>l.destroy()});}}
 impact(r){
  const strong=r.match==='weak',color=Phaser.Display.Color.HexStringToColor(elementInfo[r.spell].color).color;this.burst(930,384,color,strong?42:17,strong?180:90);this.ring(930,442,strong?155:80,color);audio.cue(strong?'break':'hit');
  this.enemy.setTintFill(0xffecc1);this.time.delayedCall(100,()=>this.enemy?.clearTint());this.tweens.add({targets:this.enemy,x:957,duration:80,yoyo:true,repeat:1});if(strong&&!reduced)this.cameras.main.shake(140,.004);
  if(strong&&run.area===2&&!s.images.boss){this.enemy.setTexture('enemies','hurt');this.enemy.setScale(s.areas[2].enemy.height/this.enemy.height);this.time.delayedCall(520,()=>{if(this.enemy){this.enemy.setTexture(...this.enemyTexture());this.enemy.setScale(this.enemyBaseScale);}});}
  this.number(930,300,r.damage,elementInfo[r.spell].color,[strong?'弱点！':r.match==='resist'?'効きにくい':'',r.guarded?'バリアで軽減':''].filter(Boolean).join(' '));
 }
 enemyAttack(r,done){
  const watery=run.area===1,color=watery?0x8ce5ed:0xb08bbc,originX=this.enemy.x;
  this.tweens.add({targets:this.enemy,x:originX-48,angle:-4,duration:180,yoyo:true,ease:'Quad.Out'});
  if(watery||r.intent.kind==='focus'){
   const orb=this.add.image(855,380,'glow').setTint(color).setScale(r.intent.kind==='focus'?2.4:1.4).setDepth(30).setBlendMode(Phaser.BlendModes.ADD);
   this.tweens.add({targets:orb,x:306,y:390,duration:340,ease:'Cubic.In',onComplete:()=>orb.destroy()});
  }else {const slash=this.add.graphics().lineStyle(8,color,.9).beginPath().moveTo(353,328).lineTo(260,466).strokePath().setDepth(30).setAlpha(0);this.tweens.add({targets:slash,alpha:1,delay:220,duration:80,yoyo:true,hold:75,onComplete:()=>slash.destroy()});}
  this.time.delayedCall(340,()=>{this.burst(304,408,r.spell==='guard'?0xb6e9f3:color,18,105);this.hero.setTintFill(r.spell==='guard'?0xb6e9f3:0xffc2a8);this.time.delayedCall(100,()=>this.hero?.clearTint());this.tweens.add({targets:this.hero,x:r.spell==='guard'?300:286,duration:75,yoyo:true,repeat:1});audio.cue('hit');this.number(307,302,r.enemyDamage,r.spell==='guard'?'#b6e9f3':'#ffc6a9',r.spell==='guard'?'ガード！':'');battle.heroHp=r.next.heroHp;syncBattle();});
  this.time.delayedCall(920,done);
 }
 endFight(result){
  if(result==='victory'){audio.cue('victory');this.burst(937,391,0xffecb0,60,210);this.tweens.add({targets:this.enemy,alpha:0,y:this.enemy.y-45,duration:720});if(this.aura)this.tweens.add({targets:this.aura,alpha:0,duration:400});}
  else {audio.cue('defeat');this.tweens.add({targets:this.hero,alpha:.45,angle:-8,duration:500});}
  // A short non-interactive interval prevents the killing keypress from skipping results.
  mode='transition';audit();this.time.delayedCall(900,()=>{mode=result;show('result');text('result-kicker',result==='victory'?'A LITTLE LIGHT RETURNS':'ONE MORE TRY');text('result-title',result==='victory'?'影を、こえた。':'もう一度、ここから。');text('result-note',result==='victory'?(run.area===2?'大樹の奥で、小さな光が動きはじめた。':'次の戦闘は、体力も回復の回数も満タン。\n森の奥へ進もう。'):'体力も、回復の回数も、元どおり。\n弱点・まもる・回復で、もう一度。');text('result-next',result==='victory'?(run.area===2?'大樹に光を →':'森へ戻る →'):'この戦闘に再挑戦 →');keys.clear();audit();});
 }
 restoreForest(){
  this.clear();mode='restoring';show();$('pause').disabled=false;this.background(s.images.battle?'custom-battle':'battle');const restored=this.background('awakened').setAlpha(0).setDepth(-9);this.ambience(65);audio.cue('ending');
  const seed=this.add.image(970,298,'glow').setTint(0xffd780).setScale(.1).setBlendMode(Phaser.BlendModes.ADD).setDepth(4);this.tweens.add({targets:seed,scale:7,alpha:.3,duration:1800,ease:'Sine.InOut'});this.tweens.add({targets:restored,alpha:1,delay:450,duration:2300});this.time.delayedCall(800,()=>this.burst(970,300,0xffe5a6,85,350));
  this.time.delayedCall(2900,()=>{mode='clear';show('ending');$('ending-title').replaceChildren();s.identity.endingLines.forEach((line,i)=>{if(i)$('ending-title').append(document.createElement('br'));$('ending-title').append(document.createTextNode(line));});$('ending-title').setAttribute('aria-label',s.identity.ending);text('end-stats',`${run.battles}つの影をこえて ／ 弱点をついた回数 ${run.weakHits}回`);$('pause').disabled=true;keys.clear();audit();});audit();
 }
 update(time,delta){
  if(!loaded||paused||mode==='error')return;const dt=Math.min(delta/1000,.05);audio.update(dt,['choose','casting'].includes(mode));
  for(const a of this.ambient){a.p.x=a.x+Math.sin(time*.0004+a.phase)*28;a.p.y=a.y+Math.cos(time*.0003+a.phase)*18;a.p.alpha=.3+Math.sin(time*.001*a.rate+a.phase)*.25;}
  if(this.enemy&&['choose','map'].includes(mode)){this.enemy.y=this.enemyBaseY+Math.sin(time*.002)*(run.area===1?8:2.5);this.enemy.scaleY=this.enemyBaseScale*(1+Math.sin(time*.0018)*.016);}
  if(this.aura)this.aura.alpha=.22+Math.sin(time*.003)*.07;
  if(mode==='intro'&&this.hero)this.hero.y=653+Math.sin(time*.0015)*3;
  if(mode!=='map')return;this.mapCooldown=Math.max(0,this.mapCooldown-dt);const a=s.areas[run.area],dx=Number(keys.has('ArrowRight'))-Number(keys.has('ArrowLeft')),dy=Number(keys.has('ArrowDown'))-Number(keys.has('ArrowUp'));
  if(dx||dy){const p=movePoint(run.x,run.y,dx,dy,dt,s.hero.speed,a.obstacles,world[run.area].walkable);run.x=p.x;run.y=p.y;this.walkTime+=dt;const texture=this.heroTexture(true);if(!s.images.heroMap&&!s.images.hero)texture[1]=dy<0?'up':'down';this.hero.setTexture(...texture);this.hero.setScale(s.hero.mapHeight/this.hero.height);this.hero.setFlipX(dx<0);this.hero.angle=Math.sin(this.walkTime*15)*2.5;this.hero.x=run.x;this.hero.y=run.y-Math.abs(Math.sin(this.walkTime*15))*4;this.heroShadow.setPosition(run.x,run.y);this.hero.setDepth(5+run.y/1000);}
  else if(this.hero){this.hero.angle=0;this.hero.y=run.y;}
  if(this.enemy)this.enemy.setDepth(5+a.enemy.y/1000);
  const close=Math.hypot(run.x-a.inspectAt.x,run.y-a.inspectAt.y)<130;this.marker.setAlpha(close?1:.55);text('map-tip',close?`SPACE  ${a.landmark}を調べる`:run.defeated.includes(run.area)?'矢印キーで、森の奥へ。':'矢印キーで移動。魔物に触れると戦闘へ。');
  if(this.mapCooldown===0&&this.enemy&&Math.hypot(run.x-a.enemy.x,run.y-a.enemy.y)<61){encounter();return;}
  if(this.mapCooldown===0&&run.x>1180){if(canTravel(run,1)){run.area++;Object.assign(run,world[run.area].left);this.renderMap();this.cameras.main.fadeIn(260,10,25,28);}else if(run.area<2){run.x=1175;this.mapCooldown=1.5;toast('この場所の影をこえて、先へ進もう。');}}
  else if(this.mapCooldown===0&&run.x<85&&canTravel(run,-1)){run.area--;Object.assign(run,world[run.area].right);this.renderMap();this.cameras.main.fadeIn(260,10,25,28);}audit();
 }
}
if(faults.length)fail('設定を確認してください：'+faults.join(' / '));
else {
 // ResizeObserver keeps canvas and HTML commands on the same logical 1280×720 grid.
 new ResizeObserver(()=>{$('interface').style.transform=`scale(${$('stage').clientWidth/1280})`;}).observe($('stage'));
 new Phaser.Game({type:Phaser.CANVAS,width:1280,height:720,parent:'game',backgroundColor:'#172b2e',transparent:false,antialias:true,roundPixels:false,input:{keyboard:false},scale:{mode:Phaser.Scale.FIT,autoCenter:Phaser.Scale.CENTER_BOTH},scene:ForestScene,audio:{noAudio:true},banner:false,fps:{target:60,forceSetTimeOut:false}});
 window.addEventListener('keydown',event=>{if(!controls.includes(event.code)||/INPUT|TEXTAREA|SELECT/.test(event.target.tagName))return;event.preventDefault();if(event.repeat||keys.has(event.code))return;keys.add(event.code);action(event.code);});
 window.addEventListener('keyup',event=>{keys.delete(event.code);if(controls.includes(event.code))event.preventDefault();});
 window.addEventListener('blur',()=>{keys.clear();pause(true);});document.addEventListener('visibilitychange',()=>{if(document.hidden)pause(true);});
 $('game').addEventListener('pointerdown',focusGame);$('start').onclick=start;$('sound').onclick=toggleSound;$('pause').onclick=()=>pause(!paused);$('resume').onclick=()=>pause(false);$('result-next').onclick=resultNext;$('replay').onclick=start;
 for(const [i,id]of commands.entries())$(id).onclick=()=>{select(i);cast(id);focusGame();};
 $('full').onclick=async()=>{try{if(document.fullscreenElement)await document.exitFullscreen();else await $('stage').requestFullscreen();}catch{toast('全画面は、このブラウザでは使えません。');}};
 document.querySelectorAll('[data-key]').forEach(b=>{const end=()=>keys.delete(b.dataset.key);b.addEventListener('pointerdown',e=>{e.preventDefault();b.setPointerCapture(e.pointerId);keys.add(b.dataset.key);action(b.dataset.key);});b.addEventListener('pointerup',end);b.addEventListener('pointercancel',end);b.addEventListener('lostpointercapture',end);});
}
$('reload').onclick=()=>location.reload();
```

