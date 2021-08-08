# iOS Memory Structure

밑의 글들이 너무 잘 설명되어 있어서 간단히 정리만 올려두겠다.

- 코드 : 소스코드. Read-Only
- 데이터 : 전역(global)변수, 정적(static)변수
- 힙 : 참조(reference)타입, 동적할당, 해제. 유일하게 런타임 시점에 결정되는 영역
- 스택 : 매개(parameter)변수, 지역(local)변수, LIFO

 Code & Data 영역은 메모리 사용량에 변동이 없다. 앱이 실행되는 동안에 Xcode 상에서 작성한 코드와 변수들이 변할 일은 그냥 없다고 보면 된다.

Code & Data 영역과는 반대로 Stack 과 Heap 영역은 가변적이다. 그렇다면 만약 스택이나 힙 영역이 거대해져서 상호 간의 영역을 침범하게 되면

어떻게 되는 것일까 ? 만약 스택 영역이 거대해져서 높은 주소로 올라가다가 힙 영역을 침범하면 스택 오버플로우. 그 반대의 경우의 힙 오버플로우 현상이 발생한다. 

예를들면 for 문을 무한히 돌리게 되면 스택 오버플로우가 발생하게 될 것이고, ARC 에 의해서 관리되지 못한 강한 순환 참조와 같은 문제들로
인해서 힙 오버플로우 현상이 발생할 수 있다.

## References

[소들이님 블로그 - iOS) 메모리 구조 (Code, Data, Stack, Heap)](https://babbab2.tistory.com/25?category=831129)

[티씨피스쿨 - 메모리의 구조](http://tcpschool.com/c/c_memory_structure)