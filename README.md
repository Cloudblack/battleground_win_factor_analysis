<div align="center">
  
# 어떻게 하면 게임을 잘 할 수 있을까?
  
</div>

## 프로젝트 기간
2021/8/30 ~ 2021/9/3

### 목차
- [프로젝트 주제](#프로젝트-주제)  
- [프로젝트 진행](#프로젝트-진행)
- [데이터](#데이터)
- [데이터 분석](#데이터-분석)
- [새로운 피처](#새로운-피처)
- [모델](#모델)
- [결과 요약 동영상](#결과-요약-동영상)
- [결론](#결론)



## 프로젝트 주제
<img src="https://user-images.githubusercontent.com/86823305/171443201-5427346a-8574-40d3-95e8-9ed513eea088.png"  width="800" />

# 프로젝트 진행
## 데이터
- 캐글의 배틀그라운드 승률 예측 데이터를 사용


## 데이터 분석
<img src="https://user-images.githubusercontent.com/86823305/171443576-6f454c4d-aa98-4c50-957b-20e56c393fa3.png"  width="800" />

가진 데이터 셋은 29개의 피처를 가진 약 4만4천판 , 440만 명의 데이터를 갖고있다
<br>
<br>

<img src="https://user-images.githubusercontent.com/86823305/171443759-621060d0-5ef9-4c38-a14b-9e7b5abe9d33.png"  width="800" />

목표로 하는 타겟값은 상위 10프로 , 타겟이 0.9이상을 잘하는 사람, 1로 두었습니다  
베이스라인은,  0 하나의 값으로만 예측해도 나오는 예측치의 최소 컷 0.894723입니다
<br>
<br>

<img src="https://user-images.githubusercontent.com/86823305/171443961-d54b02b8-5432-4c2c-8c96-dd5f91060284.png"  width="800" />

다른 특성들과 타겟의 상관관계입니다  
오래 걸을수록 생존률이 높은거니까 상관관계가 높고 
킬 플레이스는 킬 순위로 숫자가 높을수록 하위권이어서 타겟과 음의 상관관계가있다
max place와 numGroup은 아주 높은 상관관계에있습니다

<br>
<br>

<img src="https://user-images.githubusercontent.com/86823305/171444198-c1acbf8e-a944-4953-ae10-6c2792229182.png"  width="800" />

matchType의 Crash 모드와 나머지 모드의 longestkill과 matchDuration입니다  
롱킬의 맥스 값을보면 약 10배가 차이나는걸 볼수있고 매칭시간도 약 2배이상 차이나느걸 볼 수있습니다     
알아본 결과 Crash는 근접전투 전용 이벤트 모드였습니다  
Flare또한 이벤트 모드여서 정상적인게임의 승리 예측에 방해가 된다고 생각되어 제거하게되었습니다

<br>
<br>

<img src="https://user-images.githubusercontent.com/86823305/171444474-53bf1006-c83d-4e3d-a2d5-71e0395aadc1.png"  width="800" />

상관관계가 아주 높았던 maxPlace와 numGroups입니다   
실제 참여팀인 numgroups 공통적으로 시작하자마자 나가거나   
듀오나 스쿼드에선 원래 팀의 인원수보다 부족한경우가있어 적을 수 있습니다  
약간의 차이가 있지만 서로 비슷한 값이어서 맥스 플레이스를 제거했습니다  

<br>
<br>

<img src="https://user-images.githubusercontent.com/86823305/171444694-4104bb91-e28f-4ccf-9b34-94a6be90c7b8.png"  width="800" />

Points들은 각각의 사람들이 이전 게임에서 부터 받은 ELO , 보이지않는 랭킹 개념이라 그 판의 행동을 보고 예측하기 위해서는 필요없는 정보여서 제거했습니다

<br>
<br>

<img src="https://user-images.githubusercontent.com/86823305/171444813-8677ec8a-f2da-40ed-8f1b-3ed3c9cea01a.png"  width="800" />

결측치가 단 하나있었는데 모든값이 0이어서 제거했습니다

<br>
<br>

<img src="https://user-images.githubusercontent.com/86823305/171446559-23337fd9-5a2d-4d3e-8fe2-1169268dc5de.png"  width="800" />

전처리 중 잦은 세션 다운의 원인을 찾는 과정에서  
전체 이동거리가 0이 나온 게임의 존재를 알게 되었고 살펴보니 한게임에 1~2명 밖에 없었습니다  
제대로 시작이 안된 게임이거나 서버 오류로 의심됩니다 

<br>
<br>


<img src="https://user-images.githubusercontent.com/86823305/171447011-48a8cab6-1fa0-4b03-8636-40b711844a99.png"  width="800" />

매칭별 numgroups의 이상치를 IQR을 이용해 찾았고 정상적인 게임이 아니라는 판단으로 제거했습니다


<br>
<br>


<img src="https://user-images.githubusercontent.com/86823305/171447090-22e833bb-4690-49f0-b38e-bc22e076ec57.png"  width="800" />

- 킬수의 이상치를 핵으로 의심했습니다 핵의 특징으로 높은 킬수, 짧은 이동거리, 짧은 게임시간, 항상 1등을 기준으로 두었으나 실제 전적사이트의 랭커들이 자주 10킬이상을 하는 기록이 존재
- 초반에 여러 스쿼드가 뭉쳐서 많은 킬을하고 빨리 죽어 이동거리가 짧을 가능성
- 게임시간에는 이상치가 존재하지않음
- 무엇보다 1등인지 확인하기위해선 타겟값을 이용해야하기때문에 혹시 모를 data leakage를 경계해서  
핵검거는 포기하고 그냥 운이좋은 판이었다고 생각하기로 했습니다


<br>
<br>

### 새로운 피처

<img src="https://user-images.githubusercontent.com/86823305/171444940-b2d14e8f-0ee7-4269-a9d5-b1bfe5a089bc.png"  width="800" />

전체이동 거리분에 걸음 차량 수영의 지분을 표현하는 새로운 피처를 만들었습니다

<br>
<br>

<img src="https://user-images.githubusercontent.com/86823305/171445067-36420315-7cfc-4774-a81c-95d47e1cf67b.png"  width="800" />

matchId를 기준으로 각 게임당   
전체 인원의 이동거리분에 개인 이동거리를 표현 하는 새로운 feature를 만들었습니다

<br>
<br>

<img src="https://user-images.githubusercontent.com/86823305/171445233-a3b2d8f9-94ae-498e-a0fc-b9f6c59186e6.png"  width="800" />

쉽게말하면 각 게임이있고 그 게임 인원들이 전체 이동거리 분의 개인의 이동거리입니다  
그밖에도 여러가지 피처를 만들었습니다


<br>
<br>

<img src="https://user-images.githubusercontent.com/86823305/171445574-85711aee-f263-4bcf-a2b3-4f6696931636.png"  width="800" />

차만 타고 다니며 킬을하는 매드맥스


<br>
<br>

<img src="https://user-images.githubusercontent.com/86823305/171445660-6a72a948-8bdd-4091-849d-77bac3a3d232.png"  width="800" />

하루 종일 템만 줍고다니는 황금고블린


<br>
<br>

<img src="https://user-images.githubusercontent.com/86823305/171445697-55b018ec-0aea-44ed-b492-db8a58339165.png"  width="800" />

외길에서 차량만 강탈하는 검문소


<br>
<br>

<img src="https://user-images.githubusercontent.com/86823305/171445771-849d9557-4312-4e67-a122-11c53c86c55b.png"  width="800" />

오로지 맨손 격투만 즐기는 주먹왕랄프


<br>
<br>

<img src="https://user-images.githubusercontent.com/86823305/171445932-dd98fffe-7fe2-424f-bcfb-43267a4fe1ed.png"  width="800" />

뒤에서 구경만 하는 유미


<br>
<br>

<img src="https://user-images.githubusercontent.com/86823305/171445999-e19e838b-a06c-4fbe-8faa-dd845322f68c.png"  width="800" />

머리만 노리는 헤드헌터 등을 만들었습니다  
더 많은 피처를 만들고 싶었지만 시간관계상 만들지 못했습니다



<br>
<br>

<img src="https://user-images.githubusercontent.com/86823305/171444813-8677ec8a-f2da-40ed-8f1b-3ed3c9cea01a.png"  width="800" />

오로지 맨손 격투만 즐기는 주먹왕랄프


<br>
<br>

<img src="https://user-images.githubusercontent.com/86823305/171444813-8677ec8a-f2da-40ed-8f1b-3ed3c9cea01a.png"  width="800" />

오로지 맨손 격투만 즐기는 주먹왕랄프


<br>
<br>

## 모델 
<img src="https://user-images.githubusercontent.com/86823305/171447188-b313bd9b-61d7-4fee-808e-b69322567ff5.png"  width="800" />

- 0.9 이상을 예측하는 분류 문제를 풀기위해 여러 개의 트리와 가중치를 줘서 과적합 방지와,  
일반화에 더 좋은 XGB를 사용
- 최적의 하이퍼 파라미터를 찾기위해 randomizesearch CV로 튜닝했습니다  
- 타겟의 임발란스가 심해 이에 맞는 분류  평가지표로 ROC커브 AUC score를 사용했습니다
- scale_pos_weight,base_score를 상시 고정
- n_estimators와 learning rate를 튜닝하며 나머지를 고정  
- max_depth, min child weight, colsample_bytree를 튜닝하며 나머지를 고정
- Gamma alpha lambda 튜닝하며 나머지를 고정
- 후에 Learning rate를 내리고 다시 반복하는 것이었습니다   
하지만 데이터가 무거워 시간이 오래걸려 많은 튜닝을 시도 할 수 없었습니다

<br>
<br>


<img src="https://user-images.githubusercontent.com/86823305/171447587-5d09fb9f-faac-471c-9d57-8455c502839a.png"  width="800" />

이전의 부분 튜닝들의 결과를 가지고 하이퍼 파라미터를 전체 튜닝하여 값을 얻게되었습니다

<br>
<br>

![image](https://user-images.githubusercontent.com/86823305/171447782-f43bb3d9-451c-4152-b3aa-4b032016f6b0.png)

학습된 모델을 이용하여 train과 validation의 AUC값을 구하고 비교해 보았습니다 둘다 0.9가 넘는 AUC값을 얻을 수 있었습니다
 
<br>
<br>

![image](https://user-images.githubusercontent.com/86823305/171448120-10adb951-fcb7-4699-b3c2-62f7c85e2762.png)

그런데 듀오나 스쿼드의경우 한명이 캐리를 해 1등을 하는 경우 세명이 수영만 하고있어도 1등이 되어버립니다

<br>
<br>

![image](https://user-images.githubusercontent.com/86823305/171448260-013af5a4-e9af-473a-9ed0-37db54ec5d25.png)

이 부분을 groupId를 기준으로 사람들을 묶고 그중 가장 높은 점수를 같은  groupId를 가진 사람들 에게도 적용하였습니다


<br>
<br>

![image](https://user-images.githubusercontent.com/86823305/171448312-a01b412e-709d-42dc-814d-789609add711.png)

새로 구한 값으로 validation의 AUC값을 측정한 결과 0.9301로 이전보다 높은 값을 얻을 수 있었습니다.


<br>
<br>

![image](https://user-images.githubusercontent.com/86823305/171448388-48a7eb43-3d63-4270-953b-a4c8c63da74a.png)


이전 튜닝을 바탕으로 새롭게 튜닝한 값입니다 마찬가지로 groupId로 묶은 결과도 내보았습니다


<br>
<br>

![image](https://user-images.githubusercontent.com/86823305/171448441-72280991-6846-4816-b89c-7c8a65b7a604.png)


이전 값과 비교하를 하면 
새롭게 튜닝 후 근소하게나마 AUC값을 조금 더 끌어올렸습니다


<br>
<br>

![image](https://user-images.githubusercontent.com/86823305/171448566-0b8d27ec-e561-46b9-a98b-723416897170.png)


Xgb의 지원하는 피처 임폴턴스와 펄뮤테이션 임폴턴스입니다  
이동거리에 관한 내용은 같은 방향이지만 그외의 boosts  longkill assist등은 뒤집히는 결과를 볼 수 있습니다  
Permutationimportance는 피처를 하나씩 순서를 무작위로 바꾸어 사실상 피처를 제거하고 예측값을 테스트하는 효과로 피처의 유무로   
타겟값에 얼마나 영향을 끼치는지를 알아볼수있어 더욱 정확하게 판단할 수 있다고 생각합니다  
펄뮤테이션 임폴턴스를 기준으로 피처들을 제거하고 학습해보고 싶었는데 아쉽게도 시간이 부족하여하지 못했습니다.


<br>
<br>

![image](https://user-images.githubusercontent.com/86823305/171448665-8df06455-ae6b-4767-a4eb-c97e04f5d4ea.png)

Shap을 이용해서 각 개인별로 피쳐가 어떻게 확률을 정했는지 알 수 있씁니다 빨간색은 확률을 올리는 방향 파란색을 올리는 방향입니다   
예를들어 마지막 플롯을 보면   
이 플레이어는 무기획들을 잘했으며 한번의 어시스트를 하며 팀을 도왔고 평균보다 두배의 이동거리를 이동하며 승률을 올렸습니다    
하지만 차량을 너무 오래이용하며 얼마 걷지 않았으며 도중에 부스트아이템을 한번만 사용하며 단하나의 킬도 만들지못하며 승리에 절반정도에 그쳤다 

<br>
<br>

![image](https://user-images.githubusercontent.com/86823305/171448790-51d99bed-8d11-42da-85c8-0fa70ddb8911.png)


모델에 대한 Shap의 서머리와 feature 임폴턴스 바 그래프입니다   
Summary는 위아래로는 피쳐의 중요도 왼쪽은 피쳐의 값이 줄어들고 오른쪽은 늘어나며 동시에 빨간색은 확률을 높이고 파란색은 확률을 떨어뜨립니다  
이상으로 0.93의 auc값을 가진 모델을 만들었고 게임에 승리하기 위한 중요한 특성들도 알게 되었습니다   

## 결과 요약 동영상

## 결론
결론적으로 게임을 잘하기위해선 
- 매칭시 사람이 조금만 들어오길 기도하고 
- 차는 폐차시키고 
- 적당한 걷기와 긴 수영으로 최대한 많이 이동하며 
- 아이템을 많이 줍고 
- 부스트 음료를 상시 복용하며 
- 팀원을 도우며 적을 죽이되 
- 최대한 멀리서 죽이며 
- 시간을 질질 끌면 됩니다

![image](https://user-images.githubusercontent.com/86823305/171448993-c3ddb51f-a0bc-4edf-b1b8-13d383bfc22c.png)
