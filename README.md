04/05 音声のテスト

# osモジュールをインポート - ファイル操作や環境変数へのアクセスなどOSレベルの操作が可能になります
import os

# requestsモジュールをインポート - HTTPリクエストを送信するための人気ライブラリです
import requests

# pygameモジュールをインポート - ゲーム開発用のライブラリですが、ここでは音声再生機能を利用します
# 注意: このモジュールは「pip install pygame」でインストールする必要があります
import pygame  # 音声再生用（pip install pygame が必要）

# pygameの音声ミキサーを初期化 - これによって音声を再生する機能が使えるようになります
pygame.mixer.init()

# OpenAIのAPIキーを環境変数から取得 - セキュリティのため、APIキーはコード内に直接書かずに環境変数として設定します
api_key = os.environ.get("OPENAI_API_KEY")

# HTTPリクエストのヘッダーを設定 - APIキーによる認証と、JSONデータを送信することを指定します
headers = {
    "Authorization": f"Bearer {api_key}",  # 「Bearer」の後にAPIキーを続けるのが標準的な認証形式です
    "Content-Type": "application/json",  # 送信データの形式がJSONであることを指定します
}


# テキストを音声に変換して再生する関数を定義します
# パラメータ: text(読み上げるテキスト), voice(音声の種類), output_file(保存するファイル名)
def text_to_speech(text, voice="alloy", output_file="temp_speech.mp3"):
    """テキストを音声に変換して再生する関数"""

    # APIリクエスト用のデータを辞書型で準備 - これが自動的にJSON形式に変換されます
    data = {
        "model": "tts-1",  # 使用するTTSモデル - OpenAIの標準音声モデル
        "input": text,  # 読み上げるテキスト内容
        "voice": voice,  # 使用する音声タイプ
    }

    # OpenAIのText-to-Speech APIにPOSTリクエストを送信します
    response = requests.post(
        "https://api.openai.com/v1/audio/speech",  # OpenAIの音声生成API エンドポイント
        headers=headers,  # 認証情報などのヘッダー
        json=data,  # 送信データ(自動的にJSON形式に変換される)
    )

    # APIからのレスポンスを確認 - ステータスコード200は成功を意味します
    if response.status_code == 200:
        # 受け取った音声データをファイルに書き込みます ("wb"は「バイナリ書き込みモード」の意味)
        with open(output_file, "wb") as audio_file:
            audio_file.write(
                response.content
            )  # response.contentにはバイナリデータ(音声ファイル)が含まれています

        # pygameを使って音声ファイルを読み込みます
        pygame.mixer.music.load(output_file)

        # 音声の再生を開始します
        pygame.mixer.music.play()

        # 音声の再生が終わるまで待機します - get_busy()は再生中はTrueを返します
        while pygame.mixer.music.get_busy():
            pygame.time.Clock().tick(10)  # CPUの負荷を減らすため10ミリ秒ごとに状態確認

        # 関数が正常に実行されたことを示すTrueを返します
        return True
    else:
        # エラーが発生した場合、ステータスコードとエラーメッセージを表示します
        print(f"エラー: {response.status_code}")
        print(response.text)  # APIからのエラーメッセージを表示

        # 関数が失敗したことを示すFalseを返します
        return False


# プログラムの開始メッセージを表示します
print("テキスト読み上げシステム（終了するには 'exit' と入力）")
print("利用可能な声: alloy, echo, fable, onyx, nova, shimmer")

# メインの対話ループ - ユーザーが「exit」と入力するまで繰り返します
while True:
    # ユーザーから読み上げるテキストを入力してもらいます
    text = input("\n読み上げるテキスト: ")

    # 「exit」と入力された場合はループを終了します(大文字小文字は区別しない)
    if text.lower() == "exit":
        break

    # 使用する音声タイプを入力してもらいます(何も入力されなければデフォルトの"alloy"を使用)
    voice = input("使用する声 (デフォルト: alloy): ") or "alloy"

    # 上で定義した関数を呼び出して、テキストを音声に変換し再生します
    text_to_speech(text, voice)


# 仮想環境をアクティブにするコマンド（Windowsの場合）
# .\venv\Scripts\Activate]]]]

# pip install requests
# pip install pygame
#2025/04/05_音声読み上げ


ーーーーーーーーーーーーーーーーー
# Windowsで仮想環境を作成して音声読み上げプログラムを実行する手順

PowerShellでデスクトップに仮想環境を作成し、音声読み上げプログラムを実行する方法を順を追って説明します。

## 1. 仮想環境の作成と有効化

```powershell
# 指定したディレクトリに移動
PS C:\Users\yukik\Desktop\ex\0405voice>

# 仮想環境を作成（venvという名前の仮想環境を作成）
python -m venv venv

# 仮想環境を有効化
.\venv\Scripts\Activate

# 有効化されると、プロンプトの先頭に (venv) が表示されます
(venv) PS C:\Users\yukik\Desktop\ex\0405voice>
```

## 2. 必要なライブラリのインストール

```powershell
# 必要なパッケージをインストール
(venv) PS C:\Users\yukik\Desktop\ex\0405voice> pip install requests pygame
```

## 3. OpenAI APIキーの設定

```powershell
# PowerShellで環境変数を設定
(venv) PS C:\Users\yukik\Desktop\ex\0405voice> $env:OPENAI_API_KEY = "あなたのAPIキーをここに入力"
```

## 4. Pythonプログラムの作成

テキストエディタで`voice_app.py`というファイルを作成し、先ほどのコードを貼り付けます：

```powershell
# Notepadでファイルを作成する例
(venv) PS C:\Users\yukik\Desktop\ex\0405voice> notepad voice_app.py
```

## 5. プログラムの実行

```powershell
# Pythonプログラムを実行
(venv) PS C:\Users\yukik\Desktop\ex\0405voice> python voice_app.py
```

## トラブルシューティング

### エラー: APIキーが見つからない場合

コード内で直接APIキーを設定する方法もあります（本番環境では非推奨）：

```python
# 環境変数からではなく、直接APIキーを設定
# api_key = os.environ.get("OPENAI_API_KEY")
api_key = "あなたのAPIキーをここに入れる"
```

### エラー: pygame関連のエラーが出る場合

サウンドデバイスに関するエラーが出る場合は、以下のように`pygame.mixer.init()`に引数を追加してみてください：

```python
# サンプルレートなどを明示的に指定
pygame.mixer.init(frequency=44100, size=-16, channels=2, buffer=4096)
```

### エラー: モジュールが見つからない場合

パッケージが正しくインストールされているか確認します：

```powershell
(venv) PS C:\Users\yukik\Desktop\ex\0405voice> pip list
```

## 完全な手順（コピペで実行できる形式）

```powershell
# 1. 仮想環境を作成して有効化
python -m venv venv
.\venv\Scripts\Activate

# 2. 必要なパッケージをインストール
pip install requests pygame

# 3. APIキーを設定
$env:OPENAI_API_KEY = "あなたのAPIキーをここに入力"

# 4. Pythonファイルを作成
# (voice_app.pyを手動で作成してコードを貼り付け)

# 5. プログラムを実行
python voice_app.py
```

この手順に従えば、仮想環境内でテキスト読み上げアプリケーションを実行できるはずです。何か問題があれば、具体的なエラーメッセージを教えていただければ、さらに詳しくサポートします！
ーーーーーーーーーーーーーーーーーーーーーーーーーーーーーー
# Windowsで仮想環境を作成して音声読み上げプログラムを実行する手順

PowerShellでデスクトップに仮想環境を作成し、音声読み上げプログラムを実行する方法を順を追って説明します。

## 1. 仮想環境の作成と有効化

```powershell
# 指定したディレクトリに移動
PS C:\Users\yukik\Desktop\ex\0405voice>

# 仮想環境を作成（venvという名前の仮想環境を作成）
python -m venv venv

# 仮想環境を有効化
.\venv\Scripts\Activate

# 有効化されると、プロンプトの先頭に (venv) が表示されます
(venv) PS C:\Users\yukik\Desktop\ex\0405voice>
```

## 2. 必要なライブラリのインストール

```powershell
# 必要なパッケージをインストール
(venv) PS C:\Users\yukik\Desktop\ex\0405voice> pip install requests pygame
```

## 3. OpenAI APIキーの設定

```powershell
# PowerShellで環境変数を設定
(venv) PS C:\Users\yukik\Desktop\ex\0405voice> $env:OPENAI_API_KEY = "あなたのAPIキーをここに入力"
```

## 4. Pythonプログラムの作成

テキストエディタで`voice_app.py`というファイルを作成し、先ほどのコードを貼り付けます：

```powershell
# Notepadでファイルを作成する例
(venv) PS C:\Users\yukik\Desktop\ex\0405voice> notepad voice_app.py
```

## 5. プログラムの実行

```powershell
# Pythonプログラムを実行
(venv) PS C:\Users\yukik\Desktop\ex\0405voice> python voice_app.py
```

## トラブルシューティング

### エラー: APIキーが見つからない場合

コード内で直接APIキーを設定する方法もあります（本番環境では非推奨）：

```python
# 環境変数からではなく、直接APIキーを設定
# api_key = os.environ.get("OPENAI_API_KEY")
api_key = "あなたのAPIキーをここに入れる"
```

### エラー: pygame関連のエラーが出る場合

サウンドデバイスに関するエラーが出る場合は、以下のように`pygame.mixer.init()`に引数を追加してみてください：

```python
# サンプルレートなどを明示的に指定
pygame.mixer.init(frequency=44100, size=-16, channels=2, buffer=4096)
```

### エラー: モジュールが見つからない場合

パッケージが正しくインストールされているか確認します：

```powershell
(venv) PS C:\Users\yukik\Desktop\ex\0405voice> pip list
```

## 完全な手順（コピペで実行できる形式）

```powershell
# 1. 仮想環境を作成して有効化
python -m venv venv
.\venv\Scripts\Activate

# 2. 必要なパッケージをインストール
pip install requests pygame

# 3. APIキーを設定
$env:OPENAI_API_KEY = "あなたのAPIキーをここに入力"

# 4. Pythonファイルを作成
# (voice_app.pyを手動で作成してコードを貼り付け)

# 5. プログラムを実行
python voice_app.py
```

この手順に従えば、仮想環境内でテキスト読み上げアプリケーションを実行できるはずです。何か問題があれば、具体的なエラーメッセージを教えていただければ、さらに詳しくサポートします！
