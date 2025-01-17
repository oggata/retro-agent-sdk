---
theme: "night"
title: "Retro Agent SDK"
enableChalkboard: false
slideNumber: true
---

# Retro Agent SDK

---

### AIAgent x Game

KRAFTON「CES 2025」にてNVIDIAと共にAIイノベーション技術「Co-Playable Character」が発表。
「CPC」は人間のようにゲームの状況を認識し、ゲームプレイヤーと会話をして協力する能力を実現。

---

### Retro Agent SDK

Retro Agent は、レトロゲーム開発のためのTypeScriptベースのSDKです。
Babylon.jsやPhaserなどの既存のゲームエンジンと組み合わせて使用することができ、革新的な機能を提供します。

---

### 特徴

1. 自動マップ生成
2. 高度なNPC AI制御
3. ダイナミックアイテム管理システム
4. クエスト自動生成
5. チップチューンミュージック生成機能 (by SUNO API)

これらの機能により、開発者はより迅速かつ効率的にゲームを作成できます。

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
  マップサイズ: 30x30
  マップの要素: 0:緑地, 1:道路, 2:川, 3:木, 4:丘 5;橋
  出力形式：要素番号を順番に配列で書き出し 例[1,0,1,0,2,2,3,1,4]
  ルール：道路や川は繋げてください。川は一定数接続するごとに橋を繋げて渡れるようにしてください。
  木や丘はある程度まとまって配置してください。
  その他の条件：{map_create_prompt}
`;

const generatedMap = await agent.generateMap(mapPrompt);

console.log(generatedMap);
```

---

![マップ生成](./map_create2.gif) 

---

### 2.AIドリブンNPC制御

- **柔軟な行動パターン**: 複雑なAIロジックをシンプルな文章で表現
- **状況に応じた行動変化**: 視野、警戒状態、追跡ロジックなどを簡単に設定
- **カスタマイズ可能なパラメータ**: 視野範囲、追跡時間などを自由に調整

Retro Agentの最大の特徴は、自然言語プロンプトを使用してNPCの複雑な行動パターンを定義できることです。

---

```typescript

import { RetroAgent } from 'retro-agent-sdk';

const agent = new RetroAgent();

const behaviorPrompt = `
  視野の番号と行いたい行動についてはこちらになります。
  0:何もない
  1:猪  プレイヤーと重なると飢えを回復することができるので、追いかけてください
  2:熊  プレイヤーと重なるとプレイヤーは死亡します。逃げてください。
  3:木  木材となります。木材を使って家をつくれるので、追いかけてください。
  9:自分の位置
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

![NPC歩行](./npc_walk2.gif) 

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
この例では、生肉を調理して焼肉を作成し、それをプレイヤーに使用する過程を示しています。RetroAgentが自動的にアイテムの効果を計算し、プレイヤーのステータスに反映します。

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

これらの要素により、ゲームはより動的で予測不可能なものとなり、プレイヤーの好奇心を刺激し続けることができます。
同時に、NPCたちとの深い感情的な繋がりを通じて、より豊かなストーリー体験を提供することが可能になります。
AI Agentによる自律的なNPCの導入は、プレイヤーにとってより魅力的で没入感のある世界を創り出す大きな可能性を秘めています。
