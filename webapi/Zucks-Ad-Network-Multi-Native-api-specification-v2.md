# Zucks Ad Network Multi Native Ad API v2.0 Specification

여러개의 광고를 리턴해주는 광고 API 스펙은 다음을 참조해주세요.  - 이하 `Sophia` 번역 > 이상한 부분은 꼭 재확인 요청 주세요 


## Request

* End point
  * `https://sh.zucks.net/opt/native/api/v2m`
* Method
  * GET

### Request Headers

* `User-Agent` : Required.
   * 광고를 표시하는 단말기의 기본 브라우저와 동등한 ua 를  Header  ua 파라미터를 통해 꼭 보내주세요
   * 뒷 부분에 추가적인 정보가 들어가는 경우에도 형식에 문제가 없습니다. 
   * 파라미터에 ua 를 전송하는 경우, 파라미터로 보낸 ua 가 헤더에 우선하여 처리됩니다. 
  
  
* `Referer` : Optional.
  * Parameter ref가 설정되어있는 경우, Parameter 측의 설정이 우선됩니다
* `Accept-Language` : Optional.
  * Parameter lang가 설정되어있는 경우, Parameter 측의 설정이 우선됩니다
* `Origin` : Optional.
  * withCredentials 속성을 true로 요청하는 경우 설정하십시오
* `Cookie` : Optional.

### Request Parameters

* `frameid` : Required.
  * 광고 프레임마다 발행되는 Frame ID  ```HoneyScreen_Female, Male 각각 발급된  frame ID 는 트렐로 카드에 업로드 되어있습니다.``` 
  * (Zucks Ad Network 관리 화면의 미디어 / 광고 공사 관리 페이지에서 확인하시기 바랍니다)
* `num` : Required.
  * 요청할 광고의 갯수입니다. 1-5사이 숫자를 입력  ```저희는 기존에 진행하던 대로 총 3개의 라인아이템을 만들었으면 합니다 ```
  * 요청한 수 만큼의 광고가 없을 경우, 존재하는 광고 수 만큼을 반환합니다. 
* `ida` : Optional.
  * IDFA(iOS) or Advertising ID(Android)
  * Parameter ida를 보낼 경우 반드시 아래의 Parameter lat를 동시에 보내야합니다
  * Parameter ida가 지정되어 있고, Parameter lat가없는 경우 반드시 HTTP Status 406 Not Acceptable를 반환하고 광고는 전달되지 않습니다
* `lat` : Optional.
  * '광고 추적 제한'이 무효 인 경우 : 0
  * '광고 추적 제한'을 사용하는 경우 : 1
* `ip` : Optional.
  * Source IP address
  * API에 대한 요청을 서버에서 실행하는 경우 광고를 표시하는 단말의 IP 주소를 필수로 설정하십시오
* `ua` : Optional.
  * Header와 다른 User-Agent를 사용하는 경우에 설정하십시오
* `ref` : Optional.
  * Header와 다른 Referer를 이용하는 경우 설정하십시오
* `lang` : Optional.
  * Header와 다른 Accept-Language를 사용하는 경우에 설정하십시오

### 기타 확인 사항

* 브라우저 / WebView 내에서 XMLHttpRequest를 사용하여 요청을 보내는 경우
  * `withCredentials` 속성을 `true` 로 요청하십시오
  * `Request Headers`의 `Origin`을 설정하십시오


## Response

JSON 문자열을 반환합니다. 문자 코드는 UTF-8입니다.

### Response Body

#### 리턴된 광고가 존재하는 경우

* `status` : String
  * `ok`
* `ads` : Array
  * `type` : String
    * `native`
  * `imp_url` : String
    * 임프레션 트래킹 URL
  * `image_src` : String
    * 광고 이미지 URL 
    * 광고 이미지의 비율을 유지하여 노출해야하며, 좌우 비율을 바꿔서는 안됩니다. 
  * `width` : String
    * 광고이미지폭
  * `height` : String
    * 광고이미지높이
  * `title` : String
    * 광고제목
    * 全角1～18文字（半角1～36文字）の文字列
  * `body_text` : String
    * 広告の本文
    * 全角1～44文字（半角1～88文字）の文字列
  * `product_name` : String
    * サービス・商品名
    * 全角1～18文字（半角1～36文字）の文字列
  * `advertiser_name` : String
    * 広告主名
    * 全角1～18文字（半角1～36文字）の文字列
  * `link_button_text` : String
    * リンクボタン設置時のテキスト
    * 全角0～7文字（半角0～14文字）の文字列
  * `landing_url` : String
    * 広告タップ時の遷移先URL

#### 広告案件が存在しない場合

* `status` : String
  * `no_ad`
* `no_ad_url` : String
  * 広告在庫切れ計測用エンドポイント

広告案件が存在しない場合でも、HTTPステータスコードは200で返却します。

`no_ad_url` 値を後述 Firing Impressions に従って処理することで、「配信する広告案件が存在しなかった」数としてカウントします。

### その他

レスポンス内容はキャッシュせずに、広告表示のタイミングで毎回APIを叩いて習得した結果を利用してください。

キャッシュした内容で広告表示を行うと正しく広告が表示されていないと判断され、インプレッション、クリックが正しくカウントされない場合があります。


## Example

### Request

```
https://sh.zucks.net/opt/native/api/v2m?frameid=_xxxxxxxxxx&num=2&ida=xxxx-xxxx-xxxx-xxxx-xxxx&lat=0&ua=Mozilla%2F5.0%20%28iPhone%3B%20CPU%20iPhone%20OS%209_0%20like%20Mac%20OS%20X%29%20AppleWebKit%2F601.1.46%20%28KHTML%2C%20like%20Gecko%29%20Version%2F9.0%20Mobile%2F13A344%20Safari%2F601.1&ref=http%3A%2F%2Fexample.com&lang=ja&ip=1.66.96.0
```

### Response

#### 広告案件が存在する場合

```json
{
    "status": "ok",
    "ads": [
        {
            "type": "native",
            "imp_url": "https:\u002F\u002Fk.zucks.net\u002F...",
            "image_src": "https:\u002F\u002Fstatic.zucks.net.zimg.jp\u002Fimage\u002F...",
            "width": "114",
            "height": "114",
            "title": "【広告タイトル1】",
            "body_text": "【広告の本文1】",
            "product_name": "【サービス・商品名1】",
            "advertiser_name": "【広告主名1】",
            "link_button_text": "【リンクボタン設置時のテキスト1】",
            "landing_url": "https:\u002F\u002Fk.zucks.net\u002F..."
        },
        {
            "type": "native",
            "imp_url": "https:\u002F\u002Fk.zucks.net\u002F...",
            "image_src": "https:\u002F\u002Fstatic.zucks.net.zimg.jp\u002Fimage\u002F...",
            "width": "114",
            "height": "114",
            "title": "【広告タイトル2】",
            "body_text": "【広告の本文2】",
            "product_name": "【サービス・商品名2】",
            "advertiser_name": "【広告主名2】",
            "link_button_text": "【リンクボタン設置時のテキスト2】",
            "landing_url": "https:\u002F\u002Fk.zucks.net\u002F..."
        }
    ]
}
```

#### 広告案件が存在しない場合

```json
{
    "status": "no_ad",
    "no_ad_url": "https:\u002F\u002Fk.zucks.net\u002F..."
}
```


## Rendering the Ads

`image_src` は、png/jpg/gif(アニメーション含む)などの画像ファイルを示すURLです。  
このURLから画像を取得し、縦横比を保った状態で表示してください。  
`image_src` の画像ファイルの内容は不変です。必要に応じてキャッシュして利用することができます。

`link_button_text` はリンクボタン設置時に利用することができるテキストです。  
広告によっては空文字の場合があります。

広告表示時には広告であることを明記するスポンサーラベル（`Sponsored`、`AD`、`広告`など）を必ず表示してください。

広告在庫状況によって、要求数分の広告が返却されない場合があります。

### Rendering Sample

1. 広告画像（`image_src`）
2. タイトル（`title`）
3. 広告の本文（`body_text`）
4. サービス・商品名（`product_name`）
5. 広告主名（`advertiser_name`）
6. リンクボタン設置時のテキスト（`link_button_text`）
7. スポンサーラベル

![ネイティブ広告表示例](images/native_ad_sample.png)


## Firing Impressions

Zucks Ad Networkでは、ビーコン送信によりインプレッションをカウントしています。

各広告をレンダリングした直後、 `imp_url` のURLにGETリクエストを送信してください。

* End point
  * `imp_url` のURL
* Method
  * GET
* ブラウザ/WebView内からの XMLHttpRequest を使ってリクエストを送る場合
  * `withCredentials` 属性を `true` にてリクエストしてください

また、広告案件が存在しない場合は `no_ad_url` に同様のリクエストを送信することで、「配信する広告案件が存在しなかった」数としてカウントします。


## Firing Clicks

広告タップ時、`landing_url` のURLをデフォルトブラウザで開いてください


## Check List

ご利用開始までに確認すべきチェック項目です

**広告収益の算出ができない場合がありますので、必ず確認してください**

- [ ] ネイティブ広告が表示された
- [ ] Zucks Ad Network管理画面で、インプレッション数が増えた
- [ ] クリック後、デフォルトブラウザでランディングページが表示された
- [ ] Zucks Ad Network管理画面で、クリック数が増えた


## Trouble Shooting

よくあるトラブルに対する確認項目です

### no_ad しか返却されません

- [ ] Request Parameterの `frameid` 値は正しいですか？
- [ ] Request Headerの `User-Agent` 値またはRequest Parameterの `ua` 値は、広告を表示する端末のデフォルトブラウザに準じた値ですか？

該当枠に対する広告在庫がない場合もございます。詳しくはお問い合わせください。

### インプレッションがカウントされません

- [ ] Request Parameterの `frameid` 値は正しいですか？
- [ ] `imp_url` 値のURLにリクエストを発行していますか？
- [ ] 管理画面上のレポーティングには多少のタイムラグがあります。しばらく時間をおいて確認してみてください。

### クリックがカウントされません

- [ ] `landing_url` 値のURLにリクエストを発行していますか？
- [ ] そのレスポンスは `302 Moved Temporarily` ですか？
- [ ] 管理画面上のレポーティングには多少のタイムラグがあります。しばらく時間をおいて確認してみてください。

