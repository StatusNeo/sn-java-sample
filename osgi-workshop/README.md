# Northwind Order Management System (OMS)

A multi-bundle **OSGi / Eclipse Tycho** project used as a hands-on assessment.
The goal is to demonstrate that you can work inside an OSGi plugin project: read and
modify `MANIFEST.MF` headers, wire bundles together through `Import-Package` /
`Export-Package`, and build the reactor with Maven Tycho.

## Module layout (8 components)

```
oms.platform.parent (pom)
├── catalog/            plugins: com.northwind.oms.core, com.northwind.oms.inventory
│                       feature: com.northwind.oms.catalog.feature
├── customer/           plugins: com.northwind.oms.customer
│                       feature: com.northwind.oms.customer.feature
├── security/           plugins: com.northwind.oms.security
│                       feature: com.northwind.oms.security.feature
├── payment/            plugins: com.northwind.oms.payment
│                       feature: com.northwind.oms.payment.feature
├── shipping/           plugins: com.northwind.oms.shipping
│                       feature: com.northwind.oms.shipping.feature
├── notification/       plugins: com.northwind.oms.notification
│                       feature: com.northwind.oms.notification.feature
├── orders/             plugins: com.northwind.oms.pricing, com.northwind.oms.gateway
│                       feature: com.northwind.oms.orders.feature
└── reporting/          plugins: com.northwind.oms.reporting
                        feature: com.northwind.oms.reporting.feature
```

Every component follows the same shape as a real Tycho reactor: a component
aggregator `pom.xml` with `features/` and `plugins/` sub-folders. Each bundle
carries `META-INF/MANIFEST.MF`, `build.properties`, `pom.xml`, `.project`,
`.classpath` and `.gitignore`; each feature carries `feature.xml`,
`feature.properties`, `build.properties`, `license.txt`, `pom.xml` and `.project`.

## Dependency wiring (via OSGi Import/Export-Package)

| Bundle | Exports | Imports (own bundles) |
|---|---|---|
| `com.northwind.oms.core` | `core.model`, `core.spi` | — |
| `com.northwind.oms.security` | `security`, `security.spi` | — |
| `com.northwind.oms.customer` | `customer` | — |
| `com.northwind.oms.inventory` | `inventory` | `core.model`, `core.spi` |
| `com.northwind.oms.pricing` | `pricing` | `core.model`, `core.spi` |
| `com.northwind.oms.payment` | `payment` | `core.model`, `security.spi` |
| `com.northwind.oms.shipping` | `shipping` | `core.model`, `customer` |
| `com.northwind.oms.notification` | `notification` | `customer`, `shipping` |
| `com.northwind.oms.gateway` | `gateway` | `core.model`, `core.spi`, `inventory`, `pricing` |
| `com.northwind.oms.reporting` | `reporting` | `core.model`, `payment`, `gateway` |

All dependencies are expressed **only** through `Import-Package` /
`Export-Package` in each bundle's `META-INF/MANIFEST.MF` — no `Require-Bundle`
is used between the project's own bundles, which is the recommended OSGi
practice. Version ranges such as `version="[1.0.0,2.0.0)"` are used so
resolution can be reasoned about.

## Build

```bash
mvn clean verify
```

## Assessment tasks (examples)

1. Add a new exported package to `com.northwind.oms.core` and consume it from `gateway`.
2. Break a version range on purpose and explain the resulting resolution error.
3. Add a brand new component (e.g. `returns`) with its own feature and wire it into the reactor.
4. Move `PaymentGateway` behind an SPI in `core` and register it as an OSGi service.
