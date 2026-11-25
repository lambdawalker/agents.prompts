# Go + AWS prompt entry points

The former combined instructions have been split by repository responsibility. Existing links to this file remain valid. Choose one role and read its linked common requirements; do not apply both roles indiscriminately.

| Entry point | Target |
| --- | --- |
| [Feature repository](go-aws-feature.md) | Reusable capability such as email verification, including its infrastructure module and isolated test app. |
| [Composed application repository](go-aws-application.md) | Host application assembling features and shared services, such as Attestra. |
| [Common requirements](go-aws-common.md) | Shared task modes, Go contracts, Composer compatibility, deployment, migration, verification and delivery. Loaded by either role. |

Each role supports **design**, **implement**, and **align**. State the target repository and intended mode. Alignment includes fixing applicable gaps unless audit-only is requested. A documentation-only task remains documentation-only. For mixed repositories, apply the appropriate role to each owning module and keep a single shared requirement matrix with explicit scopes.

An ordinary Go module with no AWS infrastructure uses the common Go contracts and applicable shared documentation rules; AWS/Composer/deployment sections are not applicable. No deployment wizard is required.
