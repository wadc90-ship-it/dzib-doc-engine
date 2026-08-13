# 04 — DZIB Transaction Core B1

Estado: **CONSTRUIDO EN LABORATORIO**
Fecha: 2026-08-13

## Propósito

Convertir las acciones de negocio de DZIB OS en transacciones persistentes, atómicas, idempotentes y auditables. La interfaz deja de modificar cifras directamente.

## Evento raíz B1

`CAPITAL_EXPRESS_DISBURSEMENT`

## Principio

Una operación de negocio genera una sola transacción raíz. The Wallet, cartera, Daily Engine, documentos e Intelligence deben leer o reaccionar a esa misma fuente de verdad.

## Dispersión Capital Express

1. Validar idempotencia.
2. Validar liquidez del banco origen.
3. Crear transacción `POSTED`.
4. Disminuir liquidez del banco.
5. Aumentar cuentas por cobrar por el principal dispersado.
6. Crear operación Capital Express activa.
7. Emitir eventos derivados.
8. Crear auditoría.
9. Confirmar todo mediante un único commit atómico.

## Eventos derivados B1

- `wallet.liquidity.changed`
- `capital.operation.activated`
- `receivable.created`
- `daily.collection.schedule.requested`
- `document.credit.requested`

## Regla patrimonial

Una dispersión no crea patrimonio por sí sola: reclasifica activo de liquidez a cuenta por cobrar.

Ejemplo de laboratorio Edith:

Antes:
- Liquidez: $8,829.00
- Cuentas por cobrar: $34,976.37
- Patrimonio operativo: $43,805.37

Dispersión principal: $5,000.00

Después:
- Liquidez: $3,829.00
- Cuentas por cobrar (activo por principal): $39,976.37
- Patrimonio operativo: $43,805.37

La comisión contractual no se infla como efectivo o patrimonio al momento de dispersar; su reconocimiento queda sujeto a la política contable/operativa definitiva.

## Persistencia B1

Laboratorio implementado sobre SQLite con tablas para:
- empresas,
- cuentas,
- clientes,
- operaciones de crédito,
- transacciones,
- asientos,
- eventos,
- auditoría.

## Idempotencia

Cada transacción usa `idempotency_key`. Repetir la misma solicitud no debe duplicar la dispersión.

## Siguiente fase

- `PAYMENT_RECEIVED`
- `BANK_RECONCILED`
- `PROMISE_TO_PAY_CREATED`
- `CREDIT_RENEWAL_REQUESTED`
- API HTTP para conectar la interfaz D Capital al Core y sustituir `localStorage` como fuente de verdad.
