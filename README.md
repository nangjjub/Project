# Project


### [1] 주제와 동기

 평소 여가시간에 인터넷 방송을 즐겨 본다. 여느 때처럼 방송을 시청하다 문뜩 방송인에게 후원되는 금액을 구경하면서 방송의 컨텐츠에 따라서 방송에 관련된 특성들이 차이가 있을 수도 있겠다는 의문이 들었다. 예를 들어 시청자의 평균 연령층, 후원되는 금액, 방송 시간, 시청자 수 등의 특성이다. 이런 특성들을 가지고 방송의 컨텐츠(카테고리)별로 분류하고 특성들을 가지고 방송인이 어떤 컨텐츠의 방송을 하는지 예측할 수 있겠다고 생각했다. 그로 인해 선정하게 된 주제는 후원 금액과 방송 시간, 시청자 수를 바탕으로 방송인의 주된 컨텐츠가 어떤 것인지 예측하는 모델을 구성하는 것이다.


### [2] 데이터 처리 및 알고리즘

(1) 데이터 확보 방법 
 https://poong.today/chart/month 사이트에서 한 달 동안 방송인의 후원받은 유료 아이템의 개수, 최대 동시 시청자, 누적 시청자, 총 방송시간 등을 볼 수 있다(연령층 데이터는 존재하지 않아 추가하지 못했다). 각 카테고리별로 후원을 많이 받은 1000명씩 복사, 붙여넣기 방식으로 메모장에 옮겨 데이터를 만들었다. 예측할 카테고리는 가장 인기 있는 카테고리인 (토크/캠방, 리그오브레전드, 배틀그라운드)로 선정했다.

(2) 분석 목표 및 알고리즘
 분석 알고리즘은 방송 카테고리별로 분류 작업을 수행할 것이기에 knn 알고리즘과 방송 특성의 가중치를 확인하기 위해 Logistic Regression을 사용하기로 정했다. knn 알고리즘을 통해서 분류를 예측하는 정확도(Acuuracy)를 측정해보고 방송의 특성을 바탕으로 카테고리가 예측이 가능한지에 대해서 판단할 생각이다. 또한 Logistic Regression을 통해 가중치를 얻어 분류의 기준이 되는 주된 요인들이 무엇인지 판별할 생각이다.

(3) 분석 과정
 먼저 메모장에 담긴 데이터들을 pandas-DataFrame 형식으로 정제했다. 그 후 정제된 데이터들이 특성별로 값의 차이가 매우 크다고 판단되어 데이터에 Z-Scaling을 적용했다.(자세한 데이터 정제과정은 코드 마크다운을 통해 설명했다.)
 
<img width="499" alt="a1" src="https://github.com/nangjjub/Project/assets/169229588/346999b0-b074-43f3-b1e0-949363f74769">

(원본 데이터를 DataFrame으로 변환한 후 결과)   

<img width="501" alt="a2" src="https://github.com/nangjjub/Project/assets/169229588/861d2f75-947e-4db7-9b35-865cb76befd2">
        
(데이터를 Z-Scaling한 결과)
 
 분류 과정은 대부분 코드는 챗지피티를 활용했다. 간단하게 사용한 모듈들을 살펴보면
 
<img width="437" alt="a3" src="https://github.com/nangjjub/Project/assets/169229588/a4c8076d-2127-4a5e-8d32-7810cb0a1b9d">

먼저 벡터끼리의 거리를 계산해주는 minkowskiDist 함수이다. p값에 따라 1일 때 맨해튼 거리를 2일 때 유클리디언 거리를 계산해 반환해준다.

<img width="373" alt="a4" src="https://github.com/nangjjub/Project/assets/169229588/b8bd79db-0b54-4368-bafb-349286b8eaae">

 다음으로 feature distance를 계산하기 위해서 기존의 Passenger 클래스를 수정하여 class Broadcaster를 생성했다. 메소드로 distance는 minkowskiDist함수를 불러와 p값을 2로 설정해 유클리디언 거리 측정으로 계산해 반환하도록 구성했다. (별풍선 개수, 최대동시시청자, 총 방송시간)을 feature로 가지며 label은 각각 토크/캠방일 때 0, 리그오브레전드일 때 1, 배틀그라운드일 때 2 값으로 설정해줬다.

<img width="377" alt="a5" src="https://github.com/nangjjub/Project/assets/169229588/b2e235cb-3118-42c0-8827-394a9ed3593a">

그 후 생성된 Broadcaster 클래스를 바탕으로 알고리즘에 넣을 examples를 생성해줬다.

<img width="241" alt="a6" src="https://github.com/nangjjub/Project/assets/169229588/97c9d88f-f386-443f-8571-1e79a8731cf7">

다음으로 true와 false값을 입력받아 accuracy를 계산하고 프린트해주는 함수들이다.

<img width="317" alt="a7" src="https://github.com/nangjjub/Project/assets/169229588/a6951416-bec8-4884-98af-8df49b564ee0">

findKnearest 함수는 exampleSet 중에서 example과 가장 가까운 거리에 존재하는 k개의 항목을 kNearest 리스트로 반환해주는 함수이다. 모든 exampleSet을 순회하며 example과 가장 가까운 거리에 있는 포인트들을 찾아준다.

<img width="236" alt="a8" src="https://github.com/nangjjub/Project/assets/169229588/2cc626af-27a5-4c5c-8d46-852e58daecd4">

 다음은 KNearestClassify 함수로 lable 세 개를 받아올 때 true와 false를 측정하도록 코드를 수정했다. TP, FP, TN, FN값을 사용하는 민감도(Sensitivity)나 특이도(Specificity)는 현재 분류할 카테고리가 세 개의 label을 가져 측정할 수 없기에 정확도(Accuracy)만을 측정해서 관찰했다. KNN 알고리즘 수행중 이웃의 개수가 같아지는 경우에 대비해 같아지는 상황에서 한 쪽 부분을 0으로 만들어주어 중복되는 상황을 피하도록 구성했다. 그 후 상황별로 예측의 성공과 실패에 따라 true와 false에 각각 값을 더해주도록 구성했다.

<img width="422" alt="a9" src="https://github.com/nangjjub/Project/assets/169229588/28172828-c606-4eaa-bfb2-7171d5b2f716">

마지막으로 사이킷 런에서 LogisitcRegression 함수를 불러와 각 카테고리별로 특성의 가중치를 측정해보는 buildModel 함수이다.

(4) 분석 결과 화면
 먼저 knn알고리즘에서 k의 값을 3을 주고 측정해본 결과이다. 3000개 중 20% 600개의 testSet의 결과이다.

<img width="308" alt="a10" src="https://github.com/nangjjub/Project/assets/169229588/2e6078c5-009e-41a1-be40-7270fac8795e">

k의 값을 10을 주고 측정해본 결과이다. 3000개 중 20% 600개의 testSet의 결과이다.

<img width="294" alt="a11" src="https://github.com/nangjjub/Project/assets/169229588/fe71a308-c022-4bfc-a313-b7c06c8b1a69">

k의 값을 50을 주고 측정해본 결과이다. 3000개 중 20% 600개의 testSet의 결과이다.

<img width="256" alt="a12" src="https://github.com/nangjjub/Project/assets/169229588/9c750ccd-d198-420e-8650-587ea0cddb84">

이후로 k의 값을 더 키웠지만 더이상 정확도가 좋아지지 않아 여기서 마쳤다.

<img width="454" alt="a13" src="https://github.com/nangjjub/Project/assets/169229588/d9bd08d7-8dd2-4be7-bb12-284db75240db">

다음으로 LogisticRegression을 통해 각 label별로 특성에 어떠한 가중치가 존재하는지 확인해본 결과이다.





### [3] 결과 분석

(1) knn 분류 결과 분석
 knn 알고리즘을 통해서 분류해본 결과 k 값이 비교적 커야 예측의 정확도가 더 올라가는 것을 확인했다. 현재 3000개의 각 카테고리별로 1000개씩 추출된 데이터를 가지고 예측하고 있다. 그러므로 k=3같이 작은 숫자로 판단하는 것보다 k=50정도로 판단하는 결과가 주변의 이웃들의 label을 더 많이 관찰하고 판달할 수 있기에 더 값이 정확해진다고 판단된다. k=50일 때 나온 값을 가지고 예측이 제대로 되었나 판단해보면

<img width="256" alt="a12" src="https://github.com/nangjjub/Project/assets/169229588/1f562a01-8cf3-4892-b5e3-3b3d9a53789e">

 정확도(Accuracy)는 66.4%정도의 수준으로 아주 높은 정확도는 아니지만 3개의 label중 랜덤으로 하나를 선택하는 33.3%의 확률과 비교해봤을 때 상당히 의미있는 예측률이라고 생각된다. 따라서 방송에 관련된 특성들에 따라서 카테고리를 에측하는 것이 가능하다고 판단된다. 방송을 구성하는 특성들은 현재 사용한 특성말고도 여러 가지가 존재할 수 있다. 예를 들어 시청자의 연령층, 방송인의 성별, 방송을 진행하는 시각(오전, 오후 등) 등 현재 분류한 특성(후원 금액, 최대 시청자 수, 총 방송 시간)보다 더 다양한 요인을 방송 카테고리를 예측하는데 사용할 수 있고 예측률을 더 상승시킬 여지가 남아있다고 생각한다.



(2) Logistic Regression 가중치 결과

<img width="454" alt="a13" src="https://github.com/nangjjub/Project/assets/169229588/f8828613-2d1b-40dd-b2f0-0cf11914e2be">

 주어진 가중치의 상대적인 비교를 통해 토크/캠방 카테고리의 방송인들은 상대적으로 리그오브레전드, 배틀그라운드를 방송하는 사람들에 비해 많은 후원 금액을 받음을 판단할 수 있다. 그에 반해 리그오브레전드 방송을 하는 방송인들은 후원 금액은 적지만 비교적 많은 시청자가 방송을 시청함을 판단할 수 있었다. 배틀그라운드 방송인들은 다른 두 카테고리와 다르게 방송 시간에 영향을 크게 받는데 이로써 배틀그라운드 방송인들은 오랫동안 방송을 한다고 생각할 수 있다.

(3) 시각화 분석
 그래프에서 한글을 지원하지 않아 한글로 된 부분을 영어로 수정했다.
["카테고리", "별풍선 개수", "최대동시시청자수", "누적시청자수", "총 방송시간", "총 방송시간(분)"]
["categori", "donated", "maxViewer", "accumulateViewer", "Total time", "Total time(minute)"]
혼돈이 없도록 수정된 영어 이름과 이전 한글 이름을 첨부했다.

<img width="536" alt="a14" src="https://github.com/nangjjub/Project/assets/169229588/f0000a4a-4622-4962-a8f0-0619a08ad0a4">

 위의 그래프는 방송 시간을 x축, 후원된 별풍선의 개수를 y축으로 구성해 카테고리 별로 색깔을 다르게 산점도를 그려본 결과 화면이다. 각각 빨간색은 토크/캠방, 초록색은 리그오브레전드, 파란색은 배틀그라운드이다. 빨간색 점의 y값이 다른 색깔에 비해서 큰 모습을 보이고 있다. Logistic Regression 가중치로 예측했던 것과 동일하게 토크/캠방이 상대적으로 많은 후원을 받고 있음을 확인할 수 있었다. 또한 파란색 점은 x 값이 다른 두 색깔과 비교해서 큰 모습을 보이는데 이 역시 가중치로 확인했던대로 배틀그라운드 카테고리는 다른 카테고리보다 방송 시간이 많음을 확인할 수 있었다. 각 항목 별로 더 자세하게 판단하기 위해 boxplot을 추가로 그려봤다.

<img width="296" alt="a15" src="https://github.com/nangjjub/Project/assets/169229588/f77e0cf4-ca02-4154-853c-e2cdf2acdf8d">

<img width="310" alt="a16" src="https://github.com/nangjjub/Project/assets/169229588/361c62a1-a60f-4f71-a385-0e7d42d19f49">

<img width="304" alt="a17" src="https://github.com/nangjjub/Project/assets/169229588/30614030-8f47-427a-9c27-24288271220f">

 후원된 금액(별풍선의 개수), 최대 시청자 수, 총 방송시간(분)에 대해서 순서대로 그린 boxplot 결과 화면이다. 첫 번째 그래프를 통해 토크/캠방이 확실히 많은 금액을 후원받고 있음을 한눈에 확인할 수 있었다. 그에 반해 최대시청자수는 굉장히 큰 이상값들이 존재하여 한눈에 판단하기에는 살짝 부족했다. 세 번째로 총 방송시간 값을 y축으로 갖는 그래프에선 배틀그라운드 항목이 대체적으로 방송 시간이 타 항목보다 많음을 확인할 수 있었다.


 ### [4] 최종 결론
 처음 품었던 의문인 “방송의 특성을 가지고 방송의 카테고리를 분류할 수 있을까?“라는 의문을 해결할 모델을 구현해봤다. 주어진 특성 3개를 활용하여 예측하는 분류 모델을 만들었는데 그 결과 66.4% 정도로 예측 가능하다는 결과를 도출해냈다. 66.4%는 비교적 잘 맞춘다고 볼 순 없지만 상당히 의미있다고 생각된다. 3개중 한 개를 랜덤으로 맞추는 상황이 33.3%임을 감안했을 때 만들어낸 분류 모형은 카테고리를 예측할 수 있었다고 말할 수 있다. 현재 세 개의 특성(후원된 금액, 최대 시청자 수, 총 방송 시간)만을 가지고 모델을 만들어 봤는데 방송에는 세 가지 요인뿐만 아니라 더 많은 특성들이 존재하기에 추가적으로 특성을 추가하여 분류 모델을 구성한다면 더욱 높은 정확도로 카테고리를 예측할 수 있다고 생각한다.
 또한 Logistic Regression을 통해서 특성별 가중치를 확인해봤다. 그 결과 카테고리별로 특성의 중요도를 상대적으로 판단할 수 있었다. 토크/캠방은 타 카테고리보다 상대적으로 많은 후원을 받고 리그오브레전드 카테고리는 최대 시청자 수가 상대적으로 많은 모습을 확인할 수 있었다. 총 방송 시간이 가장 큰 양의 영향을 미친 항목은 배틀그라운드임도 확인했다. 또한 각각의 카테고리별로 그래프를 그려봄으로써 구현 결과로 나타나는 가중치가 제대로 측정되었음을 설명할 수 있었다.
 위의 결과들을 토대로 방송 카테고리와의 연관이 직관적으로 떠오르지 않는 방송의 특성들을 가지고 카테고리를 분류할 수 있다는 결론을 얻었고 각 카테고리 별로 특성의 가중치 정도가 다름을 확인할 수 있었다.
