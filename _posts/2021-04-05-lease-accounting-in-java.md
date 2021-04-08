---
published: true
layout: single
title: "Lease Accounting in Java"
comments: true
classes: wide
categories:
  - java
tags:
  - [serious, java, finance, jpa]
date: 2021-04-06
last_modified_at: 2021-04-06 
---
 2019년부터 적용한 회계법인 리스부채에 대한 이야기를 한다. 기본적인 원리와 등록예제를 설명한다.
![img](https://1.bp.blogspot.com/-_8agDEnJUqQ/YEeXw6orqPI/AAAAAAAAQZI/4VIhPtsdDYkCzEb3cZJw8Zfw8K6jtWwuQCLcBGAsYHQ/w400-h143/%25EB%258B%25A4%25EC%259D%25B4%25EC%2596%25B4%25EA%25B7%25B8%25EB%259E%25A8.png){: .align-center}

## 리스부채

 'K-IFRS 1116'가 도입되었다. 기존 사용권 자산(Righ-of-use assets, 이하 리스자산)은 비용 지불 분개만 기재하면 끝났다. 앞으로는 자산과 부채를 현재가치 금액으로 분개를 기재해야 한다. 

![Lease Liabilities Journal](https://1.bp.blogspot.com/-crNLxVkwn2k/YEeGtdnOyWI/AAAAAAAAQXw/pQ8o57oO4aUkZKdshXbupdIyfubQLa2cwCLcBGAsYHQ/w640-h132/%25EA%25B7%25B8%25EB%25A6%25BC1.jpg){: .align-center}

 모든 리스자산를 등록하는 것이 아니다. USD 5,000 이하 또는 리스기간이 1년 이내인 리스거래는 비용만 처리해도 된다.

### 효과

 리스효과에 대해서 부정적인 기사들이 많다. 부채나 이자비용이 높아져 회사운영에 나쁜 영향을 준다는 기사들이다. 사실 결과적으로 리스부채는 경제적 득실이 없다. 하지만 관리적인 측면이 크다. 빌린 자산을 비용으로 운영했을 때와 부채로 운영했을 때는 제무제표의 부채비율이 다르기 때문이다.

 부채비율은 재무 건전성을 확인하는 지표다. 이 지표의 정확한 정보제공이 주주들의 의사결정에 영향을 끼치기 때문에 세분화시킬 필요가 있다. 그러므로 투자자는 이 항목을 잘 볼 필요가 있다.

## Java

<img src="https://user-images.githubusercontent.com/22446581/113953564-11024100-9853-11eb-99e8-5ab749f80966.png" alt="다이어그램" style="zoom:25%;" />

#### LeaseContract.class

 리스계약 정보를 가지고 있다. 기간, 이자율, 계약금액, LeaseSchedule의 정보를 가지고 있다. Lease Schedule과 연관관계를 맺기 위해 addSchedule()만 Public으로 선언했다.

#### LeaseSchedule.class

 리스회사에 지급할 계획정보를 가진다. 지급일자, 금액, 리스부채, 이자, 현재가치 정보를 가진다. LeaseSchedule의 각 현재가치는 리스부채와 이자를 계산하는데 중요한 역할을 하기 때문에 정확히 계산해야 한다.

### 계산 방식

![Lease Liabilities Excel](https://1.bp.blogspot.com/-7GCiMLre0io/YEeQ31rQLwI/AAAAAAAAQYY/aQymweLyhog0AsH6-Sd3M0JNfelbqZDgACLcBGAsYHQ/w640-h538/%25EA%25B7%25B8%25EB%25A6%25BC1.jpg){: .align-center}

이번 예제는 통화는 'KRW', 계약기간은 12개월, 월 지급액은 100원, 이자율은 0.195%고 가정한다. 

#### PV

![img](https://1.bp.blogspot.com/-MAKgZFXsxBw/YEeU8H01IiI/AAAAAAAAQY4/le1y1e39Jqg7JX8HSfk9ahby7WX9OnxQACLcBGAsYHQ/s16000/%25EA%25B7%25B8%25EB%25A6%25BC1.jpg){: .align-center}

 현재가치 계산은 [이전 Post](https://thisandthatit.blogspot.com/2021/03/finance-java-pv-calculattion.html)를 참조하자. 각 Schedule의 라인별로 계산한다. PV를 계산할 때 중요한 점이 있다. **바로 지급날짜 순서로 정렬해야 한다.** (금액이 동일하면 상관없다.) 지급 금액과 순서가 달라지면 총 금액이 같더라도 PV는 다르다. 금액의 정확도를 위해서 BigDecimal Class를 사용했다.

```java
BigDecimal calculateSchedulePV(long index) {
this.schedulePV = this.scheduleAmount.divide(
    BigDecimal.valueOf(
        Math.pow(1 + this.contract.getInterestRate() / 100d, index)), 0, RoundingMode.HALF_UP);
return schedulePV;
}
```

#### 이자율

![img](https://1.bp.blogspot.com/-ri2rVnfB_Ak/YEeQodlfkfI/AAAAAAAAQYU/N0k4ziDpvOEM4ZVZrFJ610pq43uWJsPFQCLcBGAsYHQ/w640-h274/%25EB%258B%25A4%25EC%259D%25B4%25EC%2596%25B4%25EA%25B7%25B8%25EB%259E%25A8.png){: .align-center}

 최초 Lease의 이자금액은 '리스부채 잔액 x 이자율'로 시작한다. 리스부채 잔액은 어떻게 구할 것인가? 위 그림을 잘보면 PV와 부채의 합계는 동일하다. 그래서 우리는 리스부채의 잔액을 각 라인별로 계산할 수 있다. 특이한 점은 리스부채 잔액으로 이자를 계산하기 때문에 이자가 점점 줄어드는 것이다.

#### 리스부채 

 지급액 - 이자를 제외하면 나머지 금액이 리스부채가 된다. 

## 결과

[![Lease Liabilities Java Result](https://lh3.googleusercontent.com/-uLeMSpUX2uQ/YEgSyq3kbcI/AAAAAAAAQbY/jrJTFkFxKz8L9OwMQb-TbBYh32mJsdCCQCLcBGAsYHQ/w640-h294/image.png)](https://lh3.googleusercontent.com/-uLeMSpUX2uQ/YEgSyq3kbcI/AAAAAAAAQbY/jrJTFkFxKz8L9OwMQb-TbBYh32mJsdCCQCLcBGAsYHQ/image.png)

## 마치며

 이번 Post는 리스부채에 대한 설명과 등록을 위한 예제를 간략하게 만들었다. 실제로는 외화환차/환산평가, 유동성, 불공제 적용 등 여러가지 업무가 있다. 그 중에서도 등록은 가장 첫번째로 해결해야하는 일이기 때문에 적어보았다. 소스는 [Github](https://github.com/chulgs15/sinabro/tree/master/finance/lease-pv)에 있다.

## 참조

- [https://brunch.co.kr/@erniekim12/20](https://brunch.co.kr/@erniekim12/20)
- [https://grokonez.com/java/how-to-sort-java-list-objects-by-date-property-with-examples](https://grokonez.com/java/how-to-sort-java-list-objects-by-date-property-with-examples)
- [https://jsonobject.tistory.com/466](https://jsonobject.tistory.com/466)
- [https://ko.wikipedia.org/wiki/%ED%98%84%EC%9E%AC%EA%B0%80%EC%B9%98](https://ko.wikipedia.org/wiki/현재가치)
- [https://blog.leocat.kr/notes/2019/02/25/java-rounding](https://blog.leocat.kr/notes/2019/02/25/java-rounding)