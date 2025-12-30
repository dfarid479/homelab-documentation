# UPS Integration

Power management and automated shutdown scripts.

## NUT (Network UPS Tools)

Configuration for APC 1500VA UPS.

## Graceful Shutdown

Automated shutdown sequence when power critical:
1. Alert via notifications
2. Stop non-critical containers
3. Flush logs to disk
4. Stop critical services
5. Shutdown TrueNAS

## Testing

Regular UPS battery tests scheduled quarterly.
