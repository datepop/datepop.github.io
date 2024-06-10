---
layout: post
title: "데이트팝 서버를 이관해보자"
author: Dragontiger
categories: [GCP, AWS, Python 3.11, DjangoNinja]
image: assets/images/aws.png
featured: false
---

## 데이트팝 서버 이관기 (GCP to AWS Migration)

안녕하세요. 데이트팝의 개발팀장, 김용범입니다🙇
무려 몇달전부터 데이트팝 서버는 클라우드 이전 프로젝트에 돌입하고 있었는데요. 기존에 GCP(Google Cloud Platform)을 메인으로 사용하고 있던 서버를 AWS(Amazon Web Service)로 옮기는 과정을 겪고 있습니다. 데이트팝은 이 글을 통해 백엔드 개발자분들이 현업에서 경험해볼 수 있는 서버 이관 과정을 여러분들에게 공유해보고자 합니다.

## 왜 데이트팝 서버 이관 작업을 시작하게 되었나요?🧐

초창기 데이트팝 서버는 GCP(Google Cloud Platform)으로 시작되었고, 메인 서버로써 GAE(Google App Engine)을 사용하고 있었습니다. 가장 큰 장점은 yaml 파일 배포 한 번으로 인프라를 직접 관리할 필요 없이 트래픽이 많은 웹 애플리케이션을 구축할 수 있다는 것이었습니다. "직접 관리를 안한다"라는 것은 결국 인프라 플랫폼에서 "모든 관리를 해준다"는 것이고, 이런 유연한 관리는 많은 서버 비용과도 직결되는 부분입니다.😇 데이트팝에서는 이런 서버 비용을 절감해보고자! 서버를 AWS(Amazon Web Service)로 옮기기로 결정했고, 현재 수개월간 이관하는 과정을 겪고 있습니다. 물론 비용절감뿐 아니라, AWS가 현업에서도 수요가 큰만큼 좋은 백엔드 개발자분을 모실 수 있는 가능성도 높이고자 하는 바램도 있습니다.

## 무엇을 진행하고 있나요?

데이트팝 서버는 적지 않은 GCP 서비스를 사용하고 있습니다. 메인 백엔드 애플리케이션인 GAE 외에도 내장 서비스인 Memcache(데이터 캐싱), CronJob(주기적인 데이터 처리), TaskQueue(비동기 큐)를 사용하고 있고, 그외로는 데이터 저장을 위한 CloudSQL, Cloud Storage, DataStore(NDB), BigQuery 등이 있습니다. 서비스마다 호환이 되거나 이관이 가능한 서비스를 찾아야 했고, 각 서비스에 매칭되는 AWS 서비스를 탐색해야하는 과정을 겪었습니다.
- GAE -> ECS
- Memcache -> 제거
- CronJob -> EventBridge + Lambda
- Taskqueue -> SQS + Lambda
- CloudSQL -> Aurora DB
- Cloud Storage -> S3
- DataStore -> DynamoDB
- BigQuery -> RedShift

위 매칭한 서비스들을 토대로 AWS로 서버 이관을 진행한 결과(TO-BE)를 그려보았을 때는 아래와 같습니다.
![Untitled](/assets/images/posts/server-migration/AWS_TO_BE.png)

추가적으로 인프라 외에도 애플리케이션 코드 이관하면서 Python 3.11 버전도 업데이트하고, Django에서 DjangoNinja로 Framework 이전 또한 진행 중입니다.🤫

## 어떻게 진행하고 있나요?
각 서비스를 AWS로 옮기면서 어떤 작업을 하고, 어떻게 진행하고 있는지 나열해보면 다음과 같습니다.

- Python 버전업을 통해 출력, 인코딩, 형 변환, 예외처리, Type Hint 적용 등의 문법적인 오류 정리
- Memcache 사용을 제거하고 데이터 조회 API를 순수 Django ORM 으로 구현
- 향후 MSA 전환이 최종 목표이고, DjangoRestFramework 의존도를 줄이기 위한 DjangoNinja 1차 탈바꿈
- CronJob처럼 주기적인 알림을 트리거로 일어나야 하는 작업들 모두 EventBrige 알림 + Lambda 조합으로 전환
- TaskQueue처럼 서버 내부에서 Background로 돌아가야할 작업들 모두 SQS + Lambda 조합으로 전환
- 애플리케이션 서버 코드 이전 후 특정 엔드포인트 그룹별로 트래픽 이전 테스트
- CloudStorage와 같은 정적 데이터 저장소는 모두 S3로 이관
- 실시간 서비스 저장소들은 유저 사용성에 큰 영향을 끼칠 수 있으므로 최종 서버 새벽 작업으로 진행

## 어떤 시행착오를 겪고 계신가요?
초창기 애플리케이션 서버로 동작해오면서 쌓여 있던 레거시 코드 개선과 서비스 운영에서 GAE 의존도가 높아 성능을 대체할 수 있는 방법을 지속적으로 탐색하고 있습니다.

1. Memcache를 제거하면서 생길 수 있는 속도 이슈
- 캐싱에 의존된 API 코드를 제거하고, Django ORM 쿼리 최적화 및 속도 개선를 진행하고 있습니다.
- DB I/O 통신이 아닌 데이터 취합 과정에서 asyncio를 적용하여 비동기로 속도 개선도 시도하는 중입니다.
2. GAE TaskQueue를 대체하기 위한 Background worker 서버
- GAE 서비스로 내장되어 있는 TaskQueue는 비동기 큐를 통해 엔드포인트를 전달하여 배포한 특정 서버를 호출하여 Background 작업을 수행합니다.
- 이 기능을 SQS 비동기 큐와 Lambda 호출 조합으로 대체해보고 있습니다.
- 하지만, SQS + Lambda 조합에서도 HTTP 통신의 서버 호출을 배제할 수는 없기 때문에 더 나아가 producer/consumer 메시지 비동기 방식의 Celery 도입도 시도하는 중입니다.
3. 서버 비용을 절감하기 위한 인프라 자원 제한
- GAE를 사용하면서 매우 유연한 스케일링 설정으로 운영하다보니 서버 비용에 누수가 생겨났습니다.
- AWS ECS로 이관하게 되면서 현재 데이트팝에 적절한 인프라 자원량을 측정하여 제한된 자원으로 운영해보고 있습니다.

## 이관을 진행하며...
레거시 개선은 많은 개발자 분들이 꺼려하는 작업이지만, 결국 누군가는 해야할 하나의 과제입니다. 서버 이관 과정을 겪어보며 회사의 많은 비즈니스 로직을 이해할 수 있었고, 시스템이 돌아가는 전체적인 과정을 그림으로 그릴 수 있었습니다.
백엔드 개발자로써 한 번의 도약을 또 이뤄내고자 하는 마음으로 도전을 할 수 있었고, 많은 시행착오를 겪으며 조금 더 깊은 경험을 해볼 수 있는 과정이라고 생각합니다.😀
데이트팝 서버는 향후 MSA 전환을 목표로 향하고 있고, 이에 CTO님(능력자👍)과 함께 여러 설계를 고민해보며 나아가고 있습니다. 개발자는 항상 변화를 일궈내야하기 때문에 저는 MSA를 향한 도약을 또 일궈내보며 다음 글로 뵙겠습니다.🙇

![Untitled](/assets/images/posts/server-migration/bye.webp)