# GASP-ALS fork by Yossi40-100
これはGASP-ALSから派生しシューター化したものです。  
This is a derivative of the GASP-ALS and is a shooter.

- https://yossi40-100.com/category/game/unrealengine/ue5gasp/
- https://yossi40-100.com/gasp-als-doc-2/

## Features
### 制限緩和・仕様変更
1. スプリント中にエイムできない制限を解除
2. エイム解除に最短0.75秒かかる制限を解除
3. TPS時、エイムオフセットのピッチ角度を上に持ち上げ

横や後ろ移動中もShift押しているとエイムできなかったのが気になっていたので、
修正がてら制限をなくしました。

エイムから撃って解除すると早いのに、エイム直後に解除しようとすると遅いのを早い側に統一しました。

３人称肩越し視点だとカメラがやや上から見下ろすため、
カメラアングル中心へのエイムオフセットでは下すぎる問題を補正する変数`OffsetPitchAngle_TPS`を追加しました。

### 内部仕様改善
1. 武器などのアイテムの使用に関する処理を、CBP直接編集からBPI_Itemに依頼する形に整理しました。
  オーバーレイステート（武器）追加時の変更点が軽減します。

2. 操作入力イベントの移動漏れ
  CBP_RetargetPlayerBaseに、IA_Fire, IA_Reloadを移動


### カバー状態の課題を改修
- https://youtu.be/6LR9CCx5EI0
- https://yossi40-100.com/gasp-als-cover-2/

1. 後ろ向きからカバーに入ると体の向きや首のエイムがおかしくなる問題を修正
　※ 後ろ向きからカバーに入る機能そのものはつけていません
2. カバー突入から解除までが一瞬の場合に位置やカプセルサイズがおかしくなる問題を修正
3. カバーに入るときの向きを左固定から、壁への侵入角に応じてどちらに首を向けるか決める仕様に改善
4. カバーからの飛び出し時のエイム・エイム解除タイミングをタイマーで改善
5. カバー状態→ラグドール→復帰時に、壁のないところでもカバー姿勢になる問題を修正


### SandboxCharacterからカメラとプレイヤー操作を分離
少しでも処理負荷下げたり派生開発しやすくなるかなと思い、機能のモジュール化に着手しました。


CBP_RetargetPlayerBase を作成し、そこに入力処理とカメラを移動しました。

AIは今まで通りSandboxCharacterを継承すれば、カメラなどの無駄がなくなります。
プレイヤーキャラはCBP_RetargetPlayerBaseをベースにすれば今まで通りのことができつつ、
私の変更を取り込みやすくなるかもしれませｎ。

CBP_SandboxCharacterは直接操作できなくなりました。
→ 個人的には問題ないですが、一応その代わりとなる CBP_DummyUEFN を用意しました。  
リターゲットキャラ用のCBPで隠れているMeshを表示させているのでほぼもとのSandboxCharacterと同じ。


### 一通りのInputActionを登録
 デバッグキーをIA_**で一通り登録しました。  
これにより、本体を改造する必要がない場合は、IMC_Sandboxだけで自分の好みのキーアサインに置き換え可能になるはずです。
※ MetaHuman用の数字キーは除く

それにともないゲームパッドのキーアサインを一部見直しました。
詳しくは Widgets/Controls に記載 ＆ ブログで


### 持ち手のHand-IK方法を大幅変更
overlay_xxのアイテムごとのソケットをやめ、hand_l_socket, hand_r_socketに統一し、
そこからのオフセットを追加の変数で管理するようにしました。
これにより、スケルトンが共通でもキャラごとに設定値を変えることができるようになりました。  

結果的にキャラ個別のABPでリターゲット後に再度Two-Bone IKで補正する必要もなくなり、
ABP_GenericRetargetに再統一しました。

さらに、ABP_GenericRetarget内で持っていたIKRetargeterのMapを廃止し、CBPからセットする仕様に変更しました  
これにより、ABP_GenericRetargetは基本的に触る必要がなくなり、CBPでキャラごとの設定が可能になりました。


ついでに、コンストラクションスクリプトに位置調整用のデバッグ処理を追加しました。

#### オーバーレイアイテム持ち手位置調整方法
adjustMode フラグをオンにして、追加した OverlayOffset 変数のフラグをオンにすると、
コンストラクションスクリプトにより、そのアイテムをビューポート上のキャラが持ってエイム姿勢になります
この状態で変数の値を変えると表示も更新されます。

- OverlayOffset
-- .main: hand_r_socketからのオフセット 位置と回転が調整可能
-- .hand_ik: Item側のBPのLHGrip／RHGripからのオフセット トランスフォーム型だが、現在位置のみが補正可能
-- .adjustPos: 調整したいアイテムにチェックをつける （1つだけチェックつけること。複数つけるとForの都合一番下のが選ばれるよ）

注意：adjustModeを有効にしたまま保存するとコンストラクションスクリプトなのでレベル上の該当クラスのアクターすべてに影響します


### その他 貯めていた細かい改善をまとめて実施

- GameplayCamerasの変数がないエラーメッセージが大量に出ているのでウィジェットから該当の選択を削除し抑制
```
LogBlueprintUserMessages: Warning: Failed to find console variable 'GameplayCameras.Debug.Enable'.
```

- Lyraの減衰設定などの同期漏れ追加 と SFXクラスをエンジンのものに割り当てなおした
- FPS/TPS視点時のカメラアームの親を、常にeyeソケットではなく、FPS視点時のみ切り替え時に取り付けなおす仕様に変更
-- TPS視点時に壁に埋もれるGASPの制限の影響で、トラバーサル時やカバー時に映像がクリッピングする問題の改善
- FPS視点時にストレイフモードじゃないと体と目線が不一致になる（目線カメラなはずが目が真後ろまで回る）のを改善
- 双眼鏡の立ちのエイムだけHand-IKが効かないGASP-ALSのバグを修正

#### 備考
- 最近本家GASP-ALSリポジトリがアップデートされましたが、追従しません。
GamePlay CameraでのFPS視点追加などで、当リポジトリではすでに廃止済で追従メリットが現状ないので。

- 英訳も併記していましたが、ブラウザの翻訳などで十分だと思うので消しました。（当初想定より記載が膨れたため）


### カバーシステムを追加
https://youtu.be/mRV0O7LGwZ4

壁に背を向けてくっつくカバーシステムを追加しました。
- 暫定で壁の近くでFキーを押すと張り付き／張り付き解除（キーアサインを決めてない）
- 張り付いた状態でエイムキーで壁から飛び出し／戻り
- アニメーションはMixamoベースでレベルシーケンスで編集したもの

TODO: いろいろあり（カバーのままトラバースできちゃうなど）


### インタラクションでとって装備できる武器を作成
https://youtu.be/wWzzEkMiqBc

武器などのアイテムを地面から拾って装備などができる仕組み BP_Pickup を作成しました。
IA_Interact（Eキー）で動作します


### Slotによるモンタージュ再生ができない問題を修正
https://youtu.be/XPHdReRM_BY

ALSのコア部分をABP_SandboxCharacterにお引越し。

サンプルとしてLyraのPistolのアニメーションを使いたかったが銃のスケルトンが異なるのでPistolごと移植しM9と置き換えた。

UpperBody Slotも利用可能にした。

Hand-IKを同じカーブの値で上書きできなかったので、Ignore_HandIKという新しい名前のカーブを用意して、手動で打ち消す処理とした

TODO: リターゲットキャラのHand-IK対応（UE5マニーだけやってある）
TODO: 新しいマガジンを左手に持ててない


### Prone状態追加
https://youtu.be/kBXotVnTXFg
https://youtu.be/2t0p_avIEEA
https://youtu.be/dzBPIvb3-vo
https://youtu.be/g5TUcB72lCE

Cキー長押しで伏せProne状態を追加しました。
必要な高さがないと起き上がれないようにしてあります。

- TODO: 武器のエイムオフセット（上下）
- TODO: コリジョンが小さいので壁にめり込みます
- TODO: 横や後ろなどアニメの充実化

### FPSカメラ追加
https://youtu.be/gOz7RRNOJY0

1キーと左ボタンにFPSモード切り替え機能を追加しました。

GamePlay Cameraとそのプラグイン仕様を廃止し、UE5.4仕様の復刻（動作不安定の実験的機能のため）
ゲームパッドのアサインを変更しました。Controlsウィジェット参照。

- 右トリガーでファイヤー
- Y＋上下でオーバーレイ選択
- 左でFPS/TPS
- 右でキャラクター変更


### NPCと戦えるように
https://youtu.be/HemUhKjy-Bw
https://yossi40-100.com/ue5gasp4/


マニーをベースにビヘイビアツリー制御の敵キャラを作成

overlay_stateでライフルかピストルを選ぶと攻撃できる

- TODO: エイムの方向がまだあってないのでこちらから動かなきゃそうやられない

発見時の「！」音は、効果音ラボ様 決定ボタンを押す13


### Hand-IKを調整
https://youtu.be/TXwtCx30DwY  
https://yossi40-100.com/ue5gaspals2/

Propsに持ち手の位置情報を追加し、その位置に手を合わせます。  
リターゲットキャラクターにも持ち手位置を正確に補正するため、リターゲット後に補正する形に変更しました。
ABPを汎用のものから個別に変更し、スケルタルメッシュ情報を参照できるようにしています。

オーバーレイアニメーションでEnable_HandIK_L または _Rが1のカーブが追加されている場合かつ、
DoingTraversalActionフラグがoffのときのみ補正されます。

スケルタルメッシュはLHGrip、RHGripという名前で左手・右手位置用のソケットを追加します。
スタティックメッシュは直接ソケットを追加できないため、ブループリント化してシーンコンポーネントを追加します。
スケルタルメッシュも（どのみち銃の発射などの処理を実装したいため、）ブループリント化し、
BPI_ItemインターフェースにてCBP_SandboxCharacterにソケット情報を提供する作りとしています。


## License

[UE-Only Content - Licensed for Use Only with Unreal Engine-based Products](https://www.unrealengine.com/en-US/eula/content)  
UnrealEngine上でしか使えないアセットを含むため、本リポジトリの内容物もすべてその扱いとなります。

This is the same as the original


## Original information
Below is the original readme  
以下はオリジナル（Fork元の情報です）

# Game Animation Sample with Overlay Layering

## Introduction

Advanced Locomotion System (ALS) provides a nice Overlay System that allows us to alter the entire locomotion animation just by applying simple overlay poses. 
This project integrates the ALS Overlay Layering System into the new Unreal Engine Motion Matching Game Animation Sample.

## Features

- Game Animation Sample
- Overlay layering system built with separate Anim Graphs and Linked Layers
- All overlays from ALS
- Basic weapon attach system from ALS
- Basic overlay switcher widget from ALS
- Removed Echo and Twinblast characters and Manny/Quinn 4k textures to lower project size

## Overview

An overview of the system is available on my YouTube channel [Polygon Hive](https://www.youtube.com/watch?v=RDWNfIqvWBk&list=PLs9e0eJQMI2aaulgKJzC8feN1UEwDkEnq)

## Contributing

Contributions are welcome! I hope that, with the help of the community, we can turn this into a next-gen fully featured locomotion system. 

Please follow these steps to contribute:

1. Clone the repository.
2. Create a new branch (`git checkout -b feature/your-feature`).
3. Make your changes.
4. Commit your changes (`git commit -m 'Add some feature'`).
5. Push to the branch (`git push origin feature/your-feature`).
6. Open a pull request.

Please ensure your code follows the project's coding and naming standards.

## License

[UE-Only Content - Licensed for Use Only with Unreal Engine-based Products](https://www.unrealengine.com/en-US/eula/content)

## Contact

For questions, suggestions, or feedback, please contact:

- Anas EL FERACHI - [anas@polygonhive.com](mailto:anas@polygonhive.com)

## Support My Work

- [Buy Me A Coffee](https://buymeacoffee.com/PolygonHive)
---

Thanks for checking out the project! I hope it helps you create amazing games.


