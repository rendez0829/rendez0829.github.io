---
title: Connect Okta to Identity Authentication
tags: [SAP BTP, IdP]
style: 
color: 
description: IAS(Identity Authentication Service)와 and  `Okta` 간의 trust configuration 하는 방법에 대해 설명합니다.
---

<!-- <br/>
<center><img src="https://rendez-static.herokuapp.com/resource/images/code_review001.gif" width="50%"></center>
<br/>
<br/> -->

## 개요
> IAS(Identity Authentication Service)와 and  `Okta` 간의 trust configuration 하는 방법에 대해 설명합니다. `Okta`와 ID Identity Authentication 간의 연결이 완료되면 이를 사용하여 여러 애플리케이션 및 환경에 연결할 수 있습니다.

<br/>

## 사전 준비

1. IAS를 보유하고 있어야 합니다.
2. Application 관리 및 기업 IdP 관리를 IAS를 통해 관리해야 합니다.
4. Okta 서비스를 사용하고 있어야 합니다.

<br/>

## 시작하기

#### Step 1: Okta 관리 포털에 로그인하고 SAML 2.0 애플리케이션 생성

<br/>

##### 1) 'Use single sign on' –> 'Add App' 선택
![](https://rendez-static.herokuapp.com/resource/sap_btp/okta/connect_okta_ias_001.png)

> **참고사항**
>
> Okta에는 사전 정의된 IdP 애플리케이션이 없으므로 수동으로 생성하고 구성해야 합니다.

Okta 측 구성에 대한 자세한 내용은 공식 Okta 문서: AIW(응용 프로그램 통합 마법사)를 사용하여 [SAML 통합 만들기](https://help.okta.com/en/prod/Content/Topics/Apps/Apps_App_Integration_Wizard_SAML.htm)를 참조하세요.

<br/>

##### 2) 'Create New App' 선택
![](https://rendez-static.herokuapp.com/resource/sap_btp/okta/connect_okta_ias_002.png)

<br/>



##### 3) 로그인 방법으로 SAML 2.0을 선택합니다.
![](https://rendez-static.herokuapp.com/resource/sap_btp/okta/connect_okta_ias_003.png)

<br/>

##### 4) Application 생성의 마지막 부분으로 사용자 정의 애플리케이션 이름, 로고를 정의할 수 있습니다. 이름을 정하고 'Next' 버튼을 클릭합니다.
![](https://rendez-static.herokuapp.com/resource/sap_btp/okta/connect_okta_ias_004.png)

<br/>


#### Step 2: Okta에서 SAML Integration 생성

이 단계에서는 ID 인증에서 가져온 SAML 설정을 채워야 합니다. 이 부분에서 취한 모든 단계에 특별한 주의를 기울이십시오.

![](https://rendez-static.herokuapp.com/resource/sap_btp/okta/connect_okta_ias_005.png)

<br/>

##### **Single sign on URL:**
URL 정보는 다음 단계에 따라 확인하세요.

1. IAS(ID 인증) 관리 콘솔 열기
```
https://<tenantid>.accounts.ondemand.com/admin
```
2. `Application & Resouce > Tenant Settings > SAML 2.0 Configuration` 로 이동
3. 'Assertion Consumer Service Endpoint (ACS endpoint)' URL 복사

![](https://rendez-static.herokuapp.com/resource/sap_btp/okta/connect_okta_ias_006.png)

URL을 복사하여 붙여넣은 후 `Use this for Recipient URL and Destination URL` 옵션을 선택합니다.

`IdP-initiated SSO`를 사용하려면 위의 URL을 아래와 같이 구성합니다.
```
https://<the current ACS endpoint URL>?sp=<sp_name>&index=<index_number>
```

Identity Authentication 의 Tenant 관리자에게 서비스 제공자의 Entity ID와 Application의 보호된 페이지 index를 요청합니다.

index는 필수입니다.

`Okta`에서는 두 URL을 모두 설정할 수 있습니다(이 앱이 다른 SSO URL을 요청하도록 허용).


![](https://rendez-static.herokuapp.com/resource/sap_btp/okta/connect_okta_ias_007.png)

<br/>

##### **Audience URI (SP Entity ID):**
URL 정보는 다음 단계에 따라 확인하세요.

IAS Tenant의 'Name'과 동일해야 합니다.

URL 정보는 다음 단계에 따라 확인하세요.
1. IAS(ID 인증) 관리 콘솔 열기
```
https://<tenantid>.accounts.ondemand.com/admin
```
2. `Application & Resource > Tenant Settings > SAML 2.0 Configuration` 로 이동
3. Identity Provider Settings의 'Name' 복사

![](https://rendez-static.herokuapp.com/resource/sap_btp/okta/connect_okta_ias_008.png)

> **참고**
>
> 대상이 [KBA 2693814](https://launchpad.support.sap.com/#/notes/2693814) - 서비스 제공자가 SAML2Assertion에 지정된 대상과 일치하지 않는 설명과 정확히 일치하는지 확인하십시오. 

<br/>

##### **Default RelayState:**
Default RelayState 는 기본값으로 둡니다.

추가 SAML 설정도 기본값으로 둡니다.

'Next' 버튼을 선택합니다.

<br/>

#### Step 3: Okta에서 Identity Provider metadata 파일 다운로드

Okta에서 'Dashboard' 메뉴로 이동합니다.

![](https://rendez-static.herokuapp.com/resource/sap_btp/okta/connect_okta_ias_009.png)

<br/>

SSO Apps를 선택합니다.

![](https://rendez-static.herokuapp.com/resource/sap_btp/okta/connect_okta_ias_010.png)

<br/>

앞서 생성한 `SAP SSO Tutorial` 을 선택합니다.

![](https://rendez-static.herokuapp.com/resource/sap_btp/okta/connect_okta_ias_011.png)

<br/>

Okta에서 `Sign On` 탭으로 이동한 다음 `SAML Signing Certificates > Actions > View IdP metadata` 선택

![](https://rendez-static.herokuapp.com/resource/sap_btp/okta/connect_okta_ias_012.png)

<br/>

metadata 내용을 `xml` 파일로 저장

![](https://rendez-static.herokuapp.com/resource/sap_btp/okta/connect_okta_ias_013.png)

<br/>

#### Step 4: Identity Authentication tenant Trust Configuration

이 단계에서는 Identity Authentication은 회사 Identity Provider에게 인증을 위임하는 proxy 역할을 합니다. 자세한 내용은 공식 SAP 문서인 [Configure Trust with Corporate Identity Provider](https://help.sap.com/viewer/6d6d63354d1242d185ab4830fc04feb1/Cloud/en-US/33832e58695345eea2cd91a2cc8ab24c.html)을 확인하십시오.

Identity Authentication을 proxy로 사용하여 external corporate identity provider에 인증을 위임하려면 해당 corporate identity provider와의 Trust Configuration을 해야 합니다.

Step3에서 다운로드한 Okta metadata를 IAS로 Import 하는 순서입니다.

1. IAS(ID 인증) 관리 콘솔 열기
```
https://<tenantid>.accounts.ondemand.com/admin
```
2. `Identity Providers > Corporate Identity Providers`로 이동
3. 'Create' 선택해 새로운 IdP를 등록합니다.

![](https://rendez-static.herokuapp.com/resource/sap_btp/okta/connect_okta_ias_014.png)

<br/>

Name은 'Okta'로 입력하고 저장합니다.

![](https://rendez-static.herokuapp.com/resource/sap_btp/okta/connect_okta_ias_015.png)

<br/>

생성된 IdP에서 `SAML 2.0 Configuration`를 선택하고 Step3에서 다운로드한 Okta metadata를 import 합니다.


![](https://rendez-static.herokuapp.com/resource/sap_btp/okta/connect_okta_ias_016.png)

<br/>

![](https://rendez-static.herokuapp.com/resource/sap_btp/okta/connect_okta_ias_017.png)

<br/>

이렇게 진행하면 필요한 정보들이 자동으로 채워집니다. 

마지막으로 Single Logout Endpoint를 설정합니다. Name과 동일한 값에 `/logout` path만 추가로 지정해  Endpoint URL을 설정합니다.

이제 설정이 끝났습니다. 저장합니다.

![](https://rendez-static.herokuapp.com/resource/sap_btp/okta/connect_okta_ias_018.png)

Tenant 관리자는 SAML 2.0 로그아웃 response를 확장해 전송되는 링크를 지정할 수 있습니다. 이 링크는 Identity Authentication이 identity provider proxy 역할을 할 때 Application에서 로그아웃한 후 사용자를 redirect하는 데 사용할 수 있습니다. 공식 문서를 참고하십시오. [Service Provider Initiated Logout with Corporate Identity Providers.](https://help.sap.com/viewer/6d6d63354d1242d185ab4830fc04feb1/Cloud/en-US/3841580082cb4af6a13289e98a0cce12.html)

'Logout Redirect URL' 옵션을 선택합니다. 로그아웃이 성공한 후 최종 사용자를 redirect할 URL을 설정합니다.


![](https://rendez-static.herokuapp.com/resource/sap_btp/okta/connect_okta_ias_019.png)


![](https://rendez-static.herokuapp.com/resource/sap_btp/okta/connect_okta_ias_020.png)


<br/>


#### Step 5: Okta를 identity provider로 사용하기 위한 Application 연결

IAS의 관리 콘솔에서 'Applications & Resources'로 이동한 다음 'Applications' 탭을 클릭하고 Applications을 구성하거나 기존 Applications을 선택합니다.

#### **Option1**

Application의 'Trust' 탭에서 'Conditional Authentication' 옵션을 클릭합니다. Okta를 `Default Identity Provider`로 설정합니다.

![](https://rendez-static.herokuapp.com/resource/sap_btp/okta/connect_okta_ias_021.png)

![](https://rendez-static.herokuapp.com/resource/sap_btp/okta/connect_okta_ias_022.png)

자세한 내용은 공식 문서: [Choose a Corporate Identity Provider as Default](https://help.sap.com/viewer/6d6d63354d1242d185ab4830fc04feb1/Cloud/en-US/44dd6368b6ed4e9994c45b3eeffac320.html) 를 참고하세요.

<br/>

#### **Option2**

'Trust all corporate Identity Providers'를 설정합니다. 이 경우 사용자를 Okta로 redirect하도록 `Conditional Authentication`을 정의해야 합니다.

![](https://rendez-static.herokuapp.com/resource/sap_btp/okta/connect_okta_ias_023.png)

<br/>
<br/>


#### Step 6: Okta에서 Application User Assign

Application에 접근할 수 있는 User를 Assign 합니다.
![](https://rendez-static.herokuapp.com/resource/sap_btp/okta/connect_okta_ias_024.png)
![](https://rendez-static.herokuapp.com/resource/sap_btp/okta/connect_okta_ias_025.png)
![](https://rendez-static.herokuapp.com/resource/sap_btp/okta/connect_okta_ias_026.png)

<br/>

#### Step 7: Application에 접속해 Idp가 정상 동작하는지 확인

Application에 접속하면 Default IdP를 사용하지 않고 Okta를 기본 IdP로 사용하는것을 확인할 수 있습니다.
![](https://rendez-static.herokuapp.com/resource/sap_btp/okta/connect_okta_ias_027.png)

<br/>

#### **요약**

위의 단계를 수행한 후 Application은 Okta를 corporate identity provider로 사용해야 하며, 이 경우 IAS가 proxy 역할을 합니다.

> 참고
>
>구성하는 동안 문제가 발생하면 IAS Tenent에서 Troubleshooting logs를 통해 오류의 원인을 확인할 수 있습니다. [KBA 2942816](https://launchpad.support.sap.com/#/notes/2942816) – How to export troubleshooting logs from Identity Authentication.

