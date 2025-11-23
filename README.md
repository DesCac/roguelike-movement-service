# Movement Service (Go)

**Validates player and NPC movement** на карте roguelike игры.

## Ответственность

✅ **ЧТО ДЕЛАЕТ:**
- Collision detection (стены, другие сущности)
- Movement distance validation
- Pathfinding validation (A*)
- Fog of War calculations
- Делегирование на Combat Service если на клетке враг

❌ **ЧТО НЕ ДЕЛАЕТ:**
- Генерация карты
- AI логика движения
- Combat расчеты (делегирует)

## Технологии

- **Язык:** Go 1.21+
- **gRPC:** google.golang.org/grpc
- **Протоколы:** contracts/proto/movement/

## Размер кода

**Target:** ~500 LOC

## gRPC Contract

```protobuf
service MovementService {
  rpc ValidateMove(ValidateMoveRequest) returns (ValidateMoveResponse);
}
```

## Запуск локально

```bash
# Install dependencies
go mod download

# Run service
go run main.go

# Health check
grpcurl -plaintext localhost:50051 grpc.health.v1.Health/Check
```

## Docker

```bash
# Build
docker build -t movement-service:dev -f Dockerfile.dev .

# Run
docker run -p 50051:50051 movement-service:dev
```

## Environment Variables

```bash
GRPC_PORT=50051
LOG_LEVEL=info
```

## Testing

```bash
go test ./...
```

## Примеры использования

### Успешное движение
```json
Request:
{
  "game_id": "game_123",
  "entity_id": "player_1",
  "current_position": {"x": 30, "y": 25},
  "target_position": {"x": 31, "y": 25}
}

Response:
{
  "status": "SUCCESS",
  "new_position": {"x": 31, "y": 25},
  "visible_tiles": [...]
}
```

### Делегирование на Combat
```json
Request:
{
  "current_position": {"x": 30, "y": 25},
  "target_position": {"x": 30, "y": 24}  // клетка с мобом
}

Response:
{
  "status": "DELEGATE",
  "failure_reason": "TILE_OCCUPIED",
  "delegate_to": {
    "action_type": "attack",
    "target_id": "mob_123"
  }
}
```

## Performance

- **Target latency:** < 5ms (p95)
- **Throughput:** ~10,000 validations/sec

---

**Статус:** Skeleton (требует имплементации)
**Следующие шаги:** Реализовать ValidateMove с collision detection и делегированием
