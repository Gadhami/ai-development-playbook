# Skill: Unit Test Patterns

> Loaded on demand by agents when writing or reviewing tests.

## .NET Test Patterns

### Basic Test Structure (xUnit)
```csharp
public class OrderServiceTests
{
    private readonly Mock<IOrderRepository> _repositoryMock = new(MockBehavior.Strict);
    private readonly Mock<IUnitOfWork> _unitOfWorkMock = new(MockBehavior.Strict);

    private OrderService CreateSut() => new(_repositoryMock.Object, _unitOfWorkMock.Object);

    [Fact]
    public async Task CreateOrder_ValidCustomer_ReturnsOrderId()
    {
        // Arrange
        var customerId = Guid.NewGuid();
        _repositoryMock.Setup(r => r.Add(It.IsAny<Order>()));
        _unitOfWorkMock.Setup(u => u.SaveChangesAsync(It.IsAny<CancellationToken>()))
            .ReturnsAsync(1);

        // Act
        var result = await CreateSut().CreateOrderAsync(customerId, CancellationToken.None);

        // Assert
        result.Should().NotBeEmpty();
        _repositoryMock.Verify(r => r.Add(It.Is<Order>(o => o.CustomerId == customerId)), Times.Once);
    }
}
```

### Basic Test Structure (MSTest)
```csharp
[TestClass]
public class OrderServiceTests
{
    private Mock<IOrderRepository> _repositoryMock;
    private Mock<IUnitOfWork> _unitOfWorkMock;

    [TestInitialize]
    public void Setup()
    {
        _repositoryMock = new(MockBehavior.Strict);
        _unitOfWorkMock = new(MockBehavior.Strict);
    }

    private OrderService CreateSut() => new(_repositoryMock.Object, _unitOfWorkMock.Object);

    [TestMethod]
    public async Task CreateOrder_ValidCustomer_ReturnsOrderId()
    {
        // Arrange / Act / Assert — same as above
    }
}
```

### Parameterized Tests
```csharp
// xUnit
[Theory]
[InlineData(0, false)]
[InlineData(1, true)]
[InlineData(100, true)]
[InlineData(-1, false)]
public void IsValidQuantity_VariousValues_ReturnsExpected(int quantity, bool expected)
{
    Order.IsValidQuantity(quantity).Should().Be(expected);
}

// MSTest
[TestMethod]
[DataRow(0, false)]
[DataRow(1, true)]
[DataRow(100, true)]
[DataRow(-1, false)]
public void IsValidQuantity_VariousValues_ReturnsExpected(int quantity, bool expected)
{
    Order.IsValidQuantity(quantity).Should().Be(expected);
}
```

### Testing Exceptions
```csharp
[Fact]
public async Task GetOrder_NotFound_ThrowsNotFoundException()
{
    // Arrange
    var id = Guid.NewGuid();
    _repositoryMock.Setup(r => r.GetByIdAsync(id, It.IsAny<CancellationToken>()))
        .ReturnsAsync((Order?)null);

    // Act
    var act = () => CreateSut().GetOrderAsync(id, CancellationToken.None);

    // Assert
    await act.Should().ThrowAsync<NotFoundException>()
        .WithMessage($"*{id}*");
}
```

### Testing with In-Memory Database
```csharp
public class OrderQueryTests : IDisposable
{
    private readonly ApplicationDbContext _context;

    public OrderQueryTests()
    {
        var options = new DbContextOptionsBuilder<ApplicationDbContext>()
            .UseInMemoryDatabase(Guid.NewGuid().ToString())
            .Options;
        _context = new ApplicationDbContext(options);
    }

    [Fact]
    public async Task GetOrders_WithStatusFilter_ReturnsOnlyMatching()
    {
        // Arrange
        _context.Orders.AddRange(
            new Order { Id = Guid.NewGuid(), Status = OrderStatus.Pending },
            new Order { Id = Guid.NewGuid(), Status = OrderStatus.Completed },
            new Order { Id = Guid.NewGuid(), Status = OrderStatus.Pending }
        );
        await _context.SaveChangesAsync();

        var handler = new GetOrdersQueryHandler(_context);

        // Act
        var result = await handler.Handle(
            new GetOrdersQuery(Status: OrderStatus.Pending), CancellationToken.None);

        // Assert
        result.Should().HaveCount(2);
        result.Should().AllSatisfy(o => o.Status.Should().Be("Pending"));
    }

    public void Dispose() => _context.Dispose();
}
```

## Angular Test Patterns

### Component Test
```typescript
describe('OrderListComponent', () => {
  let fixture: ComponentFixture<OrderListComponent>;

  beforeEach(async () => {
    await TestBed.configureTestingModule({
      imports: [OrderListComponent],
    }).compileComponents();

    fixture = TestBed.createComponent(OrderListComponent);
  });

  it('should render all orders', () => {
    const orders: Order[] = [
      { id: '1', title: 'Order A', status: 'pending' },
      { id: '2', title: 'Order B', status: 'completed' },
    ];
    fixture.componentRef.setInput('orders', orders);
    fixture.detectChanges();

    const cards = fixture.debugElement.queryAll(By.css('[data-testid="order-card"]'));
    expect(cards.length).toBe(2);
  });

  it('should emit orderClicked when card is clicked', () => {
    const order: Order = { id: '1', title: 'Order A', status: 'pending' };
    fixture.componentRef.setInput('orders', [order]);
    fixture.detectChanges();

    spyOn(fixture.componentInstance.orderClicked, 'emit');
    const card = fixture.debugElement.query(By.css('[data-testid="order-card"]'));
    card.triggerEventHandler('click');

    expect(fixture.componentInstance.orderClicked.emit).toHaveBeenCalledWith(order);
  });
});
```

### Service Test with HttpClientTesting
```typescript
describe('OrderService', () => {
  let service: OrderService;
  let httpTesting: HttpTestingController;

  beforeEach(() => {
    TestBed.configureTestingModule({
      providers: [provideHttpClient(), provideHttpClientTesting()],
    });
    service = TestBed.inject(OrderService);
    httpTesting = TestBed.inject(HttpTestingController);
  });

  afterEach(() => httpTesting.verify());

  it('should fetch orders', () => {
    const expected: Order[] = [{ id: '1', title: 'Test', status: 'pending' }];

    service.getOrders().subscribe(orders => {
      expect(orders).toEqual(expected);
    });

    const req = httpTesting.expectOne('/api/orders');
    expect(req.request.method).toBe('GET');
    req.flush(expected);
  });

  it('should handle error', () => {
    service.getOrders().subscribe({
      error: (err) => expect(err.status).toBe(500),
    });

    httpTesting.expectOne('/api/orders').error(new ProgressEvent('error'), { status: 500 });
  });
});
```
