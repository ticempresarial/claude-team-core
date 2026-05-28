---
name: demo-data-strategies
description: Patrones de generación de data demo para producción según stack (PHP/Perfex CRM, PHP/Laravel, Node/Prisma, Node/Drizzle, Node/TypeORM, Node/Sequelize, Postgres raw, MySQL raw, Flutter/Dart). Define dónde poner markers internos, cómo hacer idempotente, qué nombres realistas usar, cómo distribuir estados/fechas. Aplica cuando se necesita generar seed data para subir a un demo público (NO para dev local).
---

# Demo data strategies — multi-stack

Patrones canónicos para generar data demo **lista para producción**:
nombres realistas, sin tags `[DEMO_TEST]` visibles, idempotente, con
distribución estadística realista.

**Diferencia con `perfex-demo-seeder`**: aquel inserta directo en MySQL
local con marker `[DEMO_TEST]` en campos visibles (bug post-mortem
task_guard). Este skill define cómo generar data **pulida para producción**
en CUALQUIER stack.

---

## Reglas universales (aplican a todos los stacks)

### 1. Markers — solo en campos NO visibles

❌ ANTI-PATRÓN (lo que rechazó task_guard):
```
INSERT INTO tblclients (company) VALUES ('[DEMO_TEST] Acme Corp');
```

✅ PATRÓN CORRECTO:
```
INSERT INTO tblclients (company, adminnote)
VALUES ('Acme Corporation', '[INTERNAL_SEED] 2026-05-20');
--           ^^^^^^^^^^^^^^^               ^^^^^^^^^^^^^^^^
--      visible al comprador        campo interno staff-only
```

Campos seguros para marker (varían por entidad):
- `adminnote`, `notes`, `internal_note`, `internal_description`,
  `private_comment`, `description_internal`

Campos PROHIBIDOS (visibles al usuario final):
- `name`, `title`, `subject`, `company`, `firstname`, `lastname`,
  `description`, `content`, `address`, `email`

### 2. Nombres realistas, no Lorem

Catálogo canónico de **empresas** (variedad de industrias):
```
Acme Corporation, TechBridge Solutions, Northwind Traders,
Global Dynamics, Pinnacle Industries, Vertex Labs, Helios Energy,
Riverside Consulting, BlueOcean Logistics, Summit Analytics,
Quantum Systems, MeridianCo, Apex Manufacturing, Stellar Media,
GreenLeaf Organics, Ironclad Security, Skyline Architects,
NovaBank Financial, Pioneer Engineering, Coastal Pharma,
Sentinel Technologies, Voyager Travel Group, Cascade Beverages,
Foundry Print Shop, Lighthouse Education
```

Catálogo canónico de **personas** (mix internacional):
```
Sarah Johnson, Michael Chen, Emily Rodriguez, David Patel,
James Wilson, Aisha Khan, Marcus Hoffman, Sofia Romano,
Carlos Mendez, Lin Wei, Diego Hernandez, Priya Sharma,
Anna Kowalski, Hiroshi Tanaka, Maya Patel, Tomas Bergman,
Olivia Martinez, Lucas Silva, Yuki Yamamoto, Fatima Al-Hassan
```

NO usar:
- `Test User`, `John Doe` (excepto el admin)
- `Lorem ipsum`, `Sample Customer 1`
- `Cliente Demo`, `Usuario Prueba`

### 3. Distribución de estados realista

Para entidades con estados (facturas, propuestas, etc.), distribuir
proporcionalmente como en producción real:

| Entidad | Estado | % típico |
|---------|--------|----------|
| Invoices | Paid | 30-40% |
| Invoices | Unpaid | 20-25% |
| Invoices | Overdue | 10-15% |
| Invoices | Partially paid | 10-15% |
| Invoices | Draft | 10-15% |
| Invoices | Cancelled | 2-5% |
| Estimates | Sent | 30% |
| Estimates | Accepted | 25% |
| Estimates | Declined | 15% |
| Estimates | Draft | 20% |
| Estimates | Expired | 10% |
| Leads | New | 35% |
| Leads | Contacted | 20% |
| Leads | Qualified | 15% |
| Leads | Negotiation | 10% |
| Leads | Won | 8% |
| Leads | Lost | 12% |

### 4. Distribución de fechas

NO concentrar todas las fechas en un día. Distribuir entre los últimos
6-12 meses con concentración mayor en los últimos 30 días:

```
40% últimos 30 días
30% últimos 31-90 días
20% últimos 91-180 días
10% últimos 181-365 días
```

Para fechas futuras (due dates), distribuir 7-60 días hacia adelante.

### 5. Montos coherentes

Para facturas/estimates/expenses: escala logarítmica, NO uniforme.

```
60% entre $200-$2,000
25% entre $2,000-$10,000
12% entre $10,000-$25,000
3% entre $25,000-$100,000
```

### 6. Idempotencia

Antes de insertar masivamente, verificar si la data demo ya existe.
Cada stack tiene su forma:

- **MySQL/MariaDB**: `INSERT IGNORE` o `ON DUPLICATE KEY UPDATE`
- **Postgres**: `ON CONFLICT (id) DO NOTHING`
- **Prisma**: `createMany({ skipDuplicates: true })`
- **Drizzle**: `.onConflictDoNothing()`
- **TypeORM/Sequelize**: `upsert()` o verificar count antes

---

## Patrón por stack

### PHP / Perfex CRM (MariaDB)

**Output**: archivo `.sql` portable, ejecutable en phpMyAdmin.

```sql
-- demo-data-<modulo>.sql
-- Ejecutar en BD perfex_demoN (no producción real).
-- Marker [INTERNAL_SEED] queda solo en adminnote.

START TRANSACTION;
SET FOREIGN_KEY_CHECKS = 0;

-- Customers (25 registros, nombres limpios)
INSERT IGNORE INTO tblclients
  (userid, company, vat, phonenumber, country, city, address, active, datecreated, adminnote)
VALUES
  (NULL, 'Acme Corporation',    '12345678', '+1 555 0101', 231, 'New York',   '123 Main St',    1, '2026-04-15 10:00:00', '[INTERNAL_SEED] 2026-05-20'),
  (NULL, 'TechBridge Solutions','23456789', '+1 555 0102', 231, 'San Francisco','456 Market St',1, '2026-04-18 11:30:00', '[INTERNAL_SEED] 2026-05-20'),
  (NULL, 'Northwind Traders',   '34567890', '+44 20 7946', 232, 'London',     '789 Oxford St',  1, '2026-03-22 09:15:00', '[INTERNAL_SEED] 2026-05-20'),
  -- ... 22 más
;

-- Invoices (40 registros, distribución de estados)
INSERT IGNORE INTO tblinvoices
  (id, number, prefix, clientid, date, duedate, status, subtotal, total, adminnote)
VALUES
  -- 10 paid (status=2)
  (NULL, 9001, 'INV-', 1, '2026-04-15', '2026-05-15', 2,  1250.00,  1450.00, '[INTERNAL_SEED] paid'),
  (NULL, 9002, 'INV-', 2, '2026-04-20', '2026-05-20', 2,  3800.00,  4408.00, '[INTERNAL_SEED] paid'),
  -- 8 unpaid (status=1)
  (NULL, 9011, 'INV-', 3, '2026-05-01', '2026-06-01', 1,   850.00,   986.00, '[INTERNAL_SEED] unpaid'),
  -- 6 overdue (status=4, duedate < CURDATE())
  (NULL, 9021, 'INV-', 1, '2026-03-10', '2026-04-10', 4,  2400.00,  2784.00, '[INTERNAL_SEED] overdue'),
  -- ... etc
;

SET FOREIGN_KEY_CHECKS = 1;
COMMIT;
```

**Cuándo usar**: módulos Perfex que necesitan demo público.
**Ejecuta**: phpMyAdmin → SQL → pegar → Go. O vía CLI `mysql -u root demo1 < demo-data.sql`.

---

### PHP / Laravel

**Output**: clase Seeder en `database/seeders/DemoDataSeeder.php`.

```php
<?php

namespace Database\Seeders;

use Illuminate\Database\Seeder;
use App\Models\Client;
use App\Models\Invoice;

class DemoDataSeeder extends Seeder
{
    public function run(): void
    {
        $companies = [
            'Acme Corporation', 'TechBridge Solutions', /* ... */
        ];

        foreach ($companies as $i => $company) {
            Client::updateOrCreate(
                ['company' => $company],  // unique key
                [
                    'vat'         => str_pad(rand(10000000, 99999999), 8, '0'),
                    'phonenumber' => '+1 555 01' . str_pad($i, 2, '0', STR_PAD_LEFT),
                    'country'     => 231,
                    'city'        => 'New York',
                    'active'      => true,
                    'admin_note'  => '[INTERNAL_SEED] ' . now()->toDateString(),
                ]
            );
        }

        // Invoices: distribuir estados
        // ...
    }
}
```

**Cuándo usar**: apps Laravel propias.
**Ejecuta**: `php artisan db:seed --class=DemoDataSeeder` en producción.

---

### Node / Prisma

**Output**: archivo `prisma/seed-demo.ts`.

```typescript
import { PrismaClient } from '@prisma/client';

const prisma = new PrismaClient();

async function main() {
  const companies = [
    'Acme Corporation', 'TechBridge Solutions', /* ... */
  ];

  // Clients
  await prisma.client.createMany({
    data: companies.map((company, i) => ({
      company,
      vat:         String(10000000 + i),
      phonenumber: `+1 555 01${String(i).padStart(2, '0')}`,
      country:     'US',
      active:      true,
      adminNote:   '[INTERNAL_SEED] ' + new Date().toISOString().slice(0, 10),
    })),
    skipDuplicates: true,  // idempotente
  });

  // Invoices con distribución de estados
  const clients = await prisma.client.findMany({
    where: { adminNote: { contains: '[INTERNAL_SEED]' } }
  });

  await prisma.invoice.createMany({
    data: Array.from({ length: 40 }, (_, i) => {
      const status = pickStatus(i);  // distribución %
      return {
        number:    9000 + i,
        clientId:  clients[i % clients.length].id,
        date:      randomDateLastMonths(6),
        dueDate:   randomDueDate(status),
        status,
        total:     logScaleAmount(),
        adminNote: '[INTERNAL_SEED] ' + status,
      };
    }),
    skipDuplicates: true,
  });
}

main()
  .catch(console.error)
  .finally(() => prisma.$disconnect());
```

**Cuándo usar**: apps Next.js / Node con Prisma.
**Ejecuta**: `npx ts-node prisma/seed-demo.ts` o `npx prisma db seed`.

---

### Node / Drizzle ORM

**Output**: archivo `scripts/seed-demo.ts`.

```typescript
import { db } from '@/db';
import { clientsTable, invoicesTable } from '@/db/schema';

const companies = ['Acme Corporation', 'TechBridge Solutions', /* ... */];

const clientsData = companies.map((company, i) => ({
  company,
  vat: String(10000000 + i),
  adminNote: '[INTERNAL_SEED] ' + new Date().toISOString().slice(0, 10),
}));

await db.insert(clientsTable)
  .values(clientsData)
  .onConflictDoNothing();  // idempotente

// Invoices similar...
```

**Ejecuta**: `bun run scripts/seed-demo.ts` o `tsx scripts/seed-demo.ts`.

---

### Node / TypeORM

**Output**: clase Seeder o script standalone.

```typescript
import { DataSource } from 'typeorm';
import { Client } from './entities/Client';

export async function seedDemoData(ds: DataSource) {
  const clientRepo = ds.getRepository(Client);
  const companies = ['Acme Corporation', /* ... */];

  for (const company of companies) {
    const existing = await clientRepo.findOneBy({ company });
    if (existing) continue;  // idempotente

    await clientRepo.save({
      company,
      vat: String(10000000 + Math.floor(Math.random() * 89999999)),
      adminNote: '[INTERNAL_SEED] ' + new Date().toISOString().slice(0, 10),
    });
  }
}
```

---

### Postgres raw

**Output**: `.sql` con `ON CONFLICT`.

```sql
BEGIN;

INSERT INTO clients (company, vat, phonenumber, country, active, admin_note)
VALUES
  ('Acme Corporation',    '12345678', '+1 555 0101', 'US', true, '[INTERNAL_SEED] 2026-05-20'),
  ('TechBridge Solutions','23456789', '+1 555 0102', 'US', true, '[INTERNAL_SEED] 2026-05-20')
  -- ... 23 más
ON CONFLICT (company) DO NOTHING;

INSERT INTO invoices (number, client_id, date, due_date, status, total, admin_note)
VALUES
  (9001, 1, '2026-04-15', '2026-05-15', 'paid',  1450.00, '[INTERNAL_SEED] paid'),
  (9002, 2, '2026-04-20', '2026-05-20', 'paid',  4408.00, '[INTERNAL_SEED] paid')
  -- ...
ON CONFLICT (number) DO NOTHING;

COMMIT;
```

**Ejecuta**: `psql -d mydb -f demo-data.sql`.

---

### SQLite raw

Idéntico a MySQL pero usa `INSERT OR IGNORE`:

```sql
INSERT OR IGNORE INTO clients (company, vat, admin_note)
VALUES ('Acme Corporation', '12345678', '[INTERNAL_SEED]');
```

---

### Flutter / Dart

**Output**: archivo `lib/data/fixtures/demo_seed.dart` con función que llama al backend.

```dart
// lib/data/fixtures/demo_seed.dart
import 'package:dio/dio.dart';

class DemoSeeder {
  final Dio _dio;
  DemoSeeder(this._dio);

  static const _companies = [
    'Acme Corporation', 'TechBridge Solutions', /* ... */
  ];

  Future<void> seed() async {
    for (final company in _companies) {
      await _dio.post('/api/clients', data: {
        'company': company,
        'admin_note': '[INTERNAL_SEED] ${DateTime.now().toIso8601String()}',
      });
    }
  }
}
```

**Cuándo usar**: apps Flutter que consumen un backend que no tiene seeder propio.

---

## Verificación post-seed

Después de cualquier seed, ejecutar query de validación:

```sql
-- ¿Hay markers en campos VISIBLES? Debe ser 0.
SELECT COUNT(*) AS leaked FROM tblclients   WHERE company LIKE '%[INTERNAL%' OR company LIKE '%[DEMO%';
SELECT COUNT(*) AS leaked FROM tblleads     WHERE name    LIKE '%[INTERNAL%' OR name    LIKE '%[DEMO%';
SELECT COUNT(*) AS leaked FROM tbltasks     WHERE name    LIKE '%[INTERNAL%' OR name    LIKE '%[DEMO%';

-- ¿Hay markers en campos INTERNOS? Debe ser >0.
SELECT COUNT(*) AS seeded FROM tblclients   WHERE adminnote LIKE '%[INTERNAL_SEED]%';
```

Si la primera query retorna >0 → STOP, hay leak visible.
Si la segunda retorna 0 → no se ejecutó el seed.

---

## Cuándo NO usar este skill

- Para data demo LOCAL de dev en Perfex → usa `perfex-demo-seeder` directo
  (más rápido, inserta vía mysql CLI directo)
- Para tests unitarios → usa fixtures de testing, no seed de producción
- Para volcado de BD existente → usa `mysqldump`/`pg_dump` con filtros

Este skill es para: **generar archivo de seed limpio que se sube al
servidor de producción demo público**.
