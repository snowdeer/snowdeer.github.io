---
layout: post
title: 블록체인 소개 - (5) 대표적인 블록체인 기술(비트코인, 이더리움, 하이퍼레저)간 비교
category: BlockChain
tag: [BlockChain]
---
# 비트코인, 이더리움, 하이퍼레저 비교

퍼블릭 블록체인과 프라이빗 블록체인의 설명에 앞서 먼저 퍼블릭 블록체인(Public Blockchain)의 대표적인 기술인 비트코인(Bitcoin Core)과 이더리움(Ethereum), 그리고 프라이빗 블록체인(Private Blockchain)의 대표적인 기술인 하이퍼레저(Hyperledger)의 특징을 간단히 살펴보도록 하겠습니다. 

항목 | 비트코인 | 이더리움 | 하이퍼레저
---|---|---|---
분류 | 퍼블릭 블록체인 | 퍼블릭 블록체인 | 프라이빗 블록체인
노드로써 참가 자격 | 누구나 참여 가능 | 누구나 참여 가능 | 멤버십 서비스를 통해 허가된 노드만 참여 가능. PKI 기반 증명서 발행
합의 알고리즘 | PoW | PoW (향후 PoS 도입 예정) | PBFT
결제 완료성 | 없음 | 없음 | 있음
성능 | 약 10분마다 블록 생성 | 약 12초마다 블록 생성 | 갱신시 합의를 확정하기 때문에 우수한 성능 보장
트랜잭션 은닉화 | 트랜잭션 정보는 공개 | 트랜잭션 정보는 공개 | 트랜잭션 정보의 공개/암호화를 선택 가능
스마트 컨트랙트 | 거의 없다시피 함. 제한적인 용도로 사용 가능 | 이더리움 버추얼 머신(EVM, Ethereum Virtual Machine)에서 동작하는 스마트 컨트랙트 구현 가능. Solidity 언어로 개발 | 체인 코드(Chaincode)를 통해 스마트 컨트랙트 구현 가능. Go 및 Java로 개발
최소 구성 대수 | 1대부터 가능. 장애 복구를 위해 최소 2대 필요 | 1대 부터 가능. 장애 복구를 위해 최소 2대 필요 | 장애 복구를 위해 최소 4대 필요

위에서 '결제 완료성(Settlement Finality)'은 결제가 완료될 때까지의 불확실성을 의미합니다. 비트코인이나 이더리움의 경우 합의 알고리즘을 PoW(Proof of Work) 방식을 이용하는데, PoW 방식은 체인이 분기되었을 때 가장 긴 체인을 올바른 체인으로 선택합니다. 체인 분기가 아주 잦은 건 아니지만 종종 발생하는 편이기 때문에 비트코인의 경우 거래가 확정되더라도 6블럭 정도를 추가로 기다리지 않으면 확실한 결과를 얻을 수가 없습니다.