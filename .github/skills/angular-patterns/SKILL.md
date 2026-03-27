# Skill: Angular Component Patterns

> Loaded on demand by agents when working on Angular features.

## Standalone Component Skeleton

```typescript
import { ChangeDetectionStrategy, Component, computed, inject, input, output, signal } from '@angular/core';

@Component({
  selector: 'app-feature-name',
  standalone: true,
  imports: [],
  changeDetection: ChangeDetectionStrategy.OnPush,
  template: `
    @if (loading()) {
      <app-spinner />
    } @else if (error()) {
      <app-error-message [message]="error()!" />
    } @else {
      <div>
        @for (item of items(); track item.id) {
          <app-item-card
            [item]="item"
            (selected)="onSelect(item)" />
        }
      </div>
    }
  `,
})
export class FeatureNameComponent {
  // Dependencies
  private readonly service = inject(FeatureService);

  // Inputs & Outputs
  readonly filterId = input.required<string>();
  readonly selected = output<Item>();

  // State
  readonly loading = signal(true);
  readonly error = signal<string | null>(null);
  readonly items = signal<Item[]>([]);

  // Derived state
  readonly itemCount = computed(() => this.items().length);
  readonly hasItems = computed(() => this.items().length > 0);

  onSelect(item: Item) {
    this.selected.emit(item);
  }
}
```

## Smart / Dumb Pattern

### Smart (Container) Component
```typescript
@Component({
  selector: 'app-order-list-page',
  standalone: true,
  imports: [OrderListComponent, OrderFilterComponent],
  template: `
    <app-order-filter
      [statuses]="statuses"
      (filterChanged)="onFilter($event)" />
    <app-order-list
      [orders]="filteredOrders()"
      [loading]="loading()"
      (orderClicked)="navigateToOrder($event)" />
  `,
})
export class OrderListPageComponent {
  private readonly orderService = inject(OrderService);
  private readonly router = inject(Router);

  readonly loading = signal(true);
  private readonly allOrders = signal<Order[]>([]);
  private readonly activeFilter = signal<OrderFilter>({});

  readonly filteredOrders = computed(() => {
    const filter = this.activeFilter();
    return this.allOrders().filter(o => !filter.status || o.status === filter.status);
  });

  // Data fetching, navigation, side effects live here
}
```

### Dumb (Presentational) Component
```typescript
@Component({
  selector: 'app-order-list',
  standalone: true,
  changeDetection: ChangeDetectionStrategy.OnPush,
  template: `
    @if (loading()) {
      <app-skeleton-list />
    } @else {
      @for (order of orders(); track order.id) {
        <div class="order-card" (click)="orderClicked.emit(order)">
          <span>{{ order.title }}</span>
          <span>{{ order.status }}</span>
        </div>
      } @empty {
        <p>No orders found.</p>
      }
    }
  `,
})
export class OrderListComponent {
  readonly orders = input.required<Order[]>();
  readonly loading = input(false);
  readonly orderClicked = output<Order>();
  // No inject(), no service calls, no side effects
}
```

## Data Service Pattern

```typescript
@Injectable({ providedIn: 'root' })
export class OrderService {
  private readonly http = inject(HttpClient);

  getOrders(filter?: OrderFilter): Observable<Order[]> {
    const params = this.buildParams(filter);
    return this.http.get<Order[]>('/api/orders', { params });
  }

  getOrderById(id: string): Observable<Order> {
    return this.http.get<Order>(`/api/orders/${encodeURIComponent(id)}`);
  }

  createOrder(request: CreateOrderRequest): Observable<Order> {
    return this.http.post<Order>('/api/orders', request);
  }

  private buildParams(filter?: OrderFilter): HttpParams {
    let params = new HttpParams();
    if (filter?.status) params = params.set('status', filter.status);
    if (filter?.from) params = params.set('from', filter.from.toISOString());
    return params;
  }
}
```

## Reactive Form Pattern

```typescript
interface OrderForm {
  customerId: FormControl<string>;
  items: FormArray<FormGroup<OrderItemForm>>;
  notes: FormControl<string>;
}

@Component({ /* ... */ })
export class CreateOrderComponent {
  private readonly fb = inject(NonNullableFormBuilder);

  readonly form = this.fb.group<OrderForm>({
    customerId: this.fb.control('', [Validators.required]),
    items: this.fb.array<FormGroup<OrderItemForm>>([]),
    notes: this.fb.control(''),
  });

  addItem() {
    this.form.controls.items.push(this.fb.group<OrderItemForm>({
      productId: this.fb.control('', [Validators.required]),
      quantity: this.fb.control(1, [Validators.required, Validators.min(1)]),
    }));
  }

  onSubmit() {
    if (this.form.invalid) {
      this.form.markAllAsTouched();
      return;
    }
    const value = this.form.getRawValue();
    // submit...
  }
}
```

## Route Configuration

```typescript
// app.routes.ts
export const routes: Routes = [
  { path: '', redirectTo: 'dashboard', pathMatch: 'full' },
  {
    path: 'dashboard',
    loadComponent: () => import('./features/dashboard/dashboard.component')
      .then(m => m.DashboardComponent),
  },
  {
    path: 'orders',
    canActivate: [authGuard],
    children: [
      {
        path: '',
        loadComponent: () => import('./features/orders/order-list-page.component')
          .then(m => m.OrderListPageComponent),
      },
      {
        path: ':id',
        loadComponent: () => import('./features/orders/order-detail-page.component')
          .then(m => m.OrderDetailPageComponent),
      },
    ],
  },
];

// Functional guard
export const authGuard: CanActivateFn = () => {
  const authService = inject(AuthService);
  const router = inject(Router);
  return authService.isAuthenticated() || router.createUrlTree(['/login']);
};
```
