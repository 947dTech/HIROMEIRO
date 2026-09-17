トラッキング用ソフトウェアについて
##################################

HIROMEIROではmediapipeのholistic landmarkerに基づいたトラッキング結果を受信します。
mediapipeは多言語対応、マルチプラットフォームで動作するため、
複数の環境用にトラッキングソフトウェアを用意しています。


トラッキング用ソフトウェアの種類
********************************

- Tauri版(最新バージョンに同梱)

    - 動作環境: Windows 11, 他のOSでも動作する可能性があります
    - 2026/9/14現在、mediapipe側の挙動により、表情パラメータの出力ができないことを確認しています。別途自前で計算しているため表情自体は動きます。

- Python版

    - 動作環境: Ubuntu 24.04, Python + uvがインストールされていれば他のOSでも動作する可能性があります。
    - CUI前提、キャリブレーション必須、など、開発者向けを想定しています。難易度は高めです。
    - 現状すべての機能が安定して動作しています。

- Android用app

    - Android Studioのみでビルド可能です。
    - 2026/9/14現在、mediapipe側の挙動により、顔が隠れるとトラッキングが止まることを確認しています。そのため背面のトラッキングを行いたい場合は別のソフトウェアを使用してください。

- 旧Android用app(旧バージョンに同梱、非推奨)

    - 旧APIを使用しています。ビルド難易度も高いです。
    - 旧APIの仕様により表情パラメータの出力には非対応です。


トラッキング用ソフトウェアのビルド(開発者向け)
**********************************************

Tauri版
=======

ソースコードを以下から入手してください。

https://github.com/947dTech/mediapipe_transceiver_app

最低限rustとnodejsがインストールされていることが前提となります。
またtauriの事前準備も参照してください。


Python版
========

ソースコードを以下から入手してください。

https://github.com/947dTech/mediapipe_python_transceiver

python, uvを事前にインストールしてください。
またlibopencv-devは事前にapt等でインストールを行ってください。


Android用app
============

**2026/9/14現在、顔が隠れるとトラッキングが止まるというmediapipe側の挙動を確認しています。ご了承の上使用してください。**

まず、お手持ちのAndroid端末を開発者モードにしてください。
設定→デバイス情報からビルド番号を連打すると移行できます。

ソースコードを以下から入手してください。

https://github.com/947dTech/HolisticTransceiver

Android Studio単体でビルド可能です。
Android Studioからプロジェクトを開いてビルドしてください。


旧Android用app
==============

まず、お手持ちのAndroid端末を開発者モードにしてください。
設定→デバイス情報からビルド番号を連打すると移行できます。

以下の操作はDockerが動くUbuntu端末を推奨します。

改造版mediapipeのソースコードを入手してください。

https://github.com/947dTech/mediapipe

``holistic_v0.9.2.1_release`` というブランチを使用します。

mediapipe環境の構築はDockerの利用を推奨します。

https://google.github.io/mediapipe/getting_started/install.html#installing-using-docker

Androidビルド環境の構築は公式ドキュメントを参照してください。

https://google.github.io/mediapipe/getting_started/android.html

Android appのビルドは以下のコマンドでできます。

```
$ bazelisk build -c opt --config=android_arm64 --linkopt="-s" mediapipe/examples/android/src/java/com/google/mediapipe/apps/holistictrackinggpu:holistictrackinggpu
```

インストールは実機をadbで認識させた上で、以下の方法でできます。

```
$ adb install bazel-bin/mediapipe/examples/android/src/java/com/google/mediapipe/apps/holistictrackinggpu/holistictrackinggpu.apk
```

トラッキング用ソフトウェアの実行方法
************************************

Tauri版
=======

起動画面で送信先IPアドレスを入力してください。
同じPCで実行する場合は  ``127.0.0.1`` のままで動作します。
Initボタンを押すとトラッキングが開始されます。

トラッキング画面の上方のドロップダウンメニューからカメラを選択してください。

その隣に画角とカメラの向きを指定するフィールドがあります。
これらは以下のように設定して下さい。

- 対角画角はご使用のカメラのスペックを調べて入力して下さい。
- カメラの向きは重力ベクトル(X-right, Y-up, Z-front)で与えてください。カメラを水平に固定している場合は初期値そのままで動作します。

詳細はソフトウェア側のマニュアルを参照してください。


Python版
========

オプション ``--host`` で送信先IPアドレスを入力します。

カメラパラメータはキャリブレーションによって求めることができます。

カメラの向きは自分で与える必要があります。
``--gravity`` で向きを与えてください。

詳細はソフトウェア側のマニュアルを参照してください。


Android用app
============

実行し、画面に映像がうつること、人物の骨格構造が認識されることを確認してください。
右上のボタンでカメラを切り替えることができます。

実行できたら、左上の歯車アイコンから設定を開き、
送信先IPアドレスを入力します。

スマホの向きをスマホ本体のセンサで認識しているため、
カメラは縦位置でも横位置でも動作します。


旧Android用app
==============

実行し、画面に映像がうつること、人物の骨格構造が認識されることを確認してください。
環境によっては起動に時間がかかることがあります。

実行できたら、右上のメニューからSettingを開き、
送信先IPアドレスを入力します。

スマホの向きをスマホ本体のセンサで認識しているため、
カメラは縦位置でも横位置でも動作します。


UDPメッセージ定義(開発者向け)
*****************************

- camera_params : 以下の子要素を含みます。

    - frame_width : 画像幅[px]
    - frame_height : 画像高さ[px]
    - focal_length : 焦点距離[px]、fx,fyがどちらも指定されている場合は無くても動作します。
    - fx : X軸方向の焦点距離[px]、ない場合はfocal_lengthを優先します。
    - fy : Y軸方向の焦点距離[px]、ない場合はfocal_lengthを優先します。
    - cx : X軸方向の画像中心[px]、ない場合はframe_width/2となります。
    - cy : Y軸方向の画像中心[px]、ない場合はframe_height/2となります。
    - rotation_degrees: センサーに対する画像の回転角度[度]、0,90,180,270のいずれかです。デフォルトは270(Androidスマホ準拠)となります。 

- gravity : 重力ベクトルを三次元配列で[m/s^2]、X-right, Y-up, Z-centerとなります。
- gravity_stamp : タイムスタンプ[ns]
- pose_landmarks : 2Dの姿勢推定結果
- pose_landmarks_stamp : タイムスタンプ[ns]
- pose_world_landmarks : 3Dの姿勢推定結果
- pose_world_landmarks_stamp : タイムスタンプ[ns]
- face_landmarks : 顔の推定結果
- face_blendshapes : 表情パラメーター(推定結果がある場合)、名前:値の形式で格納
- face_landmarks_stamp : タイムスタンプ[ns]
- right_hand_landmarks : 2Dの右手推定結果、2Dか3Dのどちらかがあれば動作します。
- right_hand_landmarks_stamp : タイムスタンプ[ns]
- right_hand_world_landmarks : 3Dの右手推定結果、2Dか3Dのどちらかがあれば動作します。
- right_hand_world_landmarks_stamp : タイムスタンプ[ns]
- left_hand_landmarks : 2Dの左手推定結果、2Dか3Dのどちらかがあれば動作します。
- left_hand_landmarks_stamp : タイムスタンプ[ns]
- left_hand_world_landmarks : 3Dの左手推定結果、2Dか3Dのどちらかがあれば動作します。
- left_hand_world_landmarks_stamp : タイムスタンプ[ns]
