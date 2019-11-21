## 테스트 코드

시스템은 언제든지 변경 될 수 있다. 때문에 코드는 잘 작성되어 있어야 한다. 유연해야한다. 이를 위해 작성된 코드를 리팩토링해서 가독성을 높이고 구조를 수정할 수 있어야 한다. 

리팩토링은 코드의 수정이다. 수정 전과 후에 코드가 같은 결과를 반환하는지 확인하지 못한다면 리팩토링을 하기 힘들다. 즉 리팩토링을 하려면 테스트 코드는 필수적이다. 

시스템은 언제든지 변경 될 수 있다. 그렇다면 테스트 코드도 변경되어야 할 상황이 있다. 즉 테스트코드도 유연하게 작성되어야 한다. 유지보수가 힘든 테스트코드는 오히려 관리하기가 힘들어진다. 가독성을 높여야 한다.

### TDD 법칙

1. 실패하는 단위 테스트를 작성할 때까지 실제 코드를 작성하지 않는다.
2. 컴파일은 실패하지 않으면서 실행이 실패하는 정도로만 단위 테스트를 작성한다.
3. 현재 실패하는 테스트를 통과할 정도로만 실제 코드를 작성한다.

### 깨끗한 테스트 코드

    public void testGetPageHierarchyAsXml() throw Exception {
    	makepage("PageOne", "PageOne.ChildOne", "PageTwo");
    
    	submitRequest("root", "type:pages");
    
    	assertResponseIsXML();
    	assertResponseContains( 
    		"<name>PageOne</name>", "<name>PageTwo</name>", "<name>ChildOne</name>"
    	);
    }

<BUILD-OPERATE-CHECK 패턴 >

1. 테스트 자료를 만든다.
2. 테스트 자료를 조작한다.
3. 조작한 결과가 올바른지 확인한다.

### 테스트 당 개념하나, assert 하나

가독성을 위해 테스트 당 assert(검증 코드) 1개를 사용할 것을 권장한다. 함수에서 하나의 기능을 하도록 하는 것와 같은 의미인 것 같다.
