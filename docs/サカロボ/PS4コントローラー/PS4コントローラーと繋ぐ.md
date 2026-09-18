# PS4コントローラーと繋ぐ

モーターを動かすプログラムができたので、次はPS4コントローラーで操作できるようにしていきます。この章の最後では、左スティックで前後、右スティックで左右回転ができるロボットのプログラムを作ります。

まずはPS4コントローラーとマイコンをBluetoothで繋いでみましょう。

## 繋がる仕組み

[全体像を知る](../全体像を知る.md)で説明したように、Mega2560にはBluetoothの機能がありません。そこで次の順番でPS4コントローラーのデータを受け取ります。

```
PS4コントローラー → (Bluetooth) → Bluetoothドングル → (USB) → USBホストシールド → (SPI) → マイコン
```

この間のやり取りは、セットアップの章でインストールした **USB Host Shield Library 2.0** が全部やってくれます。私たちはライブラリから「どのボタンが押されたか」「スティックがどれだけ傾いたか」を受け取るだけです。

## ペアリングする

初めて繋ぐときは、マイコンとPS4コントローラーを **ペアリング** (お互いを登録すること)します。

```cpp
#include <PS4BT.h>
#include <usbhub.h>
#include <SPI.h>

USB Usb;
BTD Btd(&Usb);
PS4BT PS4(&Btd, PAIR);

void setup() {
    Serial.begin(9600);

    if (Usb.Init() == -1) {
        Serial.println("USBホストシールドが見つかりません");
        while (true) {
        }
    }
    Serial.println("PS4コントローラーを待っています");
}

void loop() {
    Usb.Task();

    if (PS4.connected()) {
        Serial.println("繋がっています");
    } else {
        Serial.println("繋がっていません");
    }
    delay(1000);
}
```

1. USBホストシールドにBluetoothドングルを挿して、プログラムを書き込みます
2. PS4コントローラーの **SHAREボタンとPSボタンを同時に長押し** します
3. コントローラーのライトが速く点滅したら手を離します
4. ライトが点灯したままになり、シリアルモニタに「繋がっています」と出ればペアリング成功です

このコードは次のように出力されます。

```
PS4コントローラーを待っています
繋がっていません
繋がっていません
繋がっています
繋がっています
.
.
.
```

## ペアリングしたあと

一度ペアリングすると、ドングルがコントローラーを覚えます。次からは`PAIR`を消して書き込みます。

```cpp
PS4BT PS4(&Btd);
```

こうしておくと、電源を入れたあと **PSボタンを押すだけ** で繋がります。`PAIR`を付けたままだと、毎回SHARE+PSの長押しが必要になります。

!!! warning "ドングルやコントローラーを替えたら、もう一度ペアリングします"

    ペアリングの情報はドングルとコントローラーの組み合わせで覚えています。別のドングルや別のコントローラーを使うときは、`PAIR`を付けて書き込み直してください。

## 1行ずつ見ていく

**`#include <PS4BT.h>`・`#include <usbhub.h>`・`#include <SPI.h>`**

PS4コントローラー用の機能と、USBホストシールドとの通信(SPI)に使う機能を読み込みます。3つともセットで書きます。

**`USB Usb;`・`BTD Btd(&Usb);`・`PS4BT PS4(&Btd, PAIR);`**

上から順に「USBホストシールド」「そこに挿したBluetoothドングル」「ドングルと繋がるPS4コントローラー」の実物を作ります。`&Usb`は「`Usb`に挿さっている」、`&Btd`は「`Btd`を通して繋がる」という意味だと思ってください。この3行は形を変えずにそのまま使います。

**`Usb.Init()`**

USBホストシールドを使い始めます。うまくいかないと`-1`が返ってくるので、そのときは「見つかりません」と表示して止めます。USBホストシールドがないとコントローラーは絶対に繋がらないので、ここは止めてしまってかまいません。

**`Usb.Task();`**

USBホストシールドからデータを受け取って、ボタンやスティックの状態を新しくします。**`loop()`の最初に必ず書きます**。これを書かないと、コントローラーを操作しても値が変わりません。

**`PS4.connected()`**

PS4コントローラーが繋がっていれば`true`、繋がっていなければ`false`になります。

!!! warning "`loop()`の中で長い`delay()`を使わないでください"

    `Usb.Task()`は`loop()`が1周するたびに1回しか呼ばれません。`delay(1000)`があると1秒に1回しかデータを受け取れず、操作してから動くまでに時間がかかります。上のコードは表示を読みやすくするために`delay(1000)`を入れていますが、ロボットを動かすプログラムでは`delay()`を使わないようにします。

## 演習

上のペアリング後のプログラム(`PS4BT PS4(&Btd);`)を使って、コントローラーが繋がっている間は13番ピンのLEDを点け、繋がっていないときは消すプログラムを書いてください。シリアルモニタへの表示と`delay()`はなくしてかまいません。

??? tip "ヒントを見る"

    | やること | 書き方 |
    | --- | --- |
    | 1. LEDのピンに名前を付ける | `#define STATUS_LED_PIN 13` |
    | 2. `setup()`でLEDのピンを出力にする | `pinMode(STATUS_LED_PIN, OUTPUT);` |
    | 3. `loop()`の最初でデータを受け取る | `Usb.Task();` |
    | 4. 繋がっていたら点ける | `if (PS4.connected())`の中で`digitalWrite(STATUS_LED_PIN, HIGH);` |
    | 5. 繋がっていなければ消す | `else`の中で`digitalWrite(STATUS_LED_PIN, LOW);` |

??? success "答えを見る"

    ```cpp
    #include <PS4BT.h>
    #include <usbhub.h>
    #ifdef dobogusinclude
    #include <spi4teensy3.h>
    #endif
    #include <SPI.h>

    #define STATUS_LED_PIN 13

    USB Usb;
    BTD Btd(&Usb);
    PS4BT PS4(&Btd);

    void setup() {
        Serial.begin(9600);
        pinMode(STATUS_LED_PIN, OUTPUT);

        if (Usb.Init() == -1) {
            Serial.println("USBホストシールドが見つかりません");
            while (true) {
            }
        }
        Serial.println("PS4コントローラーを待っています");
    }

    void loop() {
        Usb.Task();

        if (PS4.connected()) {
            digitalWrite(STATUS_LED_PIN, HIGH);
        } else {
            digitalWrite(STATUS_LED_PIN, LOW);
        }
    }
    ```

    出力は次のようになります。

    ```
    PS4コントローラーを待っています
    ```

    `delay()`がないので、PSボタンを押して繋がるとすぐにLEDが点きます。

## 脳内実行してみよう

上の演習の答えのプログラムから、`loop()`の`Usb.Task();`の行を消してしまいました。コントローラーのPSボタンを押すと、LEDはどうなるでしょうか。

??? success "答えを見る"

    **LEDは消えたままで、コントローラーも繋がりません。**

    | 行 | 何が起きるか |
    | --- | --- |
    | `Usb.Task();`がない | USBホストシールドからデータを受け取らない |
    | `if (PS4.connected())` | 繋がる処理が進まないので、ずっと`false` |
    | `else`の中 | ずっと`digitalWrite(STATUS_LED_PIN, LOW);` |

    コントローラーと繋がる処理も、ボタンやスティックの値を受け取る処理も、全部`Usb.Task()`の中で行われています。`Usb.Task()`は **`loop()`の最初に必ず書く1行** として覚えておきましょう。

#### 使用したコード

- [USB Host Shield Library 2.0 (GitHub)](https://github.com/felis/USB_Host_Shield_2.0)
