---
title: Git flow
parent: Git
nav_order: 1
layout: default
---

<p>
<img alt="Static Badge" src="https://img.shields.io/badge/git-%23F05032?logo=git&logoColor=%23fff">
</p>

<h1 style="color:#0c0c0c;font-weight:500;">Git flow</h1>

팀내 협업 + 실제로 운영중인 서비스 + 서비스 버전 관리 할때 Git 전략    
Git flow 전략은 큰 하나의 가이드인것 같고 사용된 곳에서 커스텀해서 잘 사용하는게 중요하다.   

큰 틀이라고 하면 `main` `stg` `dev` `hotfix` `feat` 브랜치들의 역할과 목적 정도?

현재 진행중인 프로젝트는 초기에 `main` `dev` 브랜치만 사용해서 따로 전략이 없었고,   
서비스를 실제 런칭하고 여러 팀원들이 생기면서 나름 버전관리와 운영관리를 위한 Git flow 전략을 수립해서 사용중이다.

github actions 를 이용해서 main 배포시 release 문서생성과 태그도 자동화 해놨다.

### main branch (prod 환경)
- 직접 push , PR merge 불가 (승인 받아야 머지 가능)
- 배포시 git release tag 생성 및 Github Release 문서생성

### stg branch (중간 단계)
- dev 에서 올라온 작업물을 최종 QA 하는 곳
- stg 에서 발견한 문제는 stg에서 수정해서 처리한다.
- 사실 지금 가장 예매한 단계 바빠서 잘 사용 안하는 경우 대다수

### dev branch 
- 각 작업물이 제일 먼저 합쳐지는 곳 
- feat 브랜치에 시작 점

### qa branch 
- dev에 병합 되고 삭제 되는 브랜치
- qa 서버가 따로 존재 해서 작업들 기능 단위 테스트할때 매우 편함
- qa 서버는 (1~3) front , server 각각 존재
- 기능 하나당 한 qa 서버 점유 (1:1)
- 사실 feat 브랜치 역할
- 크롬 익스텐션 [모두헤더] 활용해서 이용중

### hotfix
- cx팀 긴급호출 이거 뭐냐 상황 발생
- main 에서 브랜치 따서 작업후 곧 바로 main PR
- main에 병합되고 삭제
- main 에서 각각 stg ,dev 로 merge

## 실제로 어떻게 일하고 있는가?
1. 기획 + 디자인 핸드오프 후 개발자 일정 산출 및 작업 시작
2. dev 에서 qa 브랜치 분기 (ex: FE : qa3, BE : qa2 )
3. qa에서 작업 + 기능 테스트
4. 개발자 단위 QA 완료후 dev 배포 통합 QA 준비 
5. dev 에서 QA 대응 + 이슈 수정 
6. QA 완료후 release 준비 stg , main 차례대로 PR (운영 배포)
7. 배포 후 dev 각각 main merge (사실상 생성된 git tag 최신화)
8.  반복

[모두헤더]:https://modheader.com/

