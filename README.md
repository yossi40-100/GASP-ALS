# GASP-ALS fork by Yossi40-100
This is a GASP-ALS fork.  
これはGASP-ALSから派生したものです。

## Features
### FPSカメラ追加
https://youtu.be/gOz7RRNOJY0

1キーと左ボタンにFPSモード切り替え機能を追加しました。

GamePlay Cameraとそのプラグイン仕様を廃止し、UE5.4仕様の復刻（動作不安定の実験的機能のため）
ゲームパッドのアサインを変更しました。Controlsウィジェット参照。

- 右トリガーでファイヤー
- Y＋上下でオーバーレイ選択
- 左でFPS/TPS
- 右でキャラクター変更

#### English
Added FPS mode switching functionality to the 1 key and left button.

Eliminated GamePlay Camera and its plugin spec, reverting to UE5.4 spec (for experimental features with unstable operation).

Changed gamepad assignments, see Controls widget.
- Right Trigger to fire
- Y + D-pad up/down for overlay selection
- D-pad Left for FPS/TPS
- D-pad Right to change character


### NPCと戦えるように
https://youtu.be/HemUhKjy-Bw
https://yossi40-100.com/ue5gasp4/


マニーをベースにビヘイビアツリー制御の敵キャラを作成

overlay_stateでライフルかピストルを選ぶと攻撃できる

- TODO: エイムの方向がまだあってないのでこちらから動かなきゃそうやられない

発見時の「！」音は、効果音ラボ様 決定ボタンを押す13

#### English
Create a behavior tree controlled enemy character based on Manny.

Select rifle or pistol in overlay_state to attack.

- TODO: Aim direction is still not right, so you have to move to avoid being hit.
- TODO: There is a bug that attacks don't hit if you are too close.

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

#### English
Add the position information of the handles to the Props and align the hands to that position.  
Changed to compensate after retargeting in order to accurately compensate for handle position even for retargeted characters.
ABP is changed from generic to individual so that skeletal mesh information can be referenced.

When a curve with Enable_HandIK_L/_R of 1 is added in the overlay animation,
The correction is made only when the flag is off "DoingTraversalAction".

Skeletal meshes add sockets for left and right hand positions named LHGrip and RHGrip.
Static meshes cannot add sockets directly, so they are blueprinted and scene components are added.
Blueprint the skeletal mesh as well (since we want to implement processes such as gun firing anyway),
The BPI_Item interface provides socket information to CBP_SandboxCharacter.

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


