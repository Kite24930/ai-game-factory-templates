# 制作例の参考コード

この資料は、参加者の希望に合った新しいゲームを作るための技術参照です。画像のbase64とゲームエンジン本体を除き、見本の設定・アプリ側コードをまとめています。コードや素材の再利用は可能ですが、見本と同じ世界・機能数・設定項目だけに制作を限定しません。

これは実行用パッケージではありません。`shell.html` の置換用プレースホルダー、元の相対import、画像名などは見本の組み立て構造を示します。この文書をそのままCanvasへ貼るだけでは動きません。作るゲームに合わせて画像・ライブラリー・HTML要素を接続してください。子どもにファイルの分割・ビルドを求める資料ではありません。

使用エンジンは Three.js（MIT）。ライブラリー本体と既存のライセンス全文・著作権表示は、対応する制作例HTMLに内包されています。ライブラリーを再利用する際は通知を維持してください。画像本体はHTMLの中にあり、この資料に画像を取得できたという保証は含みません。アプリ側コード・画像に新しい包括ライセンスを付けたものではありません。

見本の入力チェックや固定の個数は、その見本用です。新しい機能や構成を作るときは関連するルール・描画・検証を合わせて変更できます。

## settings.json

```json
{
  "version": 1,
  "identity": {
    "id": "shimairokart",
    "title": "しまいろカート",
    "subtitle": "SHIMAIRO KART",
    "eyebrow": "海と、風と、最後の直線。",
    "description": "カーブで少しゆるめて、\nまっすぐになったら、思いきり。",
    "startLabel": "レースをはじめる",
    "courseName": "しおかぜアイランド"
  },
  "race": {
    "laps": 2,
    "maxSpeed": 36,
    "acceleration": 11,
    "braking": 19,
    "steering": 28,
    "wheelbase": 2.8,
    "speedSteeringFalloff": 3.5,
    "steerResponse": 9,
    "steerSlowdown": 0.08,
    "steerDeceleration": 4,
    "wallImpact": 0.58,
    "wallDrag": 2.8,
    "wallSteeringSpeed": 7,
    "rivals": [
      {"name": "ソラ", "color": "#2a9dce", "helmet": "#edf8e9", "lane": 1.6, "start": 7, "lookAhead": 30, "cornerMargin": 1.08, "decisionPeriod": 0.07},
      {"name": "ヒナ", "color": "#efb63c", "helmet": "#fff2c7", "lane": -1.6, "start": 13, "lookAhead": 35, "cornerMargin": 1.15, "decisionPeriod": 0.09},
      {"name": "モリ", "color": "#61a477", "helmet": "#f0efd7", "lane": 1.6, "start": 19, "lookAhead": 38, "cornerMargin": 1.21, "decisionPeriod": 0.08}
    ]
  },
  "items": {
    "enabled": true,
    "seed": 73,
    "rows": [0.07, 0.23, 0.40, 0.56, 0.74, 0.9],
    "lanes": [-4.8, -1.6, 1.6, 4.8],
    "pickupRadius": 1.6,
    "respawnSeconds": 2.4,
    "boostSeconds": 1.8,
    "boostMultiplier": 1.22,
    "shieldSeconds": 7,
    "shotRange": 95,
    "shotSpeed": 72,
    "shotWarning": 0.65,
    "slowSeconds": 1.25,
    "slowMultiplier": 0.58,
    "hitImmunity": 2.1
  },
  "hero": {
    "name": "あなた",
    "kartColor": "#ee6244",
    "accentColor": "#ffe8b2",
    "furColor": "#d98136",
    "helmetColor": "#fff2d7",
    "driverImage": null,
    "driverWidth": 1.15,
    "driverHeight": 1.55
  },
  "world": {
    "sky": "#71c9e3",
    "horizon": "#f7e6bc",
    "sea": "#22aaa9",
    "grass": "#86b96b",
    "sand": "#eddb9a",
    "road": "#789093",
    "curb": "#e76049",
    "sunlight": "#fff0d0",
    "seed": 73,
    "roadTexture": null
  },
  "track": {
    "width": 15,
    "points": [
      [0, 0, 0], [0, 0, -110], [0, 0.8, -225],
      [44, 2.5, -315], [126, 5, -348], [218, 6, -305],
      [252, 5.2, -218], [226, 3.8, -149], [172, 2.2, -93],
      [216, 1.2, -22], [194, 0.3, 56], [115, 0, 109],
      [32, 0, 94], [0, 0, 54]
    ],
    "sections": [
      {"at": 0, "name": "海岸ストレート"},
      {"at": 0.22, "name": "灯台カーブ"},
      {"at": 0.49, "name": "みどりのS字"},
      {"at": 0.85, "name": "ラストストレート"}
    ]
  },
  "audio": {"volume": 0.18},
  "display": {"maxPixelRatio": 1.5, "shadows": true}
}
```

## shell.html

```html
<!doctype html>
<html lang="ja"><head><meta charset="utf-8"><meta name="viewport" content="width=device-width,initial-scale=1"><title>しまいろカート</title><style>__STYLE__</style></head>
<body><main id="app">
<header><a class="wordmark" href="#" aria-label="ゲームタイトル"><span class="flag-icon" aria-hidden="true"></span><span id="brand">SHIMAIRO KART</span></a><div class="edition">A LITTLE ISLAND. A GREAT RACE.</div><nav aria-label="ゲーム操作"><button id="audio" aria-label="音をオンにする">音 OFF</button><button id="pause" aria-label="一時停止" disabled>Ⅱ</button><button id="full" aria-label="全画面にする">⛶</button></nav></header>
<section id="stage" aria-label="レーシングゲーム">
<canvas id="game" tabindex="0" aria-label="左右キーでハンドル、スペースを押してアクセル、離してブレーキ。Zでアイテム。Escapeで一時停止。"></canvas>
<div id="loading" role="status">島のレースを準備しています…</div>
<div id="intro" class="screen" hidden><div class="intro-copy"><p class="eyebrow" id="eyebrow"></p><h1 id="title"></h1><p id="description" class="description"></p><div class="race-details"><span id="course-name"></span><span id="race-format"></span></div><button id="start" class="primary">レースをはじめる <span aria-hidden="true">→</span></button><p class="controls"><span><kbd>←</kbd><kbd>→</kbd> ハンドル</span><span><kbd>SPACE</kbd> アクセル</span><span><kbd>Z</kbd> アイテム</span></p><p class="control-note">スペースを離すとブレーキ。？ボックスでアイテムをゲット！</p></div><div class="intro-stamp">SEA BREEZE<br><strong>GRAND PRIX</strong><span>走り方で、変わる。</span></div></div>
<div id="hud" hidden><div class="top-left"><div class="position"><strong id="rank">4</strong><span>位 <small id="field-size">/ 4</small></span></div><div class="lap"><span>LAP</span><b id="lap">1 / 2</b></div></div><div class="top-right"><span class="timer-label">RACE TIME</span><b id="timer">0:00.00</b><small id="best"></small></div><div class="bottom-left"><canvas id="map" width="210" height="165" aria-label="コース図"></canvas><span id="section"></span></div><div class="speed"><div><strong id="speed">0</strong><span>km/h</span></div><div class="speed-track"><i id="speed-bar"></i></div><p id="pedal">SPACE でアクセル</p></div><div id="corner" hidden><span id="turn-arrow">↱</span><div><small>この先のカーブ</small><b id="turn-label">スペースを離して減速</b></div></div></div>
<div id="item-slot" hidden><span id="effect" hidden></span><button id="item-use" disabled data-item="empty"><svg id="item-icon" viewBox="0 0 32 32" aria-hidden="true"><use href="#icon-box"/></svg><span class="item-copy"><b id="item-name">アイテム</b><small id="item-hint">？ボックスを拾おう</small></span><kbd>Z</kbd></button></div><div id="threat" role="status" hidden></div>
<div id="countdown" aria-live="polite" hidden></div><div id="toast" role="status"></div><div id="collision-flash"></div>
<div id="paused" class="screen veil" hidden><div class="panel"><p class="eyebrow">PIT STOP</p><h2>ひと休み。</h2><p>準備ができたら、続きのレースへ。</p><button id="resume" class="primary">つづける <span>→</span></button><button id="restart" class="text-button">はじめから</button></div></div>
<div id="ending" class="screen veil" hidden><div class="panel finish-panel"><p class="eyebrow">FINISH! / GOOD DRIVE.</p><h2>島を、走りきった。</h2><div class="finish-result"><div><strong id="finish-rank">1</strong><span>位</span></div><div class="finish-time"><small>YOUR TIME</small><b id="finish-time">0:00.00</b><span id="record"></span></div></div><div id="lap-times"></div><p class="finish-note">カーブを抜けた、その先へ。もう一度。</p><button id="again" class="primary">もう一度走る <span>↗</span></button></div></div>
<div id="error" class="screen veil" hidden><div class="panel"><h2>読み込みを確認してください</h2><p id="error-message"></p><button id="reload" class="primary">開き直す</button></div></div>
<div id="touch" hidden><div><button data-action="left" aria-label="左へハンドル">←</button><button data-action="right" aria-label="右へハンドル">→</button></div><button data-action="accelerate" class="accelerator">アクセル</button></div>
</section><footer><span>← → ハンドル　/　SPACE アクセル・離してブレーキ　/　Z アイテム</span><span>ESC 休けい　·　M 音</span></footer></main>
<svg class="icon-definitions" xmlns="http://www.w3.org/2000/svg" aria-hidden="true"><defs>
<symbol id="icon-boost" viewBox="0 0 32 32"><path d="M18 2 6 19h9l-1 11L27 12h-9z" fill="currentColor"/></symbol>
<symbol id="icon-shot" viewBox="0 0 32 32"><path d="m5 4 8 3M2 11l9 2M4 19l7 1" stroke="currentColor" stroke-width="3" stroke-linecap="round"/><circle cx="21" cy="20" r="9" fill="currentColor"/><circle cx="24" cy="17" r="2" fill="#fff1cf"/></symbol>
<symbol id="icon-shield" viewBox="0 0 32 32"><path d="m16 2 12 5v10c0 7-12 13-12 13S4 24 4 17V7z" fill="currentColor"/><path d="m10 16 4 4 8-9" fill="none" stroke="#fff1cf" stroke-width="3" stroke-linecap="round" stroke-linejoin="round"/></symbol>
<symbol id="icon-box" viewBox="0 0 32 32"><rect x="3" y="3" width="26" height="26" rx="5" fill="none" stroke="currentColor" stroke-width="2" stroke-dasharray="4 2"/><path d="M12 11c0-5 10-5 9 0-.2 3-5 3-5 7" fill="none" stroke="currentColor" stroke-width="3" stroke-linecap="round"/><circle cx="16" cy="23" r="1.7" fill="currentColor"/></symbol>
</defs></svg>
<script type="application/json" id="race-settings">__SETTINGS__</script><script type="application/json" id="race-assets">__ASSETS__</script><script>__RUNTIME__</script></body></html>
```

## style.css

```css
:root{color-scheme:light;--ink:#19474d;--cream:#fff1cf;--accent:#ed6c46}*{box-sizing:border-box}html,body{margin:0;min-height:100%;background:#e8ecdf}body{font-family:"Avenir Next","Hiragino Kaku Gothic ProN","Yu Gothic",sans-serif;color:var(--ink);display:grid;place-items:center;min-height:100dvh}button,a{-webkit-tap-highlight-color:transparent}button{font:inherit;cursor:pointer}button:focus-visible,a:focus-visible{outline:3px solid #ef8b3f;outline-offset:5px}button:disabled{opacity:.4;cursor:wait}#app{width:min(1600px,100%);padding:0 28px}header{height:65px;display:flex;align-items:center;justify-content:space-between;gap:20px}.wordmark{display:flex;align-items:center;gap:12px;color:var(--ink);text-decoration:none;font-size:15px;font-weight:900;letter-spacing:.13em}.flag-icon{width:24px;height:18px;transform:skewY(-7deg);background:conic-gradient(#275761 25%,#e8ecdf 0 50%,#275761 0 75%,#e8ecdf 0);background-size:12px 12px}.edition{font-size:9px;letter-spacing:.24em;color:#718d8c}nav{display:flex;gap:8px}nav button{border:1px solid #25535b24;border-radius:3px;background:transparent;color:var(--ink);height:30px;min-width:32px;font-size:15px}#audio{font-size:10px;padding:0 9px}#stage{position:relative;isolation:isolate;aspect-ratio:16/9;width:min(100%,calc((100dvh - 110px)*16/9));margin:auto;background:#8ad1dd;overflow:hidden;box-shadow:0 12px 40px #265b6122}#game{position:absolute;inset:0;width:100%;height:100%;display:block;outline:none}#loading{position:absolute;inset:0;display:grid;place-items:center;background:#b9e0da;color:#27545b;letter-spacing:.1em}#loading[hidden],[hidden]{display:none!important}.screen{position:absolute;inset:0;z-index:5}.intro-copy{position:absolute;top:13%;left:6.6%;width:44%;z-index:1}#intro{background:linear-gradient(90deg,#fff1cde6,#fff3dba3 31%,#fff2d500 64%)}.eyebrow{font-size:clamp(11px,1.1vw,15px);letter-spacing:.14em;font-weight:700;margin:0 0 19px}h1{font-size:clamp(37px,5.2vw,77px);font-weight:900;letter-spacing:-.075em;line-height:1.13;margin:0 0 24px;white-space:pre-line;text-wrap:balance}.description{font-size:clamp(12px,1.35vw,18px);line-height:1.9;white-space:pre-line;margin:0 0 20px;font-weight:600}.race-details{display:flex;gap:17px;margin-bottom:29px;color:#447275;font-size:11px;letter-spacing:.06em}.race-details span+span{border-left:1px solid #55777555;padding-left:17px}.primary{background:var(--ink);color:#fff2d4;border:0;border-radius:3px;box-shadow:0 5px 0 #103840;min-width:235px;display:flex;align-items:center;justify-content:space-between;gap:30px;padding:16px 20px;font-size:16px;letter-spacing:.055em;font-weight:800;transition:transform .15s,background .15s}.primary:hover{background:#28616a;transform:translateY(-2px)}.primary:active{transform:translateY(3px);box-shadow:0 2px 0 #103840}.primary span{font-size:25px;line-height:1}.controls{display:flex;gap:20px;font-size:11px;margin-top:26px}kbd{font:inherit;border:1px solid #26596155;border-radius:3px;padding:2px 5px;margin-right:4px}.control-note{font-size:11px;margin-top:8px;color:#426d72}.intro-stamp{position:absolute;right:5%;bottom:7%;font-size:10px;letter-spacing:.2em;text-align:right;color:#fff6d8;text-shadow:0 1px 4px #20515780}.intro-stamp strong{display:block;font-size:25px;letter-spacing:.04em;font-style:italic}.intro-stamp span{display:block;font-size:11px;margin-top:8px;letter-spacing:.1em}#hud{position:absolute;inset:0;z-index:2;pointer-events:none;color:#fff6df;text-shadow:0 2px 5px #163b4480}.top-left{position:absolute;left:3%;top:3%;display:flex;align-items:center;gap:25px}.position{display:flex;align-items:baseline;gap:5px}.position strong{font-size:64px;font-style:italic;line-height:1;font-weight:900}.position>span{font-size:20px;font-weight:700}.position small{font-size:13px;color:#e4f0d5}.lap{border-left:1px solid #ffffff66;padding-left:25px;display:flex;flex-direction:column;gap:3px}.lap>span,.timer-label{font-size:10px;letter-spacing:.17em;font-weight:700}.lap b{font-size:25px}.top-right{position:absolute;right:3%;top:4%;display:flex;flex-direction:column;align-items:flex-end;gap:3px}.top-right b{font-size:29px;font-variant-numeric:tabular-nums;font-weight:700;letter-spacing:.04em}.top-right small{font-size:10px;min-height:13px}.bottom-left{position:absolute;left:2.5%;bottom:5%;display:flex;flex-direction:column;align-items:center;gap:9px}#map{width:145px;height:114px;filter:drop-shadow(0 2px 3px #28495266)}#section{font-size:11px;font-weight:700;letter-spacing:.07em}.speed{position:absolute;right:3.2%;bottom:5%;width:170px}.speed>div:first-child{display:flex;align-items:baseline;justify-content:flex-end;gap:8px}.speed strong{font-size:58px;font-variant-numeric:tabular-nums;font-weight:800;font-style:italic;line-height:1}.speed span{font-size:12px;font-weight:700}.speed-track{height:4px;background:#183f5144;margin-top:10px;border-radius:2px}.speed-track i{display:block;height:4px;width:0;background:#fff3c8;border-radius:2px}#pedal{text-align:right;font-size:11px;margin:9px 0 0;font-weight:700;letter-spacing:.04em}#corner{position:absolute;top:4%;left:50%;transform:translateX(-50%);background:#fff2d9ed;color:#25535c;text-shadow:none;padding:10px 16px;display:flex;align-items:center;gap:13px;box-shadow:0 3px 0 #1a596333;border-radius:4px}#turn-arrow{font-size:34px;font-weight:900;line-height:1}#corner small{display:block;font-size:9px;letter-spacing:.08em}#corner b{display:block;font-size:14px;margin-top:4px;white-space:nowrap}#corner.brake{background:#ffe3a2}#countdown{position:absolute;inset:0;display:grid;place-items:center;z-index:4;font-size:120px;font-weight:900;color:#fff5d6;text-shadow:0 5px 0 #25606f,0 12px 30px #1d647466;font-style:italic;pointer-events:none}#toast{position:absolute;left:50%;bottom:11%;transform:translate(-50%,8px);opacity:0;transition:opacity .15s,transform .2s;z-index:3;background:#19474de3;color:#fff1d0;padding:11px 22px;border-radius:3px;font-size:14px;font-weight:700;letter-spacing:.05em;white-space:nowrap;pointer-events:none}#toast.show{opacity:1;transform:translate(-50%,0)}#collision-flash{position:absolute;inset:0;box-shadow:inset 0 0 45px #ff9a49;opacity:0;pointer-events:none;z-index:3}.veil{display:grid;place-items:center;background:#153e55a1;backdrop-filter:blur(7px)}.panel{color:#fff3d6;text-align:center;padding:35px;width:min(90%,620px)}.panel h2{font-size:40px;margin:8px 0 23px;letter-spacing:-.03em}.panel>p{font-size:14px}.panel .primary{margin:26px auto 0;background:#fff0c7;color:#21515a;box-shadow:0 5px 0 #e6c77f}.text-button{background:transparent;border:0;color:#d5e5da;padding:22px;font-size:12px}.finish-result{display:flex;align-items:center;justify-content:center;gap:36px;margin:26px 0;border-block:1px solid #fff2c847;padding:18px 0}.finish-result>div:first-child strong{font-size:96px;font-style:italic;line-height:1}.finish-result>div:first-child>span{font-size:24px;margin-left:8px}.finish-time{display:flex;flex-direction:column;text-align:left;gap:6px}.finish-time small{font-size:10px;letter-spacing:.18em}.finish-time b{font-size:35px;font-variant-numeric:tabular-nums}.finish-time span{font-size:11px;min-height:13px}#lap-times{font-size:12px;letter-spacing:.06em;word-spacing:5px}.finish-note{margin-top:25px;color:#dce7d7}footer{height:45px;display:flex;justify-content:space-between;align-items:center;font-size:10px;color:#67878a}#touch{position:absolute;bottom:14px;left:18px;right:18px;z-index:4;display:flex;justify-content:space-between;pointer-events:none}#touch>div{display:flex;gap:10px}#touch button{pointer-events:auto;touch-action:none;user-select:none;border:1px solid #fff5da99;background:#25596db3;color:#fff3d0;width:60px;height:55px;border-radius:12px;font-size:24px}.accelerator{width:88px!important;font-size:13px!important}#stage:fullscreen{width:100%;height:100%;aspect-ratio:auto}@media(max-width:850px){#app{padding:0 10px}header{height:48px}.edition{display:none}#stage{width:100%}.intro-copy{top:9%;width:49%}h1{font-size:42px;margin-bottom:13px}.eyebrow{margin-bottom:12px}.description{font-size:12px;margin-bottom:12px}.race-details{margin-bottom:16px;font-size:9px}.primary{font-size:13px;min-width:190px;padding:11px 15px}.controls{margin-top:19px;font-size:10px;gap:9px}.intro-stamp strong{font-size:19px}.position strong{font-size:47px}.lap b{font-size:19px}.top-right b{font-size:22px}.speed{width:130px}.speed strong{font-size:43px}#map{width:103px;height:81px}#corner{padding:7px 11px}#corner b{font-size:11px}.panel h2{font-size:30px}footer{font-size:9px}}@media(max-width:550px){.wordmark{font-size:11px}.intro-copy{top:10%}h1{font-size:30px;margin-bottom:10px}.eyebrow{font-size:9px;margin-bottom:8px}.description{font-size:10px;line-height:1.6}.race-details,.controls,.control-note,.intro-stamp{display:none}.primary{font-size:11px;min-width:160px;padding:10px 11px}#start{margin-top:17px}.position strong{font-size:35px}.position>span{font-size:13px}.position small{font-size:9px}.top-left{gap:11px}.lap{padding-left:11px}.lap b{font-size:15px}.lap>span,.timer-label{font-size:8px}.top-right b{font-size:16px}.speed strong{font-size:32px}#map{width:76px;height:60px}#section,#pedal{font-size:8px}#corner{top:23%;padding:5px 8px}#turn-arrow{font-size:21px}#corner small{display:none}#corner b{font-size:9px;margin:0}#toast{font-size:10px;padding:8px 12px}.panel{padding:10px}.panel h2{font-size:23px}.panel>p{font-size:10px}.finish-result{margin:12px 0;padding:8px;gap:20px}.finish-result>div:first-child strong{font-size:53px}.finish-time b{font-size:23px}.finish-time small{font-size:8px}.finish-note{display:none}.panel .primary{margin-top:15px}#lap-times{font-size:9px}footer{font-size:7px;height:31px}#countdown{font-size:80px}.speed{bottom:24%;width:100px}.bottom-left{bottom:24%}}
@media(prefers-reduced-motion:reduce){button,#toast{transition:none!important}}
#stage{container-type:inline-size}
.intro-copy{width:47%}
h1{font-size:clamp(28px,5.8cqw,77px);margin-bottom:clamp(10px,1.7cqw,24px)}
.eyebrow{font-size:clamp(9px,1.1cqw,15px);margin-bottom:clamp(8px,1.5cqw,19px)}
.description{font-size:clamp(10px,1.35cqw,18px);margin-bottom:clamp(10px,1.5cqw,20px)}
@container(max-width:950px){.intro-copy{top:10%}.race-details{margin-bottom:18px}.primary{padding:12px 18px;font-size:14px;min-width:210px}.controls{margin-top:18px}.description{line-height:1.8}.control-note{margin-top:5px}}
@container(max-width:600px){.primary{font-size:11px;min-width:160px;padding:10px 11px}.intro-copy{top:8%}.controls,.control-note,.race-details,.intro-stamp{display:none}.panel .primary{min-width:170px}h1{font-size:5.8cqw}}
.icon-definitions{position:absolute;width:0;height:0;overflow:hidden}
.controls{gap:13px;flex-wrap:wrap;row-gap:9px}
#item-slot{position:absolute;top:28%;left:3%;z-index:4;text-align:left;display:flex;flex-direction:column-reverse;align-items:flex-start;gap:8px}
#item-use{display:flex;align-items:center;gap:13px;min-width:257px;padding:10px 14px;border:2px solid #fff5d2;border-radius:7px;background:#fff1d2;color:#265861;box-shadow:0 4px 0 #1f50578a,0 5px 14px #143c5033;text-align:left}
#item-use:disabled{opacity:.85;cursor:default;background:#244f59db;color:#fff0d0;border-color:#fff1d058;box-shadow:none}
#item-use[data-item=boost]{color:#996007}#item-use[data-item=shot]{color:#b34536}#item-use[data-item=shield]{color:#217795}
#item-icon{width:36px;height:36px;flex:none}.item-copy{display:flex;flex:1;flex-direction:column;gap:4px}.item-copy b{font-size:16px;letter-spacing:.04em}.item-copy small{font-size:10px;color:#365960}#item-use:disabled small{color:#d4e8e5}
#item-use kbd{font-size:21px;font-weight:900;padding:5px 10px;margin:0;border:1px solid currentColor;border-bottom-width:3px}#item-use:disabled kbd{opacity:.45}
#effect{display:block;background:#275a68ed;color:#e0fcff;border:1px solid #b4effb8c;padding:5px 13px;width:fit-content;border-radius:3px;font-size:12px;font-weight:800}
#threat{position:absolute;right:3%;top:19%;padding:9px 13px;border-left:4px solid #ec7855;background:#fff0dbe8;color:#95492e;font-size:13px;font-weight:800;z-index:3;box-shadow:0 2px 8px #254c5b22}
#toast{top:20%;bottom:auto;font-size:13px;max-width:52%;white-space:normal;text-align:center}
@container(max-width:850px){#item-use{min-width:220px;padding:8px 11px;gap:9px}#item-icon{width:30px;height:30px}.item-copy b{font-size:14px}.item-copy small{font-size:9px}#item-use kbd{font-size:17px;padding:4px 8px}#threat{font-size:11px;padding:7px 10px}#toast{font-size:11px}}
@container(max-width:600px){#item-use{min-width:146px;padding:5px 8px}.item-copy b{font-size:12px}.item-copy small{display:none}#item-icon{width:23px;height:23px}#item-use kbd{font-size:14px;padding:3px 6px}#threat{font-size:9px;top:22%;max-width:130px}#effect{font-size:9px;padding:3px 7px}#item-slot{top:30%}#toast{top:42%;font-size:9px}}
@media(pointer:coarse){#item-use kbd{display:none}}
```

## config.mjs

```javascript
export function validateSettings(s){
 const errors=[],obj=(o,p)=>{if(!o||typeof o!=='object'||Array.isArray(o)){errors.push(p+' はオブジェクトにしてください');return false;}return true;};
 const num=(n,p,a,b)=>{if(!Number.isFinite(n)||n<a||n>b)errors.push(p+' は '+a+'〜'+b+' にしてください');};
 const text=(v,p)=>{if(typeof v!=='string'||!v.trim())errors.push(p+' は文字列を入れてください');};
 const color=(v,p)=>{if(!/^#[0-9a-f]{6}$/i.test(v||''))errors.push(p+' は #RRGGBB にしてください');};
 if(!obj(s,'設定'))return errors;for(const k of ['identity','race','items','hero','world','track','audio','display'])obj(s[k],k);if(errors.length)return errors;
 if(s.version!==1)errors.push('version は 1 にしてください');
 for(const k of ['id','title','subtitle','eyebrow','description','startLabel','courseName'])text(s.identity[k],'identity.'+k);
 if(!/^[a-z0-9][a-z0-9-]{0,63}$/.test(s.identity.id))errors.push('identity.id は英数字とハイフンにしてください');
 for(const [k,a,b]of [['laps',1,5],['maxSpeed',10,55],['acceleration',2,30],['braking',5,40],['steering',10,45],['wheelbase',1.5,5],['speedSteeringFalloff',1,6],['steerResponse',3,20],['steerSlowdown',0,.2],['steerDeceleration',1,10],['wallImpact',.2,.8],['wallDrag',1,6],['wallSteeringSpeed',4,12]])num(s.race[k],'race.'+k,a,b);
 if(!Number.isInteger(s.race.laps))errors.push('laps は整数にしてください');
 if(!Array.isArray(s.race.rivals)||s.race.rivals.length!==3)errors.push('rivals は3台分にしてください');else s.race.rivals.forEach((r,i)=>{if(obj(r,'rivals.'+i)){text(r.name,'rivals.name');color(r.color,'rivals.color');color(r.helmet,'rivals.helmet');num(r.lane,'rivals.lane',-5,5);num(r.start,'rivals.start',0,40);num(r.lookAhead,'rivals.lookAhead',12,60);num(r.cornerMargin,'rivals.cornerMargin',.8,1.5);num(r.decisionPeriod,'rivals.decisionPeriod',.03,.15);}});
 if(typeof s.items.enabled!=='boolean')errors.push('items.enabled はtrue/falseにしてください');
 for(const [k,a,b]of [['seed',0,4294967295],['pickupRadius',.6,2],['respawnSeconds',.5,10],['boostSeconds',.5,4],['boostMultiplier',1.05,1.5],['shieldSeconds',2,15],['shotRange',20,160],['shotSpeed',60,130],['shotWarning',.4,1.5],['slowSeconds',.3,2.5],['slowMultiplier',.35,.9],['hitImmunity',1,5]])num(s.items[k],'items.'+k,a,b);
 for(const [key,min,max]of [['rows',.02,.98],['lanes',-8,8]]){const values=s.items[key];if(!Array.isArray(values)||!values.length||values.length>12)errors.push('items.'+key+' は1〜12個の配列にしてください');else values.forEach((v,i)=>{num(v,'items.'+key,min,max);if(i&&v<=values[i-1])errors.push('items.'+key+' は重複せず小さい順に並べてください');});}
 if(Array.isArray(s.items.lanes)&&s.items.lanes.some(v=>Math.abs(v)>s.track.width/2-1.2))errors.push('アイテムの位置は道路の中にしてください');
 for(const k of ['kartColor','accentColor','furColor','helmetColor'])color(s.hero[k],'hero.'+k);text(s.hero.name,'hero.name');num(s.hero.driverWidth,'hero.driverWidth',.3,3);num(s.hero.driverHeight,'hero.driverHeight',.3,3);
 for(const k of ['sky','horizon','sea','grass','sand','road','curb','sunlight'])color(s.world[k],'world.'+k);num(s.world.seed,'world.seed',0,4294967295);
 for(const [v,p]of [[s.hero.driverImage,'hero.driverImage'],[s.world.roadTexture,'world.roadTexture']])if(v!==null&&typeof v!=='string')errors.push(p+' は画像パス・data URL・nullのいずれかです');
 num(s.track.width,'track.width',10,24);
 if(!Array.isArray(s.track.points)||s.track.points.length<6||s.track.points.length>40)errors.push('track.points は6〜40個の座標にしてください');else s.track.points.forEach((p,i)=>{if(!Array.isArray(p)||p.length!==3)errors.push('track.points.'+i+' は [x,y,z] にしてください');else{num(p[0],'point.x',-110,350);num(p[1],'point.y',0,20);num(p[2],'point.z',-450,150);const previous=s.track.points[(i+s.track.points.length-1)%s.track.points.length];if(Array.isArray(previous)&&Math.hypot(p[0]-previous[0],p[2]-previous[2])<5)errors.push('隣り合うコース座標は5以上離してください');}});
 if(!Array.isArray(s.track.sections)||!s.track.sections.length)errors.push('track.sections を設定してください');else s.track.sections.forEach((p,i)=>{if(obj(p,'sections.'+i)){num(p.at,'section.at',0,.99);text(p.name,'section.name');if(!i&&p.at!==0)errors.push('最初の区間は at:0');if(i&&s.track.sections[i-1]&&p.at<=s.track.sections[i-1].at)errors.push('区間はatの小さい順に並べてください');}});
 num(s.audio.volume,'audio.volume',0,.8);num(s.display.maxPixelRatio,'display.maxPixelRatio',1,2);if(typeof s.display.shadows!=='boolean')errors.push('display.shadows はtrue/falseにしてください');return errors;
}
export function recordKey(s){let h=2166136261;for(const c of JSON.stringify([s.race,s.track,s.items]))h=Math.imul(h^c.charCodeAt(0),16777619);return 'shimairokart:'+s.identity.id+':'+(h>>>0).toString(16);}
```

## track.mjs

```javascript
import {CatmullRomCurve3,Vector3} from 'three';
export const clamp=(v,a,b)=>Math.max(a,Math.min(b,v));
export const mod=(v,n)=>((v%n)+n)%n;
export function createTrack(cfg){
 const curve=new CatmullRomCurve3(cfg.points.map(p=>new Vector3(...p)),true,'centripetal');
 curve.arcLengthDivisions=4096;curve.updateArcLengths();
 const length=curve.getLength(),count=1600,step=length/count;
 const points=curve.getSpacedPoints(count),tangents=points.slice(0,count).map((p,i)=>points[(i+1)%count].clone().sub(points[mod(i-1,count)]).normalize());
 const curvature=tangents.map((t,i)=>{const a=tangents[mod(i-2,count)],b=tangents[(i+2)%count];return Math.atan2(a.x*b.z-a.z*b.x,a.x*b.x+a.z*b.z)/(4*step);});
 const nearestPoints=points.filter((_,i)=>i%4===0);
 function sample(distance){
  const u=mod(distance,length)/step,i=Math.floor(u),f=u-i,j=(i+1)%count;
  const position=points[i].clone().lerp(points[j],f),tangent=tangents[i].clone().lerp(tangents[j],f).normalize(),right=new Vector3(-tangent.z,0,tangent.x).normalize();
  return {position,tangent,right,curvature:curvature[i]+(curvature[j]-curvature[i])*f};
 }
 function maxCurve(distance,lookAhead=36){let v=0;for(let n=0;n<=lookAhead;n+=4){const c=sample(distance+n).curvature;if(Math.abs(c)>Math.abs(v))v=c;}return v;}
 function nearest(x,z){let best=Infinity,point;for(const p of nearestPoints){const d=(p.x-x)**2+(p.z-z)**2;if(d<best){best=d;point=p;}}return {distance:Math.sqrt(best),point};}
 function inside(x,z){let hit=false;for(let i=0,j=nearestPoints.length-1;i<nearestPoints.length;j=i++){const a=nearestPoints[i],b=nearestPoints[j];if((a.z>z)!==(b.z>z)&&x<(b.x-a.x)*(z-a.z)/(b.z-a.z)+a.x)hit=!hit;}return hit;}
 return {length,width:cfg.width,points,curve,sample,maxCurve,nearest,inside};
}
```

## barrier.mjs

```javascript
import {clamp} from './track.mjs';

// One boundary definition for the visible rail and every racer's collision hull.
export const RAIL={offset:.18,thickness:.26,bottom:.45,top:1.1,halfWidth:1.34,halfLength:1.8};
export function railInnerEdge(track){return track.width/2+RAIL.offset-RAIL.thickness/2;}
export function wallLimit(track,heading){
 return railInnerEdge(track)-Math.abs(Math.cos(heading))*RAIL.halfWidth-Math.abs(Math.sin(heading))*RAIL.halfLength-.04;
}
export function resolveGuardrail(r,dt,track,cfg){
 const limit=wallLimit(track,r.heading),side=Math.sign(r.lane),penetration=Math.abs(r.lane)-limit;
 const into=Math.max(0,side*Math.sin(r.heading));
 if(penetration<-.015||(penetration<=0&&into<.001)){r.wallSide=0;return null;}
 let event=null;
 if(r.wallSide!==side&&r.wallCooldown<=0&&r.speed>1){
  const blocked=r.shield>0;
  if(blocked){r.shield=0;r.blocks++;}else r.speed*=1-cfg.wallImpact*into;
  r.wallHits++;r.wallCooldown=.5;r.wallFlash=.5;
  event={blocked,side};
 }
 r.wallSide=side;r.wallTime+=dt;r.lane=clamp(r.lane,-limit,limit);
 // Scraping stays slow even with a shield/boost; steering never follows the rail.
 if(into>0){r.speed*=Math.exp(-cfg.wallDrag*(.5+3*into*into)*dt);r.lateral=0;if(into>.2)r.boost=0;}
 return event;
}
```

## driving.mjs

```javascript
import {clamp} from './track.mjs';
import {resolveGuardrail,wallLimit} from './barrier.mjs';

// Player and CPU share every parameter and this complete movement calculation.
export function driveVehicle(r,dt,input,track,cfg,items){
 const curve=track.sample(r.distance).curvature;
 const steer=Number(!!input.right)-Number(!!input.left);
 r.steer+=(steer-r.steer)*Math.min(1,dt*cfg.steerResponse);
 const boosted=r.boost>0,baseLimit=cfg.maxSpeed*(boosted?items.boostMultiplier:1)*(r.slow>0?items.slowMultiplier:1);
 // A small, bounded speed reduction follows the actual steering angle.
 // Below this cap acceleration is unchanged, so slow corners and wall recovery do not stall.
 const limit=baseLimit*(1-cfg.steerSlowdown*Math.abs(r.steer));
 const acceleration=cfg.acceleration*(boosted?1.8:1);
 r.braking=!input.accelerate&&r.speed>1;
 if(!input.accelerate)r.speed=Math.max(0,r.speed-cfg.braking*dt);
 else if(r.speed>baseLimit)r.speed=Math.max(baseLimit,r.speed-cfg.braking*dt);
 else if(r.speed>limit)r.speed=Math.max(limit,r.speed-cfg.steerDeceleration*dt);
 else r.speed=Math.min(limit,r.speed+acceleration*dt);
 const ratio=r.speed/cfg.maxSpeed;
 const wheelAngle=r.steer*(cfg.steering*Math.PI/180)/(1+cfg.speedSteeringFalloff*ratio*ratio);
 const forward=r.speed*Math.cos(r.heading)/clamp(1-curve*r.lane,.5,1.5);
 // At the wall, only an intentional inward turn gets low-speed steering help.
 const inward=r.wallSide*steer<0,turnSpeed=input.accelerate&&inward?Math.max(r.speed,cfg.wallSteeringSpeed):r.speed;
 const yaw=turnSpeed/cfg.wheelbase*Math.tan(wheelAngle);
 r.heading=clamp(r.heading+(yaw-curve*forward)*dt,-Math.PI*.49,Math.PI*.49);
 r.lateral=r.speed*Math.sin(r.heading);r.lane+=r.lateral*dt;
 r.slip=clamp(Math.abs(wheelAngle)*ratio*4-.6,0,1);
 const contact=resolveGuardrail(r,dt,track,cfg);
 const progress=Math.max(0,r.speed*Math.cos(r.heading)/clamp(1-curve*r.lane,.5,1.5));
 // Correct the road-frame rotation when a contact reduced this step's travel.
 r.heading=clamp(r.heading+curve*(forward-progress)*dt,-Math.PI*.49,Math.PI*.49);
 const limitAfterTurn=wallLimit(track,r.heading);r.lane=clamp(r.lane,-limitAfterTurn,limitAfterTurn);
 r.distance+=progress*dt;
 return contact;
}

// Only digital steering/throttle commands; no direct changes to position/speed.
export function steeringCommand(r,track,cfg,{lane=0,lookAhead=34,cornerMargin=1.12,boostMultiplier=1.22}={}){
 const angle=cfg.steering*Math.PI/180,ahead=Math.abs(track.maxCurve(r.distance,lookAhead));
 let target=cfg.maxSpeed*(r.boost>0?boostMultiplier:1);
 while(target>10&&Math.tan(angle/(1+cfg.speedSteeringFalloff*(target/cfg.maxSpeed)**2))/cfg.wheelbase<ahead*cornerMargin)target-=.5;
 const aim=Math.atan2((lane-r.lane)*1.4,Math.max(8,r.speed));
 const yaw=track.sample(r.distance).curvature*r.speed+(aim-r.heading)*5;
 const capacity=r.speed/cfg.wheelbase*Math.tan(angle/(1+cfg.speedSteeringFalloff*(r.speed/cfg.maxSpeed)**2));
 const command=yaw/Math.max(.1,capacity);
 return {accelerate:r.speed<target,right:command>.13,left:command<-.13};
}
```

## core.mjs

```javascript
import {clamp,mod} from './track.mjs';
import {driveVehicle,steeringCommand} from './driving.mjs';
import {resolveGuardrail} from './barrier.mjs';
export const ITEM_NAMES={boost:'ダッシュ',shot:'おじゃま弾',shield:'バリア'};
export function createRace(settings,track){
 const cfg=settings.race,items=settings.items,s={};let randomSeed=0,round=0,projectileId=0;
 const random=()=>{randomSeed=(Math.imul(randomSeed,1664525)+1013904223)>>>0;return randomSeed/4294967296;};
 const physical=(id,distance,lane)=>({id,distance,lane,speed:0,lateral:0,heading:0,steer:0,braking:false,slip:0,recoveryLock:false,offroad:0,offroadTotal:0,recoveries:0,wallSide:0,wallHits:0,wallTime:0,wallCooldown:0,wallFlash:0,collision:0,collisions:0,finished:null,item:null,itemSince:0,boost:0,shield:0,slow:0,immunity:0,collected:0,used:{boost:0,shot:0,shield:0},hits:0,blocks:0,bag:[],control:{},think:0});
 function reset(){
  randomSeed=(items.seed+round*977)>>>0;projectileId=0;
  Object.assign(s,physical('player',0,-2.7),{mode:'ready',beforePause:'racing',countdown:3,time:0,lap:1,rank:4,finishRank:0,finishTime:0,lapStart:0,lapTimes:[],overtakes:0,passed:0,leadChanges:0,leader:null,minRivalGap:Infinity,closeSeconds:0,message:'',messageTime:0,events:[],projectiles:[],bursts:[],rivals:cfg.rivals.map((r,i)=>({...r,...physical('cpu-'+i,r.start,r.lane),profile:r})),boxes:items.enabled?items.rows.flatMap((at,row)=>items.lanes.map((lane,col)=>({id:row+'-'+col,distance:at*track.length,lane,cooldown:0}))):[]});
 }
 function racers(){return [s,...s.rivals];}
 const signedGap=(a,b)=>mod(b.distance-a.distance+track.length/2,track.length)-track.length/2;
 function targetFor(r){return racers().filter(v=>v!==r&&v.finished===null).map(v=>({r:v,gap:mod(v.distance-r.distance,track.length)})).filter(v=>v.gap>2&&v.gap<items.shotRange).sort((a,b)=>a.gap-b.gap)[0]?.r;}
 function notice(message,type='notice'){s.message=message;s.messageTime=2.4;s.events.push({type,text:message});}
 function emit(type,r,text,extra={}){s.events.push({type,racer:r.id,...extra,...(r===s&&text?{text}:{})});}
 function wallNotice(r,contact){if(contact)emit(contact.blocked?'block':'wallHit',r,contact.blocked?'バリアでガード！':contact.side>0?'ガードレール！ 左へハンドル':'ガードレール！ 右へハンドル');}
 function drawItem(r){
  if(!r.bag.length){r.bag=['boost','shot','shield'];for(let i=2;i>0;i--){const j=Math.floor(random()*(i+1));[r.bag[i],r.bag[j]]=[r.bag[j],r.bag[i]];}}
  return r.bag.pop();
 }
 function start(){round++;reset();s.mode='countdown';}
 function pause(){if(s.mode==='paused')s.mode=s.beforePause;else if(['racing','countdown'].includes(s.mode)){s.beforePause=s.mode;s.mode='paused';}}
 function useItem(r){
  if(!items.enabled||!r.item||r.finished!==null)return false;
  const item=r.item,target=item==='shot'?targetFor(r):null;
  if(item==='shot'&&!target){if(r===s)notice('前の相手に近づいてから Z！');return false;}
  if(item==='boost')r.boost=items.boostSeconds;
  if(item==='shield')r.shield=items.shieldSeconds;
  if(item==='shot')s.projectiles.push({id:++projectileId,owner:r.id,target:target.id,distance:r.distance+2,lane:r.lane,delay:items.shotWarning,age:0});
  r.item=null;r.used[item]++;emit('use',r,item==='boost'?'ダッシュ！':item==='shot'?'おじゃま弾、発射！':'バリア！',{item});return true;
 }
 function cpuInput(r,dt){
  r.think-=dt;
  if(r.think<=0){
   r.think=r.profile.decisionPeriod;
   let lane=r.profile.lane+Math.sin(r.distance*.013+r.profile.start)*.35;
   if(!r.item){const candidates=s.boxes.filter(b=>b.cooldown===0).map(b=>({b,gap:mod(b.distance-r.distance,track.length)})).filter(v=>v.gap>0&&v.gap<80).sort((a,b)=>a.gap-b.gap||Math.abs(a.b.lane-r.lane)-Math.abs(b.b.lane-r.lane));if(candidates.length)lane=candidates[0].b.lane;}
   const traffic=racers().filter(v=>v!==r&&v.finished===null&&signedGap(r,v)>0&&signedGap(r,v)<16&&Math.abs(v.lane-lane)<2.4).sort((a,b)=>signedGap(r,a)-signedGap(r,b))[0];
   if(traffic)lane=clamp(traffic.lane+(r.lane>=traffic.lane?3.1:-3.1),-track.width/2+2.5,track.width/2-2.5);
   r.control=steeringCommand(r,track,cfg,{lane,lookAhead:r.profile.lookAhead,cornerMargin:r.profile.cornerMargin,boostMultiplier:items.boostMultiplier});
  }
  let item=false;
  if(r.item&&s.time-r.itemSince>.75){
   if(r.item==='boost')item=Math.abs(track.maxCurve(r.distance,75))<.011;
   if(r.item==='shot')item=!!targetFor(r);
   if(r.item==='shield')item=s.projectiles.some(p=>p.target===r.id)||racers().some(v=>v!==r&&v.finished===null&&Math.abs(signedGap(r,v))<12)||s.time-r.itemSince>6;
  }
  return {...r.control,item};
 }
 function hitByShot(r){
  const blocked=r.shield>0;
  if(blocked){r.shield=0;r.blocks++;r.immunity=items.hitImmunity;emit('block',r,'バリアで防いだ！');}
  else if(r.immunity<=0){r.speed*=items.slowMultiplier;r.slow=items.slowSeconds;r.boost=0;r.immunity=items.hitImmunity;r.collision=.7;r.hits++;emit('itemHit',r,'おじゃま弾！ 少しの間スピードダウン');}
  s.bursts.push({distance:r.distance,lane:r.lane,life:.5,kind:blocked?'shield':'hit'});
 }
 function tick(dt,input){
  if(s.mode==='countdown'){const old=Math.ceil(s.countdown);s.countdown=Math.max(0,s.countdown-dt);if(Math.ceil(s.countdown)!==old)s.events.push({type:'count',value:Math.ceil(s.countdown)});if(s.countdown<=1e-6){s.mode='racing';s.events.push({type:'go'});}return;}
  if(s.mode!=='racing')return;
  s.time+=dt;s.messageTime=Math.max(0,s.messageTime-dt);const all=racers(),previous=new Map(all.map(r=>[r.id,r.distance]));
  for(const b of s.boxes)b.cooldown=Math.max(0,b.cooldown-dt);
  for(const r of all){
   for(const k of ['collision','boost','shield','slow','immunity','wallCooldown','wallFlash'])r[k]=Math.max(0,r[k]-dt);
   if(r.finished!==null)continue;
   const keys=r===s?input:cpuInput(r,dt);
   if(keys.item)useItem(r);
   wallNotice(r,driveVehicle(r,dt,keys,track,cfg,items));
  }
  // Contacts have the same slowdown for each unshielded kart, with a cooldown.
  for(let i=0;i<all.length;i++)for(let j=i+1;j<all.length;j++){
   const a=all[i],b=all[j];if(a.finished!==null||b.finished!==null||a.collision||b.collision||Math.max(a.speed,b.speed)<4)continue;
   if(Math.abs(signedGap(a,b))<2.55&&Math.abs(a.lane-b.lane)<1.8){
    const direction=a.lane===b.lane?1:Math.sign(a.lane-b.lane),separation=(1.9-Math.abs(a.lane-b.lane))*.5;a.lane+=direction*separation;b.lane-=direction*separation;
    for(const [r,other]of [[a,b],[b,a]]){r.collision=.85;r.collisions++;if(r.shield>0){r.shield=0;r.blocks++;emit('block',r,'バリアで防いだ！');}else{r.speed*=.76;r.heading+=(r.lane>=other.lane?1:-1)*.04;emit('hit',r,'ぶつかった！ アクセルでもう一度');}}
   }
  }
  // A kart-to-kart push may never put either vehicle through the rail.
  for(const r of all)if(r.finished===null)wallNotice(r,resolveGuardrail(r,0,track,cfg));
  // Resolve claims by crossing time, not player-first iteration order.
  for(const b of s.boxes){
   if(b.cooldown>0)continue;
   const claims=all.filter(r=>r.finished===null&&!r.item&&Math.abs(r.lane-b.lane)<items.pickupRadius).map(r=>{const before=previous.get(r.id),delta=mod(b.distance-before,track.length);return {r,delta,t:delta/Math.max(.00001,r.distance-before)};}).filter(v=>v.delta<=v.r.distance-previous.get(v.r.id)+.01).sort((a,b)=>a.t-b.t);
   if(claims.length){const r=claims[0].r;r.item=drawItem(r);r.itemSince=s.time;r.collected++;b.cooldown=items.respawnSeconds;emit('pickup',r,ITEM_NAMES[r.item]+'をゲット！ Zで使う',{item:r.item});}
  }
  for(const p of s.projectiles){
   p.age+=dt;const owner=all.find(r=>r.id===p.owner),target=all.find(r=>r.id===p.target);
   if(!target||target.finished!==null||p.age>5){p.dead=true;continue;}
   if(p.delay>0){p.delay-=dt;p.distance=owner.distance+2;p.lane=owner.lane;continue;}
   const gap=mod(target.distance-p.distance,track.length),travel=items.shotSpeed*dt;
   p.lane+=(target.lane-p.lane)*Math.min(1,dt*9);
   if(gap<=travel+1.8){hitByShot(target);p.dead=true;}else p.distance+=travel;
  }
  s.projectiles=s.projectiles.filter(p=>!p.dead);s.bursts.forEach(b=>b.life-=dt);s.bursts=s.bursts.filter(b=>b.life>0);
  const finish=track.length*cfg.laps;
  for(const r of all){if(r.finished!==null||r.distance<finish)continue;const before=previous.get(r.id);r.finished=s.time-dt+dt*(finish-before)/Math.max(1e-9,r.distance-before);if(r!==s){r.distance=finish;r.speed=0;}}
  const ordered=[...all].sort((a,b)=>a.finished!==null&&b.finished!==null?a.finished-b.finished:a.finished!==null?-1:b.finished!==null?1:b.distance-a.distance),rank=ordered.indexOf(s)+1;
  if(rank<s.rank){s.overtakes+=s.rank-rank;notice('追いぬいた！ '+rank+'位','pass');}else if(rank>s.rank){s.passed+=rank-s.rank;notice('抜かれた！ まだ追いつける','passed');}s.rank=rank;
  const leader=ordered[0].id;if(s.leader&&leader!==s.leader)s.leadChanges++;s.leader=leader;
  const nearest=Math.min(...s.rivals.map(r=>Math.abs(r.distance-s.distance)));s.minRivalGap=Math.min(s.minRivalGap,nearest);if(nearest<30)s.closeSeconds+=dt;
  const before=previous.get(s.id),oldLap=Math.floor(before/track.length),newLap=Math.floor(s.distance/track.length);
  if(newLap>oldLap){const crossing=s.time-dt+dt*((oldLap+1)*track.length-before)/Math.max(1e-9,s.distance-before);s.lapTimes.push(crossing-s.lapStart);s.lapStart=crossing;if(newLap<cfg.laps)notice('ラスト'+(cfg.laps-newLap)+'周！','lap');}
  s.lap=Math.min(cfg.laps,Math.floor(s.distance/track.length)+1);
  if(s.finished!==null){s.finishTime=s.finished;s.finishRank=1+s.rivals.filter(r=>r.finished!==null&&r.finished<=s.finishTime).length;s.rank=s.finishRank;s.distance=finish;s.speed=0;s.lateral=0;s.mode='finished';s.events.push({type:'finish'});}
 }
 function step(seconds,input={}){let left=clamp(seconds,0,.12),first=true;while(left>1e-8){const dt=Math.min(left,1/120);tick(dt,{...input,item:first&&input.item});first=false;left-=dt;}}
 reset();return {s,start,reset,pause,step};
}
```

## models.mjs

```javascript
import * as THREE from 'three';
import {RoundedBoxGeometry} from 'three/addons/geometries/RoundedBoxGeometry.js';
import {mergeGeometries} from 'three/addons/utils/BufferGeometryUtils.js';
const materials=new Map();
export function material(color,metalness=0){const key=color+':'+metalness;if(!materials.has(key))materials.set(key,new THREE.MeshStandardMaterial({color,roughness:metalness?.34:.78,metalness}));return materials.get(key);}
export function mesh(parent,geometry,mat,x=0,y=0,z=0){const m=new THREE.Mesh(geometry,typeof mat==='string'?material(mat):mat);m.position.set(x,y,z);m.castShadow=true;m.receiveShadow=true;parent.add(m);return m;}
export function box(parent,w,h,d,color,x=0,y=0,z=0,r=.08){return mesh(parent,new RoundedBoxGeometry(w,h,d,2,Math.min(r,w/3,h/3,d/3)),color,x,y,z);}
export function ball(parent,r,color,x=0,y=0,z=0,sx=1,sy=1,sz=1){const m=mesh(parent,new THREE.SphereGeometry(r,16,10),color,x,y,z);m.scale.set(sx,sy,sz);return m;}
export function rod(parent,a,b,r,color){const va=new THREE.Vector3(...a),vb=new THREE.Vector3(...b),m=mesh(parent,new THREE.CylinderGeometry(r,r,va.distanceTo(vb),8),color);m.position.copy(va).lerp(vb,.5);m.quaternion.setFromUnitVectors(new THREE.Vector3(0,1,0),vb.sub(va).normalize());return m;}
export function batch(group){
 group.updateMatrixWorld(true);const inverse=group.matrixWorld.clone().invert(),bins=new Map(),meshes=[];
 group.traverse(m=>{if(!m.isMesh||Array.isArray(m.material))return;const key=m.material.uuid+':'+m.castShadow;let bin=bins.get(key);if(!bin){bin={mat:m.material,cast:m.castShadow,geometries:[]};bins.set(key,bin);}const g=m.geometry.clone();g.applyMatrix4(new THREE.Matrix4().multiplyMatrices(inverse,m.matrixWorld));const non=g.index?g.toNonIndexed():g;for(const name of Object.keys(non.attributes))if(!['position','normal','uv'].includes(name))non.deleteAttribute(name);if(!non.attributes.uv)non.setAttribute('uv',new THREE.Float32BufferAttribute(new Float32Array(non.attributes.position.count*2),2));bin.geometries.push(non);meshes.push(m);});
 for(const m of meshes)m.removeFromParent();
 for(const bin of bins.values()){const g=mergeGeometries(bin.geometries,false);const m=new THREE.Mesh(g,bin.mat);m.castShadow=bin.cast;m.receiveShadow=true;group.add(m);for(const geo of bin.geometries)geo.dispose();}
}
export function createKart(options,textureLoader){
 const root=new THREE.Group(),body=new THREE.Group(),driver=new THREE.Group(),staticParts=new THREE.Group();root.add(body);body.add(staticParts,driver);
 const color=options.kartColor,cream=options.accentColor||'#fff1ca',dark='#263c42',metal=material('#b8c6bd',.65);
 box(staticParts,1.72,.36,2.45,color,0,.57,0,.15);
 box(staticParts,1.3,.30,.78,color,0,.7,1.22,.15);
 box(staticParts,.2,.025,1.14,cream,0,.885,1.0,.008);
 box(staticParts,1.7,.12,.18,cream,0,.5,1.68,.05);
 box(staticParts,.32,.40,1.72,color,-.97,.55,.04,.12);box(staticParts,.32,.40,1.72,color,.97,.55,.04,.12);
 box(staticParts,.34,.11,1.1,cream,-.98,.77,.04);box(staticParts,.34,.11,1.1,cream,.98,.77,.04);
 rod(staticParts,[-1.2,.34,-1.35],[1.2,.34,-1.35],.095,dark);
 rod(staticParts,[-1.16,.31,1.35],[1.16,.31,1.35],.085,metal);
 box(staticParts,.85,.15,.75,dark,0,.81,-.48);box(staticParts,.82,.75,.21,dark,0,1.12,-.83,.12).rotation.x=-.15;
 box(staticParts,.93,.52,.5,metal,0,.75,-1.1,.06);
 for(let i=0;i<5;i++)box(staticParts,1,.05,.05,dark,0,.59+i*.1,-1.38,.01);
 for(const x of [-.67,.67]){rod(staticParts,[x,.65,-1.1],[x,.76,-1.60],.10,metal);mesh(staticParts,new THREE.CylinderGeometry(.078,.078,.025,12),dark,x,.76,-1.61).rotation.x=Math.PI/2;}
 const lights=[];for(const x of [-.63,.63]){const mat=new THREE.MeshStandardMaterial({color:'#923526',emissive:'#ff3920',emissiveIntensity:.08,roughness:.4});lights.push(box(staticParts,.26,.14,.10,mat,x,.78,-1.28,.035));}
 const wheels=[];for(const x of [-1.13,1.13])for(const z of [-.96,.95]){const pivot=new THREE.Group();pivot.position.set(x,.43,z);root.add(pivot);const wheel=new THREE.Group();pivot.add(wheel);
  mesh(wheel,new THREE.CylinderGeometry(.42,.42,.36,20),material('#222d31')).rotation.z=Math.PI/2;
  mesh(wheel,new THREE.CylinderGeometry(.255,.255,.38,16),metal).rotation.z=Math.PI/2;
  mesh(wheel,new THREE.CylinderGeometry(.13,.13,.40,12),cream).rotation.z=Math.PI/2;
  for(let i=0;i<8;i++){const a=i*Math.PI/4;box(wheel,.38,.045,.095,'#465156',0,Math.cos(a)*.40,Math.sin(a)*.40,.005).rotation.x=a;}
  batch(wheel);wheels.push({pivot,wheel,front:z>0});}
 const fur=options.furColor||'#acb9b0',helmet=options.helmetColor||'#fff2d7';
 if(options.driverImage){
  const tex=textureLoader.load(options.driverImage);tex.colorSpace=THREE.SRGBColorSpace;
  const avatar=new THREE.Sprite(new THREE.SpriteMaterial({map:tex,transparent:true,depthWrite:false}));avatar.position.set(0,1.5,-.2);avatar.scale.set(options.driverWidth||1.15,options.driverHeight||1.55,1);driver.add(avatar);
 }else{
  ball(driver,.38,fur,0,1.16,-.25,1,1.2,.8);
  ball(driver,.46,fur,0,1.89,-.20,1,.97,.88);
  ball(driver,.31,cream,0,1.77,.17,1,.8,1.05);ball(driver,.08,dark,0,1.84,.45,1,.7,.75);
  for(const x of [-.24,.24]){ball(driver,.093,dark,x,1.99,.14,.75,1,.65);ball(driver,.027,'#ffffff',x-.015,2.018,.185);}
  const cap=mesh(driver,new THREE.SphereGeometry(.5,24,12,0,Math.PI*2,0,Math.PI*.52),helmet,0,1.92,-.22);cap.scale.set(1.06,1,1);
  const rim=mesh(driver,new THREE.TorusGeometry(.51,.04,6,32),cream,0,1.935,-.22);rim.rotation.x=Math.PI/2;
  const stripe=mesh(driver,new THREE.TorusGeometry(.506,.047,6,24,Math.PI),color,0,1.922,-.22);stripe.rotation.y=Math.PI/2;
  for(const x of [-.35,.35]){const ear=mesh(driver,new THREE.ConeGeometry(.19,.5,4),fur,x,2.38,-.22);ear.rotation.z=-Math.sign(x)*.17;mesh(driver,new THREE.ConeGeometry(.11,.33,4),cream,x,2.42,-.15);}
  rod(driver,[-.29,1.37,-.1],[-.35,1.22,.48],.11,fur);rod(driver,[.29,1.37,-.1],[.35,1.22,.48],.11,fur);
  ball(driver,.12,cream,-.35,1.22,.49);ball(driver,.12,cream,.35,1.22,.49);
  const belt=mesh(driver,new THREE.TorusGeometry(.29,.06,6,20),color,0,1.45,-.22);belt.rotation.x=Math.PI/2;
  box(driver,.19,.08,.5,color,.32,1.44,-.51,.025).rotation.y=-.4;
  batch(driver);
 }
 const steering=mesh(staticParts,new THREE.TorusGeometry(.29,.045,8,20),dark,0,1.22,.49);steering.rotation.x=Math.PI/3;
 const tail=new THREE.Group();if(!options.driverImage){const curve=new THREE.CatmullRomCurve3([new THREE.Vector3(.18,1.0,-.64),new THREE.Vector3(.7,1.06,-.85),new THREE.Vector3(.94,1.25,-1.18),new THREE.Vector3(.8,1.5,-1.3)]);mesh(tail,new THREE.TubeGeometry(curve,14,.20,10,false),fur);ball(tail,.24,cream,.81,1.48,-1.3,.9,1.5,.85);body.add(tail);batch(tail);}
 batch(staticParts);
 const shadowTexture=(()=>{const canvas=document.createElement('canvas');canvas.width=128;canvas.height=128;const c=canvas.getContext('2d'),g=c.createRadialGradient(64,64,12,64,64,64);g.addColorStop(0,'#112f3d70');g.addColorStop(1,'#112f3d00');c.fillStyle=g;c.fillRect(0,0,128,128);return new THREE.CanvasTexture(canvas);})();
 const shadow=new THREE.Mesh(new THREE.PlaneGeometry(3.6,4.1),new THREE.MeshBasicMaterial({map:shadowTexture,transparent:true,depthWrite:false}));shadow.rotation.x=-Math.PI/2;shadow.position.y=.04;root.add(shadow);
 return {root,body,driver,wheels,tail,lights,update(speed,steer,braking,time){body.rotation.z=steer*Math.min(speed/36,1)*.06;driver.rotation.z=-steer*.1;body.position.y=Math.sin(time*18)*.009*Math.min(speed,12);tail.rotation.y=Math.sin(time*5)*.06;for(const w of wheels){w.wheel.rotation.x=time*speed*1.8;if(w.front)w.pivot.rotation.y=-steer*.26;}for(const l of lights)l.material.emissiveIntensity=braking?2.5:.08;}};
}
```

## guardrail-visuals.mjs

```javascript
import * as THREE from 'three';
import {RAIL} from './barrier.mjs';
import {material,mesh} from './models.mjs';

export function createGuardrails(parent,track,accent){
 const sections=Math.ceil(track.length/1.5),steel=material('#dbe5d9',.3),post=material('#62858a',.25);
 for(const side of [-1,1]){
  const vertices=[],indices=[];
  // Closed, continuous beam rather than disconnected straight fences.
  for(let i=0;i<=sections;i++){
   const p=track.sample(i*track.length/sections);
   for(const [lateral,height]of [[-1,RAIL.bottom],[1,RAIL.bottom],[1,RAIL.top],[-1,RAIL.top]]){
    const v=p.position.clone().addScaledVector(p.right,side*(track.width/2+RAIL.offset+lateral*RAIL.thickness/2));
    vertices.push(v.x,v.y+height,v.z);
   }
   if(i<sections)for(let face=0;face<4;face++){
    const a=i*4+face,b=i*4+(face+1)%4,c=a+4,d=b+4;
    indices.push(...(side>0?[a,c,b,b,c,d]:[a,b,c,b,d,c]));
   }
  }
  const geometry=new THREE.BufferGeometry();geometry.setAttribute('position',new THREE.Float32BufferAttribute(vertices,3));geometry.setIndex(indices);geometry.computeVertexNormals();mesh(parent,geometry,steel);
  const count=Math.ceil(track.length/5);
  for(let i=0;i<count;i++){
   const p=track.sample(i*track.length/count),q=p.position.clone().addScaledVector(p.right,side*(track.width/2+RAIL.offset+.10));
   const support=mesh(parent,new THREE.BoxGeometry(.13,.95,.13),post,q.x,q.y+.49,q.z);support.rotation.y=Math.atan2(p.tangent.x,p.tangent.z);
   if(i%2===0){const v=p.position.clone().addScaledVector(p.right,side*(track.width/2+RAIL.offset-RAIL.thickness/2-.015));const marker=mesh(parent,new THREE.BoxGeometry(.045,.16,.30),material(accent),v.x,v.y+.83,v.z);marker.rotation.y=support.rotation.y;}
  }
 }
}
```

## world.mjs

```javascript
import * as THREE from 'three';
import {clamp} from './track.mjs';
import {material,mesh,box,ball,rod,batch} from './models.mjs';
import {createGuardrails} from './guardrail-visuals.mjs';
export function createWorld(scene,track,settings,roadTexture){
 const cfg=settings.world,staticWorld=new THREE.Group();scene.add(staticWorld);
 let seed=cfg.seed;const rnd=()=>{seed=(Math.imul(seed,1664525)+1013904223)>>>0;return seed/4294967296;};
 const sand=new THREE.Color(cfg.sand),grass=new THREE.Color(cfg.grass),deepGreen=new THREE.Color('#649451');
 const sun=new THREE.DirectionalLight(cfg.sunlight,3.2);sun.position.set(-90,150,60);sun.castShadow=settings.display.shadows;sun.shadow.mapSize.set(2048,2048);Object.assign(sun.shadow.camera,{left:-58,right:58,top:58,bottom:-58,near:1,far:330});sun.shadow.normalBias=.06;sun.shadow.bias=-.00018;sun.shadow.radius=3;scene.add(sun,sun.target);
 scene.add(new THREE.HemisphereLight('#d9f4ef','#769657',2.2));
 scene.fog=new THREE.Fog('#bee4d9',150,720);
 const sky=new THREE.Mesh(new THREE.SphereGeometry(1800,32,16),new THREE.ShaderMaterial({side:THREE.BackSide,depthWrite:false,uniforms:{top:{value:new THREE.Color(cfg.sky)},bottom:{value:new THREE.Color(cfg.horizon)}},vertexShader:'varying vec3 p;void main(){p=position;gl_Position=projectionMatrix*modelViewMatrix*vec4(position,1.);}',fragmentShader:'varying vec3 p;uniform vec3 top;uniform vec3 bottom;void main(){vec3 d=normalize(p);float h=pow(max(0.,d.y),.55);vec3 c=mix(bottom,top,smoothstep(0.,.55,h));float sun=dot(d,normalize(vec3(-.5,.45,.4)));c+=vec3(1.,.74,.35)*pow(max(sun,0.),120.)*.32;c=mix(c,vec3(1.,.95,.76),smoothstep(.9986,.9992,sun));gl_FragColor=vec4(c,1.);}'}));scene.add(sky);
 const water=new THREE.Mesh(new THREE.PlaneGeometry(4000,4000,1,1),new THREE.ShaderMaterial({uniforms:{time:{value:0},sea:{value:new THREE.Color(cfg.sea)},eye:{value:new THREE.Vector3()}},vertexShader:'varying vec3 p;void main(){vec4 w=modelMatrix*vec4(position,1.);p=w.xyz;gl_Position=projectionMatrix*viewMatrix*w;}',fragmentShader:`varying vec3 p;uniform float time;uniform vec3 sea;uniform vec3 eye;void main(){float w=sin(p.x*.19+p.z*.11+time*.8)+sin(p.x*.47-p.z*.22-time*1.1)*.38+sin(p.z*.95+p.x*.04+time*.5)*.16;float fleck=smoothstep(.94,1.17,w);vec3 c=mix(sea,vec3(.72,.94,.82),fleck*.28);float dist=length(p-eye);c=mix(c,vec3(.71,.87,.84),smoothstep(150.,1200.,dist));float sparkle=pow(max(0.,sin(p.x*2.7+p.z*.31+time)*sin(p.z*2.1-time*.7)),28.);c+=sparkle*.15;gl_FragColor=vec4(c,1.);}`}));water.rotation.x=-Math.PI/2;water.position.y=-2.4;scene.add(water);
 function terrainInfo(x,z){
  const n=track.nearest(x,z),inside=track.inside(x,z),d=n.distance,roadY=n.point.y;
  if(!inside&&d>38)return {y:-7,blend:0};
  if(!inside){const u=clamp((d-17)/21,0,1);return {y:roadY-.35-(roadY+6.6)*u*u,blend:clamp((29-d)/8,0,1)};}
  const hill=24*Math.exp(-((x-98)**2/4400+(z+176)**2/10200))+16*Math.exp(-((x-164)**2/1700+(z+275)**2/2400));
  return {y:roadY-.35+hill*clamp((d-16)/40,0,1),blend:1};
 }
 const terrainGeo=new THREE.PlaneGeometry(550,730,137,182);terrainGeo.rotateX(-Math.PI/2);terrainGeo.translate(110,0,-140);
 const pos=terrainGeo.attributes.position,colors=[];
 for(let i=0;i<pos.count;i++){const x=pos.getX(i),z=pos.getZ(i),data=terrainInfo(x,z);pos.setY(i,data.y);const c=sand.clone().lerp(grass,data.blend);if(data.blend>.5)c.lerp(deepGreen,(.5+.5*Math.sin(x*.07+z*.12))*.13);const shade=.97+.04*Math.sin(x*.73+z*1.2);c.multiplyScalar(shade);colors.push(c.r,c.g,c.b);}
 terrainGeo.setAttribute('color',new THREE.Float32BufferAttribute(colors,3));terrainGeo.computeVertexNormals();const terrain=new THREE.Mesh(terrainGeo,new THREE.MeshStandardMaterial({vertexColors:true,roughness:1}));terrain.receiveShadow=true;scene.add(terrain);
 const half=track.width/2;
 createGuardrails(staticWorld,track,cfg.curb);
 function ribbon(left,right,mat,height=.04){
  const vertices=[],uv=[],indices=[];const count=1200;
  for(let i=0;i<=count;i++){const d=i*track.length/count,p=track.sample(d);for(const side of [left,right]){const v=p.position.clone().addScaledVector(p.right,side);vertices.push(v.x,v.y+height,v.z);uv.push((side-left)/3.5,d/3.5);}if(i<count){const a=i*2;indices.push(a,a+1,a+2,a+1,a+3,a+2);}}
  const g=new THREE.BufferGeometry();g.setAttribute('position',new THREE.Float32BufferAttribute(vertices,3));g.setAttribute('uv',new THREE.Float32BufferAttribute(uv,2));g.setIndex(indices);g.computeVertexNormals();const m=new THREE.Mesh(g,mat);m.receiveShadow=true;scene.add(m);return m;
 }
 ribbon(-half-.8,half+.8,material('#e8e0b1'),.00);
 roadTexture.wrapS=roadTexture.wrapT=THREE.RepeatWrapping;roadTexture.colorSpace=THREE.SRGBColorSpace;roadTexture.anisotropy=8;
 const roadMat=new THREE.MeshStandardMaterial({color:cfg.road,map:roadTexture,roughness:.94});ribbon(-half,half,roadMat,.06);
 ribbon(-half+.25,-half+.36,material('#f5edd0'),.075);ribbon(half-.36,half-.25,material('#f5edd0'),.075);
 for(let d=0,i=0;d<track.length;d+=3,i++){
  const at=track.sample(d),next=track.sample(d+3.04);
  for(const side of [-1,1]){const v=[];for(const p of [at,next])for(const offset of [half+.05,half+.7]){const point=p.position.clone().addScaledVector(p.right,offset*side);v.push(point.x,point.y+.095,point.z);}const g=new THREE.BufferGeometry();g.setAttribute('position',new THREE.Float32BufferAttribute(v,3));g.setAttribute('uv',new THREE.Float32BufferAttribute([0,0,1,0,0,1,1,1],2));g.setIndex(side===1?[0,1,2,1,3,2]:[0,2,1,1,2,3]);g.computeVertexNormals();mesh(staticWorld,g,material(i%2?cfg.curb:'#fff3d4'));}
  if(i%3===0){const p=track.sample(d);const marker=box(staticWorld,.14,.025,2.5,'#ece7c6',p.position.x,p.position.y+.082,p.position.z,.001);marker.rotation.y=Math.atan2(p.tangent.x,p.tangent.z);}
 }
 const atStart=track.sample(0),start=new THREE.Group();start.position.copy(atStart.position);start.rotation.y=Math.atan2(atStart.tangent.x,atStart.tangent.z);staticWorld.add(start);
 for(let x=0;x<12;x++)for(let z=0;z<2;z++)box(start,track.width/12,.018,.55,(x+z)%2?'#fdf5d7':'#344a4d',-half+(x+.5)*track.width/12,.09,(z-.5)*.55,.001);
 for(const x of [-half-1,half+1]){box(start,.45,8,.45,'#f7edd4',x,4,0,.1);box(start,.8,1,.8,'#df6c4c',x,.5,0,.08);}
 box(start,track.width+2.8,1,.65,'#2d6868',0,7.7,0,.12);
 const banner=labelTexture(settings.identity.subtitle+' / START',1024,128,'#fff1c8','#2d6868');const bannerMat=new THREE.MeshBasicMaterial({map:banner,side:THREE.DoubleSide});const bannerMesh=mesh(start,new THREE.PlaneGeometry(track.width+1.8,.92),bannerMat,0,7.7,-.335);bannerMesh.rotation.y=Math.PI;
 const flags=[];
 for(const sign of [-1,1])for(let i=0;i<5;i++){const d=35+i*14,p=track.sample(d),base=p.position.clone().addScaledVector(p.right,sign*(half+3));const pole=new THREE.Group();pole.position.copy(base);staticWorld.add(pole);rod(pole,[0,0,0],[0,6.8,0],.06,'#f4e2bf');const flag=mesh(pole,new THREE.PlaneGeometry(1.5,2.8),new THREE.MeshStandardMaterial({color:i%2?'#f7ca61':'#ed7154',side:THREE.DoubleSide}),.70,5.0,0);flag.rotation.y=Math.PI*.35;flags.push(flag);}
 // Scenic props sit outside the drivable corridor, leaving the corner readable.
 function palm(x,y,z,height=8){const g=new THREE.Group();g.position.set(x,y,z);staticWorld.add(g);const sway=.8+rnd()*.8;for(let i=0;i<6;i++){const a=i/6,b=(i+1)/6;rod(g,[sway*a*a,height*a,0],[sway*b*b,height*b,0],.25-a*.08,'#ae865d');}const tip=new THREE.Vector3(sway,height,0);
  for(let j=0;j<7;j++){const a=j*Math.PI*2/7+rnd()*.14;const pts=[];for(let k=0;k<5;k++){const t=k/4;pts.push(new THREE.Vector3(tip.x+Math.cos(a)*t*4.1,height+Math.sin(t*Math.PI)*1.1-t*.9,Math.sin(a)*t*4.1));}const vertices=[];for(let k=0;k<pts.length;k++){const w=Math.sin(k/4*Math.PI)*.60+.025;for(const side of [-1,1])vertices.push(pts[k].x+Math.sin(a)*w*side,pts[k].y,pts[k].z-Math.cos(a)*w*side);}const geom=new THREE.BufferGeometry();geom.setAttribute('position',new THREE.Float32BufferAttribute(vertices,3));const index=[];for(let k=0;k<4;k++)index.push(k*2,k*2+1,k*2+2,k*2+1,k*2+3,k*2+2);geom.setIndex(index);geom.computeVertexNormals();const mat=material(j%2?'#569451':'#77af5b');mat.side=THREE.DoubleSide;mesh(g,geom,mat);}
  for(let i=0;i<3;i++)ball(g,.28,'#927550',sway+(i-1)*.3,height-.2,.2);
 }
 for(let i=0;i<48;i++){const d=(i+.31)*track.length/48,p=track.sample(d),side=i%2?1:-1,offset=half+5+rnd()*8,v=p.position.clone().addScaledVector(p.right,side*offset);const y=terrainInfo(v.x,v.z).y;if(y>-1)palm(v.x,y,v.z,6+rnd()*5);}
 for(let i=0;i<100;i++){const x=20+rnd()*230,z=-325+rnd()*410,n=track.nearest(x,z);if(n.distance<21||!track.inside(x,z))continue;const data=terrainInfo(x,z);const h=3+rnd()*4;rod(staticWorld,[x,data.y,z],[x,data.y+h*.7,z],.22,'#91744d');for(let j=0;j<3;j++)ball(staticWorld,2.5, j%2?'#6a9f60':'#84b36c',x+(j-1)*1.4,data.y+h+(j%2)*1.3,z,1,1.1,1);}
 for(let i=0;i<130;i++){const d=rnd()*track.length,p=track.sample(d),v=p.position.clone().addScaledVector(p.right,(i%2?1:-1)*(half+1.4+rnd()*3.5));const y=terrainInfo(v.x,v.z).y;if(y<-.7)continue;ball(staticWorld,.35+rnd()*.6,i%3?'#a8c37a':'#eacb70',v.x,y+.15,v.z,1,.5,1);}
 // A lighthouse marks the long opening bend.
 const lighthouseAt=track.sample(track.length*.27),lp=lighthouseAt.position.clone().addScaledVector(lighthouseAt.right,-23),baseY=terrainInfo(lp.x,lp.z).y;
 const tower=new THREE.Group();tower.position.set(lp.x,baseY,lp.z);staticWorld.add(tower);
 mesh(tower,new THREE.CylinderGeometry(3.2,4,1.2,24),'#dac58e',0,.6,0);
 for(let i=0;i<5;i++)mesh(tower,new THREE.CylinderGeometry(1.65-i*.13,1.78-i*.13,2.8,20),i%2?'#ef7757':'#fff0c6',0,2.6+i*2.8,0);
 mesh(tower,new THREE.CylinderGeometry(2.3,2.3,.3,24),'#2b7375',0,16,0);mesh(tower,new THREE.CylinderGeometry(1.25,1.25,2.1,16),'#b8e4db',0,17.2,0);mesh(tower,new THREE.ConeGeometry(2,1.3,20),'#ef7155',0,18.8,0);
 ball(tower,.48,material('#ffe7a2',.1),0,17.3,0);rod(tower,[0,19.2,0],[0,20.7,0],.07,'#315859');
 // Corner chevrons remain outside the full-circuit guardrails.
 const arrowMat=new THREE.MeshBasicMaterial({map:labelTexture('›',128,128,'#fbecc2','#286b70'),side:THREE.DoubleSide});
 for(let d=24;d<track.length;d+=16){const p=track.sample(d);if(Math.abs(p.curvature)<.012)continue;const outside=p.curvature>0?-1:1;const q=p.position.clone().addScaledVector(p.right,outside*(half+1.8));const g=new THREE.Group();g.position.copy(q);g.rotation.y=Math.atan2(p.tangent.x,p.tangent.z)+Math.PI;staticWorld.add(g);box(g,.10,2.3,.10,'#e9e0b4',0,1.15,0,.025);const sign=mesh(g,new THREE.PlaneGeometry(1.4,1.15),arrowMat,0,2.6,0);if(p.curvature<0)sign.scale.x=-1;
 }
 // Buildings and tents at the start, kept well clear of the circuit.
 for(let i=0;i<4;i++){const p=track.sample(55+i*18),v=p.position.clone().addScaledVector(p.right,-20);const y=terrainInfo(v.x,v.z).y;box(staticWorld,5.5,3.4,5,'#f4dfb0',v.x,y+1.7,v.z,.2);const roof=mesh(staticWorld,new THREE.ConeGeometry(4.4,2,4),i%2?'#4d9a94':'#e78e5e',v.x,y+4.3,v.z);roof.rotation.y=Math.PI/4;box(staticWorld,.05,1.8,1.2,'#427779',v.x+2.76,y+1.8,v.z,.01);}
 for(let i=0;i<12;i++){const g=new THREE.Group();g.position.set(-500+rnd()*1200,120+rnd()*140,-650+rnd()*950);scene.add(g);for(let j=0;j<4;j++){const c=ball(g,18,'#fff9e7',j*20,Math.sin(j*1.7)*4,0,1.7,.58,.9);c.castShadow=false;}batch(g);}
 // Distant islands provide a stable horizon while the near scenery flows past.
 for(let i=0;i<7;i++){const x=-500+i*180,z=-550-(i%3)*100;const island=mesh(staticWorld,new THREE.IcosahedronGeometry(1,2),'#86bdb0',x,-4,z);island.scale.set(70+rnd()*45,20+rnd()*38,45+rnd()*50);island.castShadow=false;}
 batch(staticWorld);
 return {terrainInfo,update(time,camera,focus){water.material.uniforms.time.value=time;water.material.uniforms.eye.value.copy(camera.position);sun.position.copy(focus).add(new THREE.Vector3(-90,150,60));sun.target.position.copy(focus);sky.position.copy(camera.position);}};
}
export function labelTexture(text,w,h,ink,paper){const c=document.createElement('canvas');c.width=w;c.height=h;const ctx=c.getContext('2d');ctx.fillStyle=paper;ctx.fillRect(0,0,w,h);ctx.fillStyle=ink;ctx.font='800 '+Math.floor(h*.65)+'px Avenir Next, sans-serif';ctx.textAlign='center';ctx.textBaseline='middle';ctx.fillText(text,w/2,h*.52,w*.95);const tex=new THREE.CanvasTexture(c);tex.colorSpace=THREE.SRGBColorSpace;return tex;}
```

## item-visuals.mjs

```javascript
import * as THREE from 'three';
import {RoundedBoxGeometry} from 'three/addons/geometries/RoundedBoxGeometry.js';
import {labelTexture} from './world.mjs';

export function createItemVisuals(scene,track,karts,initialBoxes){
 const cubeGeometry=new RoundedBoxGeometry(1.45,1.45,1.45,2,.18),edgeGeometry=new THREE.EdgesGeometry(cubeGeometry,22);
 const cubeMaterial=new THREE.MeshStandardMaterial({color:'#7becdf',emissive:'#2ca8ac',emissiveIntensity:.35,roughness:.25,metalness:.1});
 const iconMaterial=new THREE.MeshBasicMaterial({map:labelTexture('?',128,128,'#fff7d4','#248e95'),side:THREE.DoubleSide});
 const edgeMaterial=new THREE.LineBasicMaterial({color:'#eaffeb'}),ringMaterial=new THREE.MeshBasicMaterial({color:'#cbfff0',transparent:true,opacity:.45,side:THREE.DoubleSide});
 const boxes=initialBoxes.map(b=>{
  const root=new THREE.Group(),cube=new THREE.Mesh(cubeGeometry,cubeMaterial);root.add(cube,new THREE.LineSegments(edgeGeometry,edgeMaterial));
  for(let i=0;i<4;i++){const face=new THREE.Mesh(new THREE.PlaneGeometry(.9,.9),iconMaterial);face.position.set(Math.sin(i*Math.PI/2)*.734,0,Math.cos(i*Math.PI/2)*.734);face.rotation.y=i*Math.PI/2;root.add(face);}
  const ring=new THREE.Mesh(new THREE.RingGeometry(.9,1.15,32),ringMaterial);ring.rotation.x=-Math.PI/2;
  const p=track.sample(b.distance);root.position.copy(p.position).addScaledVector(p.right,b.lane);root.position.y+=1.65;ring.position.copy(root.position);ring.position.y=p.position.y+.08;
  scene.add(root,ring);return {root,ring,baseY:root.position.y};
 });
 const effects=karts.map(k=>{
  const shield=new THREE.Mesh(new THREE.SphereGeometry(1,24,14),new THREE.MeshBasicMaterial({color:'#73e3f4',transparent:true,opacity:.18,depthWrite:false}));shield.position.y=1.2;shield.scale.set(1.55,1.65,2.0);k.root.add(shield);
  const shieldRim=new THREE.Mesh(new THREE.TorusGeometry(1.5,.035,5,40),new THREE.MeshBasicMaterial({color:'#c1ffff',transparent:true,opacity:.9}));shieldRim.rotation.x=Math.PI/2;shieldRim.position.y=1;k.root.add(shieldRim);
  const flames=[-.55,.55].map(x=>{const m=new THREE.Mesh(new THREE.ConeGeometry(.27,1.8,8),new THREE.MeshBasicMaterial({color:'#ffd15b'}));m.rotation.x=-Math.PI/2;m.position.set(x,.47,-2.1);k.root.add(m);return m;});
  const held=new THREE.Mesh(new THREE.OctahedronGeometry(.18),new THREE.MeshBasicMaterial({color:'#a3fff1'}));held.position.set(0,2.9,0);k.root.add(held);return {shield,shieldRim,flames,held};
 });
 const shotGeometry=new THREE.SphereGeometry(.42,12,8),shotMaterial=new THREE.MeshBasicMaterial({color:'#ff8468'}),shotRingGeometry=new THREE.TorusGeometry(.62,.045,5,16),shotRingMaterial=new THREE.MeshBasicMaterial({color:'#ffe3b2'}),shots=new Map();
 const burstPool=Array.from({length:8},()=>{const m=new THREE.Mesh(new THREE.SphereGeometry(1,12,8),new THREE.MeshBasicMaterial({color:'#fff0b0',wireframe:true,transparent:true,opacity:.8,depthWrite:false}));scene.add(m);m.visible=false;return m;});
 return {update(s,time){
  boxes.forEach((v,i)=>{const b=s.boxes[i];v.root.visible=!!b&&b.cooldown<=0;v.ring.visible=v.root.visible;if(!v.root.visible)return;v.root.position.y=v.baseY+Math.sin(time*2.8+i)*.17;v.root.rotation.set(.12*Math.sin(time+i),time*.9+i,.12*Math.cos(time+i));v.ring.rotation.z=time*.3;});
  [s,...s.rivals].forEach((r,i)=>{const e=effects[i],active=s.mode!=='ready'&&s.mode!=='finished';e.shield.visible=e.shieldRim.visible=active&&r.shield>0;e.shield.material.opacity=.13+Math.sin(time*5)*.035;e.shieldRim.rotation.z=time;for(const f of e.flames){f.visible=active&&r.boost>0&&r.speed>2;f.scale.y=.8+Math.sin(time*40)*.2;}e.held.visible=active&&!!r.item;e.held.material.color.set(r.item==='boost'?'#ffe09b':r.item==='shot'?'#ff8468':'#85e5fb');e.held.rotation.y=time*2;});
  const alive=new Set(s.projectiles.map(p=>p.id));for(const [id,v]of shots)if(!alive.has(id)){scene.remove(v);shots.delete(id);}
  for(const p of s.projectiles){let v=shots.get(p.id);if(!v){v=new THREE.Group();v.add(new THREE.Mesh(shotGeometry,shotMaterial));v.add(new THREE.Mesh(shotRingGeometry,shotRingMaterial));scene.add(v);shots.set(p.id,v);}const point=track.sample(p.distance);v.position.copy(point.position).addScaledVector(point.right,p.lane);v.position.y+=1.25;v.rotation.set(time*5,time*3,0);v.scale.setScalar(p.delay>0?.6+Math.sin(time*25)*.1:1);}
  burstPool.forEach((v,i)=>{const b=s.bursts[i];v.visible=!!b;if(!b)return;const p=track.sample(b.distance);v.position.copy(p.position).addScaledVector(p.right,b.lane);v.position.y+=1;v.scale.setScalar((.55-b.life)*7+.3);v.material.opacity=b.life*1.6;v.material.color.set(b.kind==='shield'?'#a5ffff':'#ffe6a5');});
 }};
}
```

## input.mjs

```javascript
export const action=e=>({ArrowLeft:'left',KeyA:'left',ArrowRight:'right',KeyD:'right',Space:'accelerate',KeyZ:'item'}[e.code]||{ArrowLeft:'left',a:'left',A:'left',ArrowRight:'right',d:'right',D:'right',' ':'accelerate',z:'item',Z:'item'}[e.key]);
export class RaceInput{
 constructor(){this.reset();}
 reset(){this.held=new Map();this.edges={left:0,right:0,accelerate:0,item:0};}
 down(a,key){if(!a||this.held.has(key))return;this.held.set(key,a);this.edges[a]=.1;}
 up(key){this.held.delete(key);}
 sample(dt){const out={item:this.edges.item>0};this.edges.item=0;for(const a of ['left','right','accelerate']){out[a]=this.edges[a]>0||[...this.held.values()].includes(a);this.edges[a]=Math.max(0,this.edges[a]-dt);}return out;}
}
```

## audio.mjs

```javascript
export class RaceAudio{
 constructor(volume){this.volume=volume;this.on=false;}
 unlock(){if(!this.ctx){const Audio=window.AudioContext||window.webkitAudioContext;if(!Audio)return;this.ctx=new Audio();this.master=this.ctx.createGain();this.master.gain.value=0;this.master.connect(this.ctx.destination);this.engine=this.ctx.createOscillator();this.engine.type='sawtooth';this.filter=this.ctx.createBiquadFilter();this.filter.type='lowpass';this.filter.frequency.value=300;this.motor=this.ctx.createGain();this.motor.gain.value=.10;this.engine.connect(this.filter).connect(this.motor).connect(this.master);this.engine.start();this.low=this.ctx.createOscillator();this.low.type='triangle';this.lowGain=this.ctx.createGain();this.lowGain.gain.value=.22;this.low.connect(this.lowGain).connect(this.master);this.low.start();}this.ctx.resume().catch(()=>{});}
 set(on){this.unlock();this.on=on;if(this.ctx)this.master.gain.setTargetAtTime(on?this.volume:0,this.ctx.currentTime,.06);}
 tone(freq,duration=.12,type='sine'){if(!this.ctx||!this.on)return;const now=this.ctx.currentTime,o=this.ctx.createOscillator(),g=this.ctx.createGain();o.type=type;o.frequency.setValueAtTime(freq,now);g.gain.setValueAtTime(.0001,now);g.gain.exponentialRampToValueAtTime(.35,now+.012);g.gain.exponentialRampToValueAtTime(.0001,now+duration);o.connect(g).connect(this.master);o.start(now);o.stop(now+duration+.02);o.onended=()=>{o.disconnect();g.disconnect();};}
 update(speed,active){if(!this.ctx)return;const time=this.ctx.currentTime;this.engine.frequency.setTargetAtTime(36+speed*2.4,time,.08);this.low.frequency.setTargetAtTime(26+speed*1.15,time,.09);this.filter.frequency.setTargetAtTime(160+speed*16,time,.1);this.motor.gain.setTargetAtTime(active?.1:0,time,.15);this.lowGain.gain.setTargetAtTime(active?.18:0,time,.15);}
}
```

## main.mjs

```javascript
import * as THREE from 'three';
import {createTrack,clamp,mod} from './track.mjs';
import {createRace,ITEM_NAMES} from './core.mjs';
import {createItemVisuals} from './item-visuals.mjs';
import {createKart} from './models.mjs';
import {createWorld} from './world.mjs';
import {wallLimit} from './barrier.mjs';
import {RaceInput,action} from './input.mjs';
import {RaceAudio} from './audio.mjs';
import {validateSettings,recordKey} from './config.mjs';
const $=s=>document.querySelector(s),label=(sel,text)=>{const el=$(sel);if(el.textContent!==String(text))el.textContent=text;};
function fail(error){$('#loading').hidden=true;$('#error').hidden=false;$('#error-message').textContent=error.message||String(error);$('#reload').onclick=()=>location.reload();}
function timeText(s){const n=Math.round(Math.max(0,s)*100);return Math.floor(n/6000)+':'+String(Math.floor(n/100)%60).padStart(2,'0')+'.'+String(n%100).padStart(2,'0');}
async function boot(){
 const settings=JSON.parse($('#race-settings').textContent),errors=validateSettings(settings);if(errors.length)throw Error(errors.join(' / '));
 const assets=JSON.parse($('#race-assets').textContent),canvas=$('#game'),stage=$('#stage');
 const renderer=new THREE.WebGLRenderer({canvas,antialias:true,powerPreference:'high-performance'});renderer.setPixelRatio(Math.min(devicePixelRatio,settings.display.maxPixelRatio));renderer.outputColorSpace=THREE.SRGBColorSpace;renderer.toneMapping=THREE.ACESFilmicToneMapping;renderer.toneMappingExposure=1.05;renderer.shadowMap.enabled=settings.display.shadows;renderer.shadowMap.type=THREE.PCFSoftShadowMap;
 const scene=new THREE.Scene(),camera=new THREE.PerspectiveCamera(58,16/9,.1,2300),track=createTrack(settings.track),game=createRace(settings,track),s=game.s,input=new RaceInput(),audio=new RaceAudio(settings.audio.volume),loader=new THREE.TextureLoader();
 const roadTexture=await loader.loadAsync(settings.world.roadTexture||assets.asphalt);
 if(settings.hero.driverImage)await loader.loadAsync(settings.hero.driverImage);
 const world=createWorld(scene,track,settings,roadTexture),hero=createKart(settings.hero,loader);scene.add(hero.root);
 const rivals=s.rivals.map(r=>{const k=createKart({kartColor:r.color,accentColor:'#fff0c7',furColor:'#c7b393',helmetColor:r.helmet,driverImage:null},loader);scene.add(k.root);return k;});
 const itemVisuals=createItemVisuals(scene,track,[hero,...rivals],s.boxes);
 const cache=recordKey(settings)+(new URLSearchParams(location.search).has('qa')?':qa':'');let best=null;try{const val=Number(localStorage.getItem(cache));if(val>0&&Number.isFinite(val))best=val;}catch{}
 document.title=settings.identity.title+' — '+settings.identity.subtitle;
 stage.setAttribute('aria-label',settings.identity.title+' レーシングゲーム');label('#brand',settings.identity.subtitle);label('#title',settings.identity.title);label('#eyebrow',settings.identity.eyebrow);label('#description',settings.identity.description);label('#course-name',settings.identity.courseName);label('#race-format',settings.race.laps+'周 / 4台');$('#start').firstChild.textContent=settings.identity.startLabel+' ';$('#loading').hidden=true;$('#intro').hidden=false;
 const resize=()=>{const r=stage.getBoundingClientRect();renderer.setSize(Math.max(1,r.width),Math.max(1,r.height),false);camera.aspect=r.width/r.height;camera.updateProjectionMatrix();};const observer=new ResizeObserver(resize);observer.observe(stage);resize();
 let previous=0,clock=0,lastMode='',lastRank=4,toastTimer=0,camReady=false,finishAt=0,frameCount=0,fpsClock=0,fps=0;
 const camLook=new THREE.Vector3(),target=new THREE.Vector3(),desired=new THREE.Vector3();
 const resetInputs=()=>input.reset();
 function begin(){resetInputs();game.start();audio.set(true);label('#audio','音 ON');$('#audio').setAttribute('aria-label','音をオフにする');canvas.focus();camReady=false;}
 function pause(){game.pause();resetInputs();canvas.focus();}
 $('#start').onclick=begin;$('#again').onclick=()=>{resetInputs();game.reset();camReady=false;};$('#pause').onclick=pause;$('#resume').onclick=pause;$('#restart').onclick=()=>{resetInputs();game.reset();camReady=false;};
 $('#item-use').onclick=()=>{if(s.mode==='racing'){input.down('item','item-button');input.up('item-button');canvas.focus({preventScroll:true});}};
 $('#audio').onclick=()=>{audio.set(!audio.on);label('#audio',audio.on?'音 ON':'音 OFF');$('#audio').setAttribute('aria-label',audio.on?'音をオフにする':'音をオンにする');canvas.focus();};
 $('#full').onclick=()=>{if(document.fullscreenElement)document.exitFullscreen?.();else stage.requestFullscreen?.().catch(()=>{});};
 $('.wordmark').onclick=e=>{e.preventDefault();if(s.mode==='racing'||s.mode==='countdown')pause();};
 window.addEventListener('keydown',e=>{if(e.code==='Escape'||e.key==='Escape'){if(!e.repeat)pause();return;}if(e.code==='KeyM'||e.key==='m'){if(!e.repeat)$('#audio').click();return;}const a=action(e);if(a&&['racing','countdown','paused'].includes(s.mode)){e.preventDefault();if(s.mode!=='paused')input.down(a,e.code||e.key);}},{capture:true});
 window.addEventListener('keyup',e=>input.up(e.code||e.key),{capture:true});
 const autoPause=()=>{if(['racing','countdown'].includes(s.mode))pause();else resetInputs();};window.addEventListener('blur',autoPause);document.addEventListener('visibilitychange',()=>{if(document.hidden)autoPause();});
 canvas.addEventListener('pointerdown',()=>canvas.focus({preventScroll:true}));
 if(matchMedia('(pointer:coarse)').matches)$('#touch').hidden=false;
 for(const b of document.querySelectorAll('[data-action]')){const a=b.dataset.action; b.onpointerdown=e=>{e.preventDefault();b.setPointerCapture(e.pointerId);if(['racing','countdown'].includes(s.mode))input.down(a,'touch:'+a);};for(const name of ['pointerup','pointercancel','lostpointercapture'])b.addEventListener(name,()=>input.up('touch:'+a));}
 function toast(text){label('#toast',text);$('#toast').classList.add('show');toastTimer=2.6;}
 const mapCanvas=$('#map'),map=mapCanvas.getContext('2d'),bounds={minX:Math.min(...track.points.map(p=>p.x)),maxX:Math.max(...track.points.map(p=>p.x)),minZ:Math.min(...track.points.map(p=>p.z)),maxZ:Math.max(...track.points.map(p=>p.z))};
 const mapScale=Math.min(165/(bounds.maxX-bounds.minX),133/(bounds.maxZ-bounds.minZ)),mx=x=>105+(x-(bounds.minX+bounds.maxX)/2)*mapScale,my=z=>82+(z-(bounds.minZ+bounds.maxZ)/2)*mapScale;
 function mapDraw(){map.clearRect(0,0,210,165);map.beginPath();track.points.forEach((p,i)=>{if(i===0)map.moveTo(mx(p.x),my(p.z));else map.lineTo(mx(p.x),my(p.z));});map.lineWidth=8;map.strokeStyle='#244d5c70';map.stroke();map.lineWidth=3;map.strokeStyle='#fff5d9d9';map.stroke();for(const r of s.rivals){const p=track.sample(r.distance).position;map.beginPath();map.arc(mx(p.x),my(p.z),4,0,Math.PI*2);map.fillStyle=r.color;map.fill();map.lineWidth=1;map.strokeStyle='#ffffff';map.stroke();}const p=track.sample(s.distance).position;map.beginPath();map.arc(mx(p.x),my(p.z),5,0,Math.PI*2);map.fillStyle=settings.hero.kartColor;map.fill();map.lineWidth=2;map.strokeStyle='#fff9de';map.stroke();}
 function setMode(){
  if(lastMode===s.mode)return;lastMode=s.mode;
  $('#intro').hidden=s.mode!=='ready';$('#hud').hidden=s.mode==='ready';$('#paused').hidden=s.mode!=='paused';$('#ending').hidden=s.mode!=='finished';$('#countdown').hidden=s.mode!=='countdown';$('#pause').disabled=['ready','finished'].includes(s.mode);$('#pause').textContent=s.mode==='paused'?'▶':'Ⅱ';$('#pause').setAttribute('aria-label',s.mode==='paused'?'再開':'一時停止');
  if(s.mode==='ready'){label('#toast','');$('#toast').classList.remove('show');toastTimer=0;$('#start').focus({preventScroll:true});}
  if(s.mode==='paused')$('#resume').focus({preventScroll:true});
  if(s.mode==='finished'){finishAt=clock;label('#finish-rank',s.finishRank);label('#finish-time',timeText(s.finishTime));label('#lap-times',s.lapTimes.map((t,i)=>'LAP '+(i+1)+'  '+timeText(t)).join('　 /　'));if(!best||s.finishTime<best){best=s.finishTime;try{localStorage.setItem(cache,String(best));}catch{}label('#record','自己ベスト更新！');}else label('#record','自己ベスト '+timeText(best));$('#again').focus({preventScroll:true});}
 }
 function positionKart(k,d,lane,speed,steer,braking,heading=0){const p=track.sample(d);k.root.position.copy(p.position).addScaledVector(p.right,lane);k.root.position.y+=.14;k.root.rotation.set(-Math.atan2(p.tangent.y,Math.hypot(p.tangent.x,p.tangent.z)),Math.atan2(p.tangent.x,p.tangent.z)-heading,0,'YXZ');k.update(speed,steer,braking,clock);return p;}
 function render(now){
  const dt=previous?Math.min(.08,(now-previous)/1000):0;previous=now;clock+=dt;frameCount++;fpsClock+=dt;if(fpsClock>1){fps=frameCount/fpsClock;fpsClock=0;frameCount=0;}
  const keys=input.sample(dt);game.step(dt,keys);setMode();
  if(toastTimer>0){toastTimer-=dt;if(toastTimer<=0)$('#toast').classList.remove('show');}
  for(const e of s.events.splice(0)){if(e.text)toast(e.text);if(e.type==='count')audio.tone(440,.13);if(e.type==='go'){audio.tone(880,.35);toast('GO!');}if(e.type==='pass')audio.tone(720,.15);if((e.type==='hit'||e.type==='wallHit')&&e.racer==='player')audio.tone(100,.16,'triangle');if(e.type==='pickup'&&e.racer==='player')audio.tone(1047,.15);if(e.type==='use'&&e.racer==='player')audio.tone(e.item==='shot'?320:e.item==='boost'?660:880,.23);if(e.type==='block'&&e.racer==='player')audio.tone(1320,.2);if(e.type==='itemHit'&&e.racer==='player')audio.tone(160,.2,'triangle');if(e.type==='finish'){audio.tone(660,.35);setTimeout(()=>audio.tone(880,.5),160);}if(e.type==='lap')audio.tone(990,.25);}
  const p=positionKart(hero,s.distance,s.lane,s.speed,s.steer,s.braking,s.heading);
  s.rivals.forEach((r,i)=>positionKart(rivals[i],r.distance,r.lane,r.speed,r.steer,r.braking,r.heading));
  itemVisuals.update(s,clock);
  const focus=hero.root.position.clone();
  if(s.mode==='ready'){
   desired.copy(focus).addScaledVector(p.tangent,-7).addScaledVector(p.right,6.2);desired.y+=4.4;target.copy(focus).addScaledVector(p.right,-3.1).addScaledVector(p.tangent,2.4);target.y+=1.05;camera.fov=51;
  }else if(s.mode==='finished'){
   const a=Math.min(1,(clock-finishAt)/2)*.32;desired.copy(focus).addScaledVector(p.tangent,-8).addScaledVector(p.right,4+a*8);desired.y+=4.5;target.copy(focus);target.y+=1.1;camera.fov=55;
  }else{
   const forward=p.tangent.clone().multiplyScalar(Math.cos(s.heading)).addScaledVector(p.right,Math.sin(s.heading));
   desired.copy(focus).addScaledVector(forward,-8.8-s.speed*.025);desired.y+=4.6+s.speed*.015;target.copy(focus).addScaledVector(forward,13);target.y+=1.0;camera.fov=55+s.speed/settings.race.maxSpeed*5;
  }
  const smooth=1-Math.exp(-dt*8);if(!camReady){camera.position.copy(desired);camLook.copy(target);camReady=true;}else{camera.position.lerp(desired,smooth);camLook.lerp(target,smooth);}camera.lookAt(camLook);camera.updateProjectionMatrix();
  world.update(clock,camera,focus);renderer.render(scene,camera);audio.update(s.speed,s.mode==='racing');
  label('#rank',s.rank);label('#lap',s.lap+' / '+settings.race.laps);label('#timer',timeText(s.mode==='finished'?s.finishTime:s.time));label('#best',best?'BEST '+timeText(best):'');label('#speed',Math.round(s.speed*3.6));$('#speed-bar').style.width=(Math.min(1,s.speed/settings.race.maxSpeed)*100)+'%';label('#pedal',s.mode==='finished'?'FINISH':s.wallSide?(s.wallSide>0?'← 左へハンドル':'右へハンドル →'): s.slow>0?'スピードダウン':s.boost>0?'ダッシュ！':s.braking?'ブレーキ':keys.accelerate?'アクセル':'SPACE でアクセル');
  const incoming=s.projectiles.some(v=>v.target==='player');$('#threat').hidden=!incoming||s.mode!=='racing';label('#threat',s.shield>0?'バリアでガード中':s.item==='shield'?'おじゃま弾 接近！ Zでバリア':'おじゃま弾 接近！');
  $('#item-slot').hidden=!settings.items.enabled||!['racing','countdown'].includes(s.mode);$('#item-use').disabled=!s.item||s.mode!=='racing';$('#item-use').dataset.item=s.item||'empty';$('#item-use').setAttribute('aria-label',s.item?ITEM_NAMES[s.item]+'を使う（Zキー）':'アイテムボックスを拾うと使えます');
  label('#item-name',s.item?ITEM_NAMES[s.item]:'アイテム');label('#item-hint',s.item==='boost'?'直線で一気に加速':s.item==='shot'?'前のライバルをねらう':s.item==='shield'?'おじゃま弾・衝突を防ぐ':'？ボックスを拾おう');$('#item-icon use').setAttribute('href','#icon-'+(s.item||'box'));
  const effect=s.shield>0?'shield':s.boost>0?'boost':s.slow>0?'slow':null;$('#effect').hidden=!effect;label('#effect',effect==='shield'?'バリア '+s.shield.toFixed(1)+'秒':effect==='boost'?'ダッシュ！':effect==='slow'?'スピードダウン':'');
  const progress=mod(s.distance,track.length)/track.length,section=settings.track.sections.filter(v=>v.at<=progress).at(-1);label('#section',section.name);
  const aheadCurve=track.maxCurve(s.distance,38),steeringCapacity=Math.tan(settings.race.steering*Math.PI/180/(1+settings.race.speedSteeringFalloff*(s.speed/settings.race.maxSpeed)**2))/settings.race.wheelbase,needsBrake=Math.abs(aheadCurve)*1.2>steeringCapacity;
  $('#corner').hidden=Math.abs(aheadCurve)<.012||s.mode!=='racing';$('#corner').classList.toggle('brake',needsBrake);label('#turn-arrow',aheadCurve>0?'↱':'↰');label('#turn-label',needsBrake?'スペースを離して減速':aheadCurve>0?'右へハンドル':'左へハンドル');
  if(s.mode==='countdown')label('#countdown',Math.max(1,Math.ceil(s.countdown)));
  $('#collision-flash').style.opacity=String(Math.max(s.collision,s.wallFlash)*.4);mapDraw();
  Object.assign(canvas.dataset,{state:s.mode,lap:String(s.lap),laps:String(settings.race.laps),rank:String(s.rank),speed:s.speed.toFixed(3),distance:s.distance.toFixed(3),length:track.length.toFixed(3),lane:s.lane.toFixed(3),lateral:s.lateral.toFixed(3),heading:s.heading.toFixed(5),recoveryLock:String(s.recoveryLock),steer:s.steer.toFixed(3),curve:p.curvature.toFixed(5),ahead:aheadCurve.toFixed(5),time:s.time.toFixed(2),offroad:s.offroadTotal.toFixed(2),recoveries:String(s.recoveries),collisions:String(s.collisions),fps:String(Math.round(fps)),gameId:settings.identity.id,drawCalls:String(renderer.info.render.calls),driver:settings.hero.driverImage?'image':'model'});
  Object.assign(canvas.dataset,{revision:'steering-slowdown',accelerating:String(!!keys.accelerate),steerSlowdown:(settings.race.steerSlowdown*Math.abs(s.steer)).toFixed(4),wallSide:String(s.wallSide),wallHits:String(s.wallHits),wallTime:s.wallTime.toFixed(2),wallLimit:wallLimit(track,s.heading).toFixed(4),item:s.item||'',boost:s.boost.toFixed(2),shield:s.shield.toFixed(2),slow:s.slow.toFixed(2),incoming:String(incoming),collected:String(s.collected),used:JSON.stringify(s.used),hits:String(s.hits),blocks:String(s.blocks),passes:String(s.overtakes),passed:String(s.passed),leadChanges:String(s.leadChanges),closeSeconds:s.closeSeconds.toFixed(2),rivals:JSON.stringify(s.rivals.map(r=>({id:r.id,distance:+r.distance.toFixed(2),lane:+r.lane.toFixed(2),speed:+r.speed.toFixed(2),item:r.item,used:r.used,hits:r.hits,blocks:r.blocks,finished:r.finished}))),boxes:JSON.stringify(s.boxes.filter(b=>!b.cooldown).map(b=>({distance:+b.distance.toFixed(2),lane:b.lane}))),boostAhead:track.maxCurve(s.distance,75).toFixed(5)});
  requestAnimationFrame(render);
 }
 requestAnimationFrame(render);
 canvas.addEventListener('webglcontextlost',e=>{e.preventDefault();autoPause();fail(Error('描画が一時停止しました。ページを開き直してください。'));});
}
boot().catch(fail);
```

