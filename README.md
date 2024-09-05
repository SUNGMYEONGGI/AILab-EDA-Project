# Olist Brazilian E-Commerce Data Analysis

## 소개
Olist Store에서 이루어진 주문에 대한 브라질 이커머스 공개 데이터셋입니다. 이 데이터셋에는 2016년부터 2018년까지 브라질의 여러 마켓플레이스에서 이루어진 약10만 건의 주문 정보가 포함되어 있습니다. 주문 상태, 가격, 결제 및 배송 실적부터 고객 위치, 제품 속성, 마지막으로 고객이 작성한 리뷰까지 다양한 차원에서 주문을 확인할 수 있는 것이 특징입니다. 또한 브라질 우편번호를 위도/경도 좌표와 연관시키는 지리적 위치 데이터 세트도 포함됩니다. 

이 데이터는 실제 상업용 데이터로 익명으로 처리되었습니다.

![소개](https://github.com/SUNGMYEONGGI/image/blob/main/Olist%20Introduce.png?raw=true)

## 데이터 스키마
![Data Schema](https://i.imgur.com/HRhd2Y0.png)

## 탐색적 데이터 분석
### 각 주별/도시별 
1. 고객분포도
    ```python
    import plotly.express as px

    # 지역별 고객분포
    fig = px.pie(olist_data, names='customer_region', title='Customer Region Distributions')
    fig.show()
    ```
    ![지역별 고객분포](https://github.com/SUNGMYEONGGI/image/blob/main/%EC%A7%80%EC%97%AD%EB%B3%84%20%EA%B3%A0%EA%B0%9D%EB%B6%84%ED%8F%AC.png?raw=true)

2. 주별 매출액
    ```python
    # 주별 매출액
    olist_data['total_price'] = olist_data['price'] + olist_data['freight_value']

    fig= px.bar(olist_data, x='kor_state', y='total_price', title='Total Sales by State')
    fig.show()
    ```
    ![주별 매출액](https://github.com/SUNGMYEONGGI/image/blob/main/%E1%84%8C%E1%85%AE%E1%84%87%E1%85%A7%E1%86%AF%E1%84%86%E1%85%A2%E1%84%8E%E1%85%AE%E1%86%AF%E1%84%8B%E1%85%A2%E1%86%A8.png?raw=true)
3. 지역별 매출액
    ```python
    # 지역별 매출액
    fig= px.bar(olist_data, x='customer_region', y='total_price', title='Total Sales by Region')
    fig.show()
    ```
    ![지역별 매출액](https://github.com/SUNGMYEONGGI/image/blob/main/%EC%A7%80%EC%97%AD%EB%B3%84%20%EB%A7%A4%EC%B6%9C%EC%95%A1.png?raw=true)
4. 지역별 평균 배송도착 시간
    ```python
    geo_customer_order_data = pd.merge(geo_customer_data, orders_data, on='customer_id')

    # order_state가 delivered인 데이터만 추출
    geo_customer_order_data = geo_customer_order_data[geo_customer_order_data['order_status'] == 'delivered']
    # state별 평균 delivery_time 시각화
    state_delivery_time = geo_customer_order_data.groupby('customer_state')['delivery_time'].mean().reset_index()

    fig = px.bar(state_delivery_time, x='customer_state', y='delivery_time', color='delivery_time', title='State별 평균 배송시간')
    fig.show()
    ```
    ![평균 배송도착일](https://github.com/SUNGMYEONGGI/image/blob/main/%EC%A3%BC%EB%B3%84%20%ED%8F%89%EA%B7%A0%20%EB%B0%B0%EC%86%A1%EC%8B%9C%EA%B0%84.png?raw=true)

> **💡 인사이트 및 알게된 점** 
>
> 인사이트 및 알게된 점인사이트 및 알게된 점인사이트 및 알게된 점인사이트 및 알게된 점인사이트 및 알게된 점인사이트 및 알게된 점인사이트 및 알게된 점인사이트 및 알게된 점인사이트 및 알게된 점

---

### 매출액
1. 연도, 월, 일, 시간대
    ```python
    olist_data['order_purchase_timestamp'] = pd.to_datetime(olist_data['order_purchase_timestamp'])
    olist_data['year'] = olist_data['order_purchase_timestamp'].dt.year
    olist_data['month'] = olist_data['order_purchase_timestamp'].dt.month
    olist_data['day'] = olist_data['order_purchase_timestamp'].dt.day
    olist_data['hour'] = olist_data['order_purchase_timestamp'].dt.strftime('%H:%M:%S')
    ```
    ![연도별 매출액 추이](https://github.com/SUNGMYEONGGI/image/blob/main/%EB%A7%A4%EC%B6%9C%EC%95%A1%20%EC%B6%94%EC%9D%B4.png?raw=true)
    ![월별 매출액 추이](https://github.com/SUNGMYEONGGI/image/blob/main/%EC%9B%94%EB%B3%84%20%EB%A7%A4%EC%B6%9C%EC%95%A1.png?raw=true)
    ![시간대별 매출액 추이](https://github.com/SUNGMYEONGGI/image/blob/main/%EC%8B%9C%EA%B0%84%EB%8C%80%EB%B3%84%20%EB%A7%A4%EC%B6%9C%EC%95%A1.png?raw=true)

> **💡 인사이트 및 알게된 점** 
>
> 인사이트 및 알게된 점인사이트 및 알게된 점인사이트 및 알게된 점인사이트 및 알게된 점인사이트 및 알게된 점인사이트 및 알게된 점인사이트 및 알게된 점인사이트 및 알게된 점인사이트 및 알게된 점

---

### 카테고리별
1. 주문건수
    ```python
    category_df = pd.DataFrame(olist_data['product_category_name_english'].value_counts())
    category_df = category_df.reset_index()
    category_df.columns = ['카테고리', '주문건수']

    fig = px.bar(category_df, x='카테고리', y='주문건수', title='카테고리별 주문 건수')
    fig.show()
    ```
    ![카테고리별 주문건수](https://github.com/SUNGMYEONGGI/image/blob/main/%EC%B9%B4%ED%85%8C%EA%B3%A0%EB%A6%AC%EB%B3%84%20%EC%A3%BC%EB%AC%B8%EA%B1%B4%EC%88%98.png?raw=true)
2. 평균 배송시간
    ![카테고리별 배송시간](https://github.com/SUNGMYEONGGI/image/blob/main/%E1%84%8F%E1%85%A1%E1%84%90%E1%85%A6%E1%84%80%E1%85%A9%E1%84%85%E1%85%B5%E1%84%87%E1%85%A7%E1%86%AF%20%E1%84%87%E1%85%A2%E1%84%89%E1%85%A9%E1%86%BC%E1%84%89%E1%85%B5%E1%84%80%E1%85%A1%E1%86%AB.png?raw=true)

> **💡 인사이트 및 알게된 점** 
>
> 인사이트 및 알게된 점인사이트 및 알게된 점인사이트 및 알게된 점인사이트 및 알게된 점인사이트 및 알게된 점인사이트 및 알게된 점인사이트 및 알게된 점인사이트 및 알게된 점인사이트 및 알게된 점

---

### 상관관계 분석 (구매자와 판매자)
1. 배송시간별(15일 기준) 고객과 판매자 간의 평균거리
    ![배송시간별(15일 기준) 고객과 판매자 간의 평균거리](https://github.com/SUNGMYEONGGI/image/blob/main/%EB%B0%B0%EC%86%A1%20%EC%8B%9C%EA%B0%84%EB%B3%84%20%EA%B3%A0%EA%B0%9D%EA%B3%BC%ED%8C%90%EB%A7%A4%EC%9E%90%20%EA%B0%84%20%ED%8F%89%EA%B7%A0%EA%B1%B0%EB%A6%AC.png?raw=true)

2. 지역별 평균 배송비
    ![지역별 평균 배송비](https://github.com/SUNGMYEONGGI/image/blob/main/%EC%A7%80%EC%97%AD%EB%B3%84%20%ED%8F%89%EA%B7%A0%20%EB%B0%B0%EC%86%A1%EB%B9%84.png?raw=true)
    
3. 상파울루에서 거래한 고객&판매자 위치
    ![상파울루와 거래한 Sellor 위치](https://github.com/SUNGMYEONGGI/image/blob/main/%E1%84%89%E1%85%A1%E1%86%BC%E1%84%91%E1%85%A1%E1%84%8B%E1%85%AE%E1%86%AF%E1%84%85%E1%85%AE%E1%84%8B%E1%85%AA%20%E1%84%80%E1%85%A5%E1%84%85%E1%85%A2%E1%84%92%E1%85%A1%E1%86%AB%20Sellor%20%E1%84%8B%E1%85%B1%E1%84%8E%E1%85%B5.png?raw=true)
    ![상파울루와 거래한 고객과 판매자 위치](https://github.com/SUNGMYEONGGI/image/blob/main/%E1%84%89%E1%85%A1%E1%86%BC%E1%84%91%E1%85%A1%E1%84%8B%E1%85%AE%E1%86%AF%E1%84%85%E1%85%AE%E1%84%8B%E1%85%AA%20%E1%84%80%E1%85%A5%E1%84%85%E1%85%A2%E1%84%92%E1%85%A1%E1%86%AB%20%E1%84%80%E1%85%A9%E1%84%80%E1%85%A2%E1%86%A8%E1%84%80%E1%85%AA%20%E1%84%91%E1%85%A1%E1%86%AB%E1%84%86%E1%85%A2%E1%84%8C%E1%85%A1%20%E1%84%8B%E1%85%B1%E1%84%8E%E1%85%B5.png?raw=true)

> **💡 인사이트 및 알게된 점** 
>
> 인사이트 및 알게된 점인사이트 및 알게된 점인사이트 및 알게된 점인사이트 및 알게된 점인사이트 및 알게된 점인사이트 및 알게된 점인사이트 및 알게된 점인사이트 및 알게된 점인사이트 및 알게된 점

<br>

## 비즈니스 개선전략
### 재구매율 향상 전략
판매자들이 고객의 재구매를 유도하기 위해 어떠한 전략을 취할 수 있을지 분석. 이를 위해 고객의 재구매 패턴, 선호하는 상품 범주, 가격 대비 가치 인식 등 분석
#### 시간대 거래분석
1. 재구매 고객의 제품구매 시간대 시각화
![재구매 고객의 제품구매 시간대](https://raw.githubusercontent.com/SUNGMYEONGGI/image/main/%E1%84%89%E1%85%A1%E1%84%85%E1%85%A1%E1%86%B7%E1%84%83%E1%85%B3%E1%86%AF%E1%84%8B%E1%85%B5%20%E1%84%8B%E1%85%A5%E1%86%AB%E1%84%8C%E1%85%A6%20%E1%84%87%E1%85%A9%E1%84%90%E1%85%A9%E1%86%BC%20%E1%84%8C%E1%85%A6%E1%84%91%E1%85%AE%E1%86%B7%E1%84%8B%E1%85%B3%E1%86%AF%20%E1%84%80%E1%85%AE%E1%84%86%E1%85%A2.png)

2. 낮 시간(9~17시)에 거래된 카테고리 분포
![낮 시간(9~17시)에 거래된 카테고리 분포](https://raw.githubusercontent.com/SUNGMYEONGGI/image/main/%E1%84%82%E1%85%A1%E1%86%BD%20%E1%84%89%E1%85%B5%E1%84%80%E1%85%A1%E1%86%AB(9%E1%84%89%E1%85%B5%7E17%E1%84%89%E1%85%B5)%E1%84%8B%E1%85%A6%20%E1%84%80%E1%85%A5%E1%84%85%E1%85%A2%E1%84%83%E1%85%AC%E1%86%AB%20%E1%84%8F%E1%85%A1%E1%84%90%E1%85%A6%E1%84%80%E1%85%A9%E1%84%85%E1%85%B5.png)

3. 밤 시간(18~23시)에 거래된 카테고리 분포
![밤 시간(18~23시)에 거래된 카테고리 분포](https://raw.githubusercontent.com/SUNGMYEONGGI/image/main/%E1%84%87%E1%85%A1%E1%86%B7%20%E1%84%89%E1%85%B5%E1%84%80%E1%85%A1%E1%86%AB(18%E1%84%89%E1%85%B5%7E23%E1%84%89%E1%85%B5)%E1%84%8B%E1%85%A6%20%E1%84%80%E1%85%A5%E1%84%85%E1%85%A2%E1%84%83%E1%85%AC%E1%86%AB%20%E1%84%8F%E1%85%A1%E1%84%90%E1%85%A6%E1%84%80%E1%85%A9%E1%84%85%E1%85%B5%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%89%E1%85%A5%E1%86%A8.png)

#### 시간대 배송비 분석
1. 낮 시간(9~17시)에 거래된 카테코리별 평균 배송비
![](https://raw.githubusercontent.com/SUNGMYEONGGI/image/main/%E1%84%82%E1%85%A1%E1%86%BD%20%E1%84%89%E1%85%B5%E1%84%80%E1%85%A1%E1%86%AB(9%E1%84%89%E1%85%B5%7E17%E1%84%89%E1%85%B5)%E1%84%8B%E1%85%A6%20%E1%84%80%E1%85%A5%E1%84%85%E1%85%A2%E1%84%83%E1%85%AC%E1%86%AB%20%E1%84%8F%E1%85%A1%E1%84%90%E1%85%A6%E1%84%80%E1%85%A9%E1%84%85%E1%85%B5%20%E1%84%91%E1%85%A7%E1%86%BC%E1%84%80%E1%85%B2%E1%86%AB%20%E1%84%87%E1%85%A2%E1%84%89%E1%85%A9%E1%86%BC%E1%84%87%E1%85%B5%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%89%E1%85%A5%E1%86%A8.png)

2. 밤 시간(18~23시)에 거래된 카테코리별 평균 배송비
![](https://raw.githubusercontent.com/SUNGMYEONGGI/image/main/%E1%84%87%E1%85%A1%E1%86%B7%20%E1%84%89%E1%85%B5%E1%84%80%E1%85%A1%E1%86%AB(18%E1%84%89%E1%85%B5%7E23%E1%84%89%E1%85%B5)%E1%84%8B%E1%85%A6%20%E1%84%80%E1%85%A5%E1%84%85%E1%85%A2%E1%84%83%E1%85%AC%E1%86%AB%20%E1%84%8F%E1%85%A1%E1%84%90%E1%85%A6%E1%84%80%E1%85%A9%E1%84%85%E1%85%B5%20%E1%84%91%E1%85%A7%E1%86%BC%E1%84%80%E1%85%B2%E1%86%AB%20%E1%84%87%E1%85%A2%E1%84%89%E1%85%A9%E1%86%BC%E1%84%87%E1%85%B5%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%89%E1%85%A5%E1%86%A8.png)

> **💡 인사이트 및 알게된 점**
>
> - 재구매 5회이상으로 분석
> ![](https://github.com/SUNGMYEONGGI/image/blob/main/%E1%84%8C%E1%85%A2%E1%84%80%E1%85%AE%E1%84%86%E1%85%A2%E1%84%8B%E1%85%B2%E1%86%AF%20%E1%84%82%E1%85%A9%E1%87%81%E1%84%8B%E1%85%B3%E1%86%AB%20%E1%84%8F%E1%85%A1%E1%84%90%E1%85%A6%E1%84%80%E1%85%A9%E1%84%85%E1%85%B5.png?raw=true)
> - 많은 고객들이 컴퓨터 및 악세사리, 가구 및 장식, 주방 용품 등의 생활용품을 낮과 밤 할것없이 많이 구매 
> - 재구매율과 고객 혹은 판매자의 위치는 크게 중요하지 않음
> - 절반 이상의 경우에서 밤에 거래되는 제품의 배송비가 더 비쌈
> - 사람들이 시간대에 상관없이 e-commerce를 통해 많이 구매하는 제품 카테고리
>   - *컴퓨터 및 액세서리* | *가구 및 장식* | *주방용품* | *침실 및 욕실*

> **💁🏼 전략**
>
> - 9시~17시 사이에 특정 sellor가 많은 트래픽을 받기 위해서는 낮시간에 특가세일 or 쿠폰이벤트가 있으면 좋을 것 같다.