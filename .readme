# Руководство по запуску тестов DocumentDB

В DocumentDB есть 2 типа тестов:

1. **Регрессионные тесты на основе pg_regress**
2. **Rust тесты (Cargo)** - тесты для шлюза (gateway)

## Запуск регрессионных тестов

### 1. Применение патча для упрощения работы с таргетами

Создадим патч в корне DocumentDB:

```bash
cat > fixed_makefile.patch << 'EOF'
--- Makefile
+++ Makefile
@@ -10,6 +10,52 @@
 	$(MAKE) -C pg_documentdb_core check
 	$(MAKE) -C pg_documentdb check
 
+# Test targets
+.PHONY: check check-all check-core check-main check-rum check-distributed
+.PHONY: check-core-minimal check-main-minimal check-distributed-minimal check-quick
+
+# Main test target - runs all tests
+check: check-all
+
+# Run all tests in all components
+check-all:
+	@echo "=== Running ALL DocumentDB tests ==="
+	$(MAKE) check-core
+	$(MAKE) check-main
+	$(MAKE) check-rum
+	$(MAKE) check-distributed
+	@echo "=== ALL TESTS COMPLETED ==="
+
+# Core DocumentDB tests
+check-core:
+	@echo "=== Running DocumentDB Core tests ==="
+	$(MAKE) -C pg_documentdb_core check
+
+# Core minimal tests
+check-core-minimal:
+	@echo "=== Running DocumentDB Core MINIMAL tests ==="
+	$(MAKE) -C pg_documentdb_core check-minimal
+
+# Main DocumentDB tests
+check-main:
+	@echo "=== Running Main DocumentDB tests ==="
+	$(MAKE) -C pg_documentdb check
+
+# Main minimal tests
+check-main-minimal:
+	@echo "=== Running Main DocumentDB MINIMAL tests ==="
+	$(MAKE) -C pg_documentdb check-minimal
+
+# Extended RUM tests
+check-rum:
+	@echo "=== Running Extended RUM tests ==="
+	$(MAKE) -C pg_documentdb_extended_rum all
+
+# Distributed DocumentDB tests
+check-distributed:
+	@echo "=== Running Distributed DocumentDB tests ==="
+	$(MAKE) -C internal/pg_documentdb_distributed check
+
 install-no-distributed: install-documentdb
 
 install-documentdb:
@@ -24,7 +70,20 @@
 	$(MAKE) -C internal/pg_documentdb_distributed
 
 %:
-	$(MAKE) -C pg_documentdb_core $@
-	$(MAKE) -C pg_documentdb $@
-	$(MAKE) -C pg_documentdb_extended_rum $@
-	$(MAKE) -C internal/pg_documentdb_distributed $@
+	@if [ "$@" != "check" ] && [ "$@" != "check-all" ] && \
+	     [ "$@" != "check-core" ] && [ "$@" != "check-main" ] && \
+	     [ "$@" != "check-rum" ] && [ "$@" != "check-distributed" ] && \
+	     [ "$@" != "check-quick" ] && [ "$@" != "check-core-minimal" ] && \
+	     [ "$@" != "check-main-minimal" ] && [ "$@" != "check-distributed-minimal" ]; then \
+		$(MAKE) -C pg_documentdb_core $@; \
+		$(MAKE) -C pg_documentdb $@; \
+		$(MAKE) -C pg_documentdb_extended_rum $@; \
+		$(MAKE) -C internal/pg_documentdb_distributed $@; \
+	fi
+
+# Distributed minimal tests
+check-distributed-minimal:
+	@echo "=== Running Distributed DocumentDB MINIMAL tests ==="
+	$(MAKE) -C internal/pg_documentdb_distributed check-dist-minimal
+
+check-quick: check-no-distributed
+	@echo "=== QUICK TESTS COMPLETED ==="
EOF
```

### 2. Применение патча

```bash
patch -p0 < fixed_makefile.patch
```

Этот патч включает все необходимые таргеты:

## Основные тестовые группы

- `make check` / `make check-all` - все тесты
- `make check-core` - тесты ядра
- `make check-main` - основные тесты
- `make check-rum` - RUM тесты
- `make check-distributed` - распределенные тесты

## Минимальные тесты

- `make check-core-minimal` - минимальные тесты ядра
- `make check-main-minimal` - минимальные основные тесты
- `make check-distributed-minimal` - минимальные распределенные тесты

## Быстрые тесты

- `make check-quick` - быстрые тесты (без распределенных)

## Запуск Rust тестов для шлюза

### 1. Переход в директорию шлюза

```bash
cd pg_documentdb_gw
```

### 2. Запуск Rust тестов

```bash
cargo test
```

Или для запуска конкретного теста:

```bash
cargo test test_name
```

где `test_name` - название теста
