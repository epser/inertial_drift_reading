# その他メモ

* 翻訳
  * キリノ山(Mount Kirino)のみ、リバースでない順方向コースでも「キリノ山R」と呼ばれている。リソースミス
* バグ
  * ストーリーモードのタイムアタックでプラチナタイムを出しても負け扱いになる（ヴィヴ/Stage1.3）
  * フォントアトラスがそこかしこで不足していて、ダイナミックフォントかなんかにフォールバックされている

## 差し替え用のフォントアトラスをビルドする

### 互換性のあるTextMeshProを用意

Unity Hubを入れて、どうにかUnity(2019.4.40f1)をインストールする。カラの3Dプロジェクトを作成してUnityを起動。

Window → Package ManagerでTextMeshProを有効にした後にプロジェクトを閉じ、`manifest.json`を開いてTextMeshProのバージョンを手動で1.4.1に変更。

### フォントアトラス生成

再度プロジェクトを開き、アセットにNoto Sans JP-Light, Medium, BlackのTTFを追加する。

Window → TextMeshPro → Font Asset Creator からフォントアトラスの生成と保存を行う（3フォント分）。パラメータは以下。

* Sampling Point Size: Auto Sizing
* Packing Method: Fast
* Padding: 5
* Atlas Resolution: 4096x4096（これは原作と同じサイズ）
* Character Setには [https://qiita.com/kgsi/items/08a1c78b3bee71136156](https://qiita.com/kgsi/items/08a1c78b3bee71136156) を借用
* Render Mode: SDF32

### オブジェクトの配置

GameObject → 3DObject → Text - TextMeshPro から必要な数だけテキストオブジェクトを作成し、各オブジェクトフォントを生成済みのTextMeshProアセットに設定する。

### ビルドと取り出し

File → Build Setitngs で適当にビルドし、{project}\_Dataディレクトリの`sharedassets0.assets`をUABEAで開き、フォントのMonoBehavior(Dump export)とTexture2D(plugin→export)を抜き出す。

* NotoSansJP-Light SDF(MonoBehavior)
* NotoSansJP-Medium SDF(MonoBehavior)
* NotoSansJP-Black SDF(MonoBehavior)
* NotoSansJP-Light SDF Atlas(Texture2D)
* NotoSansJP-Medium SDF Atlas(Texture2D)
* NotoSansJP-Black SDF Atlas(Texture2D)

## フォントアトラスを差し替える

### 取り出し

UABEAで`sharedassets0.assets`または`resources.assets`を開き（UABEAの場合どちらにせよ後者を参照する）、下記のMonoBehavior(Dump)を抜き出す。

* NotoSansJP-Light SDF Static
* NotoSansJP-Medium SDF Static
* NotoSansJP-Black SDF Static

### マージ

同名のMonoBehaviorダンプをマージする。その際、以下の部分は新規作成したほうを優先する。

* m\_FaceInfoからm\_FaceIndexを除いた全部
* m\_FaceInfoより下の変数を全部、まるまるコピペで置き換え。一応リストにしてあるが気にせず全部上書きしてる
  * m\_GlyphTable
  * m\_AtlasTextureIndex
  * m\_UsedGlyphRects
  * m\_FreeGlyphRects
  * m\_fontInfo
  * atlas
  * m\_AtlasWidth
  * m\_AtlasHeight
  * m\_AtlasPadding
  * m\_AtlasRenderMode
  * m\_glyphInfoList
  * m\_KerningTable
  * m\_FontFeatureTable
  * fallbackFontAssets
  * m\_FallbackFontAssetTable
  * m\_CreationSettings
  * m\_FontWeightTable
  * fontWeights
  * normalStyle
  * normalSpacingOffset
  * boldStyle
  * boldSpacing
  * italicStyle
  * tabSize

### インポート

マージしたMonoBehaviorとAtlasをUABE上でインポートする。ファイル名を合わせてバッチインポートすれば2手くらいで済むはず。



