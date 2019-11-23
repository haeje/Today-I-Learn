## 클래스 멤버 체계

    클래스.
    	정적 공개 상수
    	정적 비공개 변수
    	비공개 인스턴스 변수 // 공개 변수가 필요한 경우는 거의 없다.
    
    	공개 함수
    	비공개 함수(는 자신을 호출하는 공개 함수 직후)

추상화 단계가 순차적으로 내려가는 구조로 작성하자. 신문처럼 자연스럽게 읽혀야 한다.

### 클래스 이름은 해당 클래스의 책임을 나타내야 한다.

클래스 이름을 간결하게 짓는 것이 어렵다면, 클래스의 크기가 크거나 책임이 많은지 생각해보아야 한다. ⇒ 여러가지 일을 하는 클래스이기 때문에 이름을 짓기 힘들 가능성이 높다.

클래스는 함수와 유사하게 작을 수록 좋다. ( 단일 책임 원칙 )

## 클래스 응집도

    // Stack을 구현한 코드, 응집도가 높은 편이다.
    // size() 메서드를 제외하고는 인스턴스 변수가 모든 함수에서 사용된다.
    
    public class Stack {
    	private int topOfStack = 0;
    	List<Integer> elements = new LinkedList<Integer>();
    
    	public int size() { 
    		return topOfStack;
    	}
    
    	public void push(int element) { 
    		topOfStack++; 
    		elements.add(element);
    	}
    	
    	public int pop() throws PoppedWhenEmpty { 
    		if (topOfStack == 0)
    			throw new PoppedWhenEmpty();
    		int element = elements.get(--topOfStack); 
    		elements.remove(topOfStack);
    		return element;
    	}
    }

클래스에 속한 메서드와 변수가 서로 의존하며 논리적인 단위로 묶여있다. ⇒ 응집도가 높다.
클래스 인스턴스가 특정 메서드에서만 사용된다.  => 응집도가 낮아진다. => 파생 클래스로 분리를 고려한다.

### 함수를 작게, 매개변수 목록을 짧게?

라는 전략을 취하려고 집중하다보면 때때로 몇몇 메서드만 사용하는 인스턴스 변수가 많아진다. 즉, 클래스의 응집도가 낮아진다. 

이럴 때, 클래스를 쪼개주면 된다.

### 리팩토링 시뮬레이션

일단 돌아가는 코드를 목표로 작성했다고 가정하자. 
돌아가는 코드에 집중했을 것이므로 함수가 잘 분리되어 있지 않거나 덩어리가 클 것이다.
클린코드를 위한 리팩토링 시뮬레이션을 해보자.

1. 큰 함수가 있을 것이다. 
2. 큰 함수를 가독성이나 단일책임을 가지는 함수로 구성하기 위해 여러 함수로 분리할 것이다. 
3. 함수를 분리할 때, 변수가 문제가 될 수 있다. 

    A라는 함수에서 사용하는 a 변수가 있다고 생각해보자. A 함수가 너무 크기 때문에 A1, A2, A3 함수로 분리하고 싶다. 하지만 a 변수가 A1, A2함수에서 사용된다면 a 변수는 어떻게 관리해야 할까.

4. a 변수를 클래스 인스턴스 변수로 변경시키는 방법과 메서드 매개변수로 전달하는 방법이 있다.
5. 결국 클래스 내부에 있는 함수들이니까 클래스 인스턴스 변수로 변경시키는 것이 더 나은 방법인 것 같다.
6. 하지만 이렇게 인스턴스 변수로 변경한 a 변수는 A와 관련된 함수에서만 사용되는 인스턴스 변수가 된다. 다른 함수 B, C, D, E, ... 에서는 사용되지 않는 인스턴스 변수이다. 즉, 응집도가 낮아진다.
7. 이때 클래스를 분리한다. a 변수와 관련함수는 다른 클래스로 분리하는 것이 좋다. 

### 수정에 강한, 유지보수에 유연한 클래스

기존의 클래스를 변경하는 것은 위험하다고 볼 수 있다. 변경되는 클래스를 사용하는 다른 클래스들에 변경사항이 영향을 미칠 수 있기 때문이다. 

    public class Sql {
    	public Sql(String table, Column[] columns)
    	public String create()
    	public String insert(Object[] fields)
    	public String selectAll()
    	public String findByKey(String keyColumn, String keyValue)
    	public String select(Column column, String pattern)
    	public String select(Criteria criteria)
    	public String preparedInsert()
    	private String columnList(Column[] columns)
    	private String valuesList(Object[] fields, final Column[] columns) private String selectWithCriteria(String criteria)
    	private String placeholderList(Column[] columns)
    }

위의 Sql 클래스는 2가지 상황에서 변경이 일어난다.

1. 기존 작성된 기능(함수)에 변경이 필요할 때
2. 새로운 기능(함수)을 추가할 때

### Open Close Principal ( OCP ) :  확장에 열리게 수정에 닫히게

    // 공개 인터페이스를 전부 SQL 클래스에서 파생하는 클래스로 만들고, 
    // 비공개 메서드는 해당 클래스로 옮기고,
    // 공통된 인터페이스는 따로 클래스로 뺐다.
    // 이렇게 하면 update문 추가 시에 기존의 클래스를 건드릴 이유가 없어진다.
    
    	abstract public class Sql {
    		public Sql(String table, Column[] columns) 
    		abstract public String generate();
    	}
    	public class CreateSql extends Sql {
    		public CreateSql(String table, Column[] columns) 
    		@Override public String generate()
    	}
    	
    	public class SelectSql extends Sql {
    		public SelectSql(String table, Column[] columns) 
    		@Override public String generate()
    	}
    	
    	public class InsertSql extends Sql {
    		public InsertSql(String table, Column[] columns, Object[] fields) 
    		@Override public String generate()
    		private String valuesList(Object[] fields, final Column[] columns)
    	}
    	
    	public class SelectWithCriteriaSql extends Sql { 
    		public SelectWithCriteriaSql(
    		String table, Column[] columns, Criteria criteria) 
    		@Override public String generate()
    	}
    	
    	public class SelectWithMatchSql extends Sql { 
    		public SelectWithMatchSql(String table, Column[] columns, Column column, String pattern) 
    		@Override public String generate()
    	}
    	
    	public class FindByKeySql extends Sql public FindByKeySql(
    		String table, Column[] columns, String keyColumn, String keyValue) 
    		@Override public String generate()
    	}
    	
    	public class PreparedInsertSql extends Sql {
    		public PreparedInsertSql(String table, Column[] columns) 
    		@Override public String generate() {
    		private String placeholderList(Column[] columns)
    	}
    	
    	public class Where {
    		public Where(String criteria) public String generate()
    	}
    	
    	public class ColumnList {
    		public ColumnList(Column[] columns) public String generate()
    	}

이렇게 클래스를 분리하면...

언급했던 변경상황 2가지가 분리된다.

1. 기존 작성된 기능에 변경이 필요할 때

    → Sql 클래스가 수정되어야 하는 경우는 Sql 기능이 변경되어야 할 때 뿐이다.

2. 새로운 기능을 추가할 때

    → Sql 클래스는 변경할 필요없이, 새로운 클래스를 추가하면 된다.

    → 변경이 필요없다는 것은 의존하고 있는 다른 시스템에 영향을 주지 않는다는 뜻이다.

