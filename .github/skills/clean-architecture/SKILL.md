# Skill: Clean Architecture Patterns

> Loaded on demand by agents when working on .NET backend features.

## Layer Reference

```
┌─────────────────────────────────────────┐
│            Presentation                 │  Controllers, Endpoints, Middleware
│            (Web/API)                    │  References: Application
├─────────────────────────────────────────┤
│            Infrastructure               │  EF Core, Repos, External Services
│                                         │  References: Application, Domain
├─────────────────────────────────────────┤
│            Application                  │  Use Cases, DTOs, Interfaces, Validation
│                                         │  References: Domain
├─────────────────────────────────────────┤
│            Domain                       │  Entities, Value Objects, Events, Enums
│                                         │  References: Nothing
└─────────────────────────────────────────┘
```

## Adding a New Feature (Step-by-Step)

### 1. Domain Layer — Define the core

```csharp
// Domain/Entities/Order.cs
public class Order
{
    public Guid Id { get; private set; }
    public DateTime CreatedAt { get; private set; }
    public OrderStatus Status { get; private set; }
    private readonly List<OrderItem> _items = [];
    public IReadOnlyList<OrderItem> Items => _items.AsReadOnly();

    public void AddItem(Product product, int quantity)
    {
        if (quantity <= 0) throw new ArgumentException("Quantity must be positive", nameof(quantity));
        _items.Add(new OrderItem(product, quantity));
    }

    public void Complete()
    {
        if (Status != OrderStatus.Pending)
            throw new InvalidOperationException($"Cannot complete order in {Status} status");
        Status = OrderStatus.Completed;
        AddDomainEvent(new OrderCompletedEvent(Id));
    }
}
```

### 2. Application Layer — Define the use case

```csharp
// Application/Orders/Commands/CreateOrder/CreateOrderCommand.cs
public record CreateOrderCommand(Guid CustomerId) : IRequest<Guid>;

// Application/Orders/Commands/CreateOrder/CreateOrderCommandHandler.cs
public class CreateOrderCommandHandler(IOrderRepository repository, IUnitOfWork unitOfWork)
    : IRequestHandler<CreateOrderCommand, Guid>
{
    public async Task<Guid> Handle(CreateOrderCommand request, CancellationToken ct)
    {
        var order = new Order(request.CustomerId);
        repository.Add(order);
        await unitOfWork.SaveChangesAsync(ct);
        return order.Id;
    }
}

// Application/Orders/Commands/CreateOrder/CreateOrderCommandValidator.cs
public class CreateOrderCommandValidator : AbstractValidator<CreateOrderCommand>
{
    public CreateOrderCommandValidator()
    {
        RuleFor(x => x.CustomerId).NotEmpty();
    }
}
```

### 3. Infrastructure Layer — Implement persistence

```csharp
// Infrastructure/Persistence/Configurations/OrderConfiguration.cs
public class OrderConfiguration : IEntityTypeConfiguration<Order>
{
    public void Configure(EntityTypeBuilder<Order> builder)
    {
        builder.HasKey(o => o.Id);
        builder.Property(o => o.Status).HasConversion<string>();
        builder.HasMany(o => o.Items).WithOne().HasForeignKey("OrderId");
        builder.Navigation(o => o.Items).AutoInclude();
    }
}

// Infrastructure/Persistence/Repositories/OrderRepository.cs
internal class OrderRepository(ApplicationDbContext context) : IOrderRepository
{
    public void Add(Order order) => context.Orders.Add(order);

    public async Task<Order?> GetByIdAsync(Guid id, CancellationToken ct)
        => await context.Orders.TagWithCallSite().FirstOrDefaultAsync(o => o.Id == id, ct);
}
```

### 4. Presentation Layer — Expose the endpoint

```csharp
// Web/Endpoints/Orders/CreateOrder.cs
public class CreateOrder : IEndpoint
{
    public void Map(IEndpointRouteBuilder app)
    {
        app.MapPost("/api/orders", async (CreateOrderRequest request, ISender sender) =>
        {
            var id = await sender.Send(new CreateOrderCommand(request.CustomerId));
            return Results.Created($"/api/orders/{id}", new { id });
        });
    }
}
```

## Common Patterns

### CQRS Query Example
```csharp
public record GetOrderQuery(Guid Id) : IRequest<OrderDto>;

public class GetOrderQueryHandler(ApplicationDbContext context)
    : IRequestHandler<GetOrderQuery, OrderDto>
{
    public async Task<OrderDto> Handle(GetOrderQuery request, CancellationToken ct)
        => await context.Orders
            .TagWithCallSite()
            .AsNoTracking()
            .Where(o => o.Id == request.Id)
            .Select(o => new OrderDto(o.Id, o.Status.ToString(), o.CreatedAt))
            .FirstOrDefaultAsync(ct)
           ?? throw new NotFoundException(nameof(Order), request.Id);
}
```

### Validation Pipeline Behavior
```csharp
public class ValidationBehavior<TRequest, TResponse>(IEnumerable<IValidator<TRequest>> validators)
    : IPipelineBehavior<TRequest, TResponse> where TRequest : notnull
{
    public async Task<TResponse> Handle(TRequest request, RequestHandlerDelegate<TResponse> next, CancellationToken ct)
    {
        if (!validators.Any()) return await next();

        var context = new ValidationContext<TRequest>(request);
        var failures = (await Task.WhenAll(validators.Select(v => v.ValidateAsync(context, ct))))
            .SelectMany(r => r.Errors)
            .Where(f => f is not null)
            .ToList();

        if (failures.Count != 0) throw new ValidationException(failures);

        return await next();
    }
}
```

### Domain Events
```csharp
// Domain
public abstract class BaseEntity
{
    private readonly List<INotification> _domainEvents = [];
    public IReadOnlyList<INotification> DomainEvents => _domainEvents.AsReadOnly();
    protected void AddDomainEvent(INotification domainEvent) => _domainEvents.Add(domainEvent);
    public void ClearDomainEvents() => _domainEvents.Clear();
}

// Application
public class OrderCompletedEventHandler(INotificationService notifications)
    : INotificationHandler<OrderCompletedEvent>
{
    public async Task Handle(OrderCompletedEvent notification, CancellationToken ct)
        => await notifications.SendAsync($"Order {notification.OrderId} completed", ct);
}
```
