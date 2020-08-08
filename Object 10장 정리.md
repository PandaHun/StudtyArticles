# 10. 상속과 코드 재사용

- 객체지향 프로그래밍의 장점 중 하나: **코드 재사용이 용이하다.**
- 상속: 가장 대표적인 클래스 재사용 방법
- 합성: 새로운 클래스의 인스턴스 안에 기존 클래스의 인스턴스를 포함 시키는 방법

## 01. 상속과 중복 코드

### DRY 원칙

- 중복은 변경을 방해한다.
  - 이것이 중복을 제거해야 하는 이유
  - *우리는 변경에 예민해야 한다.*
- 코드를 수정하는데 필요한 노력을 몇 배로 증가시킨다.
- 중복의 기준은 변경
  - 요구사항 변경에 있어 두 코드가 함께 수정한다면, 중복
- **Don't Repeat Yourself(DRY)**

### 중복과 변경

[전화 요금 계산 어플리케이션]

- 통화 시간을 단위 시간당 요금으로 나누기.
- "10초당 5원" 요금제

<detail>

​	<summary>코드</summary>

```java
//개별 통화 기간을 저장하는 클래스
public class Call {
	private LocalDateTime from;
	private LocalDateTime to;

	public Call(LocalDatetime from, LocalDateTime to) {
		this.from = from;
		this.to = to;
	}

	public Duration getDuration() {
		return Duration.between(from, to);
	}
}
//Call을 관리할 Phone
public class Phone {
	private Money amount;
	private Duration seconds;
	private List<Call> calls = new ArrayList<>();
	
	public Phone(Money amount, Duration seconds) {
		this.amount = amount;
		this.seconds = seconds;
	}
	
	public void call(Call call) {
		calls.add(call);
	}

	public Money calculateFee() {
		Money result = Money.zero;
		for(Call call : calls) {
			result = result.plus(amount.times(call.getDuration().getSeconds() / seconds.getSeconds()));
		}
		return result;
	}
}
```

</detail>

- 만약 요금제가 "심야 할인 요금제"가 추가되면?

  - 가장 빠른 방법은 코드를 복사해 `NightlyDiscountPhone`을 만든다.

    <detail>

    ​	<summary>코드</summary>

    ```java
    public class NightlyDiscountPhone {
    	private static final int LAET_NIGHT_HOUR 22;
     
    	private Money nightlyAmount;
    	private Monde regularAmount;
    	private Duration seconds;
    	private List<Call> calls = new ArrayList<>();
    	
    	public NightlyDiscountPhone(Money nightlyAmount, Money regularAmount, Duration seconds) {
    		this.nightlyAmount = nightlyAmount;
    		this.regularAmount = regularAmount;
    		this.seconds = seconds;
    	}
    	
    	public void call(Call call) {
    		calls.add(call);
    	}
    
    	public Money calculateFee() {
    		Money result = Money.zero;
    		for(Call call : calls) {
    			if (call.getFrom().getHour() >= LATE_NIGHT_HOUR) {
    				result = result.plus(
    					nightlyAmount.times(call.getDuration().getSeconds() / seconds.getSeconds()));
    			} else {
    				result = result.plus(
    					regular.times(call.getDuration().getSeconds() / seconds.getSeconds()));
    			}
    		}
    		return result;
    	}
    }	
    ```

    </detail>

- 코드를 복붙해서 만들면 되지만, 시한폭탄 같은 존재

- 만약 추가되는 요금에 세금을 부과한다면???

  <detail>

  ​	<summary>코드</summary>

  ```java
  public class Phone {
  		...
      private double taxRate;
  
      public Phone(Money amount, Duration seconds, double taxRate) {
  				...
          this.taxRate = taxRate;
      }
  
      public Money calculateFee() {
          Money result = Money.ZERO;
          for(Call call : calls) {
              result = result.plus(amount.times(call.getDuration().getSeconds() / seconds.getSeconds()));
          }
          return result.plus(result.times(taxRate));
      }
  }
  
  public class NightlyDiscountPhone {
  		...
      private double taxRate;
  
      public NightlyDiscountPhone(Money nightlyAmount, Money regularAmount, Duration seconds, double taxRate) {
  				...
          this.taxRate = taxRate;
      }
  
      public Money calculateFee() {
          Money result = Money.ZERO;
          for(Call call : calls) {
              if (call.getFrom().getHour() >= LATE_NIGHT_HOUR) {
                  result = result.plus(nightlyAmount.times(call.getDuration().getSeconds() / seconds.getSeconds()));
              } else {
                  result = result.plus(regularAmount.times(call.getDuration().getSeconds() / seconds.getSeconds()));
              }
          }
          return result.minus(result.times(taxRate));
      }
  }
  ```

  </detail>

  - 두 클래스 모두 수정해야 한다.
  - 이 때 어느 코드가 중복이고 파악하는 일이 쉽지 않다.

- 새로운 중복 코드가 늘어날 수록 변경에 취약하고 버그가 발생할 가능성이 높다.

- 이를 해결 하기 위해 타입 코드를 추가한다.

  <detail>

  ​	<summary>코드</summary>

  ```java
  public class Phone {
      private static final int LATE_NIGHT_HOUR = 22;
      enum PhoneType { REGULAR, NIGHTLY }
  
      private PhoneType type;
  
      private Money amount;
      private Money regularAmount;
      private Money nightlyAmount;
      private Duration seconds;
      private List<Call> calls = new ArrayList<>();
  
      public Phone(Money amount, Duration seconds) {
          this(PhoneType.REGULAR, amount, Money.ZERO, Money.ZERO, seconds);
      }
  
      public Phone(Money nightlyAmount, Money regularAmount,
                   Duration seconds) {
          this(PhoneType.NIGHTLY, Money.ZERO, nightlyAmount, regularAmount,
                  seconds);
      }
  
      public Phone(PhoneType type, Money amount, Money nightlyAmount,
                   Money regularAmount, Duration seconds) {
          this.type = type;
          this.amount = amount;
          this.regularAmount = regularAmount;
          this.nightlyAmount = nightlyAmount;
          this.seconds = seconds;
      }
  
      public Money calculateFee() {
          Money result = Money.ZERO;
          for(Call call : calls) {
              if (type == PhoneType.REGULAR) {
                  result = result.plus(amount.times(call.getDuration().getSeconds() / seconds.getSeconds()));
              } else {
                  if (call.getFrom().getHour() >= LATE_NIGHT_HOUR) {
                      result = result.plus(nightlyAmount.times(call.getDuration().getSeconds() / seconds.getSeconds()));
                  } else {
                      result = result.plus(regularAmount.times(call.getDuration().getSeconds() / seconds.getSeconds()));
                  }
              }
          }
          return result;
      }
  }
  ```

  </detail>

  - 하지만 낮은 응집도와 높은 결합도를 야기한다.

### 상속을 이용해서 중복 코드 제거하기

- 기존의 `Phone`과 거의 유사한 `NightlyPhone` 코드.

  <detail>

  ​	<summary>코드</summary>

  ```java
  public class NightlyDiscountPhone extends Phone {
      private static final int LATE_NIGHT_HOUR = 22;
  
      private Money nightlyAmount;
  
      public NightlyDiscountPhone(Money nightlyAmount, Money regularAmount, Duration seconds) {
          super(regularAmount, seconds);
          this.nightlyAmount = nightlyAmount;
      }
  
      @Override
      public Money calculateFee() {
          // 부모클래스의 calculateFee 호출
          Money result = super.calculateFee();
          Money nightlyFee = Money.ZERO;
          for(Call call : getCalls()) {
              if (call.getFrom().getHour() >= LATE_NIGHT_HOUR) {
                  nightlyFee = nightlyFee.plus(
                          getAmount().minus(nightlyAmount).times(
                                  call.getDuration().getSeconds() / getSeconds().getSeconds()));
              }
          }
          return result.minus(nightlyFee);
      }
  }
  ```

  </detail>

- 다만 개발자의 가정을 이해해야 한다.

  - 그 가정이 코드를 이해하기 어렵게, 직관에도 어긋나게 만든다.

- 자식 클래스는 부모 클래스에 대한 정확한 이해를 해야한다.

  - 즉 지식이 많다.
  - **결합도가 높다**

### 강하게 결합된 Phone과 NightlyDiscountPhone

- 다시 세금이 부과된다면?

  <detail>

  ​	<summary>코드</summary>

  ```java
  public class NightlyDiscountPhone extends Phone {
  		...
      public NightlyDiscountPhone(Money nightlyAmount, Money regularAmount, Duration seconds, double taxRate) {
          super(regularAmount, seconds, taxRate);
          this.nightlyAmount = nightlyAmount;
      }
  
      @Override
      public Money calculateFee() {
          // 부모클래스의 calculateFee() 호출
          Money result = super.calculateFee();
          Money nightlyFee = Money.ZERO;
          for(Call call : getCalls()) {
              if (call.getFrom().getHour() >= LATE_NIGHT_HOUR) {
                  nightlyFee = nightlyFee.plus(
                          getAmount().minus(nightlyAmount).times(
                                  call.getDuration().getSeconds() / getSeconds().getSeconds()));
              }
          }
          return result.minus(nightlyFee.plus(nightlyFee.times(getTaxRate())));
      }
  ```

  </detail>

  - 상속의 목적은 `Phone`의 코드를 재사용하고 중복 코드를 제거하기 위해서
  - 하지만 로직을 추가하기 위해 새로운 중복 코드를 만들어야 한다.

- 자식 클래스에서 `super`를 이용한다면 부모-자식이 강하게 결합된다.

  - 상속 경고 1

## 02. 취약한 기반 클래스 문제

- 강한 결합도로 인해 자식 클래스와 부모 클래스가 엮인다.
- 이를 **취약한 기반 클래스 문제**라 한다.
  - 캡슐화를 약화시키고 결합도를 높인다.
  - 상속이 위험 하면서 피해야 하는 첫번째 이유
- 상속이 추가될 수록 전체 시스템의 결합도가 높아진다.
- 객체지향의 기반은 캡슐화를 통한 변경의 통제

### 불필요한 인터페이스 상속 문제

- 초기 자바의 `Properties`, `Stack`
- 초기 자바의 `Stack`은 `Vector`를 상속 받았다.
  - 부모의 `get, add, remove`를 자식이 사용함으로써 `stack`의 규칙을 위반.
- 초기 자바의 `Properties`는 `HashTable`을 상속 받았다.
  - 부모가 `Object`를 받고, `Propertise`에는 검증이 없었기에 아무 값이나 들어갔다.
- 부모 클래스의 메서드가 자식 클래스의 내부 규칙을 깰 수 있다.
  - 상속 경고 2

### 메서드 오버라이딩의 오작용 문제

- `HashSet`에 강하게 결합된 `InstrumentedHashSet`

  - 요소의 수를 셀 수 있는 기능을 추가한 클래스

    <detail>

    ​	<summary>코드</summary>

    ```java
    public class InstrumentedHashSet<E> extends HashSet<E> {
        private int addCount = 0;
    
        public InstrumentedHashSet() {
        }
    
        public InstrumentedHashSet(int initCap, float loadFactor) {
            super(initCap, loadFactor);
        }
    
        @Override
        public boolean add(E e) {
            addCount++;
            return super.add(e);
        }
    
        @Override
        public boolean addAll(Collection<? extends E> c) {
            addCount += c.size();
            return super.addAll(c);
        }
    }	
    ```

    </detail>

  - 3개의 값을 집어 넣는다면?

    - 결과는 6
    - 자신과 부모가 같은 동작을 하기 때문에

    <detail>

    ​	<summary>개선 코드</summary>

    ```java
    public class InstrumentedHashSet<E> extends HashSet<E> {
    		...
        @Override
        public boolean addAll(Collection<? extends E> c) {
            boolean modified = false;
            for (E e : c)
                if (add(e))
                    modified = true;
            return modified;
        }
    
        public int getAddCount() {
            return addCount;
        }
    }
    ```

    </detail>

  - 자식 클래스가 부모 클래스의 메서드를 오버라이딩 할 경우 부모 클래스의 메서드를 사용 방법에 자식 클래스가 결합될 수 있다.

    - 상속 경고 3

  - 상속은 코드 재사용을 위해 캡슐화를 희생한다.

    - 완벽한 캡슐화를 원한다면 재사용을 포기하거나 상속 이외의 다른 방법을 고안해야 한다.

### 부모 클래스와 자식 클래스의 동시 수정 문제

- 음악 목록을 추가할 수 있는 플레이리스트

  <detail>

  ​	<summary>코드</summary>

  ```java
  public class Song {
      private String singer;
      private String title;
  
      public Song(String singer, String title) {
          this.singer = singer;
          this.title = title;
      }
  
      public String getSinger() {
          return singer;
      }
  
      public String getTitle() {
          return title;
      }
  }
  
  public class Playlist {
      private List<Song> tracks = new ArrayList<>();
  
      public void append(Song song) {
          getTracks().add(song);
      }
  
      public List<Song> getTracks() {
          return tracks;
      }
  }
  ```

  </detail>

  - 삭제 기능을 추가한 `PersonalPlaylist`

    - 상속을 통해 코드를 재사용하는 방법이 제일 빠르다

    <detail>

    ​	<summary>코드</summary>

    ```java
    public class PersonalPlaylist extends Playlist {
        public void remove(Song song) {
            getTracks().remove(song);
        }
    }	
    ```

    </detail>

- 만약 요구사항이 변경돼서 목록 뿐 아니라, 가수별 노래의 제목도 관리한다면?

  <detail>

  ​	<summary>코드</summary>

  ```java
  public class Playlist {
      private List<Song> tracks = new ArrayList<>();
      private Map<String, String> singers = new HashMap<>();
  
      public void append(Song song) {
          tracks.add(song);
          singers.put(song.getSinger(), song.getTitle());
      }
  
      public List<Song> getTracks() {
          return tracks;
      }
  
      public Map<String, String> getSingers() {
          return singers;
      }
  }
  ```

  </detail>

  - 완벽한 수정을 위해 `PersonalPlaylis`도 수정해야 한다.

    <detail>

    ​	<summary>코드</summary>

    ```java
    public class PersonalPlaylist extends Playlist {
        public void remove(Song song) {
            getTracks().remove(song);
            getSingers().remove(song.getSinger());
        }
    }
    ```

    </detail>

- 클래스를 상속하면 결합도로 인해 자식 클래스와 부모 클래스의 구현을 영원히 변경하지 않거나, 동시에 변경하거나 둘 중 하나를 선택할 수 밖에 없다.

  - 상속 경고 4

## 03. Phone 다시 살펴보기

### 추상화에 의존하자

- 자식 클래스를 부모가 아닌 추상화에 의존하게 만들자.
  - 정확히는 부모 자식 모두 추상화에 의존하게
- 상속을 도입할 때 따르는 두가지 원칙
  1. 두 메서드가 유사하게 보인다면, 차이점을 메서드로 추출하라. 메서드 추출을 통해 두 메서드를 동일한 형태로 보이도록 만들 수 있다.
  2. 부모 클래스의 코드를 하위로 내리지 말고, 자식 클래스의 코드를 상위로 올려라. 자식 클래스의 추상적인 메서드를 부모 클래스로 올리는 것이 재사용성, 응집도 측면에서 더 좋다.

### 차이를 메서드로 추출하라

- "변하는 것으로부터 변하지 않는 것을 분리하라", "변하는 부분을 찾고 이를 캡슐화 하라"

- 예제에서 다른 것은 요금 계산 로직

  <detail>

  ​	<summary>코드</summary>

  ```java
  public class Phone {
  		...
  	public Money calculateFee() {
  		Money result = Money.zero;
  		for(Call call : calls) {
  			result = result.plus(calculateCallFee(call));
  		}
  		return result.plus(result.times(taxRate));	
  	}
  
  	private Money calculateCallFee(Call call) {
  		return amout.times(call.getDuration().getSeconds() / seconds.getSeconds());
  	}
  }
  
  public class NightlyDiscountPhone extends Phone {
  		...
      private Money calculateCallFee(Call call) {
          if (call.getFrom().getHour() >= LATE_NIGHT_HOUR) {
              return nightlyAmount.times(call.getDuration().getSeconds() / seconds.getSeconds());
          } else {
              return regularAmount.times(call.getDuration().getSeconds() / seconds.getSeconds());
          }
      }
  }
  ```

  ​	

  </detail>

### 중복 코드를 부모 클래스로 올려라

- 추상화를 의존하게 할 것이므로, 추상 클래스로 작성

  <detail>

  ​	<summary>코드</summary>

  ```java
  public abstract class AbstractPhone {
      private List<Call> calls = new ArrayList<>();
  
      public Money calculateFee() {
          Money result = Money.ZERO;
          for(Call call : calls) {
              result = result.plus(calculateCallFee(call));
          }
          return result;
      }
  
      abstract protected Money calculateCallFee(Call call);
  }
  
  public class Phone extends AbstractPhone {
      private Money amount;
      private Duration seconds;
  
      public Phone(Money amount, Duration seconds) {
          this.amount = amount;
          this.seconds = seconds;
      }
  
      @Override
      protected Money calculateCallFee(Call call) {
          return amount.times(call.getDuration().getSeconds() / seconds.getSeconds());
      }
  }
  
  public class NightlyDiscountPhone extends AbstractPhone {
      private static final int LATE_NIGHT_HOUR = 22;
  
      private Money nightlyAmount;
      private Money regularAmount;
      private Duration seconds;
  
      public NightlyDiscountPhone(Money nightlyAmount, Money regularAmount, Duration seconds) {
          this.nightlyAmount = nightlyAmount;
          this.regularAmount = regularAmount;
          this.seconds = seconds;
      }
  
      @Override
      protected Money calculateCallFee(Call call) {
          if (call.getFrom().getHour() >= LATE_NIGHT_HOUR) {
              return nightlyAmount.times(call.getDuration().getSeconds() / seconds.getSeconds());
          } else {
              return regularAmount.times(call.getDuration().getSeconds() / seconds.getSeconds());
          }
      }
  }	
  ```

  </detail>

- 추상화를 통한 상속 계층을 구성할 수 있다.

### 추상화가 핵심이다

- 각 클래스는 서로 다른 변경의 이유를 갖게 된다.

- 부모 클래스는 자신 내부에 구현된 추상 메서드를 호출하기 때문에 추상화에 의존한다.

  - 요금 계산과 관련된 상위 정책을 구현하는 클래스가 세부 항목에 의존하지 않고 세부 클래스가 상위 정책에 의존하는 의존성 역전도 만족한다.

- 세금 추가하기

  <detail>

  ​	<summary>코드</summary>

  ```java
  public abstract class Phone {
      private double taxRate;
      private List<Call> calls = new ArrayList<>();
  
      public Phone(double taxRate) {
          this.taxRate = taxRate;
      }
  
      public Money calculateFee() {
          Money result = Money.ZERO;
          for(Call call : calls) {
              result = result.plus(calculateCallFee(call));
          }
          return result.plus(result.times(taxRate));
      }
  
      protected abstract Money calculateCallFee(Call call);
  }
  
  public class RegularPhone extends Phone {
      private Money amount;
      private Duration seconds;
  
      public RegularPhone(Money amount, Duration seconds, double taxRate) {
          super(taxRate);
          this.amount = amount;
          this.seconds = seconds;
      }
  
      @Override
      protected Money calculateCallFee(Call call) {
          return amount.times(call.getDuration().getSeconds() / seconds.getSeconds());
      }
  }
  
  public class NightlyDiscountPhone extends Phone {
      private static final int LATE_NIGHT_HOUR = 22;
  
      private Money nightlyAmount;
      private Money regularAmount;
      private Duration seconds;
  
      public NightlyDiscountPhone(Money nightlyAmount, Money regularAmount, Duration seconds, double taxRate) {
          super(taxRate);
          this.nightlyAmount = nightlyAmount;
          this.regularAmount = regularAmount;
          this.seconds = seconds;
      }
  
      @Override
      protected Money calculateCallFee(Call call) {
          if (call.getFrom().getHour() >= LATE_NIGHT_HOUR) {
              return nightlyAmount.times(call.getDuration().getSeconds() / seconds.getSeconds());
          } else {
              return regularAmount.times(call.getDuration().getSeconds() / seconds.getSeconds());
          }
      }
  }
  ```

  ​	

  </detail>

## 04. 차이에 의한 프로그래밍

- 상속을 이용하면 새로운 기능을 쉽고 빠르게 추가할 수 있다.
  - 이런 방식을 **차이에 의한 프로그래밍**
- 중복 코드를 제거하고 코드를 재사용 하는 목적을 갖는다.
  - 중복을 제거: 코드를 재사용 가능한 단위로 분해하고 재구성
  - 코드 재사용: 중복 코드를 제거해서 하나의 모듈로 모은다.
  - 재사용 가능한 코드는 심각한 버그가 존재하지 않는 코드.
- 상속은 강력한 도구지만, 위험하다.
  - 정말 필요한 경우에만 사용하길
  - 합성을 사용하라