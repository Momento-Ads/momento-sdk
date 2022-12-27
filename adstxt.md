# ads.txt, app-ads.txt 란?

ads.txt, app-ads.txt는 Momento 등 웹, 앱에 대하여 승인된 판매자를 통해서만 광고 지면의 트래픽이 판매될 수 있도록 하는 IAB Tech Lab의 솔루션입니다.

ads.txt, app-ads.txt 파일을 활용하므로써 매체에 광고를 게시할 수 있는 광고주를 더 효과적으로 관리할 수 있으며, 가짜 인벤토리가 광고주에게 판매되는 것을 방지할 수 있습니다.

따라서 ads.txt 및 app-ads.txt는 광고주가 가짜 인벤토리를 식별할 수 있게 도와주어 가짜 인벤토리에 광고비가 지출되지 않도록 해 주므로 광고주의 수익이 늘어날 수 있습니다.

## 적용 방법

1. 웹의 경우 ads.txt, 앱의 경우 app-ads.txt 라고 된 텍스트 파일을 생성합니다.

2. 파일에 Momento에서 제공한 행을 붙여넣고 저장합니다.

- 웹

  A. 웹의 경우 생성한 ads.txt파일을 사이트의 루트 디렉토리에 업로드합니다. 즉, 최상위 도메인에 이어 나오는 디렉토리 또는 폴더에 업로드 해주셔야 크롤러가 정상적으로 인식합니다. (example.com/ads.txt).

  B. 파일이 올바르게 게시되었는지 알고 싶다면 https://example.com/ads.txt 에 접속했을 때 텍스트 파일 안의 내용이 정확하게 보이면 올바르게 게시된 상태입니다.

- 앱

  A. 앱의 경우 구글 플레이 또는 애플 앱 스토어에 등록된 개발자 웹사이트의 루트를 크롤링합니다. 개발자 웹사이트를 https://example.com 으로 등록하셨다면, 크롤러는 https://example.com/app-ads.txt 를 크롤링합니다.

  B. 따라서 개발자 웹사이트 루트 디렉토리에 app-ads.txt를 게시해 주시면 됩니다.

  C. 개발자 웹사이트/app-ads.txt 에 접속했을 때 텍스트 파일 안의 내용이 정확하게 보이면 올바르게 게시된 상태입니다.

## 주의사항
ads.txt, app.ads.txt 내부의 Line을 정리하실 경우, 비슷해 보이더라도 절대로 내용을 바꾸지 말고, Momento에서 제공한 그대로 적용해주세요.


## ads.txt 및 app-ads.txt 예시
google.com, pub-0000000000000000, RESELLER, f08c47fec0942fa0<br></br>google.com, pub-0000000000000010, RESELLER, f08c47fec0942fa1<br></br>google.com, pub-0000000000000020, RESELLER, f08c47fec0942fa2
