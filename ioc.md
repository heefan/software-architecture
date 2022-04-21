
```java
public class BookService {
    private HikariConfig config = new HikariConfig();
    private DataSource dataSource = new HikariDataSource(config);

    public Book getBook(long bookId) {
        try (Connection conn = dataSource.getConnection()) {
            ...
            return book;
        }
    }
}
```

```java
public class UserService {
    private HikariConfig config = new HikariConfig();
    private DataSource dataSource = new HikariDataSource(config);

    public User getUser(long userId) {
        try (Connection conn = dataSource.getConnection()) {
            ...
            return user;
        }
    }
}
```

```java
public class CartServlet extends HttpServlet {
    private BookService bookService = new BookService();
    private UserService userService = new UserService();

    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        long currentUserId = getFromCookie(req);
        User currentUser = userService.getUser(currentUserId);
        Book book = bookService.getBook(req.getParameter("bookId"));
        cartService.addToCart(currentUser, book);
        ...
    }
}
```


```java
public class HistoryServlet extends HttpServlet {
    private BookService bookService = new BookService();
    private UserService userService = new UserService();
}
```

Problems: 
1. too many instance initialization. 
2. Can we let `BookService` and `UserService` share the same instance `DataSource`. But who will control (create/destroy) `DataSource`?
3. Similarly, `CarServlet` and `HistoryServlet` has the same issue with (2)
4. If we have more components in the future, the logic will be messy. 
5. Testing is hard, for example, the `BookService` require the real service to test. 

How to resolve? 
1. Who manage the instances?
2. How to initialize in sequence? 
3. How to dealloc in sequence? 

IoC solution

Example, `BookService`, loose couple the dependency. 
Does `BookService` need to know `DataSource`?
Ans: No, `BookService` only need to use it.  So we can use `set` method to do the denpendency injection. 

```java
public class BookService {
    private DataSource dataSource;

    public void setDataSource(DataSource dataSource) {
        this.dataSource = dataSource;
    }
}
```

How Java implement IoC?
Via Bean (XML file)

```
<beans>
    <bean id="dataSource" class="HikariDataSource" />
    <bean id="bookService" class="BookService">
        <property name="dataSource" ref="dataSource" />
    </bean>
    <bean id="userService" class="UserService">
        <property name="dataSource" ref="dataSource" />
    </bean>
</beans>
```

https://www.tutorialspoint.com/spring/spring_bean_definition.htm
https://www.baeldung.com/spring-bean-scopes
