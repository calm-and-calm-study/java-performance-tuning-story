# Transfer Object Pattern

Transfer Object Pattern 은 우리가 흔히 아는 VO (Value Object) 인 데이터를 전송하기 위한 객체이다.
Dto 와 Vo 의 차이에 대해 여기서 사람들은 많은 혼동을 한다. 둘의 차이는 무엇일까?

## DTO
Dto 란 데이터를 계층 간으로 전달하는 객체이다.

![Screen Shot 2022-08-03 at 8 40 14 PM](https://user-images.githubusercontent.com/41246605/182613086-c177f820-8d3d-481b-a4b7-186c97354441.png)


클라이언트에서 Controller 로 데이터를 받아오는 역할을 하며, Client 에서 Request 로 던지는 Parameter 를 그대로 받아오는 역할을 수행한다. 
따라서 우리의 Service 에서 그대로 사용하는데 있어서 필드의 빈값이 존재할 수도 있고, 데이터 타입이 Service 에 적합하게 내려오지 않을 수 있다.


![Screen Shot 2022-08-03 at 8 47 10 PM](https://user-images.githubusercontent.com/41246605/182612886-60299272-f69f-41b7-b2be-8fde2d0302c8.png)


이에 대한 적합한 예시로, 공공데이터 API (Client) 를 통해 전달 받은 값은 String 타입의 notice_no, notice_sdt, notice_edt 이다. 
Client 자체가 공공기관이기 때문에 우리의 서비스를 위해 필드의 데이터 타입 변경을 요청하기 어려우며, API Spec 은 변경되기 어렵다.
Dto 는 Data Transfer 말 그대로, Client 에 대한 Request 를 받아오고, 우리의 서비스에서 반환하는 Response 값을 Client 에 내려주는 역할을 하는 객체라고 볼 수 있다.

이 때 Dto 는 Client 와 Server 간 Request 와 Response 를 전달하는 역할을 제외한 다른 로직을 수행해서는 안된다.


### 여기서 주의할 점은 Dto 에 대해 Setter 의 남발로 가변객체로 만들어 버리는 점이다.

만일 Client 에 받아온 데이터가 우리의 로직 안에서 아무도 모르게 값이 변경되어버린다면, 
우리는 Client 에서 Request 에서 제공받은 데이터와 로직 안에서 Dto 객체가 들고있는 값이 변경되었는지 일일이 Debugging 을 하면서 찾아볼 수 밖에 없다.
우리는 에러를 고치기 위해 수많은 시간을 Dto 가 들고 있는 값의 변경 시점을 찾는데에 할애하고 있을지도 모른다.
따라서 Dto 는 불변객체로 사용하여 데이터의 불변성을 지켜주는 것이 좋다.


<br />
그러면 다들 이쯤에서 의문이 들 것이다.
로직 상에서 객체가 들고 있는 값이 변하는 일이 허다한데, Dto 를 불변객체로 만들어버려도 되는 거야?


![Screen Shot 2022-08-03 at 8 47 10 PM](https://user-images.githubusercontent.com/41246605/182612886-60299272-f69f-41b7-b2be-8fde2d0302c8.png)

앞서 보여줬던 예시에서도 공지사항 날짜 데이터를 String 값으로 전달해주는데, 
우리 서비스에서는 해당 공지사항 날짜를 LocalDate 타입으로 사용하는 Needs 가 있었다. 그러면 이 작업은 누가해야 할까?


## VO (Value Object)
VO 는 값 그 자체를 나타내는 객체이다. 이 객체 또한 불변객체인데, VO의 특징 중 하나는 오로지 생성자를 통해서만 데이터를 주입 받을 수 있으며, 
객체의 정보가 변경되지 않음을 보장하는 객체이다.

즉, 우리의 서비스 로직에서 사용되는 데이터 값을 나타내는 객체라 할 수 있다.

Client 의 Request 에 전달 받은 값들을 우리의 서비스에 맞게 Convert 하여 값을 가질 수 있는 객체이다.

VO 를 통해 우리는 String 타입의 notice_sdt, notice_edt 를 notice_start_date , notice_end_date 의 LocalDate 타입으로 데이터 값을 가질 수 있게 되었다.


## VO 와 JPA 의 Entity 의 차이점은 무엇일까?

정확히 말하자면 VO 는 우리 서비스에서 값 그 자체를 나타내는 객체로서의 역할만을 수행하는 것이 주된 관심사라 할 수 있겠다.

그리고 JPA 의 Entity 는 실제 DB 의 테이블과 매핑되는 객체로서, 우리 서비스에서 행하는 Business 로직 중 데이터의 변경을 동반하는 역할을 수행하는 객체라 할 수 있겠다.

![TrasferObject drawio](https://user-images.githubusercontent.com/41246605/182613792-ad803569-1fa9-45e4-8e0e-593e7b231754.png)

위의 그림과 같이, VO 는 Entity 클래스 객체 뿐만 아니라 로직을 수행하는 여러 객체에게 우리 서비스에서의 데이터를 제공하는 역할로 볼 수 있겠다.


<br/>
이렇게 객체의 역할들을 나눈 절대적 기준이 있다. 

## 바로 객체의 관심사는 오로지 생성 이후에 있지, 생성 이전에 있는 일은 관심을 두지 않는다.

DTO 는 Client 의 Request 값을 그대로 전달하는 역할만 수행하면 되고, VO 는 DTO 에서 전달받은 값을 우리 서비스의 데이터에 맞게 Convert 하여 값 자체를 나타내는 역할만 수행하면 된다. 그리고 이후의 다른 객체들은 VO 를 인자값으로 받아 주생성자에서 그대로 할당만 하여 각 객체의 역할에만 집중할 수 있는 것을 의미한다.

이러한 관심사의 분리가 일어나지 않으면, DTO 에서 주었던 String 타입의 notice_sdt, notice_edt 는 이 데이터를 사용하는 모든 객체의 생성자에서 convert 로직을 각각 수행하고 있을 것이고, 우리는 해당 객체들이 역할을 수행할 때 얘네들이 String 타입의 notice_sdt, notice_edt 값을 LocalDate 타입으로 변환했는지 불을 키고 뒤져봐야 할 것이다.

이러한 디자인 패턴으로 짜야

### 흠. Client 에서 받은 Request, Response 는 DTO 를 통해 알 수 있군

### 흠. 우리 서비스에서는 데이터를 VO 의 형태로 쓰는구만

### 흠, Entity는 (각각의 객체) 는 이러한 비즈니스 로직을 수행하고, 이렇게 데이터를 변경하는구만.

바로. 바로 이해가 빠르며, 가독성이 좋고, 유지보수가 좋으며, 관심사의 분리로 추상화에도 용이하다.

### 보통은 Client 와 Server 를 한 회사에서 관리한다고 하였을 때, DTO 와 VO 의 데이터 차이가 발생하지 않아. VO 의 역할이 생략되는 부분이 많다.

그러나, 위의 공공 API 처럼 외부 API 를 사용하는 경우에는 Client 와 우리 서비스의 데이터 타입이 일치하는 경우가 없기 때문에, 위처럼 역할을 수행하면 되겠다.
