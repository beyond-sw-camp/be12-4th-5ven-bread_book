# 중고서적 거래 플랫폼 책빵 📖🍞 !
<p align="middle" style="margin: 0; padding: 0;">
  <img width="200px" src="https://github.com/user-attachments/assets/59f2249b-790f-416c-a965-42a51ac5e06e">
</p>

<p align="middle">
[플레이 데이터] 한화시스템 BEYOND SW캠프
<br>🥪팀 5VEN
</p>

## 😃 팀원 소개

<figure>
    <table>
      <tr>
        <td align="center"><img src="https://github.com/user-attachments/assets/161f9f29-3a5e-415c-96ac-3dfe206d2c81" width="180px"/></td>
        <td align="center"><img src="https://github.com/user-attachments/assets/aefd8909-7201-4162-be56-b4815512d4c4" width="180px"/></td>
        <td align="center"><img src="https://github.com/user-attachments/assets/f73098ea-a9ee-4915-b38a-a6c05f6e4c6a" width="180px"/></td>
	<td align="center"><img src="https://github.com/user-attachments/assets/854f6d73-5aac-4c17-add8-5c5ebc768f7b" width="180px"/></td>
        <td align="center"><img src="https://github.com/user-attachments/assets/06e97207-fffe-459c-934e-a2ef79ef4f22" width="180px"/></td>
      </tr>
      <tr>
        <td align="center">팀장: <a href="https://github.com/daydeiday">곽효림</a></td>
        <td align="center">팀원: <a href="https://github.com/wkdlrn">김재구</a></td>
        <td align="center">팀원: <a href="https://github.com/ChangeunLim" >임찬근</a></td>
        <td align="center"><strong>팀장</strong>: <a href="https://github.com/InukChoi">최인욱</a></td>
	<td align="center">팀원: <a href="https://github.com/choi-won-ik" >최원익</a></td>
      </tr>
    </table>
</figure>



&nbsp; 

## 🔧 기술 스택
![Kubernetes](https://img.shields.io/badge/k8s-%23326ce5.svg?style=for-the-badge&logo=kubernetes&logoColor=white)
![Docker](https://img.shields.io/badge/docker-%232496ED.svg?style=for-the-badge&logo=docker&logoColor=white)
![Jenkins](https://img.shields.io/badge/jenkins-%23D24939.svg?style=for-the-badge&logo=jenkins&logoColor=white)

### 목차
- [프로젝트 배경](#-프로젝트-배경)
- [jenkins 선택 이유](#-젠킨스-선택-이유)
- [블루/그린 선택 이유](#블루-그린-배포-전략이-중고거래-플랫폼에-적합한-이유)
- [시스템 아키텍처](#-시스템-아키텍처)
- [다른 시나리오와 비교](#-다른-시나리오와의-비교)
- [배포 흐름](#-배포-흐름)
- [CI/CD 테스트 및 결과](#-ci-cd-테스트-및-결과)
<br><br>
---

## 🌳 프로젝트 배경

우리 프로젝트는 **중고거래 플랫폼**으로서 사용자 피드백에 빠르게 대응하고, 새로운 기능을 신속하고 안정적으로 배포하는 것이 중요합니다. 특은 1:1 거래가 주요 비즈니스 모델이므로, 사용자 경험에 직접적인 영향을 주는 기능들을 **최대한 빠르고 오류 없이 배포하는 것이 필수적**입니다. 이러한 요구사항을 충족하기 위해 **젠킨스(Jenkins)** 를 CI/CD 도구로 선택했습니다.
1. **배포 파이프라인** 구성 방안 : **Jenkins**  (빌드 → 배포)
    - Jenkins Agent Server 3대 구성: ⓐMaster ⓑSlave1 ⓒSlave2
2. **배포 전략**: **Blue/Green Deployment** 
    - K8S 6대 구성: master 1 / worker 5대
3. **서버 구성 자동화**: **Ansible** (IaC) 연동


### ⚙ Frontend / Backend 시스템 공통
|       | 빌드      | 배포              |
|-------|---------|-----------------|
| 자동화도구 | Jenkins | Jenkins         |
| 사용 기술 | Docker  | K8S Blue/Green |

### ⚙ Kafka 시스템
|       |서버준비단계| 빌드     | 배포 |
|-------|---|-|----|
| 자동화도구 | Ansible   | 아파치 제공     | Ansible |

### 💫 젠킨스 선택 이유
| 구분 | 내용 |
|------|------|
| **브랜치 전략 지원** | Git Flow, GitHub Flow 등 다양한 브랜치 전략을 쉽게 구성하여 기능 단위 배포와 핫픽스 대응이 가능 |
| **Docker 기반 배포 자동화** | 개발과 운영 환경 간 일관성 보장 및 빈번한 업데이트에도 시스템 안정성 유지 |
| **코드 품질 관리** | SonarQube 등 품질 관리 도구와 통합이 쉬워, 배포 전 코드 품질 검사 및 자동 검증 가능 |
| **오픈소스 기반 유연성** | 오픈소스 기반으로 커뮤니티, 문서가 풍부해 빠른 적응 및 운영 중 이슈 신속 해결 가능 |


### 🟦🟩 블루-그린 배포 전략이 중고거래 플랫폼에 적합한 이유

| 구분 | 설명 |
|------|------|
| **서비스 무중단 배포 신뢰성** | 새 버전을 별도 환경에 배포 후 트래픽을 전환하여 서비스 중단 리스크 최소화. 문제 발생 시 즉시 기존 버전으로 전환 가능 |
| **명확한 배포 시점과 빠른 롤백** | 트래픽 전환이 즉각적이며, 긴급 이슈 발생 시 원클릭 롤백 가능. 롤링·카나리 배포보다 빠름 |
| **사용자 경험 일관성 보장** | 모든 사용자에게 동일한 버전을 동시에 제공해 혼란 방지 및 품질 유지 |
| **젠킨스와의 높은 통합성** | 젠킨스 플러그인 및 파이프라인을 통해 블루-그린 배포 자동화, 모니터링, 롤백까지 완전 자동화 가능 |

---

## 🌐 시스템 아키텍처


---

## 📚 다른 시나리오와의 비교

|**GitHub Actions**| 빌드 → 배포|
|------|------|
|**Jenkins**|  빌드 → 배포|
|**CircleCI**+ α|  빌드|

- Jenkins는 온프레미스, 내부망 환경에서 구축 자유롭고, 온프레미스 서버를 직접 운영할 수 있음
- Jenkins는 개인정보 보호 및 내부 데이터 관리에 적합하여 **보안과 안정적 관리가 가능해 적합**
- GitHubActions와 달리 Jenkins는 **세부적 관리 및 커스터마이징 가능**
- CircleCI를 사용할 경우 서비스 장애 시 운영 불가하나, Jenkins의 경우**자체 서버 운영으로 안정성 확보** 
- CircleCI를 사용할 경우 외부 서비스에 의존하는 종속성이 생기나, Jenkins의 경우 **독립적이고 자율적인 환경 제공**

---

## 🌀 배포 흐름

1. **Ansible 연동을 통한 서버 관리 및 배포 자동화**
    - Ansible을 통해 서버 설정 관리, 배포 자동화 스크립트 실행을 간소화하여 관리 효율성을 높입니다.
2. **코드 변경 및 푸시**
    - `Feat`, `Fix` 등 개발에 따른 프로젝트(코드) 변경사항을 GitHub 원격 레포지토리에 `Push`합니다.

3. **GitHub 웹훅을 통한 젠킨스 파이프라인 실행 요청**
    - GitHub에서 젠킨스로 `WebHook` 호출 → **파이프라인 자동 실행.**

4. **젠킨스 파이프라인 실행 순서**
    - `git clone` 
    - `build`
    - `Docker` 이미지 생성 및 레지스트리로 푸시 (CI)
    - 배포를 위한 `YAML` 파일 전송 후 **블루-그린 배포** 적용 (CD)

5. **프론트엔드와 백엔드 분리 배포**
    - 각각 독립적인 배포 파이프라인을 구성하여, 프론트엔드와 백엔드의 기능 변경을 빠르게 반영할 수 있습니다.


---

## 💻 CI/CD 테스트 및 결과


<details>
   <summary> 젠킨스 파이프라인 </summary>


</details>
&nbsp; 
<details>
   <summary> K8S Blue/Green 배포 </summary>


</details>
&nbsp; 
<details>
   <summary> 디스코드 알림 </summary>


</details>
