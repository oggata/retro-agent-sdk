---
theme: "night"
title: "Retro Agent SDK"
enableChalkboard: false
slideNumber: true
---

# Retro Agent SDK

---

### AIAgent x Game

<small>
KRAFTON「CES 2025」にてNVIDIAと共にAIイノベーション技術「Co-Playable Character」が発表。
「CPC」は人間のようにゲームの状況を認識し、ゲームプレイヤーと会話をして協力する能力を実現。
</small>

---

### Retro Agent SDK

<small>
Retro Agent は、レトロゲーム開発のためのTypeScriptベースのSDKです。
Babylon.jsやPhaserなどの既存のゲームエンジンと組み合わせて使用することができ、革新的な機能を提供します。
WasmでのWeb-LLMを使うことでローカル実行を行うことで手軽にAIの活用が可能です。
</small>

---

### 特徴


1. 自動マップ生成
2. 高度なNPC AI制御
3. ダイナミックアイテム管理システム
4. クエスト自動生成
5. チップチューンミュージック生成機能 (by SUNO API)
<small>
これらの機能により、開発者はより迅速かつ効率的にゲームを作成できます。
</small>
---

### 1.自動マップ生成


- **プロンプトベースの生成**: 簡単な説明文でマップを生成
- **カスタマイズ可能**: サイズ、オブジェクトタイプ、シーン設定などを指定可能
- **整合性の自動調整**: 地形の起伏や道路の接続を自動的に最適化
- **ゲームプレイ要素の自動配置**: 敵の隠れ場所やアジトなどを自動的に配置


---

```typescript
import { RetroAgent } from 'retro-agent-sdk';

const agent = new RetroAgent();

const mapPrompt = `
    マップのサイズ:
      縦30*横30 合計900
    マップの高さ:
      1,2,3,4,5,6,7,8
    出力形式：
      マップの高さを配列で表現して出力してください 例[1,2,1,4,2,2,3,1,4]
      配列の順番は左上から右下に向かって順番に配列に入れる形式を取ります。
    ルール：
      平均的な高さは3として、道路などは3のマップで作成してください。
      川や海などは3よりも低い高さ、つまり1か2として表現します。
      また、川の幅は1で、なるべく繋がるように考えて配置してください。
      起伏のあるようなマップにして欲しいのですが、プレイヤーは、マップの高さの差が1より大きいマップには登れないため、なるべくなだらかに作成するようにしてください。
      マップの中に少なくとも一つは最大の高さが8になるような山を配置するようにしてください。
      山の形状は上から見たときになるべく円を描くような形にしてください。
  その他の条件：{map_create_prompt}
`;

const generatedMap = await agent.generateMap(mapPrompt);

console.log(generatedMap);
```

---

<img src="./map_create3.gif" width="100%">

---

### 2.AIドリブンNPC制御


- **柔軟な行動パターン**: 複雑なAIロジックをシンプルな文章で表現
- **状況に応じた行動変化**: 視野、警戒状態、追跡ロジックなどを簡単に設定
- **カスタマイズ可能なパラメータ**: 視野範囲、追跡時間などを自由に調整
<small>
Retro Agentの最大の特徴は、自然言語プロンプトを使用してNPCの複雑な行動パターンを定義できることです。
</small>
---

```typescript

import { RetroAgent } from 'retro-agent-sdk';

const agent = new RetroAgent();

const behaviorPrompt = `
  条件:
      あなたはゲームのNPCです。あらかじめ与えられたマップの条件と、視野を元に、東西南北のどの方向に
      進めば良いかを意思決定してください。
      基本的なロジックは、プレイヤーが視野に入るまでは巡回行動を取ります。
      一定の区間を巡回するように動いてください。
      プレイヤーが視野に入った場合、プレイヤーを追いかけるような行動を取ってください。
    ルール：
      視野から得られる情報は、自分から見て東西南北にあるオブジェクトの情報を配列にしたものです。
      9の番号が自分の位置です。
      [
        0,0,0,0,0,
        0,0,0,0,0,
        0,0,9,0,0,
        0,0,0,0,0,
        0,0,0,0,0
      ]

      視野の番号と行いたい行動についてはこちらになります。
      0:何もない
      1:猪  プレイヤーと重なると飢えを回復することができるので、追いかけてください
      2:熊  プレイヤーと重なるとプレイヤーは死亡します。逃げてください。
      3:木  木材となります。木材を使って家をつくれるので、追いかけてください。
      9:自分の位置
    ....
    視野から得られる情報：{field_of_vision_info}
`;

const npc = await agent.createNPC(mapPrompt);

//tickの中で呼び出し
let d = npc.getDirection(      
  [        
    0,0,0,0,0,
    0,0,0,0,0,
    0,0,0,0,2,
    0,0,0,0,0,
    0,0,0,0,0
);

```

---

<img src="./npc_walk3.gif" width="100%">

---

### 3.ダイナミックアイテム管理システム


- **リアルな効果生成**: 食べ物、武器、防具などの効果を自動計算
- **アイテム組み合わせ**: 複数のアイテムを組み合わせて新しいアイテムを作成
- **バランス調整**: アイテムの効果を自動的にバランス調整


---

```typescript
import { RetroAgent, Item, CookingSystem } from 'retro-agent';

// RetroAgentのインスタンスを作成
const agent = new RetroAgent();

// アイテムを作成
const rawMeat = new Item('生肉', { nutrition: 5, toxicity: 2 });
const cookedMeat = new Item('焼肉', { nutrition: 8, toxicity: 0 });

// 調理システムを使用
const cookingSystem = new CookingSystem(agent);
const cookedResult = cookingSystem.cook(rawMeat);

console.log(cookedResult.name); // 出力: '焼肉'
console.log(cookedResult.effects); // 出力: { nutrition: 8, toxicity: 0 }

// プレイヤーにアイテムを使用
const player = agent.createPlayer();
player.useItem(cookedMeat);

console.log(player.status); // 出力: { health: 100, hunger: 92 } (空腹度が8減少)
```

<small>
この例では、生肉を調理して焼肉を作成し、それをプレイヤーに使用する過程を示しています。RetroAgentが自動的にアイテムの効果を計算し、プレイヤーのステータスに反映します。
</small>

---

### ユースケース


1. **ローグライクゲーム**: 毎回異なるダンジョンを自動生成
2. **ステルスゲーム**: 複雑なAI行動パターンを持つ敵を簡単に作成
3. **サバイバルクラフトゲーム**: 多様なアイテムとその組み合わせを自動生成


---

### インストール方法

```javascript
npm create vite@latest
cd vite-project
npm install
npm install @babylonjs/core @babylonjs/loaders
npm install --save retro-agent-sdk
npm run dev
```

---

### まとめ

<small>
これらの要素により、ゲームはより動的で予測不可能なものとなり、プレイヤーの好奇心を刺激し続けることができます。
同時に、NPCたちとの深い感情的な繋がりを通じて、より豊かなストーリー体験を提供することが可能になります。
</small>

---

### demonstration

https://oggata.github.io/retro-agent-demo/

<img src="./QR.png">
