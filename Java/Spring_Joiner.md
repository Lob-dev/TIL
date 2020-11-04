2020.10.24

<br/>

Spring Joiner

JDK 8에 추가된 객체로 배열이나 리스트를 받아서 추가하고 싶은 문자를 넣어주면 인덱스 사이마다 해당 값을 넣은 문자열로 반환해준다.

```java
public static void main(String[] args) {
        String[] strings = new String[] { "야", "아", "오", "우" };
        System.out.println(String.join("++", strings));
    }

public static String join(CharSequence delimiter, CharSequence... elements) {
		//인자의 null 체크를 해주고 null이 아닐시 그대로 반환, null 일 경우 NPE를 발생
    Objects.requireNonNull(delimiter);
    Objects.requireNonNull(elements);

    StringJoiner joiner = new StringJoiner(delimiter);
    for (CharSequence cs: elements) {
        joiner.add(cs); //StringJoiner add(CharSequence newElement) 호출
    }
    return joiner.toString();
}

/** 추가 된 모든 문자열 구성 요소(값)를 포함하는 변수. */
private String[] elts;

/** 추가 된 문자열 구성 요소의 갯수 */
private int size;

/** 접두사와 접미사를 제외한 총 문자 길이 */
private int len;

public StringJoiner add(CharSequence newElement) {
				// 인자 값을 String으로 변경하고
        final String elt = String.valueOf(newElement);
				
				// 값이 추가된 적이 없는 경우 (초기 생성 String[8])
        if (elts == null) {
            elts = new String[8];
        } else { // 기존 값이 있는 경우 구성 요소의 갯수와 인자의 갯수가 같다면.
            if (size == elts.length)
                elts = Arrays.copyOf(elts, 2 * size); // 인자 값을 담고 구성 요소 수 x 2 
																											// 만큼의 공간을 가진 array를 반환

						// 총 문자 길이에 중간에 추가할 data 의 길이만큼 추가한다.
            len += delimiter.length(); 
        }
				// 구성 요소의 길이 (여유 공간) 가 인자의 길이보다 크다면. 전달된 인자의 길이를 더한다
        len += elt.length();
				
				// 현재 구성요소의 끝부분에 해당 String을 저장한다
        elts[size++] = elt;
        return this;
    }
```

```java
야++아++오++우
```