## 컬렉션과 I/O 자세히 알아보기.
***

### 컬렉션 
  
#### 컬렉션 타입
코틀린 컬렉션 타입은 기본적으로 네 가지로 분류할 수 있다. 배열, 이터러블, 시퀀스, 맵이다.  

**이터러블**  
이터러블은 Interable<T> 타입으로 표현되며, 일반적으로 즉시 계산되는 상태가 있는 컬렉션을 표현한다.
상태가 있다는 말은 컬렉션이 원소를 필요할 때 생성하는 제너레이터 함수를 유지하지 않고 원소를 저장한다는 뜻이다.
즉시 계산이라는 말은 나중에 어느 필요한 시점에 원소가 초기화되지 않고 컬렉션을 최초로 생성할 때 초기화된다는 뜻이다.
이터러블 타입 자체는 자바의 이터러블 타입과 상당히 비슷하다. 이터러블 타입은 원소를 순회할 수 있는 interator()
라는 메서드를 제공한다. 코틀린 for 루프에서는 이 메서드를 통해 모든 이터러블 객체를 활용할 수 있다.
```kotlin
val list = listOf("red", "green", "blue")
for(item in list){
    println(item+" ")
}
```
코틀린 Iterator 타입은 기본적으로 자바와 동일하다. 내부에는 이터레이터가 컬렉션의 끝에 도달했는지를 판단하는 
hasNext()와 컬렉션의 다음 원소를 반환하는 next() 메서드가 들어왔다. 유일한 차이는 remove()가 없다는 점인데,
이 메서드는 MutableIterator로 옮겨졌다.  

가변 이터러블의 기본 타입은 MutableIterable로 표현된다. 이 인터페이스의 객체는 MutableIterator 인스턴스를
생성할 수 있다.  
불변 컬렉션 타입에서 유용한 특징으로는 공변성이 있다. 공변성이라는 말은 T가 U의 하위 타입인 경우 Iterable
< T >도 Iterable< U >의 하위 타입이라는 뜻이다. Iterator, Collection, List, Set, Map 등과 같은
컬렉션 관련 타입의 경우에도 이런 공변성이 성립한다.  

**컬렉센, 리스트, 집합**  
이터러블의 하위 분류 중에는 Collection 인터페이스로 표현되는 타입들과 Collection 타입의 하위 타입인 
MutableCollection 인터페이스로 표현되는 타입들이 있다. 이들은 이터러블에 대한 여러 가지 표준적인 구현을 위한
기본 클래스다. Collection을 상속한 클래스는 크게 아래와 같이 분류할 수 있다.
* 리스트는 인덱스를 통한 원소 접근이 가능한, 순서가 정해진 원소 컬렉션이다.
* 집합은 유일한 원소들로 이뤄진 컬렉션이다. 원소의 순서는 구현에 따라 다르다.
  * HashSet은 해기 테이블이 기반인 구현이며, 원소의 해시 코드에 따라 원소 순서가 정해진다.
  * LinkedHashSet은 해시 테이블이 기반이지만 삽입 순서를 유지하기 때문에 원소를 이터레이션하면 집합에 삽인된
    순서대로 원소를 순회할 수 있다.
  * TressSet은 이진 검색 트리가 기반이며, 어떤 비교 규칙에 따라 일관성있는 원소 순서를 제공한다.

JVM 플랫폼에서는 이런 코틀린 인터페이스를 구현하는 구체적인 클래스들이 JDK의 컬렉션 클래스에 해당한다.  

**시퀀스**  

이터러블과 비슷하게 시퀀스도 iterator() 메서드를 제공한다. 이 메서드를 통해 시퀀스의 내용을 순회할 수 있다.
하지만 시퀀스는 지연 계산을 가정하기 때문에 iterator()의 의도가 이터러블과 다르다. 대부분의 시퀀스 구현은 
객체 초기화 시 원소를 초기화하지 않고 요청에 따라 원소를 계산한다. 대부분의 시퀀스 구현은 상태가 없다. 이 말은
지연 계산한 컬렉션 원소 중에 정해진 개수의 원소만 저장한다는 뜻이다. 반면 이터러블은 원소 개수에 비례해 메모리를
사용한다.  
이터러블과 달리 대부분의 시퀀스 구현은 내부적이므로 외부에서 직접 사용할 수 없다.  

**맵**  

맵은 키와 값 쌍으로 이뤄진 집합이다. 여기서 키는 유일해야 한다. 맵자체는 Collection의 하위 타입이 아니지만
맵에 들어있는 원소들을 컬렉션처럼 사용할 수 있다. 구체적인 예를 들면, 맵으로부터 모든 키로 이뤄진 집합을 얻을 수
있고 모든 키-값 쌍으로 이뤄진 집합이나 모든 값으로 이뤄진 컬렉션을 얻을 수도 있다. 키-값 쌍은 Map.Entry와 
MutableMap.MutableEntry 인터페이스로 표현된다.  
맵에는 두 가지 종류의 원소가 들어있기 때문에 맵의 타입은 두 가지 타입을 파라미터로 받는 제네릭 타입이다.
****

#### Comparable과 Comparator
자바처럼 코틀린도 Comparable과 Comparator 타입을 지원하며, 몇몇 컬렉션 연산에 이를 활용한다. 비교 가능
인스턴스는 자연적인 순소를 지원하며, 이런 타입의 인스턴스들은 모두 동일한 타입의 다른 인스턴스와 순소를 비교할 때 
쓸 수 있는 compareTo() 메서드를 포함한다.  

compareTo() 함수의 관례는 자바와 똑같다. 즉 현재 인스턴스가 상대방 인스턴스보다 더 크면 양수, 더 작으면 음수,
같으면 영(0)을 반환한다. compareTo() 구현은 equals() 함수 구현과 서로 의미가 일치해야 한다.  

어떤 클래스를 여러 가지 방법으로 비교해야 하는 경우도 많다. 예를 들어 Person의 컬렉션을 이름이나 나이, 또는
여러 가지 프로퍼티의 조합을 사용해 정렬할 수도 있다. 이런 경우에 대비해 코틀린 라이브러리는 비교기라는 개념을
제공한다. 자바와 마찬가지로 Comparator< T > 클래스는 타입 T의 인스턴스 객체를 두 개 인자로 받는 compare()
함수를 제공한다. 이 함수는 compareTo()와 동일한 관례를 따라 인자로 받은 두 인스턴스를 비교한 결과를 반환한다.
***

#### 컬렉션 생성하기.
2장 코틀린 언어 기초에서 이미 배열 인스턴스를 생성자를 통해 생성하거나 arrayOf() 같은 표준 함수를 통해
생성할 수 있다는 사싫을 배웠다. 또한, 여러 표준 컬렉션 클래스의 인스턴스를 비슷한 방식으로 생성할 수 있다.
예를 들어, 자바와 마찬가지로 ArrayList나 LinkedHashSet 같은 클래스의 인스턴스를 생성자를 호출해 생성할 수 있다.
```kotlin
val list = ArrayList<String>()
list.add("red")
list.add("green")
// [ red, green ]

val set1 = HashSet<Int>()
set1.add(12)
set1.add(21)
set1.add(12)
// [12, 21]

val map = TreeMap<Int, String>()
map[20] = "Twenty"
map[10] = "Ten"
// [10=Ten, 20=Twenty]
```
그리고 arrayOf()와 비슷하게 가변 길이 인자를 받는 함수를 사용해 몇몇 컬렉션 클래스의 인스턴스를 생성할 
수 있다.
* emptyList()/emptySet(): 불변인 빈 리스트/집합 인스턴스를 생성한다.
* listOf()/setOf(): 인자로 제공한 배열에 기반한 불변 리스트/집합 인스턴스를 만든다.
* listOfNotNull(): 넙인 값을 걸러내고 남은 원소들로 이뤄진 새 불변 리스트를 만든다.
* mutableListOf()/mutableSetOf(): 가변 리스트/집합의 디폴트 구현 인스턴스를 만든다.
* arrayListOf(): 새로운 ArrayList를 만든다.
* hashSetOf()/linkedSetOf()/sortedSetOf((): HashSet/LinkedHashSet/TreeSet의 새 인스턴스를 
만든다.

맵을 만들 때도 비슷한 함수를 쓸 수 있다.
* emptyMap(): 빈 불변 맵을 만든다.
* mapOf(): 새 불변 맵을 만든다.
* mutableMao(): 가변 맵의 디폴트 구현 인스턴스를 만든다.
* hashMapOf()/linkedMapOf()/sortedMapOf(): HashMap/LinkedHashMap/TreeMap의 새 인스턴스를
만든다.

앞에서 보여준 맵 함수들은 Pair 객체들로 이뤄진 가변 인자를 받는다는 점에 유의하라. to 중위 연산자를 사용하면
Pair 객체를 쉽게 만들 수 있다.
```kotlin
val emptyMap = emptyMap<int, String>()
//println(emptyMap) // {}
emptyMap[10] = "Ten"  // error

val singletonMap = mapOf(10 to "Ten")
println(singletonMap) // {10=Ten}
//singletonMap.remove("abc")   error

val mutableMap = mutableMapOf(10 to "Ten")
println(mutableMap)  // {10=Ten}
mutableMap[20] = "Twenty"
mutableMap[100] = "Hundred"
mutableMap.remove(10)
println( mutableMap )  // {20=Twenty, 100=Hundred}

val soredMap = sortedMapOf(3 to "three", 1 to "one", 2 to "two")
println(soredMap) // {1=one, 2=two, 3=three}
soredMap[0] = "zero"
println(soredMap)  // {0=zero, 1=one, 2=two, 3=three}
```
또는 Pair를 생성하고 싶지 않다면, 가변 맵을 만들고 set() 메서드나 인덱스 연산자를 사용해 맵에 원소를
추가할 수도 있다.  

원소가 알려진 시퀀스를 만드는 가장 단순한 방법은 sequenceOf() 함수를 사용하는 것이다. 이 함수는 가변 인자를
받는다. 또는 배열, 이터러블, 맵 등의 기존 컬렉션 객체ㄹ에 대해 asSequence() 함수를 호출해서 시퀀스를 얻을 
수도 있다.  

맵에 대해 asSequence()를 호출하면 맵 엔트리의 시퀀스를 얻는다.  

또한 제너레이터 함수를 바탕으로 시퀀스를 만드는 방법도 있다. 두가지 generateSequence() 함수를 사용하면
함수를 사용해 시퀀스를 만들 수 있다. 첫 번째 generateSequence()는 시퀀슬의 다음 원소를 생성해주는 파라미터가
없는 함수를 인자로 받는다. 이 함수가 널을 반환할 때까지 시퀀스 원소 생성이 계속된다.  

두 번째 generateSequence() 함수는 초긱값과 파라미터가 하나인 함수를 인자로 받는 다. 이 함수는 이전
값으로부터 다음 값을 만들어 낸다. 첫 번째 generateSequence()와 마찬가지로 이 경우도 제너레이터 함수가
다음 값으로 널을 반환하면 시퀀스가 끝난다.  

코틀린 1.3부터 특별한 빌더를 사용해 시퀀스를 만드는 방법이 추가됐다. 이때 빌더는 시퀀스 원소 부분 부분 지정한다.
SequenceScope가 수신 객체 타입인 확장 람다를 받는 sequence() 함수를 통해 빌더를 구현할 수 있다.
SequenceScope 타입은 확장 람다 본문 안에서 시퀀스 뒤에 값을 추가할 수 있는 다음과 같은 함수를 제공한다.
* yield(): 원소를 하나 시퀀스에 추가한다.
* yieldAll(): 지정한 이터레이터, 이터러블, 시퀀스에 들어있는 모든 원소를 시퀀스에 추가한다.

sequence()/yield()/yieldAll()로 만들어진 이 시퀀스 빌더는 실제로는 유예 가능 계산이라는 강력한 코틀린
 기능의 예다. 다중 스레드 환경에서는 이 기능이 아주 유용하다.  

마지막으로, 컬렉션 사이의 변환을 처리하는 함수를 설명한다. 예를 들어 배열 원소를 바탕으로 리스트를 만들거나
시퀀스를 집합으로 만들 수 있다.
```kotlin
listOf(1, 2, 3, 4).toSet()  // [1, 2 ,3]

arrayOf("red", "green", "blue").toSortedSet() //  [blue, green, red]

mapOf(1 to "one", 2 to "two", 3 to "three").toList()  // [(1, one), (2, two), (3, three)]

sequenceOf(1 to "one", 2 to "two", 3 to "three").toMap()
// {1=one, 2=two, 3=three}
```
****

#### 기본 컬렉션 연산

모든 컬렉션이 기본으로 지원하는 공통 연산으로는 이터레이션이 있다. 배역, 이터러블, 시퀀스 맵은 iterator()
함수를 지원한다. 이 함수가 반환하는 Iterator 객체를 사용해 컬렉션 원소를 순회할 수는 있지만, 코틀린이 같은
일을 할 수 있는 더 간결한 방법을 제공하므로 실무에서 이터레이터를 사용하는 경우는 드물다.  

iterator() 함수가 있다는 사실은 이터러블 절에서 설명했던 것처럼 모든 컬렉션에 대해 for 루프를 쓸 수 있다는
뜻이다. 다만 map의 iterator() 함수가 반환하는 이터레이터는 Map, Entry 타입의 값을 돌려준다는 사실을
알아두라, 코틀린 맵 원소는 구조 분해를 지원하므로, 다음과 같이 맵 이터레이션을 할 수 있다.
```kotlin
val map = mapOf(1 to "one", 2 to "two", 3 to "three")
for( (key, value) in map){
    println("$key -> $value")
}
```
Map, Entry 타입의 값을 인자로 받는 람다에서도 마찬가지 방식으로 구조 분해를 슬 수 있다.  

컬렉션 타입이 제공하는 기본 기능은 다음과 같아.
* size 프로퍼티는 원소 개수를 돌려준다.
* isEmpty() 함수는 컬렉션에 원소가 없는지 검사한다.
* contains()/containsAll() 함수는 인자로 지정한 원소나 인자로 지정한 컬렉션의 모든 원소가 수신
객체 컬렉션에 들어있는지 검사한다.
****

#### 컬렉션 원소에 접근하기

기본 컬렉션 연산 외에 코틀린 표준 라이브러리에는 개별 컬랙션 원소에 대한 접근을 편하게 해주는 확장 함수들이
포함돼 있다. 이 절에서는 이런 함수를 다룬다.  
first()/last() 함수는 각각 주어진 컬렉션의 첫 번째와 마지막 원소를 반환하며, 컬렉션이 비어있으면 NoSuchElementException을
발생시킨다. 원소가 없으면 널을 반환하는 안전한 버전인 firstOrNull()/lastOrNull()도 있다.  
```kotlin
println( listOf(1, 2, 3).first() )  // 1
println( listOf(1, 2, 3).last() )   // 3
println( emptyArray<String>().first() )     // Exception
println( emptyArray<String>().firstOrNull() )       // null

val seq = generateSequence(1) { if (it > 50) null else it * 3 }
println( seq.first() )      // 1
println( seq.last() )       // 81
```
elemetnAt() 함수를 사용하면 인덱스를 사용해 컬렉션 원소를 읽을 수 있다. 이 함수는 리스트의 get() 함수를
일반화한 함수로 배열, 이터러블, 시퀀스 등에 모두 적용할 수 있다. 하지만 일반적으로 이 함수를 임의 접근 컬렉션이
아닌 컬렉션에 사용하면 인덱스 값에 비례한 실행 시간이 걸린다는 점에 유의하라.  
인덱스가 잘못된 경우 elementAt()은 예외로 던진다. 인덱스가 컬렉션의 인덱스 경계를 벗어날때 널을 반환하는
안전한 버전인 elementAtOrNull()이나 인덱스가 경계를 벗어난 경우 지정된 람다가 반환하는 원소를 돌려주는
elementAtOrElse()도 있다.
```kotlin
import kotlin.math.round

println( listOf(1, 2, 3).elementAt(2) )             // 3
println( sortedSetOf(1, 2, 3).elementAtOrNull(-1) )     // null
println( arrayOf("a", "b", "c").elementAtOrElse(1) {"???"} )        // b

val seq = generateSequence(1) { if ( it>50) null else it * 3 }

println( seq.elementAtOrNull(2) )       // 9
println( seq.elementAtOrElse(100) {Int.MAX_VALUE} )     // 2147483647
```
배열이나 리스트에 대한 구조 분해를 통해 앞에서부터 최대 다섯 개의 원소를 추출할 수 있다는 점을 알아두라,
하지만 컬렉션에 있는 원소의 수보다 더 많은 원소를 구조 분해로 가져오려고 하면 예외가 발생한다.
****

#### 콜렉션에 대한 조건 검사
어떤 컬렉션이 주어진 조건을 만족하는 검사하는 일이 많이 있을 것이다. 코틀린 라이브러리는 컬렉션 원소에 대해 
주어진 술어를 테스트하는 등의 기본적인 검사를 구현하는 함수를 제공한다.  
all() 함수는 컬렉션의 모든 원소가 주어진 술어를 만족하면 true를 반환한다. 배열, 이터러블, 시퀀스, 맵 등의
모든 컬렉션 객체에 대해 이 함수를 적용할 수 있다. 맵의 경우 맵 엔트리가 술어에 전달된다.
```kotlin
println( listOf(1, 2, 3).all { it < 10 } )         // true
println( listOf(1, 2, 3, 4).all { it % 2 == 2 } )   // false
println(
    mapOf(1 to "I", 5 to "V", 10 to "X").all { it.key == 1 || it.key % 5 == 0 }
)

val seq = generateSequence(1) { if (it < 50) it * 3 else null }

println(seq.all { it % 3 == 0 })        // false
println(seq.all { it == 1 || it % 3 == 0 })     // true
```
none() 함수는 all()과 반대다. 컬렉션에 주어진 조건을 만족하는 원소가 하나도 없을 때 true를 반환한다.
```kotlin
println( listOf(1, 2, 3, 4).none{ it > 5} )
println(
    mapOf(1 to "I", 5 to "V", 10 to "X").none { it.key % 2 == 0 }
)
val seq = generateSequence(1) { if ( it < 50 ) it*3 else null }
println(seq.none{ it >=100})
```
이런 종류의 함수로 any()는 컬렉션 원소 중 적어도 하나가 주어진 술어를 만족할 때 true를 반환한다.
```kotlin
println( listOf(1, 2, 3).any { it < 0 } )         // true
println( listOf(1, 2, 3, 4).any { it % 2 == 0 } )   // false
println(
    mapOf(1 to "I", 5 to "V", 10 to "X").any { it.key == 1 }
)
val seq = generateSequence(1) { if ( it < 50 ) it*3 else null }
println( seq.any {it % 3 == 0} )
println( seq.any {it > 100} )  
```
빈 컬렉션의 경우 all(), none(), any() 함수는 false를 반환한다. 드 모르간 법칙을 사용하면 세 함수를 다른 
함수를 사용해 기술할 수 있다. 무한 시퀀스에 all(), none(), any()를 적용하면 실행이 끝나지 않을 수도 있다.  

any()와 none() 함수에는 파라미터를 전혀 받지 않는 오버로딩된 버정이 있다. 
****

#### 집계

컬렉션 원소의 합계를 계산하거나 최댓값을 찾는 것처럼 컬렉션 내용으로부터 한 값을 계산해내는 경우를 집계라고 부른다.
코틀린 라이브러리는 이런 목적에 사용할 수 있는 몇 가지 함수를 제공한다. 앞 절에서는 any()나 all()처럼 컬렉션에 
대한 조건을 검사하는 함수를 살펴 봤다 이런 함수를 Boolean 값을 계산해내는 특별한 집계 함수라고 생각할 수 있다.  

일반적으로는 결코 반환값을 돌려받을 수 없기 때문에 무한 시퀀스에 대해 집계 함수를 적용할 수 없다.  
집계 함수는 세 가지 기본 그룹으로 나눌 수 있다. 첫 번째 그룹은 함계, 최솟값, 최댓값 등 자주 쓰이는 집계값을
계산한다.  

count()는 컬렉션의 원소 개수를 반환한다. 배열, 이터러블, 시퀀스, 맵을 포함하는 모든 컬렉션 객체에 이 함수를 
적용할 수 있다. 따라서 이 함수는 배열, 맵, Collection 인스턴스가 제공하는 size 프로퍼티를 일반화한 함수다.
```kotlin
println( listOf(1, 2, 3, 4).count() )       // 4
println( mapOf(1 to "I", 5 to "V", 10 to "X").count() )     // 3

val seq = generateSequence(1) { if(it < 50) it * 3 else null }
println( seq.count() )      // 5
```
average() 함수는 수로 이뤄진 배열, 이터러블, 시퀀스의 산술 평균을 구한다. 결과는 항상 Double이다.
```kotlin
println( listOf(1, 2, 3, 4).average() )     // 2.5
println( doubleArrayOf(1, 2, 2.3, 3.4).average() )      // 2.30000000003

val seq = generateSequence(1) { if ( it < 50) it*3 else null }
println( seq.average() )        // 24.2
```
컬렉션이 비어있으면 average() 함수는 항상 Double.NaN을 반환한다. 비어있지 않은 컬렉션 c에 대해 c.average()
는 근본적으로 c.sum().toDouble()/c.count()와 같다. count() 함수와 비슷한게 average() 함수도 컬렉션에
Int.MAX_VALUE보다 더 많은 원소가 들어있으면 예외를 발생시킨다.  

minOrNull()과 maxOrNull()은 비교 가능한 타입의 값이 들어있는 배열, 이터러블, 시퀀스의 최솟값과 최댓값을 계산한다.
```kotlin
println( intArrayOf(5, 8, 1, 4, 2).minOrNull() )        // 1
println( intArrayOf(5, 8, 1, 4, 2).maxOrNull() )        // 8
println( listOf("abc", "w", "xyz", "def", "hij").minOrNull() )      // abc
println( listOf("abc", "w", "xyz", "def", "hij").maxOrNull() )      // xyz
```
합계와 비슷하게 비교 가능한 값으로 원소를 변환하는 함수를 제공함으로써 비교할 수 없는 원소들로 이뤄진 컬렉션의 최댓값/
최솟값을 구할 수도 있다. minByOrNull()과 maxByOrNull() 함수가 이런 동작을 구현한다.

```kotlin
class Person(val firstName: String, val familyName: String, val age: Int) {
    override fun toString() = "$firstName $familyName $age"
}
fun main(){
    val persons = sequenceOf(
        Person("Brook", "Watts", 25),
        Person("Silver", "Hudson", 30)
    )
    
    println( persons.minByOrNull { it.firstName } )     // Brook Watts: 25
    println( persons.maxByOrNull { it.age } )     // Silver Huson: 30 
}
```
또는 minWithOrNull() maxWithOrNull()을 사용할 수도 있다. 이 두 함수는 변환 함수 대신 비교기를 받는다.  

빈 컬렉션에 대해 적용하는 경우 min/max 집계 함수의 모든 변형은 널을 반환한다.  

두 번째 집계 함수 그룹은 컬렉션 원소를 문자열로 엮는 일을 단당한다. 기본 함수는 joinTo String()이며,
가장 단순한 형태로 사용할 때 다음과 같이 아무 인자도 받지 않는다.
```kotlin
println( listOf(1, 2, 3).joinToString() )       //1, 2, 3
```
디폴트로 각 원소를 toString() 메소드를 이용해 문자열로 변환한 후에 구분 문자열로 콤마와 공백(",")을 사용한다.
하지만 커스텀 변환 함수를 제공해 원소를 다른 형태인 문자열로 변환할 수도 있다. 람다 파라미터를 통해 이런 변환을
제공한다.  

이 밖에 다음과 같은 파라미터를 선택적으로 지정할 수도 있다.
* seqparator: 인접한 두 원소 사이에 들어갈 구분 문자열
* prefix와 postfix: 결과 문자열의 맨 앞과 맨 뒤에 들어갈 문장열
* limit: 최대로 보여줄 수 있는 원소의 개수
* truncated: limit가 양수인 경우, 컬렉션의 원소를 모두 표현하지 못할 때 이 파라미터 뒤에 추가함

세 번째 집계 함수 그룹은 두 값을 조합하는 함수를 활용해 여러분이 원하는 임의의 집계 방식을 구현하게 해주는 함수다.
이 그룹은 fold()/reduce() 함수와 그 변형으로 이뤄진다.  
reduce() 함수는 파라미터가 두 개인 함수를 받는다. 이 함수의 첫 번째 인자는 누적된 값이고, 두 번째 인자는
컬렉션의 현재 값이다.
1. 누적값은 최초에 컬렉션의 첫 번째 원소로 초기화된다.
2. 컬렉션의 매 원소에 대해 현재 누적값과 현재 원소를 파라미터로 받은 함수에 적용하고 이 함수 적용의 결과를
누적값에 대입한다.
3. 누적의 결과를 반환한다.
****

#### 걸러내기
코틀린 표준 라이브러리는 컬렉션에서 조건을 만족하지 못하는 원소를 걸러내 버리고, 원하는 원소만 남기는 여러 확장
함수를 제공한다. 걸러내기 연산은 원본 컬렉션을 변하지 않는다. 대신 전혀 새로운 컬렉션을 만들거나 원본 컬렉션과
구별되는 기존 가변 컬렉션에 선택된 원소를 추가한다.  
가장 기본적으로 걸러내기 연산은 filter() 함수에 의해 제공된다. 이 함수에 전달되는 술어는 현재 원소를 인자로
받아서 원소를 컬렉션에 유지해야 하는 경우 true를 반환하고, 버려야 하는 경우 false를 반환한다. filter()
함수를 배열, 이터러블, 맵, 시퀀스에 적용할 수 있으며, 각각의 반환 타입은 다음과 같이 결정된다.
* Array<T> Iterable<T>를 거르면 List<T>를 얻는다.
* Map<K, V>를 거르면 Map<K, V>를 얻는다.
* Sequence<T>를 거르면 Sequence<T>를 얻는다.

filter()를 IntArray와 같은 원시 타입의 배열에도 적용할 수 있다. 다만, 반환타입은 List<Int>처럼 원시
타입이 아닌 박싱된 타입이 원소인 리스트다. filter()를 원시 타입 배열에 적용할 때는 강제로 방식이 이뤄진다는
점에 유의하라.
****

### 파일과 I/O 스트림

#### 스트림 유틸리티
 코틀린 표준 라이브러리는 자바 I/O 스트림에 대한 도우미 확장을 다수 제공한다. 이런 도우미 확장 함수들은 스트림
 콘텐츠에 대한 접근을 단순화해주고 복사, 자동 스트림 정리 등과 같은 복잡한 사용한 사용 패턴을 구현해준다.

 ```kotlin
import java.io.InputStream
import java.io.Reader

fun InputStream.readBytes(): ByteArray
fun Reader.readText(): String
fun Reader.readLines(): Line<String>
 ```
끝의 두 함수와 BufferedReader 클래스에 있는 readLine() 메서드의 차이를 기억하라. readLine()은 스트림에서
한 줄을 가져오지만, readText()/readLines()는 스트림 끝까지 콘텐츠를 읽어서 전체를 한 문자열이나 각 줄을
나타내는 문자열의 리스트로 반환한다.  
readText()와 달리 readLine() 함수는 값을 반환하면서 스트림을 닫아준다.

코틀린은 버퍼가 있는 스트림에 대한 직접 이터레이션을 허용한다. 다만 이진 데이터와 텍스트 데이터에 대한 API는 조금
다르다. BufferedOutputStream의 경우에는 iterator()가 있기 때문에 for 루프에서 스트림을 사용해 개별
바이트에 대해 이터레이션할 수 있다.

```kotlin
import java.io.FileInputStream

FileInputStream("data.bin").buffered().use { 
    val sum = 0
    for ( byte in it ) sum += byte
}
```
반면 BufferedReader에는 각 줄로 이뤄진 시퀀스를 돌려주는 lineSequence()가 있다.  

forEachLine()에 전달되는 람다는 이터레이션이 진행되면서 한 줄씩 데이터를 인자로 전달받는 반면, useLines()
에 전달되는 람다는 모든 라인에 대한 시퀀스를 인자로 받는다는 점이 다르다.  
copyTo() 함수를 사용하면 한 스트림에서 다른 스트림으로 데이터를 전달할 수 있다. 이 함수는 이진 데이터와
텍스트 데이터를 처리하는 오버로딩 버전이 존재한다.
  
이 절에서 다룰 다른 함수로, 명시적으로 정리해야 하는 스트림이나 다른 자원을 안전하게 처리할 수 있는 방법을 
제공하는 use() 함수가 있다. use() 함수는 자바 java.io.Closeable 타입의 값에 대해 호출할 수 있으며,
인자로 전달받은 람다를 실행하고 나서 마지막 자원을 적절히 정리해준 후 람다의 결과를 돌려준다.  

이 함수는 자바 7에 도입된 try-with-resource와 같은 역할을 한다.
****

#### 스트림 생성
코틀린 표준 라이브러리는 자바 I/O 스트림을 편하게 만들 수 있는 함수들을 제공한다.  

bufferReaders()/bufferedWriter() 확장 함수를 사용하면 지정한 File 객체에 대해 BufferedReader/
BufferedWriter 인스턴스를 만들 수 있다.  

비슷한 함수로 reader()/writer() 확장 함수도 있다. 이들은 각각 버퍼가 없는 Filereader/FileWriter
객체를 만든다.  
리더/라이터 관련 함수를 사용할 때는 선택적으로 인코딩 문자 집합을 지정할 수도 있다. 버퍼를 사용하는 버전에는
버퍼 크기를 지정하는 선택적인 추가 파라미터도 있다. 디퐆트 버퍼 크기는 DEFAULT_BUFFER_SIZE 상수에 들어있는데,
현재는 8킬로바이트다.  

Charsets 객체에는 US-ASCII나 UTF의 변종 등과 같은 여러 표준 문자 집합 상수가 정의돼 있다.  
이진 파일을 처리하고 싶다면 inputSteam()/outputStream()을 사용해 적절한 스트림을 생성하면 된다.  

코틀린 표준 라이브러리에는 스트림의 연결을 단순화해주는 몇 가지 확장 함수도 들어있다. 다음과 같은 함수를 
사용하면 일반적인 InputStream 클래스의 인스턴스를 바탕으로 Reader, BufferedReader, BufferedInputStream
객체를 만들 수 있다.  

OutputStream을 writer, BufferedWriter, BufferedOutputStream에 연결할 때 사용할 수 있는 비슷한
함수도 있다. 
****

#### URL 유틸리티
코틀린 표준 라이브러리는 URL 객체의 주소로부터 네트워크 연결을 통해 데이터를 읽어오는 몇가지 도우미 함수를 제공한다.

```kotlin
import java.nio.charset.Charset

fun URL.readText(charset: Charset = Charsets.UTF_8): String
fun URL.readByBytes(): ByteArray
```
readText() 함수는 URL 인스턴스에 해당하는 입력 스트림의 콘텐츠를 전부 읽어온다. readBytes() 함수도
비슷하게 입력 이진 스트림의 바이트 배열로 읽어온다  
두 함수 모두 전체 스트림 콘텐츠를 읽어오는 작업이 완료될 때까지 스레드를 블럭시키므로, 큰 파일을 다운로드할 때는
이 두 함수를 사용하면 안된다.
****

#### 파일 콘텐츠 접근하기
코틀린 표준 라이브러리는 명시적으로 I/O 스트림을 쓰지 않고도 파일 콘텐츠를 읽을 수 있는 특별한 함수를 제공한다.
이런 함수는 전체 파일을 읽고 쓰거나, 데이터를 기존 파일 뒤에 추가하거나, 한 줄씩 파일을 처리해야 할 때 유용하다.  
텍스트 콘텐츠를 처리할 때는 다음 함수를 쓸 수 있다.
* readText(): 파일 콘텐츠 전부를 한 문자열로 읽어온다.
* readLines(): 파일 콘텐츠 전부를 줄 구분 문자를 사용해 줄 단위로 나눠 읽어서 문자열의 리스트를 반환한다.
* writeText(): 파일 콘텐츠를 주어진 문자열로 설정한다. 필요하면 파일을 덮어 쓴다.
* appendText(): 주어진 문자열을 파일의 콘텐츠 뒤에 추가한다.

파일 콘텐츠를 처리하게 해주는 다른 함수들은 파일을 전부 읽지 않고 블록 안에서 조금씩 처리할 수 있게 해준다.
메모리에 모두 다 읽어올 수 없는 큰 파일을 다루는 경우 이런 함수가 유용하다.  
forEachLine() 함수를 사용하면 파일 전체를 읽지 않고 텍스트 콘텐츠를 한 줄씩 처리할 수 있다.
```kotlin
fun main(){
    val file = File("data.txt")
    
    file.writeText("One\nTwo\nThree")
    file.forEachLine{ print("/$it") }  // /One/Two/Three
}
```
다른 텍스트 관련 파일 함수와 마찬가지로 forEachLine()과 useLines()에도 선택적인 Charset 파라미터를
전달할 수 있다.  
이진 파일을 처리할 때는 forEachBlock() 함수를 사용한다. 이 함수가 받는 람다는 ByteArray 버퍼와 현재 
이터레이션에서 몇 바이트가 버퍼 안에 들어있는지 알려주는 정수를 파라미터로 받는다.
****

#### 파일 시스템 유틸리티
이 절에서는 파일 복사, 삭제, 디렉터리 계층 구조 순회 들을 쉽게 해주는 코틀린 표준 라이브러리 함수를 다룬다.  
deleteRecursively() 함수를 사용하면 파일이나 디렉터리를 자신에게 포함된 자손들까지 포함해 쉽게 지울 수 있다.
이 함수는 삭제가 성공하면 true를, 그렇지 않으면 false를 반환한다. 이 함수가 false를 반환한 경우, 자식
디렉터리는 안 지워지는 등 파일 중 일부만 지워졌을 수도 있다. 이 함수는 자바 API가 mkdir() 메서드의 역할을
한다고 볼 수 있다.  

copyTo() 함수는 자신의 주신 객체를 다른 파일에 복사하고 복사본을 가리키는 파일 객체를 돌려준다.

```kotlin
import java.io.File

fun main() {
    val source = File("data.txt")
    source.writeText("Hello")
    
    val target = source.copyTo(File("dataNew.txt"))
    println(target.readText())
}
```
디폴트로 대상 파일을 덮어 쓰지는 않는다. 따라서 대상 파일이 이미 존대하는 경우 copyTo() 함수는 FileAlreadyExistsException을
발생시킨다. 하지만 파일을 강제로 복사하도록 overwrite 파라미터를 지정할 수 있다.  

copyTo() 함수를 디렉터리에 적용할 수도 있다. 하지만 디렉터리의 경우 copyTo()는 하위 디렉터리나 디렉터리에
들어있는 파일을 복사하지 않고 대상 경로에 맞춰 빈 디렉터리만 만들어준다. 디렉터리와 내용물을 모두 복사하고 싶다면
copyRecursively() 함수를 사용해야 한다.

```kotlin
import java.io.File

fun main() {
    File("old/dir").mkdirs()
    File("old/dir/data1.txt").also( it.writeText("One") )
    File("old/dir/data2.txt").also( it.writeText("Two") )
    
    FIle("old").copyRecursively(File("new"))
    
    println( File("new/dir/data1.txt").readText() )     // One
    println( File("new/dir/data2.txt").readText() )     // Two
}
```
copyTo()와 마찬가지로 copyRecursively()에도 overwrite 파라미터를 통해 덮어 쓸지 여부를 정해줄 수 있다.
추가로 어떤 파일을 복사하다가 IOException이 발생하면 호출할 액션을 설정할 수도 있다. 선택적인 OnError 파라미터를
통해 OnErrorAction 타입의 람다를 넘기면 된다. 람다의 결과값 copyRecursively() 함수가 문제가 되는 파일을 
어떻게 처리할지를 결정한다.
* SKIP: 파일을 무시하고 복사를 계속 진행한다.
* TERMINATE: 복사를 중단한다.
  
walk() 함수는 깊이 우선 디렉터리 구조 순회를 구현한다. 
* TOP_DOWN: 자식보다 부모를 먼저 방문
* BOTTOM_UP: 자식을 부모보다 먼저 방문  

또한, walk(TOP_DOWN) 대신 walkTopDown()을 쓸 수 있고, walk(BOTTOM_UP) 대신 walkBottomUp()을
쓸 수 있다.  
walk() 함수가 반환하는 시퀀스는 특별한 FileTreeWalk 클래스에 속한다. 이 클래스는 일반적인 시퀀스 기능과
더불어 별도의 순회 옵션을 지정할 수 있게 해준다. maxDepth() 함수는 순회할 하위 트리의 최대 깊이를 지정한다.

 onEnter()와 onLeave() 함수는 순회가 디렉터리에 들어가거나 디렉터리에서 나올 때 호출할 동작을 지정한다.
 onEnter() 호출은 Boolean 람다를 파라미터로 받고, 이 람다의 반환값은 디렉터리 Unit 람다를 받는다. onFail()
 함수를 통해 디렉터리의 자식에 접근할 때 IOExcepction이 발생하는 경우에 호출될 액션을 정할 수 있다. 이 액션은
 (File, IOException) -> Unit 타입의 람다이며, 문제가 되는 디렉터리와 발생한 예외를 인자로 받는다.  
 
이 네 함수가 모두 FileTreeWalk의 현재 인스턴스를 반환한다.