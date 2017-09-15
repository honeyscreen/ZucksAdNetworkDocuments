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
    * 광고 이미지의 비율을 유지하여 노출해야함. 
  * `width` : String
    * 광고이미지폭
  * `height` : String
    * 광고이미지높이
  * `title` : String
    * 광고제목
    * 전각 1 ~ 18 자 (영문 1 ~ 36 문자) 문자열
  * `body_text` : String
    * 광고 본문 
    * 전각 1 ~ 44 자 (영문 1 ~ 88 문자) 문자열
  * `product_name` : String
    * 서비스 상품명
    * 전각 1 ~ 18 자 (영문 1 ~ 36 문자) 문자열
  * `advertiser_name` : String
    * 광고주 이름
    * 전각 1 ~ 18 자 (영문 1 ~ 36 문자) 문자열
  * `link_button_text` : String
    * 링크 버튼 설치시 텍스트
    * 전각 0-7 문자 (0 ~ 14 문자) 문자열
  * `landing_url` : String
    * 광고 클릭시 랜딩 할 URL

#### 광고 리턴이 없는 경우

* `status` : String
  * `no_ad`
* `no_ad_url` : String
  * 광고 소진 측정 용 end point

광고가 존재하지 않는 경우에도 HTTP 상태 코드 200로 반환합니다

`no_ad_url` 값을 아래 Firing Impressions 따라 처리함으로써 "전달하는 광고가 존재하지 않았다"고 계산합니다.

### 기타

응답 내용은 캐시하지 않고 광고 표시의 타이밍에서 매번 API를 호출하여 수신 한 결과를 이용하십시오. `아라타가 별도로 캐싱 허용 받음`

캐시 된 내용으로 광고 표시를하면 제대로 광고가 표시되어 있지 않다고 판단되며, 노출, 클릭이 제대로 계산되지 않을 수 있습니다.


## Example

### Request

```
https://sh.zucks.net/opt/native/api/v2m?frameid=_xxxxxxxxxx&num=2&ida=xxxx-xxxx-xxxx-xxxx-xxxx&lat=0&ua=Mozilla%2F5.0%20%28iPhone%3B%20CPU%20iPhone%20OS%209_0%20like%20Mac%20OS%20X%29%20AppleWebKit%2F601.1.46%20%28KHTML%2C%20like%20Gecko%29%20Version%2F9.0%20Mobile%2F13A344%20Safari%2F601.1&ref=http%3A%2F%2Fexample.com&lang=ja&ip=1.66.96.0
```

### Response

#### 광고가 존재하는 경우

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

#### 광고가 존재하지 않는 경우

```json
{
    "status": "no_ad",
    "no_ad_url": "https:\u002F\u002Fk.zucks.net\u002F..."
}
```


## Rendering the Ads

`image_src` 는 png / jpg / gif (애니메이션 포함) 등의 이미지 파일의 URL입니다.이 URL에서 이미지를 가져 종횡비를 유지 한 상태로 표시합니다.
`image_src` 이미지 파일의 내용은 변경할 수 없습니다. 필요에 따라 캐시하여 이용할 수 있습니다.

`link_button_text` 링크 버튼 설치시 사용할 수있는 텍스트입니다.
광고에 따라 빈 경우가 있습니다
광고 표시시에는 광고임을 명시 스폰서 라벨 (`Sponsored``AD``광고`등)을 반드시 표시하십시오.

광고 상황에 따라 광고가 선택적으로 반환되지 않을 수 있습니다.

### Rendering Sample

1. 광고 이미지（`image_src`）
2. 제목（`title`）
3. 본문（`body_text`）
4. 상품명（`product_name`）
5. 광고주면（`advertiser_name`）
6. call to action（`link_button_text`）
7. 스폰서 라벨

![네이티브 광고 표시 샘플](images/native_ad_sample.png)


## Firing Impressions

Zucks Ad Network는 신호를 통해 노출을 계산합니다. 

각 광고를 렌더링 한 직후`imp_url`의 URL에 GET 요청을 보내 주시기 바랍니다.

* End point
  * `imp_url` 의URL
* Method
  * GET
* 브라우저 / WebView 내에서 XMLHttpRequest를 사용하여 요청을 보내는 경우
  * `withCredentials` 속성을`true`로 요청하십시오

또한 광고 안건이 존재하지 않는 경우는`no_ad_url` 비슷한 요청을 제출함으로써 "전달하는 광고 안건이 존재하지 않았다"으로 계산합니다.


## Firing Clicks

광고 탭시`landing_url` URL을 기본 브라우저로 열어 봅니다


## Check List

실 유저 오픈까지 확인해야 할 체크 항목입니다

** 광고 수익이 잡히지 않는 경우가 있으므로 반드시 확인하십시오 **

- [ ] 광고가 실제로 잘 표시되었는지
- [ ] Zucks Ad Network대시보드에서 임프레션이 잘 잡히는지
- [ ] 랜딩했을때 올바른 페이지로 랜딩 되는지 
- [ ] Zucks Ad Network대시보드에서 클릭이 잘 잡히는지


## Trouble Shooting

일반적인 문제에 대한 확인 사항입니다

### no_ad 만 반환되고, 광고 리턴이 없습니다. 

- [ ] Request Parameter의`frameid` 값이 정확합니까?
- [ ] Request Header의`User-Agent` 값 또는 Request Parameter의`ua` 값은 광고를 표시하는 단말기의 기본 브라우저에 준한 가치인가?

해당 프레임에 대한 광고 재고가없는 경우도 있습니다. 자세한 내용은 문의 바랍니다

### 노출이 대시보드에 잡히지 않습니다.

- [ ] Request Parameter의`frameid` 값이 정확합니까?
- [ ] `imp_url` 값의 URL에 요청을 발행하고 있습니까?
- [ ] 는 약간의 지연 시간이 있을 수 있습니다. 충분한 시간을 기다린 후 확인하세요.


### 클릭이 잡히지 않습니다.

- [ ] `landing_url` 값의 URL에 요청을 제대로 보내고 있나요 ?
- [ ] 그 응답이 `302 Moved Temporarily`입니까?
- [ ] 대시보드는 지연이 있을 수 있으니, 시간을두고 확인해주세요.

