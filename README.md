# @unified-plugin-framework/sdk

Plugin development SDKs for the Unified Plugin Framework.

## Overview

This monorepo contains the development SDKs for building UPF plugins:

| Package | Description |
|---------|-------------|
| `@unified-plugin-framework/backend-sdk` | Backend plugin development SDK |
| `@unified-plugin-framework/ui-sdk` | Frontend UI component SDK |
| `@unified-plugin-framework/grpc-client` | gRPC client utilities |
| `@unified-plugin-framework/testing` | Testing utilities and mocks |

## Installation

```bash
# Backend SDK
npm install @unified-plugin-framework/backend-sdk

# UI SDK
npm install @unified-plugin-framework/ui-sdk

# gRPC Client
npm install @unified-plugin-framework/grpc-client

# Testing utilities
npm install -D @unified-plugin-framework/testing
```

## Backend SDK

The backend SDK provides utilities for creating gRPC services with Bun:

```typescript
import { PluginServer, createServer, injectable, inject } from '@unified-plugin-framework/backend-sdk';
import { IStorage, ICache } from '@unified-plugin-framework/interfaces';

@injectable()
class OrderService {
  constructor(
    @inject('IStorage') private storage: IStorage,
    @inject('ICache') private cache: ICache
  ) {}

  async createOrder(request: CreateOrderRequest): Promise<Order> {
    // Implementation
  }
}

const server = createServer({
  manifest: './manifest.yaml',
  services: [OrderService],
});

await server.start();
```

## UI SDK

The UI SDK provides React Native components and hooks for plugin UIs:

```typescript
import { useGrpcClient, useStateBridge, useEventBus, useTheme } from '@unified-plugin-framework/ui-sdk';
import { registerPlugin } from '@unified-plugin-framework/ui-sdk';

// Register plugin with the UI Shell
registerPlugin({
  id: 'my-plugin',
  screens: {
    Home: HomeScreen,
    Details: DetailsScreen,
  },
  widgets: {
    Summary: SummaryWidget,
  },
});

// Use hooks in components
function OrderList() {
  const client = useGrpcClient(OrderServiceClient);
  const theme = useTheme();
  const eventBus = useEventBus();

  // ...
}
```

## gRPC Client

Typed gRPC client utilities:

```typescript
import { createGrpcClient, GrpcError, Status } from '@unified-plugin-framework/grpc-client';
import { OrderServiceClient } from './generated/order';

const client = createGrpcClient(OrderServiceClient, 'gateway:50051');

try {
  const order = await client.getOrder({ id: '123' });
} catch (error) {
  if (error instanceof GrpcError && error.status === Status.NOT_FOUND) {
    // Handle not found
  }
}
```

## Testing

Mocking and testing utilities:

```typescript
import { createMock, TestContainer } from '@unified-plugin-framework/testing';
import { IStorage, ICache } from '@unified-plugin-framework/interfaces';

const mockStorage = createMock<IStorage>();
mockStorage.get.mockResolvedValue({ id: '1', name: 'Test' });

const container = new TestContainer()
  .bind('IStorage', mockStorage)
  .bind('ICache', createMock<ICache>());

const service = container.resolve(OrderService);
```

## Documentation

See the [full documentation](https://unified-plugin-framework.github.io/docs/guides/plugin-development) for complete guides.

## Related Packages

- [@unified-plugin-framework/interfaces](https://github.com/Unified-Plugin-Framework/interfaces) - Interface definitions
- [@unified-plugin-framework/cli](https://github.com/Unified-Plugin-Framework/cli) - Command line tools

## License

MIT
