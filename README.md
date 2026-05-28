# Doppler Circle Monitor

円運動する音源（または観測者）のドップラー効果を、Arduino Nano ESP32 製の周波数センサだけで定量計測するブラウザアプリです。

共通テスト 2023 年・物理「第 3 問」（音源が等速円運動し、観測者が円外で静止する設定）と同じ構図を、実機で再現することを想定しています。

本システムは Physics Exam Lab 物理入試ラボシリーズの一環として開発されたプロトタイプであり、姉妹リポジトリ [physicsexamlab/dopplercart](https://github.com/physicsexamlab/dopplercart) から smart cart 関連を取り除いた派生版です。

---

## 必要機器

| 機器 | 用途 |
|---|---|
| スマートフォン + phyphox | 2000 Hz 音源（[phyphox.org](https://phyphox.org/)）|
| Arduino Nano ESP32 + MAX9814 マイクアンプモジュール | 周波数センサ |
| 回転機構（電動ターンテーブル・回転アーム等） | 音源または観測者の等速円運動 |
| PC（Mac / Windows / Chromebook）+ **Google Chrome** | 計測アプリの実行 |

> Safari・Firefox には Web Bluetooth API が実装されていないため使用できません。

---

## 実験の構成

以下の 2 通りが可能です。どちらも観測者と音源の相対速度はサインカーブ状に変化し、周波数も同様に振動します。

- **Config A（音源が動く）**：スマートフォン（音源）を回転アームに固定し、マイク（観測者）を円外で静止させる。
- **Config B（観測者が動く）**：マイク（観測者）を回転アームに固定し、スマートフォン（音源）を円外で静止させる。

低速近似（$v \ll v_s$）では、観測周波数は

$$f(t) \approx f_0 - \frac{f_0}{v_s} \, v_r(t)$$

で与えられ、$v_r(t)$ は音源・観測者を結ぶ線方向の相対速度成分（半径 $r$、角速度 $\omega$ の等速円運動なら $v_r(t) = r\omega \cos(\omega t + \phi)$ 型の関数）です。

$f_0 = 2000\,\text{Hz}$、$v_s = 343\,\text{m/s}$。

---

## 使い方

### 1. ファームウェアの書き込み（初回のみ）

周波数センサのファームウェアは姉妹リポジトリ [dopplercart](https://github.com/physicsexamlab/dopplercart) と共通です。書き込みページは以下：

**https://physicsexamlab.github.io/dopplercart/flash.html**

書き込み手順の詳細（USB ケーブル抜き差し → RESET ボタン押下による ROM ブートローダ突入）は [dopplercart README](https://github.com/physicsexamlab/dopplercart#1-ファームウェアの書き込み初回のみ) を参照してください。

### 2. 計測アプリの起動

Chrome で以下の URL を開きます。

**https://physicsexamlab.github.io/dopplercircle/doppler-circle.html**

1. スマートフォンで phyphox を起動し、2000 Hz を最大音量で再生する
2. **[Connect Freq Sensor]** をクリックして周波数センサを接続する
3. **[Record]** を押した後、回転を始める前に音源／観測者をマイク近傍で 5 秒以上静置する
   （この区間を含めることで、全フレームの中央値による $f_0$ 校正（ESP32 xtal バイアス補正）が安定する）
4. 円運動を開始し、**1 周以上記録**する（中央値が $f_0$ に一致するには対称な往復が必要）
5. **[Stop]** を押す
6. **[CSV Export]** で計測データをダウンロードする

CSV の列は `時間(s),周波数(Hz)` の 2 列のみです。

---

## 周波数センサの回路

MAX9814 マイクアンプモジュールと Arduino Nano ESP32 を次のように接続します。

| MAX9814 | Arduino Nano ESP32 |
|---|---|
| Out | A0（GPIO1）|
| GND | GND |
| Vdd | 3.3V |
| GAIN | フローティング（最大ゲイン 60 dB）|

USB-C ケーブルで電池パックに接続して動作します。

---

## リポジトリ構成

```
doppler-circle.html   計測アプリ（Chrome で開く）
README.md             このファイル
```

ファームウェア本体・書き込みページは [dopplercart](https://github.com/physicsexamlab/dopplercart) を参照してください。

---

## ライセンス

Copyright (c) 2026 一般社団法人 国際物理オリンピック2023記念協会

Licensed under the [Creative Commons Attribution-NonCommercial 4.0 International License](https://creativecommons.org/licenses/by-nc/4.0/).
