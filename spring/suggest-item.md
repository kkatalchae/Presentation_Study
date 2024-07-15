# 추천 알고리즘

> 추천 시스템에 대한 간단한 이론과 실습

0. 목차
1. 이론
    1. 추천 시스템이란 
    2. 추천 알고리즘이란
       1. 필요한 데이터
       2. Content-Based Filtering
       3. Collaborative Filtering
2. 실습
    1. 아파치 머하웃
    2. 구현
3. ref.

======================================================================

## 1. 이론

### 1. 추천 시스템이란

- 사용자가 관심 있을 만한 아이템을 제시하는 것
- 사용자 행동의 관찰 결과에 따라 연관성이 높은 콘텐츠를 제공해야 함.
- 플랫폼의 추천 검색어, 추천 상품, 개인화 추천
- 서비스의 체류 시간을 늘리고, 사용자의 방문이 구매행위로 이어지는 중요한 역할을 함
- ex. 쿠팡 메인페이지의 [내가 본 상품의 연관 상품], [좋아하실만한 브랜드 상품] ...  

### 2. 추천 알고리즘이란

#### 1. 필요한 데이터
1. 속성(카테고리, 아이템 정보, 내용 등)이 반영된 아이템 정보
2. 나이 범위, 성, 사는 곳, 친구등의 사용자 프로필 정보
3. 아이템에 대한 평점, 관심 태그, 북마크, 클릭 등 사용자의 상호작용 정보


#### 2. Content-Based Filtering: 콘텐츠 기반 필터링, CB

> 어제는 가나 초콜렛, 내일은 ABC 초콜렛 
>
콘텐츠 기반 필터링은(이하 CB), 사용자가 소비한 아이템에 대해 아이템의 내용이 비슷하거나 특별한 관계가 있는 다른 아이템을 추천하는 방법.  

- 아이템의 내용이란, 아이템을 표한할 수 있는 데이터(_카테고리, 아이템의 이름, 이미지.._)를 지칭함
- 아이템이 유사한지 확인하려면, 아이템의 비슷한 정보를 수치로 계산할 수 있어야 함
- 보통 유사도 계산을 위해선 아이템을 벡터 형태로 표현하고 벡터간의 유사도 계산 방법을 활용
- 아이템 벡터는 벡터 유사도 측정 방식을 통해 유사도를 측정하며, 이를 바탕으로 최근 소비한 아이템과 유사한 아이템을 추천함  


- 아이템 정보만 있으면 추천이 가능하기 때문에, 소비 이력이 없는 새로운 아이템에 대한 추천이 바로 가능
- 충분히 소비이력이 쌓인 아이템에 대해선 CF 보다 추천 성능이 밀림
- 대개, 추천 대상 아이템이 빠르게 바뀌거나 소비 이력이 적은 아이템에 대해 CF를 보완하는 용도로 사용

#### 3. Collaborative filtering: 협업 필터링, CF
> 치맥, 피맥... ,  삼겹살엔 소주? 
>
협업 필터링(이하 CF)은 유저-아이템간 상호 작용하는 데이터를 활용해 추천하는 방법.  
구매 패턴이 비슷한 사용자를 한 집단으로 보며, 그 집단에 속한 소비자들의 취향을 추천하는 방식
- 다른 사용자의 아이템 소비 이력을 활용함
- 방식에 따라 User Based CF(UBCF), Item Based CF(IBCF)로 나뉨
- User Based CF는 패턴이 비슷한 사용자를 기반으로 상품을 추천함
  - 나와 비슷한 취향을 가진 고객이 본 다른 영화 
  - -> [환승연애] 본 고객은 대부분 [나는솔로]도 보았음
- Item Based CF는 상품을 기반으로 연관성이 있는 상품을 추천함
  - 다른 고객이 함께 구매한 상품
  - -> 삼겹살을 구매한 고객들은 소주도 함께 구매하는 경향을 보였음
  - 보통 웹사이트에서는, 가입 유저의 수가 보유한 컨텐츠의 수보다 비교할 수 없을 정도로 많음  
  따라서 아이템 기반 협업필터링이 유저 기반 협업 필터링에 비해 확장성 및 연산 효율성 측면에서 유리함.  
    - 비슷한 유저를 찾으려면 (User Based CF) 유사도 연산을 많이, 매번 수행
    - 비슷한 아이템을 찾으려면 (Item Based CF) 오프라인으로 미리 수행해두고 필요시 결과값만 불러옴
- Cold Start 문제가 있음. 
  - 신상품은 유저가 남긴 데이터가 없기 때문에, 비슷한 아이템을 찾는 연산 과정에 포함될 수 없음.

## 2. 실습 
### 1. Apache Mahout
> 기계학습(머신러닝)용 라이브러리
>   

### 2. 구현 

#### 1.구현 환경 

- jdk 17
- Spring 2.7.6
- maven

#### 2. 구현
```
# 데이터 셋 형태

1,00,1.0
1,01,2.0
1,02,5.0
1,03,5.0
1,04,5.0

2,00,1.0
2,01,2.0
2,05,5.0
2,06,4.5
2,02,5.0

3,01,2.5
3,02,5.0
3,03,4.0
3,04,3.0

4,00,5.0
4,01,5.0
4,02,5.0
4,03,0.0
```
```
<dependency>
	<groupId>org.apache.mahout</groupId>
	<artifactId>mahout-core</artifactId>
	<version>0.9</version>		
</dependency>
```
```kotlin
fun userBased() {
    
    //데이터 모델 생성
    val desktopPath: String = System.getProperty("user.home") + "/Desktop"
    val dataModel = FileDataModel(File(desktopPath + "/word_count.csv"))
    
    //유사도 모델 생성
    val usersimilarity: UserSimilarity = PearsonCorrelationSimilarity(dataModel)

    //모든 유저들로부터 주어진 유저와 특정 임계값을 충족하거나 초과하는 사용자를 이웃으로 선택함 
    val thresholdNeighbor: UserNeighborhood = ThresholdUserNeighborhood(0.8, usersimilarity, dataModel)
    //사용자간의 유사성을 계산하고, 그 유사성이 가장 높은 상위 N명의 사용자를 이웃으로 선택함.
    val nearestNeighbor: NearestNUserNeighborhood = NearestNUserNeighborhood(5, usersimilarity, dataModel)
    
    //선택한 알고리즘과 데이터 모델을 바탕으로 추천기 생성
    val thresRecommender: UserBasedRecommender = GenericUserBasedRecommender(dataModel, thresholdNeighbor, usersimilarity)
    val nearestRecommender: UserBasedRecommender = GenericUserBasedRecommender(dataModel, nearestNeighbor, usersimilarity)
    
    val thresRecommendList: List<RecommendedItem> = thresRecommender.recommend(2, 2)
    val nearestRecommendList: List<RecommendedItem> = nearestRecommender.recommend(2, 2)

    thresRecommendList.map {
        println("추천 된 아이템 아이디 : ${it.itemID},  value : ${it.value}")
    }
    nearestRecommendList.map {
        println("추천 된 아이템 아이디 : ${it.itemID}, value : ${it.value}")
    }
}

```

```console
>> 결과값 

추천 된 아이템 아이디 : 3, value : 4.5
추천 된 아이템 아이디 : 4, value : 4.0 

```

## 3. ref

1. https://enterprise.kt.com/bt/dxstory/2278.do  
KT Enterprise, "초개인화 시대, 추천 알고리즘 경쟁력이 기업의 미래를 좌우한다" , 23.10.25
2. https://tech.kakao.com/2021/12/27/content-based-filtering-in-kakao/  
kakao Tech, "카카오 AI 추천: 카카오의 콘텐츠 기반 필터링", 21.12.27
3. https://ai-with-sudal-ee.tistory.com/7  
수달이가 알려주는 머신러닝, "추천시스템" 22.2.2
4. 머신러닝 인 자바  
보스티얀 칼루자, 2016
