# Retro Agent SDK

Retro Agent は、レトロゲーム開発のためのTypeScriptベースのSDKです。
Babylon.jsやPhaserなどの既存のゲームエンジンと組み合わせて使用することができ、革新的な機能を提供します。

## 特徴

Retro Agent SDKは、以下の主要な機能を提供します：

1. 自動マップ生成
2. 高度なNPC AI制御
3. ダイナミックアイテム管理システム
4. クエスト自動生成
5. チップチューンミュージック生成機能 (by SUNO API)

これらの機能により、開発者はより迅速かつ効率的にゲームを作成できます。


## 利用しているゲームエンジン

Retro Agent SDKは、ゲーム開発に特化した強力なツールキットです。
このSDKは、BabylonJSやPhaserなどの現代的なゲームエンジンと組み合わせて使用することで、ゲームを効率的に開発できます。BabylonJSとの組み合わせでは、BabylonJSの3D環境に統合することで、レトロな2Dグラフィックスと現代的な3D要素を融合させた独特な視覚体験を創出できます。Retro Agent SDKは、これらのゲームエンジンと互換性のあるAPIを提供し、開発者はシームレスに統合できます。


## 詳細説明

### 1.自動マップ生成

プロンプトを使ってマップの自動生成を行えます。サイズの指定や、利用するオブジェクトの種類に加えて、どのような場面で利用するかなどを記載することで自動的にマップを生成します。通常、道などを接続する場合に上下左右など回転などを考えて配置することや、山などの起伏のあるものを作る場合に隣り合ったマップチップとの整合性を考えることは非常に大変な作業でした。また、敵NPCが動くための隠れるポイントや敵のアジトをどのように配置するかなど、ゲームなどによっては細かな調整項目が多いことも挙げられます。retro agent sdkを使うことで、これらをプロンプトで作成することができるため、一度プロンプトを作成しておけば、何通りもの異なるマップを自動生成して、ユーザーに新しい体験を作り出すことが容易となります。Retro Agent SDKの自動マップ生成機能は、開発者の作業を大幅に効率化します。

- **プロンプトベースの生成**: 簡単な説明文でマップを生成
- **カスタマイズ可能**: サイズ、オブジェクトタイプ、シーン設定などを指定可能
- **整合性の自動調整**: 地形の起伏や道路の接続を自動的に最適化
- **ゲームプレイ要素の自動配置**: 敵の隠れ場所やアジトなどを自動的に配置

```typescript
import { RetroAgent } from 'retro-agent-sdk';

const agent = new RetroAgent();

const mapPrompt = `
  サイズ: 20x20
  テーマ: 中世の城
  要素: 城壁、塔、中庭、秘密の通路
`;

const generatedMap = await agent.generateMap(mapPrompt);

console.log(generatedMap);
```

### 2.AIドリブンNPC制御

NPCの行動をプロンプトで簡単に定義できます。パックマンの敵のNPCは種類によって様々なロジックが使われているのは有名ですが、プロンプトで指定することで様々な敵の動作を作ることができます。また、ロジックで作成する場合、敵の視野や、歩行スピード、警戒状態になった場合の動作ロジックなどの切り替えなど細かな実装が難しいですが、プロンプトを使うことでシンプルに指示を与えることができます。例えばプロンプトで、「通常は陣地の中を右回りに監視警戒を行うような行動をとる。視野は3で、プレイヤーを発見した場合、30秒の間、追跡体制となりプレイヤーを追跡する。30秒の間に、プレイヤーを視野の中に入れることができなかった場合は通常のモードに戻って監視警戒の行動となる」というような形で指定することでNPCは与えられた指示をこなすことができます。

- **柔軟な行動パターン**: 複雑なAIロジックをシンプルな文章で表現
- **状況に応じた行動変化**: 視野、警戒状態、追跡ロジックなどを簡単に設定
- **カスタマイズ可能なパラメータ**: 視野範囲、追跡時間などを自由に調整

Retro Agentの最大の特徴は、自然言語プロンプトを使用してNPCの複雑な行動パターンを定義できることです。

**例: パックマン風の敵AI**
```typescript
const ghostBehavior = retroAgent.createNPC(`
  通常は陣地の中を右回りに監視警戒を行う。
  視野は3で、プレイヤーを発見した場合、30秒間追跡体制となる。
  30秒以内にプレイヤーを見失った場合、通常モードに戻る。
`);
```

### 3.ダイナミックアイテム管理システム

現実世界の法則に基づいたアイテム効果を自動生成します。アイテムがプレイヤーに与える様々な効果を実世界を参考にして、パラメータを自動生成することができます。例えば、焼いた肉が与える効果は空腹を満たすことですが、焼いた魚と比べてどうであるか、または生の肉を食べた場合には、副作用としてプレイヤーにどのようなマイナス効果を与えるかなどをパラメータとして指定することが可能です。また料理などの組み合わせおよび、パラメーターの作成は非常に難易度の高いものですが、実世界の情報と、プロンプトを活用することで、卵とベーコンを組み合わせることで、ベーコンエッグが作られることや、そのパラメーターが及ぼす影響についても自動生成が可能です。他にも繊維などを作って洋服を作成することや、金属などを使って武器を生成することなども、可能となります。

- **リアルな効果生成**: 食べ物、武器、防具などの効果を自動計算
- **アイテム組み合わせ**: 複数のアイテムを組み合わせて新しいアイテムを作成
- **バランス調整**: アイテムの効果を自動的にバランス調整


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


## ユースケース

1. **ローグライクゲーム**: 毎回異なるダンジョンを自動生成
2. **ステルスゲーム**: 複雑なAI行動パターンを持つ敵を簡単に作成
3. **サバイバルクラフトゲーム**: 多様なアイテムとその組み合わせを自動生成


## インストール方法

```javascript
npm create vite@latest
cd vite-project
npm install
npm install @babylonjs/core @babylonjs/loaders
npm install --save retro-agent-sdk
npm run dev
```

## 自律的なAI Agentがゲームにもたらす未来


## まとめ

Retro Agent SDKは、レトロゲーム開発を革新的に簡素化します。自動マップ生成、高度なAI制御、ダイナミックなアイテム管理システムにより、開発者はクリエイティブな側面に集中できます。プロンプトベースのインターフェースにより、複雑な機能を簡単に実装でき、多様なゲームジャンルに対応可能です。また、AI Agentによる自律的なNPCの導入は、ゲーム体験を大きく変革し、より魅力的で没入感のある世界を創り出す可能性を秘めています。

1. **動的なクエスト生成**
NPCたちが自律的に行動することで、プレイヤーの行動や世界の状況に応じて新しいクエストが自然に発生します[5]。例えば、あるNPCが困っている状況を別のNPCが察知し、プレイヤーに助けを求めるといった具合です。

2. **リアルタイムの世界変化**
NPCたちが自分たちで判断して行動するため、プレイヤーがいない間も世界が変化し続けます[8]。町の状況が刻々と変わったり、NPCたち同士の関係性が変化したりすることで、より生きた世界を体験できます。

3. **個性的な対話と関係性構築**
各NPCが独自の性格や目標を持ち、プレイヤーとの対話履歴を記憶します[30]。これにより、プレイヤーとNPCの間に深い関係性が築かれ、より感情的な繋がりを感じられるストーリー体験が可能になります。

4. **予測不可能なストーリー展開**
NPCたちの自律的な判断により、開発者も予想していなかったようなストーリー展開が生まれる可能性があります[39]。これにより、プレイヤーは毎回新鮮な体験を楽しむことができます。

5. **生きているような街の雰囲気**
NPCたちが自分たちの日常生活を送り、互いにコミュニケーションを取ることで、街全体が生き生きとした雰囲気を醸し出します[15]。プレイヤーは本当に生きている世界に入り込んだような没入感を味わえるでしょう。

これらの要素により、ゲームはより動的で予測不可能なものとなり、プレイヤーの好奇心を刺激し続けることができます。同時に、NPCたちとの深い感情的な繋がりを通じて、より豊かなストーリー体験を提供することが可能になります。AI Agentによる自律的なNPCの導入は、ゲームの再現性を高め、プレイヤーにとってより魅力的で没入感のある世界を創り出す大きな可能性を秘めているのです。


情報源
[1] 25人の自律的なエージェントがつくる社会｜岡瑞起 Mizuki Oka - note https://note.com/mizuki_oka/n/nf1770c3dc28b
[2] [PDF] 生物学的制約の導入による「人間らしい」振る舞いを伴う ゲームAI ... https://ipsj.ixsq.nii.ac.jp/ej/?action=repository_action_common_download&item_id=95814&item_no=1&attribute_id=1&file_no=1
[3] [PDF] Expressive AI: Games and Artificial Intelligence https://users.soe.ucsc.edu/~michaelm/publications/mateas-digra2003.pdf
[4] Artificial intelligence in video games - Wikipedia https://en.wikipedia.org/wiki/Artificial_intelligence_in_video_games
[5] AI革命でクリエイティブはどうなる？｜こたまる - note https://note.com/cotamaru6/n/ncd96f835fcea
[6] AI革命がゲーム開発に与える影響とその未来像 | Reinforz Insight https://reinforz.co.jp/bizmedia/50772/
[7] AI-Driven NPCs: The Future of Gaming Explained - Capermint https://www.capermint.com/blog/everything-you-need-to-know-about-non-player-character-npc/
[8] #1。 ユーザーがゲームの中のキャラクターに出会うと、「私に何か ... https://www.mk.co.kr/jp/it/11134115
[9] ゲームAIの最新動向：機械学習と深層学習の応用事例 https://www.change-makers.jp/technology/gameai-trend
[10] # ユーザーがゲームの中のキャラクターに出会うと、「私に何か言い ... https://www.mk.co.kr/jp/it/11133697
[11] ゲームAIとは？歴史から最新活用事例まで徹底解説 - Alibaba Cloud https://www.alibabacloud.com/help/ja/cloud-migration-guide-for-beginners/latest/game-ai
[12] ゲームAIが新時代を切り開く！AIの概要や種類、最新の活用事例を ... https://weel.co.jp/media/game-ai/
[13] ゲームAIから考える、AIと人間の最適な関係性とは？ https://seikatsusha-ddm.com/article/15049/
[14] [PDF] 4 ビデオゲームに浸透する エージェント技術 - 情報学広場 https://ipsj.ixsq.nii.ac.jp/ej/index.php?action=pages_view_main&active_action=repository_action_common_download&item_id=66001&item_no=1&attribute_id=1&file_no=1&page_id=13&block_id=8
[15] UnityのAI機能がゲーム開発に与える影響 https://tendo-programming.com/column/20240312035556-7ea014f0-6c25-465d-b479-f8af65c026ce
[16] 自律型AIゲームエージェントに共同投資へ 次世代ゲーム開発支援 https://coinpost.jp/?p=586734
[17] Pacman AI https://pacman.calvinjc.com
[18] 10 Must-Try Artificial Intelligence Games That Redefine Gameplay https://autogpt.net/10-must-try-artificial-intelligence-games-that-redefine-gameplay/
[19] Reinforcement Learning: Pac-Man - NDC Oslo 2020 - YouTube https://www.youtube.com/watch?v=XgPRWcX4sYU
[20] 'Video games are in for quite a trip': How generative AI could ... - CNN https://edition.cnn.com/world/generative-ai-video-games-spc-intl-hnk/index.html
[21] Artificial Intelligence - Pacman Capture The Flag - Creed's Log Book https://abhinavcreed13.github.io/projects/ai-team-pacamon/
[22] Understanding Pac-Man Ghost Behavior - GameInternals https://gameinternals.com/understanding-pac-man-ghost-behavior
[23] The Role of AI in Modern Game Design and Experiences - DigiMantra https://digimantralabs.com/blog/the-role-of-ai-in-shaping-next-gen-game-engines
[24] Real-Time Game Design of Pac-Man Using Fuzzy Logic https://www.researchgate.net/publication/220413631_Real-Time_Game_Design_of_Pac-Man_Using_Fuzzy_Logic
[25] Non-Player Characters and Artificial Intelligence - ResearchGate https://www.researchgate.net/publication/345257663_Non-Player_Characters_and_Artificial_Intelligence
[26] Silent Protagonists Look Like Idiots in Modern RPGs According to ... https://www.reddit.com/r/dragonquest/comments/1ecyrz2/silent_protagonists_look_like_idiots_in_modern/
[27] Has anyone figured out a detailed understanding of NPCs' AI? https://gamefaqs.gamespot.com/boards/215336-dragon-quest-builders-2/77969542
[28] The quest for npc behavior | RPG Maker Forums https://forums.rpgmakerweb.com/index.php
[29] NPC AI and behavior should be better. :: Dragon's Dogma 2 General ... https://steamcommunity.com/app/2054970/discussions/0/7607215003626909911/?l=english
[30] NPC Believability in Single-Player RPGs - ResearchGate https://www.researchgate.net/publication/370829074_Cognitive_Intervention_and_Reconciliation_-_NPC_Believability_in_Single-Player_RPGs
[31] Nvidia researchers use AI to recreate PAC-MAN, without a game ... https://www.zdnet.com/article/nvidia-researchers-use-ai-to-recreate-pac-man-without-a-game-engine/
[32] AI in Gaming: Creating Adaptive and Intelligent Game Environments https://fpgainsights.com/artificial-intelligence/ai-in-gaming-creating-adaptive-and-intelligent/
[33] 40 Years on, PAC-MAN Recreated with AI by NVIDIA Researchers https://blogs.nvidia.com/blog/gamegan-research-pacman-anniversary/
[34] Artificial intelligence in video games - Wikipedia https://en.wikipedia.org/wiki/Artificial_intelligence_in_video_games
[35] When AI Sparks into Web3 Games & Metaverse - Ancient8 https://research.ancient8.gg/research/en/articles/when-AI-sparks-into-games-and-metaverse
[36] The Evolution of AI in Video Games - Nero AI https://ai.nero.com/blog/video-game-ai-evolution/
[37] RTC monster AI and NPC - Dungeon Master Forum https://www.dungeon-master.com/forum/viewtopic.php?t=29668
[38] [TTS] From NPC Origins to Today's AI - YouTube https://www.youtube.com/watch?v=mjNPbC2LRsI
[39] [PDF] The Turing Quest: Can Transformers Make Good NPCs? https://aclanthology.org/2023.acl-srw.17.pdf
[40] [PDF] The Effect of Context-aware LLM-based NPC Conversations on ... https://projekter.aau.dk/projekter/files/536738243/The_Effect_of_Context_aware_LLM_based_NPC_Dialogues_on_Player_Engagement_in_Role_playing_Video_Games.pdf
[41] NVIDIA Redefines Game AI With ACE Autonomous Game Characters https://www.nvidia.com/en-us/geforce/news/nvidia-ace-autonomous-ai-companions-pubg-naraka-bladepoint/

