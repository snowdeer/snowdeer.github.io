---
layout: post
title: 블록체인 소개 - (4) 스마트 컨트랙트(Smart Contract)
category: BlockChain
tag: [BlockChain]
---
# 스마트 컨트랙트

블록체인을 4차 산업 혁명의 대표 기술이라고 말하는 이유에는 블록체인의 핵심 기능인 '스마트 컨트랙트(Smart Contract)' 때문일 것입니다. '똑똑한 계약'이라고도 번역하는 스마트 컨트랙트가 과연 어떤 기술인지 살짝만 포스팅해보도록 하겠습니다.

<br>

# 스마트 컨트랙트의 개념

우리 주위에서 흔히 볼 수 있는 부동산 계약을 생각해봅시다. 

부동산 계약은 보통 집을 파는 매도자, 집을 구매하는 매수자, 그리고 중간에서 계약을 성사시켜주고 관리해주는 중개자로 구성되어 있습니다. 정해진 날짜에 계약을 하고 계약금을 걸고, 정해진 날짜에 중도금, 잔금 등을 치르면서 집 문서를 거래합니다. 스마트 컨트랙트도 비슷합니다. 부동산 거래에서는 보통 부동산 중개업자를 포함해서 계약을 진행하지만, 스마트 컨트랙트는 중개인 없이 자동으로 거래를 성사시켜줍니다. 

즉, 중개자없이 계약 당사자들끼리 자동으로 거래를 할 수 있게 해주는 것이 스마트 컨트랙트입니다.

사실 별 것이 없습니다. 스마트 컨트랙트 개념은 오래된 용어입니다. 1996년 Nick Szabo에 의해 제안된 용어이고, 지금도 이미 PC나 인터넷을 통해 제공되고 있는 서비스입니다.

<br>

# 스마트 컨트랙트 예시

![Image](/assets/blockchain/015.webp)

위 이미지는 (블록체인과 무관한) 아주 기본적인 스마트 컨트랙트 예시입니다. 계약서 내용을 보면 계약 내용, 당사자들 정보, 금액, 보증금 등이 있습니다. 보증금은 부동산 거래에서 계약금과 같은 역할을 하는 금액입니다. 계약 미체결시 보증금을 못 돌려받도록 해서 계약의 강제성을 갖도록 하고 있습니다.

<br>

# 스마트 컨트랙트의 장점

스마트 컨트랙트의 가장 큰 장점은 중개인이 없다는 점입니다. 중개인이 없다보니 비용 절감 효과가 아주 큽니다.

* 빠르고 Realtime에 가깝다.
* 정확도가 높다. (사람에 의해 실수하는 부분이 없다.)
* 중개자의 수를 없애거나 줄일 수 있다.
* 비용이 절감된다.

<br>

# 스마트 컨트랙트 활용 사례

스마트 컨트랙트는 블록체인이 나타나기 전부터 다양한 형태로 적용되어 왔습니다. 예를 들어 불법 복제를 막는 DRM은 스마트 컨트랙트의 초기 컨셉이라고 볼 수 있습니다. 불법 복제를 막음으로써 계약 위반을 강제적으로 방지하는 개념입니다.

옥션이나 지마켓 등 온라인 쇼핑몰에서 상품을 구매하는 것도 스마트 컨트랙트의 일종입니다. 각 마켓 서비스에서는 소비자가 금액을 지불하면 상품을 보내주거나 상품 구매 내역을 증명해줍니다.

신용 카드로 자동차를 구매했다가 구매자가 할부금을 갚지 못할 경우 자동으로 자동차 문이 안 열리게 된다거나, 할부금을 갚지 못한 전자 제품이 망가지도록 Kill Switch를 적용한 것도 스마트 컨트랙트입니다.

<br>

# 블록체인과 스마트 컨트랙트

블록체인에서의 스마트 컨트랙트도 위 스마트 컨트랙트와 크게 다르지 않습니다. 다만 기존에는 중앙 서버에서 계약을 관리하고 증명해야 했다면, 블록체인에서는 분산 장부를 통해 네트워크에 참여한 모든 사람들이 계약을 증명한다는 점만 다릅니다.

현실 세계의 계약이든 스마트 컨트랙트든 모두 '조건'과 '실행'으로 이루어져 있습니다. 예를 들어 부동산 거래나 물건 거래는 '돈을 입금하면'이라는 조건과 조건이 만족되었을 때, '집 문서를 전달한다'라는 실행 부분으로 구성되어 있습니다.

블록체인에서도 마찬가지입니다. 어떤 조건이 만족지면 무엇인가를 실행하도록 블록체인내에 코드로 기록할 수 있습니다. 스크립트로 작성되어질 수 있으며, 블록체인에 기록되어 네트워크의 모든 노드들이 해당 계약을 확인하고 증명합니다. 계약의 강제성을 위해 두 계약자 모두에게 일정 금액의 보증금을 입력하기도 합니다. 게약 조건이 만족되면 블록체인내에 스크립트로 작성한 실행 내용이 자동으로 이행되어집니다.

비트코인같은 경우는 `Contract Code`라고 부르는 아주 작은 스크립트 정도를 작성가능하며(보통은 비트코인은 스마트 컨트랙트 기능이 없다라고도 많이 표현합니다.), 이더리움 같은 경우는 스마트 컨트랙트를 위해 `Solidity`라는 스크립트 언어를 제공하고 있습니다. 프로그래밍 언어이다보니 코딩하듯이 스마트 컨트랙트를 작성할 수 있습니다. 프라이빗 블록체인들의 경우는 대부분 스마트 컨트랙트 기능을 제공하고 있습니다. IBM 주도하의 하이퍼레저(Hyperledger)같은 경우는 `Chaincode`라는 이름으로 스마트 컨트랙트를 제공하고 있습니다.

스마트 컨트랙트를 이용하면 다음과 같은 일들을 자동으로 할 수 있습니다.

* 정해진 금액을 입금하면, 영화 1시간 스트리밍 이용권을 제공
* 전자 장비를 보증금을 맡기면서 빌리고 사용한 시간만큼만 제하고 다시 돌려받는 서비스

프로그래밍 언어처럼 표현되다보니 조건에 돈이나 금액이 들어가지 않더라도 다음과 같은 예시도 가능합니다.

* 내일 날씨가 맑으면, 아침 6시에 깨워저

물론, 위 조건은 블록체인의 스마트 컨트랙트가 해결하지 못하는 나쁜 예제(Bad case)이긴 하지만 그만큼 다양하게 계약 코드를 작성할 수 있다는 의미입니다.

<br>

# 스마트 컨트랙트가 만능은 아님

사실 이 부분은 이번 포스팅에서 적을 내용은 아니지만 위에서 이상한 예제를 하나 들었기에 살짝 언급을 해봅니다.

'내일 날씨가 맑으면, 아침 6시에 깨워줘'와 같은 계약은 '내일 날씨가 맑으면'이라는 조건이 너무 모호합니다. 블록체인은 합의 기반이다보니 모든 노드들의 날씨 정보가 같지 않으면 합의가 될 수가 없습니다. 물론, '내일 기상청에서 발표하는 서울 강남 지역의 일기 예보가 맑음이라고 뜨면'과 같이 구체적이고 객관적인 조건으로 이를 해결할 수는 있겠지만, 또다른 조건이 누락될 수도 있고 항상 모든 조건을 커버할 수는 없기 때문에 스마트 컨트랙트를 무조건 적용하기는 어렵습니다.

또한, '내가 농협 계좌로 100만원을 입금하면 비트코인으로 1BTC를 나에게 전송해줘'와 같은 계약문도 블록체인만으로 해결하기는 쉽지 않은 예제입니다. 농협 계좌로 100만원을 입금하는 일은 블록체인 외부의 작업이며, 송금의 결과도 블록체인 외부에서만 확인가능합니다. 즉 블록체인 내의 노드들이 해당 조건을 확인할 수도 없으며, 설사 농협에서 해당 기록을 조회할 수 있는 API를 제공한다고 하더라도 모든 노드에서 그 결과 확인을 위해 API 호출을 할 수도 없는 일입니다. 