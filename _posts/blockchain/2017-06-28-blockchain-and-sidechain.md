---
layout: post
title: 블록체인과 사이드체인
category: BlockChain
tag: [BlockChain]
---

블록체인 또는 비트코인을 공부하다보면 컬러드 코인(Coloredcoin), 메타코인(Metacoin),
멀티시그(Multi-Sig) 등의 용어들이 등장합니다. 그러면서 사이드체인(Sidechain)이라는
용어도 등장하게 될텐데, 사이드체인이란 무엇을 말하는 것인지 포스팅해보도록 하겠습니다.

![image -fullwidth](/assets/2017-06-28-blockchain-and-sidechain/01.png)

<br>

# 사이드체인이란
사이드체인이란 서로 다른 블록체인들 위에 존재하는 자산들(Assets, 쉽게 말해서 코인들)을
쉽게 거래할 수 있도록 하는 기술입니다. 예를 들면 비트코인의 자산과 이더리움의 자산을
서로 거래할 수 있다는 말입니다. 현재 수많은 블록체인 기반의 암호화폐들이 등장하고 있는데,
상당수의 화폐들은 사이드체인 기반으로 구현되어 있습니다. 왜 사이드체인을 이용할까요?

쉽게 생각해보면 비트코인이나 이더리움이나 각각의 화폐들은 장단점이 있고, 누군가
자기 입맛에 맞도록 수정하는게 어렵습니다. 그렇다고 새로운 화폐를 만들려고 하니,
아무래도 제일 문제가 되는 건 해당 화폐의 블록체인을 돌리고 있는 노드수입니다.
새로운 화폐이다보니 노드 수가 얼마 안되고 이는 ['51퍼센트 공격'](/blockchain/2017/06/26/how-to-hacking-blockchain/)이나 다양한 공격에 쉽게
노출이 되는 문제가 발생하게 됩니다. 그래서 신규 화폐들은 사이드체인을 이용해서
기존에 많이 퍼져있는 비트코인이나 이더리움의 리소스를 활용하고 그 위에 자신의 입맛에
맞는 화폐를 구축하는 방법을 많이 사용합니다.

<br>

# 비트코인의 단점들

가장 대표적인 블록체인기반 가상 화폐는 비트코인입니다. 가장 먼저 나와서 많은 점유율을
갖고 있지만, 그만큼 가장 오래된 기술로 구현되어 있습니다. 대표적인 비트코인의 단점들은
다음과 같습니다.

<br>

## 성능

비트코인은 느립니다. 마이닝 속도가 약 10분 정도라서 느린 것도 있지만,
블럭 사이즈 한계(현재 1MB로 맞춰져 있습니다.)도 있고 다양한 문제점들이 있어서
속도 문제가 많이 발생합니다. 심한 경우 송금을 했을 때 몇 주씩 걸리는 경우도 있을 정도입니다.

## 비트코인만 거래 가능

당연하겠지만 비트코인 블록체인 상에서는 비트코인 거래만 가능합니다.
각 업체들의 입맛에 맞는 다른 코인들의 거래는 불가능합니다.
예를 들어 익명성이 보장된 코인을 만들거나 하는 것도 불가능합니다.


## 다양한 서비스로의 확장 불가능(스마트 컨트랙트 미지원)

비트코인은 화폐 거래에만 목적으로 만들어졌기 때문에, 블록체인 기반의 다른 서비스로의
확장은 많이 어렵습니다. 블록체인 기반의 대표적인 서비스인
'스마트 컨트랙트(Smart Contract)'나 탈중앙화 어플리케이션(DApp, Decentralized Application)은
비트코인 위에서는 거의 불가능합니다.

<br>

# 사이드체인의 원리

그러면 과연 어떤 방식으로 다른 블록체인에서 비트코인을 거래할 수 있다는 것일까요?

담보물을 맡기고 돈을 빌리는 것을 생각하면 됩니다. 비트코인 소유자가 다른 블록체인 위에서
거래를 하고자 할 때, 기존 비트코인을 '동결(Freeze)'시킵니다. 그리고 이 비트코인에 해당하는
'대체물(Counterpart)'을 만들어 교환하게 됩니다. 이렇게 만들어진 대체물은 사이드체인 위에서
얼마든지 입맛에 맞게 사용되어질 수 있고, 나중에 비트코인으로 다시 교환해갈 수 있습니다.

<br>

# 사이드체인의 시사점

사이드체인을 이용하여 서로 다른 블록체인간 거래를 할 수 있다는 점은 시사점이 큽니다.
다양한 장단점이 있는 수많은 블록체인들이 서로 긴밀하게 거래가 되고, 한 군데서 관리까지
가능한 형태가 될 수 있습니다.

비트코인이 갖고 있는 약점인 속도나 확장성, 익명성(장점이자 약점인) 등은 사이드체인을 통해
또 다른 블록체인 위에서 극복이 가능합니다.

또한, 새로 만들어지는 블록체인은 사이드체인을 이용해서 비트코인의 해시파워 기반의
보안성이나 무결성을 획득할 수 있습니다.
